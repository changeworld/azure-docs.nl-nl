---
title: Gebeurtenis schema's — Azure Event Grid IoT Edge | Microsoft Docs
description: Gebeurtenis schema's in Event Grid op IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ba261aeedf6574f69d3c05f8fd005c912dcc59d1
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/31/2019
ms.locfileid: "73242461"
---
# <a name="event-schemas"></a>Gebeurtenisschema's

Event Grid-module accepteert en levert gebeurtenissen in JSON-indeling. Er zijn momenteel drie schema's die worden ondersteund door Event Grid:-

* **EventGridSchema**
* **CustomSchema**
* **CloudEventSchema**

U kunt het schema configureren waaraan een uitgever moet voldoen tijdens het maken van het onderwerp. Als u geen waarde opgeeft, wordt standaard **EventGridSchema**. Gebeurtenissen die niet voldoen aan het verwachte schema, worden geweigerd.

Abonnees kunnen ook het schema configureren waarin de gebeurtenissen moeten worden bezorgd. Indien niet opgegeven, is het schema standaard het onderwerp.
Momenteel moet het leverings schema van de abonnee overeenkomen met het invoer schema van het onderwerp. 

## <a name="eventgrid-schema"></a>EventGrid-schema

Het EventGrid-schema bestaat uit een set vereiste eigenschappen waaraan een publicerende entiteit moet voldoen. Elke uitgever moet de velden op het hoogste niveau vullen.

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

### <a name="eventgrid-schema-properties"></a>Eigenschappen van EventGrid-schema

Alle gebeurtenissen hebben de volgende gegevens op het hoogste niveau:

| Eigenschap | Type | Verplicht | Beschrijving |
| -------- | ---- | ----------- |-----------
| onderwerp | string | Nee | Moet overeenkomen met het onderwerp waarop het wordt gepubliceerd. Event Grid vult het met de naam van het onderwerp dat wordt gepubliceerd als niet opgegeven. |
| Onderwerp | string | Ja | Het door de uitgever gedefinieerde pad naar het gebeurtenis onderwerp. |
| Type | string | Ja | Gebeurtenis type voor deze gebeurtenis bron, bijvoorbeeld BlobCreated. |
| eventTime | string | Ja | Het tijdstip waarop de gebeurtenis is gegenereerd op basis van de UTC-tijd van de provider. |
| Id | string | Nee | De unieke id voor de gebeurtenis. |
| gegevens | object | Nee | Wordt gebruikt om gebeurtenis gegevens vast te leggen die specifiek zijn voor de publicatie-entiteit. |
| dataVersion | string | Ja | De schema versie van het gegevens object. De uitgever definieert de schema versie. |
| metadataVersion | string | Nee | De schema versie van de meta gegevens van de gebeurtenis. Event Grid definieert het schema van de eigenschappen op het hoogste niveau. Event Grid levert deze waarde. |

### <a name="example--eventgrid-schema-event"></a>Voor beeld: EventGrid-schema gebeurtenis

```json
[
  {
    "id": "1807",
    "eventType": "recordInserted",
    "subject": "myapp/vehicles/motorcycles",
    "eventTime": "2017-08-10T21:03:07+00:00",
    "data": {
      "make": "Ducati",
      "model": "Monster"
    },
    "dataVersion": "1.0"
  }
]
```

## <a name="customevent-schema"></a>CustomEvent-schema

In aangepaste schema's zijn er geen verplichte eigenschappen die worden afgedwongen zoals het EventGrid-schema. De publicatie-entiteit kan het gebeurtenis schema volledig beheren. Het biedt maximale flexibiliteit en maakt scenario's mogelijk waarin al een op gebeurtenissen gebaseerd systeem is geïnstalleerd en bestaande gebeurtenissen opnieuw moet gebruiken en/of niet aan een specifiek schema hoeven te worden gekoppeld.

### <a name="custom-schema-properties"></a>Aangepaste schema-eigenschappen

Geen verplichte eigenschappen. Het is tot de publicatie-entiteit om de payload te bepalen.

### <a name="example--custom-schema-event"></a>Voor beeld: aangepaste schema gebeurtenis

```json
[
  {
    "eventdata": {
      "make": "Ducati",
      "model": "Monster"
    }
  }
]
```

## <a name="cloudevent-schema"></a>CloudEvent-schema

Naast de hierboven genoemde schema's ondersteunt Event Grid systeem eigen gebeurtenissen in het [JSON-schema CloudEvents](https://github.com/cloudevents/spec/blob/master/json-format.md). CloudEvents is een open specificatie voor het beschrijven van gebeurtenis gegevens. Het vereenvoudigt interoperabiliteit door een gemeen schappelijk gebeurtenis schema te bieden voor het publiceren en gebruiken van gebeurtenissen. Het maakt deel uit van [CNCF](https://www.cncf.io/) en momenteel beschik bare versie is 1,0-RC1.

### <a name="cloudevent-schema-properties"></a>Eigenschappen van CloudEvent-schema

Raadpleeg de [CloudEvents-specificatie](https://github.com/cloudevents/spec/blob/master/json-format.md#3-envelope) voor de eigenschappen van de verplichte envelop.

### <a name="example--cloud-event"></a>Voor beeld: Cloud gebeurtenis
```json
[{
       "id": "1807",
       "type": "recordInserted",
       "source": "myapp/vehicles/motorcycles",
       "time": "2017-08-10T21:03:07+00:00",
       "datacontenttype": "application/json",
       "data": {
            "make": "Ducati",
            "model": "Monster"
        },
        "dataVersion": "1.0",
        "specVersion": "1.0-rc1"
}]
```
