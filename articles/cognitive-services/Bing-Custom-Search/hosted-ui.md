---
title: Een gehoste gebruikers interface configureren voor Bing Aangepaste zoekopdrachten | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: Gebruik dit artikel om een gehoste gebruikers interface voor Bing Aangepaste zoekopdrachten te configureren en te integreren.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: aahi
ms.openlocfilehash: 2cc89bf57167db75404c044f58d18ab48edfaf38
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854072"
---
# <a name="configure-your-hosted-ui-experience"></a>Uw gehoste UI-ervaring configureren

Bing Aangepaste zoekopdrachten biedt een gehoste gebruikers interface die u eenvoudig kunt integreren in uw webpagina's en webtoepassingen als een Java script-code fragment. Met de Bing Aangepaste zoekopdrachten Portal kunt u de opties voor indeling, kleur en zoeken van de gebruikers interface configureren.



## <a name="configure-the-custom-hosted-ui"></a>De aangepaste gehoste gebruikers interface configureren

Als u een gehoste gebruikers interface voor uw webtoepassingen wilt configureren, volgt u deze stappen. Wanneer u wijzigingen aanbrengt, krijgt u in het deel venster aan de rechter kant een voor beeld van uw gebruikers interface. De weer gegeven Zoek resultaten zijn geen werkelijke resultaten voor uw exemplaar.

1. Meld u aan bij Bing Aangepaste zoekopdrachten [Portal](https://customsearch.ai).  
  
2. Selecteer uw Bing Aangepaste zoekopdrachten-exemplaar.

3. Klik op het tabblad **Hosted UI**.  
  
4. Selecteer een lay-out.

    |  |  |
    |---------|---------|
    |Zoek balk en resultaten (standaard)    | Hiermee wordt een zoekvak met zoek resultaten weer gegeven daaronder.         |
    |Alleen resultaten     | Alleen Zoek resultaten worden weer gegeven, zonder zoekvak. Wanneer u deze indeling gebruikt, moet u de zoek query (`&q=<query string>`) opgeven. Voeg de query-para meter toe aan de aanvraag-URL in het Java script-fragment of de koppeling naar het HTML-eind punt.        |
    |Pop-over     | Voorziet in een zoekvak en de zoek resultaten worden weer gegeven in een schuif bare overlay.        |
    
5. Selecteer een kleurenthema. U kunt de kleuren aanpassen aan uw toepassing door te klikken op **thema aanpassen**. Als u een kleur wilt wijzigen, voert u de RGB hexadecimale waarde van de kleur `#366eb8`(bijvoorbeeld) in of klikt u op de kleur voorbeeld.

   U kunt een voor beeld bekijken van uw wijzigingen aan de rechter kant van de portal. **Als u op opnieuw instellen** klikt, worden de standaard kleuren voor het geselecteerde thema hersteld.

   > [!NOTE]
   > Houd bij het kiezen van kleuren rekening met toegankelijkheid.

6. Geef onder **aanvullende configuraties**waarden op die geschikt zijn voor uw app. Deze instellingen zijn optioneel. Zie het voorbeeld venster aan de rechter kant om het effect van het Toep assen of verwijderen van deze items te bekijken. Beschik bare configuratie opties zijn:  

7. Voer de sleutel voor het zoek abonnement in of kies er een in de vervolg keuzelijst. De vervolg keuzelijst wordt gevuld met sleutels uit de abonnementen van uw Azure-account. Zie [Cognitive Services-API-account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).  

8. Als u automatische suggesties hebt ingeschakeld, voert u de abonnements sleutel voor Automatische suggestie in of kiest u er een in de vervolg keuzelijst. De vervolg keuzelijst wordt gevuld met sleutels uit de abonnementen van uw Azure-account. Voor aangepaste automatische suggesties is een specifieke abonnementweergave vereist. Zie de [prijzen](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/).

[!INCLUDE [publish or revert](./includes/publish-revert.md)]

## <a name="consume-custom-ui"></a>Aangepaste gebruikers interface gebruiken

Als u de gehoste gebruikers interface wilt gebruiken, hetzij: 

- Het script toevoegen aan uw webpagina  
  
  ```html
  <html>
      <body>
          <script type="text/javascript" 
              id="bcs_js_snippet"
              src="https://ui.customsearch.ai /api/ux/rendering-js?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate&version=latest&q=">
          </script>
      </body>    
  </html>
  ```

- Of gebruik de volgende URL in een webbrowser.   
  
  `https://ui.customsearch.ai/hosted?customConfig=YOUR-CUSTOM-CONFIG-ID`  
  
  > [!NOTE]
  > Voeg indien nodig de volgende query parameters toe aan de URL. Zie [aangepaste zoekopdrachten-API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters) Reference (Engelstalig) voor meer informatie over deze para meters.
  >
  > - q
  > - mkt
  > - SafeSearch
  > - setlang

  > [!IMPORTANT]
  > De pagina kan uw privacyverklaring of andere mede delingen en voor waarden niet weer geven. De geschiktheid voor uw gebruik kan variëren.  

Ga naar **eind punten** onder het tabblad **productie** voor aanvullende informatie, inclusief uw aangepaste configuratie-ID.

## <a name="configuration-options"></a>Configuratie-opties

U kunt het gedrag van uw gehoste gebruikers interface configureren door te klikken op **aanvullende configuraties**en waarden op te geven. Deze instellingen zijn optioneel. Zie het voorbeeld venster aan de rechter kant om het effect van het Toep assen of verwijderen van deze items te bekijken. 

### <a name="web-search-configurations"></a>Webzoekactie configuraties

|  |  |
|---------|---------|
|Webresultaten ingeschakeld    | Hiermee wordt bepaald of zoeken op internet is ingeschakeld (boven aan de pagina wordt het tabblad Web weer geven)        |
|Automatische suggestie inschakelen     | Hiermee wordt bepaald of aangepaste automatische suggesties zijn ingeschakeld (Zie de [prijzen](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/) voor extra kosten).        |
|Webresultaten per pagina    | Aantal Zoek resultaten van de webtoepassingen tegelijk worden weer gegeven (de maximum waarde is 50 resultaten per pagina).        |
|Bijschrift voor afbeelding   | Hiermee wordt bepaald of afbeeldingen worden weer gegeven met zoek resultaten.|


De volgende configuraties worden weer gegeven als u op **geavanceerde configuraties weer geven**klikt:


|  | |
|---------|---------|
|Markeer woorden     | Hiermee wordt bepaald of resultaten worden weer gegeven met een vetgedrukte zoek term.         |
|Doel van koppeling    |  Bepaalt of de webpagina wordt geopend in een nieuw browser tabblad (leeg) of hetzelfde browser tabblad (zelf) wanneer de gebruiker op een Zoek resultaat klikt.        |

### <a name="image-search-configurations"></a>Configuraties voor afbeeldingen zoeken

| | |
|---------|---------|
|Resultaten van afbeelding ingeschakeld     | Hiermee wordt bepaald of de afbeelding zoeken is ingeschakeld (boven aan de pagina wordt een tabblad afbeeldingen weer geven).            |
|Afbeeldings resultaten per pagina     | Aantal Zoek resultaten van de afbeelding dat tegelijk wordt weer gegeven (de maximum waarde is 150 resultaten per pagina).          |

De volgende configuratie wordt weer gegeven als u op **geavanceerde configuraties weer geven**klikt.  
  
| | |
|---------|---------|
| Filters inschakelen     | Voegt filters toe die de gebruiker kan gebruiken om de afbeeldingen te filteren die door Bing worden geretourneerd. De gebruiker kan bijvoorbeeld de resultaten filteren voor alleen GIF-animaties.|

### <a name="video-search-configurations"></a>Configuraties voor video zoeken

|  | |
|---------|---------|
|Video resultaten ingeschakeld     | Hiermee wordt bepaald of zoeken in video is ingeschakeld (het tabblad Video's wordt boven aan de pagina weer geven).           |
|Video resultaten per pagina   | Aantal Zoek resultaten van de video dat tegelijk wordt weer gegeven (de maximum waarde is 150 resultaten per pagina).        |

De volgende configuratie wordt weer gegeven als u op **geavanceerde configuraties weer geven**klikt.  
  
|  | |
|---------|---------|
|Filters inschakelen    | Hiermee voegt u filters toe die de gebruiker kan gebruiken voor het filteren van de Video's die door Bing worden geretourneerd. De gebruiker kan bijvoorbeeld de resultaten filteren voor Video's met een specifieke resolutie of Video's die in de afgelopen 24 uur zijn gedetecteerd.          |

### <a name="miscellaneous-configurations"></a>Diverse configuraties


| |  |
|---------|---------|
|Paginatitel   | De tekst die wordt weer gegeven in het titel gebied van de pagina met zoek resultaten (niet voor pop-over-indeling).        |
|Thema van de werk balk    | Bepaalt de achtergrond kleur van het titel gebied van de pagina met zoek resultaten. |

De volgende configuraties worden weer gegeven als u op **geavanceerde configuraties weer geven**klikt.  

|Kolom1  |Kolom2  |
|---------|---------|
|Tijdelijke aanduiding voor tekst in zoekvak   | Tekst die wordt weer gegeven in het zoekvak vóór invoer.        |
|URL van titel koppeling    |Doel voor de koppeling titel.         |
|URL logo     | Afbeelding die naast de titel wordt weer gegeven.         |
|Favicon    | Pictogram dat wordt weer gegeven in de titel balk van de browser.          |

De volgende configuraties zijn alleen van toepassing als u gebruikmaakt van de gehoste gebruikers interface via het HTML-eind punt (ze zijn niet van toepassing als u het Java script-fragment gebruikt).

- Paginatitel
- Thema van de werk balk
- URL van titel koppeling
- URL logo
- Faviicon-URL  

## <a name="next-steps"></a>Volgende stappen

- [Decoratiemarkeringen gebruiken om tekst te markeren](../bing-web-search/hit-highlighting.md)
- [Bladeren door webpagina's](./page-webpages.md)
