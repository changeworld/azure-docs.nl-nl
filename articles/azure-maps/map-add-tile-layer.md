---
title: Een laag voor een tegel aan een kaart toevoegen | Microsoft Azure kaarten
description: In dit artikel wordt beschreven hoe u een tegel laag kunt bedekken op een kaart met behulp van de Microsoft Azure Maps Web SDK. Met de tegel lagen kunt u afbeeldingen op een kaart weer geven.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 61d7a11df499e6b740adb45968721b6a9bb1af22
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988597"
---
# <a name="add-a-tile-layer-to-a-map"></a>Een titellaag toevoegen aan een kaart

In dit artikel wordt beschreven hoe u een tegel laag kunt bedekken op de kaart. Met tegel lagen kunt u afbeeldingen boven op Azure Maps basis kaart tegels plaatsen. Zie [zoom niveaus en tegel raster](zoom-levels-and-tile-grid.md)voor meer informatie over Azure Maps tegel systeem.

Een tegel laag wordt in tegels van een server geladen. Deze installatie kopieën kunnen vooraf worden weer gegeven of dynamisch worden weer gegeven. Vooraf gerenderde installatie kopieën worden op dezelfde manier opgeslagen als andere installatie kopieën op een server met behulp van een naamgevings Conventie die de laag van de tegel begrijpt. Dynamisch gerenderde afbeeldingen gebruiken een service om de afbeeldingen in realtime te laden. Er worden drie verschillende naamgevings conventies voor tegel Services ondersteund door Azure Maps klasse [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) : 

* X, Y, zoom notatie-X is de kolom, Y is de rijpositie van de tegel in het tegel raster en de zoom notatie een waarde op basis van het zoom niveau.
* Quadkey notatie: Hiermee wordt de x-, y-en zoom informatie gecombineerd tot één teken reeks waarde. Deze teken reeks waarde wordt een unieke id voor één tegel.
* Begrenzingsvak: Geef een afbeelding op in de coördinaten notatie voor het begrenzingsvak `{west},{south},{east},{north}`. Deze indeling wordt vaak gebruikt door [Web Mapping Services (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> Een [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) is een uitstekende manier om grote gegevens sets op de kaart te visualiseren. U kunt niet alleen een tegel laag genereren op basis van een afbeelding, maar u kunt ook vector gegevens weer geven als een tegel laag. Door vector gegevens als een tegel laag te renderen, hoeft u alleen de tegels te laden die kleiner zijn dan de vector gegevens die ze vertegenwoordigen. Deze techniek wordt vaak gebruikt om miljoenen rijen met gegevens op de kaart weer te geven.

De tegel-URL die wordt door gegeven aan een tegel laag moet een HTTP-of HTTPS-URL zijn naar een TileJSON-resource of een tegel-URL-sjabloon die gebruikmaakt van de volgende para meters: 

* `{x}`-X-positie van de tegel. Ook moet `{y}` en `{z}`.
* `{y}`-Y-positie van de tegel. Ook moet `{x}` en `{z}`.
* `{z}`-zoom niveau van de tegel. Ook moet `{x}` en `{y}`.
* `{quadkey}`-tegel quadkey-id op basis van de naam Conventie voor Bing Maps-tegel systemen.
* `{bbox-epsg-3857}`-een teken reeks voor selectie kader met de indeling `{west},{south},{east},{north}` in het spatiale referentie systeem van EPSG 3857.
* `{subdomain}`: een tijdelijke aanduiding voor de waarden van het subdomein, indien opgegeven dat de `subdomain` worden toegevoegd.

## <a name="add-a-tile-layer"></a>Een titellaag toevoegen

 Dit voor beeld laat zien hoe u een tegel laag maakt die verwijst naar een set tegels. In dit voor beeld wordt gebruikgemaakt van het systeem x, y en zoomen. De bron van deze tegel laag is een weers radar-overlay van de [Iowa Environment Mesonet van de Iowa State University](https://mesonet.agron.iastate.edu/ogc/). Bij het weer geven van radar gegevens zien gebruikers in het ideale geval de labels van steden duidelijk wanneer ze door de kaart navigeren. Dit gedrag kan worden geïmplementeerd door de laag van de tegel onder de laag `labels` in te voegen.

```javascript
//Create a tile layer and add it to the map below the label layer.
//Weather radar tiles from Iowa Environmental Mesonet of Iowa State University.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

Hieronder ziet u het volledige programma voor het uitvoeren van code van de bovenstaande functionaliteit.

<br/>

<iframe height='500' scrolling='no' title='Tegel laag met X, Y en Z' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Bekijk de laag met de hoek van de pen <a href='https://codepen.io/azuremaps/pen/BGEQjG/'>met X, Y en Z</a> per Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-tile-layer"></a>Een laag voor een tegel aanpassen

De klasse van de tegel laag heeft veel stijl opties. Hier volgt een hulp programma om het uit te proberen.

<br/>

<iframe height='700' scrolling='no' title='Opties voor de laag van de tegel' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de <a href='https://codepen.io/azuremaps/pen/xQeRWX/'>laag opties</a> van de Pen naast Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de klassen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

Raadpleeg de volgende artikelen voor meer code voorbeelden om toe te voegen aan uw kaarten:

> [!div class="nextstepaction"]
> [Een afbeelding slaag toevoegen](./map-add-image-layer.md)
