---
title: 'Snelstartgids: een synthese van de C++ spraak, (Windows)-spraak service'
titleSuffix: Azure Cognitive Services
description: Meer informatie over het in-en C++ uitspreken van spraak in het Windows-bureau blad met behulp van de Speech SDK
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/24/2019
ms.author: yinhew
ms.openlocfilehash: ab2193a1ea34b176e5f97806f0099dfc86d75965
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2020
ms.locfileid: "78925732"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, moet u het volgende doen:

> [!div class="checklist"]
> * [Een Azure-spraak resource maken](../../../../get-started.md)
> * [Stel uw ontwikkel omgeving in en maak een leeg project](../../../../quickstarts/setup-platform.md?tabs=windows)

## <a name="add-sample-code"></a>Voorbeeldcode toevoegen

1. Open het bronbestand **helloworld.cpp**.

1. Vervang alle code door het volgende fragment:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/text-to-speech/helloworld/helloworld.cpp#code)]

1. Vervang in hetzelfde bestand de tekenreeks `YourSubscriptionKey` door uw abonnementssleutel.

1. Vervang de tekenreeks `YourServiceRegion` door de [regio](~/articles/cognitive-services/Speech-Service/regions.md) die gekoppeld is aan uw abonnement (bijvoorbeeld `westus` voor het gratis proefabonnement).

1. Kies in de menu balk de optie **bestand** > alles op te **slaan**.

## <a name="build-and-run-the-application"></a>De toepassing bouwen en uitvoeren.

1. Selecteer in de menu balk de optie **build** > **Build** om de toepassing te bouwen. De code moet nu zonder fouten worden gecompileerd.

1. Kies **fout opsporing** > **fout opsporing starten** (of druk op **F5**) om de toepassing **HelloWorld** te starten.

1. Typ een Engelse zin of zin. De toepassing verzendt uw tekst naar de spraak service, waarmee gesynthesizerde spraak naar de toepassing wordt verzonden om op uw spreker af te spelen.

   ![Console-uitvoer na geslaagde spraak-synthese](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-cpp-windows-console-output.png)

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Zie ook

- [Een aangepaste stem maken](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Aangepaste spraak voorbeelden vastleggen](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
