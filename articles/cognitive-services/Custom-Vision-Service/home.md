---
title: Wat is Custom Vision?
titleSuffix: Azure Cognitive Services
description: Leer hoe u de Custom Vision-service kunt gebruiken om aangepaste classificaties voor afbeeldingen te bouwen in de Azure-cloud.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 829921ae5600873fdc8f2946ae3ff355bffb06a8
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978591"
---
# <a name="what-is-custom-vision"></a>Wat is Custom Vision?

Custom Vision is een cognitieve service waarmee u uw eigen afbeeldingsclassificaties kunt compileren, implementeren en verbeteren. Een afbeeldingsclassificatie is een AI-service die op basis van de visuele kenmerken van een afbeelding labels (die _klassen_ vertegenwoordigen) op afbeeldingen toepast. Anders dan bij de [Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)-service kunt u met Custom Vision bepalen welke labels u wilt toepassen.

## <a name="what-it-does"></a>Wat het doet

De Custom Vision-service gebruikt een machine learning-algoritme om labels toe te passen op afbeeldingen. U, als ontwikkelaar, moet groepen afbeeldingen indienen die de betreffende kenmerken al dan niet hebben. U voorziet de afbeeldingen op het moment van inzending zelf van een label. Vervolgens wordt het algoritme getraind op basis van deze gegevens en berekent het algoritme de eigen nauwkeurigheid aan de hand van deze zelfde gegevens. Wanneer het algoritme is getraind, kunt u het model testen, opnieuw trainen en uiteindelijk gebruiken om nieuwe afbeeldingen te classificeren op basis van de behoeften van de app. U kunt het model zelf ook exporteren voor offlinegebruik.

### <a name="classification-and-object-detection"></a>Classificatie- en objectdetectie

Custom Vision-functionaliteit kan worden onderverdeeld in twee functies. Met **afbeeldingsclassificatie** worden een of meer labels toegepast op een afbeelding. **Objectdetectie** is vergelijkbaar, maar hiermee worden ook de coördinaten van de afbeelding geretourneerd waar de toegepaste labels kunnen worden gevonden.

### <a name="optimization"></a>Optimalisatie

De Custom Vision-service is zodanig geoptimaliseerd dat deze snel grote verschillen tussen afbeeldingen herkent. U kunt dus al met een kleine hoeveelheid gegevens beginnen met het ontwikkelen van prototypes van uw model. 50 afbeeldingen per label is meestal al een goed begin. De service is echter niet optimaal voor het detecteren van subtiele verschillen in afbeeldingen (bijvoorbeeld het detecteren van kleine barsten of inspringing in scenario's met kwaliteits bewaking).

Daarnaast kunt u kiezen uit verschillende Custom Vision-algoritmen die zijn geoptimaliseerd voor afbeeldingen met bepaalde onderwerpen&mdash;bijvoorbeeld oriëntatiepunten of detailhandelsartikelen. Zie voor meer informatie de hand leiding [een classificatie bouwen](getting-started-build-a-classifier.md) .

## <a name="what-it-includes"></a>Samenstelling van Content Moderator

De Custom Vision Service is beschikbaar als een set met systeemeigen SDK’s of via een webinterface op de [Custom Vision-startpagina](https://customvision.ai/). U kunt een model maken, testen en trainen via een van de interfaces of beide samen gebruiken.

![Custom Vision-startpagina in een Chrome-browservenster](media/browser-home.png)

## <a name="data-privacy-and-security"></a>Gegevensprivacy en -beveiliging

Zoals geldt voor alle Cognitive Services, dienen ontwikkelaars die de Custom Vision-service gebruiken op de hoogte te zijn van het beleid van Microsoft inzake klantgegevens. Zie de [pagina Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) (Engelstalig) in het Microsoft Trust Center voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Volg de handleiding [Een classificatie bouwen](getting-started-build-a-classifier.md) om aan de slag te gaan met Custom Vision op het web, of voltooi een [zelfstudie voor afbeeldingsclassificatie](csharp-tutorial.md) om een basisscenario in code te implementeren.
