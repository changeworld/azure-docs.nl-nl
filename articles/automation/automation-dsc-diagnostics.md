---
title: Azure Automation status configuratie rapport gegevens door sturen naar Azure Monitor-logboeken
description: In dit artikel wordt beschreven hoe u de gewenste status configuratie (DSC)-rapport gegevens van Azure Automation status configuratie naar Azure Monitor Logboeken kunt verzenden om meer inzicht en beheer te bieden.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 578fcf4cd03a2d4fc8400b9e84f53206750a588c
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2020
ms.locfileid: "77430717"
---
# <a name="forward-azure-automation-state-configuration-reporting-data-to-azure-monitor-logs"></a>Azure Automation status configuratie rapport gegevens door sturen naar Azure Monitor-logboeken

Met de configuratie van Azure Automation status worden de knooppunt status gegevens gedurende 30 dagen bewaard.
U kunt knooppunt status gegevens verzenden naar uw Log Analytics-werk ruimte als u deze gegevens gedurende een langere periode wilt bewaren.
Nalevings status is zichtbaar in de Azure Portal of met Power shell, voor knoop punten en voor afzonderlijke DSC-resources in knooppunt configuraties.
Met Azure Monitor-Logboeken kunt u het volgende doen:

- Compatibiliteits informatie ophalen voor beheerde knoop punten en individuele resources
- Een e-mail of waarschuwing activeren op basis van de nalevings status
- Geavanceerde query's schrijven op uw beheerde knoop punten
- Nalevings status voor alle Automation-accounts correleren
- De nalevings geschiedenis van het knoop punt in de loop van de tijd visualiseren

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om de configuratie Rapporten van de Automation-status te verzenden naar Azure Monitor-logboeken:

- De release van [Azure PowerShell](/powershell/azure/overview) november 2016 of hoger (v 2.3.0).
- Een Azure Automation-account. Zie [een inleiding tot Azure Automation](automation-intro.md)voor meer informatie.
- Een Log Analytics-werk ruimte met een Automation & Control service-aanbieding. Zie [aan de slag met log Analytics in azure monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)voor meer informatie.
- Ten minste één configuratie knooppunt voor Azure Automation status. Zie voor meer informatie [onboarding machines voor beheer door Azure Automation status configuratie](automation-dsc-onboarding.md).
- De [xDscDiagnostics](https://www.powershellgallery.com/packages/xDscDiagnostics/2.7.0.0) -module, versie 2.7.0.0 of hoger. Zie voor installatie stappen [problemen oplossen Azure Automation desired state Configuration](./troubleshoot/desired-state-configuration.md).

## <a name="set-up-integration-with-azure-monitor-logs"></a>Integratie met Azure Monitor-logboeken instellen

Voer de volgende stappen uit om te beginnen met het importeren van gegevens uit Azure Automation DSC in Azure Monitor logboeken:

1. Meld u aan bij uw Azure-account in Power shell. Zie [Aanmelden met Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps).
1. Haal de resource-ID van uw Automation-account op door de volgende Power shell-cmdlet uit te voeren. Als u meer dan één Automation-account hebt, kiest u de resource-ID voor het account dat u wilt configureren.

   ```powershell
   # Find the ResourceId for the Automation account
   Get-AzResource -ResourceType 'Microsoft.Automation/automationAccounts'
   ```

1. De resource-ID van uw Log Analytics-werk ruimte ophalen door de volgende Power shell-cmdlet uit te voeren. Als u meer dan één werk ruimte hebt, kiest u de resource-ID voor de werk ruimte die u wilt configureren.

   ```powershell
   # Find the ResourceId for the Log Analytics workspace
   Get-AzResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
   ```

1. Voer de volgende Power shell-cmdlet uit en vervang `<AutomationResourceId>` en `<WorkspaceResourceId>` door de *ResourceID* -waarden uit elk van de vorige stappen.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Category 'DscNodeStatus'
   ```

1. Voer de volgende Power shell-cmdlet uit als u wilt stoppen met het importeren van gegevens van Azure Automation status configuratie in Azure Monitor Logboeken.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Category 'DscNodeStatus'
   ```

## <a name="view-the-state-configuration-logs"></a>De status configuratie logboeken weer geven

Nadat u de integratie met Azure Monitor-Logboeken hebt ingesteld voor de configuratie gegevens van uw Automation-status, kunt u deze weer geven door **Logboeken** te selecteren in het gedeelte **bewaking** in het linkerdeel venster van de pagina status configuratie (DSC).

![Logboeken](media/automation-dsc-diagnostics/automation-dsc-logs-toc-item.png)

Het deel venster **Zoeken in Logboeken** wordt geopend met een query regio binnen het bereik van uw Automation-account bron. U kunt in de logboeken voor de status configuratie zoeken naar DSC-bewerkingen door in Azure Monitor logboeken te zoeken. De records voor DSC-bewerkingen worden opgeslagen in de tabel AzureDiagnostics. Als u bijvoorbeeld knoop punten zoekt die niet compatibel zijn, typt u de volgende query.

```AzureDiagnostics
| where Category == 'DscNodeStatus' 
| where OperationName contains 'DSCNodeStatusData'
| where ResultType != 'Compliant'
```
Details filteren:

* Filter op *DscNodeStatusData* om bewerkingen voor elk status configuratie knooppunt te retour neren.
* Filter op *DscResourceStatusData* om bewerkingen te retour neren voor elke DSC-resource met de naam in de knooppunt configuratie die op die resource is toegepast. 
* Filter op *DscResourceStatusData* om fout gegevens voor eventuele DSC-resources te retour neren die mislukken.

Zie [overzicht van logboek query's in azure monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)voor meer informatie over het maken van logboek query's om gegevens te zoeken.

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>Een e-mail verzenden wanneer een controle op naleving van de status configuratie mislukt

Een van de belangrijkste aanvragen van klanten is de mogelijkheid om een e-mail bericht of een tekst te verzenden wanneer er iets mis gaat met een DSC-configuratie.

Als u een waarschuwings regel wilt maken, moet u beginnen met het maken van een zoek opdracht in het logboek voor de status configuratie rapport records die de waarschuwing moeten aanroepen. Klik op de knop **+ nieuwe waarschuwings regel** om de waarschuwings regel te maken en te configureren.

1. Klik op de pagina overzicht van Log Analytics werk ruimte op **Logboeken**.
1. Maak een zoek opdracht in het logboek voor uw waarschuwing door de volgende zoek opdracht in het query veld in te voeren: `Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`

   Als u logboeken van meer dan één Automation-account of abonnement op uw werk ruimte hebt ingesteld, kunt u uw waarschuwingen groeperen op abonnement en Automation-account. De naam van het Automation-account afleiden van het veld Resource in de zoek opdracht van DscNodeStatusData.
1. Als u het scherm **regel maken** wilt openen, klikt u boven aan de pagina op **nieuwe waarschuwings regel** . 

Zie [een waarschuwings regel maken](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)voor meer informatie over de opties voor het configureren van de waarschuwing.

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Niet-werkende DSC-resources zoeken op alle knoop punten

Een voor deel van het gebruik van Azure Monitor-Logboeken is dat u kunt zoeken naar mislukte controles op verschillende knoop punten.
Alle instanties van DSC-resources zoeken die zijn mislukt:

1. Klik op de pagina overzicht van Log Analytics werk ruimte op **Logboeken**.
1. Maak een zoek opdracht in het logboek voor uw waarschuwing door de volgende zoek opdracht in het query veld in te voeren: `Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`

### <a name="view-historical-dsc-node-status"></a>Historische DSC-knooppunt status weer geven

Als u de geschiedenis van de DSC-knooppunt status gedurende een periode wilt visualiseren, kunt u deze query gebruiken:

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`

Met deze query wordt een grafiek van de knooppunt status gedurende een periode weer gegeven.

## <a name="azure-monitor-logs-records"></a>Azure Monitor registreert records

Met Azure Automation diagnostische gegevens worden twee categorieën records gemaakt in Azure Monitor logboeken:

* Status gegevens van knoop punt (DscNodeStatusData)
* Resource status gegevens (DscResourceStatusData)

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Eigenschap | Beschrijving |
| --- | --- |
| TimeGenerated |De datum en het tijdstip waarop de controle op naleving is uitgevoerd. |
| OperationName |DscNodeStatusData. |
| ResultType |Hiermee wordt aangegeven of het knoop punt compatibel is. |
| NodeName_s |De naam van het beheerde knoop punt. |
| NodeComplianceStatus_s |Hiermee wordt aangegeven of het knoop punt compatibel is. |
| DscReportStatus |Of de nalevings controle is uitgevoerd. |
| ConfigurationMode | Hoe de configuratie wordt toegepast op het knoop punt. Mogelijke waarden zijn: <ul><li>*ApplyOnly*: DSC past de configuratie toe en doet niets verder tenzij een nieuwe configuratie wordt gepusht naar het doel knooppunt of wanneer een nieuwe configuratie wordt opgehaald van een server. Na de eerste toepassing van een nieuwe configuratie controleert DSC niet op een eerder geconfigureerde status. DSC probeert de configuratie toe te passen totdat deze is voltooid voordat de *ApplyOnly* -waarde van kracht wordt. </li><li>*ApplyAndMonitor*: dit is de standaard waarde. De LCM past nieuwe configuraties toe. Als er na de eerste toepassing van een nieuwe configuratie het doel knooppunt van de gewenste status is, wordt de discrepantie in de logboeken door DSC gerapporteerd. DSC probeert de configuratie toe te passen totdat deze is voltooid voordat de *ApplyAndMonitor* -waarde van kracht wordt.</li><li>*ApplyAndAutoCorrect*: DSC past nieuwe configuraties toe. Als er na de eerste toepassing van een nieuwe configuratie het doel knooppunt van de gewenste status is, wordt de discrepantie in de logboeken door DSC gerapporteerd en wordt de huidige configuratie opnieuw toegepast.</li></ul> |
| HostName_s | De naam van het beheerde knoop punt. |
| IP-adres | Het IPv4-adres van het beheerde knoop punt. |
| Categorie | DscNodeStatus. |
| Resource | De naam van het Azure Automation-account. |
| Tenant_g | GUID waarmee de Tenant voor de oproepende functie wordt geïdentificeerd. |
| NodeId_g |GUID waarmee het beheerde knoop punt wordt aangeduid. |
| DscReportId_g |GUID waarmee het rapport wordt geïdentificeerd. |
| LastSeenTime_t |De datum en het tijdstip waarop het rapport het laatst is bekeken. |
| ReportStartTime_t |De datum en tijd waarop het rapport is gestart. |
| ReportEndTime_t |De datum en tijd waarop het rapport is voltooid. |
| NumberOfResources_d |Het aantal DSC-resources dat wordt aangeroepen in de configuratie die wordt toegepast op het knoop punt. |
| SourceSystem | Hoe Azure Monitor Logboeken de gegevens verzamelen. Altijd ' Azure ' voor Azure Diagnostics. |
| ResourceId |De id van het Azure Automation-account. |
| ResultDescription | De beschrijving voor deze bewerking. |
| SubscriptionId | De ID (GUID) van het Azure-abonnement voor het Automation-account. |
| ResourceGroup | De naam van de resource groep voor het Automation-account. |
| ResourceProvider | Micro soft. Automat. |
| ResourceType | AUTOMATIONACCOUNTS. |
| CorrelationId |GUID die de correlatie-id van het nalevings rapport is. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Eigenschap | Beschrijving |
| --- | --- |
| TimeGenerated |De datum en het tijdstip waarop de controle op naleving is uitgevoerd. |
| OperationName |DscResourceStatusData|
| ResultType |Hiermee wordt aangegeven of de resource compatibel is. |
| NodeName_s |De naam van het beheerde knoop punt. |
| Categorie | DscNodeStatus. |
| Resource | De naam van het Azure Automation-account. |
| Tenant_g | GUID waarmee de Tenant voor de oproepende functie wordt geïdentificeerd. |
| NodeId_g |GUID waarmee het beheerde knoop punt wordt aangeduid. |
| DscReportId_g |GUID waarmee het rapport wordt geïdentificeerd. |
| DscResourceId_s |De naam van het DSC-resource-exemplaar. |
| DscResourceName_s |De naam van de DSC-resource. |
| DscResourceStatus_s |Hiermee wordt aangegeven of de DSC-resource compatibel is. |
| DscModuleName_s |De naam van de Power shell-module die de DSC-resource bevat. |
| DscModuleVersion_s |De versie van de Power shell-module die de DSC-resource bevat. |
| DscConfigurationName_s |De naam van de configuratie die op het knoop punt wordt toegepast. |
| ErrorCode_s | De fout code als de resource is mislukt. |
| ErrorMessage_s |Het fout bericht als de bron is mislukt. |
| DscResourceDuration_d |De tijd, in seconden, die de DSC-resource heeft uitgevoerd. |
| SourceSystem | Hoe Azure Monitor Logboeken de gegevens verzamelen. Altijd *Azure* voor Azure Diagnostics. |
| ResourceId |Hiermee geeft u het Azure Automation-account. |
| ResultDescription | De beschrijving voor deze bewerking. |
| SubscriptionId | De ID (GUID) van het Azure-abonnement voor het Automation-account. |
| ResourceGroup | De naam van de resource groep voor het Automation-account. |
| ResourceProvider | Micro soft. Automat. |
| ResourceType | AUTOMATIONACCOUNTS. |
| CorrelationId |GUID die de correlatie-ID van het nalevings rapport is. |

## <a name="summary"></a>Samenvatting

Als u de configuratie gegevens voor de automatiserings status naar Azure Monitor-logboeken verzendt, kunt u beter inzicht krijgen in de status van uw configuratie knooppunten voor de automatiserings status.

- Waarschuwingen instellen om u op de hoogte te stellen wanneer er een probleem is
- Met aangepaste weer gaven en zoek query's kunt u de runbook-resultaten, de status van de runbook-taak en andere gerelateerde sleutel indicatoren of metrische gegevens visualiseren.

Azure Monitor-Logboeken biedt meer operationele zicht baarheid van de configuratie gegevens van de automatiserings status en kunnen incidenten sneller aanpakken.

## <a name="next-steps"></a>Volgende stappen

- Zie [Azure Automation State Configuration](automation-dsc-overview.md) (Engelstalig) voor een overzicht
- Zie aan de slag [met de configuratie van de Azure Automation-status](automation-dsc-getting-started.md) om aan de slag te gaan.
- Zie [configuraties compileren in azure Automation status configuratie](automation-dsc-compile.md) voor meer informatie over het compileren van DSC-configuraties zodat u ze aan doel knooppunten kunt toewijzen.
- Zie [Azure Automation status configuratie-cmdlets](/powershell/module/azurerm.automation/#automation) voor informatie over de Power shell-cmdlet.
- Zie [prijzen voor Azure Automation status configuratie](https://azure.microsoft.com/pricing/details/automation/) voor prijs informatie.
- Voor een voor beeld van het gebruik van Azure Automation status configuratie in een pijp lijn voor continue implementatie gaat u naar [continue implementatie met behulp van Azure Automation-status configuratie en chocolade](automation-dsc-cd-chocolatey.md)
- Zie [Zoek opdrachten in Logboeken in azure monitor logboeken](../log-analytics/log-analytics-log-searches.md) voor meer informatie over het maken van verschillende Zoek query's en het controleren van de configuratie logboeken voor de automatiserings status met Azure monitor Logboeken.
- Zie [Azure Storage-gegevens verzamelen in azure monitor logs Overview](../azure-monitor/platform/collect-azure-metrics-logs.md) voor meer informatie over Azure monitor logboeken en bronnen voor gegevens verzameling.
