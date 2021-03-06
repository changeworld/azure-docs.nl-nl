---
title: Percentage vooraf samengestelde entiteit-LUIS
titleSuffix: Azure Cognitive Services
description: In dit artikel bevat percentage vooraf gedefinieerde entiteitgegevens in Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 31ea1c36139abcb1e102161ad76a203073ba4dfd
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270501"
---
# <a name="percentage-prebuilt-entity-for-a-luis-app"></a>Percentage vooraf samengestelde entiteit voor een LUIS-app
Percentage nummers kunnen worden weer gegeven als breuken, `3 1/2`of als percentage `2%`. Omdat deze entiteit wordt al getraind, hoeft u niet om toe te voegen van de voorbeeld-uitingen met percentage van de toepassing intents. Het percentage entiteit wordt ondersteund in [veel cult uren](luis-reference-prebuilt-entities.md).

## <a name="types-of-percentage"></a>Typen percentage
Percentage wordt beheerd vanuit de map [recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L114) github

## <a name="resolution-for-prebuilt-percentage-entity"></a>Oplossing voor vooraf gedefinieerde percentage entiteit

De volgende entiteits objecten worden geretourneerd voor de query:

`set a trigger when my stock goes up 2%`

#### <a name="v3-response"></a>[V3-antwoord](#tab/V3)

De volgende JSON is waarvan de `verbose` para meter is ingesteld op `false`:

```json
"entities": {
    "percentage": [
        2
    ]
}
```
#### <a name="v3-verbose-response"></a>[Uitgebreide respons van v3](#tab/V3-verbose)
De volgende JSON is waarvan de `verbose` para meter is ingesteld op `true`:

```json
"entities": {
    "percentage": [
        2
    ],
    "$instance": {
        "percentage": [
            {
                "type": "builtin.percentage",
                "text": "2%",
                "startIndex": 36,
                "length": 2,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```
#### <a name="v2-response"></a>[V2-antwoord](#tab/V2)

In het volgende voor beeld ziet u de resolutie van de **ingebouwde percent** -entiteit.

```json
"entities": [
    {
        "entity": "2%",
        "type": "builtin.percentage",
        "startIndex": 36,
        "endIndex": 37,
        "resolution": {
        "value": "2%"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [v3-Voorspellings eindpunt](luis-migration-api-v3.md).

Meer informatie over het [rang telwoord](luis-reference-prebuilt-ordinal.md), het [aantal](luis-reference-prebuilt-number.md)en de [Tempe ratuur](luis-reference-prebuilt-temperature.md) .
