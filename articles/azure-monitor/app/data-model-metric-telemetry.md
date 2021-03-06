---
title: Gegevens model voor metrische telemetrie-Azure-toepassing inzichten
description: Application Insights gegevens model voor metrische telemetrie
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 3e4a1fc3de58b8e65ab9c7a288bdf3eb37e7bae0
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671967"
---
# <a name="metric-telemetry-application-insights-data-model"></a>Metrische telemetrie: Application Insights gegevens model

Er zijn twee soorten metrische telemetriegegevens die worden ondersteund door [Application Insights](../../azure-monitor/app/app-insights-overview.md): single meet en vooraf geaggregeerde metriek. Eén meting is slechts een naam en waarde. Statistische metrische gegevens Hiermee geeft u de minimum-en maximum waarde op van de metriek in het aggregatie-interval en de standaard deviatie van de metriek.

Bij een vooraf samengestelde metrische telemetriegegevens wordt ervan uitgegaan dat de aggregatie periode één minuut was.

Er zijn verschillende bekende metrische namen die door Application Insights worden ondersteund. Deze metrische gegevens worden in de tabel Performance Counters geplaatst.

Metrische waarde waarmee systeem-en proces tellers worden weer gegeven:

| **.NET-naam**             | **Neutraal naam van het platform** | **REST API naam** | **Beschrijving**
| ------------------------- | -------------------------- | ----------------- | ---------------- 
| `\Processor(_Total)\% Processor Time` | Onderhanden werk... | [processorCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessorCpuPercentage) | totale machine-CPU
| `\Memory\Available Bytes`                 | Onderhanden werk... | [memoryAvailableBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FmemoryAvailableBytes) | Toont de hoeveelheid fysiek geheugen (in bytes) die beschikbaar is voor processen die op de computer worden uitgevoerd. Het wordt berekend door de hoeveelheid ruimte op de geheugen lijsten met nulwaarden, vrij en stand-by op te tellen. Beschikbaar geheugen is klaar voor gebruik. Zeroed geheugen bestaat uit pagina's van geheugen gevuld met nullen om te voor komen dat latere processen gegevens zien die worden gebruikt door een eerder proces; stand-by geheugen is geheugen dat is verwijderd uit de werkset van een proces (het fysieke geheugen) en de route naar de schijf, maar nog steeds beschikbaar is om te worden ingetrokken. [Geheugen object](https://msdn.microsoft.com/library/ms804008.aspx) weer geven
| `\Process(??APP_WIN32_PROC??)\% Processor Time` | Onderhanden werk... | [processCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessCpuPercentage) | CPU van het proces dat als host fungeert voor de toepassing
| `\Process(??APP_WIN32_PROC??)\Private Bytes`      | Onderhanden werk... | [processPrivateBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessPrivateBytes) | geheugen dat wordt gebruikt door het proces dat als host fungeert voor de toepassing
| `\Process(??APP_WIN32_PROC??)\IO Data Bytes/sec` | Onderhanden werk... | [processIOBytesPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessIOBytesPerSecond) | het aantal I/O-bewerkingen wordt uitgevoerd door het proces dat als host fungeert voor de toepassing
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec`             | Onderhanden werk... | [requestsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsPerSecond) | aantal aanvragen dat is verwerkt door de toepassing 
| `\.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec`    | Onderhanden werk... | [exceptionsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FexceptionsPerSecond) | frequentie van uitzonde ringen die worden veroorzaakt door de toepassing
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time`   | Onderhanden werk... | [requestExecutionTime](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestExecutionTime) | gemiddelde uitvoerings tijd van aanvragen
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue` | Onderhanden werk... | [requestsInQueue](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsInQueue) | aantal aanvragen dat wacht op verwerking in een wachtrij

## <a name="name"></a>Name

De naam van de metrische gegevens die u wilt weer geven in Application Insights Portal en gebruikers interface. 

## <a name="value"></a>Waarde

Eén waarde voor meting. Som van afzonderlijke metingen voor de aggregatie.

## <a name="count"></a>Count

Metrieke gewicht van de cumulatieve metriek. Mag niet worden ingesteld voor een meting.

## <a name="min"></a>Min.

Minimum waarde van de cumulatieve metriek. Mag niet worden ingesteld voor een meting.

## <a name="max"></a>Max.

De maximum waarde van de cumulatieve metriek. Mag niet worden ingesteld voor een meting.

## <a name="standard-deviation"></a>Standaardafwijking

Standaard afwijking van de cumulatieve metriek. Mag niet worden ingesteld voor een meting.

## <a name="custom-properties"></a>Aangepaste eigenschappen

Metriek met de aangepaste eigenschap `CustomPerfCounter` ingesteld op `true` geeft aan dat de metrische gegevens het prestatie meter item van Windows vertegenwoordigen. Deze metrische gegevens worden in de tabel Performance Counters geplaatst. Niet in customMetrics. Ook de naam van deze metriek wordt geparseerd om categorie-, teller-en instantie namen op te halen.

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het gebruik [van Application Insights-API voor aangepaste gebeurtenissen en metrische gegevens](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric).
- Zie [gegevens model](data-model.md) voor Application Insights typen en gegevens model.
- Bekijk de [platforms](../../azure-monitor/app/platforms.md) die door Application Insights worden ondersteund.
