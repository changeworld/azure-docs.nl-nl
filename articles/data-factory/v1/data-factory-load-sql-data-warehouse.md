---
title: Terabytes aan gegevens in SQL Data Warehouse laden
description: Laat zien hoe 1 TB aan gegevens binnen 15 minuten in Azure SQL Data Warehouse kan worden geladen met Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: a6c133c0-ced2-463c-86f0-a07b00c9e37f
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 68afc782e13f967bc1b455434c3ae952baff81b9
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980925"
---
# <a name="load-1-tb-into-azure-sql-data-warehouse-under-15-minutes-with-data-factory"></a>1 TB onder 15 minuten aan Azure SQL Data Warehouse laden met Data Factory
> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [gegevens kopiëren naar of van Azure SQL data warehouse met behulp van Data Factory](../connector-azure-sql-data-warehouse.md).


[Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) is een op de cloud gebaseerde, scale-out data base waarmee grote hoeveel heden gegevens kunnen worden verwerkt, zowel relationele als niet-relationeel.  SQL Data Warehouse is gebouwd op een enorm parallelle verwerkings architectuur (MPP) en is geoptimaliseerd voor bedrijfsdata Warehouse-workloads.  De service biedt de flexibiliteit van de cloud om opslag te schalen en onafhankelijk te berekenen.

Aan de slag met Azure SQL Data Warehouse is nu eenvoudiger dan ooit gebruik te maken van **Azure Data Factory**.  Azure Data Factory is een volledig beheerde service voor gegevens integratie in de Cloud, die kan worden gebruikt voor het vullen van een SQL Data Warehouse met de gegevens van uw bestaande systeem en het besparen van uw kost bare tijd bij het evalueren van SQL Data Warehouse en het bouwen van uw analyse nodige. Hier volgen de belangrijkste voor delen van het laden van gegevens in Azure SQL Data Warehouse met behulp van Azure Data Factory:

* **Eenvoudig in te stellen**: 5-stap intuïtieve wizard zonder scripting vereist.
* **Ondersteuning voor uitgebreide gegevens opslag**: ingebouwde ondersteuning voor een uitgebreide set on-premises en cloud-gebaseerde gegevens archieven.
* **Beveiligd en compatibel**: gegevens worden overgebracht via https of ExpressRoute, en wereld wijde service-aanwezigheid zorgt ervoor dat uw gegevens nooit de geografische grens verlaten
* **Ongeëvenaarde prestaties met poly base** – het gebruik van poly Base is de meest efficiënte manier om gegevens naar Azure SQL Data Warehouse te verplaatsen. Met behulp van de functie voor het maken van een staging-BLOB kunt u hoge laad snelheden realiseren van alle typen gegevens opslag, behalve voor Azure Blob Storage, die standaard door poly Base wordt ondersteund.

Dit artikel laat u zien hoe u met behulp van Data Factory wizard kopiëren 1 TB gegevens vanaf Azure Blob Storage in Azure SQL Data Warehouse binnen 15 minuten kunt laden, bij een door Voer van meer dan 1,2 GBps.

In dit artikel vindt u stapsgewijze instructies voor het verplaatsen van gegevens naar Azure SQL Data Warehouse met behulp van de wizard kopiëren.

> [!NOTE]
>  Zie voor algemene informatie over de mogelijkheden van Data Factory bij het verplaatsen van gegevens naar/van Azure SQL Data Warehouse [gegevens verplaatsen naar en van Azure SQL data warehouse met behulp van Azure Data Factory](data-factory-azure-sql-data-warehouse-connector.md) -artikel.
>
> U kunt ook pijp lijnen maken met behulp van Visual Studio, Power shell, enzovoort. Zie [zelf studie: gegevens kopiëren van Azure Blob naar Azure SQL database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor een snelle walkthrough met stapsgewijze instructies voor het gebruik van de Kopieer activiteit in azure Data Factory.  
>
>

## <a name="prerequisites"></a>Vereisten
* Azure-Blob Storage: dit experiment maakt gebruik van Azure Blob Storage (GRS) voor het opslaan van de gegevensset van de TPC-H-test.  Als u geen Azure Storage-account hebt, leert u [hoe u een opslag account maakt](../../storage/common/storage-account-create.md).
* [TPC-h-](http://www.tpc.org/tpch/) gegevens: we gaan TPC-h gebruiken als de gegevensset voor testen.  Hiervoor moet u `dbgen` van de TPC-H Toolkit gebruiken, waarmee u de gegevensset kunt genereren.  U kunt de bron code voor `dbgen` van de [TPC-Hulpprogram ma's](http://www.tpc.org/tpc_documents_current_versions/current_specifications.asp) downloaden en zelf compileren of het gecompileerde binaire bestand downloaden uit [github](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TPCHTools).  Voer dbgen. exe uit met de volgende opdrachten om een plat bestand van 1 TB te genereren voor `lineitem` tabel verdeeld over 10 bestanden:

  * `Dbgen -s 1000 -S **1** -C 10 -T L -v`
  * `Dbgen -s 1000 -S **2** -C 10 -T L -v`
  * …
  * `Dbgen -s 1000 -S **10** -C 10 -T L -v`

    Kopieer nu de gegenereerde bestanden naar Azure Blob.  Raadpleeg voor meer informatie over het [verplaatsen van gegevens naar en van een on-premises bestands systeem met behulp van Azure Data Factory](data-factory-onprem-file-system-connector.md) om dat te doen met behulp van ADF copy.    
* Azure SQL Data Warehouse: in dit experiment worden gegevens geladen in Azure SQL Data Warehouse gemaakt met 6.000 Dwu's

    Raadpleeg [een Azure SQL Data Warehouse maken](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md) voor gedetailleerde instructies voor het maken van een SQL Data Warehouse-data base.  Om de best mogelijke laad prestaties te verkrijgen in SQL Data Warehouse met poly Base, kiezen we het maximum aantal data warehouse-eenheden (Dwu's) dat is toegestaan in de instelling prestaties, namelijk 6.000 Dwu's.

  > [!NOTE]
  > Bij het laden van Azure Blob, worden de prestaties van het laden van gegevens rechtstreeks in verhouding staan tot het aantal Dwu's dat u configureert op de SQL Data Warehouse:
  >
  > Het laden van 1 TB in 1.000 DWU SQL Data Warehouse duurt 87 minuten (~ 200 MBps door Voer) bij het laden van 1 TB in 2.000 DWU SQL Data Warehouse langer dan 46 minuten (~ 380 MBps door Voer) het laden van 1 TB in 6.000 DWU SQL Data Warehouse 14 minuten duren (~ 1,2 GBps door Voer)
  >
  >

    Als u een SQL Data Warehouse met 6.000 Dwu's wilt maken, moet u de schuif regelaar prestaties helemaal naar rechts verplaatsen:

    ![Schuif regelaar prestaties](media/data-factory-load-sql-data-warehouse/performance-slider.png)

    Voor een bestaande data base die niet is geconfigureerd met 6.000 Dwu's, kunt u deze schalen met behulp van Azure Portal.  Ga naar de data base in Azure Portal en er bevindt zich een knop **schalen** in het deel venster **overzicht** , dat wordt weer gegeven in de volgende afbeelding:

    ![Knop schalen](media/data-factory-load-sql-data-warehouse/scale-button.png)    

    Klik op de knop **schalen** om het volgende deel venster te openen, verplaats de schuif regelaar naar de maximum waarde en klik op de knop **Opslaan** .

    ![Dialoog venster schalen](media/data-factory-load-sql-data-warehouse/scale-dialog.png)

    In dit experiment worden gegevens geladen in Azure SQL Data Warehouse met behulp van `xlargerc` resource klasse.

    Voor een optimale door voer moet kopiëren worden uitgevoerd met behulp van een SQL Data Warehouse-gebruiker die deel uitmaakt van `xlargerc` resource klasse.  Meer informatie over hoe u dit doet door [een voor beeld van een gebruikers resource klasse te wijzigen](../../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md).  
* Maak een doel tabel schema in Azure SQL Data Warehouse data base door de volgende DDL-instructie uit te voeren:

    ```SQL  
    CREATE TABLE [dbo].[lineitem]
    (
        [L_ORDERKEY] [bigint] NOT NULL,
        [L_PARTKEY] [bigint] NOT NULL,
        [L_SUPPKEY] [bigint] NOT NULL,
        [L_LINENUMBER] [int] NOT NULL,
        [L_QUANTITY] [decimal](15, 2) NULL,
        [L_EXTENDEDPRICE] [decimal](15, 2) NULL,
        [L_DISCOUNT] [decimal](15, 2) NULL,
        [L_TAX] [decimal](15, 2) NULL,
        [L_RETURNFLAG] [char](1) NULL,
        [L_LINESTATUS] [char](1) NULL,
        [L_SHIPDATE] [date] NULL,
        [L_COMMITDATE] [date] NULL,
        [L_RECEIPTDATE] [date] NULL,
        [L_SHIPINSTRUCT] [char](25) NULL,
        [L_SHIPMODE] [char](10) NULL,
        [L_COMMENT] [varchar](44) NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    ```
  Wanneer de vereiste stappen zijn uitgevoerd, kunt u de Kopieer activiteit nu configureren met behulp van de wizard kopiëren.

## <a name="launch-copy-wizard"></a>De wizard Kopiëren starten
1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Klik op **een resource maken** in de linkerbovenhoek, klik op **Intelligence en analytische**gegevens en klik op **Data Factory**.
3. In het deel venster **nieuw Data Factory** :

   1. Voer **LoadIntoSQLDWDataFactory** in als **naam**.
       De naam van de Azure-gegevensfactory moet wereldwijd uniek zijn. Als het volgende fout bericht wordt weer gegeven: **naam van Data Factory "LoadIntoSQLDWDataFactory" is niet beschikbaar**, wijzigt u de naam van de Data Factory (bijvoorbeeld yournameLoadIntoSQLDWDataFactory) en probeert u het opnieuw. Raadpleeg het onderwerp [Data Factory - Naamgevingsregels](data-factory-naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.  
   2. Selecteer uw Azure-**abonnement**.
   3. Voer een van de volgende stappen uit voor de resourcegroep:
      1. Selecteer **Bestaande gebruiken** om een bestaande resourcegroep te selecteren.
      2. Selecteer **Nieuwe maken** als u een naam voor een resourcegroep wilt typen.
   4. Selecteer een **locatie** voor de gegevensfactory.
   5. Selecteer het selectievakje **Vastmaken aan dashboard** onderaan de blade.  
   6. Klik op **Maken**.
4. Wanneer het aanmaken is voltooid, ziet u de blade **Gegevensfactory** zoals op de volgende afbeelding wordt weergegeven:

   ![Startpagina van de gegevensfactory](media/data-factory-load-sql-data-warehouse/data-factory-home-page-copy-data.png)
5. Op de startpagina van de gegevensfactory klikt u op de tegel **Gegevens kopiëren** om de **wizard Kopiëren** te openen.

   > [!NOTE]
   > Als u ziet dat de webbrowser is vastgelopen bij Autoriseren... schakelt u **Cookies van derden en sitegegevens blokkeren** uit, of u laat dit ingeschakeld en u maakt een uitzondering voor **login.microsoftonline.com** en opent u de wizard opnieuw.
   >
   >

## <a name="step-1-configure-data-loading-schedule"></a>Stap 1: planning voor het laden van gegevens configureren
De eerste stap is het configureren van de planning voor het laden van gegevens.  

Op de pagina **Eigenschappen**:

1. **CopyFromBlobToAzureSqlDataWarehouse** voor **taak naam** invoeren
2. Selecteer de optie **nu uitvoeren** .   
3. Klik op **Volgende**.  

    ![Wizard kopiëren-pagina eigenschappen](media/data-factory-load-sql-data-warehouse/copy-wizard-properties-page.png)

## <a name="step-2-configure-source"></a>Stap 2: bron configureren
In deze sectie worden de stappen beschreven voor het configureren van de bron: Azure-Blob met de items bestanden van de TPC-H-lijn van 1 TB.

1. Selecteer **Azure Blob Storage** als gegevens archief en klik op **volgende**.

    ![Wizard kopiëren-bron pagina selecteren](media/data-factory-load-sql-data-warehouse/select-source-connection.png)

2. Vul de verbindings gegevens voor het Azure Blob-opslag account in en klik op **volgende**.

    ![Wizard kopiëren-informatie bron verbinding](media/data-factory-load-sql-data-warehouse/source-connection-info.png)

3. Selecteer de **map** met de TPC-H-regel item bestanden en klik op **volgende**.

    ![Wizard kopiëren-map voor invoer selecteren](media/data-factory-load-sql-data-warehouse/select-input-folder.png)

4. Wanneer u op **volgende**klikt, worden de instellingen voor de bestands indeling automatisch gedetecteerd.  Controleer of het kolom scheidings teken | in plaats van de standaard komma ', ' is.  Klik op **volgende** nadat u een voor beeld van de gegevens hebt bekeken.

    ![Wizard kopiëren: instellingen voor bestands indeling](media/data-factory-load-sql-data-warehouse/file-format-settings.png)

## <a name="step-3-configure-destination"></a>Stap 3: bestemming configureren
In deze sectie wordt beschreven hoe u de bestemming configureert: `lineitem` tabel in de Azure SQL Data Warehouse-data base.

1. Kies **Azure SQL Data Warehouse** als doel archief en klik op **volgende**.

    ![Wizard kopiëren-doel gegevens archief selecteren](media/data-factory-load-sql-data-warehouse/select-destination-data-store.png)

2. Vul de verbindings gegevens voor de Azure SQL Data Warehouse in.  Zorg ervoor dat u de gebruiker opgeeft die lid is van de rol `xlargerc` (Zie de sectie **vereisten** voor gedetailleerde instructies) en klik op **volgende**.

    ![Wizard kopiëren: de verbindings gegevens voor de bestemming](media/data-factory-load-sql-data-warehouse/destination-connection-info.png)

3. Kies de doel tabel en klik op **volgende**.

    ![Wizard kopiëren-tabel toewijzings pagina](media/data-factory-load-sql-data-warehouse/table-mapping-page.png)

4. In de pagina schema toewijzing, schakelt u de optie kolom toewijzing Toep assen uit en klikt u op **volgende**.

## <a name="step-4-performance-settings"></a>Stap 4: prestatie-instellingen

**Poly base toestaan** is standaard ingeschakeld.  Klik op **Volgende**.

![Wizard kopiëren-schema toewijzing](media/data-factory-load-sql-data-warehouse/performance-settings-page.png)

## <a name="step-5-deploy-and-monitor-load-results"></a>Stap 5: laad resultaten implementeren en bewaken
1. Klik op de knop **volt ooien** om te implementeren.

    ![Wizard kopiëren-overzichts pagina](media/data-factory-load-sql-data-warehouse/summary-page.png)

2. Nadat de implementatie is voltooid, klikt u op `Click here to monitor copy pipeline` om de voortgang van de Kopieer bewerking te controleren. Selecteer de Kopieer pijplijn die u hebt gemaakt in de lijst **activiteit Windows** .

    ![Wizard kopiëren-overzichts pagina](media/data-factory-load-sql-data-warehouse/select-pipeline-monitor-manage-app.png)

    U kunt de details van de Kopieer uitvoering bekijken in het **activiteiten venster Verkenner** in het rechterdeel venster, met inbegrip van het gegevens volume gelezen van de bron en geschreven in doel, duur en de gemiddelde door Voer voor de uitvoering.

    Zoals u kunt zien in de volgende scherm afbeelding, is het kopiëren van 1 TB van Azure Blob Storage naar SQL Data Warehouse 14 minuten geduurd, waardoor de door Voer van 1,22 GBps effectief is.

    ![Wizard kopiëren-geslaagd dialoog venster](media/data-factory-load-sql-data-warehouse/succeeded-info.png)

## <a name="best-practices"></a>Aanbevolen procedures
Hier volgen enkele aanbevolen procedures voor het uitvoeren van uw Azure SQL Data Warehouse-Data Base:

* Gebruik een grotere resource klasse wanneer u deze in een geclusterde column Store-INDEX laadt.
* Voor efficiëntere samen voegingen kunt u het gebruik van hash-distributie door een kolom selecteren in plaats van de standaard round robin distributie.
* Voor snellere laad snelheden kunt u de opslag ruimte gebruiken voor tijdelijke gegevens.
* Maak statistieken nadat u de Azure SQL Data Warehouse hebt geladen.

Zie [Aanbevolen procedures voor Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-best-practices.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
* [Wizard Data Factory kopiëren](data-factory-copy-wizard.md) : in dit artikel vindt u informatie over de wizard kopiëren.
* [Gids voor het kopiëren van prestaties en het afstemmen](data-factory-copy-activity-performance.md) van de activiteit-dit artikel bevat de referentie prestaties en de richt lijnen voor het afstemmen.
