---
title: Ruimtelijke gegevens lezen en schrijven | Microsoft Azure kaarten
description: Meer informatie over het lezen en schrijven van gegevens met behulp van de ruimtelijke IO-module, die wordt verschaft door Azure Maps Web-SDK.
author: farah-alyasari
ms.author: v-faalya
ms.date: 03/01/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 458b307cf1158c467100e032e3f789462e8fdcca
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78370906"
---
# <a name="read-and-write-spatial-data"></a>Ruimtelijke gegevens lezen en schrijven

In de volgende tabel ziet u de indeling van ruimtelijke bestanden die worden ondersteund voor lees-en schrijf bewerkingen met de ruimtelijke IO-module.

| Gegevens indeling       | Lezen | Schrijven |
|-------------------|------|-------|
| GeoJSON           | ✓  |  ✓  |
| GeoRSS            | ✓  |  ✓  |
| GML               | ✓  |  ✓  |
| GPX               | ✓  |  ✓  |
| KML               | ✓  |  ✓  |
| KMZ               | ✓  |  ✓  |
| Ruimtelijke CSV       | ✓  |  ✓  |
| Bekende tekst   | ✓  |  ✓  |

In deze volgende secties vindt u een overzicht van de verschillende hulpprogram ma's voor het lezen en schrijven van ruimtelijke gegevens met behulp van de ruimtelijke IO-module.

## <a name="read-spatial-data"></a>Ruimtelijke gegevens lezen

De functie `atlas.io.read` is de belangrijkste functie die wordt gebruikt voor het lezen van veelgebruikte ruimtelijke gegevens indelingen, zoals KML, GPX, GeoRSS, geojson en CSV files met ruimtelijke gegevens. Deze functie kan ook gecomprimeerde versies van deze indelingen als een zip-bestand of een KMZ-bestand lezen. De KMZ-bestands indeling is een gecomprimeerde versie van KML die ook assets kan bevatten, zoals installatie kopieën. De Lees functie kan ook worden gebruikt in een URL die verwijst naar een bestand in een van deze indelingen. Url's moeten worden gehost op een eind punt waarvoor CORs is ingeschakeld of er moet een proxy service worden gegeven in de Lees opties. De proxy service wordt gebruikt voor het laden van bronnen op domeinen waarvoor CORs niet is ingeschakeld. De functie Read retourneert een Promise om de afbeeldings pictogrammen toe te voegen aan de kaart en gegevens asynchroon te verwerken om de impact op de UI-thread te minimaliseren.

Bij het lezen van een gecomprimeerd bestand, als een zip-of KMZ, wordt het uitgepakt en gescand op het eerste geldige bestand. Bijvoorbeeld doc. KML of een bestand met een andere geldige extensie, zoals:. KML,. XML, geojson,. json,. CSV,. TSV of. txt. Vervolgens worden afbeeldingen waarnaar wordt verwezen in KML-en GeoRSS-bestanden vooraf geladen om te controleren of ze toegankelijk zijn. Niet-toegankelijke afbeeldings gegevens kunnen een alternatieve terugval afbeelding laden of worden verwijderd uit de stijlen. Afbeeldingen die uit KMZ-bestanden zijn geëxtraheerd, worden geconverteerd naar gegevens-Uri's.

Het resultaat van de Lees functie is een `SpatialDataSet`-object. Dit object breidt de geojson FeatureCollection-klasse uit. Het kan eenvoudig worden door gegeven aan een `DataSource` as-is om de functies ervan weer te geven op een kaart. De `SpatialDataSet` bevat niet alleen functie-informatie, maar kan ook KML-bedekkingen, verwerkings metrieken en andere details bevatten, zoals beschreven in de volgende tabel.

| Naam van eigenschap | Type | Beschrijving | 
|---------------|------|-------------|
| `bbox` | `BoundingBox` | Selectie kader van alle gegevens in de gegevensset. |
| `features` | `Feature[]` | Geojson-functies in de gegevensset. |
| `groundOverlays` | `(atlas.layer.ImageLayer | atlas.layers.OgcMapLayer)[]` | Een matrix van KML GroundOverlays. |
| `icons` | Record&lt;teken reeks, teken reeks&gt; | Een reeks pictogram-Url's. Key = pictogram naam, waarde = URL. |
| properties | iedere | Eigenschaps informatie die op document niveau van een ruimtelijke gegevensset is opgegeven. |
| `stats` | `SpatialDataSetStats` | Statistieken over de inhoud en verwerkings tijd van een ruimtelijke gegevens verzameling. |
| `type` | `'FeatureCollection'` | Alleen-lezen waarde voor geojson-type. |

## <a name="examples-of-reading-spatial-data"></a>Voor beelden van het lezen van ruimtelijke gegevens

De volgende code laat zien hoe u een eenvoudige ruimtelijke gegevensset kunt lezen en deze op de kaart kunt weer geven met behulp van de klasse `SimpleDataLayer`. De code gebruikt een GPX-bestand waarnaar een URL verwijst.

<br/>

<iframe height='500' scrolling='no' title='Ruimtelijke gegevens eenvoudig laden' src='//codepen.io/azuremaps/embed/yLNXrZx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de ruimtelijke gegevens van de pen <a href='https://codepen.io/azuremaps/pen/yLNXrZx/'>laden eenvoudig</a> door Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

In de volgende code-demo ziet u hoe u KML, of KMZ, kunt lezen en laden voor de kaart. KML kunnen basis bedekkingen bevatten. dit wordt in de vorm van een `ImageLyaer` of `OgcMapLayer`. Deze overlays moeten afzonderlijk van de functies op de kaart worden toegevoegd. Als de gegevensset aangepaste pictogrammen bevat, moeten deze pictogrammen ook worden geladen in de resources van de toewijzing voordat de functies worden geladen.

<br/>

<iframe height='500' scrolling='no' title='KML naar kaart laden' src='//codepen.io/azuremaps/embed/XWbgwxX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de KML van de pen <a href='https://codepen.io/azuremaps/pen/XWbgwxX/'>loading to map</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

U kunt eventueel ook een proxy service voor toegang tot meerdere domein activa opgeven waarvoor geen CORs is ingeschakeld. Dit code fragment laat zien hoe u een proxy service kunt bieden:

```javascript

//Set the location of your proxyServiceUrl file 
var proxyServiceUrl = window.location.origin + '/CorsEnabledProxyService.ashx?url=';

//Read a KML file from a URL or pass in a raw KML string.
atlas.io.read('https://s3-us-west-2.amazonaws.com/s.cdpn.io/1717245/2007SanDiegoCountyFires.kml', {
    //Provide a proxy service
    proxyService: proxyServiceUrl
}).then(async r => {
    if (r) {
        // Some code goes here . . .
    }
});

```

In de laatste demo hieronder ziet u hoe u een gescheiden bestand kunt lezen en op de kaart kunt weer geven. In dit geval gebruikt de code een CSV-bestand met ruimtelijke gegevens kolommen.

<br/>

<iframe height='500' scrolling='no' title='Een bestand met scheidings tekens toevoegen' src='//codepen.io/azuremaps/embed/ExjXBEb/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/ExjXBEb/'>een bestand met scheidings tekens toevoegen</a> door Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="write-spatial-data"></a>Ruimtelijke gegevens schrijven

Er zijn twee belang rijke schrijf functies in de ruimtelijke IO-module. De functie `atlas.io.write` genereert een teken reeks, terwijl de functie `atlas.io.writeCompressed` een gecomprimeerd zip-bestand genereert. Het gecomprimeerde zip-bestand zou een op tekst gebaseerd bestand kunnen bevatten met de ruimtelijke gegevens. Beide functies retour neren een Promise om de gegevens toe te voegen aan het bestand. En beide kunnen de volgende gegevens schrijven: `SpatialDataSet`, `DataSource`, `ImageLayer`, `OgcMapLayer`, functie verzameling, functie, geometrie of een matrix van een combi natie van deze gegevens typen. Wanneer u met behulp van beide functies schrijft, kunt u de gewenste bestands indeling opgeven. Als de bestands indeling niet is opgegeven, worden de gegevens als KML geschreven.

Het onderstaande hulp programma demonstreert het meren deel van de schrijf opties die kunnen worden gebruikt in combi natie met de functie `atlas.io.write`.

<br/>

<iframe height='700' scrolling='no' title='Opties voor het schrijven van ruimtelijke gegevens' src='//codepen.io/azuremaps/embed/YzXxXPG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de <a href='https://codepen.io/azuremaps/pen/YzXxXPG/'>Schrijf opties voor ruimtelijke gegevens</a> van de Pen per Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="example-of-writing-spatial-data"></a>Voor beeld van het schrijven van ruimtelijke gegevens

In het volgende voor beeld kunt u ruimtelijke bestanden slepen en neerzetten en vervolgens op de kaart laden. U kunt geojson-gegevens exporteren van de kaart en deze in een van de ondersteunde ruimtelijke gegevens indelingen schrijven als een teken reeks of als een gecomprimeerd bestand.

<br/>

<iframe height='700' scrolling='no' title='Ruimtelijke bestanden naar kaart slepen en neerzetten' src='//codepen.io/azuremaps/embed/zYGdGoO/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/zYGdGoO/'>slepen en neerzetten van ruimtelijke bestanden naar map</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

U kunt eventueel ook een proxy service voor toegang tot meerdere domein activa opgeven waarvoor geen CORs is ingeschakeld. Dit code fragment laat zien hoe u een proxy service kunt opnemen:

```javascript

//Set the location of your proxyServiceUrl file 
var proxyServiceUrl = window.location.origin + '/CorsEnabledProxyService.ashx?url=';

function readData(data, fileName) {
    loadingIcon.style.display = '';
    fileCount++;
    //Attempt to parse the file and add the shapes to the map.
    atlas.io.read(data, {
        //Provide a proxy service
        proxyService: proxyServiceUrl
    }).then(
        //Success
        function(r) {
            //some code goes here ...
        }
    );
}
```

## <a name="read-and-write-well-known-text-wkt"></a>Bekende tekst lezen en schrijven (WKT)

[Bekende tekst](https://en.wikipedia.org/wiki/Well-known_text_representation_of_geometry) (WKT) is een open GEOSPATIAL Consortium (OGC) standaard voor het vertegenwoordigen van ruimtelijke geometrie als tekst. Veel georuimtelijke systemen bieden ondersteuning voor WKT, zoals Azure SQL en Azure PostgreSQL met behulp van de PostGIS-invoeg toepassing. Net als bij de meeste OGC-standaarden zijn coördinaten ingedeeld als ' lengte graad breedte ' om te worden uitgelijnd met het ' x y-' Conventie. Een voor beeld: een punt op de lengte graad-110 en Latitude 45 kan worden geschreven als `POINT(-110 45)` met behulp van de WKT-indeling.

Bekende tekst kan worden gelezen met behulp van de functie `atlas.io.ogc.WKT.read` en geschreven met behulp van de functie `atlas.io.ogc.WKT.write`.

## <a name="examples-of-reading-and-writing-well-known-text-wkt"></a>Voor beelden van bekende tekst met lees-en schrijf bewerkingen (WKT)

De volgende code laat zien hoe u de bekende teken reeks kunt lezen `POINT(-122.34009 47.60995)` en deze kunt weer geven op de kaart met behulp van een Bubble Layer.

<br/>

<iframe height='500' scrolling='no' title='Lees bekende tekst' src='//codepen.io/azuremaps/embed/XWbabLd/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Raadpleeg de pen <a href='https://codepen.io/azuremaps/pen/XWbabLd/'>Lees bekende tekst</a> door Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

De volgende code toont de lees-en schrijf bewerkingen van bekende tekst heen en weer.

<br/>

<iframe height='700' scrolling='no' title='Bekende tekst lezen en schrijven' src='//codepen.io/azuremaps/embed/JjdyYav/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Bekijk de <a href='https://codepen.io/azuremaps/pen/JjdyYav/'>Lees-en schrijf bekende tekst</a> van de Pen door Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="read-and-write-gml"></a>GML lezen en schrijven

GML is een ruimtelijke XML-bestands specificatie die vaak wordt gebruikt als een uitbrei ding van andere XML-specificaties. Geojson-gegevens kunnen als XML met GML-Tags worden geschreven met behulp van de functie `atlas.io.core.GmlWriter.write`. De XML die GML bevat, kan worden gelezen met behulp van de functie `atlas.io.core.GmlReader.read`. De functie Read heeft twee opties:

- De `isAxisOrderLonLat` optie: de as-volg orde van de coördinaten "breedte graad, lengte graad" of "lengte graad" kan variëren tussen gegevens sets en is niet altijd goed gedefinieerd. De GML Reader leest de coördinaten gegevens standaard als ' breedte graad, lengte graad ', maar als deze optie is ingesteld op True, wordt deze als ' lengte graad, breedte graad ' gelezen.
- De optie `propertyTypes`: deze optie is een opzoek tabel voor sleutel waarden waarbij de sleutel de naam is van een eigenschap in de gegevensset. De waarde is het object type waarmee de waarde moet worden omgezet tijdens het parseren. De ondersteunde type waarden zijn: `string`, `number`, `boolean`en `date`. Als een eigenschap zich niet in de opzoek tabel bevindt of als het type niet is gedefinieerd, wordt de eigenschap geparseerd als een teken reeks.

De functie `atlas.io.read` wordt standaard ingesteld op de `atlas.io.core.GmlReader.read` functie wanneer deze detecteert dat de invoer gegevens XML zijn, maar de gegevens zijn niet een van de andere ruimtelijke XML-indelingen die worden ondersteund.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de klassen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [statische functies van atlas.io](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io)

> [!div class="nextstepaction"]
> [SpatialDataSet](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.spatialdataset)

> [!div class="nextstepaction"]
> [SpatialDataSetStats](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.spatialdatasetstats)

> [!div class="nextstepaction"]
> [GmlReader](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlreader?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [GmlWriter](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlwriter?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [de functies Atlas. io. OGC. WKT](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.ogc.wkt)

Raadpleeg de volgende artikelen voor meer code voorbeelden om toe te voegen aan uw kaarten:

> [!div class="nextstepaction"]
> [Een OGC-kaartLaag toevoegen](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Verbinding maken met een WFS-service](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Kern bewerkingen gebruiken](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Details van ondersteunde gegevens indeling](spatial-io-supported-data-format-details.md)
