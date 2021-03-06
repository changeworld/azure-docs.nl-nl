---
title: Domein-specifieke inhoud-Computer Vision
titleSuffix: Azure Cognitive Services
description: Informatie over het opgeven van een categorisatie domein voor een afbeelding om meer gedetailleerde informatie over een installatie kopie te retour neren.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 8d6dc91ae7bb0f6d7a24064749d9295558a7d39c
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946327"
---
# <a name="detect-domain-specific-content"></a>Domein-specifieke inhoud detecteren

Naast het coderen en categoriseren op hoog niveau, ondersteunt Computer Vision ook verdere domein-specifieke analyse met behulp van modellen die zijn getraind op gespecialiseerde gegevens.

Er zijn twee manieren om domein-specifieke modellen te gebruiken: op zichzelf (bereik analyse) of als een uitbrei ding van de categorisatie functie.

### <a name="scoped-analysis"></a>Bereik analyse

U kunt een installatie kopie alleen analyseren met het gekozen domein-specifieke model door de [modellen/\<model\>/-](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) API aan te roepen.

Hier volgt een voor beeld van een JSON-antwoord dat wordt geretourneerd door de **modellen/beroemdheden/analyse-** API voor de opgegeven installatie kopie:

![Satya Nadellae, glimlachend](./images/satya.jpeg)

```json
{
  "result": {
    "celebrities": [{
      "faceRectangle": {
        "top": 391,
        "left": 318,
        "width": 184,
        "height": 184
      },
      "name": "Satya Nadella",
      "confidence": 0.99999856948852539
    }]
  },
  "requestId": "8217262a-1a90-4498-a242-68376a4b956b",
  "metadata": {
    "width": 800,
    "height": 1200,
    "format": "Jpeg"
  }
}
```

### <a name="enhanced-categorization-analysis"></a>Uitgebreide categorisatie-analyse

U kunt ook gebruikersspecifieke modellen gebruiken om een aanvulling op de algemene afbeeldings analyse uit te kunnen laten. U doet dit als onderdeel van [categorisatie op hoog niveau](concept-categorizing-images.md) door domein-specifieke modellen op te geven in de para meter *Details* van de API-aanroep voor [analyse](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) .

In dit geval wordt de taxonomie classificatie 86-categorie eerst aangeroepen. Als een van de gedetecteerde categorieën een overeenkomend specifiek domein model heeft, wordt de installatie kopie door het model door gegeven en worden de resultaten toegevoegd.

In het volgende JSON-antwoord ziet u hoe serverspecifieke analyses kunnen worden opgenomen als `detail` het knoop punt in een bredere categorisatie analyse.

```json
"categories":[
  {
    "name":"abstract_",
    "score":0.00390625
  },
  {
    "name":"people_",
    "score":0.83984375,
    "detail":{
      "celebrities":[
        {
          "name":"Satya Nadella",
          "faceRectangle":{
            "left":597,
            "top":162,
            "width":248,
            "height":248
          },
          "confidence":0.999028444
        }
      ],
      "landmarks":[
        {
          "name":"Forbidden City",
          "confidence":0.9978346
        }
      ]
    }
  }
]
```

## <a name="list-the-domain-specific-models"></a>De domein-specifieke modellen weer geven

Computer Vision ondersteunt momenteel de volgende domein-specifieke modellen:

| Name | Description |
|------|-------------|
| beroemdheden | Beroemdheden-herkenning, ondersteund voor installatie kopieën die `people_` in de categorie zijn geclassificeerd |
| oriëntatie punten | Oriëntatie punt herkenning, ondersteund voor installatie kopieën die `outdoor_` zijn `building_` geclassificeerd in de categorieën of |

Het aanroepen van de [modellen](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fd) API retourneert deze informatie samen met de categorieën waarop elk model kan worden toegepast:

```json
{
  "models":[
    {
      "name":"celebrities",
      "categories":[
        "people_",
        "人_",
        "pessoas_",
        "gente_"
      ]
    },
    {
      "name":"landmarks",
      "categories":[
        "outdoor_",
        "户外_",
        "屋外_",
        "aoarlivre_",
        "alairelibre_",
        "building_",
        "建筑_",
        "建物_",
        "edifício_"
      ]
    }
  ]
}
```

## <a name="next-steps"></a>Volgende stappen

Leer concepten over het categoriseren van [afbeeldingen](concept-categorizing-images.md).
