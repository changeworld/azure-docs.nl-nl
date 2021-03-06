---
title: Wat wordt bewaakt door Azure Monitor
description: Verwijzing van alle services en andere resources die worden bewaakt door Azure Monitor.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/17/2020
ms.openlocfilehash: 3cd330e9c4ceba2feeb7a74cafe9f094fd03d690
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79249163"
---
# <a name="what-is-monitored-by-azure-monitor"></a>Wat wordt er door Azure Monitor bewaakt?
In dit artikel worden de verschillende toepassingen en services beschreven die door Azure Monitor worden bewaakt. 

## <a name="insights-and-core-solutions"></a>Inzichten en kern oplossingen
Kern inzichten en oplossingen worden beschouwd als onderdeel van Azure Monitor en volgen de ondersteuning en service overeenkomsten voor Azure. Ze worden ondersteund in alle Azure-regio's waar Azure Monitor beschikbaar is.

### <a name="insights"></a>Insights

Inzichten bieden een aangepaste bewakings ervaring voor bepaalde toepassingen en services. Ze verzamelen en analyseren beide logboeken en metrische gegevens.

| Inzicht | Beschrijving |
|:---|:---|
| [Application Insights](app/app-insights-overview.md) | Uitbreid bare APM-service (Application Performance Management) voor het bewaken van uw Live Web-app op elk platform. |
| [Azure Monitor voor containers](insights/container-insights-overview.md) | Bewaakt de prestaties van container werkbelastingen die zijn geïmplementeerd op Azure Container Instances of beheerde Kubernetes-clusters die worden gehost op de Azure Kubernetes-service (AKS). |
| [Azure Monitor voor Cosmos DB (preview-versie)](insights/cosmosdb-insights-overview.md) | Biedt een overzicht van de algemene prestaties, fouten, capaciteit en operationele status van al uw Azure Cosmos DB resources in een uniforme interactieve ervaring. |
| [Azure Monitor voor netwerken (preview-versie)](insights/network-insights-overview.md) | Biedt een uitgebreid overzicht van de status en metrische gegevens voor al uw netwerk bronnen. De geavanceerde zoek functie helpt u bij het identificeren van bron afhankelijkheden, het inschakelen van scenario's zoals het identificeren van resources die als host fungeren voor uw website, door eenvoudigweg te zoeken naar de naam van uw website. |
[Azure Monitor voor resource groepen (preview-versie)](insights/resource-group-insights.md) |  Sorteren en diagnose eventuele problemen die uw afzonderlijke bronnen ondervinden, terwijl u context biedt voor de status en prestaties van de resource groep als geheel. |
| [Azure Monitor voor opslag (preview-versie)](insights/storage-insights-overview.md) | Biedt uitgebreide bewaking van uw Azure Storage-accounts door een uniforme weer gave te bieden van de prestaties, capaciteit en beschik baarheid van uw Azure Storage services. |
| [Azure Monitor voor VM's (preview-versie)](insights/container-insights-overview.md) | Bewaakt uw Azure virtual machines (VM) en virtuele-machine schaal sets op schaal. De service analyseert de prestaties en status van uw Windows- en Linux-VM's en bewaakt hun processen en afhankelijkheden van andere resources en externe processen. |

### <a name="core-solutions"></a>Kern oplossingen

Oplossingen zijn gebaseerd op logboek query's en weer gaven die zijn aangepast voor een bepaalde toepassing of service. Ze verzamelen en analyseren alleen logboeken en worden na verloop van tijd afgeschaft om inzicht te krijgen in de voor keuren.

| Oplossing | Beschrijving |
|:---|:---|
| [Status van agent](insights/solution-agenthealth.md) | Analyseer de status en configuratie van Log Analytics agents. |
| [Waarschuwingsbeheer](platform/alert-management-solution.md) | Analyseer waarschuwingen die zijn verzameld van System Center Operations Manager, nagios of zabbix. |
| [Serviceoverzicht](insights/service-map.md) | Detecteert automatisch toepassings onderdelen op Windows-en Linux-systemen en wijst de communicatie tussen services toe. |



## <a name="azure-services"></a>Azure-services
De volgende tabel geeft een lijst van Azure-Services en de gegevens die ze in Azure Monitor verzamelen. 

- Metrische gegevens: de service verzamelt automatisch metrische gegevens in Azure Monitor metrische gegevens. 
- Logboeken: de service ondersteunt Diagnostische instellingen waarmee de logboeken en metrische gegevens van het platform kunnen worden verzameld voor Azure Monitor Logboeken.
- Inzicht: er is een inzicht beschikbaar voor de service die een aangepaste bewakings ervaring biedt voor de service.

| Service | Metrische gegevens | Logboeken | Inzicht | Opmerkingen |
|:---|:---|:---|:---|:---|
|Active Directory | Nee | Ja | [Ja](../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md) |  |
|Active Directory business-to-consumer | Nee | Nee | Nee |  |
|Active Directory Domain Services | Nee | Ja | Nee |  |
|Activiteitenlogboek | Nee | Ja | Nee | |
|Advanced Threat Protection | Nee | Nee | Nee |  |
|Advisor | Nee | Nee | Nee |  |
|AI-opbouw functie | Nee | Nee | Nee |  |
|Analysis Services | Ja | Ja | Nee |  |
|API voor FHIR | Nee | Nee | Nee |  |
|API-beheer | Ja | Ja | Nee |  |
|App Service | Ja | Ja | Nee |  |
|AppConfig | Nee | Nee | Nee |  |
|Application Gateway | Ja | Ja | Nee |  |
|Attestation-service | Nee | Nee | Nee |  |
|Automatisering | Ja | Ja | Nee |  |
|Azure Service Manager (RDFE) | Nee | Nee | Nee |  |
|Back-up maken | Nee | Ja | Nee |  |
|Bastion | Nee | Nee | Nee |  |
|Batch | Ja | Ja | Nee |  |
|Batch AI | Nee | Nee | Nee |  |
|Blockchain Service | Nee | Ja | Nee |  |
|Blauwdrukken | Nee | Nee | Nee |  |
|Bot-service | Nee | Nee | Nee |  |
|Cloud Services | Ja | Ja | Nee | De agent die is vereist om het gast besturingssysteem en de werk stromen te bewaken.  |
|Cloud Shell | Nee | Nee | Nee |  |
|Cognitive Services | Ja | Ja | Nee |  |
|Container Instances | Ja | Nee | Nee |  |
|Container Registry | Ja | Ja | Nee |  |
|Content Delivery Network (CDN) | Nee | Ja | Nee |  |
|Cosmos DB | Ja | Ja | [Ja](insights/cosmosdb-insights-overview.md) |  |
|Cost Management | Nee | Nee | Nee |  |
|Data Box | Nee | Nee | Nee |  |
|Data Catalog Gen2 | Nee | Nee | Nee |  |
|Data Explorer | Ja | Ja | Nee |  |
|Data Factory | Ja | Ja | Nee |  |
|Data Factory v2 | Nee | Ja | Nee |  |
|Gegevens share | Nee | Nee | Nee |  |
|Data Base voor MariaDB | Ja | Ja | Nee |  |
|Data Base voor MySQL | Ja | Ja | Nee |  |
|Data Base voor PostgreSQL | Ja | Ja | Nee |  |
|Database Migration Service | Nee | Nee | Nee |  |
|Databricks | Nee | Ja | Nee |  |
|DDoS Protection | Ja | Ja | Nee |  |
|DevOps | Nee | Nee | Nee |  |
|DNS | Ja | Nee | Nee |  |
|Domein namen | Nee | Nee | Nee |  |
|DPS | Nee | Nee | Nee |  |
|Dynamics 365-klant betrokkenheid | Nee | Nee | Nee |  |
|Dynamics 365-Financiën en-bewerkingen | Nee | Nee | Nee |  |
|Event Grid | Ja | Nee | Nee |  |
|Event Hubs | Ja | Ja | Nee |  |
|ExpressRoute | Ja | Ja | Nee |  |
|Firewall | Ja | Ja | Nee |  |
|Front Door | Ja | Ja | Nee |  |
|Functies | Ja | Ja | Nee |  |
|HDInsight | Nee | Ja | Nee |  |
|HPC-cache | Nee | Nee | Nee |  |
|Gegevensbeveiliging | Nee | Ja | Nee |  |
|Intune | Nee | Ja | Nee |  |
|IoT Central | Nee | Nee | Nee |  |
|IoT Hub | Ja | Ja | Nee |  |
|Key Vault | Ja | Ja | Nee |  |
|Kubernetes Service (AKS) | Nee | Nee | [Ja](insights/container-insights-overview.md)  |  |
|Load Balancer | Ja | Ja | Nee |  |
|Logic Apps | Ja | Ja | Nee |  |
|Machine Learning-service | Nee | Nee | Nee |  |
|Beheerde toepassingen  | Nee | Nee | Nee |  |
|Kaarten  | Nee | Nee | Nee |  |
|Media Services | Ja | Ja | Nee |  |
|Microsoft Flow | Nee | Nee | Nee |  |
|Micro soft Managed Desktop | Nee | Nee | Nee |  |
|Microsoft PowerApps | Nee | Nee | Nee |  |
|Sociale betrokkenheid van micro soft | Nee | Nee | Nee |  |
|Microsoft Stream | Ja | Ja | Nee |  |
|Migreren | Nee | Nee | Nee |  |
|Meervoudige verificatie | Nee | Ja | Nee |  |
|Network Watcher | Ja | Ja | Nee |  |
|Meldingshubs | Ja | Nee | Nee |  |
|Open gegevenssets | Nee | Nee | Nee |  |
|Beleid | Nee | Nee | Nee |  |
|Power BI | Ja | Ja | Nee |  |
|Power BI Embedded | Nee | Nee | Nee |  |
|Private Link | Nee | Nee | Nee |  |
|Communicatie platform voor project spooler | Nee | Nee | Nee |  |
|Red Hat OpenShift | Nee | Nee | Nee |  |
|Redis Cache | Ja | Ja | Nee |  |
|Resource Graph | Nee | Nee | Nee |  |
|Resource Manager | Nee | Nee | Nee |  |
|Retail-zoek opdracht: door Bing | Nee | Nee | Nee |  |
|Zoeken | Ja | Ja | Nee |  |
|Service Bus | Ja | Ja | Nee |  |
|Infrastructuur van service | Nee | Ja | Nee | De agent die is vereist om het gast besturingssysteem en de werk stromen te bewaken.  |
|Aanmeldings Portal | Nee | Nee | Nee |  |
|Site Recovery | Nee | Ja | Nee |  |
|Lente-Cloud service | Nee | Nee | Nee |  |
|SQL Data Warehouse | Ja | Ja | Nee |  |
|SQL Database | Ja | Ja | Nee |  |
|SQL Server Stretch Database | Ja | Ja | Nee |  |
|Stack | Nee | Nee | Nee |  |
|Opslag | Ja | Nee | [Ja](insights/storage-insights-overview.md) |  |
|Opslag cache | Nee | Nee | Nee |  |
|Opslag synchronisatie Services | Nee | Nee | Nee |  |
|Stream Analytics | Ja | Ja | Nee |  |
|Time Series Insights | Ja | Ja | Nee |  |
|TINA | Nee | Nee | Nee |  |
|Traffic Manager | Ja | Ja | Nee |  |
|Universeel afdrukken | Nee | Nee | Nee |  |
|Virtual Machine Scale Sets | Nee | Ja | [Ja](insights/vminsights-overview.md) | De agent die is vereist om het gast besturingssysteem en de werk stromen te bewaken. |
|Virtuele machines | Ja | Ja | [Ja](insights/vminsights-overview.md) | De agent die is vereist om het gast besturingssysteem en de werk stromen te bewaken. |
|Virtueel netwerk | Ja | Ja | [Ja](insights/network-insights-overview.md) |  |
|Virtual Network-NSG-stroom logboeken | Nee | Ja | Nee |  |
|VPN Gateway | Ja | Ja | Nee |  |
|Windows Virtual Desktop | Nee | Nee | Nee |  |


## <a name="product-integrations"></a>Product integraties
De services en oplossingen in de volgende tabel slaan hun gegevens op in een Log Analytics-werk ruimte, zodat deze kunnen worden geanalyseerd met andere logboek gegevens die worden verzameld door Azure Monitor.

| Product/Service | Beschrijving |
|:---|:---|
| [Azure Automation](/azure/automation/) | Updates van het besturings systeem beheren en wijzigingen bijhouden op Windows-en Linux-computers. Zie [Wijzigingen bijhouden](../automation/change-tracking.md) en [updatebeheer](../automation/automation-update-management.md). |
| [Azure Information Protection](https://docs.microsoft.com/azure/information-protection/) | U kunt documenten en e-mail berichten classificeren en optioneel beveiligen. Zie [centrale rapportage voor Azure Information Protection](https://docs.microsoft.com/azure/information-protection/reports-aip#configure-a-log-analytics-workspace-for-the-reports). |
| [Azure Security Center](/azure/security-center/) | Verzamelen en analyseren van beveiligings gebeurtenissen en het uitvoeren van bedreigings analyses. [Gegevens verzameling in azure Security Center](/azure/security-center/security-center-enable-data-collection) weer geven |
| [Azure-Sentinel](/azure/sentinel/) | Maakt verbinding met verschillende bronnen, waaronder Office 365 en Amazon Web Services Cloud Trail. Zie [verbinding maken met gegevens bronnen](/azure/sentinel/connect-data-sources). |
| [Key Vault-analyse](insights/azure-key-vault.md) | Analyseer Azure Key Vault audit event-Logboeken. |
| [Microsoft Intune](https://docs.microsoft.com/intune/) | Een diagnostische instelling maken om logboeken naar Azure Monitor te verzenden. Zie [logboek gegevens naar opslag, Event hubs of log Analytics verzenden in intune (preview)](https://docs.microsoft.com/intune/fundamentals/review-logs-using-azure-monitor).  |
| Netwerk  | [Netwerkprestatiemeter](insights/network-performance-monitor.md) -Controleer de netwerk verbinding en prestaties voor service-en toepassings eindpunten.<br>[Azure-toepassing gateway](insights/azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-azure-monitor) -logboeken en metrische gegevens van Azure-toepassing gateway analyseren.<br>[Traffic Analytics](/azure/network-watcher/traffic-analytics) -Network Watcher netwerk beveiligings groep (NSG) stroom logboeken analyseren om inzicht te krijgen in de verkeers stroom in uw Azure-Cloud. |
| [Office 365](insights/solution-office-365.md) | Uw Office 365-omgeving bewaken. Bijgewerkte versie met verbeterde onboarding beschikbaar via Azure Sentinel. |
| [SQL-analyse](insights/azure-sql.md) | Bewaak de prestaties van Azure SQL-data bases, elastische Pools en beheerde exemplaren op schaal en op meerdere abonnementen. |
| [Surface Hub](insights/surface-hubs.md) | De status en het gebruik van Surface Hub apparaten bijhouden. |
| [System Center Operations Manager](https://docs.microsoft.com/system-center/scom) | Gegevens verzamelen van Operations Manager agents door hun beheer groep te verbinden met Azure Monitor. Zie [Operations Manager verbinding maken met Azure monitor](platform/om-agents.md)<br> Evalueer het risico en de status van uw System Center Operations Manager-beheer groep met [Operations Manager-beoordelings](insights/scom-assessment.md) oplossing. |
| [Micro soft teams-kamers](https://docs.microsoft.com/microsoftteams/room-systems/azure-monitor-deploy) | Geïntegreerd, end-to-end-beheer van micro soft teams-apparaten. |
| [Visual Studio App Center](https://docs.microsoft.com/appcenter/) | Bouw, test en distribueer toepassingen en controleer hun status en gebruik. Zie [beginnen met het analyseren van uw mobiele app met app Center en Application Insights](learn/mobile-center-quickstart.md). |
| Windows | [Windows Update naleving](https://docs.microsoft.com/windows/deployment/update/update-compliance-get-started) : Evalueer de upgrades van uw Windows-bureau blad.<br>[Desktop Analytics](https://docs.microsoft.com/configmgr/desktop-analytics/overview) : integreert met Configuration Manager om inzicht en intelligentie te bieden, zodat u meer weloverwogen beslissingen kunt nemen over de update gereedheids van uw Windows-clients. |



## <a name="other-solutions"></a>Andere oplossingen
Andere oplossingen zijn beschikbaar voor het bewaken van verschillende toepassingen en services, maar actieve ontwikkeling is gestopt en is mogelijk niet beschikbaar in alle regio's. Deze worden gedekt door de Azure Log Analytics gegevens opname service level agreement.

| Oplossing | Beschrijving |
|:---|:---|
| [Active Directory status controle](insights/ad-assessment.md) | Het risico en de status van uw Active Directory omgevingen evalueren. |
| [Replicatie status van Active Directory](insights/ad-replication-status.md) | Bewaakt uw Active Directory-omgeving regel matig voor replicatie fouten. |
| [Activiteiten logboek analyse](platform/activity-log-view.md#activity-logs-analytics-monitoring-solution) | Analyseer vermeldingen in het activiteiten logboek met vooraf gedefinieerde logboek query's en weer gaven. |
| [DNS-analyse (preview-versie)](insights/dns-analytics.md) | Verzamelt, analyseert en correleert Windows DNS analytic-en audit logboeken en andere gerelateerde gegevens van uw DNS-servers. |
| [Cloud Foundry](../cloudfoundry/cloudfoundry-oms-nozzle.md) | Verzamel, Bekijk en analyseer uw Cloud Foundry systeem status-en prestatie gegevens over meerdere implementaties. |
| [Containers](insights/containers.md) | Docker-en Windows-container-hosts weer geven en beheren. |
| [Evaluaties op aanvraag](https://docs.microsoft.com/services-hub/health/getting_started_with_on_demand_assessments) | De beschik baarheid, de beveiliging en de prestaties van uw on-premises, hybride en Cloud micro soft-technologie omgevingen beoordelen en optimaliseren. |
| [SQL-status controle](insights/sql-assessment.md) | Het risico en de status van uw SQL Server omgevingen evalueren.  |
| [Bedradingsgegevens](insights/wire-data.md) | Geconsolideerde netwerk-en prestatie gegevens die zijn verzameld van met Windows verbonden en Linux verbonden computers met de Log Analytics-agent. |


## <a name="third-party-integration"></a>Integratie van derden

| Oplossing | Beschrijving |
|:---|:---|
| [ITSM](platform/itsmc-overview.md) | Met de IT Service Management-connector (ITSMC) kunt u verbinding maken met Azure en een ondersteund ITSM-product (IT Service Management).  |


## <a name="resources-outside-of-azure"></a>Bronnen buiten Azure
Azure Monitor kunt gegevens verzamelen van resources buiten Azure met behulp van de methoden die in de volgende tabel worden weer gegeven.

| Resource | Methode |
|:---|:---|
| Toepassingen | Bewaak webtoepassingen buiten Azure met Application Insights. Zie [Wat is Application Insights?](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). |
| Virtuele machines | Gebruik de Log Analytics-agent voor het verzamelen van gegevens van het gast besturingssysteem van virtuele machines in andere Cloud omgevingen of on-premises. Zie [logboek gegevens verzamelen met de log Analytics-agent](platform/log-analytics-agent.md). |
| REST API-client | Er zijn afzonderlijke Api's beschikbaar voor het schrijven van gegevens naar Azure Monitor logboeken en meet waarden van een REST API-client. Zie [logboek gegevens naar Azure monitor verzenden met de http data collector-API](platform/data-collector-api.md) voor logboeken en [aangepaste metrische gegevens voor een Azure-resource naar het Azure monitor metrische archief verzenden met behulp van een rest API](platform/metrics-store-custom-rest-api.md) voor metrieken. |



## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [Azure monitor-gegevens platform waarin de logboeken en gegevens worden opgeslagen die door inzichten en oplossingen zijn verzameld](platform/data-platform.md).
- Voltooi een [zelf studie over het bewaken van een Azure-resource](learn/tutorial-resource-logs.md).
- Voltooi een [zelf studie over het schrijven van een logboek query voor het analyseren van gegevens in azure monitor logboeken](learn/tutorial-resource-logs.md).
- Voltooi een [zelf studie over het maken van een metrische grafiek voor het analyseren van gegevens in azure monitor meet waarden](learn/tutorial-metrics-explorer.md).

 
