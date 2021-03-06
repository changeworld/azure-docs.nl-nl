---
title: Diagnostische logboekregistratie inschakelen
description: Lees hoe u diagnostische logboek registratie kunt inschakelen en instrumentatie kunt toevoegen aan uw toepassing, en hoe u toegang hebt tot de gegevens die zijn geregistreerd door Azure.
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.topic: article
ms.date: 09/17/2019
ms.custom: seodec18
ms.openlocfilehash: 433f8fa36f17f7cb145261273586a684658acda5
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79280467"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>Diagnostische logboek registratie inschakelen voor apps in Azure App Service
## <a name="overview"></a>Overzicht
Azure biedt ingebouwde diagnostische gegevens om te helpen bij het opsporen van fouten in een [app service-app](overview.md). In dit artikel leert u hoe u diagnostische logboek registratie kunt inschakelen en instrumentatie kunt toevoegen aan uw toepassing, en hoe u toegang hebt tot de gegevens die zijn geregistreerd door Azure.

In dit artikel wordt gebruikgemaakt van de [Azure Portal](https://portal.azure.com) en Azure CLI voor het werken met Diagnostische logboeken. Zie [problemen met Azure in Visual Studio oplossen](troubleshoot-dotnet-visual-studio.md)voor meer informatie over het werken met Diagnostische logboeken met Visual Studio.

> [!NOTE]
> Naast de instructies voor logboek registratie in dit artikel, is er een nieuwe, geïntegreerde logboek registratie functie met Azure-bewaking. Meer informatie over deze mogelijkheid vindt u in de sectie [Logboeken naar Azure monitor (preview) verzenden](#send-logs-to-azure-monitor-preview) . 
>
>

|Type|Platform|Locatie|Beschrijving|
|-|-|-|-|
| Toepassingslogboeken | Windows, Linux | App Service bestands systeem en/of Azure Storage-blobs | Registreert berichten die zijn gegenereerd door de toepassings code. De berichten kunnen worden gegenereerd door het webframework dat u kiest, of vanuit de code van uw toepassing rechtstreeks met het standaard logboek registratie patroon van uw taal. Aan elk bericht wordt een van de volgende categorieën toegewezen: **kritiek**, **fout**, **waarschuwing**, **info**, **fout opsporing**en **tracering**. U kunt selecteren hoe uitgebreid u de logboek registratie wilt maken door het Ernst niveau in te stellen wanneer u logboek registratie van toepassingen inschakelt.|
| Logboek registratie van webserver| Windows | App Service bestands systeem of Azure Storage-blobs| Onbewerkte HTTP-aanvraag gegevens in de [uitgebreide W3C-indeling van logboek bestand](/windows/desktop/Http/w3c-logging). Elk logboek bericht bevat gegevens zoals de HTTP-methode, bron-URI, client-IP, client poort, gebruikers agent, respons code, enzovoort. |
| Gedetailleerde fout berichten| Windows | App Service bestands systeem | Kopieën van de *htm* -fout pagina's die naar de client browser zouden zijn verzonden. Uit veiligheids overwegingen mogen gedetailleerde fout pagina's niet worden verzonden naar clients in de productie omgeving, maar App Service kunt de fout pagina opslaan telkens wanneer er een toepassings fout optreedt met HTTP-code 400 of hoger. De pagina bevat mogelijk informatie die u kan helpen bij het bepalen van de oorzaak van de fout code door de server. |
| Tracering van mislukte aanvragen | Windows | App Service bestands systeem | Gedetailleerde tracerings informatie over mislukte aanvragen, inclusief een tracering van de IIS-onderdelen die worden gebruikt om de aanvraag te verwerken en de tijd die in elk onderdeel is gemaakt. Het is handig als u de prestaties van de site wilt verbeteren of een specifieke HTTP-fout wilt isoleren. Er wordt één map gegenereerd voor elke mislukte aanvraag, die het XML-logboek bestand bevat en het XSL-opmaak model waarmee het logboek bestand wordt weer gegeven. |
| Implementatie logboek registratie | Windows, Linux | App Service bestands systeem | Logboeken voor wanneer u inhoud naar een app publiceert. De registratie van de implementatie wordt automatisch uitgevoerd en er zijn geen Configureer bare instellingen voor de registratie van de implementatie. Het helpt u om te bepalen waarom een implementatie is mislukt. Als u bijvoorbeeld een [aangepast implementatie script](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)gebruikt, kunt u de implementatie logboek registratie gebruiken om te bepalen waarom het script mislukt. |

> [!NOTE]
> App Service biedt een specifiek, interactief hulp programma voor diagnostische gegevens om u te helpen bij het oplossen van problemen met uw toepassing. Zie [Azure app service Diagnostics-overzicht](overview-diagnostics.md)voor meer informatie.
>
> Daarnaast kunt u andere Azure-Services gebruiken om de mogelijkheden voor logboek registratie en controle van uw app, zoals [Azure monitor](../azure-monitor/app/azure-web-apps.md), te verbeteren.
>

## <a name="enable-application-logging-windows"></a>Toepassings logboeken inschakelen (Windows)

Als u toepassings logboeken wilt inschakelen voor Windows-apps in de [Azure Portal](https://portal.azure.com), navigeert u naar uw app en selecteert u **app service logboeken**.

Selecteer **aan** voor **toepassings Logboeken (bestands systeem)** of **toepassings Logboeken (BLOB)** of beide. 

De optie **Bestands systeem** is bedoeld voor tijdelijke fout opsporing en schakelt zichzelf in 12 uur uit. De **BLOB** -optie is voor langdurige logboek registratie en er is een BLOB storage-container nodig om logboeken naar te schrijven.  De **BLOB** -optie bevat ook aanvullende informatie in de logboek berichten, zoals de id van de oorspronkelijke VM-instantie van het logboek bericht (`InstanceId`), thread-ID (`Tid`) en een gedetailleerdere tijds tempel ([`EventTickCount`](https://docs.microsoft.com/dotnet/api/system.datetime.ticks)).

> [!NOTE]
> Momenteel kunnen alleen .NET-toepassings logboeken worden geschreven naar de Blob-opslag. Java, PHP, node. js, python-toepassings logboeken kunnen alleen worden opgeslagen op het App Service-bestands systeem (zonder code wijzigingen voor het schrijven van logboeken naar externe opslag).
>
> Als u de [toegangs sleutels van uw opslag account opnieuw genereert](../storage/common/storage-create-storage-account.md), moet u ook de configuratie van de betreffende logboek registratie opnieuw instellen om de bijgewerkte toegangs sleutels te gebruiken. Om dit te doen:
>
> 1. Stel op het tabblad **configureren** de respectieve logboek registratie functie in op **uit**. Sla de instelling op.
> 2. Schakel logboek registratie voor de blob van het opslag account opnieuw in. Sla de instelling op.
>
>

Selecteer het **niveau**of het niveau van de details die moeten worden vastgelegd. De volgende tabel bevat de logboek categorieën die zijn opgenomen in elk niveau:

| Niveau | Opgenomen categorieën |
|-|-|
|**Uitgeschakeld** | None |
|**Optreedt** | Fout, kritiek |
|**Waarschuwing** | Waarschuwing, fout, kritiek|
|**Gegevens** | Info, waarschuwing, fout, kritiek|
|**Uitgebreide** | Traceren, fouten opsporen, info, waarschuwing, fout, kritiek (alle categorieën) |

Wanneer u klaar bent, selecteert u **Opslaan**.

## <a name="enable-application-logging-linuxcontainer"></a>Toepassings logboeken inschakelen (Linux/container)

Als u toepassings logboeken wilt inschakelen voor Linux-apps of aangepaste container-apps in de [Azure Portal](https://portal.azure.com), navigeert u naar uw app en selecteert u **app service logboeken**.

Selecteer in **toepassings logboeken**de optie **Bestands systeem**.

Geef in **quotum (MB)** het schijf quotum voor de toepassings logboeken op. Stel in de **Bewaar periode (dagen)** het aantal dagen in dat de logboeken moeten worden bewaard.

Wanneer u klaar bent, selecteert u **Opslaan**.

## <a name="enable-web-server-logging"></a>Logboek registratie van webserver inschakelen

Als u webserver logboek registratie wilt inschakelen voor Windows-apps in de [Azure Portal](https://portal.azure.com), navigeert u naar uw app en selecteert u **app service logboeken**.

Voor **logboek registratie van webserver**selecteert u **opslag** om logboeken op te slaan in Blob Storage of op het **Bestands systeem** om logboeken op te slaan op het app service-Bestands systeem. 

Stel in de **Bewaar periode (dagen)** het aantal dagen in dat de logboeken moeten worden bewaard.

> [!NOTE]
> Als u de [toegangs sleutels van uw opslag account opnieuw genereert](../storage/common/storage-create-storage-account.md), moet u de configuratie van de betreffende logboek registratie opnieuw instellen om de bijgewerkte sleutels te gebruiken. Om dit te doen:
>
> 1. Stel op het tabblad **configureren** de respectieve logboek registratie functie in op **uit**. Sla de instelling op.
> 2. Schakel logboek registratie voor de blob van het opslag account opnieuw in. Sla de instelling op.
>
>

Wanneer u klaar bent, selecteert u **Opslaan**.

## <a name="log-detailed-errors"></a>Gedetailleerde fouten in logboek vastleggen

Als u de fout pagina of tracering van mislukte aanvragen voor Windows-apps in de [Azure Portal](https://portal.azure.com)wilt opslaan, gaat u naar uw app en selecteert u **app service logboeken**.

Onder **gedetailleerde fout registratie** of **tracering van mislukte aanvragen**selecteert u **aan**en selecteert u vervolgens **Opslaan**.

Beide typen logboeken worden opgeslagen in het App Service-bestands systeem. Er worden Maxi maal 50 fouten (bestanden/mappen) bewaard. Wanneer het aantal HTML-bestanden groter is dan 50, worden de oudste 26 fouten automatisch verwijderd.

## <a name="add-log-messages-in-code"></a>Logboek berichten toevoegen in code

In de toepassings code gebruikt u de gebruikelijke logboek functies om logboek berichten te verzenden naar de toepassings Logboeken. Bijvoorbeeld:

- ASP.NET-toepassingen kunnen de klasse [System. Diagnostics. trace](/dotnet/api/system.diagnostics.trace) gebruiken om informatie te registreren in het logboek voor toepassings diagnose. Bijvoorbeeld:

    ```csharp
    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");
    ```

- ASP.NET Core maakt standaard gebruik van de logboek provider [micro soft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) . Zie [ASP.net core logboek registratie in azure](https://docs.microsoft.com/aspnet/core/fundamentals/logging/)voor meer informatie.

## <a name="stream-logs"></a>Logboeken streamen

Voordat u Logboeken in realtime streamen, schakelt u het gewenste logboek type in. Alle informatie die wordt geschreven naar bestanden die eindigen op. txt,. log of. htm, die zijn opgeslagen in de */logfiles* -map (d:/Home/logfiles), wordt gestreamd door app service.

> [!NOTE]
> Sommige typen logboek registratie buffer schrijven naar het logboek bestand, wat kan leiden tot onbestelbare gebeurtenissen in de stroom. Een toepassings logboek vermelding die zich voordoet wanneer een gebruiker een pagina bezoekt, kan bijvoorbeeld worden weer gegeven in de stroom vóór de bijbehorende HTTP-logboek vermelding voor de pagina-aanvraag.
>

### <a name="in-azure-portal"></a>In Azure Portal

Als u logboeken wilt streamen in de [Azure Portal](https://portal.azure.com), navigeert u naar uw app en selecteert u **log stream**. 

### <a name="in-cloud-shell"></a>In Cloud Shell

Als u Logboeken Live in [Cloud shell](../cloud-shell/overview.md)wilt streamen, gebruikt u de volgende opdracht:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup
```

Als u specifieke gebeurtenissen, zoals fouten, wilt filteren, gebruikt u de para meter **--filter** . Bijvoorbeeld:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --filter Error
```
Als u specifieke logboek typen, zoals HTTP, wilt filteren, gebruikt u de para meter **--Path** . Bijvoorbeeld:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --path http
```

### <a name="in-local-terminal"></a>In lokale terminal

Als u logboeken wilt streamen in de lokale console, [installeert u Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli) en [meldt u zich aan bij uw account](https://docs.microsoft.com/cli/azure/authenticate-azure-cli). Nadat u bent aangemeld, volgt u de [instructies voor Cloud shell](#in-cloud-shell)

## <a name="access-log-files"></a>Toegang tot logboek bestanden

Als u de Azure Storage blobs-optie voor een logboek type configureert, hebt u een client hulpprogramma nodig dat met Azure Storage werkt. Zie [Azure Storage-client hulpprogramma's](../storage/common/storage-explorers.md)voor meer informatie.

Voor logboeken die zijn opgeslagen in het bestands systeem van App Service is de eenvoudigste manier om het ZIP-bestand te downloaden in de browser:

- Linux/container-apps: `https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip`
- Windows-apps: `https://<app-name>.scm.azurewebsites.net/api/dump`

Voor Linux/container-apps bevat het ZIP-bestand console-uitvoer logboeken voor de docker-host en de docker-container. Voor een uitgeschaalde app bevat het ZIP-bestand één set logboeken voor elk exemplaar. In het App Service bestands systeem zijn deze logboek bestanden de inhoud van de map */Home/logfiles* .

Voor Windows-apps bevat het ZIP-bestand de inhoud van de *D:\Home\LogFiles* -map in het app service-Bestands systeem. Het heeft de volgende structuur:

| Logboek type | Directory | Beschrijving |
|-|-|-|
| **Toepassings logboeken** |*/LogFiles/Application/* | Bevat een of meer tekst bestanden. De indeling van de logboek berichten is afhankelijk van de logboek registratie provider die u gebruikt. |
| **Traceringen van mislukte aanvragen** | */LogFiles/W3SVC#########/* | Bevat XML-bestanden en een XSL-bestand. U kunt de opgemaakte XML-bestanden weer geven in de browser. |
| **Gedetailleerde fouten logboeken** | */LogFiles/DetailedErrors/* | Bevat HTM-fout bestanden. U kunt de HTM-bestanden weer geven in de browser.<br/>Een andere manier om de traceringen van mislukte aanvragen weer te geven, is door naar de app-pagina in de portal te navigeren. Selecteer in het linkermenu **problemen vaststellen en oplossen**, zoek naar **Logboeken voor tracering van mislukte aanvragen**en klik vervolgens op het pictogram om de gewenste tracering weer te geven. |
| **Webserver logboeken** | */LogFiles/http/RawLogs/* | Bevat tekst bestanden die zijn ingedeeld met de [uitgebreide W3C-indeling van logboek bestand](/windows/desktop/Http/w3c-logging). Deze informatie kan worden gelezen met een tekst editor of een hulp programma zoals [log parser](https://go.microsoft.com/fwlink/?LinkId=246619).<br/>App Service biedt geen ondersteuning voor de velden `s-computername`, `s-ip`of `cs-version`. |
| **Implementatie logboeken** | */Logfiles/Git/* en */Deployments/* | Logboeken bevatten die zijn gegenereerd door de interne implementatie processen, evenals logboeken voor git-implementaties. |

## <a name="send-logs-to-azure-monitor-preview"></a>Logboeken naar Azure Monitor verzenden (preview-versie)

Met de nieuwe [integratie van Azure monitor](https://aka.ms/appsvcblog-azmon)kunt u [Diagnostische instellingen maken (preview)](https://azure.github.io/AppService/2019/11/01/App-Service-Integration-with-Azure-Monitor.html#create-a-diagnostic-setting) voor het verzenden van logboeken naar opslag Accounts, Event hubs en log Analytics. 

> [!div class="mx-imgBorder"]
> Diagnostische instellingen ![(preview)](media/troubleshoot-diagnostic-logs/diagnostic-settings-page.png)

### <a name="supported-log-types"></a>Ondersteunde logboek typen

De volgende tabel bevat de ondersteunde logboek typen en beschrijvingen: 

| Logboek type | Windows-ondersteuning | Linux-ondersteuning (docker) | Beschrijving |
|-|-|-|
| AppServiceConsoleLogs | TBA | Ja | Standaard uitvoer en standaard fout |
| AppServiceHTTPLogs | Ja | Ja | Webserver logboeken |
| AppServiceEnvironmentPlatformLogs | Ja | Ja | App Service Environment: schalen, configuratie wijzigingen en status logboeken|
| AppServiceAuditLogs | Ja | Ja | Aanmeldings activiteiten via FTP en kudu |
| AppServiceFileAuditLogs | TBA | Ja | Bestands wijzigingen via FTP en kudu |
| AppServiceAppLogs | TBA | Java SE & Tomcat | Toepassings logboeken |

## <a name="nextsteps"></a> Volgende stappen
* [Query's uitvoeren op Logboeken met Azure Monitor](../azure-monitor/log-query/log-query-overview.md)
* [Azure App Service bewaken](web-sites-monitor.md)
* [Problemen met Azure App Service oplossen in Visual Studio](troubleshoot-dotnet-visual-studio.md)
* [App-Logboeken in HDInsight analyseren](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
