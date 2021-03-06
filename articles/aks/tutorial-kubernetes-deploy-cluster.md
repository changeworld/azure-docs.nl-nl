---
title: 'Zelfstudie voor Kubernetes in Azure: Een cluster implementeren'
description: In deze zelfstudie Azure Kubernetes Service (AKS) gaat u een AKS-cluster maken en kubectl gebruiken om verbinding te maken met het hoofdknooppunt van Kubernetes.
services: container-service
ms.topic: tutorial
ms.date: 02/25/2020
ms.custom: mvc
ms.openlocfilehash: bc31a4197b08cbeb1a99820d7ff490f20147c7bf
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78191262"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>Zelfstudie: Een AKS-cluster (Azure Kubernetes Service) implementeren

Kubernetes biedt een gedistribueerd platform voor toepassingen in containers. Met AKS kunt u snel een productieklaar Kubernetes-cluster maken. In deze zelfstudie, deel drie van de zeven, wordt een Kubernetes-cluster geïmplementeerd in AKS. In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Een Kubernetes AKS-cluster implementeren dat kan worden geverifieerd bij een Azure container Registry
> * De Kubernetes-CLI (kubectl) installeren
> * Kubectl configureren om verbinding te maken met uw AKS-cluster

In volgende zelfstudies wordt de Azure Vote-toepassing geïmplementeerd in het cluster en vervolgens geschaald en bijgewerkt.

## <a name="before-you-begin"></a>Voordat u begint

In de vorige zelfstudies is een containerinstallatiekopie gemaakt en geüpload naar een Azure Container Registry-exemplaar. Als u deze stappen niet hebt uitgevoerd en u wilt door gaan met de [zelf studie 1: container installatie kopieën maken][aks-tutorial-prepare-app].

Voor deze zelf studie moet u de Azure CLI-versie 2.0.75 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli-install] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="create-a-kubernetes-cluster"></a>Een Kubernetes-cluster maken

AKS-clusters kunnen gebruikmaken van op rollen gebaseerd toegangsbeheer (RBAC) van Kubernetes. Met deze vorm van toegangsbeheer kunt u de toegang tot resources definiëren op basis van rollen die zijn toegewezen aan gebruikers. Machtigingen worden gecombineerd als aan een gebruiker meerdere rollen zijn toegewezen, en machtigingen kunnen gelden voor één enkele naamruimte of voor een heel cluster. Standaard schakelt de Azure CLI automatisch RBAC in wanneer u een AKS-cluster maakt.

Maak een AKS-cluster met behulp van [az aks create][]. In het volgende voorbeeld wordt een cluster met de naam *myAKSCluste* gemaakt in de resourcegroep met de naam *myResourceGroup*. Deze resource groep is gemaakt in de [vorige zelf studie][aks-tutorial-prepare-acr]. Als u een AKS-cluster wilt toestaan om te communiceren met andere Azure-resources, wordt er automatisch een Azure Active Directory Service-Principal gemaakt, omdat u er geen hebt opgegeven. Deze service-principal krijgt hier [het recht om installatie kopieën te halen][container-registry-integration] uit het Azure container Registry-exemplaar (ACR) dat u in de vorige zelf studie hebt gemaakt.

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --generate-ssh-keys \
    --attach-acr <acrName>
```

U kunt ook hand matig een Service-Principal configureren om installatie kopieën van ACR te halen. Zie [ACR-verificatie met Service-principals](../container-registry/container-registry-auth-service-principal.md) of verificatie [van Kubernetes met een pull-geheim](../container-registry/container-registry-auth-kubernetes.md)voor meer informatie.

Na enkele minuten is de implementatie voltooid en retourneert JSON opgemaakte informatie over de AKS-implementatie.

> [!NOTE]
> Om ervoor te zorgen dat uw cluster betrouwbaar functioneert, moet u ten minste twee knoop punten uitvoeren.

## <a name="install-the-kubernetes-cli"></a>De Kubernetes-CLI installeren

Als u verbinding wilt maken met het Kubernetes-cluster vanaf uw lokale computer, gebruikt u [kubectl][kubectl], de Kubernetes-opdracht regel-client.

Als u Azure Cloud Shell gebruikt, is `kubectl` al geïnstalleerd. Als u het lokaal wilt installeren, gebruikt u de opdracht [az aks install-cli][]:

```azurecli
az aks install-cli
```

## <a name="connect-to-cluster-using-kubectl"></a>Verbinding maken met cluster met behulp van kubectl

Gebruik de opdracht `kubectl`az aks get-credentials[ om ][] zodanig te configureren dat er verbinding wordt gemaakt met het Kubernetes-cluster. In het volgende voorbeeld worden de referenties voor het AKS-cluster met de naam *myAKSCluster* in *myResourceGroup* opgehaald:

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Als u de verbinding met uw cluster wilt controleren, voert u de opdracht [kubectl Get nodes][kubectl-get] uit om een lijst met cluster knooppunten te retour neren:

```
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-12345678-0   Ready    agent   32m   v1.14.8
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie is een Kubernetes-cluster geïmplementeerd in AKS, en hebt u `kubectl` geconfigureerd om er verbinding mee te maken. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een Kubernetes AKS-cluster implementeren dat kan worden geverifieerd bij een Azure container Registry
> * De Kubernetes-CLI (kubectl) installeren
> * Kubectl configureren om verbinding te maken met uw AKS-cluster

Ga door naar de volgende zelfstudie voor informatie over het implementeren van toepassingen naar het cluster.

> [!div class="nextstepaction"]
> [Toepassing implementeren in Kubernetes][aks-tutorial-deploy-app]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-tutorial-deploy-app]: ./tutorial-kubernetes-deploy-application.md
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az ad sp create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az acr show]: /cli/azure/acr#az-acr-show
[az role assignment create]: /cli/azure/role/assignment#az-role-assignment-create
[az aks create]: /cli/azure/aks#az-aks-create
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[ om ]: /cli/azure/aks#az-aks-get-credentials
[azure-cli-install]: /cli/azure/install-azure-cli
[container-registry-integration]: ./cluster-container-registry-integration.md
