---
title: Het gebruik en de kosten voor Azure Monitor logboeken beheren | Microsoft Docs
description: Meer informatie over het wijzigen van het prijs plan en het beheren van het gegevens volume en het Bewaar beleid voor uw Log Analytics-werk ruimte in Azure Monitor.
services: azure-monitor
documentationcenter: azure-monitor
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: dc784fa2dd5317932294af6e9c9d36dcce7d32f1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79274396"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>Gebruik en kosten beheren met Azure Monitor-logboeken

> [!NOTE]
> In dit artikel wordt beschreven hoe u uw kosten voor Azure Monitor-logboeken begrijpt en beheert. Een verwant artikel, het [bewaken van het gebruik en de geschatte kosten](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs) , beschrijft het weer geven van het gebruik en de geschatte kosten in meerdere Azure-bewakings functies voor verschillende prijs modellen.

> [!NOTE]
> Alle prijzen en kosten die in dit artikel worden weer gegeven, zijn alleen bedoeld als voor beeld. 

Azure Monitor-Logboeken is ontworpen voor het schalen en ondersteunen van het verzamelen, indexeren en opslaan van enorme hoeveel heden gegevens per dag vanuit elke bron in uw onderneming of die in Azure is geïmplementeerd.  Hoewel dit mogelijk een belangrijkste reden voor uw organisatie, is kostenefficiënt uiteindelijk het onderliggende stuurprogramma. Daarom is het belang rijk te weten dat de kosten van een Log Analytics werk ruimte alleen gebaseerd zijn op het volume van verzamelde gegevens, dat ook afhankelijk is van het geselecteerde plan en hoe lang u ervoor hebt gekozen om gegevens op te slaan die zijn gegenereerd op basis van uw verbonden bronnen.  

In dit artikel wordt uitgelegd hoe u opgenomen gegevens volume en opslag groei proactief kunt controleren en hoe u limieten definieert om deze kosten te beheren. 

## <a name="pricing-model"></a>Prijsmodel

De standaard prijs voor Log Analytics is een model voor **betalen naar gebruik** op basis van het gegevens volume dat is opgenomen en optioneel voor langere gegevens retentie. Het gegevens volume wordt gemeten als de grootte van de gegevens die worden opgeslagen. Elke Log Analytics-werk ruimte wordt in rekening gebracht als een afzonderlijke service en draagt bij aan de factuur voor uw Azure-abonnement. De hoeveelheid gegevens opname kan aanzienlijk zijn, afhankelijk van de volgende factoren: 

  - Het aantal beheer oplossingen dat is ingeschakeld en de configuratie ervan (bijvoorbeeld 
  - Aantal bewaakte Vm's
  - Type gegevens die worden verzameld van elke bewaakte VM 
  
Naast het betalen naar gebruik-model is Log Analytics **capaciteits reserverings** lagen waarmee u Maxi maal 25% kunt besparen op basis van de betalen naar gebruik-prijs. Met de prijzen voor capaciteits reservering kunt u een reserve ring kopen vanaf 100 GB per dag. Elk gebruik boven het reserverings niveau wordt gefactureerd op basis van het betalen naar gebruik-tarief. De lagen voor capaciteits reservering hebben een toezeggings periode van 31 dagen. Tijdens de toezeggings periode kunt u overschakelen naar een reserverings tier van een hoger niveau (waardoor de dag van 31 dagen opnieuw wordt opgestart), maar u kunt niet teruggaan naar betalen naar gebruik of naar een reserverings laag met een lagere capaciteit totdat de toezeggings periode is verstreken geïnstalleerd. 
Meer [informatie](https://azure.microsoft.com/pricing/details/monitor/) over log Analytics prijzen voor betalen per gebruik en capaciteits reservering. 

In alle prijs categorieën wordt het gegevens volume berekend op basis van een teken reeks representatie van de gegevens die worden voor bereid om te worden opgeslagen. Diverse [eigenschappen die voor alle gegevens typen gelden](https://docs.microsoft.com/azure/azure-monitor/platform/log-standard-properties) , worden niet opgenomen in de berekening van de gebeurtenis grootte, met inbegrip van `_ResourceId`, `_ItemId`, `_IsBillable` en `_BilledSize`.

Houd er ook rekening mee dat sommige oplossingen, zoals [Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/) en [Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/), hun eigen prijs model hebben. 

## <a name="estimating-the-costs-to-manage-your-environment"></a>Schatting van de kosten voor het beheren van uw omgeving 

Als u Azure Monitor-logboeken nog niet gebruikt, kunt u de [Azure monitor prijs calculator](https://azure.microsoft.com/pricing/calculator/?service=monitor) gebruiken om de kosten van het gebruik van log Analytics te schatten. Begin met het invoeren van ' Azure Monitor ' in het zoekvak en klik op de resulterende Azure Monitor tegel. Schuif omlaag in de pagina naar Azure Monitor en selecteer Log Analytics in de vervolg keuzelijst Type.  Hier kunt u het aantal Vm's en de GB aan gegevens opgeven die u naar verwachting van elke VM wilt verzamelen. Doorgaans is 1 tot 3 GB gegevens maand opgenomen van een typische Azure-VM. Als u Azure Monitor logboeken al evalueert, kunt u uw gegevens statistieken uit uw eigen omgeving gebruiken. Hieronder vindt u informatie over het bepalen [van het aantal bewaakte vm's](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-nodes-sending-data) en het [volume van de gegevens die in uw werk ruimte worden](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume)opgenomen. 

## <a name="understand-your-usage-and-estimate-costs"></a>Inzicht in uw gebruik en geschatte kosten

Als u Azure Monitor-logboeken nu gebruikt, is het eenvoudig om te begrijpen wat de kosten waarschijnlijk zijn op basis van recente gebruiks patronen. Gebruik hiervoor **log Analytics gebruik en geschatte kosten** om het gegevens gebruik te controleren en analyseren. Hier ziet u hoeveel gegevens worden verzameld door elke oplossing, hoeveel gegevens er worden bewaard en een schatting van uw kosten op basis van de hoeveelheid gegevens die wordt opgenomen en een extra Bewaar periode van meer dan het inbegrepen bedrag.

![Gebruik en geraamde kosten](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

Als u uw gegevens gedetailleerder wilt bekijken, klikt u op het pictogram aan de rechter bovenhoek van een van de grafieken op de pagina **gebruik en geschatte kosten** . Nu kunt u werken met deze query voor het verkennen van meer informatie over uw gebruik.  

![Logboeken weergeven](media/manage-cost-storage/logs.png)

Op de pagina **gebruik en geschatte kosten** kunt u uw gegevens volume voor de maand controleren. Dit omvat alle gegevens ontvangen en opgeslagen in uw Log Analytics-werkruimte.  Klik op **gebruiks gegevens** boven aan de pagina om het gebruiks dashboard weer te geven met informatie over trends op gegevens volume per bron, computers en aanbieding. Als u een daglimiet wilt weer geven en instellen of als u de retentie periode wilt wijzigen, klikt u op **gegevens volume beheer**.
 
Log Analytics kosten worden toegevoegd aan uw Azure-factuur. U kunt de details van uw Azure-factuur bekijken onder het gedeelte Facturering van de Azure Portal of in de [Azure billing-Portal](https://account.windowsazure.com/Subscriptions).  

## <a name="viewing-log-analytics-usage-on-your-azure-bill"></a>Log Analytics gebruik op uw Azure-factuur weer geven 

Azure biedt een groot aantal handige functies in de hub [Azure Cost Management en facturering](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) . Zo kunt u met de functionaliteit ' cost analysis ' uw uitgaven voor Azure-resources weer geven. Door een filter toe te voegen op resource type (aan micro soft. operationalinsights/Workspace voor Log Analytics), kunt u uw uitgaven bijhouden.

Meer informatie over uw gebruik kan worden verkregen door het [gebruik van het Azure portal te downloaden](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal). In het gedownloade werk blad kunt u het gebruik per Azure-resource (bijvoorbeeld Log Analytics-werk ruimte) per dag bekijken. In dit Excel-werk blad kunt u het gebruik van uw Log Analytics-werk ruimten vinden door eerst te filteren op de kolom meter categorie om "inzichten en analyse" weer te geven (gebruikt door enkele van de verouderde prijs categorieën) en "Log Analytics" en vervolgens een filter toe te voegen aan het exemplaar De ID-kolom bevat de naam van de werk ruimte. Het gebruik wordt weer gegeven in de kolom verbruikte hoeveelheid en de eenheid voor elk item wordt weer gegeven in de kolom eenheid.  Meer informatie is beschikbaar om u te helpen [uw Microsoft Azure factuur te begrijpen](https://docs.microsoft.com/azure/billing/billing-understand-your-bill). 

## <a name="changing-pricing-tier"></a>Prijs categorie wijzigen

Als u de Log Analytics prijs categorie van uw werk ruimte wilt wijzigen, 

1. In de Azure Portal opent u het **gebruik en de geschatte kosten** van uw werk ruimte waar u een lijst ziet van elk van de prijs categorieën die beschikbaar zijn voor deze werk ruimte.

2. Bekijk de geschatte kosten voor elk van de prijs categorieën. Deze schatting is gebaseerd op het gebruik van de afgelopen 31 dagen, dus deze kosten raming is afhankelijk van de laatste 31 dagen die representatief is voor uw typische gebruik. In het onderstaande voor beeld ziet u hoe, op basis van de gegevens patronen van de afgelopen 31 dagen, deze werk ruimte minder kost in de betalen naar gebruik-laag (#1) vergeleken met de 100 GB/dagen capaciteit reserverings tier (#2).  

    ![Prijscategorieën](media/manage-cost-storage/pricing-tier-estimated-costs.png)

3. Als u de prijs categorie hebt gecontroleerd op basis van de laatste 31 dagen van gebruik, klikt u op **selecteren**.  

U kunt [de prijs categorie ook instellen via Azure Resource Manager](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) met behulp van de `sku` para meter (`pricingTier` in de sjabloon Azure Resource Manager). 

## <a name="legacy-pricing-tiers"></a>Verouderde prijs Categorieën

Abonnementen die een Log Analytics werk ruimte hebben of Application Insights resource vóór 2 april 2018 zijn gekoppeld aan een Enterprise Agreement die zijn gestart vóór 1 februari 2019, blijven toegang hebben tot de verouderde prijs Categorieën: **gratis**, **zelfstandig (per GB)** en **per knoop punt (OMS)** .  Voor werk ruimten in de gratis prijs categorie geldt een dagelijkse gegevens opname van 500 MB (met uitzonde ring van beveiligings gegevens typen die worden verzameld door Azure Security Center) en de Bewaar periode van gegevens is beperkt tot 7 dagen. De gratis prijs categorie is alleen bedoeld voor evaluatie doeleinden. Werk ruimten in de zelfstandige of per knooppunt prijs categorie hebben een door de gebruiker geconfigureerde Bewaar periode van Maxi maal twee jaar. 

Werk ruimten die zijn gemaakt vóór 2016 april hebben ook toegang tot de oorspronkelijke **Standard** -en **Premium** -prijs categorieën die respectievelijk 30 en 365 dagen zijn bewaard. Nieuwe werk ruimten kunnen niet worden gemaakt in de prijs categorie **Standard** of **Premium** , en als een werk ruimte uit deze lagen wordt verplaatst, kan deze niet meer worden teruggezet. 

[Hier](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#log-analytics-workspaces)vindt u meer informatie over de beperkingen van de prijs categorie.

> [!NOTE]
> Als u de rechten wilt gebruiken die afkomstig zijn van het aanschaffen van OMS E1 Suite, OMS E2-Suite of OMS-invoeg toepassing voor System Center, kiest u de prijs categorie Log Analytics *per knoop punt* .

## <a name="change-the-data-retention-period"></a>Wijzigen van de bewaartermijn voor gegevens

De volgende stappen wordt beschreven hoe u configureren hoe lang logboek gegevens worden bewaard door in uw werkruimte.

### <a name="default-retention"></a>Standaard retentie

Als u de standaard retentie voor uw werk ruimte wilt instellen, 
 
1. Selecteer in de Azure Portal in uw werk ruimte de optie **gebruik en geschatte kosten** in het linkerdeel venster.
2. Klik op bovenaan de pagina **Gebruik en geschatte kosten** op **Gegevensvolumebeheer**.
3. Verplaats in het deel venster de schuif regelaar om het aantal dagen te verhogen of te verlagen en klik vervolgens op **OK**.  Als u zich in de laag *gratis* bevindt, kunt u de Bewaar periode voor gegevens niet wijzigen en moet u upgraden naar de laag betaald om deze instelling te kunnen beheren.

    ![Instelling voor het bewaren van gegevens van de werk ruimte wijzigen](media/manage-cost-storage/manage-cost-change-retention-01.png)
    
De retentie kan ook [via Azure Resource Manager worden ingesteld](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) met behulp van de para meter `retentionInDays`. Als u de gegevens retentie instelt op 30 dagen, kunt u ook een onmiddellijke opschoning van oudere gegevens activeren met behulp van de para meter `immediatePurgeDataOn30Days`, wat nuttig kan zijn voor nalevings scenario's. Deze functionaliteit is alleen beschikbaar via Azure Resource Manager. 

Twee gegevens typen--`Usage` en `AzureActivity`--worden standaard 90 dagen bewaard en er worden geen kosten in rekening gebracht voor deze Bewaar periode van 90 dagen. Deze gegevens typen zijn ook gratis van de kosten voor gegevens opname. 

### <a name="retention-by-data-type"></a>Bewaren op gegevens type

Het is ook mogelijk om verschillende Bewaar instellingen voor afzonderlijke gegevens typen op te geven. Elk gegevens type is een subresource van de werk ruimte. De SecurityEvent-tabel kan bijvoorbeeld als volgt worden behandeld in [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) :

```
/subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent
```

Houd er rekening mee dat het gegevens type (tabel) hoofdletter gevoelig is.  Als u de huidige instellingen voor het bewaren van gegevens typen van een bepaald gegevens type (in dit voor beeld SecurityEvent) wilt ophalen, gebruikt u:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
```

Als u de huidige instellingen voor het bewaren van gegevens type wilt ophalen voor alle gegevens typen in uw werk ruimte, laat u gewoon het specifieke gegevens type weg, bijvoorbeeld:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables?api-version=2017-04-26-preview
```

Ga als volgt te werk om de Bewaar periode van een bepaald gegevens type (in dit voor beeld SecurityEvent) in te stellen op 730 dagen.

```JSON
    PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
    {
        "properties": 
        {
            "retentionInDays": 730
        }
    }
```

De gegevens typen `Usage` en `AzureActivity` kunnen niet worden ingesteld met aangepaste retentie. Ze nemen het maximum van de standaard retentie van de werk ruimte of 90 dagen in beslag. 

Een uitstekend hulp programma om rechtstreeks verbinding te maken met Azure Resource Manager om Bewaar periode in te stellen op gegevens type is het OSS-hulp programma [ARMclient](https://github.com/projectkudu/ARMClient).  Meer informatie over ARMclient van artikelen op [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) en de [Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/).  Hier volgt een voor beeld van het gebruik van ARMClient, waarbij SecurityEvent-gegevens worden ingesteld op een retentie van 730 dagen:

```
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview "{properties: {retentionInDays: 730}}"
```

> [!NOTE]
> Het instellen van de Bewaar periode voor afzonderlijke gegevens typen kan worden gebruikt om de kosten voor het bewaren van gegevens te verlagen.  Voor gegevens die vanaf oktober 2019 worden verzameld (als deze functie is uitgebracht) vermindert u de retentie voor sommige gegevens typen in de loop van de tijd om uw Bewaar kosten te verlagen.  Voor de gegevens die u eerder hebt verzameld, is het instellen van een lagere Bewaar periode voor een afzonderlijk type geen invloed op de kosten voor de Bewaar periode.  

## <a name="manage-your-maximum-daily-data-volume"></a>Uw maximale dagelijkse gegevens volume beheren

Een dagelijkse limiet configureren en de dagelijkse opname van gegevens voor uw werkruimte beperken, maar wees voorzichtig als het doel mag geen aan de dagelijkse limiet bereikt.  Anders verliest u de gegevens voor de rest van de dag die invloed kan zijn op andere Azure-services en oplossingen waarvan functionaliteit mogelijk afhankelijk van de meest recente gegevens beschikbaar worden gesteld in de werkruimte.  Als gevolg hiervan de mogelijkheid om te zien en krijg een waarschuwing wanneer de voorwaarden van de status van resources ondersteuning van IT-services worden beïnvloed.  Het dagelijks kapje is bedoeld om te worden gebruikt als een manier om de onverwachte toename van het gegevens volume van uw beheerde resources te beheren en binnen uw limiet te blijven, of wanneer u niet-geplande kosten voor uw werk ruimte wilt beperken.  

Wanneer de dagelijkse limiet is bereikt, stopt het verzamelen van factureerbare gegevenstypen voor de rest van de dag. Er wordt een waarschuwings banner boven aan de pagina weer gegeven voor de geselecteerde Log Analytics-werk ruimte en er wordt een bewerkings gebeurtenis verzonden naar de *bewerkings* tabel onder **LogManagement** categorie. Het verzamelen van gegevens wordt hervat nadat het tijdstip waarop de tijd opnieuw is ingesteld onder *dagelijkse limiet is ingesteld op*. Het is raadzaam om het definiëren van een waarschuwingsregel op basis van deze bewerkingsgebeurtenis, geconfigureerd om te melden wanneer de dagelijkse limiet is bereikt. 

> [!NOTE]
> Het dagelijks kapje stopt niet het verzamelen van gegevens uit Azure Security Center, met uitzonde ring van werk ruimten waarin Azure Security Center is geïnstalleerd vóór 19 juni 2017. 

> [!NOTE]
> De latentie die inherent is aan het Toep assen van de dagelijkse limiet, kan betekenen dat het kapje niet zo precies het opgegeven niveau voor de dagelijkse Cap wordt toegepast. 

### <a name="identify-what-daily-data-limit-to-define"></a>Identificeren welke dagelijkse limiet voor gegevens definiëren

Bekijk [log Analytics gebruik en de geschatte kosten](usage-estimated-costs.md) om inzicht te krijgen in de trend van de gegevens opname en wat het dagelijkse volume Cap is dat moet worden gedefinieerd. Deze moet worden overwogen zorgvuldig, omdat het niet mogelijk om te controleren van uw bronnen nadat de limiet is bereikt. 

### <a name="set-the-daily-cap"></a>Het dagelijks kapje instellen

In de volgende stappen wordt beschreven hoe u een limiet kunt configureren voor het beheren van de hoeveelheid gegevens die Log Analytics werk ruimte wordt opgenomen per dag.  

1. Selecteer in de werkruimte in het linkerdeelvenster **Gebruik en geschatte kosten**.
2. Klik op het tabblad **gebruik en geschatte kosten** voor de geselecteerde werk ruimte op **gegevens volume beheer** vanaf de bovenkant van de pagina. 
3. Dagelijks Cap is standaard **uitgeschakeld** **: Klik op om het in te** scha kelen en stel de gegevens volume limiet in GB/dag in.

    ![Gegevens limiet Log Analytics configureren](media/manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-daily-cap-reached"></a>Waarschuwen wanneer de dagelijkse limiet is bereikt

Terwijl we een visuele hint aanwezig in Azure portal wanneer uw limiet drempelwaarde wordt voldaan, wordt dit gedrag niet per se uitlijnen voor het beheren van operationele problemen die onmiddellijke aandacht.  Voor het ontvangen van een waarschuwingsmelding, kunt u een nieuwe waarschuwingsregel maken in Azure Monitor.  Zie [waarschuwingen maken, weer geven en beheren](alerts-metric.md)voor meer informatie.

Als u aan de slag te gaan, moet u hier de aanbevolen instellingen voor de waarschuwing zijn:

- Doel: Selecteer uw Log Analytics-resource
- De criteria: 
   - Signaalnaam: aangepast zoeken in Logboeken
   - Zoekquery: bewerking | waar Details 'overschrijding ' van het quotum heeft
   - Op basis van: aantal resultaten
   - Voorwaarde: Groter is dan
   - Drempelwaarde: 0
   - Periode: 5 (minuten)
   - Frequentie: 5 (minuten)
- Naam waarschuwingsregel: dagelijkse gegevenslimiet bereikt
- Ernst: Waarschuwing (Sev 1)

Zodra de waarschuwing is gedefinieerd en de limiet is bereikt, wordt een waarschuwing wordt geactiveerd en voert het antwoord dat is gedefinieerd in de actiegroep. Dit kan uw team op de hoogte stellen via e-mail en SMS-berichten, of acties automatiseren met webhooks, Automation-runbooks of [integreren met een externe ITSM-oplossing](itsmc-overview.md#create-itsm-work-items-from-azure-alerts). 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>Het oplossen van problemen met een hoger gebruik dan verwacht

Hoger gebruik wordt veroorzaakt door een of beide volgende oorzaken:
- Meer knoop punten dan de verwachte verzen ding van gegevens naar Log Analytics werk ruimte
- Er worden meer gegevens dan verwacht verzonden naar Log Analytics-werk ruimte (mogelijk als gevolg van het gebruik van een nieuwe oplossing of een configuratie wijziging naar een bestaande oplossing)

## <a name="understanding-nodes-sending-data"></a>Informatie over knoop punten die gegevens verzenden

Gebruik voor meer informatie over het aantal knoop punten dat elke dag in de afgelopen maand de heartbeats rapporteert van de agent.

```kusto
Heartbeat 
| where TimeGenerated > startofday(ago(31d))
| summarize nodes = dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```
Het aantal knoop punten ophalen dat gegevens kan verzenden, kan worden bepaald met behulp van: 

```kusto
union withsource = tt * 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodes = dcount(computerName)
```

De follow-query kan worden gebruikt om een lijst met knoop punten te verkrijgen die gegevens verzenden (en de hoeveelheid gegevens die door elk van beide worden verzonden):

```kusto
union withsource = tt * 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

> [!NOTE]
> Gebruik deze `union withsource = tt *` query's spaarzaam als scans over gegevens typen duur zijn om uit te voeren. Met deze query wordt de oude manier voor het opvragen van gegevens per computer met het gegevens Type Usage vervangen.  

## <a name="understanding-ingested-data-volume"></a>Meer informatie over opgenomen gegevens volume

Op de pagina **gebruik en geschatte kosten** toont het diagram *gegevens opname per oplossing* het totale volume van de verzonden gegevens en hoeveel er door elke oplossing wordt verzonden. Hiermee kunt u bepalen trends, zoals of de algehele gegevensgebruik (of het gebruik door een bepaalde oplossing) groeit, stabiel blijft of afneemt. 

### <a name="data-volume-by-solution"></a>Gegevensvolume per oplossing

De query die wordt gebruikt om het factureer bare gegevens volume per oplossing weer te geven, is

```kusto
Usage 
| where TimeGenerated > startofday(ago(31d))
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(TimeGenerated, 1d), Solution | render barchart
```

Houd er rekening mee dat de component `where IsBillable = true` gegevens typen uit bepaalde oplossingen filtert waarvoor geen opname kosten in rekening worden gebracht. 

### <a name="data-volume-by-type"></a>Gegevens volume per type

U kunt verder inzoomen om gegevens trends weer te geven voor op gegevens type:

```kusto
Usage | where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| where TimeGenerated > startofday(ago(31d))
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(TimeGenerated, 1d), DataType | render barchart
```

Of om een tabel weer te geven op oplossing en type voor de afgelopen maand,

```kusto
Usage 
| where TimeGenerated > startofday(ago(31d))
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) by Solution, DataType
| sort by Solution asc, DataType asc
```

### <a name="data-volume-by-computer"></a>Gegevens volume per computer

Het gegevens type `Usage` bevat geen informatie op het niveau van de volledige versie. Als u de **grootte** van opgenomen gegevens per computer wilt zien, gebruikt u de [eigenschap](log-standard-properties.md#_billedsize)`_BilledSize`, die de grootte in bytes levert:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize BillableDataBytes = sum(_BilledSize) by  computerName | sort by Bytes nulls last
```

De [eigenschap](log-standard-properties.md#_isbillable) `_IsBillable` geeft aan of de opgenomen gegevens kosten in rekening worden gebracht.

Voor een overzicht van het **aantal** factureer bare gebeurtenissen dat per computer is opgenomen, gebruikt u 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount = count() by computerName  | sort by eventCount nulls last
```

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>Gegevens volume per Azure-resource, resource groep of abonnement

Voor gegevens van knoop punten die worden gehost in azure, kunt u de **grootte** van opgenomen gegevens __per computer__verkrijgen, met behulp van de [eigenschap](log-standard-properties.md#_resourceid)_ResourceId, die het volledige pad naar de bron bevat:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId | sort by Bytes nulls last
```

Voor gegevens van knoop punten die worden gehost in azure, kunt u de **grootte** van opgenomen gegevens __per Azure-abonnement__verkrijgen door de `_ResourceId`-eigenschap te parseren als:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize BillableDataBytes = sum(_BilledSize) by subscriptionId | sort by Bytes nulls last
```

Als `subscriptionId` naar `resourceGroup` wordt gewijzigd, wordt het factureer bare opgenomen gegevens volume per Azure-resource groep weer gegeven. 

> [!NOTE]
> Sommige velden van het gegevens type gebruik, terwijl ze nog steeds in het schema zijn, zijn afgeschaft en hun waarden worden niet meer ingevuld. Dit zijn zowel **computers** als velden met betrekking tot opname (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**, **BatchesCapped** en **AverageProcessingTimeMs**.

### <a name="querying-for-common-data-types"></a>Query's uitvoeren voor algemene gegevens typen

Om u te verdiepen in de bron van gegevens voor een bepaald type, volgen hier enkele handige voorbeelden van query's:

+ **Beveiligingsoplossing**
  - `SecurityEvent | summarize AggregatedValue = count() by EventID`
+ **Logboekbeheeroplossing**
  - `Usage | where Solution == "LogManagement" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | summarize AggregatedValue = count() by DataType`
+ **Perf-gegevenstype**
  - `Perf | summarize AggregatedValue = count() by CounterPath`
  - `Perf | summarize AggregatedValue = count() by CounterName`
+ **Gebeurtenisgegevenstype**
  - `Event | summarize AggregatedValue = count() by EventID`
  - `Event | summarize AggregatedValue = count() by EventLog, EventLevelName`
+ **Syslog-gegevenstype**
  - `Syslog | summarize AggregatedValue = count() by Facility, SeverityLevel`
  - `Syslog | summarize AggregatedValue = count() by ProcessName`
+ **AzureDiagnostics**-gegevenstype
  - `AzureDiagnostics | summarize AggregatedValue = count() by ResourceProvider, ResourceId`

### <a name="tips-for-reducing-data-volume"></a>Tips voor het gegevensvolume verminderen

Enkele suggesties voor het verminderen van het volume van de logboeken die worden verzameld zijn onder andere:

| Bron van hoog gegevensvolume | Het gegevensvolume verminderen |
| -------------------------- | ------------------------- |
| Beveiligingsgebeurtenissen            | Selecteer [normale of minimale beveiligingsgebeurtenissen](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier) <br> Wijzig het beleid voor beveiligingscontrole zodat alleen de gewenste gebeurtenissen worden verzameld. Controleer vooral de noodzaak voor het verzamelen van gebeurtenissen voor <br> - [filterplatform controleren](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [register controleren](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [bestandssysteem controleren](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [kernel-object controleren](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [greepbewerking controleren](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> -Verwijderbare opslag controleren |
| Prestatiemeteritems       | Wijzig de [Prestatiemeteritemconfiguratie](data-sources-performance-counters.md) in: <br> - Frequentie van het verzamelen van gegevens beperken <br> - Aantal prestatiemeteritems beperken |
| Gebeurtenislogboeken                 | Wijzig [Configuratie van gebeurtenislogboek](data-sources-windows-events.md) in: <br> - Aantal verzamelde gebeurtenislogboeken beperken <br> - Alleen vereiste gebeurtenisniveaus verzamelen. Bijvoorbeeld, gebeurtenissen op *informatie*niveau niet verzamelen |
| Syslog                     | Wijzig de [syslog-configuratie](data-sources-syslog.md) in: <br> - Aantal verzamelde installaties beperken <br> - Alleen vereiste gebeurtenisniveaus verzamelen. Bijvoorbeeld, gebeurtenissen op *informatie*- en *foutopsporings*niveau niet verzamelen |
| AzureDiagnostics           | Wijzig de resourcelogboekverzameling om: <br> - Het aantal resources dat logboeken naar Log Analytics verzendt te verkleinen <br> - Alleen vereiste logboeken te verzamelen |
| Oplossingsgegevens van computers die de oplossing niet nodig hebben | Gebruik [oplossingstargeting](../insights/solution-targeting.md) om gegevens te verzamelen van alleen de vereiste groepen computers. |

### <a name="getting-nodes-as-billed-in-the-per-node-pricing-tier"></a>Knoop punten ophalen als gefactureerd in de prijs categorie per knoop punt

Als u een lijst wilt ophalen met computers die worden gefactureerd als knoop punten als de werk ruimte zich in de prijs categorie verouderd per knoop punt bevindt, zoekt u naar knoop punten waarnaar **gefactureerde gegevens typen** worden verzonden (sommige gegevens typen zijn gratis). Als u dit wilt doen, gebruikt u de [eigenschap](log-standard-properties.md#_isbillable) `_IsBillable` en gebruikt u het meest linkse veld van de Fully Qualified Domain name. Hiermee wordt het aantal computers met gefactureerde gegevens per uur geretourneerd (de granulatie van de knoop punten die worden geteld en gefactureerd):

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

### <a name="getting-security-and-automation-node-counts"></a>Aantal knoop punten voor beveiliging en automatisering ophalen

Als u gebruikmaakt van de prijs categorie per knoop punt (OMS), worden de kosten berekend op basis van het aantal knoop punten en oplossingen dat u gebruikt. het aantal inzichten en analyse knooppunten waarvoor u wordt gefactureerd, wordt weer gegeven in de tabel op de pagina **gebruik en geschatte kosten** .  

Als u wilt zien van het aantal afzonderlijke knooppunten voor beveiliging, kunt u de query:

```kusto
union
(
    Heartbeat
    | where (Solutions has 'security' or Solutions has 'antimalware' or Solutions has 'securitycenter')
    | project Computer
),
(
    ProtectionStatus
    | where Computer !in~
    (
        (
            Heartbeat
            | project Computer
        )
    )
    | project Computer
)
| distinct Computer
| project lowComputer = tolower(Computer)
| distinct lowComputer
| count
```

Als u wilt zien van het aantal afzonderlijke knooppunten voor automatisering, gebruikt u de query:

```kusto
 ConfigurationData 
 | where (ConfigDataType == "WindowsServices" or ConfigDataType == "Software" or ConfigDataType =="Daemons") 
 | extend lowComputer = tolower(Computer) | summarize by lowComputer 
 | join (
     Heartbeat 
       | where SCAgentChannel == "Direct"
       | extend lowComputer = tolower(Computer) | summarize by lowComputer, ComputerEnvironment
 ) on lowComputer
 | summarize count() by ComputerEnvironment | sort by ComputerEnvironment asc
```

## <a name="create-an-alert-when-data-collection-is-high"></a>Een waarschuwing maken wanneer het verzamelen van gegevens hoog is

In deze sectie wordt beschreven hoe u een waarschuwing instelt als:
- Het gegevensvolume groter is dan een opgegeven hoeveelheid.
- Het gegevensvolume naar verwachting een opgegeven hoeveelheid gaat overschrijden.

Azure-waarschuwingen bieden ondersteuning voor [logboekwaarschuwingen](alerts-unified-log.md) die gebruikmaken van zoekquery’s. 

De volgende query geeft een resultaat wanneer er meer dan 100 GB aan gegevens in de afgelopen 24 uur is verzameld:

```kusto
union withsource = $table Usage 
| where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true 
| extend Type = $table | summarize DataGB = sum((Quantity / 1000.)) by Type 
| where DataGB > 100
```

De volgende query gebruikt een eenvoudige formule om te voorspellen wanneer meer dan 100 GB aan gegevens op een dag wordt verzonden: 

```kusto
union withsource = $table Usage 
| where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true 
| extend Type = $table 
| summarize EstimatedGB = sum(((Quantity * 8) / 1000.)) by Type 
| where EstimatedGB > 100
```

Als u een waarschuwing wilt instellen bij een ander gegevensvolume, wijzigt u 100 in de query's in het aantal GB waarbij u een waarschuwing wilt.

Gebruik de stappen in [Een nieuwe logboekwaarschuwing maken](alerts-metric.md) om een melding te krijgen wanneer de hoeveelheid verzamelde gegevens groter is dan verwacht.

Bij het instellen van de waarschuwing voor de eerste query - wanneer er meer dan 100 GB aan gegevens in 24 uur, stelt u het volgende in:  

- **Waarschuwingsvoorwaarde definiëren** - geef uw Log Analytics-werkruimte op als het resourcedoel.
- **Waarschuwingscriteria** - geef het volgende op:
   - **Signaalnaam** - selecteer **Aangepast zoeken in logboeken**
   - **Zoekquery** op `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1000.)) by Type | where DataGB > 100`
   - **De waarschuwings logica** is **gebaseerd op** het *aantal resultaten* en de **voor waarde** is *hoger dan* de **drempel waarde** *0*
   - **Tijdsperiode** van *1440* minuten en **Waarschuwingsfrequentie** van elke *60* minuten, omdat de gebruiksgegevens maar één keer per uur worden bijgewerkt.
- **Waarschuwingsdetails definiëren** - geef het volgende op:
   - **Naam** op *Gegevensvolume groter dan 100 GB in 24 uur*
   - **Ernst** op *Waarschuwing*

Maak een nieuwe [Actiegroep](action-groups.md) of geef een bestaande op, zodat u een melding ontvangt wanneer aan de criteria voor een logboekwaarschuwing wordt voldaan.

Bij het maken van de waarschuwing voor de tweede query - wanneer wordt voorspeld dat er meer dan 100 GB aan gegevens in 24 uur zal zijn, stelt u het volgende in:

- **Waarschuwingsvoorwaarde definiëren** - geef uw Log Analytics-werkruimte op als het resourcedoel.
- **Waarschuwingscriteria** - geef het volgende op:
   - **Signaalnaam** - selecteer **Aangepast zoeken in logboeken**
   - **Zoekquery** op `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1000.)) by Type | where EstimatedGB > 100`
   - **De waarschuwings logica** is **gebaseerd op** het *aantal resultaten* en de **voor waarde** is *hoger dan* de **drempel waarde** *0*
   - **Tijdsperiode** van *180* minuten en **Waarschuwingsfrequentie** van elke *60* minuten, omdat de gebruiksgegevens maar één keer per uur worden bijgewerkt.
- **Waarschuwingsdetails definiëren** - geef het volgende op:
   - **Naam** op *Gegevensvolume naar verwachting groter dan 100 GB in 24 uur*
   - **Ernst** op *Waarschuwing*

Maak een nieuwe [Actiegroep](action-groups.md) of geef een bestaande op, zodat u een melding ontvangt wanneer aan de criteria voor een logboekwaarschuwing wordt voldaan.

Wanneer u een waarschuwing ontvangt, gebruikt u de stappen in de volgende sectie om te bepalen waarom het-gebruik is hoger dan verwacht.

## <a name="data-transfer-charges-using-log-analytics"></a>Kosten voor gegevens overdracht met behulp van Log Analytics

Bij het verzenden van gegevens naar Log Analytics kunnen kosten voor de gegevens bandbreedte worden berekend. Zoals beschreven op de [pagina met prijzen voor Azure-band breedte](https://azure.microsoft.com/pricing/details/bandwidth/), wordt gegevens overdracht tussen Azure-Services in twee regio's in rekening gebracht als uitgaande gegevens overdracht tegen het normale tarief. Inkomende gegevens overdracht is gratis. Deze kosten zijn echter zeer klein (aantal%) vergeleken met de kosten voor Log Analytics gegevens opname. Als gevolg van het beheer van de kosten voor Log Analytics moet u zich richten op uw opgenomen gegevens volume, en wij hebben richt lijnen om die [hier](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume)te helpen begrijpen.   


## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>Problemen oplossen waarom Log Analytics geen gegevens meer verzamelt

Als u zich in de prijs categorie verouderde gratis bevindt en meer dan 500 MB aan gegevens op een dag hebt verzonden, stopt het verzamelen van gegevens voor de rest van de dag. De dagelijkse limiet wordt bereikt, is een veelvoorkomende reden die Log Analytics stopt het verzamelen van gegevens of gegevens lijkt te ontbreken.  Log Analytics maakt een gebeurtenis van het type bewerking wanneer het verzamelen van gegevens wordt gestart en gestopt. Voer de volgende query uit in de zoek opdracht om te controleren of u de dagelijkse limiet bereikt en ontbrekende gegevens: 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

Wanneer het verzamelen van gegevens stopt, wordt de OperationStatus **waarschuwing**. Wanneer het verzamelen van gegevens wordt gestart, is de OperationStatus **voltooid**. De volgende tabel beschrijft de redenen die het verzamelen van gegevens gestopt en een voorgestelde actie voor het verzamelen van gegevens hervatten:  

|Reden verzameling stopt| Oplossing| 
|-----------------------|---------|
|De dagelijkse limiet van de verouderde gratis prijs categorie is bereikt |Wachten tot de volgende dag voor de verzameling automatisch opnieuw opstarten of wijzigen in een betaalde prijscategorie.|
|Het dagelijkse kapje van uw werk ruimte is bereikt|Wacht tot de verzameling automatisch opnieuw wordt opgestart of verhoog de dagelijkse gegevens volume limiet die wordt beschreven in het maximale dagelijkse gegevens volume beheren. De tijd voor het opnieuw instellen van de dagelijkse limiet wordt weer gegeven op de pagina **gegevens volume beheer** . |
|Azure-abonnement is de status onderbroken vanwege:<br> Gratis proefversie is beëindigd<br> Azure geslaagd is verlopen<br> Maandelijkse bestedingslimiet bereikt (bijvoorbeeld op een MSDN of Visual Studio-abonnement)|Converteren naar een betaald abonnement<br> Limiet verwijderen of wacht u totdat de limiet wordt opnieuw ingesteld|

Als u een melding wilt ontvangen wanneer het verzamelen van gegevens wordt gestopt, gebruikt u de stappen in de waarschuwing *dagelijkse gegevens Cap maken* om een melding te ontvangen wanneer het verzamelen van gegevens wordt gestopt. Gebruik de stappen die worden beschreven in [een actie groep maken](action-groups.md) om een e-mail, webhook of runbook-actie voor de waarschuwings regel te configureren. 

## <a name="limits-summary"></a>Limieten overzicht

Er zijn enkele aanvullende Log Analytics limieten, waarvan sommige afhankelijk zijn van de Log Analytics prijs categorie. Deze worden [hier](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#log-analytics-workspaces)beschreven.


## <a name="next-steps"></a>Volgende stappen

- Zie [Zoek opdrachten in Logboeken in azure monitor logboeken](../log-query/log-query-overview.md) voor meer informatie over het gebruik van de Zoek taal. U kunt zoekquery’s gebruiken om aanvullende analyses uit te voeren op de gebruiksgegevens.
- Gebruik de stappen in [Een nieuwe logboekwaarschuwing maken](alerts-metric.md) om een melding te krijgen wanneer aan een zoekcriterium wordt voldaan.
- Gebruik [oplossingstargeting](../insights/solution-targeting.md) om gegevens te verzamelen van alleen de vereiste groepen computers.
- Als u een effectief gebeurtenis verzamelings beleid wilt configureren, raadpleegt u [Azure Security Center filter beleid](../../security-center/security-center-enable-data-collection.md).
- Wijzig de [prestatiemeteritemconfiguratie](data-sources-performance-counters.md).
- Bekijk de [configuratie van gebeurtenislogboek](data-sources-windows-events.md) om de instellingen voor het verzamelen van gebeurtenissen te wijzigen.
- Bekijk [syslog-configuratie](data-sources-syslog.md) om de instellingen voor syslog-verzamelingen te wijzigen.