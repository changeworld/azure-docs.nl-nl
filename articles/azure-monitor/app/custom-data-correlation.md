---
title: Inzichten Azure-toepassing | Microsoft Docs
description: Correleer gegevens van Application Insights aan andere gegevens sets, zoals gegevens verrijking of opzoek tabellen, niet-Application Insights gegevens bronnen en aangepaste gegevens.
ms.topic: conceptual
author: eternovsky
ms.author: evternov
ms.date: 08/08/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 121e4699bd6a72f6865d3a6ffdef58c1b3806047
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2020
ms.locfileid: "79082758"
---
# <a name="correlating-application-insights-data-with-custom-data-sources"></a>Application Insights gegevens correleren met aangepaste gegevens bronnen

Application Insights verzamelt verschillende gegevens typen: uitzonde ringen, traceringen, pagina weergaven en anderen. Hoewel dit vaak voldoende is om de prestaties, betrouw baarheid en het gebruik van uw toepassing te onderzoeken, zijn er gevallen waarin het nuttig is om te correleren van de gegevens die zijn opgeslagen in Application Insights naar andere volledig aangepaste gegevens sets.

In de volgende situaties kunt u aangepaste gegevens gebruiken:

- Gegevens verrijking of opzoek tabellen: u kunt bijvoorbeeld een server naam aanvullen met de eigenaar van de server en de locatie van het lab waarin deze kan worden gevonden 
- Correlatie met niet-Application Insights gegevens bronnen: u kunt bijvoorbeeld gegevens over een aankoop in een Web Store correleren met informatie van uw aankoop-fulfillment-service om te bepalen hoe nauw keurig de geschatte verzend tijd 
- Volledig aangepaste gegevens: veel van onze klanten hebben de query taal en de prestaties van het Azure Monitor-logboek platform voor het maken van een back-up van Application Insights en willen ze gebruiken om gegevens op te vragen die geen verband houden met Application Insights. Als u bijvoorbeeld de prestaties van het zonne paneel wilt bijhouden als onderdeel van een slimme start installatie, zoals [hier](https://www.catapultsystems.com/blogs/using-log-analytics-and-a-special-guest-to-forecast-electricity-generation/)wordt beschreven.

## <a name="how-to-correlate-custom-data-with-application-insights-data"></a>Aangepaste gegevens correleren met Application Insights gegevens 

Omdat Application Insights wordt ondersteund door het krachtige Azure Monitor-logboek platform, kunnen we de volledige kracht van Azure Monitor gebruiken om de gegevens op te nemen. Vervolgens schrijven we query's met behulp van de operator ' samen voegen ', waarmee deze aangepaste gegevens worden afgestemd op de gegevens die voor ons beschikbaar zijn in Azure Monitor Logboeken. 

## <a name="ingesting-data"></a>Gegevens opnemen

In deze sectie wordt beschreven hoe u uw gegevens ophaalt in Azure Monitor-Logboeken.

Als u er nog geen hebt, kunt u een nieuwe Log Analytics-werk ruimte inrichten door [deze instructies](../learn/quick-collect-azurevm.md) te volgen met behulp van de stap ' een werk ruimte maken '.

Om het verzenden van logboek gegevens naar Azure Monitor te starten. Er zijn verschillende opties:

- Voor een synchroon mechanisme kunt u de [Data Collector-API](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api) rechtstreeks aanroepen of de logische app-connector gebruiken – gewoon zoeken naar ' Azure log Analytics ' en de optie ' gegevens verzenden ' kiezen:

  ![Scherm opname kiezen en actie](./media/custom-data-correlation/01-logic-app-connector.png)  

- Voor een asynchrone optie gebruikt u de Data Collector API om een verwerkings pijplijn te bouwen. Raadpleeg [dit artikel](https://docs.microsoft.com/azure/log-analytics/log-analytics-create-pipeline-datacollector-api) voor meer informatie.

## <a name="correlating-data"></a>Correleren van gegevens

Application Insights is gebaseerd op het Azure Monitor-logboek platform. We kunnen daarom [Cross-resource join's](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search) gebruiken voor het correleren van gegevens die zijn opgenomen in azure monitor met onze Application Insights-gegevens.

We kunnen bijvoorbeeld onze Lab-inventaris en-locaties opnemen in een tabel met de naam ' LabLocations_CL ' in een Log Analytics werk ruimte met de naam ' myLA '. Als we de aanvragen die zijn bijgehouden in Application Insights-app met de naam "myAI", vervolgens willen bekijken en de computer namen die de aanvragen hebben verzonden, correleren aan de locaties van deze machines die zijn opgeslagen in de eerder genoemde aangepaste tabel, kunnen we de volgende query uitvoeren vanuit de Application Insights-of Azure Monitor context:

```
app('myAI').requests
| join kind= leftouter (
    workspace('myLA').LabLocations_CL
    | project Computer_S, Owner_S, Lab_S
) on $left.cloud_RoleInstance == $right.Computer
```

## <a name="next-steps"></a>Volgende stappen

- Bekijk de naslag informatie voor de [Data Collector-API](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api) .
- Voor meer informatie over [Cross-resource-samen voegingen](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search).
