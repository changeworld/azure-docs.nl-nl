---
title: Veelgestelde vragen over prijzen voor Azure Netwerkprestatiemeter | Microsoft Docs
description: Veelgestelde vragen-Azure Netwerkprestatiemeter
ms.subservice: logs
ms.topic: conceptual
author: agummadi
ms.author: agummadi
ms.date: 04/02/2018
ms.openlocfilehash: c5a80c7ff6d08ce601a6c2bbb0db0ef866d3b425
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77654389"
---
# <a name="pricing-changes-for-azure-network-performance-monitor"></a>Prijs wijzigingen voor Azure Netwerkprestatiemeter

We hebben geluisterd naar uw feedback en hebben onlangs een [nieuwe prijs ervaring](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) geïntroduceerd voor verschillende bewakings Services in Azure. In dit artikel worden de prijs wijzigingen met betrekking tot Azure [Netwerkprestatiemeter](https://docs.microsoft.com/azure/networking/network-monitoring-overview) (NPM) vastgelegd in een gemakkelijk te lezen vraag-en antwoord indeling.

Netwerkprestatiemeter bestaat uit drie onderdelen:
* [Prestatie meter](https://docs.microsoft.com/azure/networking/network-monitoring-overview#performance-monitor)
* [Controle programma voor service-eind punten](https://docs.microsoft.com/azure/networking/network-monitoring-overview)
* [Monitor voor ExpressRoute](https://docs.microsoft.com/azure/networking/network-monitoring-overview#expressroute-monitor)

In de volgende secties worden de prijs wijzigingen voor de NPM-onderdelen uitgelegd.

## <a name="performance-monitor"></a>Prestatiemeter

**Hoe werd het gebruik van de prestatie meter in het oude model gefactureerd?**

De facturering voor NPM is gebaseerd op het gebruik en het verbruik van twee onderdelen:
* **Knoop punten**: alle synthetische trans acties zijn afkomstig van en eindigen op de knoop punten. Knoop punten worden ook wel agents of micro soft-beheer agenten genoemd.
* **Gegevens**: de resultaten van de verschillende netwerk tests worden opgeslagen in de werk ruimte log Analytics.

Onder het oude model werd de factuur berekend op basis van het aantal knoop punten en het gegenereerde gegevens volume. 

**Hoe wordt het gebruik van prestatie meter in rekening gebracht onder het nieuwe model?**

De functie prestatie meter in NPM wordt nu gefactureerd op basis van een combi natie van: 

* Subnet koppelingen bewaakt
* Gegevensvolume

**Wat is een subnet-koppeling?**

Prestatie meter bewaakt de connectiviteit tussen twee of meer locaties op het netwerk. De verbinding tussen een groep knoop punten of agents op één subnet en een groep knoop punten op een ander subnet wordt een subnet-koppeling genoemd.

**Ik heb twee subnetten (A en B) en ik heb meerdere agents op elk subnet. Met de prestatie meter wordt de connectiviteit van alle agents op subnet A gecontroleerd op alle agents in Subnet B. Worden er kosten in rekening gebracht op basis van het aantal verbindingen tussen subnetten?**

Nee. Voor facturerings doeleinden worden alle verbindingen van subnet A naar subnet B gegroepeerd in één subnet-koppeling. U wordt gefactureerd voor één verbinding. Prestatie meter blijft de connectiviteit tussen verschillende agents op elk subnet bewaken.

**Wat zijn de kosten voor het bewaken van een subnet-koppeling?**

Zie de sectie [ping mesh](https://azure.microsoft.com/pricing/details/network-watcher/) voor de kosten voor het bewaken van één subnet-koppeling voor de hele maand.

**Wat zijn de kosten voor gegevens die door prestatie meter worden gegenereerd?**

De kosten voor opname (gegevens uploaden naar Log Analytics werk ruimte in Azure Monitor, verwerking en indexering) zijn beschikbaar op de [pagina met prijzen](https://azure.microsoft.com/pricing/details/log-analytics/) voor log Analytics in de sectie gegevens opname. De kosten voor het bewaren van gegevens (dat wil zeggen, gegevens bewaard op de optie van de klant, na de eerste maand) zijn ook beschikbaar op de [pagina met prijzen](https://azure.microsoft.com/pricing/details/log-analytics/)in het gedeelte gegevens retentie.


## <a name="expressroute-monitor"></a>Monitor voor ExpressRoute

**Wat zijn de kosten voor het gebruik van de ExpressRoute-monitor?**

Kosten voor ExpressRoute-monitor worden gefactureerd op basis van het volume van de gegevens die tijdens de bewaking zijn gegenereerd. Zie ' wat zijn de kosten voor gegevens die worden gegenereerd door prestatie meter? ' voor meer informatie.

**Ik gebruik de ExpressRoute-monitor om meerdere ExpressRoute-circuits te bewaken. Worden er kosten in rekening gebracht op basis van het aantal circuits dat wordt bewaakt?**

Er worden geen kosten in rekening gebracht op basis van het aantal circuits of het type peering (bijvoorbeeld persoonlijke peering, micro soft-peering). Er worden kosten in rekening gebracht op basis van het volume van de gegevens, zoals eerder is uitgelegd.

**Wat is het volume van de gegevens die worden gegenereerd wanneer ExpressRoute één circuit bewaakt?**

Het volume van de gegevens die per maand worden gegenereerd, is als volgt wanneer ExpressRoute een particuliere peering-verbinding bewaakt:

|Percentiel      |Gegevens/maand (MB)|
| :---:          |           ---:|
|50<sup>th</sup> |            192|
|60<sup>th</sup> |            256|
|70<sup>th</sup> |            360|
|80<sup>th</sup> |            498|
|90<sup>th</sup> |            870|
|95<sup>th</sup> |           1560|


Volgens deze tabel betalen klanten met het 50e percentiel voor 192 MB aan gegevens. Bij USD $2.30/GB voor de eerste maand zijn de kosten voor het bewaken van een circuit USD $0,43 (= 192 * 2,30/1024).

**Wat zijn de redenen voor variaties in het volume van de gegevens?**

Hoeveel bewakings gegevens er worden gegenereerd, is afhankelijk van verschillende factoren, zoals:
* Aantal agents. De nauw keurigheid van fouten isolatie neemt toe met een toename van het aantal agents.
* Het aantal hops in het netwerk.
* Het aantal paden tussen de bron en de bestemming.

Klanten op het hoogste percentiel (in de voor gaande tabel) bewaken doorgaans hun circuits van verschillende Vantage-punten in hun on-premises netwerk. Meerdere agents worden ook dieper in het netwerk geplaatst, verder van de service provider edge-router. De agents worden vaak geplaatst op verschillende gebruikers sites, vertakkingen en racks in data centers.

## <a name="service-endpoint-monitor"></a>Controle programma voor service-eind punten

**Wat zijn de kosten voor het gebruik van de service-eindpunt monitor?**

De kosten voor het gebruik van de service-eindpunt monitor worden berekend op basis van:
* Aantal verbindingen
* Hoeveelheid gegevens

**Wat is een verbinding?**

Een verbinding is een test op het bereiken van een eind punt (URL of netwerk service) van één agent voor de hele maand. Als u bijvoorbeeld een verbinding met bing.com van drie agents bewaken, worden er drie verbindingen tot stand.

**Wat zijn de kosten voor service Endpoint monitor?**

Raadpleeg de sectie [verbindings bewaking](https://azure.microsoft.com/pricing/details/network-watcher/) voor de kosten voor het bewaken van een eind punt voor de hele maand. De kosten voor gegevens zijn beschikbaar op de [pagina met prijzen](https://azure.microsoft.com/pricing/details/log-analytics/) voor log Analytics, in de sectie gegevens opname.

## <a name="references"></a>Verwijzingen

[Veelgestelde vragen over log Analytics prijs](https://azure.microsoft.com/pricing/details/log-analytics/): de sectie Veelgestelde vragen bevat informatie over de gratis laag, de prijzen per knoop punt en andere prijs informatie.

