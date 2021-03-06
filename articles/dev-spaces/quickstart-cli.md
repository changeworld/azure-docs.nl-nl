---
title: Een toepassing ontwikkelen op Kubernetes
services: azure-dev-spaces
ms.date: 02/20/2020
ms.topic: quickstart
description: In deze Quick Start leert u hoe u Azure dev Spaces en de opdracht regel gebruikt voor het ontwikkelen van een toepassing op de Azure Kubernetes-service
keywords: Docker, Kubernetes, azure, AKS, Azure Kubernetes service, containers, helm, service-net, service mesh routing, kubectl, K8S
manager: gwallace
ms.openlocfilehash: 974f0039bb16dc685bb056e279df63933e358edd
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78245254"
---
# <a name="quickstart-develop-an-application-on-kubernetes---azure-dev-spaces"></a>Quick Start: een toepassing ontwikkelen op Kubernetes-Azure dev Spaces
In deze handleiding leert u het volgende:

- Azure Dev Spaces instellen met een beheerd Kubernetes-cluster in Azure.
- Code in containers ontwikkelen en uitvoeren met behulp van de opdracht regel.

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Als u geen abonnement op Azure hebt, kunt u een [gratis account](https://azure.microsoft.com/free) maken.
- [Azure CLI geïnstalleerd](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Een Azure Kubernetes service-cluster maken

U moet een AKS-cluster maken in een [ondersteunde regio][supported-regions]. Met de onderstaande opdrachten maakt u een resource groep met de naam *MyResourceGroup* en een AKS-cluster met de naam *MyAKS*.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Azure dev Spaces inschakelen op uw AKS-cluster

Gebruik de `use-dev-spaces` opdracht om ontwikkel ruimten in uw AKS-cluster in te scha kelen en de prompts te volgen. Met de onderstaande opdracht maakt u ontwikkel ruimten in het *MyAKS* -cluster in de groep *MyResourceGroup* en maakt u een *standaard* dev-ruimte.

> [!NOTE]
> De `use-dev-spaces`-opdracht installeert ook de Azure dev Space CLI als deze nog niet is geïnstalleerd. U kunt de CLI voor Azure dev Spaces niet installeren in de Azure Cloud Shell.

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

```output
'An Azure Dev Spaces Controller' will be created that targets resource 'MyAKS' in resource group 'MyResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'MyAKS' in resource group 'MyResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

## <a name="get-sample-application-code"></a>Voorbeeld toepassings code ophalen

In dit artikel gebruikt u de [voorbeeld toepassing Azure dev Spaces](https://github.com/Azure/dev-spaces) om te demonstreren hoe u Azure dev Spaces gebruikt.

Kloon de toepassing van GitHub en navigeer naar de map *dev-Spaces/samples/nodejs/Getting-Started/Webfront-front-front-end* :

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="prepare-the-application"></a>De toepassing voorbereiden

Als u uw toepassing wilt uitvoeren op Azure dev Spaces, hebt u een Dockerfile-en helm-grafiek nodig. Voor sommige talen, zoals [Java][java-quickstart], [.net core][netcore-quickstart]en [node. js][nodejs-quickstart], kunnen met het client hulpprogramma van Azure dev Spaces alle benodigde assets worden gegenereerd. Voor veel andere talen, zoals go, PHP en Python, kan de-client hulpprogramma het helm-diagram genereren, mits u een geldige Dockerfile kunt opgeven.

Genereer de activa docker-en helm-grafiek voor het uitvoeren van de toepassing in Kubernetes met behulp van de `azds prep` opdracht:

```cmd
azds prep --enable-ingress
```

U moet de `prep`-opdracht uitvoeren vanuit de map *dev-Spaces/samples/nodejs/Getting-Started/webfrontend* om de docker-en helm-grafiek elementen correct te genereren.

> [!TIP]
> Met de `prep` opdracht wordt geprobeerd [een Dockerfile-en helm-grafiek](how-dev-spaces-works.md#prepare-your-code) te genereren voor uw project. Azure dev Spaces gebruiken deze bestanden om uw code te bouwen en uit te voeren, maar u kunt deze bestanden wijzigen als u wilt wijzigen hoe het project wordt gemaakt en uitgevoerd.

## <a name="build-and-run-code-in-kubernetes"></a>Code schrijven en uitvoeren in Kubernetes

Bouw en voer uw code in AKS met behulp van de `azds up` opdracht:

```cmd
$ azds up
Using dev space 'default' with target 'MyAKS'
Synchronizing files...2s
Installing Helm chart...2s
Waiting for container image build...2m 25s
Building container image...
Step 1/8 : FROM node
Step 2/8 : ENV PORT 80
Step 3/8 : EXPOSE 80
Step 4/8 : WORKDIR /app
Step 5/8 : COPY package.json .
Step 6/8 : RUN npm install
Step 7/8 : COPY . .
Step 8/8 : CMD ["npm", "start"]
Built container image in 6m 17s
Waiting for container...13s
Service 'webfrontend' port 'http' is available at http://webfrontend.1234567890abcdef1234.eus.azds.io/
Service 'webfrontend' port 80 (http) is available at http://localhost:54256
...
```

U ziet de service die wordt uitgevoerd door de open bare URL te openen, die wordt weer gegeven in de uitvoer van de `azds up` opdracht. In dit voor beeld is de open bare URL *http://webfrontend.1234567890abcdef1234.eus.azds.io/* .

> [!NOTE]
> Wanneer u naar uw service navigeert tijdens het uitvoeren van `azds up`, worden de traceringen van de HTTP-aanvraag ook weer gegeven in de uitvoer van de `azds up` opdracht. Deze traceringen kunnen u helpen bij het oplossen van problemen met uw service en fout opsporing. U kunt deze traceringen uitschakelen met behulp van `--disable-http-traces` wanneer u `azds up`uitvoert.

Als u de `azds up` opdracht met *CTRL + c*stopt, wordt de service nog steeds uitgevoerd in AKS en blijft de open bare URL beschikbaar.

## <a name="update-code"></a>Code bijwerken

Als u een bijgewerkte versie van uw service wilt implementeren, kunt u elk bestand in het project bijwerken en de `azds up` opdracht opnieuw uitvoeren. Bijvoorbeeld:

1. Als `azds up` nog steeds wordt uitgevoerd, drukt u op *CTRL + c*.
1. Werk [regel 13 in `server.js`](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/server.js#L13) bij naar:
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
    ```

1. Sla uw wijzigingen op.
1. Voer de `azds up` opdracht opnieuw uit:

    ```cmd
    $ azds up
    Using dev space 'default' with target 'MyAKS'
    Synchronizing files...1s
    Installing Helm chart...3s
    Waiting for container image build...
    ...    
    ```

1. Navigeer naar uw actieve service en Bekijk uw wijzigingen.
1. Druk op *CTRL + c* om de `azds up` opdracht te stoppen.

## <a name="clean-up-your-azure-resources"></a>Uw Azure-resources opschonen

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe Azure dev Spaces u helpt om complexere toepassingen te ontwikkelen in meerdere containers en hoe u samenwerkings ontwikkeling kunt vereenvoudigen door te werken met verschillende versies of vertakkingen van uw code in verschillende ruimten.

> [!div class="nextstepaction"]
> [Team ontwikkeling in azure dev Spaces][team-quickstart]

[java-quickstart]: quickstart-java.md
[nodejs-quickstart]: quickstart-nodejs.md
[netcore-quickstart]: quickstart-netcore.md
[team-quickstart]: quickstart-team-development.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service