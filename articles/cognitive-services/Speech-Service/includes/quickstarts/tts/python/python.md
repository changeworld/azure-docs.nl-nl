---
title: 'Quick Start: een synthese van spraak, python-Speech-Service'
titleSuffix: Azure Cognitive Services
description: Meer informatie over het maken van spraak in python met behulp van de Speech SDK
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 19/14/2020
ms.author: dapine
ms.openlocfilehash: 58f2b3693b924197dda1e396ae71192776ca276e
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2020
ms.locfileid: "78925697"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, moet u het volgende doen:

> [!div class="checklist"]
> * [Een Azure-spraak resource maken](../../../../get-started.md)
> * [Stel uw ontwikkel omgeving in en maak een leeg project](../../../../quickstarts/setup-platform.md)

## <a name="support-and-updates"></a>Ondersteuning en updates

Updates voor het Python Speech-SDK-pakket worden gedistribueerd via PyPI en aangekondigd in de [Releaseopmerkingen](~/articles/cognitive-services/Speech-Service/releasenotes.md).
Als een nieuwe versie beschikbaar is, kunt u een update naar deze versie uitvoeren met behulp van de opdracht `pip install --upgrade azure-cognitiveservices-speech`.
U kunt controleren welke versie momenteel is geïnstalleerd door de variabele `azure.cognitiveservices.speech.__version__` te bekijken.

Zie [opties voor ondersteuning en hulp](~/articles/cognitive-services/Speech-Service/support.md) als er een probleem is of als er een functie ontbreekt.

## <a name="create-a-python-application-that-uses-the-speech-sdk"></a>Een Python-toepassing maken dat de Speech-SDK gebruikt

### <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

U kunt de [voorbeeldcode](#sample-code) uit deze quickstart kopiëren naar een `quickstart.py`-bronbestand en de voorbeeldcode uitvoeren in uw IDE of in de console:

```sh
python quickstart.py
```

Of u kunt deze quickstart-zelfstudie downloaden als een [Jupyter](https://jupyter.org)-notebook uit de [opslagplaats met Speech-SDK-voorbeelden](https://aka.ms/csspeech/samples) en de quickstart uitvoeren als een notitieblok.

### <a name="sample-code"></a>Voorbeeldcode

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/text-to-speech/quickstart.py#code)]

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>De Speech-SDK installeren en gebruiken met Visual Studio Code

1. Down load en installeer een 64-bits versie van [python](https://www.python.org/downloads/), 3,5 tot 3,8 op uw computer.
1. Download en installeer [Visual Studio Code](https://code.visualstudio.com/Download).
1. Open Visual Studio Code en installeer de Python-extensie. Selecteer **Bestand** > **Voorkeuren** > **Extensies** in het menu. Zoek naar **Python**.

   ![De Python-extensie installeren](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-extension.png)

1. Maak een map om daar het project in op te slaan. Dat kunt u bijvoorbeeld doen met Windows Verkenner.
1. Selecteer in Visual Studio Code het pictogram **Bestand**. Open vervolgens de map die u hebt gemaakt.

   ![Een map openen](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-open-folder.png)

1. Maak een nieuw Python-bronbestand, `speechsdk.py`, door het pictogram Nieuw bestand te selecteren.

   ![Een bestand maken](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-newfile.png)

1. Kopieer en plak de [Python-code](#sample-code) en sla deze op in het zojuist gemaakte bestand.
1. Voeg de gegevens van uw Speech Service-abonnement in.
1. Als er een Python-interpreter is geselecteerd, wordt deze onder aan het venster weergegeven, links van de statusbalk.
   Anders wordt er een lijst met beschikbare Python-interpreters geopend. Open het opdracht palet (CTRL + SHIFT + P) en voer **python in: Select interpreter**. Kies de juiste versie.
1. U kunt het Python Speech-SDK-pakket installeren vanuit Visual Studio Code. Doe dat als het pakket nog niet is geïnstalleerd voor de Python-interpreter die u hebt geselecteerd.
   Open een terminal voor het installeren van het Speech-SDK-pakket. Breng het opdracht palet opnieuw aan (CTRL + SHIFT + P) en voer de **volgende Terminal in: nieuwe geïntegreerde terminal maken**.
   Voer in de terminal die wordt geopend, de opdracht `python -m pip install azure-cognitiveservices-speech` in, of de juiste opdracht voor uw systeem.
1. Als u de voorbeeldcode wilt uitvoeren, klikt u met de rechtermuisknop ergens in de editor. Selecteer **Python-bestand uitvoeren in de Terminal**.
   Typ een tekst wanneer u hierom wordt gevraagd. De gesynthesizerde audio wordt binnenkort afgespeeld.

   ![Een voorbeeld uitvoeren](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-run-tts.png)

Als er zich problemen voordoen met deze instructies, raadpleegt u de uitgebreidere [Python-zelfstudie voor Visual Studio Code](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Zie ook

- [Een aangepaste stem maken](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Aangepaste spraak voorbeelden vastleggen](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
