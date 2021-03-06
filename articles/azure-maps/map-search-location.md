---
title: Zoek resultaten weer geven op een kaart | Microsoft Azure kaarten
description: In dit artikel leert u hoe u een zoek opdracht kunt uitvoeren met Microsoft Azure Maps Web SDK en de resultaten op de kaart kunt weer geven.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 13daf20e364b1e629b3ae13be2c9406da3702211
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209644"
---
# <a name="show-search-results-on-the-map"></a>Zoek resultaten weer geven op de kaart

Dit artikel laat u zien hoe u kunt zoeken naar interessante locaties en de zoek resultaten op de kaart kunt weer geven.

Er zijn twee manieren om te zoeken naar een locatie van belang. Een van de manieren is een service module te gebruiken voor het maken van een zoek opdracht. De andere manier is om een zoek opdracht te maken om te [Azure Maps fuzzy Search-API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) via de [API voor ophalen](https://fetch.spec.whatwg.org/). Beide manieren worden hieronder besproken.

## <a name="make-a-search-request-via-service-module"></a>Een zoek aanvraag indienen via de service module

<iframe height='500' scrolling='no' title='Zoek resultaten weer geven op een kaart (Service module)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>Zoek resultaten weer geven op een kaart (Service module)</a> door Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

In de bovenstaande code maakt het eerste blok een kaart object en stelt het verificatie mechanisme in voor het gebruik van het toegangs token. U kunt [een overzicht maken](./map-create.md) voor instructies.

Met het tweede code blok wordt een `TokenCredential` voor het verifiëren van HTTP-aanvragen voor Azure Maps met het toegangs token gemaakt. Vervolgens wordt de `TokenCredential` door gegeven aan `atlas.service.MapsURL.newPipeline()` en wordt een [pijplijn](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) instantie gemaakt. De `searchURL` vertegenwoordigt een URL voor het Azure Maps van [Zoek](https://docs.microsoft.com/rest/api/maps/search) bewerkingen.

Met het derde code blok wordt een gegevens bron object gemaakt met behulp van de klasse [Data Source](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) en worden er Zoek resultaten aan toegevoegd. Een [symbool laag](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) maakt gebruik van tekst of pictogrammen voor het weer geven van op punten gebaseerde gegevens die in de [gegevens bron](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) zijn verpakt als symbolen op de kaart.  Vervolgens wordt er een symbool laag gemaakt. De gegevens bron wordt toegevoegd aan de Symbol-laag, die vervolgens wordt toegevoegd aan de kaart.

Het vierde code blok maakt gebruik van de methode [SearchFuzzy](/javascript/api/azure-maps-rest/atlas.service.models.searchgetsearchfuzzyoptionalparams) in de [module service](how-to-use-services-module.md). U kunt met behulp van de zoek actie voor het maken van een vrije tekst zoeken naar interessante [rest API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) . Aanvragen ophalen voor de zoek actie voor fuzzy-API kan elke combi natie van fuzzy invoer verwerken. Een verzameling geojson-functies uit het antwoord wordt vervolgens geëxtraheerd met behulp van de `geojson.getFeatures()` methode en toegevoegd aan de gegevens bron, waardoor automatisch de gegevens worden weer gegeven op de kaart via de laag van het symbool.

Het laatste code blok past de camera grenzen voor de kaart aan met behulp van de eigenschap [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) van de kaart.

De zoek aanvraag, de gegevens bron, de symbool-laag en de camera grenzen bevinden zich in de [gebeurtenislistener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) van de kaart. We willen er zeker van zijn dat de resultaten worden weer gegeven nadat de kaart volledig is geladen.


## <a name="make-a-search-request-via-fetch-api"></a>Een zoek aanvraag indienen via de API voor ophalen

<iframe height='500' scrolling='no' title='Zoek resultaten weer geven op een kaart' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Bekijk de pen <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>Zoek resultaten weer geven op een kaart</a> op basis van Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

In de bovenstaande code maakt het eerste code blok een kaart object. Het verificatie mechanisme wordt ingesteld voor het gebruik van het toegangs token. U kunt [een overzicht maken](./map-create.md) voor instructies.

Het tweede code blok maakt een URL voor het maken van een zoek opdracht. Er worden ook twee matrices gemaakt voor het opslaan van grenzen en pincodes voor zoek resultaten.

Het derde code blok maakt gebruik van de [API voor ophalen](https://fetch.spec.whatwg.org/). De [ophaal-API](https://fetch.spec.whatwg.org/) wordt gebruikt om een aanvraag in te stellen voor [Azure Maps fuzzy Search-API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) om te zoeken naar de gewenste punten. De fuzzy Search-API kan elke combi natie van fuzzy invoer verwerken. Vervolgens wordt het antwoord verwerkt en geparseerd en worden de resultaten van de zoek opdracht toegevoegd aan de searchPins-matrix.

Met het vierde code blok wordt een gegevens bron object gemaakt met behulp van de klasse [Data Source](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) . In de code voegen we Zoek resultaten toe aan het bron object. Een [symbool laag](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) maakt gebruik van tekst of pictogrammen voor het weer geven van op punten gebaseerde gegevens die in de [gegevens bron](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) zijn verpakt als symbolen op de kaart. Vervolgens wordt er een symbool laag gemaakt. De gegevens bron wordt toegevoegd aan de Symbol-laag, die vervolgens wordt toegevoegd aan de kaart.

Met het laatste code blok wordt een [BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) -object gemaakt. Het gebruikt de matrix met resultaten en vervolgens worden de camera grenzen voor de kaart aangepast met behulp van de [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-)van de kaart. Vervolgens worden de resultaat pinnen weer gegeven.

De zoek aanvraag, de gegevens bron, de symbool-laag en de grenzen van de camera worden ingesteld in de [gebeurtenislistener van de](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) kaart, om ervoor te zorgen dat de resultaten worden weer gegeven nadat de kaart volledig is geladen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over **fuzzy Search**:

> [!div class="nextstepaction"]
> [Azure Maps fuzzy Search-API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)

Meer informatie over de klassen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [Diagram](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Raadpleeg de volgende artikelen voor voor beelden van volledige code:

> [!div class="nextstepaction"]
> [Informatie ophalen uit een coördinaat](./map-get-information-from-coordinate.md)
<!-- Comment added to suppress false positive warning -->
> [!div class="nextstepaction"]
> [Route beschrijving van A naar B weer geven](./map-route.md)
