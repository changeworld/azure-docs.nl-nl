---
title: bestand opnemen
description: bestand opnemen
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 02/14/2020
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: 53e6382cf8d046b2c9818b906890bc64642fd2ed
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/15/2020
ms.locfileid: "77372014"
---
Gebruik de Language Understanding (LUIS)-ontwerp-client bibliotheek voor .NET voor het volgende:

* Een app maken
* Intenties, entiteiten en voor beeld-uitingen toevoegen
* Functies toevoegen, zoals een woordgroepen lijst
* App trainen en publiceren

[Referentie documentatie](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet) | [bron code](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Authoring) van de bibliotheek | het [ontwerp pakket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/) | [ C# voor beelden](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/LUIS/LUIS.cs)

## <a name="prerequisites"></a>Vereisten

* Language Understanding-Portal account (LUIS): [Maak er gratis een](https://www.luis.ai) .
* De huidige versie van [.net core](https://dotnet.microsoft.com/download/dotnet-core).


## <a name="setting-up"></a>Instellen

### <a name="get-your-language-understanding-luis-starter-key"></a>Uw Language Understanding-starter sleutel (LUIS) ophalen

Haal uw [Start sleutel](../luis-how-to-azure-subscription.md#starter-key) op door een Luis-ontwerp bron te maken. Behoud de sleutel en de regio van de sleutel voor de volgende stap.

### <a name="create-an-environment-variable"></a>Een omgevings variabele maken

Met uw sleutel, en de regio voor de sleutel, maakt u twee omgevings variabelen voor verificatie:

* `COGNITIVESERVICE_AUTHORING_KEY`: de resource sleutel voor het verifiëren van uw aanvragen.
* `COGNITIVESERVICE_REGION`-de regio die aan uw sleutel is gekoppeld. Bijvoorbeeld `westus`.

Volg de instructies voor uw besturings systeem.

#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx COGNITIVESERVICE_AUTHORING_KEY <replace-with-your-authoring-key>
setx COGNITIVESERVICE_REGION <replace-with-your-authoring-region>
```

Nadat u de omgevings variabele hebt toegevoegd, start u het console venster opnieuw.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export COGNITIVESERVICE_AUTHORING_KEY=<replace-with-your-authoring-key>
export COGNITIVESERVICE_REGION=<replace-with-your-authoring-region>
```

Nadat u de omgevingsvariabele toevoegt, voert u `source ~/.bashrc` uit vanuit het consolevenster om de wijzigingen van kracht te laten worden.

#### <a name="macostabunix"></a>[MacOS](#tab/unix)

Bewerk uw `.bash_profile`en voeg de omgevings variabele toe:

```bash
export COGNITIVESERVICE_AUTHORING_KEY=<replace-with-your-authoring-key>
export COGNITIVESERVICE_REGION=<replace-with-your-authoring-region>
```

Nadat u de omgevingsvariabele toevoegt, voert u `source .bash_profile` uit vanuit het consolevenster om de wijzigingen van kracht te laten worden.
***

### <a name="create-a-new-c-application"></a>Een nieuwe C# toepassing maken

Maak een nieuwe .NET core-toepassing in uw voorkeurs editor of IDE.

1. Gebruik in een console venster (zoals cmd, Power shell of bash) de opdracht DotNet `new` om een nieuwe console-app te maken met de naam `language-understanding-quickstart`. Met deze opdracht maakt u een eenvoudig ' C# Hallo wereld '-project met één bron bestand: `Program.cs`.

    ```dotnetcli
    dotnet new console -n language-understanding-quickstart
    ```

1. Wijzig uw directory in de zojuist gemaakte app-map.

1. U kunt de toepassing samen stellen met:

    ```dotnetcli
    dotnet build
    ```

    De build-uitvoer mag geen waarschuwingen of fouten bevatten.

    ```console
    ...
    Build succeeded.
     0 Warning(s)
     0 Error(s)
    ...
    ```


### <a name="install-the-sdk"></a>De SDK installeren

Installeer in de toepassingsmap de Language Understanding (LUIS) ontwerp-client bibliotheek voor .NET met de volgende opdracht:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring --version 3.0.0
```

Als u de Visual Studio IDE gebruikt, is de client bibliotheek beschikbaar als een downloadbaar NuGet-pakket.


## <a name="object-model"></a>Object model

De ontwerp-client voor Language Understanding (LUIS) is een [LUISAuthoringClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-dotnet) -object dat wordt geverifieerd bij Azure, dat uw ontwerp sleutel bevat.

Nadat de client is gemaakt, gebruikt u deze client voor toegang tot de functionaliteit, waaronder:

* Apps: [maken](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync?view=azure-dotnet), [verwijderen](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.deleteasync?view=azure-dotnet), [publiceren](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync?view=azure-dotnet)
* Voor beeld van uitingen- [toevoegen per batch](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.batchasync?view=azure-dotnet), [verwijderen op id](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.deleteasync?view=azure-dotnet)
* Functies: [woordgroepen lijsten](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.featuresextensions.addphraselistasync?view=azure-dotnet) beheren
* Model- [intents](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions?view=azure-dotnet) en entiteiten beheren
* Patroon: [patronen](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.patternextensions?view=azure-dotnet) beheren
* [Train de app](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.trainversionasync?view=azure-dotnet) en vraag naar [trainings status](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync?view=azure-dotnet)
* [Versies](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.versionsextensions?view=azure-dotnet) : beheren met klonen, exporteren en verwijderen


## <a name="code-examples"></a>Code voorbeelden

Deze code fragmenten laten zien hoe u het volgende kunt doen met de Language Understanding (LUIS)-ontwerp-client bibliotheek voor .NET:

* [Een app maken](#create-a-luis-app)
* [Entiteiten toevoegen](#create-entities-for-the-app)
* [Intents toevoegen](#create-intent-for-the-app)
* [Voor beeld uitingen toevoegen](#add-example-utterance-to-intent)
* [De app trainen](#train-the-app)
* [De app publiceren](#publish-a-language-understanding-app)

## <a name="add-the-dependencies"></a>De afhankelijkheden toevoegen

Open het *Program.cs* -bestand in de map van het project in uw voorkeurs editor of IDE. Vervang de bestaande `using` code door de volgende `using`-instructies:

[!code-csharp[Using statements](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=Dependencies)]

## <a name="authenticate-the-client"></a>De client verifiëren

1. Maak een variabele voor het beheren van uw ontwerp sleutel die wordt opgehaald uit een omgevings variabele met de naam `COGNITIVESERVICES_AUTHORING_KEY`. Als u de omgevings variabele hebt gemaakt nadat de toepassing is gestart, moet de editor, IDE of shell die deze uitvoert, worden gesloten en opnieuw worden geladen om toegang te krijgen tot de variabele. De methoden worden later gemaakt.

1. Maak variabelen om uw ontwerp regio en-eind punt te bewaren. De regio van uw ontwerp sleutel is afhankelijk van waar u ontwerpt. De [drie ontwerp regio's](../luis-reference-regions.md) zijn:

    * Australië-`australiaeast`
    * Europa-`westeurope`
    * Verenigde Staten en andere regio's-`westus` (standaard)

    [!code-csharp[Authorization to resource key](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=Variables)]

1. Maak een [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.apikeyserviceclientcredentials?view=azure-dotnet) -object met uw sleutel en gebruik het met uw eind punt om een [LUISAuthoringClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-dotnet) -object te maken.

    [!code-csharp[Create LUIS client object](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>Een LUIS-app maken

1. Maak een LUIS-app die de intenties, entiteiten en voorbeeld uitingen van het model voor natuurlijke taal verwerking (NLP) bevat.

1. Een [ApplicationCreateObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.applicationcreateobject?view=azure-dotnet)maken. De naam en taal cultuur zijn vereiste eigenschappen.

1. Roep de [apps. AddAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync?view=azure-dotnet) -methode aan. Het antwoord is de App-ID.

    [!code-csharp[Create a LUIS app](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringCreateApplication)]

## <a name="create-intent-for-the-app"></a>Intentie maken voor de app
Het primaire object in het model van een LUIS-app is het doel. De intentie Lijnt uit met een groepering van gebruikers utterance _voornemens_. Een gebruiker kan een vraag stellen of een overzicht maken van een bepaalde _gewenste_ reactie van een bot (of een andere client toepassing). Voor beelden van bedoelingen zijn het reserveren van een vlucht, waarin wordt gevraagd om de weer gave van een bestemmings plaats en om contact op te nemen met de klanten service.

Maak een [ModelCreateObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modelcreateobject?view=azure-dotnet) met de naam van de unieke intentie en geef de App-ID, versie-id en de ModelCreateObject door aan de methode [model. AddIntentAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addintentasync?view=azure-dotnet) . Het antwoord is de intentie-ID.

[!code-csharp[Create intent](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringAddIntents)]

## <a name="create-entities-for-the-app"></a>Entiteiten voor de app maken

Hoewel entiteiten niet vereist zijn, worden ze in de meeste apps gevonden. De entiteit extraheert informatie uit de utterance van de gebruiker, die nodig is om de bedoeling van de gebruiker te fullfilen. Er zijn verschillende typen [vooraf ontwikkelde](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addprebuiltasync?view=azure-dotnet) en aangepaste entiteiten met hun eigen DTO-modellen (Data Transformation object).  Algemene vooraf gemaakte entiteiten die u wilt toevoegen aan uw app, zijn [Number](../luis-reference-prebuilt-number.md), [datetimeV2](../luis-reference-prebuilt-datetimev2.md), [geographyV2](../luis-reference-prebuilt-geographyv2.md), [Ordinal](../luis-reference-prebuilt-ordinal.md).

Met deze methode **AddEntities** maakt u een `Location` eenvoudige entiteit met twee rollen, een `Class` eenvoudige entiteit, een `Flight` samengestelde entiteit en voegt u verschillende vooraf gemaakte entiteiten toe.

Het is belang rijk te weten dat entiteiten niet zijn gemarkeerd met een bedoeling. Ze kunnen en meestal worden toegepast op veel intenties. Alleen voor beelden van gebruikers uitingen zijn gemarkeerd voor een specifiek, enkelvoudig doel.

Methode voor het maken van entiteiten maakt deel uit van de [model](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions?view=azure-dotnet) klasse. Elk entiteits type heeft een eigen DTO-model (Data Transformation object) dat meestal het woord `model` bevat in de naam ruimte [model](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models?view=azure-dotnet) .

[!code-csharp[Create entities](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringAddEntities)]

## <a name="add-example-utterance-to-intent"></a>Bijvoorbeeld utterance toevoegen aan intentie

De app heeft voor beelden van uitingen nodig om de voor delen en het ophalen van entiteiten van een utterance te bepalen. De voor beelden moeten gericht zijn op een specifieke, afzonderlijke intentie en alle aangepaste entiteiten moeten worden gemarkeerd. Vooraf gemaakte entiteiten hoeven niet te worden gemarkeerd.

Een voor beeld van een uitingen toevoegen door een lijst met [ExampleLabelObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.examplelabelobject?view=azure-dotnet) -objecten te maken, één object voor elk voor beeld utterance. Elk voor beeld moet alle entiteiten markeren met een woorden lijst met naam/waarde-paren van de naam van de entiteit en de waarde van de entiteit. De waarde van de entiteit moet exact zo zijn als deze wordt weer gegeven in de tekst van het voor beeld utterance.

Call- [voor beelden. BatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.batchasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_ExamplesExtensions_BatchAsync_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_IExamples_System_Guid_System_String_System_Collections_Generic_IList_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_Models_ExampleLabelObject__System_Threading_CancellationToken_) met de App-ID, versie-id en de lijst met voor beelden. De aanroep reageert met een lijst met resultaten. U moet het resultaat van elk voor beeld controleren om er zeker van te zijn dat het is toegevoegd aan het model.

[!code-csharp[Add example utterances to a specific intent](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringBatchAddUtterancesForIntent)]

De methoden **CreateUtterance** en **CreateLabel** zijn hulp middelen om u te helpen bij het maken van objecten.

## <a name="train-the-app"></a>De app trainen

Zodra het model is gemaakt, moet de LUIS-app worden getraind voor deze versie van het model. Een getraind model kan worden gebruikt in een [container](../luis-container-howto.md)of naar de staging-of product sleuven worden [gepubliceerd](../luis-how-to-publish-app.md) .

De methode [Train. TrainVersionAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions?view=azure-dotnet) moet de App-ID en de versie-id hebben.

Een zeer klein model, zoals deze Quick Start laat zien, wordt zeer snel getraind. Voor toepassingen op productie niveau moet de training van de app een polling aanroep naar de methode [GetStatusAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_TrainExtensions_GetStatusAsync_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_ITrain_System_Guid_System_String_System_Threading_CancellationToken_) bevatten om te bepalen of de training is geslaagd. Het antwoord is een lijst met [ModelTrainingInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo?view=azure-dotnet) -objecten met een afzonderlijke status voor elk object. De training kan alleen volledig worden beschouwd als alle objecten zijn voltooid.

[!code-csharp[Train the app's version](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringTrainVersion)]

## <a name="publish-a-language-understanding-app"></a>Een Language Understanding-app publiceren

Publiceer de LUIS-app met behulp van de methode [PublishAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync?view=azure-dotnet) . Hiermee wordt de huidige getrainde versie gepubliceerd naar de opgegeven sleuf op het eind punt. Uw client toepassing gebruikt dit eind punt om gebruikers uitingen te verzenden voor het voors pellen van intentie en het uitpakken van entiteiten.

[!code-csharp[Create entities](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringPublishVersionAndSlot)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit met de opdracht `dotnet run` van de toepassingsmap.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u wilt opruimen, kunt u de LUIS-app verwijderen. Het verwijderen van de app is voltooid met de [apps. DeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.deleteasync?view=azure-dotnet) -methode. U kunt de app ook uit de [Luis-Portal](https://www.luis.ai)verwijderen.