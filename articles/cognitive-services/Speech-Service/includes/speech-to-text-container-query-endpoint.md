---
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/21/2020
ms.author: dapine
ms.openlocfilehash: 9b4317064196c4ea3d761fd1a0bd43a764054fe6
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/22/2020
ms.locfileid: "77563289"
---
### <a name="speech-to-text-or-custom-speech-to-text"></a>Spraak-naar-tekst-of Custom Speech-naar-tekst

De container biedt Api's voor query-eind punten op basis van websockets die worden geopend via de [Speech SDK](../index.yml). De Speech SDK maakt standaard gebruik van online spraak Services. Als u de container wilt gebruiken, moet u de initialisatie methode wijzigen.

> [!TIP]
> Wanneer u de Speech SDK met containers gebruikt, hoeft u de Azure speech resource [Subscription-sleutel of een verificatie Bearer-token](../rest-speech-to-text.md#authentication)niet op te geven.

Zie de onderstaande voor beelden.

# <a name="c"></a>[C#](#tab/csharp)

Wijzigen van het gebruik van deze Azure-Cloud initialisatie aanroep:

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

voor deze aanroep via de container- [host](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet):

```csharp
var config = SpeechConfig.FromHost(
    new Uri("ws://localhost:5000"));
```
# <a name="python"></a>[Python](#tab/python)

Wijzigen van het gebruik van deze Azure-Cloud initialisatie aanroep:

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

voor deze aanroep via de container- [host](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python):

```python
speech_config = speechsdk.SpeechConfig(
    host="ws://localhost:5000")
```

***
