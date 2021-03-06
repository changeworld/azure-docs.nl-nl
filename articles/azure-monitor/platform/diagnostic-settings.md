---
title: Diagnostische instelling maken voor het verzamelen van Logboeken en metrische gegevens in azure
description: Diagnostische instellingen maken voor het door sturen van Azure-platform logboeken naar Azure Monitor-logboeken, Azure-opslag of Azure Event Hubs.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: fb2f9ff5af68575d9f9d29e9a6aca83d603395b3
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77672409"
---
# <a name="create-diagnostic-setting-to-collect-platform-logs-and-metrics-in-azure"></a>Diagnostische instelling maken voor het verzamelen van platform logboeken en metrische gegevens in azure
[Platform logboeken](platform-logs-overview.md) in azure, inclusief Azure-activiteiten logboek en resource logboeken, bieden gedetailleerde informatie over diagnostische gegevens en controle voor Azure-resources en het Azure-platform waarvan ze afhankelijk zijn. In dit artikel vindt u informatie over het maken en configureren van diagnostische instellingen voor het verzenden van platform logboeken naar verschillende bestemmingen.

> [!IMPORTANT]
> Voordat u een diagnostische instelling maakt om het activiteiten logboek te verzamelen, moet u eerst alle verouderde configuratie uitschakelen. Zie [Azure-activiteiten logboek verzamelen met verouderde instellingen](diagnostic-settings-legacy.md) voor meer informatie.

Elke Azure-resource vereist een eigen diagnostische instelling, waarmee het volgende wordt gedefinieerd:

- Categorieën van Logboeken en metrische gegevens die worden verzonden naar de bestemmingen die in de instelling zijn gedefinieerd. De beschik bare categorieën verschillen per resource type.
- Een of meer bestemmingen voor het verzenden van de logboeken. Huidige doelen bevatten Log Analytics-werk ruimte, Event Hubs en Azure Storage.
 
Een enkele diagnostische instelling kan niet meer dan een van de doelen definiëren. Als u gegevens wilt verzenden naar meer dan een van een bepaald type bestemming (bijvoorbeeld twee verschillende Log Analytics werk ruimten), maakt u meerdere instellingen. Elke resource kan Maxi maal vijf Diagnostische instellingen hebben.


> [!NOTE]
> De [metrische gegevens](metrics-supported.md) van het platform worden automatisch verzameld om [Azure monitor metrische gegevens](data-platform-metrics.md). Diagnostische instellingen kunnen worden gebruikt voor het verzamelen van metrische gegevens voor bepaalde Azure-Services in Azure Monitor logboeken voor analyse met andere bewakings informatie met behulp van [logboek query's](../log-query/log-query-overview.md).

## <a name="destinations"></a>Ontvangst 
Platform logboeken kunnen worden verzonden naar de doelen in de volgende tabel. De configuratie voor elke bestemming wordt uitgevoerd met hetzelfde proces voor het maken van diagnostische instellingen die in dit artikel worden beschreven. Volg elke koppeling in de volgende tabel voor meer informatie over het verzenden van gegevens naar deze bestemming.

| Doel | Beschrijving |
|:---|:---|
| [Log Analytics-werkruimte](resource-logs-collect-workspace.md) | Door Logboeken te verzamelen in een Log Analytics-werk ruimte kunt u ze analyseren met andere bewakings gegevens die zijn verzameld door Azure Monitor met behulp van krachtige logboek query's en ook om gebruik te maken van andere Azure Monitor functies, zoals waarschuwingen en visualisaties. |
| [Event hubs](resource-logs-stream-event-hubs.md) | Door Logboeken naar Event Hubs te verzenden, kunt u gegevens streamen naar externe systemen, zoals Siem's van derden en andere log Analytics-oplossingen. |
| [Azure Storage-account](resource-logs-collect-storage.md) | Het archiveren van logboeken naar een Azure Storage-account is handig voor controle, statische analyses of back-ups. |

## <a name="create-diagnostic-settings-in-azure-portal"></a>Diagnostische instellingen maken in Azure Portal
U kunt Diagnostische instellingen configureren in de Azure Portal in het menu Azure Monitor of in het menu voor de resource.

1. Waar u de diagnostische instellingen in de Azure Portal configureert, is afhankelijk van de resource.

   - Voor één resource klikt u op **Diagnostische instellingen** onder **monitor** in het resource menu.

        ![Diagnostische instellingen](media/diagnostic-settings/menu-resource.png)

    - Klik voor een of meer resources op **Diagnostische instellingen** onder **instellingen** in het menu Azure monitor en klik vervolgens op de resource.
    
        ![Diagnostische instellingen](media/diagnostic-settings/menu-monitor.png)

    - Klik voor het activiteiten logboek op **activiteiten logboek** in het menu **Azure monitor** en vervolgens op **Diagnostische instellingen**. Zorg ervoor dat u alle verouderde configuraties voor het activiteiten logboek uitschakelt. Zie [bestaande instellingen uitschakelen](diagnostic-settings-legacy.md#disable-existing-settings) voor meer informatie.

        ![Diagnostische instellingen](media/diagnostic-settings/menu-activity-log.png)

2. Als er geen instellingen zijn op de resource hebt u geselecteerd, wordt u gevraagd om een instelling te maken. Klik op **Diagnostische instelling toevoegen**.

   ![Diagnostische instelling - er zijn geen bestaande instellingen toevoegen](media/diagnostic-settings/add-setting.png)

   Als er bestaande instellingen zijn voor de resource, ziet u een lijst met instellingen die al zijn geconfigureerd. Klik op **Diagnostische instelling toevoegen** om een nieuwe instelling of **Bewerk instelling** toe te voegen die u kunt bewerken. Elke instelling kan niet meer dan één van de doel typen hebben.

   ![Diagnostische instelling - bestaande instellingen toevoegen](media/diagnostic-settings/edit-setting.png)

3. Geef een naam op voor uw instelling als deze nog niet aanwezig is.
4. Schakel het selectie vakje voor elke bestemming in om de logboeken te verzenden. Klik op **configureren** om de instellingen op te geven, zoals wordt beschreven in de volgende tabel.

    | Instelling | Beschrijving |
    |:---|:---|
    | Log Analytics-werkruimte | De naam van de werk ruimte. |
    | Storage-account | De naam van het opslag account. |
    | Event hub-naamruimte | De naam ruimte waar de Event Hub wordt gemaakt (als dit uw eerste keer is dat streaming-logboeken zijn) of gestreamd naar (als er al resources zijn die de logboek categorie naar deze naam ruimte streamen).
    | Naam van Event Hub | Geef eventueel een Event Hub naam op voor het verzenden van alle gegevens in de instelling. Als u geen naam opgeeft, wordt er een Event Hub voor elke logboek categorie gemaakt. Als u meerdere categorieën verzendt, wilt u mogelijk een naam opgeven om het aantal gemaakte Event hubs te beperken. Zie [Azure Event hubs quota's en limieten](../../event-hubs/event-hubs-quotas.md) voor meer informatie. |
    | De naam van een Event hub-beleid | Hiermee worden de machtigingen gedefinieerd die het streaming-mechanisme heeft. |

    ![Diagnostische instelling - bestaande instellingen toevoegen](media/diagnostic-settings/setting-details.png)

5. Schakel het selectie vakje in voor elk van de gegevens categorieën die u naar de opgegeven doelen wilt verzenden. De lijst met categorieën is afhankelijk van elke Azure-service.

   > [!NOTE]
   > Het verzenden van multidimensionale metrische gegevens via diagnostische instellingen wordt momenteel niet ondersteund. Metrische gegevens met dimensies worden geëxporteerd als platte eendimensionale metrische gegevens, als totaal van alle dimensiewaarden.
   >
   > *Een voorbeeld*: de meetwaarde 'Binnenkomende berichten' voor een Event Hub kan worden verkend en uitgezet op wachtrijniveau. Wanneer de waarde wordt geëxporteerd via diagnostische instellingen, wordt deze echter voorgesteld als alle binnenkomende berichten voor alle wachtrijen in de Event Hub.

6. Klik op **Opslaan**.

Na enkele ogen blikken wordt de nieuwe instelling weer gegeven in de lijst met instellingen voor deze resource en worden logboeken naar de opgegeven doelen gestreamd wanneer er nieuwe gebeurtenis gegevens worden gegenereerd. Houd er rekening mee dat er Maxi maal vijf tien minuten zijn tussen het moment dat een gebeurtenis wordt verzonden en wanneer deze [wordt weer gegeven in een log Analytics-werk ruimte](data-ingestion-time.md).



## <a name="create-diagnostic-settings-using-powershell"></a>Diagnostische instellingen maken met behulp van Power shell
Gebruik de cmdlet [set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) om een diagnostische instelling met [Azure PowerShell](powershell-quickstart-samples.md)te maken. Raadpleeg de documentatie voor deze cmdlet voor beschrijvingen van de para meters.

> [!IMPORTANT]
> U kunt deze methode niet gebruiken voor het Azure-activiteiten logboek. Gebruik in plaats daarvan [Diagnostische instelling maken in azure monitor met behulp van een resource manager-sjabloon](diagnostic-settings-template.md) om een resource manager-sjabloon te maken en te implementeren met Power shell.

Hieronder volgt een voor beeld van een Power shell-cmdlet om een diagnostische instelling te maken met behulp van alle drie de bestemmingen.


```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```


## <a name="create-diagnostic-settings-using-azure-cli"></a>Diagnostische instellingen maken met behulp van Azure CLI
Gebruik de opdracht [AZ monitor Diagnostic-settings Create](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create) om een diagnostische instelling te maken met [Azure cli](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest). Raadpleeg de documentatie voor deze opdracht voor beschrijvingen van de para meters.

> [!IMPORTANT]
> U kunt deze methode niet gebruiken voor het Azure-activiteiten logboek. Gebruik in plaats daarvan [Diagnostische instelling maken in azure monitor met behulp van een resource manager-sjabloon](diagnostic-settings-template.md) om een resource manager-sjabloon te maken en te implementeren met cli.

Hier volgt een voor beeld van een CLI-opdracht om een diagnostische instelling te maken met behulp van alle drie de bestemmingen.



```azurecli
az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true}]' \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

### <a name="configure-diagnostic-settings-using-rest-api"></a>Diagnostische instellingen configureren met behulp van REST API
Zie [Diagnostische instellingen](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings) voor het maken of bijwerken van diagnostische instellingen met behulp van de [Azure monitor rest API](https://docs.microsoft.com/rest/api/monitor/).


### <a name="configure-diagnostic-settings-using-resource-manager-template"></a>Diagnostische instellingen configureren met Resource Manager-sjabloon
Zie [Diagnostische instelling maken in azure monitor met behulp van een resource manager-sjabloon](diagnostic-settings-template.md) voor het maken of bijwerken van diagnostische instellingen met een resource manager-sjabloon.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over Azure platform-logboeken](platform-logs-overview.md)
