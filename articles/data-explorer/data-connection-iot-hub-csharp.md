---
title: Een IoT Hub gegevens verbinding maken voor Azure Data Explorer met behulp vanC#
description: In dit artikel leert u hoe u een IoT Hub gegevens verbinding voor Azure Data Explorer maakt met behulp C#van.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 0cac03e50bf46910f8430b745803107b60905769
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74667387"
---
# <a name="create-an-iot-hub-data-connection-for-azure-data-explorer-by-using-c-preview"></a>Een IoT Hub gegevens verbinding maken voor Azure Data Explorer met behulp C# van (preview)

> [!div class="op_single_selector"]
> * [Portal](ingest-data-iot-hub.md)
> * [C#](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)
> * [Azure Resource Manager-sjabloon](data-connection-iot-hub-resource-manager.md)

Azure Data Explorer is een snelle en zeer schaalbare service voor gegevensverkenning voor telemetrische gegevens en gegevens uit logboeken. Azure Data Explorer biedt opname (gegevens laden) van Event Hubs, IoT hubs en blobs die zijn geschreven naar BLOB-containers. In dit artikel maakt u een IoT Hub gegevens verbinding voor Azure Data Explorer met behulp C#van.

## <a name="prerequisites"></a>Vereisten

* Als Visual Studio 2019 niet is geïnstalleerd, kunt u de **gratis** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)downloaden en gebruiken. Zorg ervoor dat u **Azure-ontwikkeling** inschakelt tijdens de installatie van Visual Studio.
* Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.
* [Een cluster en data base](create-cluster-database-csharp.md) maken
* [Tabel-en kolom toewijzing](net-standard-ingest-data.md#create-a-table-on-your-test-cluster) maken
* [Data Base-en tabel beleid](database-table-policies-csharp.md) instellen (optioneel)
* Maak een [IOT hub waarvoor een gedeeld toegangs beleid is geconfigureerd](ingest-data-iot-hub.md#create-an-iot-hub).

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../../includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-iot-hub-data-connection"></a>Een IoT Hub gegevens verbinding toevoegen 

In het volgende voor beeld ziet u hoe u een IoT Hub gegevens verbinding programmatisch kunt toevoegen. Zie [Azure Data Explorer-tabel verbinden met IOT hub](ingest-data-iot-hub.md#connect-azure-data-explorer-table-to-iot-hub) voor het toevoegen van een IOT hub-gegevens verbinding met behulp van de Azure Portal.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
var credential = new ClientCredential(clientId, clientSecret);
var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

var kustoManagementClient = new KustoManagementClient(credentials)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the Prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
var dataConnectionName = "myeventhubconnect";
//The IoT hub that is created as part of the Prerequisites
var iotHubResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Devices/IotHubs/xxxxxx";
var sharedAccessPolicyName = "iothubforread";
var consumerGroup = "$Default";
var location = "Central US";
//The table and column mapping are created as part of the Prerequisites
var tableName = "StormEvents";
var mappingRuleName = "StormEvents_CSV_Mapping";
var dataFormat = DataFormat.CSV;

await kustoManagementClient.DataConnections.CreateOrUpdate(resourceGroupName, clusterName, databaseName, dataConnectionName,
            new IotHubDataConnection(iotHubResourceId, consumerGroup, sharedAccessPolicyName, tableName: tableName, location: location, mappingRuleName: mappingRuleName, dataFormat: dataFormat));
```

|**Instelling** | **Voorgestelde waarde** | **Beschrijving van veld**|
|---|---|---|
| tenantId | *xxxxxxxx-xxxxx-XXXX-XXXX-xxxxxxxxx* | Uw Tenant-ID. Ook bekend als Directory-ID.|
| subscriptionId | *xxxxxxxx-xxxxx-XXXX-XXXX-xxxxxxxxx* | De abonnements-ID die u gebruikt voor het maken van resources.|
| clientId | *xxxxxxxx-xxxxx-XXXX-XXXX-xxxxxxxxx* | De client-ID van de toepassing die toegang heeft tot bronnen in uw Tenant.|
| clientSecret | *xxxxxxxxxxxxxx* | Het client geheim van de toepassing die toegang heeft tot bronnen in uw Tenant. |
| resourceGroupName | *testrg* | De naam van de resource groep die het cluster bevat.|
| clusterName | *mykustocluster* | De naam van uw cluster.|
| databaseName | *mykustodatabase* | De naam van de doel database in uw cluster.|
| dataConnection | *myeventhubconnect* | De gewenste naam van uw gegevens verbinding.|
| tableName | *StormEvents* | De naam van de doel tabel in de doel database.|
| mappingRuleName | *StormEvents_CSV_Mapping* | De naam van de kolom toewijzing die is gerelateerd aan de doel tabel.|
| dataFormat | *bestand* | De gegevens indeling van het bericht.|
| iotHubResourceId | *Resource-ID* | De resource-ID van uw IoT-hub die de gegevens voor opname bevat. |
| sharedAccessPolicyName | *iothubforread* | De naam van het beleid voor gedeelde toegang dat de machtigingen definieert voor apparaten en services om verbinding te maken met IoT Hub. |
| consumerGroup | *$Default* | De consumenten groep van uw Event Hub.|
| location | *US - centraal* | De locatie van de bron van de gegevens verbinding.|

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](../../includes/data-explorer-data-connection-clean-resources-csharp.md)]
