---
title: 'Quick Start: spraak herkennen vanuit een microfoon, doel-C-spraak service'
titleSuffix: Azure Cognitive Services
description: Meer informatie over het herkennen van spraak in doel-C op macOS met behulp van de Speech SDK
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/23/2019
ms.author: chlandsi
ms.openlocfilehash: c2f0fbe66b26c6eca6e0c0b2530efacba9bae958
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75380589"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-macos-by-using-the-speech-sdk"></a>Quick Start: spraak herkennen in doel-C op macOS met behulp van de Speech SDK

Quick starts zijn ook beschikbaar voor [spraak synthese](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/objectivec-macos.md).

In dit artikel leert u hoe u een macOS-app kunt maken in doel-C met behulp van de Azure Cognitive Services Speech SDK om spraak opnamen van een microfoon naar tekst te transcriberen.

## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, hebt u het volgende nodig:

* Een [abonnements sleutel](~/articles/cognitive-services/Speech-Service/get-started.md) voor de spraak service.
* Een macOS-computer met [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) of hoger en MacOS 10,13 of hoger.

## <a name="get-the-speech-sdk-for-macos"></a>De Speech SDK voor macOS ophalen

[!INCLUDE [License notice](~/includes/cognitive-services-speech-service-license-notice.md)]

De Cognitive Services Speech SDK voor Mac wordt gedistribueerd als een framework-bundel. Het kan worden gebruikt in Xcode-projecten als [CocoaPod](https://cocoapods.org/) of worden gedownload van https://aka.ms/csspeech/macosbinary en hand matig worden gekoppeld. In dit artikel wordt gebruikgemaakt van een CocoaPod.

## <a name="create-an-xcode-project"></a>Een Xcode-project maken

Start Xcode en start een nieuw project door **bestand** > **Nieuw** > **project**te selecteren. Selecteer in het dialoog venster sjabloon selectie de sjabloon voor de **cacao-app** .

In de volgende dialoog vensters volgt u de onderstaande opties.

1. In het dialoog venster **project opties** :
    1. Voer een naam in voor de Quick Start-app, bijvoorbeeld *HelloWorld*.
    1. Voer de juiste organisatie naam en organisatie-id in als u al een Apple-ontwikkelaars account hebt. Gebruik voor test doeleinden een naam zoals *testorg*. Om de app te kunnen ondertekenen, hebt u een geschikt inrichtingsprofiel nodig. Zie de [Apple Developer-site](https://developer.apple.com/)voor meer informatie.
    1. Zorg ervoor dat de **doel-C** is geselecteerd als de taal voor het project.
    1. Schakel de selectie vakjes uit om Story boards te gebruiken en om een op documenten gebaseerde toepassing te maken. De eenvoudige gebruikers interface voor de voor beeld-app wordt programmatisch gemaakt.
    1. Schakel alle selectie vakjes voor testen en basis gegevens uit.

    ![Projectinstellingen](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-project-settings.png)

1. Selecteer een projectmap:
    1. Kies een map waarin u het project wilt plaatsen. Met deze stap maakt u een map HelloWorld in uw basismap die alle bestanden voor het Xcode-project bevat.
    1. Schakel het maken van een Git-opslagplaats uit voor dit voorbeeldproject.
1. Stel de rechten in voor toegang tot het netwerk en de microfoon. Selecteer de naam van de app in de eerste regel in het overzicht aan de linkerkant om naar de app-configuratie te gaan. Selecteer vervolgens het tabblad **mogelijkheden** .
    1. Schakel de **sandbox** -instelling van de app in voor de app.
    1. Selecteer de selectie vakjes voor **uitgaande verbindingen** en toegang tot de **microfoon** .

    ![Sandbox-instellingen](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-sandbox.png)

1. De app moet ook het gebruik van de microfoon in het `Info.plist`-bestand declareren. Selecteer het bestand in het overzicht en voeg de sleutel **gebruiks beschrijving privacy-microfoon** toe met een waarde als *microfoon is vereist voor spraak herkenning*.

    ![Instellingen in info. plist](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-info-plist.png)

1. Sluit het project Xcode. U kunt later een ander exemplaar van het item gebruiken nadat u de CocoaPods hebt ingesteld.

## <a name="install-the-sdk-as-a-cocoapod"></a>De SDK installeren als een CocoaPod

1. Installeer de CocoaPod dependency manager zoals beschreven in de [installatie-instructies](https://guides.cocoapods.org/using/getting-started.html).
1. Ga naar de map van uw voor beeld-app. Dit is HelloWorld. Plaats een tekst bestand met de naam *Podfile* en de volgende inhoud in die map:

   [!code-ruby[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/from-microphone/helloworld/Podfile)]
1. Ga naar de map HelloWorld in een Terminal en voer de opdracht uit `pod install`. Met deze opdracht wordt een `helloworld.xcworkspace` Xcode-werk ruimte gegenereerd die zowel de voor beeld-app als de spraak-SDK als een afhankelijkheid bevat. Deze werk ruimte wordt in de volgende stappen gebruikt.

## <a name="add-the-sample-code"></a>De voorbeeldcode toevoegen

1. Open de werk ruimte `helloworld.xcworkspace` in Xcode.
1. Vervang de inhoud van het automatisch gegenereerde `AppDelegate.m` bestand door de volgende code:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/from-microphone/helloworld/helloworld/AppDelegate.m#code)]
1. Vervang de tekenreeks `YourSubscriptionKey` door uw abonnementssleutel.
1. Vervang de teken reeks `YourServiceRegion` door de [regio](~/articles/cognitive-services/Speech-Service/regions.md) die aan uw abonnement is gekoppeld. Gebruik bijvoorbeeld `westus` voor het gratis proef abonnement.

## <a name="build-and-run-the-sample"></a>Het voorbeeldproject compileren en uitvoeren

1. Maak de uitvoer van de fout opsporing zichtbaar door > **gebied voor fout opsporing** **weer geven** > **console activeren**te selecteren.
1. Bouw de voorbeeld code en voer deze uit door in het menu **Product** > uit te **voeren** . U kunt ook **afspelen**selecteren.
1. Nadat u de knop hebt geselecteerd en enkele woorden hebt gedicteerd, ziet u de tekst die u in het onderste gedeelte van het scherm hebt gesp roken. Wanneer u de app voor de eerste keer uitvoert, wordt u gevraagd om de app toegang te geven tot de microfoon van uw computer.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Objective-C-voorbeelden op GitHub verkennen](https://aka.ms/csspeech/samples)
