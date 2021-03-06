---
title: 'Azure-ExpressRoute: circuits en peering'
description: Deze pagina bevat een overzicht van ExpressRoute-circuits en routering domeinen/peering.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: mialdrid
ms.openlocfilehash: c68ffd019937f902567c3deda8d879448dc082da
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79281351"
---
# <a name="expressroute-circuits-and-peering"></a>ExpressRoute-circuits en -peering

ExpressRoute-circuits verbinden met uw on-premises infrastructuur Microsoft via een connectiviteitsprovider. Dit artikel helpt u inzicht in de ExpressRoute-circuits en routering domeinen/peering. De volgende afbeelding ziet u een logische representatie van connectiviteit tussen uw WAN en Microsoft.

![](./media/expressroute-circuit-peerings/expressroute-basic.png)

> [!IMPORTANT]
> Open bare Azure-peering is afgeschaft en is niet beschikbaar voor nieuwe ExpressRoute-circuits. Nieuwe circuits ondersteunen micro soft-peering en persoonlijke peering.  
>

## <a name="circuits"></a>ExpressRoute-circuits

Een ExpressRoute-circuit vertegenwoordigt een logische verbinding tussen uw on-premises infrastructuur en Microsoft cloud-services via een connectiviteitsprovider. U kunt meerdere ExpressRoute-circuits bestellen. Elk circuit kan in de dezelfde of verschillende regio's, en kan worden verbonden met uw locatie via verschillende connectiviteitsproviders.

ExpressRoute-circuits toewijzen niet aan een fysieke entiteiten. Een circuit wordt uniek geïdentificeerd door een standaard die GPT als een servicesleutel (s-sleutel aangeroepen). De servicesleutel is de enige informatie die worden uitgewisseld tussen Microsoft en de connectiviteitsprovider. De s-sleutel is niet een geheim om veiligheidsredenen. Er is een 1:1-toewijzing tussen een ExpressRoute-circuit en de s-sleutel.

Nieuwe ExpressRoute-circuits twee onafhankelijke peerings kunnen opnemen: persoonlijke peering en Microsoft-peering. Terwijl bestaande ExpressRoute-circuits drie de peerings kunnen bevatten: Azure openbare, persoonlijke Azure- en Microsoft. Elke peering is een combinatie van een onafhankelijke BGP-sessies, elk van deze toch toe geconfigureerd voor hoge beschikbaarheid. Er is een 1: n (1 < = N < = 3) toewijzing tussen een ExpressRoute-circuit en routering van domeinen. Een ExpressRoute-circuit kan hebben een, twee of alle drie de peerings per ExpressRoute-circuit is ingeschakeld.

Elk circuit heeft een vaste bandbreedte (50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 10 Gbps) en is toegewezen aan een connectiviteitsprovider en een locatie. De bandbreedte die u selecteert, wordt gedeeld door alle circuitpeerings

### <a name="quotas"></a>Quota's, limieten en beperkingen

Standaardquota en limieten gelden voor elk ExpressRoute-circuit. Raadpleeg de pagina [Azure-abonnement en service limieten, quota's en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md) voor actuele informatie over quota's.

## <a name="routingdomains"></a>ExpressRoute-peering

Een ExpressRoute-circuit heeft meerdere Routering domeinen/peerings die ermee verbonden zijn: Azure openbare, Azure privé- en Microsoft. Elke peering identiek is geconfigureerd op twee routers (in actief / actief- of delen van belasting configureren) voor hoge beschikbaarheid. Azure-Services worden gecategoriseerd als *Azure Public* en *Azure private* om de IP-adresserings schema's weer te geven.

![](./media/expressroute-circuit-peerings/expressroute-peerings.png)

### <a name="privatepeering"></a>Persoonlijke Azure-peering

Azure compute-services, namelijk virtuele machines (IaaS) en cloudservices (PaaS), die zijn geïmplementeerd in een virtueel netwerk kunnen worden verbonden via het domein voor persoonlijke peering. Het domein voor persoonlijke peering wordt beschouwd als een vertrouwde uitbreiding van uw Basisnetwerk in Microsoft Azure. U kunt bidirectionele connectiviteit tussen uw Basisnetwerk en de Azure-netwerken (VNets) instellen. Deze peering, kunt u verbinding maken met virtuele machines en services rechtstreeks op hun persoonlijke IP-adressen in de cloud.  

U kunt meer dan één virtueel netwerk verbinden met het domein voor persoonlijke peering. Bekijk de [pagina met veelgestelde vragen](expressroute-faqs.md) voor informatie over limieten en beperkingen. U kunt de pagina [Azure-abonnement en service limieten, quota's en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md) voor actuele informatie over limieten bekijken.  Raadpleeg de pagina [route ring](expressroute-routing.md) voor gedetailleerde informatie over de routerings configuratie.

### <a name="microsoftpeering"></a>Micro soft-peering

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

Connectiviteit met micro soft onlineservices (Office 365 en Azure PaaS Services) vindt plaats via micro soft-peering. We inschakelen bidirectionele connectiviteit tussen uw WAN- en Microsoft cloud-services via de Microsoft-peering routering domein. U moet verbinding maken met Microsoft cloud-services alleen via het openbare IP-adressen die eigendom zijn van u of uw connectiviteitsprovider en u moet voldoen aan alle gedefinieerde regels. Zie de pagina met ExpressRoute- [vereisten](expressroute-prerequisites.md) voor meer informatie.

Raadpleeg de [pagina met veelgestelde vragen](expressroute-faqs.md) voor meer informatie over ondersteunde services, kosten en configuratie details. Zie de pagina [ExpressRoute locations](expressroute-locations.md) voor informatie over de lijst met connectiviteits providers die ondersteuning bieden voor micro soft-peering.

## <a name="peeringcompare"></a>Peering-vergelijking

De volgende tabel worden de drie de peerings vergeleken:

[!INCLUDE [peering comparison](../../includes/expressroute-peering-comparison.md)]

U kunt een of meer van de Routeringsdomeinen inschakelen als onderdeel van uw ExpressRoute-circuit. U kunt kiezen om alle Routeringsdomeinen plaats op de dezelfde VPN-verbinding als u wilt combineren in één routeringsdomein. U kunt ze ook op verschillende Routeringsdomeinen, vergelijkbaar met het diagram plaatsen. De aanbevolen configuratie is dat privépeering rechtstreeks met het Basisnetwerk verbonden is en de openbare en Microsoft peeringkoppelingen zijn verbonden met uw DMZ.

Elke peering moeten afzonderlijke BGP-sessies (een paar voor elk type peering). De BGP-sessie-paren bieden een maximaal beschikbare koppeling. Als u verbinding via layer 2-connectiviteitsproviders maakt, bent u verantwoordelijk voor het configureren en beheren van routering. U kunt meer te weten komen door de [werk stromen](expressroute-workflows.md) te controleren voor het instellen van ExpressRoute.

## <a name="health"></a>ExpressRoute-status

ExpressRoute-circuits kunnen worden bewaakt voor Beschik baarheid, connectiviteit met VNets en bandbreedte gebruik met behulp van [Netwerkprestatiemeter](https://docs.microsoft.com/azure/networking/network-monitoring-overview) (NPM).

NPM controleert de status van persoonlijke Azure-peering en Microsoft-peering. Bekijk onze [post](https://azure.microsoft.com/blog/monitoring-of-azure-expressroute-in-preview/) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* Zoek een serviceprovider Zie [ExpressRoute-service providers en-locaties](expressroute-locations.md).
* Controleer of aan alle vereisten is voldaan. Zie [ExpressRoute prerequisites](expressroute-prerequisites.md) (Vereisten voor ExpressRoute).
* Configureer uw ExpressRoute-verbinding.
  * [Create and manage ExpressRoute circuits](expressroute-howto-circuit-portal-resource-manager.md) (ExpressRoute-circuits maken en beheren)
  * [Routering (peering) configureren voor ExpressRoute-circuits](expressroute-howto-routing-portal-resource-manager.md)
