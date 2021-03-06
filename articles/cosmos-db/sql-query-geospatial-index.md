---
title: Georuimtelijke gegevens indexeren met Azure Cosmos DB
description: Ruimtelijke gegevens indexeren met Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: eb0a2b2778b3217e185b9883def6eaa54674cc5b
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2020
ms.locfileid: "79137900"
---
# <a name="index-geospatial-data-with-azure-cosmos-db"></a>Georuimtelijke gegevens indexeren met Azure Cosmos DB

De data base-engine van Azure Cosmos DB is ontworpen om echt schema neutraal te zijn en biedt ondersteuning voor de eerste klasse voor JSON. De geoptimaliseerde data base-engine voor schrijven van Azure Cosmos DB heeft in systeem eigen inzicht in de ruimtelijke gegevens die in de geojson-standaard worden weer gegeven.

In een kort gezegd wordt de geometrie geprojecteerd van Geodetic-coördinaten naar een 2D-vlak en vervolgens geleidelijk onderverdeeld in cellen met behulp van een **quadtree**. Deze cellen worden toegewezen aan 1D op basis van de locatie van de cel binnen een **Hilbert ruimte**die de lokale positie van punten behoudt. Wanneer locatie gegevens worden geïndexeerd, wordt er ook een proces met de naam **mozaïek patroon**genoemd, dat wil zeggen dat alle cellen die een locatie overlappen worden geïdentificeerd en opgeslagen als sleutels in de Azure Cosmos DB index. Op het moment dat de query, argumenten, zoals punten en polygonen zijn ook representatie mozaïekpatroon om op te halen van de relevante ID celbereiken, en vervolgens gebruikt voor het ophalen van gegevens uit de index.

Als u een indexerings beleid opgeeft dat ruimtelijke index voor/* (alle paden) bevat, worden alle gegevens die in de container zijn gevonden, geïndexeerd voor efficiënte ruimtelijke query's.

> [!NOTE]
> Azure Cosmos DB ondersteunt het indexeren van punten, line strings toe, veelhoeken en multiveelhoeken
>
>

## <a name="modifying-geospatial-data-type"></a>Georuimtelijk gegevens type wijzigen

In uw container geeft de `geospatialConfig` op hoe de georuimtelijke gegevens worden geïndexeerd. U moet één `geospatialConfig` per container opgeven: Geografie of geometrie. Als u niets opgeeft, wordt de `geospatialConfig` standaard ingesteld op het gegevens type geografie. Wanneer u de `geospatialConfig`wijzigt, worden alle bestaande georuimtelijke gegevens in de container opnieuw geïndexeerd.

> [!NOTE]
> Azure Cosmos DB ondersteunt momenteel alleen wijzigingen in de geospatialConfig in de .NET SDK in versie 3,6 en hoger.
>

Hier volgt een voor beeld van het wijzigen van het georuimtelijke gegevens type op `geometry` door de eigenschap `geospatialConfig` in te stellen en een **boundingBox**toe te voegen:

```csharp
    //Retrieve the container's details
    ContainerResponse containerResponse = await client.GetContainer("db", "spatial").ReadContainerAsync();
    //Set GeospatialConfig to Geometry
    GeospatialConfig geospatialConfig = new GeospatialConfig(GeospatialType.Geometry);
    containerResponse.Resource.GeospatialConfig = geospatialConfig;
    // Add a spatial index including the required boundingBox
    SpatialPath spatialPath = new SpatialPath
            {  
                Path = "/locations/*",
                BoundingBox = new BoundingBoxProperties(){
                    Xmin = 0,
                    Ymin = 0,
                    Xmax = 10,
                    Ymax = 10
                }
            };
    spatialPath.SpatialTypes.Add(SpatialType.Point);
    spatialPath.SpatialTypes.Add(SpatialType.LineString);
    spatialPath.SpatialTypes.Add(SpatialType.Polygon);
    spatialPath.SpatialTypes.Add(SpatialType.MultiPolygon);

    containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(spatialPath);

    // Update container with changes
    await client.GetContainer("db", "spatial").ReplaceContainerAsync(containerResponse.Resource);
```

## <a name="geography-data-indexing-examples"></a>Voor beelden van geografie gegevens indexering

Het volgende JSON-code fragment bevat een indexerings beleid waarvoor ruimtelijke indexering is ingeschakeld voor het **geografie** gegevens type. Het is geldig voor ruimtelijke gegevens met het geografie gegevens type en indexeert een geojson Point, veelhoek, multiveelhoek of lines Tring die zich binnen documenten bevinden voor ruimtelijke query's. Als u het indexerings beleid wijzigt met behulp van de Azure Portal, kunt u de volgende JSON voor het indexerings beleid opgeven om ruimtelijke indexering in te scha kelen in uw container:

**Container indexerings beleid JSON met geografie ruimtelijke indexeringen**

```json
    {
       "automatic":true,
       "indexingMode":"Consistent",
        "includedPaths": [
        {
            "path": "/*"
        }
        ],
        "spatialIndexes": [
        {
            "path": "/*",
            "types": [
                "Point",
                "Polygon",
                "MultiPolygon",
                "LineString"
            ]
        }
    ],
       "excludedPaths":[]
    }
```

> [!NOTE]
> Als de locatie GeoJSON-waarde in het document onjuist gevormd of ongeldig is, worden klikt u vervolgens het niet geïndexeerd voor ruimtelijke query's. U kunt de locatie waarden met behulp van ST_ISVALID en ST_ISVALIDDETAILED valideren.

U kunt het [indexerings beleid ook wijzigen](how-to-manage-indexing-policy.md) met behulp van de Azure CLI, Power shell of een SDK.

## <a name="geometry-data-indexing-examples"></a>Voor beelden van geometrie gegevens indexeren

Met het gegevens type **geometrie** , vergelijkbaar met het gegevens type geografie, moet u relevante paden en typen opgeven die u wilt indexeren. Daarnaast moet u ook een `boundingBox` binnen het indexerings beleid opgeven om aan te geven dat het gewenste gebied moet worden geïndexeerd voor dat specifieke pad. Voor elk georuimtelijke pad zijn eigen`boundingBox`vereist.

Het selectie kader bestaat uit de volgende eigenschappen:

- **xmin**: de minimale geïndexeerde x-coördinaat
- **ymin**: de minimale geïndexeerde y-coördinaat
- **xmax**: het maximum aantal geïndexeerde x-coördinaten
- **ymax**: het maximum aantal geïndexeerde y-coördinaten

Een selectie kader is vereist omdat geometrische gegevens een vlieg tuig in beslag nemen dat oneindig kan zijn. Ruimtelijke indexen vereisen echter een eindige spatie. Voor het gegevens type **geografie** is de aarde de grens en hoeft u geen begrenzingsvak in te stellen.

U moet een selectie kader maken dat alle (of de meeste) van uw gegevens bevat. Alleen bewerkingen die zijn berekend op de objecten die zich volledig binnen het begrenzingsvak bevinden, kunnen de ruimtelijke index gebruiken. U moet het selectie kader niet aanzienlijk groter maken dan nodig is, omdat dit een negatieve invloed heeft op de query prestaties.

Hier volgt een voor beeld van een indexerings beleid waarmee **geometrie** gegevens worden geïndexeerd waarvan de **geospatialConfig** is ingesteld op `geometry`:

```json
 {
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/\"_etag\"/?"
        }
    ],
    "spatialIndexes": [
        {
            "path": "/locations/*",
            "types": [
                "Point",
                "LineString",
                "Polygon",
                "MultiPolygon"
            ],
            "boundingBox": {
                "xmin": -10,
                "ymin": -20,
                "xmax": 10,
                "ymax": 20
            }
        }
    ]
}
```

Het bovenstaande indexerings beleid heeft een **boundingBox** van (-10, 10) voor x-coördinaten en (-20, 20) voor y-coördinaten. De container met het bovenstaande indexerings beleid indexeert alle punten, veelhoeken, multiveelhoeken en line strings toe die volledig binnen deze regio vallen.

> [!NOTE]
> Als u probeert een indexerings beleid met een **boundingBox** toe te voegen aan een container met `geography` gegevens type, zal dit mislukken. U moet de **geospatialConfig** van de container wijzigen in `geometry` voordat u een **boundingBox**toevoegt. U kunt gegevens toevoegen en de rest van uw indexerings beleid (zoals de paden en typen) wijzigen vóór of na het selecteren van het georuimtelijke gegevens type voor de container.

## <a name="next-steps"></a>Volgende stappen

U hebt geleerd hoe u aan de slag met georuimtelijke ondersteuning in Azure Cosmos DB, naast kunt u:

* Meer informatie over [Azure Cosmos DB query](sql-query-getting-started.md)
* Meer informatie over het [opvragen van ruimtelijke gegevens met Azure Cosmos DB](sql-query-geospatial-query.md)
* Meer informatie over [georuimtelijke en GEOjson-locatie gegevens in azure Cosmos DB](sql-query-geospatial-intro.md)