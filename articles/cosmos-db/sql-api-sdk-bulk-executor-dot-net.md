---
title: 'Azure Cosmos DB: bulk-uitvoerder .NET API, SDK & resources'
description: Meer informatie over de bulk-uitvoerder .NET API en SDK, inclusief release datums, pensioen datums en wijzigingen die zijn aangebracht tussen de verschillende versies van de Azure Cosmos DB bulk-vervoerder .NET SDK.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 01/16/2020
ms.author: ramkris
ms.openlocfilehash: 1a8040fc397b526b540ce9343baa985cab49e2b4
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169403"
---
# <a name="net-bulk-executor-library-download-information"></a>.NET-bibliotheek voor bulksgewijs laden: informatie downloaden 

> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET-Wijzigingenfeed](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST-resourceprovider](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Bulk-uitvoerder-.NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk-uitvoerder-java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
| **Beschrijving**| Met de bibliotheek .net bulksgewijze uitvoerder kunnen client toepassingen bulk bewerkingen uitvoeren op Azure Cosmos DB accounts. Deze bibliotheek bevat BulkImport-, BulkUpdate-en BulkDelete-naam ruimten. De BulkImport-module kan documenten bulksgewijs opnemen in een geoptimaliseerde manier, zodat de door Voer ingericht voor een verzameling voor het maximale aantal wordt verbruikt. De BulkUpdate-module kan bestaande gegevens in azure Cosmos-containers bulksgewijs bijwerken als patches. De BulkDelete-module kan documenten bulksgewijs verwijderen op een geoptimaliseerde manier, zodat de door Voer ingericht voor een verzameling voor de maximale grootte wordt verbruikt.|
|**SDK downloaden**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/) |
| **Bibliotheek voor bulk-uitvoerder in GitHub**| [GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)|
|**API-documentatie**|[.NET API-referentiedocumentatie](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)|
|**Aan de slag**|[Aan de slag met de bibliotheek voor bulk-uitvoerder .NET SDK](bulk-executor-dot-net.md)|
| **Huidige ondersteunde framework**| Microsoft .NET Framework 4.5.2, 4.6.1 en .NET Standard 2,0 |

> [!NOTE]
> Als u een bulk-uitvoerder gebruikt, raadpleegt u de meest recente versie 3. x van de [.NET SDK](tutorial-sql-api-dotnet-bulk-import.md), die in de SDK is ingebouwd. 

## <a name="release-notes"></a>Releaseopmerkingen

### <a name="a-name241-preview241-preview"></a><a name="2.4.1-preview"/>2.4.1-Preview

* Vaste TotalElapsedTime in het antwoord van BulkDelete om de totale tijd correct te meten, inclusief eventuele nieuwe pogingen.

### <a name="a-name240-preview240-preview"></a><a name="2.4.0-preview"/>2.4.0-Preview

* De SDK-afhankelijkheid is gewijzigd in > = 2.5.1

### <a name="a-name230-preview2230-preview2"></a><a name="2.3.0-preview2"/>2.3.0-preview2

* Er is ondersteuning toegevoegd voor de bulk-uitvoerder van de grafiek om TTL op hoek punten en randen te accepteren

### <a name="a-name220-preview2220-preview2"></a><a name="2.2.0-preview2"/>2.2.0-preview2

* Er is een probleem opgelost, waardoor uitzonde ringen worden veroorzaakt tijdens het gebruik van elastische schaling van Azure Cosmos DB wanneer de gateway modus wordt uitgevoerd. Deze oplossing maakt IT functioneel equivalent aan 1.4.1-release.

### <a name="a-name210-preview2210-preview2"></a><a name="2.1.0-preview2"/>2.1.0-preview2

* Toegevoegde BulkDelete-ondersteuning voor SQL-API-accounts voor het accepteren van partitie sleutel, te verwijderen document-id-Tuples. Deze wijziging is functioneel gelijk aan 1.4.0-release.

### <a name="a-name200-preview2200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-preview2

* Inclusief MongoBulkExecutor ter ondersteuning van .NET Standard 2,0. Deze functie is functioneel gelijk aan 1.3.0-release, met toevoeging van ondersteuning van .NET Standard 2,0 als het doel raamwerk.

### <a name="a-name200-preview200-preview"></a><a name="2.0.0-preview"/>2.0.0-Preview

* .NET Standard 2,0 is toegevoegd als een van de ondersteunde doel stellingen om de bulk-uitvoerder bibliotheek te laten werken met .NET core-toepassingen.

### <a name="a-name188188"></a><a name="1.8.8"/>1.8.8

* Er is een probleem opgelost in MongoBulkExecutor die de document grootte onverwacht heeft verhoogd door opvulling toe te voegen en in sommige gevallen de maximale document grootte te overschrijden.

### <a name="a-name187187"></a><a name="1.8.7"/>1.8.7

* Er is een probleem met BulkDeleteAsync opgelost wanneer de verzameling geneste partitie sleutel paden heeft.

### <a name="a-name186186"></a><a name="1.8.6"/>1.8.6

* MongoBulkExecutor implementeert nu IDisposable en wordt naar verwachting buiten gebruik gesteld.

### <a name="a-name185185"></a><a name="1.8.5"/>1.8.5

* De vergren deling op de SDK-versie is verwijderd. Pakket is nu afhankelijk van SDK > = 2.5.1.

### <a name="a-name184184"></a><a name="1.8.4"/>1.8.4

* Vast verwerken van id's bij het aanroepen van BulkImport met een lijst met POCO-objecten met numerieke waarden.

### <a name="a-name183183"></a><a name="1.8.3"/>1.8.3

* Vaste TotalElapsedTime in het antwoord van BulkDelete om de totale tijd correct te meten, inclusief eventuele nieuwe pogingen.

### <a name="a-name182182"></a><a name="1.8.2"/>1.8.2

* Vast hoog CPU-verbruik voor bepaalde scenario's.
* Tracering maakt nu gebruik van TraceSource. Gebruikers kunnen listeners definiëren voor de `BulkExecutorTrace` bron.
* Er is een zeldzaam scenario opgelost dat kan leiden tot een vergren deling bij het verzenden van documenten bij een grootte van 2 MB.

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0

* De bulk-uitvoerder is bijgewerkt naar nu de nieuwste versie van de Azure Cosmos DB .NET SDK (2.4.0)

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0

* Er is ondersteuning toegevoegd voor de bulk-uitvoerder van de grafiek om TTL op hoek punten en randen te accepteren

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1

* Er is een probleem opgelost, waardoor uitzonde ringen worden veroorzaakt tijdens het gebruik van elastische schaling van Azure Cosmos DB wanneer de gateway modus wordt uitgevoerd.

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0

* Toegevoegde BulkDelete-ondersteuning voor SQL-API-accounts voor het accepteren van partitie sleutel, te verwijderen document-id-Tuples.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0

* Er is een probleem opgelost, waardoor er een opmaak probleem is opgetreden in de gebruikers agent die wordt gebruikt door bulk-uitvoerder.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0

* Verbetering van de import-en update-Api's voor bulk-uitvoerder om op transparante wijze aan te passen aan elastisch schalen van de Cosmos-container wanneer opslag de huidige capaciteit overschrijdt zonder uitzonde ringen te genereren.

### <a name="a-name112112"></a><a name="1.1.2"/>1.1.2

* Bewaak de DocumentDB .NET SDK-afhankelijkheid naar versie 2.1.3.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* Er is een probleem opgelost, waardoor een bulk-uitvoerder de JSRT-fout genereert tijdens het importeren in vaste verzamelingen.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* Er is ondersteuning toegevoegd voor de BulkDelete-bewerking voor Azure Cosmos DB SQL-API-accounts.
* Er is ondersteuning toegevoegd voor BulkImport-bewerking voor accounts met de API van Azure Cosmos DB voor MongoDB.
* Bewaak de DocumentDB .NET SDK-afhankelijkheid naar versie 2.0.0. 

### <a name="a-name102102"></a><a name="1.0.2"/>1.0.2

* Er is ondersteuning toegevoegd voor de BulkImport-bewerking voor de Azure Cosmos DB Gremlin-API-accounts.

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1

* Kleine fout oplossing voor de BulkImport-bewerking voor Azure Cosmos DB SQL-API-accounts.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

* Er is ondersteuning toegevoegd voor BulkImport-en BulkUpdate-bewerkingen voor Azure Cosmos DB SQL-API-accounts.

## <a name="next-steps"></a>Volgende stappen

Zie het volgende artikel voor meer informatie over de Java-bibliotheek voor bulk-uitvoerder:

[SDK en release-informatie voor de Java bulk-uitvoerder](sql-api-sdk-bulk-executor-java.md)
