---
title: Veelgestelde vragen
description: Antwoorden op veelgestelde vragen met betrekking tot de Azure Container Instances-service
author: dkkapur
ms.topic: article
ms.date: 01/07/2020
ms.openlocfilehash: 4a3fb4c1818d86f7fe2913790fd9e573c630cbfd
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79247174"
---
# <a name="frequently-asked-questions-about-azure-container-instances"></a>Veelgestelde vragen over Azure Container Instances

In dit artikel vindt u veelgestelde vragen over Azure Container Instances.

## <a name="deployment"></a>Implementatie

### <a name="how-large-can-my-container-image-be"></a>Hoe groot kan mijn container installatie kopie?

De maximale grootte voor een Implementeer bare container installatie kopie op Azure Container Instances is 15 GB. U kunt mogelijk grotere installatie kopieën implementeren, afhankelijk van de exacte Beschik baarheid op het moment dat u implementeert, maar dit is niet gegarandeerd.

De grootte van uw container installatie kopie heeft invloed op hoe lang het duurt om te implementeren, dus doorgaans wilt u de container installatie kopieën zo klein mogelijk blijven.

### <a name="how-can-i-speed-up-the-deployment-of-my-container"></a>Hoe kan ik de implementatie van mijn container versnellen?

Omdat een van de belangrijkste determinanten van implementatie tijden de afbeeldings grootte is, zoekt u naar manieren om de grootte te verkleinen. Verwijder lagen die u niet nodig hebt of verklein de grootte van de lagen in de installatie kopie (door een lichtere installatie kopie van het basis besturingssysteem te kiezen). Als u bijvoorbeeld Linux-containers uitvoert, kunt u Alpine gebruiken als basis installatie kopie in plaats van een volledige Ubuntu-Server. Voor Windows-containers gebruikt u, indien mogelijk, een nano Server-basis installatie kopie. 

U moet ook de lijst met vooraf in cache opgeslagen installatie kopieën in azure container-installatie kopieën controleren, die beschikbaar zijn via de [lijst in de cache opgeslagen installatie kopieën](/rest/api/container-instances/listcachedimages) -API. U kunt mogelijk een afbeelding slaag uitschakelen voor een van de vooraf in cache opgeslagen installatie kopieën. 

Bekijk meer [gedetailleerde richt lijnen](container-instances-troubleshooting.md#container-takes-a-long-time-to-start) voor het beperken van de opstart tijd van de container.

### <a name="what-windows-base-os-images-are-supported"></a>Welke Windows Base OS-installatie kopieën worden ondersteund?

#### <a name="windows-server-2016-base-images"></a>Basis installatie kopieën van Windows Server 2016

* [Nano server](https://hub.docker.com/_/microsoft-windows-nanoserver): `10.0.14393.x`, `sac2016`
* [Windows Server Core](https://hub.docker.com/_/microsoft-windows-servercore): `ltsc2016`, `10.0.14393.x`

> [!NOTE]
> Windows-installatie kopieën op basis van een semi-Annual-kanaal release 1709 of 1803 worden niet ondersteund.

#### <a name="windows-server-2019-and-client-base-images-preview"></a>Windows Server 2019 en client basis installatie kopieën (preview-versie)

* [Nano server](https://hub.docker.com/_/microsoft-windows-nanoserver): `1809`, `10.0.17763.x`
* [Windows Server Core](https://hub.docker.com/_/microsoft-windows-servercore): `ltsc2019`, `1809`, `10.0.17763.x`
* [Windows](https://hub.docker.com/_/microsoft-windows): `1809`, `10.0.17763.x` 

### <a name="what-net-or-net-core-image-layer-should-i-use-in-my-container"></a>Welke .NET-of .NET Core-installatie kopie laag moet ik gebruiken in mijn container? 

Gebruik de kleinste afbeelding die voldoet aan uw vereisten. Voor Linux kunt u een installatie kopie voor *runtime-Alpine* .net core gebruiken, die wordt ondersteund sinds de release van .net Core 2,1. Als u voor Windows gebruikmaakt van de volledige .NET Framework, moet u een installatie kopie van Windows Server Core gebruiken (alleen runtime-installatie kopie, zoals *4.7.2-windowsservercore-ltsc2016*). Installatie kopieën met alleen runtime zijn kleiner, maar ondersteunen geen workloads waarvoor de .NET SDK is vereist.

## <a name="availability-and-quotas"></a>Beschik baarheid en quota's

### <a name="how-many-cores-and-memory-should-i-allocate-for-my-containers-or-the-container-group"></a>Hoeveel kernen en geheugen moet ik toewijzen voor mijn containers of de container groep?

Dit is echt afhankelijk van uw werk belasting. Start kleine en test prestaties om te zien hoe uw containers doen. [Bewaak het gebruik van CPU-en geheugen bronnen](container-instances-monitor.md)en voeg vervolgens kernen of geheugen toe op basis van het soort processen dat u in de container implementeert. 

Zorg ervoor dat u ook de [Beschik baarheid van resources](container-instances-region-availability.md#availability---general) controleert voor de regio die u implementeert in voor de bovengrens op CPU-kernen en geheugen beschikbaar per container groep. 

### <a name="what-underlying-infrastructure-does-aci-run-on"></a>Op welke onderliggende infra structuur wordt ACI uitgevoerd?

Azure Container Instances streeft naar een serverloze containers-on-demand service, dus we willen u graag richten op het ontwikkelen van uw containers en geen zorgen over de infra structuur. Voor degenen die op het werk zijn of willen werken met de prestaties, wordt ACI uitgevoerd op sets van Azure-Vm's van verschillende Sku's, voornamelijk uit de F en de D-serie. We verwachten dat dit in de toekomst wordt gewijzigd, omdat we de service blijven ontwikkelen en optimaliseren. 

### <a name="i-want-to-deploy-thousand-of-cores-on-aci---can-i-get-my-quota-increased"></a>Ik wil duizenden kern geheugens implementeren op ACI: kan ik mijn quota verhogen?
 
Ja (soms). Zie het artikel [quota's en beperkingen](container-instances-quotas.md) voor huidige quota's en de limieten kunnen worden verhoogd met behulp van aanvragen.

### <a name="can-i-deploy-with-more-than-4-cores-and-16-gb-of-ram"></a>Kan ik met meer dan 4 kernen en 16 GB aan RAM-geheugen implementeren?

Nog niet. Dit zijn momenteel de maximum waarden voor een container groep. Neem contact op met de ondersteuning van Azure met specifieke vereisten of aanvragen. 

### <a name="when-will-aci-be-in-a-specific-region"></a>Wanneer neemt de berekenings bevinden zich in een specifieke regio?

De huidige Beschik baarheid van de regio wordt [hier](container-instances-region-availability.md#availability---general)gepubliceerd. Als u een vereiste hebt voor een bepaalde regio, neemt u contact op met de ondersteuning van Azure.

## <a name="features-and-scenarios"></a>Functies en scenario's

### <a name="how-do-i-scale-a-container-group"></a>Hoe kan ik een container groep schalen?

Schalen is momenteel niet beschikbaar voor containers of container groepen. Als u meer exemplaren wilt uitvoeren, gebruikt u onze API om meer aanvragen voor het maken van een container groep te automatiseren en te maken met de service. 

### <a name="what-features-are-available-to-instances-running-in-a-custom-vnet"></a>Welke functies zijn beschikbaar voor instanties die worden uitgevoerd in een aangepast VNet?

U kunt [container groepen implementeren in een virtueel Azure-netwerk](container-instances-vnet.md) van uw keuze, en privé ip's delegeren aan de container groepen om verkeer tussen uw Azure-resources te routeren binnen het VNet. Implementatie van een container groep in een virtueel netwerk is momenteel beschikbaar voor productie werkbelastingen in een subset van Azure-regio's.

## <a name="pricing"></a>Prijzen

### <a name="when-does-the-meter-start-running"></a>Wanneer wordt de meter gestart?

De duur van de container groep wordt berekend op basis van het tijdstip waarop de installatie kopie van de eerste container wordt opgehaald (voor een nieuwe implementatie) of de container groep wordt opnieuw opgestart (indien al geïmplementeerd) totdat de container groep is gestopt. Bekijk de details op [container instances prijzen](https://azure.microsoft.com/pricing/details/container-instances/).

### <a name="do-i-stop-being-charged-when-my-containers-are-stopped"></a>Worden er geen kosten in rekening gebracht wanneer mijn containers worden gestopt?

Meters worden niet meer uitgevoerd wanneer de hele container groep is gestopt. Zolang er een container in uw container groep wordt uitgevoerd, bevatten we de resources voor het geval u de containers opnieuw wilt starten. 

## <a name="next-steps"></a>Volgende stappen

* Meer [informatie](container-instances-overview.md) over Azure container instances.
* [Veelvoorkomende problemen](container-instances-troubleshooting.md) in azure container instances oplossen.