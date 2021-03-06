---
title: Gegevens modellering in Preview-omgevingen-Azure Time Series Insights | Microsoft Docs
description: Meer informatie over gegevens modellering in Azure Time Series Insights preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/18/2020
ms.custom: seodec18
ms.openlocfilehash: 39ebbf99ad31cce20eabc20fbdc056c889235713
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77470748"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Gegevens modellering in Azure Time Series Insights preview

In dit artikel wordt beschreven hoe u kunt werken met Time Series-model in Azure Time Series Insights preview. Het bevat een aantal veelvoorkomende gegevens scenario's.

> [!TIP]
> * Meer informatie over het [Time Series-model](time-series-insights-update-tsm.md)preview.
> * Meer informatie over het navigeren in de preview-gebruikers interface in [Azure time series Insights preview Explorer](./time-series-insights-update-explorer.md).

## <a name="instances"></a>exemplaren

De Azure Time Series Insights Explorer ondersteunt instanties voor **maken**, **lezen**, **bijwerken**en **verwijderen** in de browser. 

Selecteer om te beginnen de **model** weergave in de weer gave **analyse** van Time Series Insights Explorer.

### <a name="create-a-single-instance"></a>Een enkel exemplaar maken

1. Ga naar het deel venster voor het selecteren van de tijdreeks model en selecteer **instanties** in het menu. Alle exemplaren die zijn gekoppeld aan de geselecteerde Time Series Insights omgeving worden weer gegeven.

    [![een enkel exemplaar maken door eerst instanties te selecteren.](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png#lightbox)

1. Selecteer **+ Toevoegen**.

    [![een instantie toevoegen door de knop + toevoegen te selecteren.](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png#lightbox)

1. Voer de details van het exemplaar in, selecteer de koppeling type en hiërarchie en selecteer **maken**.

### <a name="bulk-upload-one-or-more-instances"></a>Bulk upload een of meer exemplaren

> [!TIP]
> U kunt uw exemplaren in JSON opslaan op uw bureau blad. Het gedownloade JSON-bestand kan vervolgens worden geüpload door de volgende stappen uit te voeren.

1. Selecteer **JSON uploaden**.
1. Selecteer het bestand dat de nettolading van de instanties bevat.

    [![bulksgewijs uploaden van exemplaren via JSON.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png#lightbox)

1. Selecteer **Uploaden**.

### <a name="edit-a-single-instance"></a>Eén exemplaar bewerken

1. Selecteer het exemplaar en selecteer het pictogram **bewerken** of **Potlood**. 
1. Breng de gewenste wijzigingen aan en selecteer **Opslaan**.

    [![één exemplaar te bewerken.](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png#lightbox)

### <a name="delete-an-instance"></a>Een exemplaar verwijderen

1. Selecteer het exemplaar en selecteer het pictogram **verwijderen** of **afval lade**.

   [![een exemplaar verwijderen door verwijderen te selecteren.](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png#lightbox)

1. Bevestig de verwijdering door **verwijderen**te selecteren.

> [!NOTE]
> Een exemplaar moet een veld validatie controle door geven die moet worden verwijderd.

## <a name="hierarchies"></a>Hiërarchieën

De Azure Time Series Insights Explorer ondersteunt de bewerkingen voor het **maken**, **lezen**, **bijwerken**en **verwijderen** van hiërarchieën in de browser. 

Selecteer om te beginnen de **model** weergave in de weer gave **analyse** van Time Series Insights Explorer.

### <a name="create-a-single-hierarchy"></a>Een enkele hiërarchie maken

1. Ga naar het deel venster voor het selecteren van de tijdreeks model en selecteer **hiërarchieën** in het menu. Alle hiërarchieën die zijn gekoppeld aan uw geselecteerde Time Series Insights omgeving worden weer gegeven.

    [![een hiërarchie maken via het deel venster.](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png#lightbox)

1. Selecteer **+ Toevoegen**.

    [knop ![hiërarchie + toevoegen.](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png#lightbox)

1. Selecteer **+ niveau toevoegen** in het rechterdeel venster.

    [![een niveau toevoegen aan de hiërarchie.](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png)](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png#lightbox)

1. Voer de hiërarchie Details in en selecteer **Opslaan**.

    [de hiërarchie Details ![opgeven.](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png)](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Bulksgewijs een of meer hiërarchieën uploaden

> [!TIP]
> U kunt uw hiërarchieën opslaan op uw bureau blad in JSON. Het gedownloade JSON-bestand kan vervolgens worden geüpload door de volgende stappen uit te voeren.

1. Selecteer **JSON uploaden**.
1. Selecteer het bestand dat de hiërarchie lading bevat.
1. Selecteer **Uploaden**.

    [![selecties voor het bulksgewijs uploaden van hiërarchieën.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Eén hiërarchie bewerken

1. Selecteer de hiërarchie en selecteer het pictogram **bewerken** of **Potlood**.
1. Breng de gewenste wijzigingen aan en selecteer **Opslaan**.

    [![selecties voor het bewerken van één hiërarchie.](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png#lightbox)

### <a name="delete-a-hierarchy"></a>Een hiërarchie verwijderen

1. Selecteer de hiërarchie en selecteer het pictogram **verwijderen** of **afval lade**. 

    [Als u een hiërarchie ![verwijderen, selecteert u de knop verwijderen.](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png#lightbox)

1. Bevestig de verwijdering door **verwijderen**te selecteren.

## <a name="types"></a>Typen

De Azure Time Series Insights Explorer ondersteunt het type **Create**-, **Read**-, **Update**-en **Delete** -bewerkingen in de browser. 

Selecteer om te beginnen de **model** weergave in de weer gave **analyse** van Time Series Insights Explorer.

### <a name="create-a-single-type"></a>Een enkel type maken

1. Ga naar het deel venster voor het selecteren van de tijdreeks model en selecteer **typen** in het menu. Alle typen die aan uw geselecteerde Time Series Insights omgeving zijn gekoppeld, worden weer gegeven.

    [![deel venster time series-model typen.](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png#lightbox)

1. Selecteer **+ toevoegen** om het pop-upvenster **een nieuw type toevoegen** modaal weer te geven.
1. Voer eigenschappen en variabelen in voor uw type. Klik op **Opslaan**als u het bestand hebt ingevoerd. 

    [![configuratie-instellingen om een type toe te voegen.](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Bulksgewijs uploaden van een of meer typen

> [!TIP]
> U kunt uw typen in JSON opslaan op uw bureau blad. Het gedownloade JSON-bestand kan vervolgens worden geüpload door de volgende stappen uit te voeren.

1. Selecteer **JSON uploaden**.
1. Selecteer het bestand dat het type Payload bevat.
1. Selecteer **Uploaden**.

    [opties voor het uploaden van ![bulksgewijs typen.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png#lightbox)

### <a name="edit-a-single-type"></a>Eén type bewerken

1. Selecteer het type en selecteer het pictogram **bewerken** of **Potlood**.
1. Breng de gewenste wijzigingen aan en selecteer **Opslaan**.

    [![een type in het deel venster te bewerken.](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png#lightbox)

### <a name="delete-a-type"></a>Een type verwijderen

1. Selecteer het type en selecteer het pictogram voor **verwijderen** of **afval lade**. .

   [![een type verwijderen door verwijderen te selecteren.](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png#lightbox)

1. Bevestig de verwijdering door **verwijderen**te selecteren.

## <a name="next-steps"></a>Volgende stappen

- Lees [gegevens modellering](./time-series-insights-update-tsm.md)voor meer informatie over het time series-model.

- Lees voor meer informatie over de preview-versie [visualiseren van gegevens in de preview-versie van Azure time series Insights](./time-series-insights-update-explorer.md).

- Lees [ondersteunde JSON-vormen](./time-series-insights-send-events.md#supported-json-shapes)voor meer informatie over ondersteunde JSON-vormen.
