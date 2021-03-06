---
title: Translator Text-API methode voor trans-tele-tele-iseren
titleSuffix: Azure Cognitive Services
description: Converteer tekst in de ene taal van het ene script naar een ander script met de Translator Text-API methode transliter.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: e6bb1541b2b668796b352bebc68d59b4ade143e3
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837282"
---
# <a name="translator-text-api-30-transliterate"></a>Translator Text-API 3,0: trans-transcribatie

Hiermee wordt tekst in één taal van het ene script naar een ander script geconverteerd.

## <a name="request-url"></a>Aanvraag-URL

Een `POST` aanvraag verzenden naar:

```HTTP
https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0
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
  <tr>
    <td>language</td>
    <td>*Vereiste para meter*.<br/>Hiermee wordt de taal van de tekst opgegeven die van het ene naar het andere script moet worden geconverteerd. Mogelijke talen worden weer gegeven in het `transliteration` bereik dat wordt verkregen door de service te doorzoeken op de [ondersteunde talen](./v3-0-languages.md).</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td>*Vereiste para meter*.<br/>Hiermee geeft u het script op dat wordt gebruikt door de invoer tekst. Zoek naar [ondersteunde talen](./v3-0-languages.md) met behulp van het `transliteration` bereik om te zoeken naar beschik bare invoer scripts voor de geselecteerde taal.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td>*Vereiste para meter*.<br/>Hiermee geeft u het uitvoer script op. Zoek naar [ondersteunde talen](./v3-0-languages.md) met behulp van het `transliteration` bereik om uitvoer scripts te vinden die beschikbaar zijn voor de geselecteerde combi natie van invoer taal en invoer script.</td>
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

De hoofd tekst van de aanvraag is een JSON-matrix. Elk matrix element is een JSON-object met een teken reeks eigenschap met de naam `Text`. Dit is de teken reeks die moet worden geconverteerd.

```json
[
    {"Text":"こんにちは"},
    {"Text":"さようなら"}
]
```

De volgende beperkingen zijn van toepassing:

* De matrix kan Maxi maal 10 elementen bevatten.
* De tekst waarde van een matrix element mag niet langer zijn dan 1.000 tekens, inclusief spaties.
* De volledige tekst die in de aanvraag is opgenomen, mag niet langer zijn dan 5.000 tekens, inclusief spaties.

## <a name="response-body"></a>Antwoord tekst

Een geslaagde reactie is een JSON-matrix met één resultaat voor elk element in de invoer matrix. Een resultaat object bevat de volgende eigenschappen:

  * `text`: een teken reeks die het resultaat is van de conversie van de invoer teken reeks naar het uitvoer script.
  
  * `script`: een teken reeks waarmee het script wordt opgegeven dat wordt gebruikt in de uitvoer.

Een voor beeld van een JSON-antwoord is:

```json
[
    {"text":"konnnichiha","script":"Latn"},
    {"text":"sayounara","script":"Latn"}
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

In het volgende voor beeld ziet u hoe u twee Japanse teken reeksen omzet in geromeins Japans.

De JSON-nettolading voor de aanvraag in dit voor beeld:

```json
[{"text":"こんにちは","script":"jpan"},{"text":"さようなら","script":"jpan"}]
```

Als u krul gebruikt in een opdracht regel venster dat geen Unicode-tekens ondersteunt, neemt u de volgende JSON-nettolading op en slaat u deze op in een bestand met de naam `request.txt`. Zorg ervoor dat u het bestand opslaat met `UTF-8` code ring.

```
curl -X POST "https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0&language=ja&fromScript=Jpan&toScript=Latn" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d @request.txt
```
