---
title: Zelf studie-Azure Databricks implementeren met een Cosmos DB-eind punt
description: In deze zelf studie wordt beschreven hoe u Azure Databricks implementeert in een virtueel netwerk met een service-eind punt dat is ingeschakeld voor Cosmos DB.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: tutorial
ms.date: 04/17/2019
ms.openlocfilehash: 4ac8c01e986cf1f3158c615a0791ba476e5bf1bb
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74706168"
---
# <a name="tutorial-implement-azure-databricks-with-a-cosmos-db-endpoint"></a>Zelf studie: Azure Databricks implementeren met een Cosmos DB-eind punt

In deze zelf studie wordt beschreven hoe u een VNet-geïnjecteerde Databricks-omgeving implementeert met een service-eind punt dat is ingeschakeld voor Cosmos DB.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Azure Databricks-werk ruimte maken in een virtueel netwerk
> * Een Cosmos DB Service-eind punt maken
> * Een Cosmos DB account maken en gegevens importeren
> * Een Azure Databricks-cluster maken
> * Cosmos DB opvragen van een Azure Databricks notitie blok

## <a name="prerequisites"></a>Vereisten

Ga als volgt te werk voordat u begint:

* Een [Azure Databricks-werk ruimte maken in een virtueel netwerk](quickstart-create-databricks-workspace-vnet-injection.md).

* Down load de [Spark-connector](https://search.maven.org/remotecontent?filepath=com/microsoft/azure/azure-cosmosdb-spark_2.4.0_2.11/1.3.4/azure-cosmosdb-spark_2.4.0_2.11-1.3.4-uber.jar).

* Down load voorbeeld gegevens van de [NOAA National Centers voor omgevings informatie](https://www.ncdc.noaa.gov/stormevents/). Selecteer een staat of gebied en selecteer **zoeken**. Accepteer de standaard instellingen op de volgende pagina en selecteer **zoeken**. Selecteer vervolgens **CSV-down load** aan de linkerkant van de pagina om de resultaten te downloaden.

* Down load het [vooraf gecompileerde binaire bestand](https://aka.ms/csdmtool) van het hulp programma voor gegevens migratie van Azure Cosmos db.

## <a name="create-a-cosmos-db-service-endpoint"></a>Een Cosmos DB Service-eind punt maken

1. Wanneer u een Azure Databricks-werk ruimte hebt geïmplementeerd in een virtueel netwerk, gaat u naar het virtuele netwerk in de [Azure Portal](https://portal.azure.com). Let op de open bare en particuliere subnetten die zijn gemaakt via de Databricks-implementatie.

   ![Subnetten van virtueel netwerk](./media/service-endpoint-cosmosdb/virtual-network-subnets.png)

2. Selecteer het *open bare subnet* en maak een Cosmos DB Service-eind punt. **Sla**vervolgens op.
   
   ![Een Cosmos DB Service-eind punt toevoegen](./media/service-endpoint-cosmosdb/add-cosmosdb-service-endpoint.png)

## <a name="create-a-cosmos-db-account"></a>Cosmos DB-account maken

1. Open Azure Portal. Selecteer in de linkerbovenhoek van het scherm **een resource maken > data bases > Azure Cosmos DB**.

2. Vul de details van het **exemplaar** in op het tabblad **basis beginselen** met de volgende instellingen:

   |Instelling|Waarde|
   |-------|-----|
   |Abonnement|*uw abonnement*|
   |Resourcegroep|*de resource groep*|
   |Accountnaam|DB-vnet-service-eind punt|
   |API|Core (SQL)|
   |Locatie|VS - west|
   |Geo-redundantie|Uitschakelen|
   |Schrijf bewerkingen met meerdere regio's|Inschakelen|

   ![Een Cosmos DB Service-eind punt toevoegen](./media/service-endpoint-cosmosdb/create-cosmosdb-account-basics.png)

3. Selecteer het tabblad **netwerk** en configureer het virtuele netwerk. 

   a. Kies het virtuele netwerk dat u hebt gemaakt als een vereiste en selecteer vervolgens *openbaar subnet*. U ziet dat het *micro soft AzureCosmosDB-eind punt ontbreekt*in het *persoonlijke subnet* . Dit komt doordat u het Cosmos DB Service-eind punt alleen hebt ingeschakeld op het *open bare subnet*.

   b. Zorg ervoor dat u **toegang vanaf Azure Portal** hebt ingeschakeld. Met deze instelling krijgt u toegang tot uw Cosmos DB-account via de Azure Portal. Als deze optie is ingesteld op **weigeren**, worden er fouten weer gegeven wanneer u probeert toegang te krijgen tot uw account. 

   > [!NOTE]
   > Dit is niet nodig voor deze zelf studie, maar u kunt ook *toegang tot mijn IP-adres toestaan* als u de toegang tot uw Cosmos DB-account vanaf uw lokale computer wilt bieden. Als u bijvoorbeeld verbinding maakt met uw account met behulp van de SDK van Cosmos DB, moet u deze instelling inschakelen. Als deze is uitgeschakeld, ontvangt u fout bericht over geweigerde toegang.

   ![Netwerk instellingen van Cosmos DB account](./media/service-endpoint-cosmosdb/create-cosmosdb-account-network.png)

4. Selecteer **controleren + maken**en **maak** vervolgens uw Cosmos DB-account in het virtuele netwerk.

5. Nadat uw Cosmos DB-account is gemaakt, gaat u naar **sleutels** onder **instellingen**. Kopieer de primaire connection string en sla deze op in een tekst editor voor later gebruik.

    ![Pagina Cosmos DB account sleutels](./media/service-endpoint-cosmosdb/cosmos-keys.png)

6. Selecteer **Data Explorer** en **nieuwe verzameling** om een nieuwe data base en verzameling toe te voegen aan uw Cosmos DB-account.

    ![Nieuwe verzameling Cosmos DB](./media/service-endpoint-cosmosdb/new-collection.png)

## <a name="upload-data-to-cosmos-db"></a>Gegevens uploaden naar Cosmos DB

1. Open de grafische interface versie van het [hulp programma voor gegevens migratie voor Cosmos DB](https://aka.ms/csdmtool), **Dtui. exe**.

    ![Cosmos DB-hulpprogramma voor gegevensmigratie](./media/service-endpoint-cosmosdb/cosmos-data-migration-tool.png)

2. Selecteer op het tabblad **Bron gegevens** de optie **CSV-bestand (en)** in de vervolg keuzelijst **importeren vanuit** . Selecteer vervolgens **bestanden toevoegen** en voeg het Storm-gegevens CSV toe dat u hebt gedownload als een vereiste.

    ![Bron informatie voor Cosmos DB hulp programma voor gegevens migratie](./media/service-endpoint-cosmosdb/cosmos-source-information.png)

3. Voer op het tabblad **doel gegevens** de Connection String in. De connection string indeling is `AccountEndpoint=<URL>;AccountKey=<key>;Database=<database>`. De AccountEndpoint en AccountKey zijn opgenomen in de primaire connection string die u in de vorige sectie hebt opgeslagen. Voeg `Database=<your database name>` toe aan het einde van de connection string en selecteer **verifiëren**. Voeg vervolgens de naam van de verzameling en de partitie sleutel toe.

    ![Doel informatie voor het hulp programma voor gegevens migratie Cosmos DB](./media/service-endpoint-cosmosdb/cosmos-target-information.png)

4. Selecteer **volgende** totdat u de overzichts pagina krijgt. Selecteer vervolgens **importeren**.

## <a name="create-a-cluster-and-add-library"></a>Een cluster maken en een bibliotheek toevoegen

1. Navigeer naar uw Azure Databricks-service in de [Azure Portal](https://portal.azure.com) en selecteer **werk ruimte starten**.

   ![Databricks-werk ruimte starten](./media/service-endpoint-cosmosdb/launch-workspace.png)

2. Maak een nieuw cluster. Kies een cluster naam en accepteer de overige standaard instellingen.

   ![Nieuwe cluster instellingen](./media/service-endpoint-cosmosdb/create-cluster.png)

3. Nadat het cluster is gemaakt, gaat u naar de pagina cluster en selecteert u het tabblad **tape wisselaars** . Selecteer **nieuwe installeren** en upload het bestand Spark-connector jar om de bibliotheek te installeren.

    ![Spark-connector bibliotheek installeren](./media/service-endpoint-cosmosdb/install-cosmos-connector-library.png)

    U kunt controleren of de bibliotheek is geïnstalleerd op het tabblad **tape wisselaars** .

    ![Tabblad Databricks cluster bibliotheken](./media/service-endpoint-cosmosdb/installed-library.png)

## <a name="query-cosmos-db-from-a-databricks-notebook"></a>Cosmos DB opvragen van een Databricks-notebook

1. Navigeer naar uw Azure Databricks-werk ruimte en maak een nieuwe python-notebook.

    ![Nieuwe Databricks-notebook maken](./media/service-endpoint-cosmosdb/new-python-notebook.png)

2. Voer de volgende python-code uit om de configuratie van de Cosmos DB-verbinding in te stellen. Wijzig het **eind punt**, de hoofd **sleutel**, de **Data Base**en de **verzameling** dienovereenkomstig.

    ```python
    connectionConfig = {
      "Endpoint" : "https://<your Cosmos DB account name.documents.azure.com:443/",
      "Masterkey" : "<your Cosmos DB primary key>",
      "Database" : "<your database name>",
      "preferredRegions" : "West US 2",
      "Collection": "<your collection name>",
      "schema_samplesize" : "1000",
      "query_pagesize" : "200000",
      "query_custom" : "SELECT * FROM c"
    }
    ```

3. Gebruik de volgende python-code om de gegevens te laden en een tijdelijke weer gave te maken.

    ```python
    users = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**connectionConfig).load()
    users.createOrReplaceTempView("storm")
    ```

4. Gebruik de volgende Magic-opdracht om een SQL-instructie uit te voeren waarmee gegevens worden geretourneerd.

    ```python
    %sql
    select * from storm
    ```

    U hebt de door VNet geïnjecteerde Databricks-werk ruimte gekoppeld aan een service-Endpoint waarvoor Cosmos DB resource is ingeschakeld. Zie [Azure Cosmos DB-connector voor Apache Spark voor](https://github.com/Azure/azure-cosmosdb-spark)meer informatie over het maken van verbinding met Cosmos db.

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze niet meer nodig hebt, verwijdert u de resource groep, de Azure Databricks-werk ruimte en alle gerelateerde resources. Als u de taak verwijdert, vermijdt u onnodig factureren. Als u van plan bent de Azure Databricks-werk ruimte in de toekomst te gebruiken, kunt u het cluster stoppen en later opnieuw opstarten. Als u deze Azure Databricks werk ruimte niet wilt blijven gebruiken, verwijdert u alle resources die u in deze zelf studie hebt gemaakt met behulp van de volgende stappen:

1. Klik in het menu aan de linkerkant in het Azure Portal op **resource groepen** en klik vervolgens op de naam van de resource groep die u hebt gemaakt.

2. Selecteer op de pagina van de resource groep **verwijderen**, typ de naam van de resource die u wilt verwijderen in het tekstvak en selecteer vervolgens opnieuw **verwijderen** .

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een Azure Databricks-werk ruimte geïmplementeerd in een virtueel netwerk en de Cosmos DB Spark-connector gebruikt om Cosmos DB gegevens op te vragen uit Databricks. Ga verder met de zelf studie voor het gebruik van SQL Server met Azure Databricks voor meer informatie over het werken met Azure Databricks in een virtueel netwerk.

> [!div class="nextstepaction"]
> [Zelf studie: een SQL Server Linux-docker-container in een virtueel netwerk doorzoeken van een Azure Databricks notebook](vnet-injection-sql-server.md)
