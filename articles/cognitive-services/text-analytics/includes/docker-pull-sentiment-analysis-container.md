---
title: Docker-pull voor de Sentimentanalyse container
titleSuffix: Azure Cognitive Services
description: Docker-pull-opdracht voor Sentimentanalyse container
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: df00d469052fa30c3f2aaa5afe1881ef74587f9a
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966706"
---
#### <a name="docker-pull-for-the-sentiment-analysis-container"></a>Docker-pull voor de Sentimentanalyse container

Gebruik de [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) opdracht om een container installatie kopie te downloaden van micro soft container Registry.

Zie de [sentimentanalyse](https://go.microsoft.com/fwlink/?linkid=2018654) -container op de docker-hub voor een volledige beschrijving van de beschik bare labels voor de Text Analytics containers.

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```
