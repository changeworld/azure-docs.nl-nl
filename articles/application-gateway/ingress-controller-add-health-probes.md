---
title: Status tests toevoegen aan uw AKS-peul
description: In dit artikel vindt u informatie over het toevoegen van status tests (gereedheid en/of bemoeilijking) voor AKS van Peul met een Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 5d0543a3a43d53e462a6406312faddf37d2653c6
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795599"
---
# <a name="add-health-probes-to-your-service"></a>Status tests toevoegen aan uw service
Standaard wordt door ingangs controller een HTTP GET-test voor het weer gegeven peul ingericht.
De eigenschappen van de test kunnen worden aangepast door een [gereedheid of een gereedheids test](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) toe te voegen aan uw `deployment`/`pod` spec.

## <a name="with-readinessprobe-or-livenessprobe"></a>Met `readinessProbe` of `livenessProbe`
```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: aspnetapp
spec:
  replicas: 3
  template:
    metadata:
      labels:
        service: site
    spec:
      containers:
      - name: aspnetapp
        image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 80
        readinessProbe:
          httpGet:
            path: /
            port: 80
          periodSeconds: 3
          timeoutSeconds: 1
```

Kubernetes API-referentie:
* [Container tests](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/#container-probes)
* [HttpGet-actie](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.14/#httpgetaction-v1-core)

> [!NOTE]
> * `readinessProbe` en `livenessProbe` worden ondersteund wanneer deze worden geconfigureerd met `httpGet`.
> * Het zoeken naar een andere poort dan die die wordt weer gegeven op de Pod wordt momenteel niet ondersteund.
> * `HttpHeaders`, `InitialDelaySeconds`, `SuccessThreshold` worden niet ondersteund.

##  <a name="without-readinessprobe-or-livenessprobe"></a>Zonder `readinessProbe` of `livenessProbe`
Als de bovenstaande tests niet zijn opgegeven, maakt de ingangs controller een veronderstelling dat de service bereikbaar is op `Path` opgegeven voor `backend-path-prefix` aantekening of de `path` die is opgegeven in de `ingress` definitie voor de service.

## <a name="default-values-for-health-probe"></a>Standaard waarden voor de status test
Voor een eigenschap die niet kan worden afgeleid van de test voor gereedheid/duur van de bewerkings tijd, worden standaard waarden ingesteld.

| Application Gateway probe-eigenschap | Standaard waarde |
|-|-|
| `Path` | / |
| `Host` | lokalehost |
| `Protocol` | HTTP |
| `Timeout` | 30 |
| `Interval` | 30 |
| `UnhealthyThreshold` | 3 |