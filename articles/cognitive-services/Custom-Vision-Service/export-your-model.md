---
title: Uw model exporteren naar Mobile-Custom Vision Service
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt uitgelegd hoe u uw model exporteert voor gebruik bij het maken van mobiele toepassingen of het lokaal uitvoeren voor realtime-classificatie.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: anroth
ms.openlocfilehash: f734f4f1a11f57b759615e7a9ce2cd2f7f8028fb
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718950"
---
# <a name="export-your-model-for-use-with-mobile-devices"></a>Uw model exporteren voor gebruik met mobiele apparaten

Custom Vision Service kunnen classificaties worden geëxporteerd om offline uit te voeren. U kunt uw geëxporteerde classificatie insluiten in een toepassing en deze lokaal uitvoeren op een apparaat voor realtime-classificatie.

## <a name="export-options"></a>Export opties

Custom Vision Service ondersteunt de volgende export bewerkingen:

* __Tensor flow__ voor __Android__.
* __CoreML__ voor __iOS11__.
* __ONNX__ voor __Windows ml__.
* __[Vision AI Developer Kit](https://azure.github.io/Vision-AI-DevKit-Pages/)__ .
* Een __docker-container__ voor Windows-, Linux-of arm-architectuur. De container bevat een tensor flow-model en een service code voor het gebruik van de Custom Vision-API.

> [!IMPORTANT]
> Custom Vision Service exporteert alleen __compacte__ domeinen. De modellen die door compacte domeinen worden gegenereerd, zijn geoptimaliseerd voor de beperkingen van real-time classificatie op mobiele apparaten. Classificaties die zijn gebouwd met een compact domein, zijn mogelijk iets minder nauw keuriger dan een standaard domein met dezelfde hoeveelheid trainings gegevens.
>
> Zie het [classificatie document verbeteren](getting-started-improving-your-classifier.md) voor meer informatie over het verbeteren van uw classificaties.

## <a name="convert-to-a-compact-domain"></a>Converteren naar een compact domein

> [!NOTE]
> De stappen in deze sectie zijn alleen van toepassing als u een bestaand model hebt dat niet is ingesteld op een compact domein.

Voer de volgende stappen uit om het domein van een bestaand model te converteren:

1. Selecteer op de [website voor aangepaste Vision](https://customvision.ai)het pictogram __Start__ om een lijst met uw projecten weer te geven.

    ![Afbeelding van de lijst Start pictogram en projecten](./media/export-your-model/projects-list.png)

1. Selecteer een project en selecteer vervolgens het __tandwiel__ pictogram in de rechter bovenhoek van de pagina.

    ![Afbeelding van het tandwiel pictogram](./media/export-your-model/gear-icon.png)

1. Selecteer in de sectie __domeinen__ een van de __compacte__ domeinen. Selecteer __wijzigingen opslaan__ om de wijzigingen op te slaan. 

    > [!NOTE]
    > Voor de Vision AI dev kit moet het project worden gemaakt met het __algemene domein (compact)__ en moet u de optie **Vision AI dev kit** opgeven onder het gedeelte **export mogelijkheden** .

    ![Afbeelding van de selectie van domeinen](./media/export-your-model/domains.png)

1. Selecteer aan de bovenkant van de pagina __trainen__ om opnieuw te trainen met het nieuwe domein.

## <a name="export-your-model"></a>Uw model exporteren

Gebruik de volgende stappen om het model na het opnieuw trainen te exporteren:

1. Ga naar het tabblad **prestaties** en selecteer __exporteren__. 

    ![Afbeelding van het pictogram voor exporteren](./media/export-your-model/export.png)

    > [!TIP]
    > Als de __export__ vermelding niet beschikbaar is, gebruikt de geselecteerde iteratie geen compact domein. Gebruik de sectie __iteraties__ van deze pagina om een herhaling te selecteren die gebruikmaakt van een compact domein, en selecteer vervolgens __exporteren__.

1. Selecteer de gewenste export indeling en selecteer vervolgens __exporteren__ om het model te downloaden.

## <a name="next-steps"></a>Volgende stappen

Integreer uw geëxporteerde model in een toepassing door een van de volgende artikelen of voor beelden te verkennen:

* [Uw tensor flow-model gebruiken met python](export-model-python.md)
* [Uw ONNX-model gebruiken met Windows Machine Learning](custom-vision-onnx-windows-ml.md)
* Zie het voor beeld voor [CoreML-model in een IOS-toepassing voor de](https://go.microsoft.com/fwlink/?linkid=857726) classificatie van realtime-afbeeldingen met SWIFT.
* Zie het voor beeld voor [tensor flow-model in een Android-toepassing](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample) voor realtime-afbeeldings classificatie op Android.
* Zie het voor beeld voor het [CoreML-model met Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel) voor de classificatie van realtime-afbeeldingen in een Xamarin IOS-app.
