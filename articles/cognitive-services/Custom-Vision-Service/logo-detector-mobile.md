---
title: 'Zelf studie: aangepaste logo detector gebruiken om Azure-Services te herkennen-Custom Vision'
titleSuffix: Azure Cognitive Services
description: In deze zelf studie gaat u een voor beeld-app door lopen die gebruikmaakt van Custom Vision als onderdeel van een logo-detectie scenario. Leer hoe Custom Vision wordt gebruikt met andere onderdelen om een end-to-end-toepassing te bieden.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 51fa6d4859eb4b7f059b499ba73d84d9fc65e6f6
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78398991"
---
# <a name="tutorial-recognize-azure-service-logos-in-camera-pictures"></a>Zelf studie: Azure service-logo's herkennen in camera afbeeldingen

In deze zelf studie gaat u een voor beeld-app verkennen die gebruikmaakt van Custom Vision als onderdeel van een groter scenario. Met de AI Visual Provision-app, een Xamarin.Forms-app voor mobiele platforms, worden camera-afbeeldingen van Azure-servicelogo’s geanalyseerd. Vervolgens worden de werkelijke services geïmplementeerd in het Azure-account van de gebruiker. Hier leert u hoe de app Custom Vision gebruikt met andere onderdelen om een nuttige end-to-end-toepassing te bieden. U kunt het hele app-scenario voor uzelf uitvoeren, maar u kunt alleen het Custom Vision onderdeel van de installatie volt ooien en verkennen hoe de app dit gebruikt.

In deze zelfstudie leert u hoe u het volgende kunt doen:

> [!div class="checklist"]
> - Een aangepaste objectdetector maken voor het herkennen van Azure-servicelogo’s.
> - Uw app verbinden met Azure Computer Vision en Custom Vision.
> - Een service-principal-account in Azure maken om Azure-services te implementeren vanuit de app.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint. 

## <a name="prerequisites"></a>Vereisten

- [Visual Studio 2017 of hoger](https://www.visualstudio.com/downloads/)
- De Xamarin-workload voor Visual Studio (zie [Xamarin installeren](https://docs.microsoft.com/xamarin/cross-platform/get-started/installation/windows))
- Een iOS- of Android-emulator voor Visual Studio
- De [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) (optioneel)

## <a name="get-the-source-code"></a>De broncode ophalen

Als u de opgegeven web-app wilt gebruiken, kloont of downloadt u de broncode van de app uit de opslagplaats [AI Visual Provision](https://github.com/Microsoft/AIVisualProvision) in GitHub. Open het bestand *Source/VisualProvision.sln* in Visual Studio. U zult een aantal projectbestanden later bewerken, zodat u de app kunt uitvoeren.

## <a name="create-an-object-detector"></a>Een objectdetector maken

Meld u aan bij de [Custom Vision-website](https://customvision.ai/) en maak een nieuw project. Geef een project Objectdetectie op en gebruik het domein Logo. Hiermee zorgt u ervoor dat de service een algoritme gebruikt dat is geoptimaliseerd voor logodetectie. 

![Nieuw projectvenster op de Custom Vision-website in de browser Chrome](media/azure-logo-tutorial/new-project.png)

## <a name="upload-and-tag-images"></a>Afbeeldingen uploaden en labelen

Vervolgens moet u het algoritme voor logodetectie trainen door afbeeldingen van Azure-servicelogo’s te uploaden en deze handmatig te labelen. De AIVisualProvision-opslagplaats bevat een set trainingsafbeeldingen die u kunt gebruiken. Op de website selecteert u de knop **afbeeldingen toevoegen** op het tabblad **trainings afbeeldingen** . Ga vervolgens naar de map **Documents/images/Training_DataSet** van de opslag plaats. U moet de logo’s in elke afbeelding handmatig labelen. Als u dit project alleen maar wilt testen, kunt u daarom beter slechts een subset van de afbeeldingen uploaden. Upload ten minste 15 exemplaren van elke tag die u wilt gebruiken.

Nadat u de trainingsafbeeldingen hebt geüpload, selecteert u de eerste afbeelding in de weergave. Het venster Tags toevoegen wordt weer gegeven. Teken vakken en wijs labels toe voor elk logo in elke afbeelding. 

![Labellen van logo’s op de website van Custom Vision](media/azure-logo-tutorial/tag-logos.png)

De app is geconfigureerd om te werken met specifieke labeltekenreeksen. Bekijk de definities in het bestand *Source\VisualProvision\Services\Recognition\RecognitionService.cs*:

[!code-csharp[Tag definitions](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/RecognitionService.cs?name=snippet_constants)]

Nadat u een afbeelding heeft gelabeld, gaat u naar rechts om de volgende te labellen. Sluit het labelvenster wanneer u klaar bent.

## <a name="train-the-object-detector"></a>De objectdetector trainen

Stel in het linkerdeelvenster **Labels** in op **Gelabeld**. Als het goed is, ziet u nu al uw afbeeldingen. Klik vervolgens op de groene knop boven aan de pagina om het model te trainen. Het algoritme traint om dezelfde tags te herkennen in nieuwe installatie kopieën. Het model wordt ook getest op een aantal bestaande afbeeldingen om nauwkeurigheidsscores te genereren.

![De Custom Vision-website op het tabblad trainings afbeeldingen. In deze scherm afbeelding ziet u een overzicht van de trein knop](media/azure-logo-tutorial/train-model.png)

## <a name="get-the-prediction-url"></a>De voorspellings-URL ophalen

Zodra dit model is getraind, kunt u het integreren in de app. U moet de eind punt-URL (het adres van uw model, die door de app wordt opgevraagd) en de Voorspellings sleutel ophalen (om de app toegang te geven tot Voorspellings aanvragen). Klik op het tabblad **Prestatie** op de knop **Voorspellings-URL** boven aan de pagina.

![De Custom Vision-website met een Voorspellings-API-scherm waarop een URL-adres en API-sleutel worden weergegeven](media/azure-logo-tutorial/cusvis-endpoint.png)

Kopieer de eind punt-URL en de waarde voor de voor **Spellings sleutel** naar de juiste velden in het *Source\VisualProvision\AppSettings.cs* -bestand:

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?name=snippet_cusvis_keys)]

## <a name="examine-custom-vision-usage"></a>Custom Vision-gebruik onderzoeken

Open het bestand *Source/VisualProvision/Services/Recognition/CustomVisionService.cs* om te zien hoe de Custom Vision-sleutel en eindpunt-URL worden gebruikt in de app. Bij de methode **PredictImageContentsAsync** wordt een bytestream van een afbeeldingsbestand gebruikt samen met een annuleringstoken (voor asynchroon taakbeheer), de voorspellings-API van Custom Vision aangeroepen, en het resultaat van de voorspelling geretourneerd. 

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/CustomVisionService.cs?name=snippet_prediction)]

Dit resultaat heeft de vorm van een **PredictionResult**-exemplaar, die een lijst met **Voorspellingsexemplaren** bevat. Een **Voorspelling** bevat een gedetecteerd label en het bijbehorende begrenzingsvak in de afbeelding.

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/Prediction.cs?name=snippet_prediction_class)]

Begin voor meer informatie over hoe deze gegevens worden verwerkt door de app met de **GetResourcesAsync**-methode. Deze methode is gedefinieerd in het bestand *Source/VisualProvision/Services/Recognition/RecognitionService.cs*.  

## <a name="add-computer-vision"></a>Computer Vision toevoegen

Het Custom Vision-gedeelte van de zelfstudie is voltooid. Als u de app wilt uitvoeren, moet u ook de Computer Vision-service integreren. De app gebruikt de functie voor tekstherkenning van Computer Vision om het logo-detectieproces aan te vullen. Een Azure-logo kan worden herkend door de weergave *of* door de tekst die in de buurt wordt weergegeven. Anders dan bij Custom Vision-modellen is Computer Vision vooraf getraind om bepaalde bewerkingen uit te voeren op afbeeldingen of video’s.

Abonneer u eenvoudig op de Computer Vision-service om een sleutel en eindpunt-URL op te halen. Zie [Hoe u abonnementssleutels kunt verkrijgen](https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtosubscribe) als u hulp nodig hebt bij deze stap.

![De Computer Vision-service in de Azure Portal, met het menu Quick Start geselecteerd. Een link voor sleutels wordt gemarkeerd, net als de API-eindpunt-URL](media/azure-logo-tutorial/comvis-keys.png)

Open vervolgens het bestand *Source\VisualProvision\AppSettings.cs* en vul de variabelen `ComputerVisionEndpoint` en `ComputerVisionKey` met de juiste waarden.

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?name=snippet_comvis_keys)]

## <a name="create-a-service-principal"></a>Een service-principal maken

Voor de app is een service-principal-account in Azure vereist om de services te implementeren in uw Azure-abonnement. Met een service-principal kunt u specifieke machtigingen delegeren naar een app met behulp van op rollen gebaseerd toegangsbeheer. Bekijk de [Handleiding voor service-principals](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-create-service-principals) voor meer informatie.

U kunt als volgt een service-principal maken met behulp van Azure Cloud Shell of de Azure CLI. Meld u aan en selecteer het abonnement dat u wilt gebruiken om te beginnen.

```azurecli
az login
az account list
az account set --subscription "<subscription name or subscription id>"
```

Vervolgens maakt u uw service-principal. (Dit proces kan enige tijd duren.)

```azurecli
az ad sp create-for-rbac --name <servicePrincipalName> --password <yourSPStrongPassword>
```

Als dit is voltooid, ziet u de volgende JSON-uitvoer met de benodigde referenties.

```json
{
  "clientId": "(...)",
  "clientSecret": "(...)",
  "subscriptionId": "(...)",
  "tenantId": "(...)",
  ...
}
```

Noteer de waarden `clientId` en `tenantId`. Voeg deze waarden toe aan de juiste velden in het bestand *Source\VisualProvision\AppSettings.cs*.

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?name=snippet_serviceprincipal)]

## <a name="run-the-app"></a>De app kunt uitvoeren

Hier aangekomen hebt u de app toegang gegeven tot:

- Een getraind Custom Vision-model
- De Computer Vision-service
- Een service-principal-account

Volg deze stappen om de app uit te voeren:

1. In Visual Studio Solution Explorer selecteert u het project **VisualProvision.Android** of het project **VisualProvision.iOS**. Kies een bijbehorende emulator of verbonden mobiele apparaat uit de vervolgkeuzelijst op de hoofdwerkbalk. Voer vervolgens de app uit.

    > [!NOTE]
    > U heeft een MacOS-apparaat nodig om een iOS-emulator uit te voeren.

1. Voer in het eerste scherm dat wordt geladen de client-id, tenant-id en het wachtwoord voor de service-principal in. Klik op de knop **Aanmelden**.

    > [!NOTE]
    > In sommige emulators wordt de knop **Aanmelden** mogelijk niet geactiveerd bij deze stap. Als dit gebeurt, stopt u de app, opent u het bestand *Source/VisualProvision/Pages/LoginPage.xaml*, gaat u naar het element `Button` met het label **AANMELDINGSKNOP**, verwijdert u de volgende regel en voert u de app nogmaals uit.
    >  ```xaml
    >  IsEnabled="{Binding IsValid}"
    >  ```
    
    ![Het app-scherm met velden voor de referenties van de service-principal](media/azure-logo-tutorial/app-credentials.png)

1. Selecteer in de vervolgkeuzelijst in het volgende scherm het Azure-abonnement dat u hebt. (Dit menu moet alle abonnementen bevatten waartoe uw Service-Principal toegang heeft.) Selecteer de knop **door gaan** . Op dit moment wordt u in de app mogelijk gevraagd om de apparaten toegang te verlenen tot de camera en de fotobibliotheek. Verleen de toegangsmachtigingen.

    ![Het app-scherm met een vervolgkeuzelijst voor het abonnement in Target Azure](media/azure-logo-tutorial/app-az-subscription.png)


1. Vervolgens wordt de camera op uw apparaat geactiveerd. Neem een foto van een van de Azure-servicelogo’s die u hebt getraind. In een implementatievenster wordt u nu gevraagd om een regio en resourcegroep te selecteren voor de nieuwe services. (Net zoals wanneer u deze implementeert vanuit de Azure Portal). 

    ![Een camerascherm van een smartphone met daarop twee papieren knipsels van Azure-logo’s](media/azure-logo-tutorial/app-camera-capture.png)

    ![Een app-scherm met velden voor de implementatieregio en de vermelding voor de resourcegroep](media/azure-logo-tutorial/app-deployment-options.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u alle stappen in dit scenario hebt gevolgd en de app hebt gebruikt om Azure-services te implementeren in uw account, gaat u naar de [Azure Portal](https://ms.portal.azure.com/) wanneer u hiermee klaar bent en annuleert u de services die u niet wilt gebruiken. Annuleer daar de services die u niet wilt gebruiken.

Bovendien, als u in de toekomst uw eigen objectdetectieproject wilt maken met Custom Vision, kunt u overwegen om het logodetectieproject dat u in deze zelfstudie hebt gemaakt, te verwijderen. Met een gratis proefversie van Custom Vision kunt u maar twee projecten maken. Om het logo detectie-project op de [Custom Vision website](https://customvision.ai) te verwijderen, open **Projecten** en selecteer vervolgens het prullenbakpictogram onder **Mijn nieuwe Project**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een volledig functionele Xamarin.Forms-app ingesteld en verkend. Deze app maakt gebruik van de Custom Vision-service om logo’s te detecteren in mobiele camera-afbeeldingen. Leer vervolgens wat de aanbevolen procedures zijn voor het bouwen van een Custom Vision-model, zodat een zelf gemaakt model voor uw app krachtig en nauwkeurig werkt.

> [!div class="nextstepaction"]
> [De classificatie verbeteren](getting-started-improving-your-classifier.md)
