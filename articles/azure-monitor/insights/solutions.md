---
title: Bewakings oplossingen in Azure Monitor | Microsoft Docs
description: Bewakings oplossingen in Azure Monitor zijn een verzameling regels voor logica, visualisatie en gegevens aanschaf die gegevens over een bepaald probleem gebied hebben gedraaid.  Dit artikel bevat informatie over het installeren en gebruiken van bewakings oplossingen.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/13/2019
ms.openlocfilehash: a04ca3768ade6058c59393591c252bc4347a3663
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79275202"
---
# <a name="monitoring-solutions-in-azure-monitor"></a>Bewakings oplossingen in Azure Monitor
Bewakings oplossingen maken gebruik van services in azure om meer inzicht te krijgen in de werking van een bepaalde toepassing of service. Dit artikel bevat een kort overzicht van de bewakings oplossingen in Azure en informatie over het gebruik en de installatie ervan.

> [!NOTE]
> Bewakings oplossingen werden voorheen beheer oplossingen genoemd.

Bewakings oplossingen verzamelen doorgaans logboek gegevens en bieden query's en weer gaven om verzamelde gegevens te analyseren. Ze kunnen ook gebruikmaken van andere services zoals Azure Automation om uit te voeren met betrekking tot de toepassing of service.

U kunt bewakings oplossingen toevoegen aan Azure Monitor voor alle toepassingen en services die u gebruikt. Deze zijn doorgaans gratis beschikbaar, maar verzamelen gegevens die gebruiks kosten kunnen aanroepen. Naast de oplossingen die door micro soft worden geleverd, kunnen partners en klanten [beheer oplossingen maken](solutions-creating.md) voor gebruik in hun eigen omgeving of worden ze beschikbaar gesteld aan klanten via de community.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="use-monitoring-solutions"></a>Bewakingsoplossingen gebruiken
Open de pagina **overzicht** in azure monitor om een tegel weer te geven voor elke oplossing die in de werk ruimte is geïnstalleerd. 

1. Ga naar de [Azure Portal](https://ms.portal.azure.com). Zoek en selecteer **monitor**.
1. Selecteer in het menu **inzichten** **meer**.
1. Gebruik de vervolgkeuzelijsten boven aan het scherm om de werkruimte of het tijdsbereik gebruikt voor de tegels te wijzigen.
1. Klik op de tegel voor een oplossing om de bijbehorende weer gave te openen met meer gedetailleerde analyse van de verzamelde gegevens.

![Overzicht](media/solutions/overview.png)

Bewakings oplossingen kunnen meerdere typen Azure-resources bevatten en u kunt alle resources die zijn opgenomen in een oplossing, net als elke andere resource bekijken. Alle logboek query's die in de oplossing zijn opgenomen, worden bijvoorbeeld vermeld onder **oplossingen query's** in [query Explorer](../log-query/get-started-portal.md#load-queries) . u kunt deze query's gebruiken bij het uitvoeren van ad hoc analyses met [logboek query's](../log-query/log-query-overview.md).

## <a name="list-installed-monitoring-solutions"></a>Geïnstalleerde bewakings oplossingen weer geven 
Gebruik de volgende procedure om de bewakings oplossingen weer te geven die in uw abonnement zijn geïnstalleerd.

1. Ga naar de [Azure Portal](https://ms.portal.azure.com). Zoek en selecteer **oplossingen**.
1. Oplossingen die zijn geïnstalleerd in al uw werkruimten worden weergegeven. De naam van de oplossing wordt gevolgd door de naam van de werk ruimte waarin deze is geïnstalleerd.
1. Gebruik de vervolgkeuzelijsten boven aan het scherm om te filteren op abonnement of resourcegroep.


![Vermeld alle oplossingen](media/solutions/list-solutions-all.png)

Klik op de naam van een oplossing voor het openen van de pagina overzicht. Deze pagina wordt weergegeven van alle weergaven die zijn opgenomen in de oplossing en biedt verschillende opties voor de oplossing zelf en de bijbehorende werkruimte. De overzichtspagina voor een oplossing met behulp van een van de procedures boven aan de lijst met oplossingen weergeven en klik vervolgens op de naam van de oplossing.

![Eigenschappen van oplossing](media/solutions/solution-properties.png)



## <a name="install-a-monitoring-solution"></a>Een bewakings oplossing installeren
Bewakings oplossingen van micro soft en partners zijn beschikbaar via [Azure Marketplace](https://azuremarketplace.microsoft.com). U kunt zoeken naar beschikbare oplossingen en installeren met behulp van de volgende procedure. Wanneer u een oplossing installeert, moet u een [log Analytics-werk ruimte](../platform/manage-access.md) selecteren waarin de oplossing wordt geïnstalleerd en waar de gegevens worden verzameld.

1. Klik in de [lijst met oplossingen voor uw abonnement](#list-installed-monitoring-solutions)op **toevoegen**.
1. Blader of zoek naar een oplossing. U kunt ook naar oplossingen bladeren via [deze zoek koppeling](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/management-tools?page=1&subcategories=management-solutions).
1. Zoek de gewenste bewakings oplossing en lees de beschrijving.
1. Klik op **maken** om het installatie proces te starten.
1. Wanneer het installatie proces wordt gestart, wordt u gevraagd om de Log Analytics-werk ruimte op te geven en de vereiste configuratie voor de oplossing op te geven.

![Installeren van een oplossing](media/solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>Installeren van een oplossing van de community
Leden van de community kunnen oplossingen voor gegevensbeheer voor Azure-Snelstartsjablonen indienen. U kunt deze oplossingen rechtstreeks installeren of download deze sjablonen later installeren.

1. Volg het proces dat wordt beschreven in [log Analytics werk ruimte en het Automation-account](#log-analytics-workspace-and-automation-account) om een werk ruimte en een account te koppelen.
2. Ga naar de [Azure Quick](https://azure.microsoft.com/documentation/templates/)start-sjablonen. 
3. Zoeken naar een oplossing waarin u geïnteresseerd bent.
4. Selecteer de oplossing in de resultaten om de details ervan weer te geven.
5. Klik op de knop **implementeren naar Azure** .
6. U wordt gevraagd om informatie te geven, zoals de resourcegroep en locatie samen met waarden voor parameters die in de oplossing.
7. Klik op **kopen** om de oplossing te installeren.


## <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics-werkruimte en het Automation-account
Voor alle bewakings oplossingen is een [log Analytics-werk ruimte](../platform/manage-access.md) vereist voor het opslaan van gegevens die zijn verzameld door de oplossing en het vinden van zoek opdrachten in Logboeken en weer gaven. Sommige oplossingen hebben ook een [Automation-account](../../automation/automation-security-overview.md#automation-account-overview) nodig om runbooks en gerelateerde resources te bevatten. De werkruimte en account moeten voldoen aan de volgende vereisten.

* Elke installatie van een oplossing kan alleen een Log Analytics-werkruimte en een Automation-account gebruiken. U kunt de oplossing afzonderlijk installeren in meerdere werkruimten.
* Als een oplossing een Automation-account vereist, moeten klikt u vervolgens de Log Analytics-werkruimte en het Automation-account worden gekoppeld aan elkaar. Een Log Analytics-werkruimte kan alleen worden gekoppeld aan een Automation-account en een Automation-account kan slechts aan één Log Analytics-werkruimte zijn gekoppeld.
* De Log Analytics-werkruimte en het Automation-account om te worden gekoppeld, moeten zich in dezelfde resourcegroep en regio. De uitzondering is een werkruimte in de regio VS-Oost en Automation-account in VS-Oost 2.

### <a name="create-a-link-between-a-log-analytics-workspace-and-automation-account"></a>Een koppeling tussen een Log Analytics-werkruimte en een Automation-account maken
Hoe u de Log Analytics-werkruimte en het Automation-account opgeven, is afhankelijk van de installatiemethode voor uw oplossing.

* Wanneer u een oplossing installeert via de Azure Marketplace, wordt u gevraagd naar een werk ruimte en een Automation-account. De koppeling tussen beide wordt gemaakt als ze niet al is gekoppeld.
* Voor oplossingen buiten de Azure Marketplace, moet u de Log Analytics-werkruimte en het Automation-account koppelen voordat de installatie van de oplossing. U kunt dit doen door het selecteren van een oplossing in de Azure Marketplace en de Log Analytics-werkruimte en het Automation-account te selecteren. U hebt geen daadwerkelijk de oplossing installeren omdat de koppeling wordt gemaakt als de Log Analytics-werkruimte en het Automation-account zijn geselecteerd. Nadat de koppeling is gemaakt, kunt u die Log Analytics-werkruimte en het Automation-account gebruiken voor elke oplossing.

### <a name="verify-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Controleer of de koppeling tussen een Log Analytics-werkruimte en een Automation-account
U kunt controleren of de koppeling tussen een Log Analytics-werkruimte en een Automation-account met behulp van de volgende procedure.

1. Selecteer het Automation-account in Azure portal.
1. Ga naar de sectie **Verwante resources** van het menu.
1. Als de **werk ruimte** instelling is ingeschakeld, wordt dit account gekoppeld aan een log Analytics-werk ruimte. U kunt klikken op de **werk ruimte** om de details van de werk ruimte weer te geven.

## <a name="remove-a-monitoring-solution"></a>Een bewakings oplossing verwijderen
Als u een geïnstalleerde oplossing wilt verwijderen, zoekt u deze in de [lijst met geïnstalleerde oplossingen](#list-installed-monitoring-solutions). Klik op de naam van de oplossing om de bijbehorende overzichts pagina te openen en klik vervolgens op **verwijderen**.


## <a name="next-steps"></a>Volgende stappen
* Een [lijst met bewakings oplossingen van micro soft](solutions-inventory.md)ophalen.
* Meer informatie over het [maken van query's](../log-query/log-query-overview.md) voor het analyseren van gegevens die zijn verzameld door bewakings oplossingen.

