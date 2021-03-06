---
title: Naslag informatie over spraak naar tekst-API (REST)-Speech Service
titleSuffix: Azure Cognitive Services
description: Meer informatie over het gebruik van de spraak-naar-tekst REST API. In dit artikel leert u over de opties voor autorisatie, opties voor query's, het structureren van een aanvraag en antwoord heeft ontvangen.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: erhopf
ms.openlocfilehash: 873898ce321100edbaa800d2436d0413c06ce175
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79220444"
---
# <a name="speech-to-text-rest-api"></a>REST API voor spraak-naar-tekst

Als alternatief voor de [Speech-SDK](speech-sdk.md)kunt u met de speech-service spraak-naar-tekst converteren met behulp van een rest API. Elk eindpunt dat toegankelijk is, is gekoppeld aan een regio. Uw toepassing is een abonnementssleutel vereist voor het eindpunt dat u van plan bent te gebruiken.

Voordat u de REST API voor spraak naar tekst gebruikt, moet u het volgende weten:

* Aanvragen die de REST API gebruiken en audio rechtstreeks verzenden, kunnen Maxi maal 60 seconden aan audio bevatten.
* De spraak-naar-tekst REST-API retourneert alleen de laatste resultaten. Gedeeltelijke resultaten zijn niet opgegeven.

Als het verzenden van meer audio een vereiste is voor uw toepassing, kunt u overwegen om de [spraak-SDK](speech-sdk.md) of een rest API op basis van een bestand te gebruiken, zoals [batch transcriptie](batch-transcription.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="regions-and-endpoints"></a>Regio's en -eindpunten

Het eind punt voor de REST API heeft de volgende indeling:

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

Vervang `<REGION_IDENTIFIER>` door de id die overeenkomt met de regio van uw abonnement uit deze tabel:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> De para meter language moet worden toegevoegd aan de URL om te voor komen dat er een 4xx HTTP-fout wordt ontvangen. De taal die is ingesteld op Amerikaans-Engels met het eind punt vs West is: `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US`.

## <a name="query-parameters"></a>Queryparameters

Deze parameters kunnen worden opgenomen in de query-tekenreeks van de REST-aanvraag.

| Parameter | Beschrijving | Vereiste / optioneel |
|-----------|-------------|---------------------|
| `language` | Hiermee geeft u de gesproken taal die wordt herkend. Zie [ondersteunde talen](language-support.md#speech-to-text). | Vereist |
| `format` | Hiermee geeft u de resultaatindeling. Geaccepteerde waarden zijn `simple` en `detailed`. Eenvoudige resultaten zijn `RecognitionStatus`, `DisplayText`, `Offset`en `Duration`. Gedetailleerde antwoorden bevatten meerdere resultaten met vertrouwen waarden en vier verschillende manieren. De standaard instelling is `simple`. | Optioneel |
| `profanity` | Geeft aan hoe grof taalgebruik in herkenningsresultaten worden verwerkt. Geaccepteerde waarden zijn `masked`, waarbij de woorden met sterretjes `removed`worden vervangen, waardoor alle woorden worden verwijderd uit het resultaat, of `raw`, waarbij de scheld woorden in het resultaat wordt opgenomen. De standaard instelling is `masked`. | Optioneel |
| `cid` | Wanneer u de [Custom speech Portal](how-to-custom-speech.md) gebruikt om aangepaste modellen te maken, kunt u aangepaste modellen gebruiken via hun **eind punt-id** op de pagina **implementatie** . Gebruik de **eind punt-id** als argument voor de `cid` query teken reeks parameter. | Optioneel |

## <a name="request-headers"></a>Aanvraagheaders

Deze tabel bevat de vereiste en optionele headers voor spraak-naar-tekst-aanvragen.

|Header| Beschrijving | Vereiste / optioneel |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | Uw abonnements sleutel voor spraak Services. | Deze header of `Authorization` is vereist. |
| `Authorization` | Een autorisatie token dat wordt voorafgegaan door het woord `Bearer`. Zie [Verificatie](#authentication) voor meer informatie. | Deze header of `Ocp-Apim-Subscription-Key` is vereist. |
| `Content-type` | Beschrijft de indeling en codec van de opgegeven gegevens. Geaccepteerde waarden zijn `audio/wav; codecs=audio/pcm; samplerate=16000` en `audio/ogg; codecs=opus`. | Vereist |
| `Transfer-Encoding` | Hiermee geeft u op of gesegmenteerde audiogegevens wordt verzonden, in plaats van één bestand. Gebruik alleen deze header als audiogegevens logische groepen te verdelen. | Optioneel |
| `Expect` | Als u gesegmenteerde overdracht wilt gebruiken, moet u `Expect: 100-continue`verzenden. De speech-service erkent de eerste aanvraag en wacht op aanvullende gegevens.| Vereist als het gesegmenteerde audiogegevens verzenden. |
| `Accept` | Als dit wordt gegeven, moet het worden `application/json`. De speech-service biedt resultaten in JSON. Sommige aanvraag raamwerken bieden een incompatibele standaard waarde. Het is raadzaam om altijd `Accept`te gebruiken. | Optioneel maar aanbevolen. |

## <a name="audio-formats"></a>Audio-indelingen

Audio wordt verzonden in de hoofd tekst van de HTTP-`POST` aanvraag. Het moet zich binnen een van de indelingen in deze tabel:

| Indeling | Codec | Bitrate | Samplefrequentie  |
|--------|-------|---------|--------------|
| WAV    | PCM   | 16-bits  | 16 kHz, mono |
| OGG    | OPUS  | 16-bits  | 16 kHz, mono |

>[!NOTE]
>De bovenstaande indelingen worden ondersteund via REST API en WebSocket in de speech-service. De [Speech SDK](speech-sdk.md) ondersteunt momenteel de WAV-indeling met PCM-codec en [andere indelingen](how-to-use-codec-compressed-audio-input-streams.md).

## <a name="sample-request"></a>Voorbeeld van een aanvraag

Het onderstaande voorbeeld bevat de hostnaam en de vereiste headers. Het is belangrijk te weten dat de service ook audiogegevens, die niet is opgenomen in dit voorbeeld wordt verwacht. Zoals vermeld eerder, wordt logische groepen te verdelen aanbevolen, maar niet vereist.

```HTTP
POST speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.stt.speech.microsoft.com
Transfer-Encoding: chunked
Expect: 100-continue
```

## <a name="http-status-codes"></a>HTTP-statuscodes

De HTTP-statuscode voor elke reactie geeft aan dat het slagen of veelvoorkomende fouten.

| HTTP-statuscode | Beschrijving | Mogelijke oorzaak |
|------------------|-------------|-----------------|
| `100` | Doorgaan | De eerste aanvraag is geaccepteerd. Doorgaan met het verzenden van de rest van de gegevens. (Gebruikt met gesegmenteerde overdracht) |
| `200` | OK | De aanvraag is uitgevoerd. de antwoordtekst is een JSON-object. |
| `400` | Ongeldig verzoek | Er is geen taal code gegeven, geen ondersteunde taal, ongeldig audio bestand, enzovoort. |
| `401` | Niet geautoriseerd | Abonnementssleutel of autorisatie-token is ongeldig in de regio is opgegeven of ongeldig eindpunt. |
| `403` | Verboden | Ontbrekende abonnementssleutel of autorisatie token. |

## <a name="chunked-transfer"></a>Gesegmenteerde overdracht

Gesegmenteerde overdracht (`Transfer-Encoding: chunked`) kan helpen de latentie van herkenning te verminderen. Hiermee kan de spraak service de verwerking van het audio bestand starten tijdens de verzen ding. De REST-API biedt geen tijdelijke of gedeeltelijke resultaten.

Dit codevoorbeeld laat zien hoe voor het verzenden van audio in segmenten. Alleen de eerste chunk moet de audio bestands-header bevatten. `request` is een `HttpWebRequest` object dat is verbonden met het juiste REST-eind punt. `audioFile` het pad is naar een audio bestand op schijf.

```csharp
var request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.Host = host;
request.ContentType = @"audio/wav; codecs=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";
request.AllowWriteStreamBuffering = false;

using (var fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{
    // Open a request stream and write 1024 byte chunks in the stream one at a time.
    byte[] buffer = null;
    int bytesRead = 0;
    using (var requestStream = request.GetRequestStream())
    {
        // Read 1024 raw bytes from the input audio file.
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        requestStream.Flush();
    }
}
```

## <a name="response-parameters"></a>Antwoord-parameters

Resultaten worden gegeven als JSON. De `simple` indeling bevat deze velden op het hoogste niveau.

| Parameter | Beschrijving  |
|-----------|--------------|
|`RecognitionStatus`|Status, zoals `Success` voor geslaagde herkenning. Zie de volgende tabel.|
|`DisplayText`|De herkende tekst na hoofdletter gebruik, interpunctie, inverse tekst normalisatie (conversie van gesp roken tekst naar kortere formulieren, zoals 200 voor "200" of "Dr. Smit" voor "Doctor Smith") en grove maskers. Alleen op succes is geïnstalleerd.|
|`Offset`|De tijd (in eenheden van 100 nanoseconden) waarmee de herkende spraak in de audiostream begint.|
|`Duration`|De duur (in eenheden van 100 nanoseconden) van de herkende spraak in de audio-stream.|

Het veld `RecognitionStatus` kan deze waarden bevatten:

| Status | Beschrijving |
|--------|-------------|
| `Success` | De herkenning is geslaagd en het `DisplayText` veld is aanwezig. |
| `NoMatch` | Spraak is gedetecteerd in de audiostream, maar er zijn geen woorden uit de doeltaal zijn afgestemd. Betekent meestal dat de opname-taal is een andere taal dan het account dat de gebruiker spreekt. |
| `InitialSilenceTimeout` | Het begin van de audiostream bevat alleen stilte en de time-out voor spraak-service. |
| `BabbleTimeout` | Het begin van de audiostream bevat alleen ruis, en de time-out voor spraak-service. |
| `Error` | De opname-service is een interne fout opgetreden en kan niet worden voortgezet. Probeer het opnieuw, indien mogelijk. |

> [!NOTE]
> Als de audio alleen uit scheld woorden bestaat en de `profanity` query parameter is ingesteld op `remove`, retourneert de service geen gesp roken resultaat.

De `detailed` indeling bevat dezelfde gegevens als de `simple` indeling, samen met `NBest`, een lijst met alternatieve interpretaties van hetzelfde herkennings resultaat. Deze resultaten worden geclassificeerd op basis van de meest waarschijnlijke kans op minst. De eerste vermelding is hetzelfde als het belangrijkste herkennings resultaat.  Wanneer u de `detailed` notatie gebruikt, wordt `DisplayText` als `Display` voor elk resultaat weer gegeven in de `NBest` lijst.

Elk object in de `NBest` lijst bevat:

| Parameter | Beschrijving |
|-----------|-------------|
| `Confidence` | De betrouwbaarheidsscore van de vermelding van 0,0 (geen vertrouwen) 1.0 (volledig vertrouwen) |
| `Lexical` | De lexicale vorm van de herkende tekst: de werkelijke woorden herkend. |
| `ITN` | De inverse-normalized-tekst ("canonieke") vorm van de herkende tekst, met telefoon getallen, getallen, afkortingen ("doctor smith' op 'dr smith') en andere transformaties toegepast. |
| `MaskedITN` | Het formulier toevoegen met grof taalgebruik maskering toegepast, indien aangevraagd. |
| `Display` | Het formulier weergegeven van de herkende tekst, met interpunctie hoofdletters en kleine letters toegevoegd. Deze para meter is hetzelfde als `DisplayText` opgegeven wanneer notatie is ingesteld op `simple`. |

## <a name="sample-responses"></a>Voorbeeld van reacties

Een typische reactie op `simple` herkenning:

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Een typische reactie op `detailed` herkenning:

```json
{
  "RecognitionStatus": "Success",
  "Offset": "1236645672289",
  "Duration": "1236645672289",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "remind me to buy five pencils",
        "ITN" : "remind me to buy 5 pencils",
        "MaskedITN" : "remind me to buy 5 pencils",
        "Display" : "Remind me to buy 5 pencils.",
      },
      {
        "Confidence" : "0.54",
        "Lexical" : "rewind me to buy five pencils",
        "ITN" : "rewind me to buy 5 pencils",
        "MaskedITN" : "rewind me to buy 5 pencils",
        "Display" : "Rewind me to buy 5 pencils.",
      }
  ]
}
```

## <a name="next-steps"></a>Volgende stappen

- [Uw proefabonnement voor Speech ophalen](https://azure.microsoft.com/try/cognitive-services/)
- [Akoestische modellen aanpassen](how-to-customize-acoustic-models.md)
- [Taalmodellen aanpassen](how-to-customize-language-model.md)
