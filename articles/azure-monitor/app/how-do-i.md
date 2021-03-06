---
title: Hoe kan ik... in Azure-toepassing inzichten | Microsoft Docs
description: Veelgestelde vragen in Application Insights.
ms.topic: conceptual
ms.date: 04/04/2017
ms.openlocfilehash: 5b65087c361911f0714723c315e0b7f7e9bb74e6
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77663854"
---
# <a name="how-do-i--in-application-insights"></a>Hoe kan ik ... in Application Insights?
## <a name="get-an-email-when-"></a>Een e-mail ontvangen wanneer...
### <a name="email-if-my-site-goes-down"></a>E-mail als mijn site uitvalt
Stel een [Beschik baarheid-webtest](../../azure-monitor/app/monitor-web-app-availability.md)in.

### <a name="email-if-my-site-is-overloaded"></a>E-mail als mijn site overbelast is
Stel een [waarschuwing](../../azure-monitor/app/alerts.md) in voor de **reactie tijd**van de server. Een drempel waarde tussen 1 en 2 seconden zou moeten werken.

![](./media/how-do-i/030-server.png)

Uw app kan ook tekenen van een stam weer geven door fout codes te retour neren. Stel een waarschuwing in voor **mislukte aanvragen**.

Als u een waarschuwing voor **Server uitzonderingen**wilt instellen, moet u mogelijk [Extra Setup](../../azure-monitor/app/asp-net-exceptions.md) uitvoeren om gegevens te bekijken.

### <a name="email-on-exceptions"></a>E-mail op uitzonde ringen
1. [Uitzonderings bewaking instellen](../../azure-monitor/app/asp-net-exceptions.md)
2. [Een waarschuwing instellen](../../azure-monitor/app/alerts.md) voor de metriek van het aantal uitzonde ringen

### <a name="email-on-an-event-in-my-app"></a>E-mail voor een gebeurtenis in mijn app
Stel dat u een e-mail bericht wilt ontvangen wanneer er een specifieke gebeurtenis plaatsvindt. Application Insights biedt deze functie niet rechtstreeks, maar kan wel [een waarschuwing verzenden wanneer een metriek een drempel waarde overschrijdt](../../azure-monitor/app/alerts.md).

Waarschuwingen kunnen worden ingesteld op [aangepaste metrische gegevens](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric), maar geen aangepaste gebeurtenissen. Schrijf wat code om een metrische waarde te verg Roten wanneer de gebeurtenis zich voordoet:

    telemetry.TrackMetric("Alarm", 10);

of:

    var measurements = new Dictionary<string,double>();
    measurements ["Alarm"] = 10;
    telemetry.TrackEvent("status", null, measurements);

Omdat waarschuwingen twee statussen hebben, moet u een lage waarde verzenden wanneer u de waarschuwing moet hebben beëindigd:

    telemetry.TrackMetric("Alarm", 0.5);

Maak een grafiek in [metrische Explorer](../../azure-monitor/app/metrics-explorer.md) om uw wekker te bekijken:

![](./media/how-do-i/010-alarm.png)

Stel nu een waarschuwing in die wordt geactiveerd wanneer de metriek boven een mid-waarde voor een korte periode komt:

![](./media/how-do-i/020-threshold.png)

Stel de gemiddelde periode in op het minimum.

U ontvangt e-mail berichten wanneer de metriek boven en onder de drempel waarde komt.

Enkele punten om in overweging te nemen:

* Een waarschuwing heeft twee statussen ("waarschuwing" en "in orde"). De status wordt alleen geëvalueerd wanneer een metriek wordt ontvangen.
* Er wordt alleen een e-mail bericht verzonden wanneer de status wordt gewijzigd. Daarom moet u zowel hoge als lage waarde-metrische gegevens verzenden.
* Als u de waarschuwing wilt evalueren, wordt het gemiddelde van de ontvangen waarden in de voor gaande periode genomen. Dit gebeurt elke keer dat er een metriek wordt ontvangen, zodat e-mail berichten vaker kunnen worden verzonden dan de periode die u hebt ingesteld.
* Omdat e-mail berichten beide op ' alert ' en ' in orde ' worden verzonden, kunt u de eenmalige gebeurtenis beschouwen als een voor waarde met twee status waarden. Een voor beeld: in plaats van de gebeurtenis ' taak voltooid ', een ' taak wordt uitgevoerd ', waarbij u e-mail berichten ontvangt aan het begin en einde van een taak.

### <a name="set-up-alerts-automatically"></a>Waarschuwingen automatisch instellen
[Power shell gebruiken voor het maken van nieuwe waarschuwingen](../../azure-monitor/app/alerts.md#automation)

## <a name="use-powershell-to-manage-application-insights"></a>Power shell gebruiken voor het beheren van Application Insights
* [Nieuwe resources maken](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#creating-a-resource-automatically)
* [Nieuwe waarschuwingen maken](../../azure-monitor/app/alerts.md#automation)

## <a name="separate-telemetry-from-different-versions"></a>Telemetrie scheiden van verschillende versies

* Meerdere rollen in een app: gebruik één Application Insights resource en filter op [cloud_Rolename](../../azure-monitor/app/app-map.md).
* Ontwikkelings-, test-en release versies scheiden: verschillende Application Insights bronnen gebruiken. Haal de instrumentatie sleutels op uit web. config. [Meer informatie](../../azure-monitor/app/separate-resources.md)
* Versie rapportage-builds: Voeg een eigenschap toe met behulp van een telemetrie-initialisatie functie. [Meer informatie](../../azure-monitor/app/separate-resources.md)

## <a name="monitor-backend-servers-and-desktop-apps"></a>Back-endservers en desktop-apps bewaken
[Gebruik de Windows Server SDK-module](../../azure-monitor/app/windows-desktop.md).

## <a name="visualize-data"></a>Gegevens visualiseren
#### <a name="dashboard-with-metrics-from-multiple-apps"></a>Dash board met metrische gegevens van meerdere apps
* Pas in de [metrische Explorer](../../azure-monitor/app/metrics-explorer.md)de grafiek aan en sla deze op als favoriet. Vastmaken aan het Azure-dash board.

#### <a name="dashboard-with-data-from-other-sources-and-application-insights"></a>Dash board met gegevens uit andere bronnen en Application Insights
* [Telemetrie exporteren naar Power bi](../../azure-monitor/app/export-power-bi.md ).

of

* Gebruik share point als uw dash board, waarmee gegevens worden weer gegeven in share point-webonderdelen. [Continue export en stream Analytics gebruiken om naar SQL te exporteren](../../azure-monitor/app/code-sample-export-sql-stream-analytics.md).  Gebruik Power View om de data base te onderzoeken en een share point-webonderdeel te maken voor Power View.

<a name="search-specific-users"></a>

### <a name="filter-out-anonymous-or-authenticated-users"></a>Anonieme of geverifieerde gebruikers filteren
Als uw gebruikers zich aanmelden, kunt u de [geverifieerde gebruikers-id](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users)instellen. (Dit gebeurt niet automatisch.)

U kunt vervolgens het volgende doen:

* Zoeken naar specifieke gebruikers-Id's

![](./media/how-do-i/110-search.png)

* De metrische gegevens filteren op anonieme of geverifieerde gebruikers

![](./media/how-do-i/115-metrics.png)

## <a name="modify-property-names-or-values"></a>Eigenschaps namen of-waarden wijzigen
Een [filter](../../azure-monitor/app/api-filtering-sampling.md#filtering)maken. Hiermee kunt u de telemetrie wijzigen of filteren voordat deze vanuit uw app naar Application Insights wordt verzonden.

## <a name="list-specific-users-and-their-usage"></a>Specifieke gebruikers en hun gebruik weer geven
Als u alleen [specifieke gebruikers wilt zoeken](#search-specific-users), kunt u de [GEVERIFIEERDE gebruikers-id](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users)instellen.

Als u een lijst met gebruikers wilt met gegevens, zoals op welke pagina's ze worden weer gegeven of hoe vaak ze zich aanmelden, hebt u twee opties:

* [Stel een geverifieerde gebruikers-id](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users) [in, exporteer naar een Data Base](../../azure-monitor/app/code-sample-export-sql-stream-analytics.md) en gebruik geschikte hulpprogram ma's om uw gebruikers gegevens daar te analyseren.
* Als u slechts een klein aantal gebruikers hebt, kunt u aangepaste gebeurtenissen of metrische gegevens verzenden met behulp van de informatie die van belang is als metrische waarde of gebeurtenis naam en de gebruikers-ID instellen als een eigenschap. Als u pagina weergaven wilt analyseren, vervangt u de standaard Java script trackPageView-aanroep. Als u telemetrie aan de server zijde wilt analyseren, gebruikt u de initialisatie functie voor telemetrie om de gebruikers-ID toe te voegen aan alle server-telemetrie. Vervolgens kunt u metrische gegevens filteren en segmenteren en zoeken op de gebruikers-ID.

## <a name="reduce-traffic-from-my-app-to-application-insights"></a>Het verkeer van mijn app naar Application Insights verminderen
* Schakel in [ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)alle modules uit die u niet nodig hebt, zoals de prestatie meter item verzamelaar.
* Gebruik [steek proeven en filters](../../azure-monitor/app/api-filtering-sampling.md) bij de SDK.
* In uw webpagina's kunt u het aantal Ajax-aanroepen dat voor elke pagina weergave wordt gerapporteerd, beperken. In het script fragment na `instrumentationKey:...`, insert: `,maxAjaxCallsPerView:3` (of een geschikt getal).
* Als u [TrackMetric](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric)gebruikt, moet u de aggregatie van batches met metrische waarden berekenen voordat u het resultaat verzendt. Er is een overbelasting van TrackMetric () waarmee dit wordt geboden.

Meer informatie over [prijzen en quota's](../../azure-monitor/app/pricing.md).

## <a name="disable-telemetry"></a>Telemetrie uitschakelen
Om het verzamelen en verzenden van telemetrie van de server **dynamisch te stoppen en te starten** :

### <a name="aspnet-classic-applications"></a>Klassieke ASP.NET-toepassingen

```csharp
    using  Microsoft.ApplicationInsights.Extensibility;

    TelemetryConfiguration.Active.DisableTelemetry = true;
```

### <a name="other-applications"></a>Andere toepassingen
Het is niet raadzaam om `TelemetryConfiguration.Active` Singleton te gebruiken op console-of ASP.NET Core-toepassingen.
Als u zelf `TelemetryConfiguration` exemplaar hebt gemaakt, stelt u `DisableTelemetry` in op `true`.

Voor ASP.NET Core-toepassingen kunt u `TelemetryConfiguration` exemplaar openen met behulp van [ASP.net core afhankelijkheids injectie](/aspnet/core/fundamentals/dependency-injection/). Meer informatie vindt u in [ApplicationInsights for ASP.net core applications](../../azure-monitor/app/asp-net-core.md) -artikel.

## <a name="disable-selected-standard-collectors"></a>Geselecteerde standaard verzamelaars uitschakelen
U kunt standaard verzamelaars uitschakelen (bijvoorbeeld prestatie meter items, HTTP-aanvragen of afhankelijkheden)

* **ASP.NET-toepassingen** : de relevante regels in [ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) verwijderen of er opmerkingen van maken
* **ASP.net core toepassingen** : configuratie opties voor telemetrie-modules volgen in [ApplicationInsights ASP.net core](../../azure-monitor/app/asp-net-core.md#configuring-or-removing-default-telemetrymodules)

## <a name="view-system-performance-counters"></a>Systeem prestatie meter items weer geven
De metrische gegevens die u kunt weer geven in Metrics Explorer zijn een set systeem prestatie meter items. Er is een vooraf gedefinieerde Blade met de titel **servers** die verschillende hiervan worden weer gegeven.

![Open uw Application Insights-resource en klik op servers](./media/how-do-i/121-servers.png)

### <a name="if-you-see-no-performance-counter-data"></a>Als er geen prestatie meter gegevens worden weer gegeven
* **IIS-server** op uw eigen computer of op een virtuele machine. [Installeer status monitor](../../azure-monitor/app/monitor-performance-live-website-now.md).
* **Azure-website** : er worden nog geen prestatie meters ondersteund. Er zijn diverse metrische gegevens die u kunt ophalen als standaard onderdeel van het configuratie scherm van Azure web site.
* **UNIX-server** - [verzamelde installeren](../../azure-monitor/app/java-collectd.md)

### <a name="to-display-more-performance-counters"></a>Meer prestatie meter items weer geven
* Voeg eerst [een nieuwe grafiek toe](../../azure-monitor/app/metrics-explorer.md) en kijk of de teller zich in de Basic-Set bevindt die wij bieden.
* Als dat niet het geval is, [voegt u de teller toe aan de set die wordt verzameld door de module prestatie meter items](../../azure-monitor/app/performance-counters.md).
