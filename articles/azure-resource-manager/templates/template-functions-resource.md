---
title: 'Sjabloon functies: bronnen'
description: Beschrijft de functies in een Azure Resource Manager-sjabloon gebruikt voor het ophalen van waarden over resources.
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 10476f5a29c12d7437beb9a9f707feda815d7ba1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79248669"
---
# <a name="resource-functions-for-azure-resource-manager-templates"></a>Functies van de resource voor Azure Resource Manager-sjablonen

Resource Manager biedt de volgende functies voor het ophalen van waarden van resources:

* [extensionResourceId](#extensionresourceid)
* [orderverzamellijst](#list)
* [hardwareproviders](#providers)
* [referentielaag](#reference)
* [resourceGroup](#resourcegroup)
* [resourceId](#resourceid)
* [abonnement](#subscription)
* [subscriptionResourceId](#subscriptionresourceid)
* [tenantResourceId](#tenantresourceid)

Zie [implementatie waarde Functions](template-functions-deployment.md)om waarden op te halen uit para meters, variabelen of de huidige implementatie.

## <a name="extensionresourceid"></a>extensionResourceId

```json
extensionResourceId(resourceId, resourceType, resourceName1, [resourceName2], ...)
```

Retourneert de resource-ID voor een [extensie resource](../management/extension-resource-types.md), een resource type dat wordt toegepast op een andere resource om aan de mogelijkheden ervan toe te voegen.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| resourceId |Ja |tekenreeks |De resource-ID voor de resource waarop de uitbreidings resource wordt toegepast. |
| resourceType |Ja |tekenreeks |Het type resource, met inbegrip van de naamruimte van de resource-provider. |
| resourceName1 |Ja |tekenreeks |De naam van de resource. |
| resourceName2 |Nee |tekenreeks |Volgend resource naam segment, indien nodig. |

Ga door met het toevoegen van resource namen als para meters wanneer het resource type meer segmenten bevat.

### <a name="return-value"></a>Retourwaarde

De basis indeling van de resource-ID die wordt geretourneerd door deze functie is:

```json
{scope}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Het bereik segment varieert van de resource die wordt uitgebreid.

Wanneer de extensie resource wordt toegepast op een **resource**, wordt de resource-id geretourneerd met de volgende indeling:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{baseResourceProviderNamespace}/{baseResourceType}/{baseResourceName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Wanneer de extensie resource wordt toegepast op een **resource groep**, is de notatie:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Wanneer de extensie resource wordt toegepast op een **abonnement**, is de indeling:

```json
/subscriptions/{subscriptionId}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Wanneer de extensie resource wordt toegepast op een **beheer groep**, is de indeling:

```json
/providers/Microsoft.Management/managementGroups/{managementGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

### <a name="extensionresourceid-example"></a>extensionResourceId-voor beeld

In het volgende voor beeld wordt de resource-ID voor een resource groeps vergrendeling geretourneerd.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "lockName":{
            "type": "string"
        }
    },
    "variables": {},
    "resources": [],
    "outputs": {
        "lockResourceId": {
            "type": "string",
            "value": "[extensionResourceId(resourceGroup().Id , 'Microsoft.Authorization/locks', parameters('lockName'))]"
        }
    }
}
```

<a id="listkeys" />
<a id="list" />

## <a name="list"></a>orderverzamellijst

```json
list{Value}(resourceName or resourceIdentifier, apiVersion, functionValues)
```

De syntaxis voor deze functie is afhankelijk van de naam van de lijst bewerkingen. Elke implementatie retourneert waarden voor het resource type dat een lijst bewerking ondersteunt. De naam van de bewerking moet beginnen met `list`. Enkele algemene gebruiks vormen zijn `listKeys` en `listSecrets`.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| resourceName of resourceIdentifier |Ja |tekenreeks |De unieke id voor de resource. |
| apiVersion |Ja |tekenreeks |API-versie van de runtimestatus van de resource. Doorgaans in de notatie **jjjj-mm-dd**. |
| functionValues |Nee |object | Een object met waarden voor de functie. Geef dit object alleen op voor functies die ondersteuning bieden voor het ontvangen van een object met parameter waarden, zoals **listAccountSas** op een opslag account. In dit artikel wordt een voor beeld gegeven van het door geven van functie waarden. |

### <a name="valid-uses"></a>Geldige toepassingen

De functies List kunnen alleen worden gebruikt in de eigenschappen van een resource definitie en de sectie outputs van een sjabloon of implementatie. In combi natie met [eigenschaps herhaling](copy-properties.md)kunt u de functies list voor `input` gebruiken omdat de expressie wordt toegewezen aan de eigenschap resource. U kunt deze niet gebruiken met `count` omdat het aantal moet worden bepaald voordat de lijst functie wordt opgelost.

### <a name="implementations"></a>Implementaties

Het mogelijke gebruik van lijst * wordt weer gegeven in de volgende tabel.

| Resourcetype | Functie naam |
| ------------- | ------------- |
| Microsoft.AnalysisServices/servers | [listGatewayStatus](/rest/api/analysisservices/servers/listgatewaystatus) |
| Microsoft.AppConfiguration/configurationStores | Listkeys ophalen |
| Microsoft.Automation/automationAccounts | [Listkeys ophalen](/rest/api/automation/keys/listbyautomationaccount) |
| Microsoft.Batch/batchAccounts | [listkeys ophalen](/rest/api/batchmanagement/batchaccount/getkeys) |
| Microsoft.BatchAI/workspaces/experiments/jobs | [listoutputfiles](/rest/api/batchai/jobs/listoutputfiles) |
| Microsoft.Blockchain/blockchainMembers | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/blockchainmembers/listapikeys) |
| Microsoft.Blockchain/blockchainMembers/transactionNodes | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/transactionnodes/listapikeys) |
| Microsoft.Cache/redis | [Listkeys ophalen](/rest/api/redis/redis/listkeys) |
| Microsoft.CognitiveServices/accounts | [Listkeys ophalen](/rest/api/cognitiveservices/accountmanagement/accounts/listkeys) |
| Micro soft. ContainerRegistry/registers | [listBuildSourceUploadUrl](/rest/api/containerregistry/registries%20(tasks)/getbuildsourceuploadurl) |
| Micro soft. ContainerRegistry/registers | [listCredentials](/rest/api/containerregistry/registries/listcredentials) |
| Micro soft. ContainerRegistry/registers | [listUsages](/rest/api/containerregistry/registries/listusages) |
| Micro soft. ContainerRegistry/registers/webhooks | [listEvents](/rest/api/containerregistry/webhooks/listevents) |
| Microsoft.ContainerRegistry/registries/runs | [listLogSasUrl](/rest/api/containerregistry/runs/getlogsasurl) |
| Micro soft. ContainerRegistry/registers/taken | [listDetails](/rest/api/containerregistry/tasks/getdetails) |
| Microsoft.ContainerService/managedClusters | [listClusterAdminCredential](/rest/api/aks/managedclusters/listclusteradmincredentials) |
| Microsoft.ContainerService/managedClusters | [listClusterUserCredential](/rest/api/aks/managedclusters/listclusterusercredentials) |
| Microsoft.ContainerService/managedClusters/accessProfiles | [listCredential](/rest/api/aks/managedclusters/getaccessprofile) |
| Microsoft.DataBox/jobs | listCredentials |
| Microsoft.DataFactory/datafactories/gateways | listauthkeys |
| Microsoft.DataFactory/factories/integrationruntimes | [listauthkeys](/rest/api/datafactory/integrationruntimes/listauthkeys) |
| Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers | [listSasTokens](/rest/api/datalakeanalytics/storageaccounts/listsastokens) |
| Micro soft. DataShare/accounts/shares | [listSynchronizations](/rest/api/datashare/shares/listsynchronizations) |
| Micro soft. DataShare/accounts/shareSubscriptions | [listSourceShareSynchronizationSettings](/rest/api/datashare/sharesubscriptions/listsourcesharesynchronizationsettings) |
| Micro soft. DataShare/accounts/shareSubscriptions | [listSynchronizationDetails](/rest/api/datashare/sharesubscriptions/listsynchronizationdetails) |
| Micro soft. DataShare/accounts/shareSubscriptions | [listSynchronizations](/rest/api/datashare/sharesubscriptions/listsynchronizations) |
| Microsoft.Devices/iotHubs | [listkeys ophalen](/rest/api/iothub/iothubresource/listkeys) |
| Microsoft.Devices/iotHubs/iotHubKeys | [listkeys ophalen](/rest/api/iothub/iothubresource/getkeysforkeyname) |
| Microsoft.Devices/provisioningServices/keys | [listkeys ophalen](/rest/api/iot-dps/iotdpsresource/listkeysforkeyname) |
| Microsoft.Devices/provisioningServices | [listkeys ophalen](/rest/api/iot-dps/iotdpsresource/listkeys) |
| Microsoft.DevTestLab/labs | [ListVhds](/rest/api/dtl/labs/listvhds) |
| Microsoft.DevTestLab/labs/schedules | [ListApplicable](/rest/api/dtl/schedules/listapplicable) |
| Microsoft.DevTestLab/labs/users/serviceFabrics | [ListApplicableSchedules](/rest/api/dtl/servicefabrics/listapplicableschedules) |
| Microsoft.DevTestLab/labs/virtualMachines | [ListApplicableSchedules](/rest/api/dtl/virtualmachines/listapplicableschedules) |
| Microsoft.DocumentDB/databaseAccounts | [listConnectionStrings](/rest/api/cosmos-db-resource-provider/databaseaccounts/listconnectionstrings) |
| Microsoft.DocumentDB/databaseAccounts | [Listkeys ophalen](/rest/api/cosmos-db-resource-provider/databaseaccounts/listkeys) |
| Microsoft.DomainRegistration | [listDomainRecommendations](/rest/api/appservice/domains/listrecommendations) |
| Micro soft. DomainRegistration/topLevelDomains | [listAgreements](/rest/api/appservice/topleveldomains/listagreements) |
| Micro soft. EventGrid/domeinen | [Listkeys ophalen](/rest/api/eventgrid/domains/listsharedaccesskeys) |
| Micro soft. EventGrid/topics | [Listkeys ophalen](/rest/api/eventgrid/topics/listsharedaccesskeys) |
| Microsoft.EventHub/namespaces/authorizationRules | [listkeys ophalen](/rest/api/eventhub/namespaces/listkeys) |
| Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules | [listkeys ophalen](/rest/api/eventhub/disasterrecoveryconfigs/listkeys) |
| Microsoft.EventHub/namespaces/eventhubs/authorizationRules | [listkeys ophalen](/rest/api/eventhub/eventhubs/listkeys) |
| Microsoft.ImportExport/jobs | [listBitLockerKeys](/rest/api/storageimportexport/bitlockerkeys/list) |
| Micro soft. Kusto/clusters/data bases | [ListPrincipals](/rest/api/azurerekusto/databases/listprincipals) |
| Microsoft.LabServices/users | [ListEnvironments](/rest/api/labservices/globalusers/listenvironments) |
| Microsoft.LabServices/users | [ListLabs](/rest/api/labservices/globalusers/listlabs) |
| Microsoft.Logic/integrationAccounts/agreements | [listContentCallbackUrl](/rest/api/logic/agreements/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/assemblies | [listContentCallbackUrl](/rest/api/logic/integrationaccountassemblies/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listCallbackUrl](/rest/api/logic/integrationaccounts/getcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listKeyVaultKeys](/rest/api/logic/integrationaccounts/listkeyvaultkeys) |
| Micro soft. Logic/integrationAccounts/Maps | [listContentCallbackUrl](/rest/api/logic/maps/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/partners | [listContentCallbackUrl](/rest/api/logic/partners/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/schemas | [listContentCallbackUrl](/rest/api/logic/schemas/listcontentcallbackurl) |
| Microsoft.Logic/workflows | [listCallbackUrl](/rest/api/logic/workflows/listcallbackurl) |
| Microsoft.Logic/workflows | [listSwagger](/rest/api/logic/workflows/listswagger) |
| Microsoft.Logic/workflows/runs/actions | [listExpressionTraces](/rest/api/logic/workflowrunactions/listexpressiontraces) |
| Microsoft.Logic/workflows/runs/actions/repetitions | [listExpressionTraces](/rest/api/logic/workflowrunactionrepetitions/listexpressiontraces) |
| Micro soft. Logic/werk stromen/triggers | [listCallbackUrl](/rest/api/logic/workflowtriggers/listcallbackurl) |
| Micro soft. Logic/werk stromen/versies/triggers | [listCallbackUrl](/rest/api/logic/workflowversions/listcallbackurl) |
| Microsoft.MachineLearning/webServices | [listkeys ophalen](/rest/api/machinelearning/webservices/listkeys) |
| Microsoft.MachineLearning/Workspaces | listworkspacekeys |
| Microsoft.MachineLearningServices/workspaces/computes | [Listkeys ophalen](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/listkeys) |
| Microsoft.MachineLearningServices/workspaces/computes | [listNodes](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/listnodes) |
| Microsoft.MachineLearningServices/workspaces | [Listkeys ophalen](/rest/api/azureml/workspacesandcomputes/workspaces/listkeys) |
| Microsoft.Maps/accounts | [Listkeys ophalen](/rest/api/maps-management/accounts/listkeys) |
| Microsoft.Media/mediaservices/assets | [listContainerSas](/rest/api/media/assets/listcontainersas) |
| Microsoft.Media/mediaservices/assets | [listStreamingLocators](/rest/api/media/assets/liststreaminglocators) |
| Microsoft.Media/mediaservices/streamingLocators | [listContentKeys](/rest/api/media/streaminglocators/listcontentkeys) |
| Microsoft.Media/mediaservices/streamingLocators | [listPaths](/rest/api/media/streaminglocators/listpaths) |
| Microsoft.Network/applicationSecurityGroups | listIpConfigurations |
| Microsoft.NotificationHubs/Namespaces/authorizationRules | [listkeys ophalen](/rest/api/notificationhubs/namespaces/listkeys) |
| Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules | [listkeys ophalen](/rest/api/notificationhubs/notificationhubs/listkeys) |
| Microsoft.OperationalInsights/workspaces | [Listkeys ophalen](/rest/api/loganalytics/workspaces%202015-03-20/listkeys) |
| Microsoft.PolicyInsights/remediations | [listDeployments](/rest/api/policy-insights/remediations/listdeploymentsatresourcegroup) |
| Micro soft. relay/naam ruimten/authorizationRules | [listkeys ophalen](/rest/api/relay/namespaces/listkeys) |
| Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules | listkeys ophalen |
| Microsoft.Relay/namespaces/HybridConnections/authorizationRules | [listkeys ophalen](/rest/api/relay/hybridconnections/listkeys) |
| Micro soft. relay/naam ruimten/WcfRelays/authorizationRules | [listkeys ophalen](/rest/api/relay/wcfrelays/listkeys) |
| Microsoft.Search/searchServices | [listAdminKeys](/rest/api/searchmanagement/adminkeys/get) |
| Microsoft.Search/searchServices | [listQueryKeys](/rest/api/searchmanagement/querykeys/listbysearchservice) |
| Microsoft.ServiceBus/namespaces/authorizationRules | [listkeys ophalen](/rest/api/servicebus/namespaces/listkeys) |
| Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules | [listkeys ophalen](/rest/api/servicebus/disasterrecoveryconfigs/listkeys) |
| Microsoft.ServiceBus/namespaces/queues/authorizationRules | [listkeys ophalen](/rest/api/servicebus/queues/listkeys) |
| Microsoft.ServiceBus/namespaces/topics/authorizationRules | [listkeys ophalen](/rest/api/servicebus/topics/listkeys) |
| Microsoft.SignalRService/SignalR | [listkeys ophalen](/rest/api/signalr/signalr/listkeys) |
| Microsoft.Storage/storageAccounts | [listAccountSas](/rest/api/storagerp/storageaccounts/listaccountsas) |
| Microsoft.Storage/storageAccounts | [listkeys ophalen](/rest/api/storagerp/storageaccounts/listkeys) |
| Microsoft.Storage/storageAccounts | [listServiceSas](/rest/api/storagerp/storageaccounts/listservicesas) |
| Micro soft. StorSimple/managers/apparaten | [listFailoverSets](/rest/api/storsimple/devices/listfailoversets) |
| Micro soft. StorSimple/managers/apparaten | [listFailoverTargets](/rest/api/storsimple/devices/listfailovertargets) |
| Micro soft. StorSimple/managers | [listActivationKey](/rest/api/storsimple/managers/getactivationkey) |
| Micro soft. StorSimple/managers | [listPublicEncryptionKey](/rest/api/storsimple/managers/getpublicencryptionkey) |
| Microsoft.Web/connectionGateways | ListStatus |
| microsoft.web/connections | listconsentlinks |
| Microsoft.Web/customApis | listWsdlInterfaces |
| microsoft.web/locations | listwsdlinterfaces |
| microsoft.web/apimanagementaccounts/apis/connections | listconnectionkeys |
| microsoft.web/apimanagementaccounts/apis/connections | listsecrets |
| microsoft.web/sites/backups | [list](/rest/api/appservice/webapps/listbackups) |
| Microsoft.Web/sites/config | [list](/rest/api/appservice/webapps/listconfigurations) |
| micro soft. web/sites/functies | [listkeys ophalen](/rest/api/appservice/webapps/listfunctionkeys)
| micro soft. web/sites/functies | [listsecrets](/rest/api/appservice/webapps/listfunctionsecrets) |
| microsoft.web/sites/hybridconnectionnamespaces/relays | [listkeys ophalen](/rest/api/appservice/appserviceplans/listhybridconnectionkeys) |
| microsoft.web/sites | [listsyncfunctiontriggerstatus](/rest/api/appservice/webapps/listsyncfunctiontriggers) |
| micro soft. web/sites/sleuven/functies | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |
| microsoft.web/sites/slots/backups | [list](/rest/api/appservice/webapps/listbackupsslot) |
| Microsoft.Web/sites/slots/config | [list](/rest/api/appservice/webapps/listconfigurationsslot) |
| micro soft. web/sites/sleuven/functies | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |

Om te bepalen welke resourcetypen de bewerking voor een lijst met hebt, hebt u de volgende opties:

* Bekijk de [rest API bewerkingen](/rest/api/) voor een resource provider en zoek naar lijst bewerkingen. Opslag accounts hebben bijvoorbeeld de Listkeys ophalen- [bewerking](/rest/api/storagerp/storageaccounts).
* Gebruik de Power shell [-cmdlet Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) . Het volgende voorbeeld haalt alle bewerkingen na opvragen voor storage-accounts:

  ```powershell
  Get-AzProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
  ```
* Gebruik de volgende Azure CLI-opdracht voor het filteren van alleen de bewerkingen na opvragen:

  ```azurecli
  az provider operation show --namespace Microsoft.Storage --query "resourceTypes[?name=='storageAccounts'].operations[].name | [?contains(@, 'list')]"
  ```

### <a name="return-value"></a>Retourwaarde

Het geretourneerde object is afhankelijk van de functie van de lijst die u gebruikt. De Listkeys ophalen voor een opslag account retourneert bijvoorbeeld de volgende indeling:

```json
{
  "keys": [
    {
      "keyName": "key1",
      "permissions": "Full",
      "value": "{value}"
    },
    {
      "keyName": "key2",
      "permissions": "Full",
      "value": "{value}"
    }
  ]
}
```

Andere functies van de lijst met hebben verschillende retour bestandsindelingen. Als u wilt zien van de indeling van een functie, opnemen in de uitvoersectie zoals wordt weergegeven in de voorbeeldsjabloon.

### <a name="remarks"></a>Opmerkingen

Geef de resource op met behulp van de resource naam of de [functie resourceId](#resourceid). Wanneer u een lijst functie gebruikt in dezelfde sjabloon die de resource van de verwijzing implementeert, gebruikt u de resource naam.

Als u een **lijst** functie gebruikt in een resource die voorwaardelijk wordt geïmplementeerd, wordt de functie geëvalueerd, zelfs als de resource niet is geïmplementeerd. Er wordt een fout bericht weer **geven** als de functie List verwijst naar een resource die niet bestaat. Gebruik de functie **als** om te controleren of de functie alleen wordt geëvalueerd wanneer de resource wordt geïmplementeerd. Zie de [functie als](template-functions-logical.md#if) voor een voorbeeld sjabloon die gebruikmaakt van if en-lijst met een voorwaardelijke geïmplementeerde resource.

### <a name="list-example"></a>Voor beeld van lijst

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/listkeys.json) ziet u hoe u de primaire en secundaire sleutels uit een opslag account retourneert in de sectie outputs. Deze retourneert ook een SAS-token voor het opslagaccount.

Om het SAS-token op te halen, geeft u een object voor de verloop tijd door. De verloop tijd moet in de toekomst liggen. In dit voorbeeld is bedoeld om ziet u hoe u de lijst met functies. Normaal gesproken u zou het SAS-token in de waarde van een resource gebruiken in plaats een uitvoerwaarde terug. Uitvoerwaarden worden opgeslagen in de geschiedenis van de implementatie en niet is beveiligd.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagename": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "southcentralus"
        },
        "accountSasProperties": {
            "type": "object",
            "defaultValue": {
                "signedServices": "b",
                "signedPermission": "r",
                "signedExpiry": "2018-08-20T11:00:00Z",
                "signedResourceTypes": "s"
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2018-02-01",
            "name": "[parameters('storagename')]",
            "location": "[parameters('location')]",
            "type": "Microsoft.Storage/storageAccounts",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "StorageV2",
            "properties": {
                "supportsHttpsTrafficOnly": false,
                "accessTier": "Hot",
                "encryption": {
                    "services": {
                        "blob": {
                            "enabled": true
                        },
                        "file": {
                            "enabled": true
                        }
                    },
                    "keySource": "Microsoft.Storage"
                }
            },
            "dependsOn": []
        }
    ],
    "outputs": {
        "keys": {
            "type": "object",
            "value": "[listKeys(parameters('storagename'), '2018-02-01')]"
        },
        "accountSAS": {
            "type": "object",
            "value": "[listAccountSas(parameters('storagename'), '2018-02-01', parameters('accountSasProperties'))]"
        }
    }
}
```

## <a name="providers"></a>Providers

```json
providers(providerNamespace, [resourceType])
```

Retourneert informatie over een resourceprovider en de ondersteunde resourcetypen. Als u een resourcetype niet opgeeft, retourneert de functie de ondersteunde typen voor de resourceprovider.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| providerNamespace |Ja |tekenreeks |Namespace van de provider |
| resourceType |Nee |tekenreeks |Het type resource binnen de opgegeven naamruimte. |

### <a name="return-value"></a>Retourwaarde

Elk type ondersteunde wordt geretourneerd in de volgende indeling:

```json
{
    "resourceType": "{name of resource type}",
    "locations": [ all supported locations ],
    "apiVersions": [ all supported API versions ]
}
```

Matrix volgorde van de geretourneerde waarden wordt niet gegarandeerd.

### <a name="providers-example"></a>Voor beeld van providers

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/providers.json) ziet u hoe u de functie provider gebruikt:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "providerNamespace": {
            "type": "string"
        },
        "resourceType": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "providerOutput": {
            "value": "[providers(parameters('providerNamespace'), parameters('resourceType'))]",
            "type" : "object"
        }
    }
}
```

Voor het resource type **micro soft. Web** resource provider en **sites** retourneert het voor gaande voor beeld een object in de volgende indeling:

```json
{
  "resourceType": "sites",
  "locations": [
    "South Central US",
    "North Europe",
    "West Europe",
    "Southeast Asia",
    ...
  ],
  "apiVersions": [
    "2016-08-01",
    "2016-03-01",
    "2015-08-01-preview",
    "2015-08-01",
    ...
  ]
}
```

## <a name="reference"></a>Verwijzing

```json
reference(resourceName or resourceIdentifier, [apiVersion], ['Full'])
```

Retourneert een object waarmee de runtimestatus van een resource.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| resourceName of resourceIdentifier |Ja |tekenreeks |Naam of de unieke id van een resource. Wanneer u verwijst naar een resource in de huidige sjabloon, geef de naam van de resource als parameter. Als er wordt verwezen naar een eerder geïmplementeerde resource of als de naam van de bron ambigu is, geeft u de resource-ID op. |
| apiVersion |Nee |tekenreeks |API-versie van de opgegeven resource. Deze parameter opgeeft als de bron is niet in dezelfde sjabloon ingericht. Doorgaans in de notatie **jjjj-mm-dd**. Zie voor een geldige API-versie voor uw resource [sjabloon verwijzing](/azure/templates/). |
| 'Volledige' |Nee |tekenreeks |De waarde die aangeeft of het volledige resource-object te retourneren. Als u `'Full'`niet opgeeft, wordt alleen het eigenschappen object van de resource geretourneerd. De volledige-object bevat waarden, zoals de resource-ID en de locatie. |

### <a name="return-value"></a>Retourwaarde

Elk resourcetype geeft als resultaat van de verschillende eigenschappen voor de referentie-functie. De functie retourneren niet een enkele, vooraf gedefinieerde indeling. De geretourneerde waarde verschilt ook, op basis van of u het volledige object hebt opgegeven. Overzicht van de eigenschappen voor een resourcetype, het object in de uitvoersectie zoals wordt weergegeven in het voorbeeld te retourneren.

### <a name="remarks"></a>Opmerkingen

De functie verwijzing haalt de runtimestatus van een eerder geïmplementeerde resource of een resource in de huidige sjabloon wordt geïmplementeerd. In dit artikel ziet u voorbeelden voor beide scenario's.

Normaal gesp roken gebruikt u de functie **Reference** om een bepaalde waarde van een object te retour neren, zoals de URI van het BLOB-eind punt of Fully Qualified Domain name.

```json
"outputs": {
    "BlobUri": {
        "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))).primaryEndpoints.blob]",
        "type" : "string"
    },
    "FQDN": {
        "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', parameters('ipAddressName'))).dnsSettings.fqdn]",
        "type" : "string"
    }
}
```

Gebruik `'Full'` wanneer u resource waarden nodig hebt die geen deel uitmaken van het eigenschappen schema. Bijvoorbeeld, om toegangsbeleid voor sleutelkluizen, Hiermee worden de identiteitseigenschappen voor een virtuele machine.

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "properties": {
    "tenantId": "[subscription().tenantId]",
    "accessPolicies": [
      {
        "tenantId": "[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')), '2019-03-01', 'Full').identity.tenantId]",
        "objectId": "[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')), '2019-03-01', 'Full').identity.principalId]",
        "permissions": {
          "keys": [
            "all"
          ],
          "secrets": [
            "all"
          ]
        }
      }
    ],
    ...
```

### <a name="valid-uses"></a>Geldige toepassingen

De referentie-functie kan alleen worden gebruikt in de eigenschappen van de resourcedefinitie van een en de uitvoersectie van een sjabloon of de implementatie. Bij gebruik in combi natie met [eigenschaps herhaling](copy-properties.md)kunt u de functie Reference gebruiken voor `input`, omdat de expressie wordt toegewezen aan de eigenschap resource. U kunt deze niet gebruiken met `count` omdat het aantal moet worden bepaald voordat de verwijzings functie wordt opgelost.

U kunt de functie Reference niet gebruiken in de uitvoer van een [geneste sjabloon](linked-templates.md#nested-template) om een resource te retour neren die u in de geneste sjabloon hebt geïmplementeerd. Gebruik in plaats daarvan een [gekoppelde sjabloon](linked-templates.md#linked-template).

Als u de functie **Reference** gebruikt in een resource die voorwaardelijk wordt geïmplementeerd, wordt de functie geëvalueerd, zelfs als de resource niet is geïmplementeerd.  Er wordt een fout bericht weer geven als de **verwijzings** functie verwijst naar een resource die niet bestaat. Gebruik de functie **als** om te controleren of de functie alleen wordt geëvalueerd wanneer de resource wordt geïmplementeerd. Zie de [functie als](template-functions-logical.md#if) voor een voorbeeld sjabloon die gebruikmaakt van if en verwijst naar een voorwaardelijk geïmplementeerde resource.

### <a name="implicit-dependency"></a>Impliciete afhankelijkheid

Met behulp van de referentie-functie, declareert u impliciet dat een resource afhankelijk van een andere resource is als de bron waarnaar wordt verwezen, is ingericht in dezelfde sjabloon en u naar de resource met de naam (geen resource-ID verwijst). U hoeft niet te gebruiken ook de eigenschap DEPENDSON te maken. De functie wordt niet geëvalueerd totdat de resource waarnaar wordt verwezen, de implementatie is voltooid.

### <a name="resource-name-or-identifier"></a>Resource naam of-id

Wanneer u verwijst naar een resource die in dezelfde sjabloon is geïmplementeerd, geeft u de naam van de resource op.

```json
"value": "[reference(parameters('storageAccountName'))]"
```

Wanneer u verwijst naar een resource die niet in dezelfde sjabloon is geïmplementeerd, geeft u de resource-ID op.

```json
"value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
```

U kunt een volledig gekwalificeerde resource-id opgeven om te voor komen dat u dubbel zinnigheid weet over welke resource u verwijst.

```json
"value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', parameters('ipAddressName')))]"
```

Bij het samen stellen van een volledig gekwalificeerde verwijzing naar een resource is de volg orde voor het combi neren van segmenten van het type en naam niet gewoon een samen voeging van de twee. Gebruik in plaats daarvan een reeks van *type-en naam* paren van minst specifieke voor de meest specifieke kenmerken:

**{resource-Provider-namespace}/{Parent-resource-type}/{Parent-resource-name} [/{Child-resource-type}/{Child-resource-name}]**

Bijvoorbeeld:

de juiste `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` voor de `Microsoft.Compute/virtualMachines/myVM/extensions/myExt` is onjuist

Gebruik de `resourceId()` functies die in dit document worden beschreven in plaats van de functie `concat()` om het maken van een resource-ID te vereenvoudigen.

### <a name="get-managed-identity"></a>Beheerde identiteit ophalen

[Beheerde identiteiten voor Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md) zijn [uitbreidings bron typen](../management/extension-resource-types.md) die impliciet voor sommige resources worden gemaakt. Omdat de beheerde identiteit niet expliciet is gedefinieerd in de sjabloon, moet u verwijzen naar de resource waarmee de identiteit wordt toegepast. Gebruik `Full` om alle eigenschappen op te halen, met inbegrip van de impliciet gemaakte identiteit.

Als u bijvoorbeeld de Tenant-ID wilt ophalen voor een beheerde identiteit die wordt toegepast op een schaalset voor virtuele machines, gebruikt u:

```json
"tenantId": "[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), '2019-03-01', 'Full').Identity.tenantId]"
```

### <a name="reference-example"></a>Referentie voorbeeld

De volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/referencewithstorage.json) implementeert een resource en verwijst naar die resource.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageAccountName": {
          "type": "string"
      }
  },
  "resources": [
    {
      "name": "[parameters('storageAccountName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-12-01",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {
      "referenceOutput": {
          "type": "object",
          "value": "[reference(parameters('storageAccountName'))]"
      },
      "fullReferenceOutput": {
        "type": "object",
        "value": "[reference(parameters('storageAccountName'), '2016-12-01', 'Full')]"
      }
    }
}
```

Het vorige voorbeeld retourneert de twee objecten. Het object voor eigenschappen is in de volgende indeling:

```json
{
   "creationTime": "2017-10-09T18:55:40.5863736Z",
   "primaryEndpoints": {
     "blob": "https://examplestorage.blob.core.windows.net/",
     "file": "https://examplestorage.file.core.windows.net/",
     "queue": "https://examplestorage.queue.core.windows.net/",
     "table": "https://examplestorage.table.core.windows.net/"
   },
   "primaryLocation": "southcentralus",
   "provisioningState": "Succeeded",
   "statusOfPrimary": "available",
   "supportsHttpsTrafficOnly": false
}
```

Het volledige object verkeert in de volgende indeling:

```json
{
  "apiVersion":"2016-12-01",
  "location":"southcentralus",
  "sku": {
    "name":"Standard_LRS",
    "tier":"Standard"
  },
  "tags":{},
  "kind":"Storage",
  "properties": {
    "creationTime":"2017-10-09T18:55:40.5863736Z",
    "primaryEndpoints": {
      "blob":"https://examplestorage.blob.core.windows.net/",
      "file":"https://examplestorage.file.core.windows.net/",
      "queue":"https://examplestorage.queue.core.windows.net/",
      "table":"https://examplestorage.table.core.windows.net/"
    },
    "primaryLocation":"southcentralus",
    "provisioningState":"Succeeded",
    "statusOfPrimary":"available",
    "supportsHttpsTrafficOnly":false
  },
  "subscriptionId":"<subscription-id>",
  "resourceGroupName":"functionexamplegroup",
  "resourceId":"Microsoft.Storage/storageAccounts/examplestorage",
  "referenceApiVersion":"2016-12-01",
  "condition":true,
  "isConditionTrue":true,
  "isTemplateResource":false,
  "isAction":false,
  "provisioningOperation":"Read"
}
```

De volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/reference.json) verwijst naar een opslag account dat niet in deze sjabloon is geïmplementeerd. Het opslagaccount bestaat al binnen hetzelfde abonnement.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageResourceGroup": {
            "type": "string"
        },
        "storageAccountName": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "ExistingStorage": {
            "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]",
            "type": "object"
        }
    }
}
```

## <a name="resourcegroup"></a>resourceGroup

```json
resourceGroup()
```

Retourneert een object dat de huidige resourcegroep vertegenwoordigt.

### <a name="return-value"></a>Retourwaarde

Het geretourneerde object is in de volgende indeling:

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "{resourceGroupLocation}",
  "managedBy": "{identifier-of-managing-resource}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

De eigenschap **managedBy** wordt alleen geretourneerd voor resource groepen die resources bevatten die worden beheerd door een andere service. Voor beheerde toepassingen, Databricks en AKS is de waarde van de eigenschap de resource-ID van de beheer bron.

### <a name="remarks"></a>Opmerkingen

De functie `resourceGroup()` kan niet worden gebruikt in een sjabloon die is [geïmplementeerd op het abonnements niveau](deploy-to-subscription.md). Deze kan alleen worden gebruikt in sjablonen die zijn geïmplementeerd in een resource groep. U kunt de functie `resourceGroup()` gebruiken in een [gekoppelde of geneste sjabloon (met binnenste bereik)](linked-templates.md) die gericht is op een resource groep, zelfs wanneer de bovenliggende sjabloon wordt geïmplementeerd in het abonnement. In dat scenario wordt de gekoppelde of geneste sjabloon geïmplementeerd op het niveau van de resource groep. Zie [Azure-resources implementeren voor meer dan één abonnement of resource groep](cross-resource-group-deployment.md)voor meer informatie over het richten op een resource groep in een implementatie op abonnements niveau.

Een algemene gebruik van de resourceGroup-functie is het maken van resources in dezelfde locatie als de resourcegroep. In het volgende voor beeld wordt de locatie van de resource groep gebruikt voor een standaard parameter waarde.

```json
"parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
}
```

U kunt ook de functie resourceGroup gebruiken om labels van de resource groep toe te passen op een resource. Zie [labels Toep assen op resource groep](../management/tag-resources.md#apply-tags-from-resource-group)voor meer informatie.

Wanneer geneste sjablonen worden gebruikt voor het implementeren van meerdere resource groepen, kunt u het bereik opgeven voor de evaluatie van de functie resourceGroup. Zie [Azure-resources implementeren voor meer dan één abonnement of resource groep](cross-resource-group-deployment.md)voor meer informatie.

### <a name="resource-group-example"></a>Voor beeld van resource groep

De volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourcegroup.json) retourneert de eigenschappen van de resource groep.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "resourceGroupOutput": {
            "value": "[resourceGroup()]",
            "type" : "object"
        }
    }
}
```

Het vorige voorbeeld retourneert een object in de volgende indeling:

```json
{
  "id": "/subscriptions/{subscription-id}/resourceGroups/examplegroup",
  "name": "examplegroup",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "southcentralus",
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

## <a name="resourceid"></a>resourceId

```json
resourceId([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2], ...)
```

Retourneert de unieke id van een resource. U kunt deze functie gebruiken als de resourcenaam van de niet eenduidig of niet ingericht binnen dezelfde sjabloon is. De notatie van de geretourneerde id varieert, afhankelijk van of de implementatie plaatsvindt bij het bereik van een resource groep, een abonnement, een beheer groep of een Tenant.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| subscriptionId |Nee |tekenreeks (In GUID-indeling) |Standaard wordt het huidige abonnement. Deze waarde opgeven wanneer u nodig hebt om op te halen van een resource in een ander abonnement. Geef deze waarde alleen op wanneer u implementeert in het bereik van een resource groep of een abonnement. |
| resourceGroupName |Nee |tekenreeks |Standaardwaarde is de huidige resourcegroep. Deze waarde opgeven wanneer u nodig hebt om op te halen van een resource in een andere resourcegroep. Geef deze waarde alleen op wanneer u implementeert voor het bereik van een resource groep. |
| resourceType |Ja |tekenreeks |Het type resource, met inbegrip van de naamruimte van de resource-provider. |
| resourceName1 |Ja |tekenreeks |De naam van de resource. |
| resourceName2 |Nee |tekenreeks |Volgend resource naam segment, indien nodig. |

Ga door met het toevoegen van resource namen als para meters wanneer het resource type meer segmenten bevat.

### <a name="return-value"></a>Retourwaarde

Wanneer de sjabloon wordt geïmplementeerd in het bereik van een resource groep, wordt de resource-ID geretourneerd met de volgende indeling:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Bij gebruik in een [implementatie op abonnements niveau](deploy-to-subscription.md)wordt de resource-id in de volgende indeling geretourneerd:

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Bij gebruik in de [implementatie van een beheer groep](deploy-to-management-group.md) of implementatie op Tenant niveau, wordt de resource-id in de volgende indeling geretourneerd:

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Als u de ID in andere indelingen wilt ophalen, raadpleegt u:

* [extensionResourceId](#extensionresourceid)
* [subscriptionResourceId](#subscriptionresourceid)
* [tenantResourceId](#tenantresourceid)

### <a name="remarks"></a>Opmerkingen

Het aantal para meters dat u opgeeft, varieert op basis van het feit of de resource een bovenliggende of onderliggende resource is en of de resource zich in hetzelfde abonnement of dezelfde resource groep bevindt.

Als u de resource-ID voor een bovenliggende resource in hetzelfde abonnement en dezelfde resource groep wilt ophalen, geeft u het type en de naam van de resource op.

```json
"[resourceId('Microsoft.ServiceBus/namespaces', 'namespace1')]"
```

Als u de resource-ID voor een onderliggende resource wilt ophalen, moet u rekening best Eden aan het aantal segmenten in het resource type. Geef een resource naam op voor elk segment van het resource type. De naam van het segment komt overeen met de resource die bestaat voor dat deel van de hiërarchie.

```json
"[resourceId('Microsoft.ServiceBus/namespaces/queues/authorizationRules', 'namespace1', 'queue1', 'auth1')]"
```

Als u de resource-ID voor een resource in hetzelfde abonnement maar een andere resource groep wilt ophalen, geeft u de naam van de resource groep op.

```json
"[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts', 'examplestorage')]"
```

Als u de resource-ID voor een resource in een ander abonnement en een andere resource groep wilt ophalen, geeft u de abonnements-ID en de naam van de resource groep op.

```json
"[resourceId('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
```

Vaak het geval is, moet u deze functie wilt gebruiken bij het gebruik van een storage-account of een virtueel netwerk in een andere resourcegroep. Het volgende voorbeeld laat zien hoe een resource vanaf een externe resourcegroep eenvoudig kan worden gebruikt:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "virtualNetworkName": {
          "type": "string"
      },
      "virtualNetworkResourceGroup": {
          "type": "string"
      },
      "subnet1Name": {
          "type": "string"
      },
      "nicName": {
          "type": "string"
      }
  },
  "variables": {
      "subnet1Ref": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnet1Name'))]"
  },
  "resources": [
  {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[parameters('nicName')]",
      "location": "[parameters('location')]",
      "properties": {
          "ipConfigurations": [{
              "name": "ipconfig1",
              "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "subnet": {
                      "id": "[variables('subnet1Ref')]"
                  }
              }
          }]
       }
  }]
}
```

### <a name="resource-id-example"></a>Resource-ID-voor beeld

De volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourceid.json) retourneert de resource-id voor een opslag account in de resource groep:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "sameRGOutput": {
            "value": "[resourceId('Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "differentRGOutput": {
            "value": "[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "differentSubOutput": {
            "value": "[resourceId('11111111-1111-1111-1111-111111111111', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "nestedResourceOutput": {
            "value": "[resourceId('Microsoft.SQL/servers/databases', 'serverName', 'databaseName')]",
            "type" : "string"
        }
    }
}
```

De uitvoer uit het vorige voorbeeld met de standaardwaarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| sameRGOutput | Tekenreeks | /Subscriptions/{Current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentRGOutput | Tekenreeks | /Subscriptions/{Current-sub-id}/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentSubOutput | Tekenreeks | /Subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| nestedResourceOutput | Tekenreeks | /Subscriptions/{Current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.SQL/servers/servername/databases/databaseName |

## <a name="subscription"></a>abonnement

```json
subscription()
```

Retourneert informatie over het abonnement voor de huidige implementatie.

### <a name="return-value"></a>Retourwaarde

De functie geeft als resultaat van de volgende indeling:

```json
{
    "id": "/subscriptions/{subscription-id}",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="remarks"></a>Opmerkingen

Wanneer geneste sjablonen worden gebruikt voor het implementeren van meerdere abonnementen, kunt u het bereik opgeven voor de evaluatie van de abonnements functie. Zie [Azure-resources implementeren voor meer dan één abonnement of resource groep](cross-resource-group-deployment.md)voor meer informatie.

### <a name="subscription-example"></a>Voor beeld van abonnement

De volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/subscription.json) toont de abonnements functie die is aangeroepen in de sectie outputs.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[subscription()]",
            "type" : "object"
        }
    }
}
```

## <a name="subscriptionresourceid"></a>subscriptionResourceId

```json
subscriptionResourceId([subscriptionId], resourceType, resourceName1, [resourceName2], ...)
```

Retourneert de unieke id voor een resource die is geïmplementeerd op het abonnements niveau.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| subscriptionId |Nee |teken reeks (in GUID-indeling) |Standaard wordt het huidige abonnement. Deze waarde opgeven wanneer u nodig hebt om op te halen van een resource in een ander abonnement. |
| resourceType |Ja |tekenreeks |Het type resource, met inbegrip van de naamruimte van de resource-provider. |
| resourceName1 |Ja |tekenreeks |De naam van de resource. |
| resourceName2 |Nee |tekenreeks |Volgend resource naam segment, indien nodig. |

Ga door met het toevoegen van resource namen als para meters wanneer het resource type meer segmenten bevat.

### <a name="return-value"></a>Retourwaarde

De id wordt geretourneerd in de volgende indeling:

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Opmerkingen

U gebruikt deze functie om de resource-ID op te halen voor resources die zijn [geïmplementeerd in het abonnement](deploy-to-subscription.md) in plaats van een resource groep. De geretourneerde ID wijkt af van de waarde die wordt geretourneerd door de functie [resourceId](#resourceid) door de waarde van een resource groep niet op te nemen.

### <a name="subscriptionresourceid-example"></a>subscriptionResourceID-voor beeld

De volgende sjabloon wijst een ingebouwde rol toe. U kunt de app implementeren voor een resource groep of abonnement. De functie subscriptionResourceId wordt gebruikt om de resource-ID voor ingebouwde rollen op te halen.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "roleNameGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "A new GUID used to identify the role assignment"
            }
        }
    },
    "variables": {
        "Owner": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('roleNameGuid')]",
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

## <a name="tenantresourceid"></a>tenantResourceId

```json
tenantResourceId(resourceType, resourceName1, [resourceName2], ...)
```

Retourneert de unieke id voor een resource die is geïmplementeerd op Tenant niveau.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| resourceType |Ja |tekenreeks |Het type resource, met inbegrip van de naamruimte van de resource-provider. |
| resourceName1 |Ja |tekenreeks |De naam van de resource. |
| resourceName2 |Nee |tekenreeks |Volgend resource naam segment, indien nodig. |

Ga door met het toevoegen van resource namen als para meters wanneer het resource type meer segmenten bevat.

### <a name="return-value"></a>Retourwaarde

De id wordt geretourneerd in de volgende indeling:

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Opmerkingen

U gebruikt deze functie om de resource-ID op te halen voor een resource die wordt geïmplementeerd op de Tenant. De geretourneerde ID wijkt af van de waarden die worden geretourneerd door andere resource-ID-functies, met inbegrip van resource groep-of abonnements waarden.

## <a name="next-steps"></a>Volgende stappen

* Zie [Azure Resource Manager sjablonen ontwerpen](template-syntax.md)voor een beschrijving van de secties in een Azure Resource Manager sjabloon.
* Zie [gekoppelde sjablonen gebruiken met Azure Resource Manager](linked-templates.md)om meerdere sjablonen samen te voegen.
* Als u een bepaald aantal keer wilt herhalen bij het maken van een type resource, raadpleegt u [meerdere exemplaren van resources maken in azure Resource Manager](copy-resources.md).
* Zie [een toepassing implementeren met Azure Resource Manager sjabloon](deploy-powershell.md)voor meer informatie over het implementeren van de sjabloon die u hebt gemaakt.

