---
title: Over de Speech SDK-Speech-Service
titleSuffix: Azure Cognitive Services
description: De speech Software Development Kit (SDK) biedt uw toepassingen systeem eigen toegang tot de functies van de spraak service, waardoor het eenvoudiger wordt om software te ontwikkelen. In dit artikel biedt aanvullende informatie over de SDK voor Windows, Linux- en Android.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: dapine
ms.openlocfilehash: 984d2dfe07faa22756b4be167aa86a69806b1a84
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78331090"
---
# <a name="about-the-speech-sdk"></a>Info over de Speech-SDK

Spraak Software Development Kit (SDK) biedt uw toepassingen toegang tot de functies van de spraak-service, waardoor het gemakkelijker wordt om spraak ingeschakelde software te ontwikkelen. Op dit moment bieden de Sdk's toegang tot **spraak-naar-tekst**, **tekst-naar-spraak**, **spraak omzetting**, **intentie herkenning**en **bot Framework direct-lijn spraak kanaal**.

U kunt eenvoudig audio van een microfoon vastleggen, vanuit een stroom lezen of audio bestanden vanuit de opslag openen met de spraak-SDK. De Speech SDK ondersteunt WAV/PCM 16-bits, 16 kHz/8 kHz, Single Channel-audio voor spraak herkenning. Aanvullende audio-indelingen worden ondersteund met behulp van het [spraak-naar-tekst rest-eind punt](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) of de [batch transcriptie-service](https://docs.microsoft.com/azure/cognitive-services/speech-service/batch-transcription#supported-formats).

Op de pagina documentatie- [invoer](https://aka.ms/csspeech)vindt u een algemeen overzicht van de mogelijkheden en ondersteunde platforms.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>Download de SDK

# <a name="windows"></a>[Windows](#tab/windows)

> [!WARNING]
> De Speech SDK ondersteunt Windows 10 of hoger. Eerdere versies van Windows worden **niet ondersteund**.

Bij Windows, wordt de volgende talen ondersteund:

* C# (UWP- en .NET), C++: U kunt verwijzen naar en de nieuwste versie van onze spraak SDK NuGet-pakket. Het pakket bevat 32-bits en 64-bits-clientbibliotheken en beheerde (.NET)-bibliotheken. De SDK kan worden geïnstalleerd in Visual Studio met behulp van NuGet, [micro soft. CognitiveServices. speech](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech).

* Java: Kunt u verwijzen naar en de nieuwste versie van onze spraak SDK Maven-pakket, die ondersteuning biedt voor alleen Windows x64 gebruikt. Voeg `https://csspeechstorage.blob.core.windows.net/maven/` in uw Maven-project als een extra opslag plaats toe en referentie `com.microsoft.cognitiveservices.speech:client-sdk:1.8.0` als een afhankelijkheid.

# <a name="linux"></a>[Linux](#tab/linux)

> [!NOTE]
> Op dit moment ondersteunen we alleen Ubuntu 16,04, Ubuntu 18,04, Debian 9, Red Hat Enterprise Linux (RHEL) 8 en CentOS 8 voor de volgende doel architecturen:
> - x86 (Debian/Ubuntu), x64, ARM32 (Debian/Ubuntu) en ARM64 (Debian/Ubuntu) voor C++ ontwikkeling
> - x64, ARM32 (Debian/Ubuntu) en ARM64 (Debian/Ubuntu) voor Java
> - x64 voor .NET core en python

Zorg ervoor dat de vereiste bibliotheken zijn geïnstalleerd door de volgende shell-opdrachten uit te voeren:

Op Ubuntu:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libasound2
```

Op Debian 9:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.2 libasound2
```

Op RHEL/CentOS 8:

```sh
sudo yum update
sudo yum install alsa-lib openssl
```

> [!NOTE]
> Volg in RHEL/CentOS 8 de instructies voor het [configureren van openssl voor Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

* C#: U kunt verwijzen naar en de meest recente versie van onze spraak SDK NuGet-pakket. Om te verwijzen naar de SDK, het volgende pakketverwijzing toevoegen aan uw project:

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="1.8.0" />
  ```

* Java: Kunt u verwijzen naar en gebruik de nieuwste versie van onze spraak SDK Maven-pakket. Voeg `https://csspeechstorage.blob.core.windows.net/maven/` in uw Maven-project als een extra opslag plaats toe en referentie `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0` als een afhankelijkheid.

* C++: Down load de SDK als een [. tar-pakket](https://aka.ms/csspeech/linuxbinary) en pak de bestanden uit in een door u gewenste map. De volgende tabel bevat de SDK-mapstructuur:

  |Pad|Beschrijving|
  |-|-|
  |`license.md`|Licentie|
  |`ThirdPartyNotices.md`|Mededelingen van derden|
  |`include`|Header-bestanden voor C en C++|
  |`lib/x64`|Systeemeigen x64 bibliotheek voor het koppelen met uw toepassing|
  |`lib/x86`|Systeemeigen x86 bibliotheek voor het koppelen met uw toepassing|

  Voor het maken van een toepassing, kopiëren of verplaatsen van de vereiste binaire bestanden (en -bibliotheken) in uw ontwikkelomgeving. Deze waar nodig in uw build-proces opnemen.

# <a name="android"></a>[Android](#tab/android)

De Java SDK voor Android is verpakt als een [Aar (Android-bibliotheek)](https://developer.android.com/studio/projects/android-library), waaronder de benodigde bibliotheken en de vereiste Android-machtigingen. Het wordt gehost in een Maven-opslag plaats op `https://csspeechstorage.blob.core.windows.net/maven/` als pakket `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0`.

Het pakket van uw Android Studio-project gebruiken, moet u de volgende wijzigingen aanbrengen:

* Voeg in het bestand build. gradle van project niveau het volgende toe aan de sectie `repository`:

  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* Voeg in het bestand build. gradle van module niveau het volgende toe aan de sectie `dependencies`:

  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.7.0'
  ```

De Java SDK maakt ook deel uit van de [SDK voor spraak apparaten](speech-devices-sdk.md).

---

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Volgende stappen

* [Uw proefabonnement voor Speech ophalen](https://azure.microsoft.com/try/cognitive-services/)
* [Zie spraak herkennen inC#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
