---
title: Para meters gekoppelde services in Azure Data Factory
description: Meer informatie over het para meters van gekoppelde services in Azure Data Factory en het door geven van dynamische waarden tijdens runtime.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/18/2018
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: acc7284eb607d20ca1d62b478d802be56048bc6c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75440098"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>Para meters gekoppelde services in Azure Data Factory

U kunt nu een gekoppelde service para meters en dynamische waarden door geven tijdens runtime. Als u bijvoorbeeld verbinding wilt maken met verschillende data bases op dezelfde Azure SQL Database Server, kunt u de naam van de data base nu para meters in de definitie van de gekoppelde service. Zo voor komt u dat u een gekoppelde service voor elke Data Base op de Azure SQL database-server hoeft te maken. U kunt ook andere eigenschappen in de definitie van de gekoppelde service para meters, bijvoorbeeld *gebruikers naam.*

U kunt de Data Factory-gebruikers interface in de Azure Portal of een programmeer interface gebruiken om gekoppelde services te para meters.

> [!TIP]
> We raden u aan om wacht woorden of geheimen niet para meters. Sla alle verbindings reeksen op in Azure Key Vault in plaats daarvan en para meters de naam van het *geheim*.

Bekijk de volgende video voor een inleiding en demonstratie van zeven minuten voor deze functie:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-data-stores"></a>Ondersteunde gegevensarchieven

Op dit moment wordt de gekoppelde service parameterisering ondersteund in de Data Factory-gebruikers interface in de Azure Portal voor de volgende gegevens archieven. Voor alle andere gegevens archieven kunt u de gekoppelde service para meters door het pictogram **code** te selecteren op het tabblad **verbindingen** en de JSON-editor te gebruiken.
- Azure SQL Database
- Azure SQL Data Warehouse
- SQL Server
- Oracle
- Cosmos DB
- Amazon Redshift
- MySQL
- Azure Database voor MySQL

## <a name="data-factory-ui"></a>Gebruikersinterface van Data Factory

![Dynamische inhoud toevoegen aan de definitie van de gekoppelde service](media/parameterize-linked-services/parameterize-linked-services-image1.png)

![Een nieuwe parameter maken](media/parameterize-linked-services/parameterize-linked-services-image2.png)

## <a name="json"></a>JSON

```json
{
    "name": "AzureSqlDatabase",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:myserver.database.windows.net,1433;Database=@{linkedService().DBName};User ID=user;Password=fake;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": null,
        "parameters": {
            "DBName": {
                "type": "String"
            }
        }
    }
}
```
