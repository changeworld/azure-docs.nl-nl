---
title: Afwijkende detector-container uitvoeren in Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Implementeer de afwijkende detector container in een Azure-container exemplaar en test deze in een webbrowser.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: 2fba0a0d64502a30b6dfbc9f4f109bca65cca8b9
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76716361"
---
# <a name="deploy-an-anomaly-detector-container-to-azure-container-instances"></a>Een anomalie detectie container implementeren naar Azure Container Instances

Meer informatie over het implementeren van de Cognitive Services [anomalie detectie](../anomaly-detector-container-howto.md) container naar Azure [container instances](https://docs.microsoft.com/azure/container-instances/). In deze procedure wordt het maken van een afwijkende detector-resource gedemonstreerd. Vervolgens bespreken we het verzamelen van de bijbehorende container installatie kopie. Ten slotte markeren we de mogelijkheid om de indeling van de twee uit een browser uit te oefenen. Door gebruik te maken van containers kan de aandacht van de ontwikkel aars van de infra structuur afnemen in plaats van de ontwikkeling van toepassingen te richten.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>Aanvraag voor toegang tot de privécontainerregister

U moet eerst het [aanvraag formulier voor de afwijkings detectie container](https://aka.ms/adcontainer) volt ooien en verzenden om toegang tot de container aan te vragen.

[!INCLUDE [Request access](../../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

[!INCLUDE [Create an Anomaly Detector container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>Volgende stappen

* Controleer de [installatie-en uitvoer containers](../anomaly-detector-container-configuration.md) voor het ophalen van de container installatie kopie en voer de container uit
* Containers voor configuratie-instellingen [configureren](../anomaly-detector-container-configuration.md) controleren
* [Meer informatie over de API-service voor anomalie detectie](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
