---
title: Media Services metrische gegevens en Diagnostische logboeken met Azure Monitor
titleSuffix: Azure Media Services
description: Meer informatie over het bewaken van Azure Media Services metrische gegevens en Diagnostische logboeken via Azure Monitor.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: f075362f976e6abb26c9781c4b0cdeb7912c0862
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514031"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs-via-azure-monitor"></a>Media Services metrische gegevens en Diagnostische logboeken via Azure Monitor bewaken

Met [Azure monitor](../../azure-monitor/overview.md) kunt u metrische gegevens en Diagnostische logboeken bewaken die u helpen begrijpen hoe uw apps worden uitgevoerd. Alle gegevens die worden verzameld door Azure Monitor, komen in een van de volgende twee fundamentele typen voor: metrieken en Logboeken. U kunt Media Services Diagnostische logboeken bewaken en waarschuwingen en meldingen maken voor de verzamelde metrische gegevens en Logboeken. U kunt de metrische gegevens visualiseren en analyseren met behulp van [Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md). U kunt logboeken naar [Azure Storage](https://azure.microsoft.com/services/storage/)verzenden, ze streamen naar [Azure Event hubs](https://azure.microsoft.com/services/event-hubs/), ze exporteren naar [log Analytics](https://azure.microsoft.com/services/log-analytics/)of services van derden gebruiken.

Zie [Azure monitor metrische gegevens](../../azure-monitor/platform/data-platform.md) en [Azure monitor Diagnostische logboeken](../../azure-monitor/platform/platform-logs-overview.md)voor een gedetailleerd overzicht.

In dit onderwerp worden ondersteunde [Media Services metrische gegevens](#media-services-metrics) en [Media Services Diagnostische logboeken](#media-services-diagnostic-logs)beschreven.

## <a name="media-services-metrics"></a>Media Services metrische gegevens

Metrische gegevens worden verzameld met regelmatige tussenpozen al dan niet de waarde wordt gewijzigd. Ze zijn nuttig omdat ze vaak kunnen worden verzameld, en een waarschuwing kan snel worden geactiveerd met relatief eenvoudige logica op waarschuwingen. Zie [metrische waarschuwingen maken, weer geven en beheren met Azure monitor](../../azure-monitor/platform/alerts-metric.md)voor meer informatie over het maken van metrische gegevens.

Media Services biedt ondersteuning voor het bewaken van metrische gegevens voor de volgende resources:

* Account
* Streaming-eind punt

### <a name="account"></a>Account

U kunt de metrische gegevens van het volgende account bewaken.

|Naam van metrische waarde|Weergavenaam|Beschrijving|
|---|---|---|
|AssetCount|Aantal assets|Assets in uw account.|
|AssetQuota|Activa quotum|Activa quota in uw account.|
|AssetQuotaUsedPercentage|Percentage gebruikt voor het activa quotum|Het percentage van het activa quotum dat al wordt gebruikt.|
|ContentKeyPolicyCount|Aantal beleids regels voor inhouds sleutels|Beleids regels voor inhouds sleutels in uw account.|
|ContentKeyPolicyQuota|Quotum voor inhouds sleutel beleid|Quota voor beleids regels voor inhouds sleutels in uw account.|
|ContentKeyPolicyQuotaUsedPercentage|Percentage gebruikt quotum voor inhouds sleutel beleid|Het percentage van het quotum voor het inhouds sleutel beleid wordt al gebruikt.|
|StreamingPolicyCount|Aantal stroomsgewijze beleids regels|Stroomsgewijze beleids regels in uw account.|
|StreamingPolicyQuota|Quota voor streaming-beleid|Het quotum voor het streamen van beleid in uw account.|
|StreamingPolicyQuotaUsedPercentage|Percentage gebruikt quotum voor het streaming-beleid|Het percentage van het quotum voor het streaming-beleid wordt al gebruikt.|

U moet ook [rekening quota's en beperkingen](limits-quotas-constraints.md)bekijken.

### <a name="streaming-endpoint"></a>Streaming-eind punt

De volgende Media Services gegevens [stromen voor streaming-eind punten](https://docs.microsoft.com/rest/api/media/streamingendpoints) worden ondersteund:

|Naam van metrische waarde|Weergavenaam|Beschrijving|
|---|---|---|
|Aanvragen|Aanvragen|Geeft het totale aantal HTTP-aanvragen dat door het streaming-eind punt wordt geleverd.|
|Uitgaand verkeer|Uitgaand verkeer|Het totale aantal uitgaande bytes. Bijvoorbeeld bytes die worden gestreamd door het streaming-eind punt.|
|SuccessE2ELatency|Geslaagde end-to-end-latentie|Tijds duur vanaf het moment waarop het streaming-eind punt de aanvraag bij het verzenden van de laatste byte van het antwoord heeft ontvangen.|

### <a name="why-would-i-want-to-use-metrics"></a>Waarom zou ik metrische gegevens willen gebruiken?

Hier volgen enkele voor beelden van de manier waarop bewaking Media Services metrische gegevens u kan helpen inzicht te krijgen in hoe uw apps worden uitgevoerd. Enkele vragen die kunnen worden behandeld met Media Services metrische gegevens zijn:

* Hoe kan ik het standaard streaming-eind punt te controleren om te weten wanneer de limieten zijn overschreden?
* Hoe kan ik weet of ik voldoende Premium streaming-eindpunt schaal eenheden heb?
* Hoe kan ik een waarschuwing instellen om te weten wanneer mijn streaming-eind punten moeten worden geschaald?
* Hoe kan ik een waarschuwing instellen om te weten wanneer het maximale aantal uitgangen dat is geconfigureerd voor het account is bereikt?
* Hoe kan ik de uitsplitsing van mislukte aanvragen zien en wat de oorzaak van de fout is?
* Hoe kan ik zien hoeveel HLS of STREEPJES aanvragen worden opgehaald uit de packager?
* Hoe kan ik een waarschuwing instellen om te weten wanneer de drempel waarde van het aantal mislukte aanvragen is bereikt?

### <a name="example"></a>Voorbeeld

Zie [Media Services metrische gegevens bewaken](media-services-metrics-howto.md).

## <a name="media-services-diagnostic-logs"></a>Diagnostische logboeken Media Services

Diagnostische logboeken bieden uitgebreide en frequente gegevens over de werking van een Azure-resource. Zie [logboek gegevens van uw Azure-resources verzamelen en gebruiken](../../azure-monitor/platform/platform-logs-overview.md)voor meer informatie.

Media Services ondersteunt de volgende Diagnostische logboeken:

* Levering van sleutels

### <a name="key-delivery"></a>Levering van sleutels

|Name|Beschrijving|
|---|---|
|Aanvraag voor key delivery service|Logboeken die de informatie over de sleutel leverings service aanvragen weer geven. Zie [schema's](media-services-diagnostic-logs-schema.md)voor meer informatie.|

### <a name="why-would-i-want-to-use-diagnostics-logs"></a>Waarom zou ik Diagnostische logboeken willen gebruiken?

Enkele dingen die u kunt onderzoeken met belang rijke Diagnostische logboeken voor het leveren van problemen zijn:

* Bekijk het aantal licenties dat is geleverd door het DRM-type.
* Bekijk het aantal licenties dat door het beleid is geleverd.
* Fouten weer geven op DRM of beleids type.
* Bekijk het aantal niet-geautoriseerde licentie aanvragen van clients.

### <a name="example"></a>Voorbeeld

Zie [Diagnostische logboeken van media service bewaken](media-services-diagnostic-logs-howto.md).

## <a name="next-steps"></a>Volgende stappen

* [Logboek gegevens van uw Azure-resources verzamelen en gebruiken](../../azure-monitor/platform/platform-logs-overview.md)
* [Metrische waarschuwing maken, bekijken en beheren met Azure Monitor](../../azure-monitor/platform/alerts-metric.md)
* [Media Services metrische gegevens controleren](media-services-metrics-howto.md)
* [Diagnostische logboeken van Media Services bewaken](media-services-diagnostic-logs-howto.md)
