---
title: Wat is Azure Load Balancer?
titleSuffix: Azure Load Balancer
description: Overzicht van Azure Load Balancer-functies, -architectuur en -implementatie. Meer informatie over hoe de Load Balancer werkt en hoe u deze kunt gebruiken in de Cloud.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer service and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 1/14/2020
ms.author: allensu
ms.openlocfilehash: ce8ae7f2f4de3659dc8dde98dc71d39886341498
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77602159"
---
# <a name="what-is-azure-load-balancer"></a>Wat is Azure Load Balancer?

*Taak verdeling* verwijst naar een gelijkmatige verdeling van belasting (binnenkomend netwerk verkeer) in een groep back-endservers of servers. 

Azure Load Balancer werkt op laag 4 van het OSI-model (Open Systems Interconnection). Het is het enige contact punt voor clients. Load Balancer distribueert inkomende stromen die binnenkomen bij de front-end van de load balancer voor back-endservers. Deze stromen zijn gebaseerd op geconfigureerde taakverdelings regels en status controles. De exemplaren van de back-endadresgroep kunnen Azure Virtual Machines of exemplaren in een schaalset voor virtuele machines zijn.

Een **[open bare Load Balancer](./concepts-limitations.md#publicloadbalancer)** kan uitgaande verbindingen bieden voor virtuele machines (vm's) in uw virtuele netwerk. Deze verbindingen worden gerealiseerd door hun privé-IP-adressen te vertalen naar open bare IP-adressen. Open bare load balancers worden gebruikt voor het verdelen van het Internet verkeer naar uw Vm's.

Een **[intern (of privé) Load Balancer](./concepts-limitations.md#internalloadbalancer)** wordt alleen gebruikt wanneer privé ip's alleen op het front-end zijn vereist. Interne load balancers worden gebruikt voor het verdelen van verkeer binnen een virtueel netwerk. Een load balancer frontend kan worden geopend vanuit een on-premises netwerk in een hybride scenario.

<p align="center">
  <img src="./media/load-balancer-overview/load-balancer.svg" width="512" title="Azure Load Balancer">
</p>

*Afbeelding: toepassingen met meerdere lagen verdelen met behulp van zowel open bare als interne Load Balancer*

Zie [Azure Load Balancer onderdelen en beperkingen](./concepts-limitations.md) voor meer informatie over de afzonderlijke Load Balancer onderdelen

>[!NOTE]
> Azure biedt een pakket volledig beheerde oplossingen voor taakverdeling voor uw scenario's. Zie [Wat is Azure-toepassing gateway?](../application-gateway/overview.md) als u behoefte hebt aan hoge prestaties en lage latentie, laag-4 taak verdeling? Als u op zoek bent naar globale DNS-taak verdeling, raadpleegt u [Wat is Traffic Manager?](../traffic-manager/traffic-manager-overview.md) Uw end-to-end-scenario's kunnen van pas komen bij het combi neren van deze oplossingen.
>
> Zie [overzicht van opties voor taak verdeling in azure](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview)voor een vergelijking van Azure-opties voor taak verdeling.

## <a name="why-use-azure-load-balancer"></a>Waarom Azure Load Balancer gebruiken?
Met Standard Load Balancer kunt u uw toepassingen schalen en Maxi maal beschik bare Services maken. De Load Balancer ondersteunt zowel binnenkomende als uitgaande scenario's. De Load Balancer biedt lage latentie en hoge door Voer en schaalt Maxi maal miljoenen stromen voor alle TCP-en UDP-toepassingen.

De belangrijkste scenario's die u kunt bereiken met Standard Load Balancer zijn onder andere:

- Taak verdeling van **[intern](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-manage-portal)** en **[extern](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-internal-portal)** verkeer naar virtuele machines van Azure.

- Verg root de beschik baarheid door resources te verdelen **[binnen](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-public-zonal-portal)** en **[tussen](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-public-zone-redundant-portal)** zones.

- Configureer de **[uitgaande connectiviteit](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)** voor virtuele Azure-machines.

- Gebruik **[status tests](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)** om resources met taak verdeling te bewaken.

- Gebruik **[poort door sturen](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-port-forwarding-portal)** om toegang te krijgen tot virtuele machines in een virtueel netwerk via een openbaar IP-adres en poort.

- Ondersteuning inschakelen voor **[taak verdeling](https://docs.microsoft.com/azure/virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell)** van **[IPv6](https://docs.microsoft.com/azure/virtual-network/ipv6-overview)** .

- Standard Load Balancer biedt multidimensionale metrische gegevens via [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview).  Deze metrische gegevens kunnen worden gefilterd, gegroepeerd en uitgesplitst voor een bepaalde dimensie.  Ze bieden actuele en historische inzichten in de prestaties en status van uw service.  Resource Health wordt ook ondersteund. Raadpleeg **[Standard Load Balancer diagnostische](load-balancer-standard-diagnostics.md)** gegevens voor meer informatie.

- Taak verdeling van services op **[meerdere poorten, meerdere IP-adressen of beide](https://docs.microsoft.com/azure/load-balancer/load-balancer-multivip-overview)** .

- Verplaats **[interne](https://docs.microsoft.com/azure/load-balancer/move-across-regions-internal-load-balancer-portal)** en **[externe](https://docs.microsoft.com/azure/load-balancer/move-across-regions-external-load-balancer-portal)** Load Balancer resources over Azure-regio's.

- Taak verdeling van de TCP-en UDP-stroom op alle poorten tegelijk met behulp van **[ha-poorten](https://docs.microsoft.com/azure/load-balancer/load-balancer-ha-ports-overview)** .

### <a name="securebydefault"></a>Standaard beveiligd

Standard Load Balancer is gebouwd op basis van het beveiligings model voor vertrouwens relaties van het netwerk. Standard Load Balancer standaard beveiligd en maakt deel uit van uw virtuele netwerk. Het virtuele netwerk is een privé-en geïsoleerd netwerk.  Dit betekent dat standaard load balancers en standaard open bare IP-adressen worden gesloten voor binnenkomende stromen, tenzij ze worden geopend door netwerk beveiligings groepen. Nsg's worden gebruikt om toegestaan verkeer expliciet toe te staan.  Als u geen NSG op een subnet of NIC van uw virtuele-machine bron hebt, mag het verkeer deze bron niet bereiken. Zie [netwerk beveiligings groepen](../virtual-network/security-overview.md)voor meer informatie over nsg's en hoe u deze kunt Toep assen voor uw scenario.
Basis Load Balancer is standaard geopend met het internet.


## <a name="pricing-and-sla"></a>Prijzen en SLA

Zie [Load Balancer prijzen](https://azure.microsoft.com/pricing/details/load-balancer/)voor Standard Load Balancer prijs informatie.
Basic Load Balancer wordt gratis aangeboden.
Zie [Sla voor Load Balancer](https://aka.ms/lbsla). Basic Load Balancer heeft geen SLA.

## <a name="next-steps"></a>Volgende stappen

Zie [een open bare Standard Load Balancer maken](quickstart-load-balancer-standard-public-portal.md) om aan de slag te gaan met het gebruik van een Load Balancer.

Voor meer informatie over Azure Load Balancer beperkingen en onderdelen raadpleegt u [Azure Load Balancer concepten en beperkingen](./concepts-limitations.md)
