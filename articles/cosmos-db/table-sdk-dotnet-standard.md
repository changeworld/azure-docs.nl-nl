---
title: Azure Cosmos DB Table-API .NET Standard SDK & resources
description: Meer informatie over de Azure Cosmos DB Table-API en de .NET Standard-SDK, inclusief release datums, pensioen datums en wijzigingen die zijn aangebracht tussen elke versie.
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/18/2019
ms.openlocfilehash: 48fd85e27feb3d0c7f7f722dbbd502be55684385
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76771592"
---
# <a name="azure-cosmos-db-table-net-standard-api-download-and-release-notes"></a>Azure Cosmos DB Table .NET Standard API: Download-en release-opmerkingen
> [!div class="op_single_selector"]
> 
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**SDK downloaden**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)|
|**Voorbeeld**|[Cosmos DB Table-API .NET-voor beeld](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started)|
|**Snelstartgids**|[Snelstartgids](create-table-dotnet.md)|
|**Zelfstudie**|[Zelfstudie](tutorial-develop-table-dotnet.md)|
|**Huidige ondersteunde framework**|[Microsoft .NET standaard 2,0](https://www.nuget.org/packages/NETStandard.Library)|
|**Probleem melden**|[Probleem melden](https://github.com/Azure/azure-cosmos-table-dotnet/issues)|

## <a name="release-notes-for-200-series"></a>Release opmerkingen voor de 2.0.0-serie
de 2.0.0-serie neemt de afhankelijkheid van [micro soft. Azure. Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/), met prestatie verbeteringen en naam ruimte consolidatie op Cosmos DB eind punt.

### <a name="a-name200-preview200-preview"></a><a name="2.0.0-preview"/>2.0.0-Preview
* eerste preview van 2.0.0 Table SDK die afhankelijk is van [micro soft. Azure. Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/), met prestatie verbeteringen en naam ruimte consolidatie op Cosmos DB eind punt. De open bare API blijft hetzelfde.

## <a name="release-notes-for-100-series"></a>Release opmerkingen voor de 1.0.0-serie
de 1.0.0-serie neemt de afhankelijkheid van [micro soft. Azure. DocumentDB. core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/).

### <a name="a-name105105"></a><a name="1.0.5"/>1.0.5
* Nieuwe configuratie introduceren onder TableClientConfiguration om rest-uitvoerder te gebruiken om te communiceren met Cosmos DB Table-API

### <a name="a-name105-preview105-preview"></a><a name="1.0.5-preview"/>1.0.5-preview
* Opgeloste fouten

### <a name="a-name104104"></a><a name="1.0.4"/>1.0.4
* Opgeloste fouten
* Geef de HttpClientTimeout-optie voor RestExecutorConfiguration op.

### <a name="a-name104-preview104-preview"></a><a name="1.0.4-preview"/>1.0.4-preview
* Opgeloste fouten
* Geef de HttpClientTimeout-optie voor RestExecutorConfiguration op.

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Opgeloste fouten

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Versie met algemene beschikbaarheid

### <a name="a-name0110-preview0110-preview"></a><a name="0.11.0-preview"/>0.11.0-Preview
* Er zijn wijzigingen aangebracht in de manier waarop CloudTableClient kan worden geconfigureerd. Er wordt nu een TableClientConfiguration-object gebruikt tijdens het bouwen. TableClientConfiguration biedt verschillende eigenschappen voor het configureren van het client gedrag, afhankelijk van het feit of het doel eindpunt Cosmos DB Table-API of Azure Storage Table-API.
* Ondersteuning toegevoegd aan TableQuery om resultaten te retour neren in de gesorteerde volg orde van een aangepaste kolom. Deze functie wordt alleen ondersteund voor Cosmos DB Table-eind punten.
* Er is ondersteuning toegevoegd om RequestCharges beschikbaar te maken voor verschillende resultaat typen. Deze functie wordt alleen ondersteund voor Cosmos DB Table-eind punten.

### <a name="a-name0101-preview0101-preview"></a><a name="0.10.1-preview"/>0.10.1-preview
* Voeg ondersteuning toe voor SAS-token, bewerkingen van TablePermissions, ServiceProperties en ServiceStats tegen Azure Storage Table-eind punten. 
   > [!NOTE]
   > Sommige functies in eerdere Azure Storage tabel-Sdk's worden nog niet ondersteund, zoals versleuteling aan de client zijde.

### <a name="a-name0100-preview0100-preview"></a><a name="0.10.0-preview"/>0.10.0-Preview
* Voeg ondersteuning toe voor belangrijkste ruwe, batch-en query bewerkingen voor Azure Storage tabel-eind punten. 
   > [!NOTE]
   > Sommige functies in eerdere Azure Storage tabel-Sdk's worden nog niet ondersteund, zoals versleuteling aan de client zijde.

### <a name="a-name091-preview091-preview"></a><a name="0.9.1-preview"/>0.9.1 tot en-preview
* Azure Cosmos DB tabel .NET Standard SDK is een platformoverschrijdende .NET-bibliotheek die efficiënte toegang biedt tot het tabel gegevens model op Cosmos DB. Deze eerste release biedt ondersteuning voor de volledige set van tabel-en entiteits functies, met vergelijk bare Api's als de [Cosmos db table SDK voor .NET Framework](table-sdk-dotnet.md). 
   > [!NOTE]
   >  Azure Storage Table-eind punten worden nog niet ondersteund in de 0.9.1 tot en-preview-versie.

## <a name="release-and-retirement-dates"></a>Release-en pensioen datums
Micro soft biedt een kennisgeving van ten minste **twaalf maanden** voor het buiten gebruik stellen van een SDK om de overgang naar een nieuwere/ondersteunde versie te versoepelen.

In deze platformoverschrijdende .NET Standard-bibliotheek [micro soft. Azure. Cosmos. table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) wordt de .NET Framework bibliotheek [micro soft. Azure. CosmosDB. table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table)vervangen.

### <a name="200-series"></a>2.0.0-serie
| Versie | Releasedatum | Vervaldatum |
| --- | --- | --- |
| [2.0.0-Preview](#2.0.0-preview) |Auguest 22, 2019 |--- |

### <a name="100-series"></a>1.0.0-serie
| Versie | Releasedatum | Vervaldatum |
| --- | --- | --- |
| [1.0.5](#1.0.5) |13 september 2019 |--- |
| [1.0.5-preview](#1.0.5-preview) |Auguest 20, 2019 |--- |
| [1.0.4](#1.0.4) |Auguest 12, 2019 |--- |
| [1.0.4-preview](#1.0.4-preview) |26 juli 2019 |--- |
| 1.0.2-Preview |2 mei 2019 |--- |
| [1.0.1](#1.0.1) |19 april 2019 |--- |
| [1.0.0](#1.0.0) |13 maart 2019 |--- |
| [0.11.0-preview](#0.11.0-preview) |5 maart 2019 |--- |
| [0.10.1-preview](#0.10.1-preview) |22 januari 2019 |--- |
| [0.10.0-preview](#0.10.0-preview) |18 december 2018 |--- |
| [0.9.1 tot en-preview](#0.9.1-preview) |18 oktober 2018 |--- |


## <a name="faq"></a>Veelgestelde vragen

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zie ook
Zie [Inleiding tot Azure Cosmos DB Table-API](table-introduction.md)voor meer informatie over de Azure Cosmos db table-API.
