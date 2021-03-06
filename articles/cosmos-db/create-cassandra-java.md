---
title: Een Java-app bouwen met Azure Cosmos DB Cassandra-API
description: In deze quickstart ziet u hoe u de Cassandra-API in Azure Cosmos DB gebruikt om een profieltoepassing te maken met Azure Portal en Java
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.subservice: cosmosdb-cassandra
ms.devlang: java
ms.topic: quickstart
ms.date: 09/24/2018
ms.custom: seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 5a21f36136c6f1d77a2e9cb9108f539c9fb39334
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77134862"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-cassandra-api-data"></a>Quick Start: een Java-app bouwen om Azure Cosmos DB Cassandra-API gegevens te beheren

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [Java](create-cassandra-java.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
>  

In deze Quick Start maakt u een Azure Cosmos DB Cassandra-API-account en gebruikt u een Cassandra java-app die is gekloond van GitHub om een Cassandra-data base en-container te maken. Azure Cosmos DB is een database service met meerdere modellen waarmee u snel documenten, tabellen, sleutel waarden en grafische data bases met globale distributie en mogelijkheden voor horizontale schaal kunt maken en er query's op uitvoert.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak er gratis een](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Of [Probeer gratis Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) zonder een Azure-abonnement.
- [Jdk 8 (Java Development Kit)](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). Verwijs uw `JAVA_HOME` omgevings variabele naar de map waarin de JDK is geïnstalleerd.
- Een [binair maven-archief](https://maven.apache.org/download.cgi). Voer `apt-get install maven` uit op Ubuntu om Maven te installeren.
- [Git](https://www.git-scm.com/downloads). Voer `sudo apt-get install git` uit om Git te installeren op Ubuntu.

## <a name="create-a-database-account"></a>Een databaseaccount maken

Voordat u een documentdatabase kunt maken, moet u een Cassandra-account maken met Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

Nu gaan we werken met code. We gaan een Cassandra-app klonen vanuit GitHub, de verbindingsreeks instellen en de app uitvoeren. U zult zien hoe gemakkelijk het is om op een programmatische manier met gegevens te werken. 

1. Open een opdrachtprompt. Maak een nieuwe map met de naam `git-samples`. Sluit de opdrachtprompt.

    ```bash
    md "C:\git-samples"
    ```

2. Open een git-terminalvenster, bijvoorbeeld git bash, en gebruik de `cd`-opdracht om naar de nieuwe map te gaan voor het installeren van de voorbeeld-app.

    ```bash
    cd "C:\git-samples"
    ```

3. Voer de volgende opdracht uit om de voorbeeldopslagplaats te klonen. Deze opdracht maakt een kopie van de voorbeeld-app op uw computer.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started.git
    ```

## <a name="review-the-code"></a>De code bekijken

Deze stap is optioneel. Als u wilt weten hoe de code de databaseresources maakt, kunt u de volgende codefragmenten bekijken. Als u deze stap wilt overslaan, kunt u verdergaan naar [Uw verbindingsgegevens bijwerken](#update-your-connection-string). Deze fragmenten zijn allemaal afkomstig uit het bestand *src/main/Java/com/azure/cosmosdb/Cassandra/util/CassandraUtils. java* .  

* De Cassandra-opties voor de host, poort, gebruikersnaam, het wachtwoord en SSL zijn ingesteld. De vereiste verbindingsreeksinformatie is afkomstig van de pagina Verbindingsreeks in Azure Portal.

   ```java
   cluster = Cluster.builder().addContactPoint(cassandraHost).withPort(cassandraPort).withCredentials(cassandraUsername, cassandraPassword).withSSL(sslOptions).build();
   ```

* Het `cluster` maakt verbinding met de Cassandra-API van Azure Cosmos DB en retourneert een sessie voor het verkrijgen van toegang.

    ```java
    return cluster.connect();
    ```

De volgende code fragmenten zijn afkomstig uit het bestand *src/main/Java/com/azure/cosmosdb/Cassandra/repository/UserRepository. java* .

* Maak een nieuwe keyspace.

    ```java
    public void createKeyspace() {
        final String query = "CREATE KEYSPACE IF NOT EXISTS uprofile WITH replication = {'class': 'SimpleStrategy', 'replication_factor': '3' } ";
        session.execute(query);
        LOGGER.info("Created keyspace 'uprofile'");
    }
    ```

* Maak een nieuwe tabel.

   ```java
   public void createTable() {
        final String query = "CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)";
        session.execute(query);
        LOGGER.info("Created table 'user'");
   }
   ```

* Voeg gebruikersentiteiten in met een voorbereid instructieobject.

    ```java
    public PreparedStatement prepareInsertStatement() {
        final String insertStatement = "INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (?,?,?)";
        return session.prepare(insertStatement);
    }

    public void insertUser(PreparedStatement statement, int id, String name, String city) {
        BoundStatement boundStatement = new BoundStatement(statement);
        session.execute(boundStatement.bind(id, name, city));
    }
    ```

* Voer een query uit voor het ophalen van alle gebruikersgegevens.

    ```java
   public void selectAllUsers() {
        final String query = "SELECT * FROM uprofile.user";
        List<Row> rows = session.execute(query).all();

        for (Row row : rows) {
            LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
        }
    }
    ```

* Voer een query uit voor het ophalen van de gebruikersgegevens van één gebruiker.

    ```java
    public void selectUser(int id) {
        final String query = "SELECT * FROM uprofile.user where user_id = 3";
        Row row = session.execute(query).one();

        LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
    }
    ```

## <a name="update-your-connection-string"></a>Uw verbindingsreeks bijwerken

Ga nu terug naar Azure Portal om de verbindingsreeksinformatie op te halen en kopieer deze in de app. De details van de verbindingsreeks stellen uw app in staat om te communiceren met de gehoste database.

1. Selecteer in uw Azure Cosmos DB-account [](https://portal.azure.com/)in de Azure Portal **verbindings reeks**. 

    ![Een gebruikersnaam bekijken en kopiëren via de pagina Verbindingsreeks in Azure Portal](./media/create-cassandra-java/copy-username-connection-string-azure-portal.png)

2. Gebruik de ![knop Kopiëren](./media/create-cassandra-java/copy-button-azure-portal.png) aan de rechterkant van het scherm om de CONTACT POINT-waarde te kopiëren.

3. Open het bestand *config. Properties* vanuit de map *C:\git-samples\azure-cosmosdb-Cassandra-Java-Getting-started\java-examples\src\main\resources* . 

3. Plak de CONTACT POINT-waarde uit de portal over `<Cassandra endpoint host>` op regel 2 heen.

    Regel 2 van *config. Properties* moet er nu ongeveer als volgt uitzien 

    `cassandra_host=cosmos-db-quickstart.cassandra.cosmosdb.azure.com`

3. Ga terug naar de portal en kopieer de waarde van de gebruikers naam. Plak de USERNAME-waarde uit de portal over `<cassandra endpoint username>` op regel 4 heen.

    Regel 4 van *config. Properties* moet er nu ongeveer als volgt uitzien 

    `cassandra_username=cosmos-db-quickstart`

4. Ga terug naar de portal en kopieer de waarde van het wacht woord. Plak de PASSWORD-waarde uit de portal over `<cassandra endpoint password>` op regel 5 heen.

    Regel 5 van *config. Properties* moet er nu ongeveer als volgt uitzien 

    `cassandra_password=2Ggkr662ifxz2Mg...==`

5. Als u wilt dat er een specifiek SSL-certificaat wordt gebruikt, vervangt u op regel 6 `<SSL key store file location>` door de locatie van het SSL-certificaat. Als er geen waarde wordt opgegeven, wordt het JDK certificaat gebruikt dat in <JAVA_HOME>/jre/lib/security/cacerts is geïnstalleerd. 

6. Als u regel 6 hebt gewijzigd omdat u wilt dat er een specifiek SSL-certificaat wordt gebruikt, werkt u regel 7 bij, zodat het wachtwoord voor dat certificaat wordt gebruikt. 

7. Sla het bestand *config. Properties* op.

## <a name="run-the-java-app"></a>De Java-app uitvoeren

1. `cd` in het git-terminalvenster naar de map `azure-cosmosdb-cassandra-java-getting-started\java-examples`.

    ```git
    cd "C:\git-samples\azure-cosmosdb-cassandra-java-getting-started\java-examples"
    ```

2. Gebruik in het git-terminalvenster de volgende opdracht om het bestand `cosmosdb-cassandra-examples.jar` te genereren.

    ```git
    mvn clean install
    ```

3. Voer in het git-terminalvenster de volgende opdracht uit om de Java-toepassing te starten.

    ```git
    java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
    ```

    In het terminalvenster worden meldingen weergegeven dat de keyspace en de tabel zijn gemaakt. Vervolgens worden gebruikers in de tabel geselecteerd en geretourneerd en wordt de uitvoer weergegeven, waarna een rij per id wordt geselecteerd en de waarde wordt weergegeven.  

    Druk op CTRL + C om de uitvoering van het programma te stoppen en het console venster te sluiten.

4. Open **Data Explorer** in de Azure-portal om deze nieuwe gegevens te bekijken, te wijzigen, een query erop uit te voeren of er iets anders mee te doen. 

    ![De gegevens in Data Explorer Azure Cosmos DB weer geven](./media/create-cassandra-java/view-data-explorer-java-app.png)

## <a name="review-slas-in-the-azure-portal"></a>SLA’s bekijken in Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een Azure Cosmos DB-account maakt met Cassandra-API en een Cassandra java-app uitvoert die een Cassandra-data base en-container maakt. U kunt nu aanvullende gegevens in uw Azure Cosmos DB-account importeren. 

> [!div class="nextstepaction"]
> [Cassandra-gegevens importeren in Azure Cosmos DB](cassandra-import-data.md)
