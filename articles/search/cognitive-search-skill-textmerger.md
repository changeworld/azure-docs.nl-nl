---
title: Cognitieve vaardigheid van tekst samen voegen
titleSuffix: Azure Cognitive Search
description: Tekst van een verzameling velden samen voegen in één geconsolideerd veld. Gebruik deze cognitieve vaardigheid in een AI-pijp lijn in azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 98ea416305f080850d85498f74693eb2d45b0944
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162341"
---
#   <a name="text-merge-cognitive-skill"></a>Cognitieve vaardigheid van tekst samen voegen

Met de functie **tekst samen voegen** wordt tekst van een verzameling velden in één veld geconsolideerd. 

> [!NOTE]
> Deze vaardigheid is niet gebonden aan een Cognitive Services-API en er worden geen kosten in rekening gebracht voor het gebruik ervan. U moet nog steeds [een Cognitive Services resource koppelen](cognitive-search-attach-cognitive-services.md)om de optie **gratis** resource te overschrijven, waardoor u een klein aantal dagelijkse verrijkingen per dag beperkt.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.MergeSkill

## <a name="skill-parameters"></a>Vaardigheids parameters

Para meters zijn hoofdletter gevoelig.

| Parameternaam     | Beschrijving |
|--------------------|-------------|
| insertPreTag  | De teken reeks die voor elke invoeging moet worden opgenomen. De standaard waarde is `" "`. Als u de ruimte wilt weglaten, stelt u de waarde in op `""`.  |
| insertPostTag | De teken reeks die na elke invoeging moet worden opgenomen. De standaard waarde is `" "`. Als u de ruimte wilt weglaten, stelt u de waarde in op `""`.  |


##  <a name="sample-input"></a>Voorbeeld invoer
Een JSON-document dat bruikbare invoer voor deze vaardigheid biedt, kan zijn:

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
      {
        "text": "The brown fox jumps over the dog",
        "itemsToInsert": ["quick", "lazy"],
        "offsets": [3, 28]
      }
    }
  ]
}
```

##  <a name="sample-output"></a>Voorbeelduitvoer
In dit voor beeld ziet u de uitvoer van de vorige invoer, ervan uitgaande dat de *insertPreTag* is ingesteld op `" "`en *insertPostTag* is ingesteld op `""`. 

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
      {
        "mergedText": "The quick brown fox jumps over the lazy dog"
      }
    }
  ]
}
```

## <a name="extended-sample-skillset-definition"></a>Uitgebreide definitie van de voor beeld-vaardigheidset

Een veelvoorkomend scenario voor het gebruik van het samen voegen van tekst is het samen voegen van de tekstuele weer gave van afbeeldingen (tekst van een OCR-vaardigheid of het bijschrift van een afbeelding) in het veld inhoud van een document. 

In het volgende voor beeld wordt gebruikgemaakt van de OCR-vaardigheid voor het extra heren van tekst uit afbeeldingen die zijn Inge sloten in het document. Vervolgens wordt er een *merged_text* veld gemaakt dat zowel de oorspronkelijke als de OCRed-tekst van elke afbeelding bevat. [Hier](https://docs.microsoft.com/azure/search/cognitive-search-skill-ocr)vindt u meer informatie over de OCR-vaardigheid.

```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
      "description": "Extract text (plain and structured) from image.",
      "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
      "context": "/document/normalized_images/*",
      "defaultLanguageCode": "en",
      "detectOrientation": true,
      "inputs": [
        {
          "name": "image",
          "source": "/document/normalized_images/*"
        }
      ],
      "outputs": [
        {
          "name": "text"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
      "description": "Create merged_text, which includes all the textual representation of each image inserted at the right location in the content field.",
      "context": "/document",
      "insertPreTag": " ",
      "insertPostTag": " ",
      "inputs": [
        {
          "name":"text", "source": "/document/content"
        },
        {
          "name": "itemsToInsert", "source": "/document/normalized_images/*/text"
        },
        {
          "name":"offsets", "source": "/document/normalized_images/*/contentOffset" 
        }
      ],
      "outputs": [
        {
          "name": "mergedText", "targetName" : "merged_text"
        }
      ]
    }
  ]
}
```
In het bovenstaande voor beeld wordt ervan uitgegaan dat er een veld met genormaliseerde afbeeldingen bestaat. Als u het veld genormaliseerde afbeeldingen wilt ophalen, stelt u de *imageAction* -configuratie in de definitie van de Indexeer functie in op *generateNormalizedImages* , zoals hieronder wordt weer gegeven:

```json
{
  //...rest of your indexer definition goes here ...
  "parameters":{
    "configuration":{
        "dataToExtract":"contentAndMetadata",
        "imageAction":"generateNormalizedImages"
    }
  }
}
```

## <a name="see-also"></a>Zie ook

+ [Ingebouwde vaardig heden](cognitive-search-predefined-skills.md)
+ [Een vaardig heden definiëren](cognitive-search-defining-skillset.md)
+ [Indexeer functie maken (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
