---
title: Het bewaken en verminderen van beperking-Azure Time Series Insights | Microsoft Docs
description: Meer informatie over het bewaken, vaststellen en oplossen van prestatie problemen die latentie en beperking in Azure Time Series Insights veroorzaken.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: 245a0b18187ff1c1b226e94b03374f2c071e51c0
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314824"
---
# <a name="monitor-and-mitigate-throttling-to-reduce-latency-in-azure-time-series-insights"></a>Beperking controleren en beperken om de latentie in Azure Time Series Insights te verminderen

Wanneer de hoeveelheid binnenkomende gegevens de configuratie van uw omgeving overschrijdt, kan er latentie of beperking in Azure Time Series Insights optreden.

U kunt latentie en beperking voor komen door uw omgeving correct te configureren voor de hoeveelheid gegevens die u wilt analyseren.

U hebt hoogstwaarschijnlijk latentie en beperking bij het volgende:

- Voeg een gebeurtenis bron toe die oude gegevens bevat die mogelijk de toegewezen ingangs snelheden overschrijden (Time Series Insights moet worden opgevangen).
- Voeg meer gebeurtenis bronnen toe aan een omgeving, wat resulteert in een piek van aanvullende gebeurtenissen (wat de capaciteit van uw omgeving kan overschrijden).
- Grote hoeveel heden historische gebeurtenissen naar een gebeurtenis bron pushen, wat resulteert in een vertraging (Time Series Insights moet worden opgevangen).
- Koppel referentie gegevens met telemetrie, wat resulteert in een grotere gebeurtenis grootte. Vanuit een beperkings perspectief wordt een ingangs gegevens pakket met een pakket grootte van 32 KB behandeld als 32-gebeurtenissen, die elk een grootte hebben van 1 KB. De Maxi maal toegestane gebeurtenis grootte is 32 KB; gegevens pakketten groter dan 32 KB worden afgekapt.

## <a name="video"></a>Video

### <a name="learn-about-time-series-insights-data-ingress-behavior-and-how-to-plan-for-itbr"></a>Meer informatie over het gedrag van Time Series Insights voor het inkomen van gegevens en hoe u het kunt plannen.</br>

> [!VIDEO https://www.youtube.com/embed/npeZLAd9lxo]

## <a name="monitor-latency-and-throttling-with-alerts"></a>Latentie controleren en beperken met waarschuwingen

Waarschuwingen kunnen u helpen bij het vaststellen en oplossen van latentie problemen die in uw omgeving optreden.

1. Selecteer uw Time Series Insights omgeving in het Azure Portal. Selecteer vervolgens **waarschuwingen**.

   [![een waarschuwing aan uw Time Series Insights omgeving toevoegen](media/environment-mitigate-latency/mitigate-latency-add-alert.png)](media/environment-mitigate-latency/mitigate-latency-add-alert.png#lightbox)

1. Selecteer **+ Nieuwe waarschuwingsregel**. Het deel venster **regel maken** wordt weer gegeven. Selecteer **toevoegen** onder **voor waarde**.

   [deel venster waarschuwing ![toevoegen](media/environment-mitigate-latency/mitigate-latency-add-pane.png)](media/environment-mitigate-latency/mitigate-latency-add-pane.png#lightbox)

1. Vervolgens configureert u de exacte voor waarden voor de signaal logica.

   [signaal logica ![configureren](media/environment-mitigate-latency/configure-alert-rule.png)](media/environment-mitigate-latency/configure-alert-rule.png#lightbox)

   Hier kunt u waarschuwingen configureren aan de hand van een van de volgende voor waarden:

   |Gegevens  |Beschrijving  |
   |---------|---------|
   |**Ontvangen bytes van binnenkomend verkeer**     | Aantal onbewerkte bytes dat uit gebeurtenis bronnen is gelezen. Aantal onbewerkte items bevat doorgaans de naam en waarde van de eigenschap.  |  
   |**Ongeldige berichten ontvangen**     | Aantal ongeldige berichten dat is gelezen uit alle Azure Event Hubs-of Azure IoT Hub-gebeurtenis bronnen.      |
   |**Ontvangen berichten met ingang**   | Aantal berichten dat is gelezen uit alle gebeurtenis bronnen van Event Hubs of IoT hubs.        |
   |**In ingangs opgeslagen bytes**     | De totale grootte van de opgeslagen en beschik bare gebeurtenissen voor de query. Grootte wordt alleen berekend op de waarde van de eigenschap.        |
   |**Opgeslagen gebeurtenissen**     |   Aantal samengevoegde gebeurtenissen dat is opgeslagen en beschikbaar is voor query's.      |
   |    van **ontvangen berichten tijds vertraging bericht**|  Verschil in seconden tussen het tijdstip waarop het bericht in de bron van de gebeurtenis in de wachtrij is geplaatst en de tijd die wordt verwerkt in binnenkomend verkeer.      |
   |    **aantal ontvangen inkomende berichten**|  Het verschil tussen het Volg nummer van de laatste bericht in de bron partitie en het Volg nummer van het bericht dat wordt verwerkt in ingress.      |

   Selecteer **Done**.

1. Nadat u de gewenste signaal logica hebt geconfigureerd, controleert u de gekozen waarschuwings regel visueel.

   [![latentie weer geven en grafieken](media/environment-mitigate-latency/mitigate-latency-view-and-charting.png)](media/environment-mitigate-latency/mitigate-latency-view-and-charting.png#lightbox)

## <a name="throttling-and-ingress-management"></a>Beperkings-en ingangs beheer

* Als u een beperking ondervindt, wordt er een waarde voor de *tijds vertraging van ontvangen berichten weer gegeven* . u weet hoeveel seconden achter uw time series Insights-omgeving afkomstig zijn van de werkelijke tijd die het bericht ophaalt uit de bron van de gebeurtenis (exclusief indexerings tijd van appx. 30-60 seconden).  

  De vertraging bij het *Ontvangen van berichten* van het aantal inkomende berichten moet ook een waarde hebben, zodat u kunt bepalen hoeveel achterstanden er achter u zijn.  De eenvoudigste manier om aan de slag te gaan is om de capaciteit van uw omgeving te verg Roten tot een grootte waardoor u het verschil kunt oplossen.  

  Als uw S1-omgeving bijvoorbeeld vertraging van 5.000.000 berichten demonstreert, kunt u de grootte van uw omgeving tot zes eenheden verg Roten om ongeveer een dag te krijgen.  U kunt nog meer verg Roten om sneller te kunnen werken. De ophaal periode is een veelvoorkomende gebeurtenis bij het inrichten van een omgeving, met name wanneer u deze verbindt met een gebeurtenis bron die al gebeurtenissen bevat of wanneer u grote hoeveel heden historische gegevens uploadt.

* Een andere techniek is het instellen van een waarschuwing over een **opgeslagen ingangs gebeurtenissen** > = een drempel waarde die iets lager is dan de totale capaciteit van de omgeving voor een periode van twee uur.  Deze waarschuwing helpt u te begrijpen als u voortdurend op capaciteit werkt, wat een hoge kans op latentie aangeeft. 

  Als u bijvoorbeeld drie S1-eenheden hebt ingericht (of 2100 gebeurtenissen per minuut ingangs capaciteit), kunt u voor twee uur een waarschuwing voor de **opgeslagen gebeurtenissen** van de ingang instellen voor > = 1900 gebeurtenissen. Als u deze drempel waarde voortdurend overschrijdt en u de waarschuwing hebt geactiveerd, bent u waarschijnlijk onder provisiond.  

* Als u vermoedt dat u wordt vertraagd, kunt u uw **ontvangen berichten** vergelijken met de egressed-berichten van uw gebeurtenis bron.  Als binnenkomend in uw event hub groter is dan uw **berichten ontvangen**, worden uw time series Insights waarschijnlijk beperkt.

## <a name="improving-performance"></a>Verbeterde prestaties

Om het beperken of de latentie te verminderen, is het de beste manier om dit te corrigeren door de capaciteit van uw omgeving te verg Roten.

U kunt latentie en beperking voor komen door uw omgeving correct te configureren voor de hoeveelheid gegevens die u wilt analyseren. Lees [uw omgeving schalen](time-series-insights-how-to-scale-your-environment.md)voor meer informatie over het toevoegen van capaciteit aan uw omgeving.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [het diagnosticeren en oplossen van problemen in uw time series Insights omgeving](time-series-insights-diagnose-and-solve-problems.md).

- Meer informatie [over het schalen van uw time series Insights-omgeving](time-series-insights-how-to-scale-your-environment.md).