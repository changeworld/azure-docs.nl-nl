---
title: 'Zelfstudie: Gezichten in een afbeelding detecteren met de Android SDK'
titleSuffix: Azure Cognitive Services
description: In deze zelf studie maakt u een eenvoudige Android-app die gebruikmaakt van de face-service voor het detecteren van en het frame van gezichten in een afbeelding.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 8d5bef141f83eedaa996bb63c1fb814aeb6af197
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76165968"
---
# <a name="tutorial-create-an-android-app-to-detect-and-frame-faces-in-an-image"></a>Zelfstudie: een Android-app maken om gezichten in een afbeelding te herkennen en te omlijsten

In deze zelf studie maakt u een Android-toepassing die gebruikmaakt van de Azure face-service, via de Java-SDK, om menselijke gezichten in een installatie kopie te detecteren. Er wordt een geselecteerde afbeelding weergegeven en een kader rond elk gedetecteerd gezicht getekend.

In deze handleiding ontdekt u hoe u:

> [!div class="checklist"]
> - Een Android-app maken
> - De face-client bibliotheek installeren
> - De clientbibliotheek gebruiken om gezichten in een afbeelding te detecteren
> - Een kader rond elk gedetecteerd gezicht tekenen

![Android-schermafbeelding van een foto met gezichten in een rood vierkant](../Images/android_getstarted2.1.PNG)

De volledige voorbeeldcode is beschikbaar in de opslagplaats [Cognitive Services Face Android](https://github.com/Azure-Samples/cognitive-services-face-android-sample) op GitHub.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint. 

## <a name="prerequisites"></a>Vereisten

- De sleutel van het gezichts abonnement. U kunt een abonnementssleutel voor een gratis proefversie downloaden van [Cognitive Services proberen](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Of volg de instructies in [Create a cognitive Services account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) om u te abonneren op de face-service en uw sleutel op te halen. Vervolgens kunt u [omgevings variabelen maken](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor de sleutel-en service-eindpunt reeks, respectievelijk met de naam `FACE_SUBSCRIPTION_KEY` en `FACE_ENDPOINT`.
- Een versie van [Visual Studio 2015 of 2017](https://www.visualstudio.com/downloads/).
- [Android Studio](https://developer.android.com/studio/) met API-niveau 22 of hoger (vereist voor de clientbibliotheek van de Face-service).

## <a name="create-the-android-studio-project"></a>Het Android Studio-project maken

Volg deze stappen voor het maken van een nieuw Android-toepassingsproject.

1. Selecteer **Start a new Android Studio project** in Android Studio.
1. Wijzig in het scherm **Create Android Project** zo nodig de standaardvelden en klik op **Volgende**.
1. Gebruik in het scherm **Target Android Devices** de vervolgkeuzeselector om **API 22** of hoger te kiezen en klik op **Volgende**.
1. Selecteer **Empty Activity** en klik op **Next**.
1. Schakel het selectievakje **Backwards Compatibility** uit en klik op **Finish**.

## <a name="add-the-initial-code"></a>De initiële code toevoegen

### <a name="create-the-ui"></a>Gebruikersinterface maken

Open *activity_main.xml*. Selecteer in de indelingseditor het tabblad **Tekst** en vervang de inhoud door de volgende code.

[!code-xml[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/res/layout/activity_main.xml?name=snippet_activitymain)]

### <a name="create-the-main-class"></a>Hoofdklasse maken

Open *MainActivity.java* en vervang de bestaande `import`-instructies door de volgende code.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_imports)]

Vervang vervolgens de inhoud van de klasse **MainActivity** door de volgende code. Hiermee wordt een gebeurtenis-handler voor **Button** gemaakt, waardoor een nieuwe activiteit wordt gestart zodat de gebruiker een afbeelding kan selecteren. De afbeelding wordt weergegeven in **ImageView**.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_mainactivity_methods)]

### <a name="try-the-app"></a>Probeer de app

Maak de aanroep voor **detectAndFrame** in de methode **onActivityResult** inactief door er commentaar bij te plaatsen. Druk vervolgens op **Uitvoeren** in het menu om uw app te testen. Als de app wordt geopend, hetzij in een emulator, hetzij in een verbonden apparaat, klikt u onderaan op de knop **Bladeren**. Het dialoogvenster voor bestandsselectie van het apparaat moet nu worden weergegeven. Kies een afbeelding en controleer of deze in het venster wordt weergegeven. Sluit vervolgens de app en ga door naar de volgende stap.

![Android-schermafbeelding van een foto met gezichten](../Images/android_getstarted1.1.PNG)

## <a name="add-the-face-sdk"></a>Face-SDK toevoegen

### <a name="add-the-gradle-dependency"></a>Gradle-afhankelijkheid toevoegen

Gebruik in het deelvenster **Project** de vervolgkeuzeselector om **Android** te selecteren. Vouw **Gradle Scripts** uit en open *build.gradle (Module: app)* . Voeg een afhankelijkheid toe voor de Face-clientbibliotheek, `com.microsoft.projectoxford:face:1.4.3`, zoals weergegeven in de onderstaande schermafbeelding. Klik vervolgens op **Sync Now**.

![Android Studio-schermafbeelding van app-bestand build.gradle](../Images/face-tut-java-gradle.png)

### <a name="add-the-face-related-project-code"></a>De met Face gerelateerde projectcode toevoegen

Ga terug naar **MainActivity.java** en voeg de volgende `import`-instructies toe:

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_face_imports)]

Voeg vervolgens boven de methode **onCreate** de volgende code toe aan de klasse **MainActivity**:

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_mainactivity_fields)]

Vouw in het deelvenster **Project** achtereenvolgens **app**, **manifests** uit en open *AndroidManifest.xml*. Voeg het volgende element in als een direct onderliggend element van het `manifest`-element:

[!code-xml[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/AndroidManifest.xml?name=snippet_manifest_entry)]

## <a name="upload-image-and-detect-faces"></a>Afbeelding uploaden en gezichten detecteren

Uw app detecteert gezichten door de methode **faceClient. Face. DetectWithStreamAsync** aan te roepen, waarmee de rest API voor [detecteren](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) wordt ingepakt en een lijst met **gezichts** instanties wordt geretourneerd.

Elke geretourneerde **Face**-instantie bevat een rechthoek om de locatie ervan aan te geven, plus een reeks optionele gezichtskenmerken. In dit voorbeeld worden alleen de rechthoeken om de gezichten aangevraagd.

Voeg de volgende twee methoden in de klasse **MainActivity** in. Wanneer het gezichts detectie is voltooid, roept de app de **drawFaceRectanglesOnBitmap** -methode aan om de **ImageView**te wijzigen. U gaat vervolgens deze methode definiëren.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_detection_methods)]

## <a name="draw-face-rectangles"></a>Gezichtsrechthoeken tekenen

Voeg de volgende helper-methode in de **MainActivity**-klasse in. Hiermee wordt een rechthoek rond elk gedetecteerd gezicht getekend met behulp van de rechthoekcoördinaten van elke **Face**-instantie.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_drawrectangles)]

Verwijder ten slotte het commentaar bij de methode **detectAndFrame** in **onActivityResult**.

## <a name="run-the-app"></a>De app uitvoeren

Voer de toepassing uit en blader naar een afbeelding met een gezicht. Wacht enkele seconden, zodat de Face-service kan reageren. U zou nu een rode rechthoek rond de gezichten in de afbeelding moeten zien.

![Android-schermafbeelding van gezichten met rode rechthoeken eromheen](../Images/android_getstarted2.1.PNG)

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u het basis proces geleerd voor het gebruik van de gezichts-Java-SDK en een toepassing gemaakt om gezichten in een installatie kopie te detecteren en in te stellen. Meer informatie over de details van gezichtsdetectie.

> [!div class="nextstepaction"]
> [Gezichten in afbeeldingen detecteren](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
