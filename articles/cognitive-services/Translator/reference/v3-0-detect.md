---
title: Detectie methode Translator Text-API
titleSuffix: Azure Cognitive Services
description: Identificeer de taal van een stuk tekst met de Cognitive Services Translator Text-API detectie methode van Azure.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: 370f3b14c12fc05f181d6497b7069bbf1cf3c9cc
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837299"
---
# <a name="translator-text-api-30-detect"></a>Translator Text-API 3,0: detecteren

Hiermee wordt de taal van een stuk tekst bepaald.

## <a name="request-url"></a>Aanvraag-URL

Een `POST` aanvraag verzenden naar:

```HTTP
https://api.cognitive.microsofttranslator.com/detect?api-version=3.0
```

## <a name="request-parameters"></a>Aanvraag parameters

Aanvraag parameters die zijn door gegeven voor de query reeks zijn:

<table width="100%">
  <th width="20%">Query parameter</th>
  <th>Beschrijving</th>
  <tr>
    <td>API-versie</td>
    <td>*Vereiste para meter*.<br/>De versie van de API die door de client is aangevraagd. De waarde moet `3.0`zijn.</td>
  </tr>
</table> 

Aanvraag headers zijn onder andere:

<table width="100%">
  <th width="20%">Headers</th>
  <th>Beschrijving</th>
  <tr>
    <td>Verificatie header (s)</td>
    <td>De <em>vereiste aanvraag header</em>.<br/>Bekijk de <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">beschik bare opties voor authenticatie</a>.</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>De *vereiste aanvraag header*.<br/>Hiermee geeft u het inhouds type van de payload op. Mogelijke waarden zijn: `application/json`.</td>
  </tr>
  <tr>
    <td>Content-length</td>
    <td>De *vereiste aanvraag header*.<br/>De lengte van de aanvraag tekst.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Optioneel*.<br/>Een door de client gegenereerde GUID om de aanvraag uniek te identificeren. Houd er rekening mee dat u deze koptekst kunt weglaten als u de tracerings-ID opneemt in de query reeks met behulp van een query parameter met de naam `ClientTraceId`.</td>
  </tr>
</table> 

## <a name="request-body"></a>Aanvraagbody

De hoofd tekst van de aanvraag is een JSON-matrix. Elk matrix element is een JSON-object met een teken reeks eigenschap met de naam `Text`. Taal detectie wordt toegepast op de waarde van de eigenschap `Text`. Een voor beeld van een aanvraag tekst ziet er als volgt uit:

```json
[
    { "Text": "Ich würde wirklich gern Ihr Auto um den Block fahren ein paar Mal." }
]
```

De volgende beperkingen zijn van toepassing:

* De matrix kan Maxi maal 100 elementen bevatten.
* De tekst waarde van een matrix element mag niet langer zijn dan 10.000 tekens, inclusief spaties.
* De volledige tekst die in de aanvraag is opgenomen, mag niet langer zijn dan 50.000 tekens, inclusief spaties.

## <a name="response-body"></a>Antwoord tekst

Een geslaagde reactie is een JSON-matrix met één resultaat voor elke teken reeks in de invoer matrix. Een resultaat object bevat de volgende eigenschappen:

  * `language`: code van de gedetecteerde taal.

  * `score`: een float-waarde waarmee het vertrouwen in het resultaat wordt aangegeven. De Score ligt tussen nul en een en een lage score geeft een lage betrouw baarheid aan.

  * `isTranslationSupported`: een Booleaanse waarde die waar is als de gedetecteerde taal een van de talen is die voor tekst omzetting wordt ondersteund.

  * `isTransliterationSupported`: een Booleaanse waarde die True is als de gedetecteerde taal een van de talen die worden ondersteund voor vele.
  
  * `alternatives`: een matrix met andere mogelijke talen. Elk element van de matrix is een ander object met dezelfde eigenschappen die hierboven worden vermeld: `language`, `score`, `isTranslationSupported` en `isTransliterationSupported`.

Een voor beeld van een JSON-antwoord is:

```json
[
  {
    "language": "de",
    "score": 0.92,
    "isTranslationSupported": true,
    "isTransliterationSupported": false,
    "alternatives": [
      {
        "language": "pt",
        "score": 0.23,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      },
      {
        "language": "sk",
        "score": 0.23,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      }
    ]
  }
]
```

## <a name="response-headers"></a>Antwoord headers

<table width="100%">
  <th width="20%">Headers</th>
  <th>Beschrijving</th>
  <tr>
    <td>X-aanvraag-}</td>
    <td>De waarde die door de service is gegenereerd om de aanvraag te identificeren. Dit wordt gebruikt voor het oplossen van problemen.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Antwoord status codes

Hier volgen de mogelijke HTTP-status codes die een aanvraag retourneert. 

<table width="100%">
  <th width="20%">Status code</th>
  <th>Beschrijving</th>
  <tr>
    <td>200</td>
    <td>Geslaagd.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Een van de query parameters ontbreekt of is ongeldig. Corrigeer de aanvraag parameters voordat u het opnieuw probeert.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>De aanvraag kan niet worden geverifieerd. Controleer of de referenties zijn opgegeven en geldig zijn.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>De aanvraag is niet geautoriseerd. Controleer het fout bericht Details. Dit betekent vaak dat alle gratis vertalingen van een proef abonnement zijn gebruikt.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>De server heeft de aanvraag geweigerd omdat de aanvraag limieten voor de client is overschreden.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Er is een onverwachte fout opgetreden. Als de fout zich blijft voordoen, meldt u deze met: datum en tijd van de fout, aanvraag-id van de reactie header `X-RequestId`en de client-id van de aanvraag header `X-ClientTraceId`.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>De server is tijdelijk niet beschikbaar. Voer de aanvraag opnieuw uit. Als de fout zich blijft voordoen, meldt u deze met: datum en tijd van de fout, aanvraag-id van de reactie header `X-RequestId`en de client-id van de aanvraag header `X-ClientTraceId`.</td>
  </tr>
</table> 

Als er een fout optreedt, wordt door de aanvraag ook een JSON-fout bericht geretourneerd. De fout code is een getal van 6 cijfers, waarbij de HTTP-status code van 3 cijfers wordt gevolgd door een getal van drie cijfers om de fout verder te categoriseren. Algemene fout codes vindt u op de [pagina v3-Translator text-API-referentie](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors). 

## <a name="examples"></a>Voorbeelden

In het volgende voor beeld ziet u hoe u talen kunt ophalen die worden ondersteund voor tekst vertaling.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/detect?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'What language is this text written in?'}]"
```
