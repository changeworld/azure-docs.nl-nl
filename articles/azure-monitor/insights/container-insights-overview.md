---
title: Overzicht van Azure Monitor voor containers | Microsoft Docs
description: Dit artikel wordt beschreven van Azure Monitor voor containers die bewaakt AKS Container Insights-oplossing en de waarde die het biedt een door de bewaking van de status van uw AKS-clusters en exemplaren van de Container in Azure.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 3ff2c35ae9f5838447ce90e2a020649427920a43
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79275228"
---
# <a name="azure-monitor-for-containers-overview"></a>Azure Monitor voor containers: overzicht

Azure Monitor voor containers is een functie die is ontworpen voor het bewaken van de prestaties van container werkbelastingen die zijn geïmplementeerd op:

- Managed Kubernetes-clusters die worden gehost op de [Azure Kubernetes-service (AKS)](../../aks/intro-kubernetes.md)
- Zelf beheerde Kubernetes-clusters die worden gehost op Azure met behulp van de [AKS-engine](https://github.com/Azure/aks-engine)
- [Azure Container Instances](../../container-instances/container-instances-overview.md)
- Zelf beheerde Kubernetes-clusters die worden gehost op [Azure stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1910) of on-premises
- [Azure Red Hat open Shift](../../openshift/intro-openshift.md)

Azure Monitor voor containers ondersteunt clusters met het besturings systeem Linux en Windows Server 2019. 

Controle van uw containers is kritiek, met name wanneer u een productiecluster op schaal, met meerdere toepassingen uitvoert.

Azure Monitor voor containers biedt u de zichtbaarheid van de prestaties door verzamelen geheugen en processors metrische gegevens van domeincontrollers, knooppunten en containers die beschikbaar in Kubernetes via de API voor metrische gegevens zijn. Er worden ook containerlogboeken verzameld.  Nadat u bewaking vanuit Kubernetes-clusters hebt ingeschakeld, worden metrische gegevens en logboeken automatisch voor u verzameld via een container versie van de Log Analytics-agent voor Linux. Metrische gegevens worden naar de metrische opslag opgeslagen en logboeken worden geschreven naar de logboeken archief die aan uw [log Analytics](../log-query/log-query-overview.md) -werk ruimte is gekoppeld. 

![Azure Monitor voor de architectuur van containers](./media/container-insights-overview/azmon-containers-architecture-01.png)
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>Wat kost Azure Monitor voor containers bieden?

Azure Monitor voor containers biedt een uitgebreide bewakings ervaring met behulp van verschillende functies van Azure Monitor. Met deze functies kunt u inzicht krijgen in de prestaties en status van uw Kubernetes-cluster met Linux-en Windows Server 2019-besturings systeem en de container werkbelastingen. Met Azure Monitor voor containers kunt u het volgende doen:

* Identificeer de AKS-containers die worden uitgevoerd op het knooppunt en het gemiddelde gebruik van de processor en geheugen. Aan de hand van deze kennis kunt u knelpunten in de resource.
* Identificeer de processor en geheugen gebruik van groepen met containers en de containers die worden gehost in Azure Container Instances.  
* Bepaal waar de container zich bevindt in een controller of een pod. Aan de hand van deze kennis kunt u de algehele prestaties van de van de domeincontroller of de schil weergeven. 
* Controleer het brongebruik van workloads die worden uitgevoerd op de host die niet gerelateerd zijn aan de standaard processen die ondersteuning bieden voor de schil.
* Begrijp het gedrag van het cluster bij gemiddelde en zwaarste belasting. Deze kennis kunt u identificeren behoeften aan capaciteit en bepaal de maximale belasting van het cluster kan tolereren. 
* Configureer waarschuwingen om u proactief te informeren of op te nemen wanneer het CPU-en geheugen gebruik op knoop punten of containers de drempel waarden overschrijdt, of wanneer er een status wijziging in het cluster optreedt bij het samen vouwen van de infra structuur of de knooppunt status.
* Integreer met [Prometheus](https://prometheus.io/docs/introduction/overview/) om de metrische gegevens van de toepassing en werk belasting weer te geven die worden verzameld van knoop punten en Kubernetes met behulp van [query's](container-insights-log-search.md) om aangepaste waarschuwingen, Dash boards en gedetailleerde gedetailleerde analyses te maken.
* Bewaak de werkbelastingen van containers [die zijn geïmplementeerd](https://github.com/Azure/aks-engine) op de AKS-engine on-premises en AKS- [engine op Azure stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908).
* Bewaak de werk belasting van containers [die zijn geïmplementeerd in azure Red Hat open Shift](../../openshift/intro-openshift.md).

    >[!NOTE]
    >De ondersteuning voor Azure Red Hat open Shift is op dit moment een functie in Public Preview.
    >

De belangrijkste verschillen in het bewaken van een Windows Server-cluster in vergelijking met een Linux-cluster zijn als volgt:

- De metrische gegevens van het geheugen zijn niet beschikbaar voor Windows-knoop punten en-containers.
- Informatie over capaciteit van schijf opslag is niet beschikbaar voor Windows-knoop punten.
- Container logboeken zijn niet beschikbaar voor containers die worden uitgevoerd in Windows-knoop punten.
- Ondersteuning voor de functie Live data (preview) is beschikbaar in de uitzonde ring van Windows-container Logboeken.
- Alleen pod omgevingen worden bewaakt, niet-docker-omgevingen.
- Met de preview-versie worden Maxi maal 30 Windows Server-containers ondersteund. Deze beperking is niet van toepassing op Linux-containers. 

Bekijk de volgende video over een dieper niveau waarmee u meer informatie kunt over het bewaken van uw AKS-cluster met Azure Monitor voor containers.

> [!VIDEO https://www.youtube.com/embed/RjsNmapggPU]

## <a name="how-do-i-access-this-feature"></a>Hoe krijg ik toegang tot deze functie?

U kunt toegang tot Azure Monitor voor twee manieren van Azure Monitor of rechtstreeks vanuit het geselecteerde AKS-cluster-containers. Vanuit Azure Monitor hebt u een wereld wijd perspectief van alle geïmplementeerde containers, die worden gecontroleerd en die niet, zodat u uw abonnementen en resource groepen kunt doorzoeken en filteren, en vervolgens inzoomen op Azure Monitor voor containers van de geselecteerde container.  Als dat niet het geval is, kunt u de functie rechtstreeks vanuit een geselecteerde AKS-container openen via de pagina AKS.  

![Overzicht van methoden voor toegang tot Azure Monitor voor containers](./media/container-insights-overview/azmon-containers-experience.png)

Als u geïnteresseerd bent in het bewaken en beheren van uw docker-en Windows-container hosts die buiten AKS worden uitgevoerd om de configuratie, controle en het gebruik van bronnen weer te geven, raadpleegt u de [container bewakings oplossing](../../azure-monitor/insights/containers.md).

## <a name="next-steps"></a>Volgende stappen

Als u wilt beginnen met het bewaken van uw Kubernetes-cluster, raadpleegt u [How to Enable the Azure monitor for containers](container-insights-onboard.md) voor meer informatie over de vereisten en beschik bare methoden voor het inschakelen van 
