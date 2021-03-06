---
title: Azure Cosmos DB bindingen voor de functies 2. x
description: Over het gebruik van Azure Cosmos DB-triggers en bindingen in Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2017
ms.author: cshoe
ms.openlocfilehash: f258a7aff52796a53540706bc8413575d63c9e7d
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77605769"
---
# <a name="azure-cosmos-db-trigger-and-bindings-for-azure-functions-2x-overview"></a>Azure Cosmos DB trigger en bindingen voor Azure Functions 2. x-overzicht

> [!div class="op_single_selector" title1="Selecteer de versie van de Azure Functions runtime die u gebruikt: "]
> * [Versie 1](functions-bindings-cosmosdb.md)
> * [Versie 2](functions-bindings-cosmosdb-v2.md)

In deze reeks artikelen wordt uitgelegd hoe u [Azure Cosmos DB](../cosmos-db/serverless-computing-database.md) bindingen kunt gebruiken in azure functions 2. x. Azure Functions ondersteunt activeren, invoeren en uitvoerbindingen voor Azure Cosmos DB.

| Bewerking | Type |
|---------|---------|
| Een functie uitvoeren wanneer een Azure Cosmos DB-document wordt gemaakt of gewijzigd | [Trigger](./functions-bindings-cosmosdb-v2-trigger.md) |
| Een Azure Cosmos DB document lezen | [Invoer binding](./functions-bindings-cosmosdb-v2-input.md) |
| Wijzigingen in een Azure Cosmos DB document opslaan  |[Uitvoer binding](./functions-bindings-cosmosdb-v2-output.md) |

> [!NOTE]
> Deze verwijzing is voor [Azure functions versie 2. x](functions-versions.md).  Zie [Azure Cosmos DB bindingen voor Azure functions 1. x](functions-bindings-cosmosdb.md)voor informatie over het gebruik van deze bindingen in functions 1. x.
>
> Deze binding heette oorspronkelijk DocumentDB. In functies versie 2. x zijn de trigger, bindingen en pakket alle benoemde Cosmos DB.

## <a name="supported-apis"></a>Ondersteunde Api's

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="add-to-your-functions-app"></a>Toevoegen aan uw functions-app

### <a name="functions-2x-and-higher"></a>Functies 2. x en hoger

Voor het werken met de trigger en bindingen moet u verwijzen naar het juiste pakket. Het NuGet-pakket wordt gebruikt voor .NET-klassen bibliotheken terwijl de uitbreidings bundel wordt gebruikt voor alle andere toepassings typen.

| Taal                                        | Toevoegen door...                                   | Opmerkingen 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Het [NuGet-pakket]installeren, versie 3. x | |
| C#Script, Java, java script, Python, Power shell | De [uitbreidings bundel] registreren          | De [Extensie van Azure-Hulpprogram Ma's] wordt aanbevolen voor gebruik met Visual Studio code. |
| C#Script (alleen online in Azure Portal)         | Een binding toevoegen                            | Zie [uw extensies bijwerken]om bestaande bindings extensies bij te werken zonder uw functie-app opnieuw te publiceren. |

[NuGet-pakket]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB
[core tools]: ./functions-run-local.md
[uitbreidings bundel]: ./functions-bindings-register.md#extension-bundles
[Uw extensies bijwerken]: ./install-update-binding-extensions-manual.md
[Extensie van Azure-Hulpprogram Ma's]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Functions 1. x apps hebben automatisch een verwijzing naar het pakket [micro soft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) , versie 2. x.

## <a name="next-steps"></a>Volgende stappen

- [Een functie uitvoeren wanneer een Azure Cosmos DB-document wordt gemaakt of gewijzigd (trigger)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Een Azure Cosmos DB document lezen (invoer binding)](./functions-bindings-cosmosdb-v2-input.md)
- [Wijzigingen opslaan in een Azure Cosmos DB-document (uitvoer binding)](./functions-bindings-cosmosdb-v2-output.md)
