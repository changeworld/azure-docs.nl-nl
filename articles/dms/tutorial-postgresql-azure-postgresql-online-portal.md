---
title: 'Zelf studie: PostgreSQL migreren naar Azure DB voor PostgreSQL via de Azure Portal'
titleSuffix: Azure Database Migration Service
description: Meer informatie over het uitvoeren van een online migratie van PostgreSQL on-premises naar Azure Database for PostgreSQL met behulp van Azure Database Migration Service via de Azure Portal.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/17/2020
ms.openlocfilehash: 67eced7f647d50733dc8d273bdd9cd8a31b7b6dc
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78255505"
---
# <a name="tutorial-migrate-postgresql-to-azure-db-for-postgresql-online-using-dms-via-the-azure-portal"></a>Zelf studie: PostgreSQL migreren naar Azure DB voor PostgreSQL online met behulp van DMS via de Azure Portal

U kunt Azure Database Migration Service gebruiken om de data bases van een on-premises PostgreSQL-exemplaar te migreren naar [Azure database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/) met een minimale uitval tijd voor de toepassing. In deze zelf studie migreert u de **gehuurde** voorbeeld database van de DVD van een on-premises exemplaar van postgresql 9,6 naar Azure database for PostgreSQL met behulp van de online migratie activiteit in azure database Migration service.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
>
> * Migreer het voorbeeld schema met behulp van het hulp programma pg_dump.
> * Maak een instantie van Azure Database Migration Service.
> * Maak een migratie project in Azure Database Migration Service.
> * De migratie uitvoeren.
> * De migratie controleren.
> * Migratie cutover uitvoeren.

> [!NOTE]
> Als u Azure Database Migration Service voor het uitvoeren van een online migratie wilt gebruiken, moet u een instantie maken op basis van de prijs categorie Premium.

> [!IMPORTANT]
> Voor een optimale migratie-ervaring raadt micro soft aan om een instantie van Azure Database Migration Service te maken in dezelfde Azure-regio als de doel database. Het verplaatsen van gegevens naar regio's of geografieën kan het migratieproces vertragen en fouten veroorzaken.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Down load en Installeer [postgresql Community edition](https://www.postgresql.org/download/) 9,4, 9,5, 9,6 of 10. De versie van de bron PostgreSQL-server moet 9,4, 9,5, 9,6, 10 of 11 zijn. Zie voor meer informatie het artikel [Supported PostgreSQL Database Versions](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions) (Ondersteunde versies van de PostgreSQL-database).

    Bovendien moet de on-premises versie van PostgreSQL overeenkomen met de versie van Azure Database for PostgreSQL. Zo kan PostgreSQL 9,6 alleen worden gemigreerd naar Azure Database for PostgreSQL 9,6, 10 of 11, maar niet naar Azure Database for PostgreSQL 9,5.

* [Maak een Azure database for postgresql server](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) of [Maak een Citus-server (Azure database for PostgreSQL-grootschalige)](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal).
* Maak een Microsoft Azure Virtual Network voor Azure Database Migration Service met behulp van het Azure Resource Manager implementatie model, dat site-naar-site-verbinding met uw on-premises bron servers biedt met behulp van [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) of [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Raadpleeg de [documentatie van Virtual Network](https://docs.microsoft.com/azure/virtual-network/)voor meer informatie over het maken van een virtueel netwerk, met name de Quick Start-artikelen met stapsgewijze Details.

    > [!NOTE]
    > Als u tijdens de installatie van het virtuele netwerk ExpressRoute gebruikt met Network-peering voor micro soft, voegt u de volgende service- [eind punten](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) toe aan het subnet waarin de service wordt ingericht:
    >
    > * Eind punt van de doel database (bijvoorbeeld SQL-eind punt, Cosmos DB-eind punt, enzovoort)
    > * Opslag eindpunt
    > * Service Bus-eind punt
    >
    > Deze configuratie is nood zakelijk omdat Azure Database Migration Service geen verbinding met internet heeft.

* Zorg ervoor dat de NSG-regels (netwerk beveiligings groep) voor uw virtuele netwerk de volgende binnenkomende communicatie poorten niet blok keren tot Azure Database Migration Service: 443, 53, 9354, 445, 12000. Zie het artikel [netwerk verkeer filteren met netwerk beveiligings groepen](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)voor meer informatie over het filteren van NSG verkeer van virtuele netwerken.
* Configureer uw [Windows Firewall voor toegang tot de database-engine](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Open uw Windows Firewall om Azure Database Migration Service toegang te geven tot de bron PostgreSQL-server, die standaard TCP-poort 5432 is.
* Wanneer u een firewallapparaat gebruikt voor de brondatabase(s), moet u mogelijk firewallregels toevoegen om voor de Azure Database Migration Service toegang tot de brondatabase(s) voor de migratie toe te staan.
* Maak een [firewall regel](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) op server niveau voor Azure Database for PostgreSQL om Azure database Migration service toegang te geven tot de doel databases. Geef het subnet-bereik van het virtuele netwerk op dat wordt gebruikt voor Azure Database Migration Service.
* Schakel logische replicatie in het bestand postgresql.config in en stel de volgende parameters in:

  * wal_level = **logical**
  * max_replication_slots = [aantal sleuven], raadt u aan **vijf sleuven** in te stellen
  * max_wal_senders = [aantal gelijktijdige taken]: met de parameter max_wal_senders stelt u het aantal taken in dat gelijktijdig kan worden uitgevoerd. De aanbevolen instelling is **10 taken**

> [!IMPORTANT]
> Alle tabellen in uw bestaande data base hebben een primaire sleutel nodig om ervoor te zorgen dat de wijzigingen kunnen worden gesynchroniseerd met de doel database.

## <a name="migrate-the-sample-schema"></a>Het voorbeeldschema migreren

Om alle databaseobjecten zoals tabelschema’s, indexen en opgeslagen procedures te voltooien, moeten we het schema uit de brondatabase extraheren en op de database toepassen.

1. Gebruik de opdracht pg_dump -s om een dumpbestand van het schema te maken voor een database.

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Als u bijvoorbeeld een schema dump bestand wilt maken voor de **dvdrental** -Data Base:

    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s -O -x > dvdrentalSchema.sql
    ```

    Zie voor meer informatie over het gebruik van het hulpprogramma pg_dump de voorbeelden in de zelfstudie [pg-dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES).

2. Maak een lege database maken in uw doelomgeving, dit is Azure Database for PostgreSQL.

    Voor meer informatie over het maken van verbinding en maken van een Data Base raadpleegt u het artikel [een Azure database for postgresql-server maken in de Azure Portal](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) of [een Citus-server (Azure database for PostgreSQL-grootschalige) maken in de Azure Portal](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal).

    > [!NOTE]
    > Een instantie van Azure Database for PostgreSQL-grootschalige (Citus) heeft slechts één Data Base: **Citus**.

3. Importeer het schema in de doeldatabase die u hebt gemaakt, door het dumpbestand van het schema te herstellen.

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    Bijvoorbeeld:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental citus < dvdrentalSchema.sql
    ```

4. Voer het volgende script uit om het script voor refererende sleutels te extra heren en toe te voegen aan de bestemming (Azure Database for PostgreSQL), in PgAdmin of in psql.

   > [!IMPORTANT]
   > Externe sleutels in uw schema zorgen ervoor dat de initiële belasting en doorlopende synchronisatie van de migratie mislukken.

    ```
    SELECT Queries.tablename
           ,concat('alter table ', Queries.tablename, ' ', STRING_AGG(concat('DROP CONSTRAINT ', Queries.foreignkey), ',')) as DropQuery
                ,concat('alter table ', Queries.tablename, ' ',
                                                STRING_AGG(concat('ADD CONSTRAINT ', Queries.foreignkey, ' FOREIGN KEY (', column_name, ')', 'REFERENCES ', foreign_table_name, '(', foreign_column_name, ')' ), ',')) as AddQuery
        FROM
        (SELECT
        tc.table_schema,
        tc.constraint_name as foreignkey,
        tc.table_name as tableName,
        kcu.column_name,
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name
    FROM
        information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu
          ON tc.constraint_name = kcu.constraint_name
          AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu
          ON ccu.constraint_name = tc.constraint_name
          AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY') Queries
      GROUP BY Queries.tablename;
    ```

5. Voer het 'drop foreign key'-script (de tweede kolom) uit in het queryresultaat.

6. Als u triggers in doel database wilt uitschakelen, voert u het onderstaande script uit.

   > [!IMPORTANT]
   > Triggers (invoegen of bijwerken) in de gegevens bekrachtigt de integriteit van gegevens in het doel vóór de gegevens die vanuit de bron worden gerepliceerd. Als gevolg hiervan wordt het aanbevolen dat u triggers in alle tabellen **op het doel tijdens de** migratie uitschakelt en vervolgens de triggers opnieuw inschakelt nadat de migratie is voltooid.

    ```
    select concat ('alter table ', event_object_table, ' disable trigger ', trigger_name)
    from information_schema.triggers;
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registreer de Microsoft.DataMigration-resourceprovider

1. Meld u aan bij de Azure-portal, selecteer **Alle services** en selecteer vervolgens **Abonnementen**.

   ![Portal-abonnementen weergeven](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-subscriptions.png)

2. Selecteer het abonnement waarin u het exemplaar van Azure Database Migration Service wilt maken en selecteer vervolgens **resource providers**.

    ![Resourceproviders weergeven](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-resource-provider.png)

3. Zoek naar migratie en selecteer rechts van **Microsoft.DataMigration** de optie **Registreren**.

    ![Resourceprovider registreren](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Een DMS-exemplaar maken

1. Selecteer in de Azure-portal **Een resource maken**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service** uit de vervolgkeuzelijst.

    ![Azure Marketplace](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-marketplace.png)

2. Selecteer in het scherm **Azure Database Migration Service** **Maken**.

    ![Azure Database Migration Service-exemplaar maken](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create1.png)
  
3. Geef in het scherm **migratie service maken** een naam, het abonnement, een nieuwe of bestaande resource groep en de locatie voor de service op.

4. Selecteer een bestaand virtueel netwerk of maak een nieuwe.

    Het virtuele netwerk biedt Azure Database Migration Service toegang tot de bron PostgreSQL-server en de doel Azure Database for PostgreSQL instantie.

    Zie het artikel [een virtueel netwerk maken met behulp van de Azure Portal](https://aka.ms/DMSVnet)voor meer informatie over het maken van een virtueel netwerk in de Azure Portal.

5. Selecteer een prijscategorie.

    Zie voor meer informatie over de kosten en prijscategorieën de [Pagina met prijzen](https://aka.ms/dms-pricing).

    ![Instellingen configureren van een Azure Database Migration Service-exemplaar](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-settings4.png)

6. Selecteer **controleren + maken** om de service te maken.

   Het maken van een service wordt binnen ongeveer 10 tot 15 minuten voltooid.

## <a name="create-a-migration-project"></a>Maak een migratieproject

Nadat de service is gemaakt, zoek deze op in de Azure-portal, open hem en maak vervolgens een nieuw migratieproject.

1. Selecteer in de Azure-portal **Alle diensten**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service**.

      ![Alle exemplaren van Azure Database Migration Service zoeken](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-search.png)

2. Op het scherm **Azure data base Migration Services** zoekt u de naam van Azure database Migration service exemplaar dat u hebt gemaakt, selecteert u het exemplaar en selecteert u vervolgens + **Nieuw migratie project**.

3. Geef in het scherm **Nieuw migratie project** een naam op voor het project, Selecteer in het tekstvak **type bron server** de optie **PostgresSQL**, Selecteer in het tekstvak **doel server type** de optie **Azure database for PostgreSQL**.

4. Selecteer in de sectie **Het type activiteit kiezen** de optie **Onlinegegevensmigratie**.

    ![Azure Database Migration Service project maken](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create-project.png)

    > [!NOTE]
    > U kunt ook **project maken** selecteren om het migratie project nu te maken en de migratie later uitvoeren.

5. Selecteer **Opslaan**, houd rekening met de vereisten voor het gebruik van Azure database Migration service voor het migreren van gegevens en selecteer vervolgens **activiteit maken en uitvoeren**.

## <a name="specify-source-details"></a>Geef brondetails op

1. Geef in het scherm **bron Details toevoegen** de verbindings Details op voor het bron postgresql-exemplaar.

    ![Scherm Brondetails toevoegen](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-source-details.png)

2. Selecteer **Opslaan**.

## <a name="specify-target-details"></a>Doeldetails opgeven

1. Geef in het scherm **doel Details** de verbindings gegevens op voor de doel-grootschalige (Citus). Dit is het vooraf ingerichte exemplaar van grootschalige (Citus) waarmee het **DVD-huur** schema is geïmplementeerd met behulp van pg_dump.

    ![Scherm Doeldetails](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-target-details.png)

2. Selecteer **Opslaan**, en klik vervolgens in het scherm **Toewijzen aan doeldatabases**, wijs de bron- en de doeldatabase voor de migratie toe.

    Als de doel database dezelfde database naam als de bron database bevat Azure Database Migration Service de doel database standaard geselecteerd.

    ![Toewijzen aan het scherm doel databases](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-map-target-databases.png)

3. Selecteer **Opslaan**en accepteer in het scherm **migratie-instellingen** de standaard waarden.

    ![Scherm migratie-instellingen](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-settings.png)

4. Selecteer **Opslaan**, geef in het scherm **Migratieoverzicht** in het tekstvak **Naam activiteit** een naam op voor de migratieactiviteit en controleer het overzicht om ervoor te zorgen dat de bron- en doeldetails overeenkomen met wat u eerder hebt opgegeven.

    ![Scherm migratie overzicht](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-summary.png)

## <a name="run-the-migration"></a>De migratie uitvoeren

* Selecteer **Migratie uitvoeren**.

    Het venster migratie activiteit wordt weer gegeven en de **status** van de activiteit moet worden bijgewerkt om weer te geven als **back-up wordt uitgevoerd**.

## <a name="monitor-the-migration"></a>Bewaak de migratie

1. Selecteer op het scherm van de migratieactiviteit de optie **Vernieuwen** om de weergave bij te werken totdat de **Status** van de migratie als **Voltooid** wordt weergegeven.

     ![Migratie proces bewaken](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-monitor-migration.png)

2. Wanneer de migratie is voltooid, selecteert u onder **database naam**een specifieke data base om de migratie status voor **volledige gegevens belasting** en **incrementele gegevens synchronisatie** bewerkingen te verkrijgen.

   > [!NOTE]
   > Bij **volledige gegevens belasting** wordt de initiële status van de laad migratie weer gegeven, terwijl **incrementele gegevens synchronisatie** de status Change Data Capture (CDC) laat zien.

     ![Details van volledige gegevens belasting](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-full-data-load-details.png)

     ![Details van incrementele gegevens synchronisatie](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-incremental-data-sync-details.png)

## <a name="perform-migration-cutover"></a>Migratie-cutover uitvoeren

Nadat de eerste volledige lading is voltooid, worden de databases gemarkeerd als **Gereed voor cutover**.

1. Wanneer u klaar bent om de databasemigratie te voltooien, selecteert u **Cutover starten**.

2. Wacht totdat de teller **in behandeling** is **0** weer gegeven om ervoor te zorgen dat alle inkomende trans acties voor de bron database zijn gestopt, schakel het selectie vakje **bevestigen** in en selecteer vervolgens **Toep assen**.

    ![Cutover-scherm volt ooien](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-complete-cutover.png)

3. Wanneer de status van de database migratie **is voltooid**, verbindt u uw toepassingen met de nieuwe doel instantie van Azure database for PostgreSQL.

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg het artikel [Bekende problemen/beperkingen met online migraties naar Azure Database for PostgreSQL](known-issues-azure-postgresql-online.md) voor informatie over bekende problemen en beperkingen bij het uitvoeren van online migraties naar Azure Database for PostgreSQL.
* Zie het artikel [Wat is de Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview) voor informatie over de Azure Database Migration Service.
* Zie het artikel [Wat is er Azure database for PostgreSQL?](https://docs.microsoft.com/azure/postgresql/overview)voor informatie over Azure database for PostgreSQL.
