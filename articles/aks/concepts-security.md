---
title: Concepten-beveiliging in azure Kubernetes Services (AKS)
description: Meer informatie over beveiliging in azure Kubernetes service (AKS), met inbegrip van Master-en knooppunt communicatie, netwerk beleid en Kubernetes geheimen.
services: container-service
ms.topic: conceptual
ms.date: 03/01/2019
ms.openlocfilehash: 7238e6cd7ab3625e2953a4408c82802d43372256
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77595940"
---
# <a name="security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks"></a>Beveiligings concepten voor toepassingen en clusters in azure Kubernetes service (AKS)

Ter bescherming van uw klant gegevens bij het uitvoeren van werk belastingen van toepassingen in azure Kubernetes service (AKS), is de beveiliging van uw cluster een belang rijke overweging. Kubernetes bevat beveiligings onderdelen, zoals *netwerk beleid* en *geheimen*. Azure wordt vervolgens toegevoegd aan onderdelen, zoals netwerk beveiligings groepen en gegroepeerde cluster upgrades. Deze beveiligings onderdelen worden gecombineerd om uw AKS-cluster uit te voeren met de meest recente beveiligings updates van het besturings systeem en Kubernetes-releases, en met beveiligd pod-verkeer en toegang tot gevoelige referenties.

In dit artikel worden de belangrijkste concepten geïntroduceerd voor het beveiligen van uw toepassingen in AKS:

- [Beveiliging van hoofd onderdelen](#master-security)
- [Knooppunt beveiliging](#node-security)
- [Cluster upgrades](#cluster-upgrades)
- [Netwerkbeveiliging](#network-security)
- [Kubernetes geheimen](#kubernetes-secrets)

## <a name="master-security"></a>Master beveiliging

In AKS maken de Kubernetes-hoofd onderdelen deel uit van de beheerde service van micro soft. Elk AKS-cluster heeft een eigen Kubernetes-Master met één Tenant om de API-server, scheduler, enzovoort te bieden. Dit model wordt beheerd en onderhouden door micro soft.

De Kubernetes API-server gebruikt standaard een openbaar IP-adres en een Fully Qualified Domain Name (FQDN). U kunt de toegang tot de API-server beheren met behulp van Kubernetes met toegangs beheer op basis van rollen en Azure Active Directory. Zie [Azure AD-integratie met AKS][aks-aad]voor meer informatie.

## <a name="node-security"></a>Knooppunt beveiliging

AKS-knoop punten zijn virtuele Azure-machines die u beheert en onderhoudt. Linux-knoop punten voeren een geoptimaliseerde Ubuntu-distributie uit met behulp van de Moby container runtime. Windows Server-knoop punten (momenteel in de preview-versie van AKS) voert u een geoptimaliseerde versie van Windows Server 2019 uit en gebruikt u ook de Moby container runtime. Wanneer een AKS-cluster wordt gemaakt of geschaald, worden de knoop punten automatisch geïmplementeerd met de meest recente beveiligings updates en-configuraties van het besturings systeem.

Het Azure-platform past automatisch de beveiligings patches van het besturings systeem op een nacht in op Linux-knoop punten. Als voor een Linux-beveiligings update een host opnieuw moet worden opgestart, wordt dat opnieuw opstarten niet automatisch uitgevoerd. U kunt de Linux-knoop punten hand matig opnieuw opstarten of een gemeen schappelijke aanpak gebruiken [Kured][kured], een open source-daemon voor opnieuw opstarten voor Kubernetes. Kured wordt uitgevoerd als een [daemonset][aks-daemonsets] en bewaakt elk knoop punt voor de aanwezigheid van een bestand dat aangeeft dat de computer opnieuw moet worden opgestart. Opnieuw opstarten wordt via het cluster beheerd met hetzelfde Cordon- [en afvoer proces](#cordon-and-drain) als een cluster upgrade.

Voor Windows Server-knoop punten (momenteel in de preview-versie van AKS) wordt Windows Update niet automatisch uitgevoerd en worden de nieuwste updates toegepast. U moet een upgrade uitvoeren op de Windows Server-knooppunt groep (en) in uw AKS-cluster, volgens een regel matige planning rond de Windows Update release cyclus en uw eigen validatie proces. Dit upgrade proces maakt knoop punten waarop de nieuwste installatie kopie en patches van Windows Server worden uitgevoerd, waarna de oudere knoop punten worden verwijderd. Zie [een knooppunt groep bijwerken in AKS][nodepool-upgrade]voor meer informatie over dit proces.

Knoop punten worden geïmplementeerd in een particulier subnet van een virtueel netwerk, waaraan geen open bare IP-adressen zijn toegewezen. Voor het oplossen van problemen en beheer doeleinden is SSH standaard ingeschakeld. Deze SSH-toegang is alleen beschikbaar via het interne IP-adres.

Om opslag te bieden, gebruiken de knoop punten Azure Managed Disks. Voor de meeste VM-knooppunt grootten zijn dit Premium-schijven die worden ondersteund door Ssd's met hoge prestaties. De gegevens die op Managed disks zijn opgeslagen, worden automatisch versleuteld in het Azure-platform. Om redundantie te verbeteren, worden deze schijven ook veilig gerepliceerd in het Azure-Data Center.

Kubernetes-omgevingen, in AKS of elders, zijn momenteel niet volledig veilig voor het gebruik van meerdere tenants. Aanvullende beveiligings functies, zoals *pod-beveiligings beleid* of meer verfijnde, op rollen gebaseerde toegangs beheer (RBAC) voor knoop punten maken aanvallen moeilijker. Voor echte beveiliging bij het uitvoeren van vijandelijke multi tenant-workloads is een Hyper Visor echter het enige beveiligings niveau dat u moet vertrouwen. Het beveiligings domein voor Kubernetes wordt het hele cluster, niet een afzonderlijk knoop punt. Voor dit soort vijandelijke multi tenant-workloads moet u fysiek geïsoleerde clusters gebruiken. Zie [Aanbevolen procedures voor cluster isolatie in AKS][cluster-isolation]voor meer informatie over manieren om workloads te isoleren.

## <a name="cluster-upgrades"></a>Cluster upgrades

Voor beveiliging en naleving, of voor het gebruik van de nieuwste functies, biedt Azure hulpprogram ma's voor het organiseren van de upgrade van een AKS-cluster en-onderdelen. Deze upgrade-indeling omvat zowel de Kubernetes-Master als de agent onderdelen. U kunt een [lijst met beschik bare Kubernetes-versies](supported-kubernetes-versions.md) voor uw AKS-cluster weer geven. Als u het upgrade proces wilt starten, geeft u een van deze beschik bare versies op. In azure wordt elk AKS-knoop punt veilig cordons en vertraagd en wordt de upgrade uitgevoerd.

### <a name="cordon-and-drain"></a>Cordon en afvoer

Tijdens het upgrade proces worden AKS-knoop punten afzonderlijk afgebakend van het cluster, zodat er geen nieuwe bestanden meer kunnen worden gepland. De knoop punten worden vervolgens als volgt geleegd en geüpgraded:

- Er wordt een nieuw knoop punt in de knooppunt groep geïmplementeerd. Dit knoop punt voert de meest recente installatie kopie van het besturings systeem en patches uit.
- Een van de bestaande knoop punten wordt geïdentificeerd voor de upgrade. Het Peul op dit knoop punt wordt op de juiste wijze beëindigd en gepland op de andere knoop punten in de knooppunt groep.
- Dit bestaande knoop punt wordt uit het AKS-cluster verwijderd.
- Het volgende knoop punt in het cluster is afgebakend en verwerkt met hetzelfde proces totdat alle knoop punten zijn vervangen als onderdeel van het upgrade proces.

Zie [een AKS-cluster upgraden][aks-upgrade-cluster]voor meer informatie.

## <a name="network-security"></a>Netwerkbeveiliging

Voor connectiviteit en beveiliging met on-premises netwerken kunt u uw AKS-cluster implementeren in bestaande subnetten van het virtuele Azure-netwerk. Deze virtuele netwerken kunnen een Azure site-naar-site VPN-of Express route-verbinding weer naar uw on-premises netwerk hebben. Kubernetes ingress-controllers kunnen worden gedefinieerd met persoonlijke, interne IP-adressen, zodat services alleen toegankelijk zijn via deze interne netwerk verbinding.

### <a name="azure-network-security-groups"></a>Azure-netwerk beveiligings groepen

Azure gebruikt regels voor netwerk beveiligings groepen om de stroom van verkeer in virtuele netwerken te filteren. Met deze regels worden de bron-en doel-IP-adresbereiken, poorten en protocollen gedefinieerd die toegang tot bronnen toestaan of weigeren. Standaard regels worden gemaakt om TLS-verkeer toe te staan voor de Kubernetes-API-server. Bij het maken van services met load balancers, poort toewijzingen of ingangs routes, wijzigt AKS automatisch de netwerk beveiligings groep voor verkeer dat op de juiste wijze stroomt.

## <a name="kubernetes-secrets"></a>Kubernetes Secrets

Een Kubernetes- *geheim* wordt gebruikt voor het injecteren van gevoelige gegevens in een Peul, zoals toegangs referenties of sleutels. U maakt eerst een geheim met behulp van de Kubernetes-API. Wanneer u uw Pod of implementatie definieert, kan een specifiek geheim worden aangevraagd. Geheimen worden alleen door gegeven aan knoop punten met een geplande pod waarvoor deze zijn vereist en het geheim wordt opgeslagen in *tmpfs*, niet naar de schijf geschreven. Wanneer de laatste pod op een knoop punt dat een geheim vereist, wordt verwijderd, wordt het geheim verwijderd uit de tmpfs van het knoop punt. Geheimen worden opgeslagen in een opgegeven naam ruimte en kunnen alleen worden gebruikt door een Peul binnen dezelfde naam ruimte.

Het gebruik van geheimen vermindert de gevoelige informatie die is gedefinieerd in het Pod-of service YAML-manifest. In plaats daarvan vraagt u het geheim op dat is opgeslagen in de Kubernetes API-server als onderdeel van uw YAML-manifest. Deze aanpak biedt alleen de specifieke pod toegang tot het geheim. Opmerking: de onbewerkte geheime manifest bestanden bevatten de geheime gegevens in Base64-indeling (Zie de [officiële documentatie][secret-risks] voor meer informatie). Dit bestand moet daarom worden behandeld als gevoelige informatie en nooit worden doorgevoerd in broncode beheer.

## <a name="next-steps"></a>Volgende stappen

Zie [een AKS-cluster upgraden][aks-upgrade-cluster]om aan de slag te gaan met het beveiligen van uw AKS-clusters.

Zie [Aanbevolen procedures voor het beveiligen van clusters en upgrades in AKS][operator-best-practices-cluster-security] en [Aanbevolen procedures voor pod Security in AKS][developer-best-practices-pod-security]voor de bijbehorende aanbevolen procedures.

Raadpleeg de volgende artikelen voor meer informatie over de belangrijkste Kubernetes-en AKS-concepten:

- [Kubernetes/AKS-clusters en-workloads][aks-concepts-clusters-workloads]
- [Kubernetes/AKS-identiteit][aks-concepts-identity]
- [Kubernetes/AKS virtuele netwerken][aks-concepts-network]
- [Kubernetes/AKS-opslag][aks-concepts-storage]
- [Kubernetes/AKS-schaal][aks-concepts-scale]

<!-- LINKS - External -->
[kured]: https://github.com/weaveworks/kured
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[secret-risks]: https://kubernetes.io/docs/concepts/configuration/secret/#risks

<!-- LINKS - Internal -->
[aks-daemonsets]: concepts-clusters-workloads.md#daemonsets
[aks-upgrade-cluster]: upgrade-cluster.md
[aks-aad]: azure-ad-integration.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[cluster-isolation]: operator-best-practices-cluster-isolation.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[developer-best-practices-pod-security]:developer-best-practices-pod-security.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
