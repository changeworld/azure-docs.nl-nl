---
title: Wat is er nieuw in Azure Monitor documentatie
description: Er zijn belang rijke updates voor Azure Monitor documentatie bijgewerkt voor elke maand.
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 03/05/2020
ms.openlocfilehash: b42acdf64612da6837bc67752f7a22169ddef7e2
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78673329"
---
# <a name="whats-new-in-azure-monitor-documentation"></a>Wat is er nieuw in Azure Monitor documentatie?
In dit artikel vindt u een lijst met Azure Monitor-artikelen die nieuw zijn of aanzienlijk zijn bijgewerkt. Het wordt de eerste week van elke maand vernieuwd, zodat artikel updates van de vorige maand worden meegenomen.

## <a name="march-2020"></a>2020 maart

### <a name="agents"></a>Agents
Meerdere updates als onderdeel van het herschrijven van inhoud van de diagnostische extensie.

- [Overzicht van de Azure-bewakings agenten](platform/agents-overview.md) : herstructurering van tabellen om de unieke functies van elke agent beter te verduidelijken.
- [Overzicht van Azure Diagnostics extensie](platform/diagnostics-extension-overview.md) -herschrijven volt ooien.
- [Gebruik Blob Storage voor IIS en tabel opslag voor gebeurtenissen in azure monitor](platform/diagnostics-extension-logs.md) -algemeen herschrijven voor updates en duidelijkheid.
- [Installeer en configureer Windows Azure Diagnostics extension (WAD)](platform/diagnostics-extension-windows-install.md) -nieuw artikel. 
- [Uitbreidings schema voor Windows diagnostische gegevens](platform/diagnostics-extension-schema-windows.md) : opnieuw georganiseerd.
- [Gegevens verzenden van de Windows Azure Diagnostics-extensie naar Azure Event hubs](platform/diagnostics-extension-stream-event-hubs.md) -volledig herschreven en bijgewerkt.
- [Diagnostische gegevens opslaan en weer geven in azure Storage](platform/diagnostics-extension-to-storage.md) -volledig herschreven en bijgewerkt.
- [Log Analytics extensie voor virtuele machines voor Windows](../virtual-machines/extensions/oms-windows.md) -betere verduidelijking van de relatie met log Analytics agent.
- [Azure monitor extensie van de virtuele machine voor Linux](../virtual-machines/extensions/oms-linux.md) -beter verduidelijkt de relatie met log Analytics agent.




### <a name="application-insights"></a>Application Insights
- [Verbindings reeksen in azure-toepassing Insights](app/sdk-connection-string.md) -nieuw artikel.

### <a name="insights-and-solutions"></a>Inzichten en oplossingen

#### <a name="azure-monitor-for-containers"></a>Azure Monitor voor containers
- [Integreer Azure Active Directory met de door Azure Kubernetes service](../aks/azure-ad-integration.md) toegevoegde opmerking voor het maken van een client toepassing ter ondersteuning van een cluster met RBAC-functionaliteit ter ondersteuning van Azure monitor voor containers.

#### <a name="azure-monitor-for-vms"></a>Azure Monitor voor virtuele machines
- [Veelgestelde vragen over Azure monitor voor VM's (ga)](insights/vminsights-ga-release-faq.md) : Wijzig hoe prestatie gegevens worden opgeslagen.

#### <a name="office-365"></a>Office 365
- [Office 365-beheer oplossing in azure](insights/solution-office-365.md) -bijgewerkte datum van afschaffing.


### <a name="logs"></a>Logboeken
- [Optimaliseer logboek query's in azure monitor](log-query/query-optimization.md) -nieuw artikel.
- [Gebruik en kosten voor Azure monitor logboeken beheren](platform/manage-cost-storage.md) : verbeterde voorbeeld query's om inzicht te krijgen in uw gebruik.

### <a name="metrics"></a>Metrische gegevens
- Azure Monitor de metrische gegevens van het [platform exporteerbaar via Diagnostische instellingen](platform/metrics-supported-export-diagnostic-settings.md) : de sectie is toegevoegd bij wijzigen in gedrag voor Null-waarden en nulwaarden.


### <a name="visualizations"></a>Visualisaties
Meerdere nieuwe artikelen voor het weer geven van Designer-conversie gids voor werkmappen.

- [Azure monitor weer gave Designer naar werkmappen-overgangs gids](platform/view-designer-conversion-overview.md) : nieuw artikel.
- [Azure monitor weer gave Designer naar werkmappen conversie opties](platform/view-designer-conversion-options.md) -nieuw artikel.
- [Azure monitor weer geven Designer naar Workbook conversies](platform/view-designer-conversion-tiles.md) -nieuw artikel.
- [Azure monitor Designer weer geven voor werkmappen conversie samenvatting en toegang tot](platform/view-designer-conversion-access.md) nieuw artikel.
- [Azure monitor Designer weer geven voor werkmappen conversie common tasks](platform/view-designer-conversion-tasks.md) -nieuw artikel.
- [Azure monitor weer gave Designer naar werkmappen conversie-voor beelden](platform/view-designer-conversion-examples.md) : nieuw artikel.




## <a name="january-2020"></a>Januari 2020

### <a name="general"></a>Algemeen
- [Wat wordt er door Azure Monitor bewaakt?](monitor-reference.md) -Nieuw artikel.

### <a name="agents"></a>Agents
- [Verzamelen van logboek gegevens met Azure log Analytics-agent](platform/log-analytics-agent.md) -bijgewerkte tabel met netwerk firewall vereisten.


### <a name="alerts"></a>Waarschuwingen
- [Maak en beheer actie groepen in de Azure Portal](platform/action-groups.md) -instelling verwijderd voor v2-functies die niet meer nodig zijn.
- [Een waarschuwing voor metrische gegevens maken met een resource manager-sjabloon](platform/alerts-metric-create-templates.md) -voor beeld voor de para meter *ignoreDataBefore* .  Er zijn beperkingen toegevoegd voor regels met meerdere criteria.
- [Log Analytics waarschuwing gebruiken rest API](platform/api-alerts.md) -JSON-voor beeld is gecorrigeerd.


### <a name="application-insights"></a>Application Insights
- [IP-adressen die worden gebruikt door Application Insights en log Analytics](app/ip-addresses.md) : de sectie Beschik baarheid is bijgewerkt met informatie over het toevoegen van een binnenkomende poort regel voor het toestaan van verkeer via Azure-netwerk beveiligings groepen.
- [Problemen met Azure-toepassing Insights Profiler oplossen](app/profiler-troubleshooting.md) -bijgewerkte algemene probleem oplossing.
- De [bemonsterings-telemetrie in azure-toepassing Insights](app/sampling.md) is bijgewerkt en opnieuw gestructureerd om de Lees baarheid te verbeteren op basis van feedback van klanten.


### <a name="data-security"></a>Gegevensbeveiliging
- [Azure monitor door de klant beheerde sleutel configuratie](platform/customer-managed-keys.md) -nieuw artikel.

### <a name="insights-and-solutions"></a>Inzichten en oplossingen

#### <a name="azure-monitor-for-containers"></a>Azure Monitor voor containers
- [Azure monitor configureren voor het verzamelen van gegevens van containers](insights/container-insights-agent-config.md) , Details over het bijwerken van agents op Azure Red Hat open Shift en extra informatie toegevoegd om de methoden voor het bijwerken van de agent te onderscheiden.
- [Maak prestatie waarschuwingen voor Azure monitor voor containers](insights/container-insights-alerts.md) : herziene informatie en bijgewerkte stappen voor het maken van een waarschuwing voor prestatie gegevens die in de werk ruimte zijn opgeslagen met behulp van werk ruimte-context waarschuwingen.
- [Kubernetes bewaking met Azure monitor voor containers](insights/container-insights-analyze.md) : het artikel overzicht en het artikel analyseren met betrekking tot ondersteuning van Windows Kubernetes-clusters bijgewerkt.
- [Azure Red Hat open Shift-clusters configureren met Azure monitor voor containers](insights/container-insights-azure-redhat-setup.md) -Details toegevoegd voor het bijwerken van de agent op Azure Red Hat open Shift en extra informatie toevoegen om de methoden voor het bijwerken van de agent te onderscheiden.
- [Configureer hybride Kubernetes-clusters met Azure monitor voor containers](insights/container-insights-hybrid-setup.md) -bijgewerkt om toegevoegde ondersteuning voor beveiligde poort te weer spie gelen: 10250 met de CAdvisor van Kubelet.
- [Het beheren van de Azure monitor voor containers agent](insights/container-insights-manage-agent.md) -bijgewerkte details met betrekking tot gedrag en configuratie van metrische gegevens over metrieken met Azure Red Hat open Shift vergeleken met andere typen Kubernetes-clusters.
- [Azure monitor configureren voor containers Prometheus-integratie](insights/container-insights-prometheus-integration.md) -bijgewerkte details met betrekking tot gedrag en configuratie van metrische uitval tijd met Azure Red Hat open Shift vergeleken met andere typen Kubernetes-clusters.
- [Het bijwerken van Azure monitor voor containers voor metrische](insights/container-insights-update-metrics.md) gegevens-bijgewerkte details met betrekking tot gedrag en configuratie van metrische uitval tijd met Azure Red Hat open Shift vergeleken met andere typen Kubernetes-clusters.


#### <a name="azure-monitor-for-vms"></a>Azure Monitor voor virtuele machines
- [Veelgestelde vragen over Azure monitor voor VM's (ga)](insights/vminsights-ga-release-faq.md) : er is informatie toegevoegd over de upgrade van de werk ruimte en agents naar een nieuwe versie.

#### <a name="office-365"></a>Office 365
- [Office 365-beheer oplossing in azure](insights/solution-office-365.md) : extra informatie en veelgestelde vragen over het migreren naar Office 365-oplossing in azure Sentinel. Verwijderde onboarding sectie.



### <a name="logs"></a>Logboeken
- [Beheer log Analytics-werk ruimten in azure monitor](platform/manage-access.md) -updates op niet-acties.
- Het [gebruik en de kosten beheren voor Azure monitor-logboeken](platform/manage-cost-storage.md) -toegevoegde uitleg over de berekening van gegevens volume in de sectie prijs model.
- [Gebruik Azure Resource Manager sjablonen voor het maken en configureren van een log Analytics werk ruimte](platform/template-workspace-configuration.md) -bijgewerkte sjabloon met nieuwe prijs categorieën.


### <a name="platform-logs"></a>Platformlogboeken
- [Azure-activiteiten logboek verzamelen met Diagnostische instellingen-Azure monitor](platform/diagnostic-settings-legacy.md) -aanvullende informatie over gewijzigde eigenschappen.
- [Exporteer het Azure-activiteiten logboek](platform/activity-log-export.md) -bijgewerkt voor wijzigingen in de gebruikers interface. 





## <a name="december-2019"></a>December 2019

### <a name="agents"></a>Agents
- [Linux-computers verbinden met Azure monitor](platform/agent-linux.md) -nieuw artikel.

### <a name="alerts"></a>Waarschuwingen
- [Een waarschuwing voor metrische gegevens maken met een resource manager-sjabloon](platform/alerts-metric-create-templates.md) -voor beeld toegevoegd voor aangepaste metrische gegevens.
- [Het maken van waarschuwingen met dynamische drempel waarden in](platform/alerts-dynamic-thresholds.md) de sectie Azure monitor-added over het interpreteren van dynamische drempel grafieken.
- [Overzicht van waarschuwings-en meldings bewaking in azure](platform/alerts-overview.md) -bijgewerkte resource grafiek query.
- [Ondersteunde resources voor metrische waarschuwingen in azure monitor](platform/alerts-metric-near-real-time.md) -bijwerken naar metrische gegevens en dimensies die worden ondersteund.
- [Overschakelen van verouderde API voor log Analytics waarschuwingen naar de nieuwe Azure Alerts API](platform/alerts-log-api-switch.md) -toegevoegde notitie voor de gewijzigde naam van de waarschuwing.
- [Begrijpen hoe metrische waarschuwingen werken in Azure Monitor.](platform/alerts-metric-overview.md) -Ondersteunde resource typen zijn toegevoegd voor bewaking op schaal.

### <a name="application-insights"></a>Application Insights
- [Application Insights voor Worker-service-apps (niet-http-apps)](app/worker-service.md) -standaard C# logboek registratie niveau toegevoegd aan code. Bijgewerkte pakket referentie versie.
- [ApplicationInsights. config-referentie-Azure](app/configuration-with-applicationinsights-config.md) -bijgewerkte voorbeeld code.
- [Automatiseer Azure-toepassing inzichten met Power shell](app/powershell.md) -update naar Resource Manager-sjabloon.
- [Azure Monitor Application Insights NuGet-pakketten](app/nuget.md) -bijgewerkte pakket versies.
- [Een nieuwe Azure-toepassing Insights-resource maken](app/create-new-resource.md) : opmerking wordt toegevoegd aan een wereld wijd unieke naam.
- [Diagnose met Live Metrics stream-Azure-toepassing Insights](app/live-stream.md) -bijgewerkte ASP.net core SDK-versie vereiste.
- [Gebeurtenis tellers in Application Insights](app/eventcounters.md) -bijgewerkte categorie en tabel naar customMetrics.
- [Verken Java-traceer Logboeken in azure-toepassing inzichten](app/java-trace-logs.md) -toegevoegde configuratie voor de drempel waarde voor logboek registratie van Java Agent.
- [IP-adressen die worden gebruikt door Application Insights en log Analytics](app/ip-addresses.md) geactualiseerde IP-adressen voor Live Metrics stream.
- De [prestaties van Azure app Services](app/azure-web-apps.md) -ondersteuning voor ASP.net Core 3,0 controleren. 
- [Bewaak python-toepassingen met Azure monitor (preview)](app/opencensus-python.md) : toegevoegde verduidelijking voor de python-schema toewijzing van opentellingen aan Azure. Schema controleren
- [Release opmerkingen voor Azure-toepassing Insights](app/release-notes.md) -notities toegevoegd voor oudere versies.
- [Telemetrie-kanalen in azure-toepassing Insights](app/telemetry-channels.md) -bijgewerkte duur voor verwijderde gegevens gedurende een langere periode van de verbinding is verbroken.
- [Telemetrie-bemonstering in azure-toepassing Insights](app/sampling.md) -gecorrigeerd code fragment voor aangepaste TelemetryInitializer.
- [Problemen met Application Insights oplossen in een Java-webproject](app/java-troubleshoot.md) -verwijderde instructie over het niet ondersteunen van afhankelijkheids verzamelingen in JDK 9.

### <a name="insights-and-solutions"></a>Inzichten en oplossingen
- [Azure monitor voor containers Veelgestelde vragen](insights/container-insights-faq.md) : er is een vraag toegevoegd over de velden afbeelding en naam.
- [Azure SQL-analyse oplossing in azure monitor](insights/azure-sql.md) -bijgewerkte data base wacht op ondersteuning van beheerde exemplaren.
- [Azure monitor configureren voor het verzamelen van gegevens door containers agent](insights/container-insights-agent-config.md) -instellingen toegevoegd voor enrich_container_logs.
- [Configureer hybride Kubernetes-clusters met Azure monitor voor](insights/container-insights-hybrid-setup.md) de sectie voor het oplossen van problemen die door containers zijn toegevoegd.
- [Controleer Active Directory replicatie status met Azure monitor](insights/ad-replication-status.md) -.NET Framework vereiste update.
- [Netwerkprestatiemeter oplossing in door Azure](insights/network-performance-monitor.md) toegevoegde ondersteunde regio's.
- [Optimaliseer uw Active Directory-omgeving met Azure monitor](insights/ad-assessment.md) -.NET Framework vereiste updates.
- [Optimaliseer uw SQL Server-omgeving met Azure monitor](insights/sql-assessment.md) -.NET Framework vereiste updates.
- [Optimaliseer uw System Center Operations Manager-omgeving met Azure log Analytics](insights/scom-assessment.md) -.NET Framework vereiste updates.
- [Ondersteunde verbindingen met IT Service Management-connector in Azure log Analytics](platform/itsmc-connections.md) toegevoegde New York aan de vereiste client-id en client Secret.

### <a name="logs"></a>Logboeken
- [Azure log Analytics Workspace verwijderen en herstellen](platform/delete-workspace.md) -Power shell-methode is toegevoegd.
- Als [u uw Azure monitor ontwerpt, wordt het aantal implementaties](platform/design-logs-deployment.md) voor een werk ruimte verhoogd.

### <a name="metrics"></a>Metrische gegevens
- Azure Monitor de metrische gegevens van het [platform exporteerbaar via Diagnostische instellingen](platform/metrics-supported-export-diagnostic-settings.md) -nieuw artikel.

### <a name="platform-logs"></a>Platformlogboeken
Er zijn meerdere artikelen bijgewerkt als onderdeel van het herstructureren van inhoud voor platform logboeken op basis van een nieuwe functie voor het configureren van het activiteiten logboek met Diagnostische instellingen.

- [Azure-resource logboeken archiveren in een opslag account](platform/resource-logs-collect-storage.md)
- [Gebeurtenis schema voor Azure-activiteiten logboek](platform/activity-log-schema.md)
- [Azure Monitor-service limieten](service-limits.md)
- [Azure-activiteiten Logboeken in Log Analytics werk ruimte verzamelen en analyseren](platform/activity-log-collect.md)
- [Azure-activiteiten logboek verzamelen met Diagnostische instellingen (preview)-Azure Monitor](platform/diagnostic-settings-legacy.md)
- [Azure-activiteiten logboeken verzamelen in een Log Analytics werkruimte over Azure-tenants](platform/activity-log-collect-tenants.md)
- [Azure-resource logboeken verzamelen in Log Analytics werk ruimte](platform/resource-logs-collect-workspace.md)
- [Een diagnostische instelling maken in azure met behulp van de Resource Manager-sjabloon](platform/diagnostic-settings-template.md)
- [Diagnostische instelling maken voor het verzamelen van Logboeken en metrische gegevens in azure](platform/diagnostic-settings.md)
- [Het Azure-activiteiten logboek exporteren](platform/activity-log-export.md)
- [Overzicht van Azure platform-logboeken](platform/platform-logs-overview.md)
- [Azure-bewakings gegevens streamen naar Event Hub](platform/stream-monitoring-data-event-hubs.md)
- [Azure-platform logboeken streamen naar een Event Hub](platform/resource-logs-stream-event-hubs.md)

### <a name="quickstarts-and-tutorials"></a>Snelstarts en zelfstudies

- [Maak een grafiek met metrische gegevens in azure monitor](learn/tutorial-metrics-explorer.md) -nieuw artikel.
- [Verzamel bron logboeken van een Azure-resource en analyseer met Azure monitor](learn/tutorial-resource-logs.md) -nieuw artikel.
- [Een Azure-resource bewaken met Azure monitor](learn/quick-monitor-azure-resource.md) -nieuw artikel.
   
## <a name="next-steps"></a>Volgende stappen

- Als u een bijdrage wilt leveren aan Azure Monitor documentatie, raadpleegt u de [hand leiding voor docs-inzenders](https://docs.microsoft.com/contribute/).