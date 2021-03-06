---
title: Telemetrie-kanalen in Azure-toepassing Insights | Microsoft Docs
description: Telemetrie-kanalen aanpassen in Azure-toepassing Insights-Sdk's voor .NET en .NET core.
ms.topic: conceptual
ms.date: 05/14/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 9c292246f947e4d3a364f79b31fe7a1deebd33d9
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79275696"
---
# <a name="telemetry-channels-in-application-insights"></a>Telemetrie-kanalen in Application Insights

Telemetrie-kanalen vormen een integraal onderdeel van de [Azure-toepassing Insights-sdk's](../../azure-monitor/app/app-insights-overview.md). Ze beheren het bufferen en verzenden van telemetrie naar de Application Insights-service. De .NET-en .NET Core-versies van de Sdk's hebben twee ingebouwde telemetrie-kanalen: `InMemoryChannel` en `ServerTelemetryChannel`. In dit artikel wordt elk kanaal in detail beschreven, met inbegrip van het gedrag van het kanaal aan te passen.

## <a name="what-are-telemetry-channels"></a>Wat zijn telemetrie-kanalen?

Telemetrie-kanalen zijn verantwoordelijk voor het bufferen van telemetriegegevens en het verzenden ervan naar de Application Insights-service, waar ze worden opgeslagen voor query's en analyses. Een telemetrie-kanaal is een klasse die de [`Microsoft.ApplicationInsights.ITelemetryChannel`](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel?view=azure-dotnet) -interface implementeert.

De `Send(ITelemetry item)` methode van een telemetrie-kanaal wordt aangeroepen nadat alle initialisatie functies en telemetrie van de telemetrie zijn aangeroepen. Daarom bereiken items die worden verwijderd door een telemetrie-processor het kanaal niet. `Send()` stuurt de items doorgaans niet direct naar de back-end. Normaal gesp roken worden ze in het geheugen gebufferd en worden ze in batches verzonden voor efficiënte verzen ding.

[Live Metrics stream](live-stream.md) heeft ook een aangepast kanaal dat de live streamen van telemetrie aanstuurt. Dit kanaal is onafhankelijk van het normale telemetrie-kanaal, en dit document is hier niet van toepassing.

## <a name="built-in-telemetry-channels"></a>Ingebouwde telemetrie-kanalen

De Application Insights .NET en .NET core Sdk's worden geleverd met twee ingebouwde kanalen:

* `InMemoryChannel`: een Lightweight Channel waarmee items in het geheugen worden gebufferd tot ze worden verzonden. Items worden gebufferd in het geheugen en eenmaal per 30 seconden leeg gemaakt, of wanneer 500 items worden gebufferd. Dit kanaal biedt minimale betrouwbaarheids garanties omdat er niet opnieuw een poging wordt gedaan om een telemetrie te verzenden na een storing. Dit kanaal houdt ook geen items op schijf bij, zodat niet-verzonden items permanent verloren gaan bij het afsluiten van de toepassing (zonder problemen). Dit kanaal implementeert een `Flush()` methode die kan worden gebruikt om in-Memory-telemetriegegevens synchroon af te dwingen. Dit kanaal is goed geschikt voor toepassingen met korte uitvoering, waarbij synchroon leegmaken ideaal is.

    Dit kanaal maakt deel uit van het grotere micro soft. ApplicationInsights NuGet-pakket en is het standaard kanaal dat door de SDK wordt gebruikt wanneer niets anders is geconfigureerd.

* `ServerTelemetryChannel`: een geavanceerdere kanaal met beleid voor opnieuw proberen en de mogelijkheid om gegevens op te slaan op een lokale schijf. Dit kanaal probeert nieuwe telemetrie te verzenden als er tijdelijke fouten optreden. Dit kanaal gebruikt ook lokale schijf opslag om items op schijf te bewaren tijdens netwerk storingen of hoge telemetriegegevens. Als gevolg van deze methoden voor opnieuw proberen en lokale schijf opslag wordt dit kanaal als betrouwbaarder beschouwd en wordt het aanbevolen voor alle productie scenario's. Dit kanaal is de standaard waarde voor [ASP.net](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) -en [ASP.net core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) -toepassingen die zijn geconfigureerd volgens de officiële documentatie. Dit kanaal is geoptimaliseerd voor Server scenario's met langlopende processen. De [`Flush()`](#which-channel-should-i-use) methode die door dit kanaal wordt geïmplementeerd, is niet synchroon.

    Dit kanaal wordt verzonden als het pakket micro soft. ApplicationInsights. WindowsServer. TelemetryChannel NuGet en wordt automatisch aangeschaft wanneer u het micro soft. ApplicationInsights. Web of micro soft. ApplicationInsights. AspNetCore NuGet gebruikt pakket.

## <a name="configure-a-telemetry-channel"></a>Een telemetrie-kanaal configureren

U configureert een telemetrie-kanaal door het in te stellen op de actieve telemetrie-configuratie. Voor ASP.NET-toepassingen omvat het configureren van het telemetrie-kanaal exemplaar in `TelemetryConfiguration.Active`of door het wijzigen van `ApplicationInsights.config`. Voor ASP.NET Core toepassingen moet de configuratie het kanaal toevoegen aan de container voor injectie van afhankelijkheid.

In de volgende secties ziet u voor beelden van het configureren van de `StorageFolder` instelling voor het kanaal in verschillende toepassings typen. `StorageFolder` is slechts een van de Configureer bare instellingen. Zie [de sectie instellingen](telemetry-channels.md#configurable-settings-in-channels) verderop in dit artikel voor een volledige lijst met configuratie-instellingen.

### <a name="configuration-by-using-applicationinsightsconfig-for-aspnet-applications"></a>Configuratie met behulp van ApplicationInsights. config voor ASP.NET-toepassingen

In de volgende sectie van [ApplicationInsights. config](configuration-with-applicationinsights-config.md) ziet u het `ServerTelemetryChannel` kanaal dat is geconfigureerd met `StorageFolder` ingesteld op een aangepaste locatie:

```xml
    <TelemetrySinks>
        <Add Name="default">
            <TelemetryProcessors>
                <!-- Telemetry processors omitted for brevity  -->
            </TelemetryProcessors>
            <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel">
                <StorageFolder>d:\temp\applicationinsights</StorageFolder>
            </TelemetryChannel>
        </Add>
    </TelemetrySinks>
```

### <a name="configuration-in-code-for-aspnet-applications"></a>Configuratie in code voor ASP.NET-toepassingen

Met de volgende code wordt een ServerTelemetryChannel-exemplaar ingesteld met `StorageFolder` ingesteld op een aangepaste locatie. Voeg deze code toe aan het begin van de toepassing, meestal in de `Application_Start()` methode in Global.aspx.cs.

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
protected void Application_Start()
{
    var serverTelemetryChannel = new ServerTelemetryChannel();
    serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
    serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
    TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
}
```

### <a name="configuration-in-code-for-aspnet-core-applications"></a>Configuratie in code voor ASP.NET Core-toepassingen

Wijzig de `ConfigureServices` methode van de klasse `Startup.cs` zoals hier wordt weer gegeven:

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

public void ConfigureServices(IServiceCollection services)
{
    // This sets up ServerTelemetryChannel with StorageFolder set to a custom location.
    services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel() {StorageFolder = @"d:\temp\applicationinsights" });

    services.AddApplicationInsightsTelemetry();
}

```

> [!IMPORTANT]
> Het configureren van het kanaal met behulp van `TelemetryConfiguration.Active` wordt niet aanbevolen voor ASP.NET Core toepassingen.

### <a name="configuration-in-code-for-netnet-core-console-applications"></a>Configuratie in code voor .NET/.NET Core-Console toepassingen

Voor console-apps is de code hetzelfde voor .NET en .NET core:

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>Operationele details van ServerTelemetryChannel

`ServerTelemetryChannel` binnenkomende items in een in-Memory buffer opslaat. De items worden eenmaal per 30 seconden geserialiseerd, gecomprimeerd en opgeslagen in een `Transmission` exemplaar, of als 500 items zijn gebufferd. Een enkele `Transmission`-instantie bevat Maxi maal 500 items en vertegenwoordigt een batch met telemetrie die wordt verzonden via één HTTPS-aanroep naar de Application Insights-service.

Standaard kunnen Maxi maal tien `Transmission` exemplaren parallel worden verzonden. Als telemetrie op snellere tarieven aankomt, of als het netwerk of de back-end van de Application Insights langzaam is, worden `Transmission` exemplaren in het geheugen opgeslagen. De standaard capaciteit van deze in-Memory `Transmission` buffer is 5 MB. Wanneer de capaciteit in het geheugen is overschreden, worden `Transmission`-exemplaren op de lokale schijf opgeslagen tot een limiet van 50 MB. `Transmission` exemplaren worden opgeslagen op de lokale schijf, ook wanneer er netwerk problemen zijn. Alleen items die zijn opgeslagen op een lokale schijf, kunnen de toepassing vastlopen. Ze worden verzonden wanneer de toepassing opnieuw wordt gestart.

## <a name="configurable-settings-in-channels"></a>Configureer bare instellingen in kanalen

Zie voor een volledige lijst met Configureer bare instellingen voor elk kanaal:

* [InMemoryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

* [ServerTelemetryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

Hier volgen de meestgebruikte instellingen voor `ServerTelemetryChannel`:

1. `MaxTransmissionBufferCapacity`: de maximale hoeveelheid geheugen (in bytes) die door het kanaal wordt gebruikt voor het bufferen van de verzen ding in het geheugen. Wanneer deze capaciteit is bereikt, worden nieuwe items rechtstreeks op de lokale schijf opgeslagen. De standaard waarde is 5 MB. Het instellen van een hogere waarde leidt tot minder schijf gebruik, maar houd er rekening mee dat items in het geheugen verloren gaan als de toepassing vastloopt.

1. `MaxTransmissionSenderCapacity`: het maximum aantal `Transmission` exemplaren dat tegelijkertijd naar Application Insights wordt verzonden. De standaard waarde is 10. Deze instelling kan worden geconfigureerd voor een hoger nummer, dat wordt aanbevolen wanneer een enorme hoeveelheid telemetrie wordt gegenereerd. Hoog volume gebeurt meestal tijdens het testen van de belasting of wanneer steek proeven zijn uitgeschakeld.

1. `StorageFolder`: de map die wordt gebruikt door het kanaal om items naar behoefte op schijf op te slaan. In Windows wordt% LOCALAPPDATA% of% TEMP% gebruikt als er geen ander pad expliciet is opgegeven. In andere omgevingen dan Windows, moet u een geldige locatie of telemetrie opgeven die niet op de lokale schijf wordt opgeslagen.

## <a name="which-channel-should-i-use"></a>Welk kanaal moet ik gebruiken?

`ServerTelemetryChannel` wordt aanbevolen voor de meeste productie scenario's waarbij langlopende toepassingen worden gebruikt. De `Flush()` methode die door `ServerTelemetryChannel` is geïmplementeerd, is niet synchroon en garandeert ook niet dat alle items in behandeling worden verzonden vanuit het geheugen of de schijf. Als u dit kanaal gebruikt in scenario's waarin de toepassing wordt afgesloten, wordt u aangeraden enige vertraging na het aanroepen van `Flush()`in te voeren. De exacte hoeveelheid vertraging die u mogelijk nodig hebt, kan niet voorspelbaar zijn. Dit is afhankelijk van factoren zoals hoeveel items of `Transmission` instanties in het geheugen zijn, hoeveel schijven er op schijf zijn, hoeveel er worden verzonden naar de back-end en of het kanaal zich in het midden van de exponentiële back-ups bevindt.

Als u een synchrone flush moet uitvoeren, wordt u aangeraden `InMemoryChannel`te gebruiken.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost"></a>Garandeert het Application Insights kanaal dat telemetrie wordt geleverd? Als dat niet het geval is, wat zijn dan de scenario's waarin telemetrie kan worden verwijderd?

Het korte antwoord is dat geen van de ingebouwde kanalen een transactie type garantie bieden voor telemetrie-levering aan de back-end. `ServerTelemetryChannel` is geavanceerdere, vergeleken met `InMemoryChannel` voor betrouw bare levering, maar het is ook een poging om telemetrie te verzenden. Telemetrie kan nog steeds in verschillende situaties verloren gaan, met inbegrip van deze algemene scenario's:

1. Items in het geheugen gaan verloren wanneer de toepassing vastloopt.

1. Telemetrie gaat verloren tijdens een lange periode van netwerk problemen. Telemetrie wordt op de lokale schijf opgeslagen tijdens netwerk storingen of wanneer er problemen optreden met de Application Insights back-end. Items die ouder zijn dan 48 uur, worden echter verwijderd.

1. De standaard schijf locaties voor het opslaan van telemetrie in Windows zijn% LOCALAPPDATA% of% TEMP%. Deze locaties zijn doorgaans lokaal voor de computer. Als de toepassing fysiek van de ene locatie naar de andere migreert, gaan alle telemetrie die op de oorspronkelijke locatie zijn opgeslagen verloren.

1. In Web Apps in Windows is de standaard schijf-opslag locatie D:\local\LocalAppData. Deze locatie is niet persistent. Het wordt gewist uit het opnieuw opstarten van de app, schalen en andere dergelijke bewerkingen, waardoor er telemetrie die daar is opgeslagen, verloren gaat. U kunt de standaard waarde onderdrukken en opslag opgeven op een blijvende locatie, zoals D:\home. Dergelijke persistente locaties worden echter geleverd door externe opslag en kunnen daarom traag zijn.

### <a name="does-servertelemetrychannel-work-on-systems-other-than-windows"></a>Werkt ServerTelemetryChannel op andere systemen dan Windows?

Hoewel de naam van het pakket en de naam ruimte ' WindowsServer ' bevat, wordt dit kanaal ondersteund op andere systemen dan Windows, met de volgende uitzonde ring. Op systemen met uitzonde ring van Windows maakt het kanaal standaard geen lokale opslagmap. U moet een lokale opslagmap maken en het kanaal configureren om het te gebruiken. Nadat de lokale opslag is geconfigureerd, werkt het kanaal op dezelfde manier op alle systemen.

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>Maakt de SDK tijdelijke lokale opslag? Worden de gegevens versleuteld op opslag?

De SDK slaat telemetrie-items in lokale opslag op tijdens netwerk problemen of tijdens het beperken. Deze gegevens zijn niet lokaal versleuteld.

Voor Windows-systemen maakt de SDK automatisch een tijdelijke lokale map in de map% TEMP% of% LOCALAPPDATA% en beperkt de toegang tot beheerders en alleen de huidige gebruiker.

Voor systemen met uitzonde ring van Windows wordt er geen lokale opslag automatisch gemaakt door de SDK, waardoor er standaard geen gegevens lokaal worden opgeslagen. U kunt zelf een opslag directory maken en het kanaal configureren om het te gebruiken. In dit geval bent u verantwoordelijk voor het beveiligen van de map.
Lees meer over [gegevens bescherming en privacy](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage).

## <a name="open-source-sdk"></a>Open-Source-SDK
Net als elke SDK voor Application Insights zijn kanalen open source. Lees en bijdragen aan de code of rapport problemen op [het officiële github opslag plaats](https://github.com/Microsoft/ApplicationInsights-dotnet).

## <a name="next-steps"></a>Volgende stappen

* [Steekproeven](../../azure-monitor/app/sampling.md)
* [SDK-probleem oplossing](../../azure-monitor/app/asp-net-troubleshoot-no-data.md)
