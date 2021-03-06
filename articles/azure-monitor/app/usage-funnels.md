---
title: Azure-toepassing Insights-trechters
description: Meer informatie over hoe u trechters kunt gebruiken om te ontdekken hoe klanten met uw toepassing communiceren.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 07/17/2017
ms.reviewer: mbullwin
ms.openlocfilehash: 89440a6385bab5b917a866b686e8d2ba828c92e5
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671049"
---
# <a name="discover-how-customers-are-using-your-application-with-application-insights-funnels"></a>Ontdek hoe klanten uw toepassing gebruiken met Application Insights trechters

Meer informatie over de ervaring van de klant is van het grootste belang voor uw bedrijf. Als uw toepassing meerdere fasen omvat, moet u weten of de meeste klanten door het hele proces worden uitgevoerd of dat het proces op een bepaald moment wordt beëindigd. De voortgang van een reeks stappen in een webtoepassing wordt een *trechter*genoemd. U kunt Azure-toepassing Insights-trechters gebruiken om inzicht te krijgen in uw gebruikers en stapsgewijze conversie tarieven te bewaken. 

## <a name="create-your-funnel"></a>Uw trechter maken
Voordat u uw trechter maakt, moet u beslissen welke vraag u wilt beantwoorden. Stel dat u wilt weten hoeveel gebruikers de start pagina bekijken, een klant profiel bekijken en een ticket maken. In dit voor beeld willen de eigen aren van het bedrijf van Fabrikam Fiber weten welk percentage van klanten een klant ticket heeft gemaakt.

Hier volgen de stappen die ze nodig hebben om hun trechter te maken.

1. Selecteer in het hulp programma Application Insights trechters de optie **Nieuw**.
1. Selecteer in de vervolg keuzelijst **tijds bereik** de waarde **afgelopen 90 dagen**. Selecteer **mijn trechters** of **gedeelde trechters**.
1. Selecteer in de vervolg keuzelijst **stap 1** de optie **index**. 
1. Selecteer in de lijst **stap 2** de optie **klant**.
1. Selecteer in de lijst **stap 3** de optie **maken**.
1. Voeg een naam toe aan de trechter en selecteer **Opslaan**.

De volgende scherm afbeelding toont een voor beeld van het soort gegevens dat het hulp programma trechters genereert. De fabrikam-eigen aren kunnen zien dat in de afgelopen 90 dagen 54,3 procent van hun klanten die de start pagina hebben bezocht, een klant ticket heeft gemaakt. Ze kunnen ook zien dat 2.700 van hun klanten is gearriveerd naar de index op de start pagina. Dit kan duiden op een probleem bij het vernieuwen.


![Scherm afbeelding van het hulp programma trechter met gegevens](media/usage-funnels/funnel1.png)

### <a name="funnels-features"></a>Functies van trechters
De vorige scherm afbeelding bevat vijf gemarkeerde gebieden. Dit zijn de functies van trechters. In de volgende lijst vindt u meer informatie over elk corresponderende gebied in de scherm afbeelding:
1. Als uw app wordt voor bereid, wordt er een voorbeeld banner weer geven. Als u de banner selecteert, wordt er een context venster geopend, waarin wordt uitgelegd hoe u de steek proeven kunt uitschakelen. 
2. U kunt uw trechter exporteren naar [Power bi](../../azure-monitor/app/export-power-bi.md ).
3. Selecteer een stap om meer informatie aan de rechter kant weer te geven. 
4. De historische conversie grafiek toont de conversie tarieven gedurende de afgelopen 90 dagen. 
5. Krijg meer inzicht in uw gebruikers door het hulp programma gebruikers te gebruiken. U kunt in elke stap filters gebruiken. 

## <a name="next-steps"></a>Volgende stappen
  * [Overzicht van gebruik](usage-overview.md)
  * [Gebruikers, sessies en gebeurtenissen](usage-segmentation.md)
  * [Retentie](usage-retention.md)
  * [Werkmappen](../../azure-monitor/app/usage-workbooks.md)
  * [Gebruikers context toevoegen](usage-send-user-context.md)
  * [Exporteren naar Power BI](../../azure-monitor/app/export-power-bi.md )

