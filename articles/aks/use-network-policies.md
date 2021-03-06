---
title: Beveiligt u met netwerk beleid in azure Kubernetes service (AKS)
description: Meer informatie over het beveiligen van verkeer dat in en uit het meren aantal stromen met behulp van Kubernetes-netwerk beleid in azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.date: 05/06/2019
ms.openlocfilehash: 92e726529f2c81b169dc5ad485148ad8118bbc81
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77592863"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>Verkeer beveiligen tussen een Peul netwerk beleid dat wordt gebruikt in azure Kubernetes service (AKS)

Wanneer u moderne, op micro Services gebaseerde toepassingen uitvoert in Kubernetes, wilt u vaak bepalen welke onderdelen met elkaar kunnen communiceren. Het principe van minimale bevoegdheden moet worden toegepast op de manier waarop verkeer tussen de peulen in een Azure Kubernetes service (AKS)-cluster kan stromen. Stel dat u het verkeer waarschijnlijk rechtstreeks wilt blok keren naar back-end-toepassingen. Met de functie *netwerk beleid* in Kubernetes kunt u regels definiëren voor binnenkomend en uitgaand verkeer tussen peulen in een cluster.

In dit artikel wordt beschreven hoe u de Network Policy Engine installeert en hoe u Kubernetes-netwerk beleid maakt om de stroom van verkeer tussen peul in AKS te beheren. Netwerk beleid mag alleen worden gebruikt voor Linux-knoop punten en een Peul in AKS.

## <a name="before-you-begin"></a>Voordat u begint

U moet de Azure CLI-versie 2.0.61 of hoger hebben geïnstalleerd en geconfigureerd. Voer  `az --version` uit om de versie te bekijken. Als u wilt installeren of upgraden, raadpleegt u [Azure cli installeren][install-azure-cli].

> [!TIP]
> Als u de functie netwerk beleid hebt gebruikt tijdens de preview-versie, wordt u aangeraden [een nieuw cluster te maken](#create-an-aks-cluster-and-enable-network-policy).
> 
> Als u bestaande test clusters wilt blijven gebruiken die tijdens de preview worden gebruikt, voert u een upgrade van uw cluster uit naar een nieuwe Kubernetes-versie voor de meest recente GA-versie en implementeert u vervolgens het volgende YAML-manifest om de gegevens server voor crashes te herstellen en te Kubernetes dash board. Deze oplossing is alleen vereist voor clusters die de Calico-netwerk beleid-engine gebruiken.
>
> [Lees de inhoud van dit yaml-manifest][calico-aks-cleanup] als beveiligings best practice om te begrijpen wat er in het AKS-cluster is geïmplementeerd.
>
> `kubectl delete -f https://raw.githubusercontent.com/Azure/aks-engine/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml`

## <a name="overview-of-network-policy"></a>Overzicht van netwerk beleid

Alle peulen in een AKS-cluster kunnen standaard zonder beperkingen verkeer verzenden en ontvangen. Om de beveiliging te verbeteren, kunt u regels definiëren die de stroom van verkeer regelen. Back-end-toepassingen worden vaak alleen blootgesteld aan de vereiste front-end-services, bijvoorbeeld. Of database onderdelen zijn alleen toegankelijk voor de toepassings lagen waarmee ze verbinding maken.

Netwerk beleid is een Kubernetes-specificatie waarmee het toegangs beleid voor communicatie tussen het gehele element wordt gedefinieerd. Met netwerk beleid definieert u een geordende set regels voor het verzenden en ontvangen van verkeer en past u deze toe op een verzameling van peulen die overeenkomen met een of meer label-selectors.

Deze regels voor netwerk beleid worden gedefinieerd als YAML-manifesten. Netwerk beleid kan worden opgenomen als onderdeel van een breder manifest dat ook een implementatie of service maakt.

### <a name="network-policy-options-in-aks"></a>Opties voor netwerk beleid in AKS

Azure biedt twee manieren om netwerk beleid te implementeren. U kiest een optie voor netwerk beleid wanneer u een AKS-cluster maakt. De beleids optie kan niet worden gewijzigd nadat het cluster is gemaakt:

* De eigen implementatie van Azure, het *Azure-netwerk beleid*genoemd.
* *Calico network policies*, een open-source netwerk-en netwerk beveiligings oplossing die is gegrondvest door [tigera][tigera].

Beide implementaties gebruiken Linux *iptables* om het opgegeven beleid af te dwingen. Beleids regels worden omgezet in sets toegestane en niet-toegestane IP-paren. Deze paren worden vervolgens geprogrammeerd als IPTable-filter regels.

### <a name="differences-between-azure-and-calico-policies-and-their-capabilities"></a>Verschillen tussen Azure-en Calico-beleid en hun mogelijkheden

| Mogelijkheid                               | Azure                      | Calico                      |
|------------------------------------------|----------------------------|-----------------------------|
| Ondersteunde platforms                      | Linux                      | Linux                       |
| Ondersteunde netwerk opties             | Azure-CNI                  | Azure CNI en kubenet       |
| Compatibiliteit met Kubernetes-specificatie | Alle beleids typen die worden ondersteund |  Alle beleids typen die worden ondersteund |
| Aanvullende functies                      | None                       | Uitgebreide beleids model dat bestaat uit globaal netwerk beleid, een globaal netwerk en een host-eind punt. Zie [calicoctl User Reference][calicoctl](Engelstalig) voor meer informatie over het gebruik van de `calicoctl` CLI voor het beheren van deze uitgebreide functies. |
| Ondersteuning                                  | Ondersteund door ondersteunings-en technisch team van Azure | Ondersteuning voor Calico-community. Zie voor meer informatie over aanvullende betaalde ondersteuning [project Calico-ondersteunings opties][calico-support]. |
| Logboekregistratie                                  | Regels die zijn toegevoegd aan of verwijderd uit IPTables worden geregistreerd op elke host onder */var/log/Azure-NPM.log* | Zie [Calico-onderdeel Logboeken][calico-logs] voor meer informatie. |

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>Een AKS-cluster maken en netwerk beleid inschakelen

Voor een overzicht van het netwerk beleid in actie, gaan we maken en vervolgens uitvouwen op een beleid dat verkeers stroom definieert:

* Al het verkeer naar pod geweigerd.
* Verkeer op basis van pod-labels toestaan.
* Verkeer toestaan op basis van naam ruimte.

Eerst gaan we een AKS-cluster maken dat netwerk beleid ondersteunt. 

> [!IMPORTANT]
>
> De functie netwerk beleid kan alleen worden ingeschakeld wanneer het cluster is gemaakt. U kunt netwerk beleid niet inschakelen op een bestaand AKS-cluster.

Als u Azure-netwerk beleid wilt gebruiken, moet u de [Azure cni-invoeg toepassing][azure-cni] gebruiken en uw eigen virtuele netwerken en subnetten definiëren. Zie [geavanceerde netwerken configureren][use-advanced-networking]voor meer gedetailleerde informatie over het plannen van de vereiste subnet bereiken. Calico-netwerk beleid kan worden gebruikt met dezelfde Azure CNI-invoeg toepassing of met de Kubenet CNI-invoeg toepassing.

Het volgende voorbeeld script:

* Hiermee maakt u een virtueel netwerk en subnet.
* Hiermee maakt u een service-principal voor Azure Active Directory (Azure AD) voor gebruik met het AKS-cluster.
* Hiermee wijst u *Inzender* machtigingen toe voor de AKS-Cluster service-principal in het virtuele netwerk.
* Hiermee maakt u een AKS-cluster in het gedefinieerde virtuele netwerk en schakelt u netwerk beleid in.
    * De optie *Azure* -netwerk beleid wordt gebruikt. Als u in plaats daarvan Calico wilt gebruiken als de optie voor netwerk beleid, gebruikt u de para meter `--network-policy calico`. Opmerking: Calico kan worden gebruikt met een `--network-plugin azure` of `--network-plugin kubenet`.

Geef uw eigen beveiligde *SP_PASSWORD*op. U kunt de *RESOURCE_GROUP_NAME* en *CLUSTER_NAME* variabelen vervangen:

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup-NP
CLUSTER_NAME=myAKSCluster
LOCATION=canadaeast

# Create a resource group
az group create --name $RESOURCE_GROUP_NAME --location $LOCATION

# Create a virtual network and subnet
az network vnet create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16

# Create a service principal and read in the application ID
SP=$(az ad sp create-for-rbac --output json)
SP_ID=$(echo $SP | jq -r .appId)
SP_PASSWORD=$(echo $SP | jq -r .password)

# Wait 15 seconds to make sure that service principal has propagated
echo "Waiting for service principal to propagate..."
sleep 15

# Get the virtual network resource ID
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP_NAME --name myVnet --query id -o tsv)

# Assign the service principal Contributor permissions to the virtual network resource
az role assignment create --assignee $SP_ID --scope $VNET_ID --role Contributor

# Get the virtual network subnet resource ID
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP_NAME --vnet-name myVnet --name myAKSSubnet --query id -o tsv)

# Create the AKS cluster and specify the virtual network and service principal information
# Enable network policy by using the `--network-policy` parameter
az aks create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --generate-ssh-keys \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --network-policy azure
```

Het duurt een paar minuten om het cluster te maken. Wanneer het cluster gereed is, configureert u `kubectl` om verbinding te maken met uw Kubernetes-cluster met behulp van de opdracht [AZ AKS Get-credentials][az-aks-get-credentials] . Met deze opdracht worden referenties gedownload en wordt de Kubernetes CLI geconfigureerd voor het gebruik ervan:

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>Alle binnenkomend verkeer naar een pod weigeren

Voordat u regels definieert om specifiek netwerk verkeer toe te staan, moet u eerst een netwerk beleid maken om al het verkeer te weigeren. Dit beleid geeft u een begin punt om te beginnen met het white list van alleen het gewenste verkeer. U kunt ook duidelijk zien dat verkeer wordt verwijderd wanneer het netwerk beleid wordt toegepast.

Voor de voor beelden van toepassings omgeving en verkeers regels maakt u eerst een naam ruimte met de naam *ontwikkeling* om het voor beeld uit te voeren:

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

Maak een voor beeld van een back-end-pod die NGINX uitvoert. Deze back-end pod kan worden gebruikt voor het simuleren van een voor beeld van een back-end-webtoepassing. Maak deze pod in de *ontwikkelings* naam ruimte en open poort *80* voor webverkeer. Voorzie de Pod met *app = webapp, Role = back-end* zodat we deze in de volgende sectie met een netwerk beleid kunnen richten:

```console
kubectl run backend --image=nginx --labels app=webapp,role=backend --namespace development --expose --port 80 --generator=run-pod/v1
```

Maak een andere pod en koppel een terminal sessie om te testen of u de standaard NGINX-webpagina kunt bereiken:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Gebruik in de shell-prompt `wget` om te bevestigen dat u toegang hebt tot de standaard NGINX-webpagina:

```console
wget -qO- http://backend
```

In de volgende voorbeeld uitvoer ziet u dat de standaard NGINX-webpagina wordt geretourneerd:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Sluit de gekoppelde terminal sessie af. De test pod wordt automatisch verwijderd.

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>Een netwerk beleid maken en Toep assen

Nu u hebt bevestigd, kunt u de NGINX-webpagina van het voor beeld van de back-end pod gebruiken, een netwerk beleid maken om al het verkeer te weigeren. Maak een bestand met de naam `backend-policy.yaml` en plak het volgende YAML-manifest. Dit manifest maakt gebruik van een *podSelector* om het beleid te koppelen aan een Peul met de *app: webapp, Role: back-end* label, zoals uw voor beeld-NGINX pod. Er worden geen regels gedefinieerd bij inkomend *verkeer, waardoor*al het binnenkomende pod wordt geweigerd:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress: []
```

Pas het netwerk beleid toe met behulp van de opdracht [kubectl apply][kubectl-apply] en geef de naam van het yaml-manifest op:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>Het netwerk beleid testen


Laten we eens kijken of u de NGINX-webpagina op de back-end-pod opnieuw kunt gebruiken. Een andere test pod maken en een terminal sessie koppelen:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Gebruik in de shell-prompt `wget` om te controleren of u toegang hebt tot de standaard NGINX-webpagina. Stel deze keer een time-outwaarde in op *2* seconden. Het netwerk beleid blokkeert nu al het inkomende verkeer, zodat de pagina niet kan worden geladen, zoals wordt weer gegeven in het volgende voor beeld:

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

Sluit de gekoppelde terminal sessie af. De test pod wordt automatisch verwijderd.

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>Binnenkomend verkeer op basis van een pod label toestaan

In de vorige sectie is een back-end NGINX pod gepland en is er een netwerk beleid gemaakt om al het verkeer te weigeren. We gaan een front-end-pod maken en het netwerk beleid bijwerken zodat verkeer van de front-end kan worden toegestaan.

Werk het netwerk beleid bij om verkeer toe te staan van een Peul met de labels *app: webapp, Role: frontend* en in elke naam ruimte. Bewerk het vorige *back-yaml-* bestand en voeg *matchLabels* ingangs regels toe zodat uw manifest eruit ziet als in het volgende voor beeld:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector: {}
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

> [!NOTE]
> Dit netwerk beleid maakt gebruik van een *namespaceSelector* -en *podSelector* -element voor de ingangs regel. De syntaxis van de YAML is belang rijk voor de regels voor inkomend verkeer. In dit voor beeld moeten beide elementen overeenkomen voor de ingangs regel die moet worden toegepast. Kubernetes-versies vóór *1,12* kunnen deze elementen niet goed interpreteren en het netwerk verkeer beperken zoals verwacht. Zie [gedrag van en van selecters][policy-rules]voor meer informatie over dit gedrag.

Pas het bijgewerkte netwerk beleid toe met behulp van de opdracht [kubectl apply][kubectl-apply] en geef de naam van het yaml-manifest op:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

Een pod plannen die als *app = webapp, Role = frontend* wordt aangeduid en een terminal sessie koppelen:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

Gebruik in de shell-prompt `wget` om te controleren of u toegang hebt tot de standaard NGINX-webpagina:

```console
wget -qO- http://backend
```

Omdat de ingangs regel verkeer met de labels *app: webapp, Role: frontend*toestaat, is het verkeer van de front-end-pod toegestaan. In de volgende voorbeeld uitvoer ziet u de standaard NGINX webpagina die wordt geretourneerd:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Sluit de gekoppelde terminal sessie af. De Pod wordt automatisch verwijderd.

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>Een pod zonder een overeenkomend label testen

Het netwerk beleid staat verkeer toe van een Peul gelabelde *app: webapp, Role:* front-end, maar moet al het andere verkeer weigeren. Laten we eens kijken of een andere pod zonder die labels toegang hebben tot de back-end NGINX pod. Een andere test pod maken en een terminal sessie koppelen:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Gebruik in de shell-prompt `wget` om te controleren of u toegang hebt tot de standaard NGINX-webpagina. Het netwerk beleid blokkeert het inkomende verkeer, zodat de pagina niet kan worden geladen, zoals wordt weer gegeven in het volgende voor beeld:

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

Sluit de gekoppelde terminal sessie af. De test pod wordt automatisch verwijderd.

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>Alleen verkeer van binnen een gedefinieerde naam ruimte toestaan

In de vorige voor beelden hebt u een netwerk beleid gemaakt dat al het verkeer heeft geweigerd en vervolgens het beleid bijgewerkt zodat verkeer van Peul met een specifiek label wordt toegestaan. Een andere gang bare behoefte is om alleen verkeer binnen een bepaalde naam ruimte te beperken. Als de voor gaande voor beelden zijn voor verkeer in een *ontwikkelings* naam ruimte, moet u een netwerk beleid maken waarmee wordt voor komen dat verkeer van een andere naam ruimte, zoals *productie*, het gehele verschil bereikt.

Maak eerst een nieuwe naam ruimte voor het simuleren van een productie naam ruimte:

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

Een test pod plannen in de *productie* naam ruimte die is gelabeld als *app = webapp, Role = frontend*. Een terminal sessie koppelen:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

Gebruik in de shell-prompt `wget` om te bevestigen dat u toegang hebt tot de standaard NGINX-webpagina:

```console
wget -qO- http://backend.development
```

Omdat de labels voor de pod overeenkomen met wat momenteel is toegestaan in het netwerk beleid, wordt het verkeer toegestaan. Het netwerk beleid bekijkt niet de naam ruimten, alleen de labels pod. In de volgende voorbeeld uitvoer ziet u de standaard NGINX webpagina die wordt geretourneerd:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Sluit de gekoppelde terminal sessie af. De test pod wordt automatisch verwijderd.

```console
exit
```

### <a name="update-the-network-policy"></a>Het netwerk beleid bijwerken

Laten we de sectie *namespaceSelector* van de ingangs regel bijwerken zodat alleen verkeer binnen de *ontwikkelings* naam ruimte wordt toegestaan. Bewerk het manifest bestand *back-end-Policy. yaml* zoals wordt weer gegeven in het volgende voor beeld:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          purpose: development
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

In complexere voor beelden kunt u meerdere insluitings regels definiëren, zoals een *namespaceSelector* en een *podSelector*.

Pas het bijgewerkte netwerk beleid toe met behulp van de opdracht [kubectl apply][kubectl-apply] en geef de naam van het yaml-manifest op:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>Het bijgewerkte netwerk beleid testen

Een andere pod plannen in de *productie* naam ruimte en een terminal sessie koppelen:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

Gebruik in de shell-prompt `wget` om te zien dat het netwerk beleid nu verkeer weigert:

```console
$ wget -qO- --timeout=2 http://backend.development

wget: download timed out
```

Sluit de test pod af:

```console
exit
```

Wanneer het verkeer is geweigerd vanuit de *productie* naam ruimte, moet u een test pod weer plannen in de *ontwikkelings* naam ruimte en een terminal sessie koppelen:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

Gebruik in de shell-prompt `wget` om te zien dat het netwerk beleid het verkeer toestaat:

```console
wget -qO- http://backend
```

Verkeer is toegestaan omdat de pod in de naam ruimte is gepland die overeenkomt met wat is toegestaan in het netwerk beleid. In de volgende voorbeeld uitvoer ziet u de standaard NGINX opgehaalde webpagina:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Sluit de gekoppelde terminal sessie af. De test pod wordt automatisch verwijderd.

```console
exit
```

## <a name="clean-up-resources"></a>Resources opschonen

In dit artikel hebben we twee naam ruimten gemaakt en een netwerk beleid toegepast. Als u deze resources wilt opschonen, gebruikt u de opdracht [kubectl verwijderen][kubectl-delete] en geeft u de resource namen op:

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over netwerk bronnen [netwerk concepten voor toepassingen in azure Kubernetes service (AKS)][concepts-network].

Zie [Kubernetes network policies][kubernetes-network-policies](Engelstalig) voor meer informatie over beleid.

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[policy-rules]: https://kubernetes.io/docs/concepts/services-networking/network-policies/#behavior-of-to-and-from-selectors
[aks-github]: https://github.com/azure/aks/issues
[tigera]: https://www.tigera.io/
[calicoctl]: https://docs.projectcalico.org/v3.9/reference/calicoctl/
[calico-support]: https://www.tigera.io/tigera-products/calico/
[calico-logs]: https://docs.projectcalico.org/v3.9/maintenance/component-logs
[calico-aks-cleanup]: https://github.com/Azure/aks-engine/blob/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-advanced-networking.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
