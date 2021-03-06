---
title: Uitvoer batching in Azure Event Grid IoT Edge | Microsoft Docs
description: Uitvoer batching in Event Grid op IoT Edge.
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: a6f033af34088081090251f2e5e7cd4a07ce43cc
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76841744"
---
# <a name="output-batching"></a>Batchverwerking van uitvoer

Event Grid biedt ondersteuning voor het leveren van meer dan één gebeurtenis in één bezorgings aanvraag. Deze functie maakt het mogelijk de algehele leverings doorvoer te verhogen zonder de HTTP per-aanvraag-overhead te betalen. Batch verwerking is standaard uitgeschakeld en kan per abonnement worden ingeschakeld.

> [!WARNING]
> De Maxi maal toegestane duur voor het verwerken van elke leverings aanvraag wordt niet gewijzigd, zelfs als de abonnee code mogelijk meer werk moet doen per batch-aanvraag. De standaard time-outwaarde is 60 seconden.

## <a name="batching-policy"></a>Batch beleid

U kunt het batch gedrag van Event Grid per abonnee aanpassen door de volgende twee instellingen te wijzigen:

* Maximum aantal gebeurtenissen per batch

  Met deze instelling stelt u een bovenlimiet in voor het aantal gebeurtenissen dat kan worden toegevoegd aan een batch-bezorgings aanvraag.

* Voorkeurs Batch grootte in KB

  Deze knop wordt gebruikt om het maximum aantal kilo bytes te bepalen dat per bezorgings aanvraag kan worden verzonden

## <a name="batching-behavior"></a>Batch verwerking

* Alle of geen

  Event Grid werkt met alle-of-geen semantiek. Het biedt geen ondersteuning voor het gedeeltelijk slagen van een batch-levering. U moet er rekening mee houden dat abonnees alleen kunnen worden gevraagd om het aantal gebeurtenissen per batch, omdat deze binnen 60 seconden redelijkerwijs kan worden verwerkt.

* Optimistische batch verwerking

  De instellingen voor batch verwerking zijn niet strikt gebonden aan het batch gedrag en worden gerespecteerd op basis van de beste inspanningen. Bij lage gebeurtenis frequenties houdt u vaak rekening met de Batch grootte die kleiner is dan het aangevraagde maximum aantal gebeurtenissen per batch.

* De standaard waarde is ingesteld op uit

  Standaard voegt Event Grid slechts één gebeurtenis toe aan elke bezorgings aanvraag. De manier om batching in te scha kelen, is door een van de eerder genoemde instellingen in het artikel in de JSON van het gebeurtenis abonnement in te stellen.

* Standaardwaarden

  Het is niet nodig om zowel de instellingen (het maximum aantal gebeurtenissen per batch en de Batch grootte in kilo bytes) op te geven bij het maken van een gebeurtenis abonnement. Als er slechts één instelling is ingesteld, gebruikt Event Grid (Configureer bare) standaard waarden. Zie de volgende secties voor de standaard waarden en hoe u deze kunt negeren.

## <a name="turn-on-output-batching"></a>Uitvoer batching inschakelen

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
             {
                "endpointUrl": "<your_webhook_url>",
                "maxEventsPerBatch": 10,
                "preferredBatchSizeInKilobytes": 64
             }
        },
    }
}
```

## <a name="configuring-maximum-allowed-values"></a>Maxi maal toegestane waarden configureren

De volgende implementatie tijd instellingen bepalen de toegestane maximum waarde bij het maken van een gebeurtenis abonnement.

| De naam van eigenschap | Beschrijving |
| ------------- | ----------- | 
| `api__deliveryPolicyLimits__maxpreferredBatchSizeInKilobytes` | De toegestane maximum waarde voor de `PreferredBatchSizeInKilobytes` knop. Standaard `1033`.
| `api__deliveryPolicyLimits__maxEventsPerBatch` | De toegestane maximum waarde voor de `MaxEventsPerBatch` knop. Standaard `50`.

## <a name="configuring-runtime-default-values"></a>Standaard waarden voor runtime configureren

De volgende implementatie tijd instellingen bepalen de runtime standaard waarde van elke knop wanneer deze niet is opgegeven in het gebeurtenis abonnement. Als u wilt herhalen, moet er ten minste één knop worden ingesteld op het gebeurtenis abonnement om batch-gedrag in te scha kelen.

| De naam van eigenschap | Beschrijving |
| ------------- | ----------- |
| `broker__defaultMaxBatchSizeInBytes` | Maximale grootte van de leverings aanvraag wanneer alleen `MaxEventsPerBatch` is opgegeven. Standaard `1_058_576`.
| `broker__defaultMaxEventsPerBatch` | Het maximum aantal gebeurtenissen dat aan een batch moet worden toegevoegd als alleen `MaxBatchSizeInBytes` is opgegeven. Standaard `10`.
