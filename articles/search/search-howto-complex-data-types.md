---
title: Complexe gegevens typen model leren
titleSuffix: Azure Cognitive Search
description: Geneste of hiërarchische gegevens structuren kunnen worden gemodelleerd in een Azure Cognitive Search-index met behulp van de gegevens typen complex type en verzameling.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
tags: complex data types; compound data types; aggregate data types
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 2edd62825de08becf22f2f953a63a7f89f55e0a6
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79283054"
---
# <a name="how-to-model-complex-data-types-in-azure-cognitive-search"></a>Complexe gegevens typen model leren in azure Cognitive Search

Externe gegevens sets die worden gebruikt voor het vullen van een Azure Cognitive Search-index kunnen in veel vormen worden geleverd. Soms bevatten deze hiërarchische of geneste substructuren. Voor beelden zijn bijvoorbeeld meerdere adressen voor één klant, meerdere kleuren en grootten voor één SKU, meerdere auteurs van één boek, enzovoort. In het kader van model lering kunnen deze structuren worden aangeduid als *complexe*, *samengestelde*, *samengestelde*of *statistische* gegevens typen. De term Azure Cognitive Search gebruikt voor dit concept is een **complex type**. In azure Cognitive Search worden complexe typen gemodelleerd met behulp van **complexe velden**. Een complex veld is een veld met onderliggende elementen (subvelden) die van elk gegevens type kunnen zijn, met inbegrip van andere complexe typen. Dit werkt op een vergelijk bare manier als gestructureerde gegevens typen in een programmeer taal.

Complexe velden vertegenwoordigen ofwel één object in het document of een matrix van objecten, afhankelijk van het gegevens type. Velden van het type `Edm.ComplexType` afzonderlijke objecten vertegenwoordigen, terwijl velden van het type `Collection(Edm.ComplexType)` matrices met objecten vertegenwoordigen.

Azure Cognitive Search biedt systeem eigen ondersteuning voor complexe typen en verzamelingen. Met deze typen kunt u nagenoeg elke JSON-structuur in een Azure Cognitive Search-index model leren. In eerdere versies van Azure Cognitive Search-Api's kunnen alleen samengevoegde Rijg sets worden geïmporteerd. In de nieuwste versie kan uw index nu nauw keuriger overeenkomen met de bron gegevens. Met andere woorden, als uw bron gegevens complexe typen hebben, kan uw index ook complexe typen hebben.

Om aan de slag te gaan, raden we de [gegevensverzameling Hotels](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md)aan, die u in de wizard **gegevens importeren** in de Azure Portal kunt laden. De wizard detecteert complexe typen in de bron en suggereert een index schema op basis van de gedetecteerde structuren.

> [!Note]
> Ondersteuning voor complexe typen is algemeen beschikbaar in `api-version=2019-05-06`. 
>
> Als uw zoek oplossing is gebaseerd op eerdere tijdelijke oplossingen van platte gegevens sets in een verzameling, moet u de index zodanig wijzigen dat deze complexe typen bevat zoals wordt ondersteund in de nieuwste API-versie. Zie voor meer informatie over het upgraden van API-versies [upgraden naar de nieuwste versie van rest API](search-api-migration.md) of voer een [upgrade uit naar de nieuwste versie van de .NET SDK](search-dotnet-sdk-migration-version-9.md).

## <a name="example-of-a-complex-structure"></a>Voor beeld van een complexe structuur

Het volgende JSON-document bestaat uit eenvoudige velden en complexe velden. Complexe velden, zoals `Address` en `Rooms`, hebben subvelden. `Address` heeft één set waarden voor die subvelden, omdat het een enkel object in het document is. `Rooms` heeft daarentegen meerdere sets waarden voor de subvelden, één voor elk object in de verzameling.

```json
{
  "HotelId": "1",
  "HotelName": "Secret Point Motel",
  "Description": "Ideally located on the main commercial artery of the city in the heart of New York.",
  "Address": {
    "StreetAddress": "677 5th Ave",
    "City": "New York",
    "StateProvince": "NY"
  },
  "Rooms": [
    {
      "Description": "Budget Room, 1 Queen Bed (Cityside)",
      "Type": "Budget Room",
      "BaseRate": 96.99
    },
    {
      "Description": "Deluxe Room, 2 Double Beds (City View)",
      "Type": "Deluxe Room",
      "BaseRate": 150.99
    },
  ]
}
```

## <a name="creating-complex-fields"></a>Complexe velden maken

Net als bij elke index definitie kunt u de portal, [rest API](https://docs.microsoft.com/rest/api/searchservice/create-index)of [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) gebruiken om een schema te maken dat complexe typen bevat. 

In het volgende voor beeld ziet u een JSON-index schema met eenvoudige velden, verzamelingen en complexe typen. U ziet dat in een complex type elk subveld een type heeft en kan kenmerken hebben, net als velden op het hoogste niveau. Het schema komt overeen met de bovenstaande voorbeeld gegevens. `Address` is een complex veld dat geen verzameling is (een hotel heeft één adres). `Rooms` is een complex verzamelings veld (een hotel heeft veel kamers).

```json
{
  "name": "hotels",
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
    { "name": "Address", "type": "Edm.ComplexType",
      "fields": [
        { "name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
        { "name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
        { "name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true }
      ]
    },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)",
      "fields": [
        { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
        { "name": "Type", "type": "Edm.String", "searchable": true },
        { "name": "BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true }
      ]
    }
  ]
}
```

## <a name="updating-complex-fields"></a>Complexe velden bijwerken

Alle regels voor opnieuw [indexeren](search-howto-reindex.md) die van toepassing zijn op velden in het algemeen blijven van toepassing op complexe velden. Als u hier enkele van de hoofd regels opnieuw opgeeft, hoeft u voor het toevoegen van een veld geen index opnieuw samen te stellen, maar de meeste wijzigingen doen wel.

### <a name="structural-updates-to-the-definition"></a>Structurele updates voor de definitie

U kunt op elk gewenst moment nieuwe subvelden toevoegen aan een complex veld zonder dat een index opnieuw moet worden samengesteld. U kunt bijvoorbeeld ' ZipCode ' toevoegen aan `Address` of ' voorzieningen ' aan `Rooms` is toegestaan, net zoals het toevoegen van een veld op het hoogste niveau aan een index. Bestaande documenten hebben een null-waarde voor nieuwe velden totdat u deze velden expliciet vult door uw gegevens bij te werken.

U ziet dat in een complex type elk subveld een type heeft en kan kenmerken hebben, net zoals velden op het hoogste niveau

### <a name="data-updates"></a>Gegevens updates

Het bijwerken van bestaande documenten in een index met de actie `upload` werkt op dezelfde manier als voor complexe en eenvoudige velden. alle velden worden vervangen. `merge` (of `mergeOrUpload` wanneer toegepast op een bestaand document) werkt echter niet in alle velden. Met name `merge` biedt geen ondersteuning voor het samen voegen van elementen binnen een verzameling. Deze beperking bestaat voor verzamelingen van primitieve typen en complexe verzamelingen. Als u een verzameling wilt bijwerken, moet u de volledige verzamelings waarde ophalen, wijzigingen aanbrengen en de nieuwe verzameling vervolgens toevoegen aan de API-aanvraag van de index.

## <a name="searching-complex-fields"></a>Complexe velden zoeken

Zoek expressies met een vrije vorm werken zoals verwacht met complexe typen. Als een doorzoekbaar veld of subveld ergens in een document overeenkomt, is het document zelf een overeenkomst.

Query's krijgen meer nuances wanneer u meerdere voor waarden en Opera tors hebt, en voor sommige termen zijn veld namen opgegeven, evenals de [syntaxis](query-lucene-syntax.md)van de lucene. Met deze query wordt bijvoorbeeld geprobeerd twee voor waarden, "Port land" en "of" te vergelijken met twee subvelden van het veld adres:

    search=Address/City:Portland AND Address/State:OR

Query's zoals dit zijn niet- *gecorreleerd* voor Zoek opdrachten in volledige tekst, in tegens telling tot filters. In filters worden query's via subvelden van een complexe verzameling gecorreleerd met behulp van bereik variabelen in [`any` of `all`](search-query-odata-collection-operators.md). De Maine-query hierboven retourneert documenten die zowel "Port land," als "Port land, Oregon" bevatten, samen met andere steden in Oregon. Dit gebeurt omdat elke component van toepassing is op alle waarden van het veld in het hele document, waardoor er geen ' Huidig subdocument ' is. Zie [informatie over OData-verzamelings filters in Azure Cognitive Search](search-query-understand-collection-filters.md)voor meer informatie.

## <a name="selecting-complex-fields"></a>Complexe velden selecteren

De para meter `$select` wordt gebruikt om te kiezen welke velden worden geretourneerd in de zoek resultaten. Als u deze para meter wilt gebruiken om specifieke subvelden van een complex veld te selecteren, neemt u het bovenliggende veld en subveld op, gescheiden door een slash (`/`).

    $select=HotelName, Address/City, Rooms/BaseRate

Velden moeten worden gemarkeerd als ophalen in de index als u deze wilt in de zoek resultaten. Alleen velden die zijn gemarkeerd als ophalen kunnen worden gebruikt in een `$select`-instructie.

## <a name="filter-facet-and-sort-complex-fields"></a>Complexe velden filteren, facetten en sorteren

De syntaxis van het [OData-pad](query-odata-filter-orderby-syntax.md) dat wordt gebruikt voor filteren en zoek opdrachten in een veld, kan ook worden gebruikt voor het befacetnen, sorteren en selecteren van velden in een zoek opdracht. Voor complexe typen gelden de regels die bepalen welke subvelden kunnen worden gemarkeerd als sorteerbaar of facetbaar. Zie voor meer informatie over deze regels de [Create Index-API-verwijzing](/rest/api/searchservice/create-index).

### <a name="faceting-sub-fields"></a>Facet Subvelden

Elk subveld kan worden gemarkeerd als facetable tenzij het van het type `Edm.GeographyPoint` of `Collection(Edm.GeographyPoint)`is.

Het aantal documenten dat wordt geretourneerd in de facet resultaten worden berekend voor het bovenliggende document (een hotel), niet de subdocumenten in een complexe verzameling (kamers). Stel bijvoorbeeld dat een hotel 20 kamers van het type ' Suite ' bevat. Gezien deze facet parameter `facet=Rooms/Type`, is het aantal facetten één voor het Hotel, niet 20 voor de kamers.

### <a name="sorting-complex-fields"></a>Complexe velden sorteren

Sorteer bewerkingen zijn van toepassing op documenten (hotels) en geen subdocumenten (kamers). Wanneer u een verzameling complexe typen hebt, zoals kamers, is het belang rijk om te realiseren dat u niet op kamers kunt sorteren. U kunt op geen enkele verzameling sorteren.

Sorteer bewerkingen werken als velden één waarde per document hebben, ongeacht of het veld een eenvoudig veld is of een subveld in een complex type. `Address/City` mag bijvoorbeeld sorteerbaar zijn omdat er maar één adres per hotel is, dus `$orderby=Address/City` worden Hotels gesorteerd op plaats.

### <a name="filtering-on-complex-fields"></a>Filteren op complexe velden

U kunt verwijzen naar subvelden van een complex veld in een filter expressie. Gebruik alleen de syntaxis van het [OData-pad](query-odata-filter-orderby-syntax.md) dat wordt gebruikt voor facetten, sorteren en selecteren van velden. Met het volgende filter worden bijvoorbeeld alle hotels in Canada geretourneerd:

    $filter=Address/Country eq 'Canada'

Als u wilt filteren op een complex verzamelings veld, kunt u een **lambda-expressie** gebruiken met de [operator`any` en `all`](search-query-odata-collection-operators.md). In dat geval is de **variabele Range** van de lambda-expressie een object met subvelden. U kunt naar deze subvelden verwijzen met de standaard syntaxis van het OData-pad. Het volgende filter retourneert bijvoorbeeld alle hotels met ten minste één luxe kamer en alle niet-roken ruimten:

    $filter=Rooms/any(room: room/Type eq 'Deluxe Room') and Rooms/all(room: not room/SmokingAllowed)

Net als bij eenvoudige velden van het hoogste niveau kunnen eenvoudige subvelden van complexe velden alleen in filters worden opgenomen als het kenmerk **filterbaar** is ingesteld op `true` in de index definitie. Zie [Create Index-API Reference](/rest/api/searchservice/create-index)(Engelstalig) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Probeer de gegevensset voor [Hotels](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md) in de wizard **gegevens importeren** . U hebt de Cosmos DB verbindings gegevens die in het Leesmij-bestand zijn opgenomen, nodig om toegang te krijgen tot de gegevens.

Met deze informatie is de eerste stap in de wizard het maken van een nieuwe Azure Cosmos DB-gegevens bron. Wanneer u in de wizard naar de doel index pagina gaat, ziet u een index met complexe typen. Maak en laad deze index en voer vervolgens query's uit om inzicht te krijgen in de nieuwe structuur.

> [!div class="nextstepaction"]
> [Snelstartgids: de wizard portal voor importeren, indexeren en query's](search-get-started-portal.md)
