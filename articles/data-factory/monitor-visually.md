---
title: Azure Data Factory visueel bewaken
description: Meer informatie over het visueel bewaken van Azure-gegevens fabrieken
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/19/2018
ms.openlocfilehash: 85b1d6b532ba11819947558226291e62af6b5119
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/07/2020
ms.locfileid: "75690933"
---
# <a name="visually-monitor-azure-data-factory"></a>Azure Data Factory visueel bewaken

Zodra u een pijp lijn in Azure Data Factory hebt gemaakt en gepubliceerd, kunt u deze koppelen aan een trigger of hand matig een ad-hoc-uitvoering starten. U kunt al uw pijplijn uitvoeringen in de Azure Data Factory gebruikers ervaring zelf bewaken. Als u de bewakings ervaring wilt openen, selecteert u de **Monitor & tegel beheren** op de blade Data Factory van de [Azure Portal](https://portal.azure.com/). Als u zich al in de ADF-UX bevindt, klikt u op het pictogram **bewaken** in de zijbalk aan de linkerkant.

Alle data factory-uitvoeringen worden weer gegeven in de lokale tijd zone van de browser. Als u de tijd zone wijzigt, worden alle datum-en tijd velden uitgelijnd op het veld dat u hebt geselecteerd.

## <a name="monitor-pipeline-runs"></a>Pijplijnuitvoeringen controleren

De standaard weergave voor bewaking is de lijst met pijplijn uitvoeringen in de geselecteerde tijds periode. De volgende kolommen worden weer gegeven:

| **De naam van kolom** | **Beschrijving** |
| --- | --- |
| Naam van pijp lijn | Naam van de pijp lijn |
| Acties | Pictogrammen waarmee u de details van de activiteit kunt bekijken, annuleren of de pijp lijn opnieuw moet uitvoeren |
| Start uitvoeren | Begin datum en-tijd voor de pijplijn uitvoering (MM/DD/JJJJ, uu: MM: SS AM/PM) |
| Duur | Uitvoerings duur (UU: MM: SS) |
| Geactiveerd door | De naam van de trigger die de pijp lijn heeft gestart |
| Status | **Mislukt**, **geslaagd**, **in behandeling**, **geannuleerd**of in **wachtrij** |
| Aantekeningen | Filter bare labels die zijn gekoppeld aan een pijp lijn  |
| Parameters | Para meters voor de pijp lijn run (naam/waarde-paren) |
| Fout | Als de pijp lijn is mislukt, wordt de uitvoerings fout |
| Uitvoerings-id | ID van de pijplijn uitvoering |

![Lijst weergave voor het bewaken van pijplijn uitvoeringen](media/monitor-visually/pipeline-runs.png)

U moet hand matig de knop **vernieuwen** selecteren om de lijst met pijp lijn-en activiteit uitvoeringen te vernieuwen. Automatisch vernieuwen wordt op dit moment niet ondersteund.

![Knop Vernieuwen](media/monitor-visually/refresh.png)

## <a name="monitor-activity-runs"></a>Uitvoering van activiteiten controleren

Als u de uitvoering van de activiteit voor elke pijplijn uitvoering wilt weer geven, selecteert u het pictogram **activiteit uitvoeringen weer geven** in de kolom **acties** . In de lijst weergave worden de uitvoeringen van activiteiten weer gegeven die overeenkomen met elke pijplijn uitvoering.

| **De naam van kolom** | **Beschrijving** |
| --- | --- |
| Activiteitsnaam | De naam van de activiteit in de pijp lijn |
| Type activiteit | Type activiteit, zoals **copy**, **ExecuteDataFlow**of **AzureMLExecutePipeline** |
| Acties | Pictogrammen waarmee u JSON-invoer gegevens, JSON-uitvoer gegevens of gedetailleerde activiteiten voor bewaking kunt weer geven | 
| Start uitvoeren | Begin datum en-tijd voor de uitvoering van de activiteit (MM/DD/JJJJ, uu: MM: SS AM/PM) |
| Duur | Uitvoerings duur (UU: MM: SS) |
| Status | **Mislukt**, **geslaagd**, **in uitvoering**of **geannuleerd** |
| Integration Runtime | Waarop Integration Runtime de activiteit is uitgevoerd |
| Gebruikerseigenschappen | Door de gebruiker gedefinieerde eigenschappen van de activiteit |
| Fout | Als de activiteit is mislukt, wordt de uitvoerings fout |
| Uitvoerings-id | ID van de uitvoering van de activiteit |

![Lijst weergave voor uitvoeringen van bewakings activiteiten](media/monitor-visually/activity-runs.png)

### <a name="promote-user-properties-to-monitor"></a>Gebruikers eigenschappen promo veren om te bewaken

Promoot elke pijplijn activiteit eigenschap als een gebruikers eigenschap, zodat deze een entiteit wordt die u bewaken. U kunt bijvoorbeeld de **bron** -en **doel** eigenschappen van de Kopieer activiteit in uw pijp lijn promo veren als gebruikers eigenschappen. Selecteer **automatisch genereren** om de eigenschappen van de **bron** -en **doel** gebruiker te genereren voor een Kopieer activiteit.

![Gebruikers eigenschappen maken](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> U kunt Maxi maal vijf eigenschappen van pijplijn activiteit verhogen als gebruikers eigenschappen.

Nadat u de gebruikers eigenschappen hebt gemaakt, kunt u deze bewaken in de controle lijst weergaven. Als de bron voor de Kopieer activiteit een tabel naam is, kunt u de naam van de bron tabel bewaken als een kolom in de lijst weergave voor uitvoeringen van activiteit.

![Lijst met uitgevoerde activiteiten zonder gebruikers eigenschappen](media/monitor-visually/monitor-user-properties-image2.png)

![Kolommen voor gebruikers eigenschappen toevoegen aan de lijst met uitvoeringen van activiteit](media/monitor-visually/monitor-user-properties-image3.png)

![Lijst met uitgevoerde activiteiten met kolommen voor gebruikers eigenschappen](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="configure-the-list-view"></a>De lijst weergave configureren

### <a name="order-and-filter"></a>Ordenen en filteren

Scha kelen tussen de uitvoering van de pijp lijn in aflopende of oplopende volg orde op basis van de start tijd. Filter pijplijn wordt uitgevoerd met behulp van de volgende kolommen:

| **De naam van kolom** | **Beschrijving** |
| --- | --- |
| Naam van pijp lijn | Filter op de naam van de pijp lijn. |
| Start uitvoeren |  Bepaal het tijds bereik van de pijplijn uitvoeringen wordt weer gegeven. Opties bevatten snelle filters voor de **afgelopen 24 uur**, de **afgelopen week**, de **afgelopen 30 dagen** of het selecteren van een aangepaste datum en tijd. |
| Uitvoerings status | Filter wordt uitgevoerd op status: **geslaagd**, **mislukt**, in **wachtrij**, **geannuleerd**of wordt **uitgevoerd**. |
| Aantekeningen | Filteren op labels die zijn toegepast op elke pijp lijn |
| Uitvoerbewerkingen | Filteren of u reran-pijp lijnen wilt zien |

![Opties voor filteren](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>Kolommen toevoegen of verwijderen
Klik met de rechter muisknop op de koptekst van de lijst weergave en kies kolommen die u wilt weer geven in de lijst weergave.

![Opties voor kolommen](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>Kolom breedten aanpassen
Verg root of verklein de kolom breedten in de lijst weergave door met de muis aanwijzer over de kolomkop te bewegen.

## <a name="rerun-activities-inside-a-pipeline"></a>Activiteiten in een pijp lijn opnieuw uitvoeren

U kunt activiteiten in een pijp lijn opnieuw uitvoeren. Selecteer **uitvoering van activiteit weer geven**en selecteer vervolgens de activiteit in de pijp lijn van welk punt u de pijp lijn opnieuw wilt uitvoeren.

![Uitvoeringen van activiteit bekijken](media/monitor-visually/rerun-activities-image1.png)

![Een uitvoering van een activiteit selecteren](media/monitor-visually/rerun-activities-image2.png)

### <a name="rerun-from-failed-activity"></a>Opnieuw uitvoeren op basis van mislukte activiteit

Als een activiteit mislukt, er een time-out optreedt of de bewerking wordt geannuleerd, kunt u de pijp lijn opnieuw uitvoeren vanaf de mislukte activiteit door **opnieuw uitvoeren te selecteren op basis van de mislukte activiteit**.

![Mislukte activiteit opnieuw uitvoeren](media/monitor-visually/rerun-failed-activity.png)

### <a name="view-rerun-history"></a>Geschiedenis van opnieuw uitvoeren weer geven

U kunt de geschiedenis van opnieuw uitvoeren weer geven voor alle pijplijn uitvoeringen in de lijst weergave.

![Geschiedenis weergeven](media/monitor-visually/rerun-history-image1.png)

U kunt ook de uitvoerings geschiedenis weer geven voor een bepaalde pijplijn uitvoering.

![Geschiedenis voor een pijplijn uitvoering weer geven](media/monitor-visually/rerun-history-image2.png)

## <a name="gantt-views"></a>Gantt-weer gaven

Gebruik Gantt-weer gaven om uw pijp lijnen en activiteiten uitvoeringen snel te visualiseren.

![Voor beeld van een Gantt-diagram](media/monitor-visually/gantt1.png)

U kunt de Gantt-weer gave per pijp lijn of groep bekijken op aantekeningen/Tags die u hebt gemaakt in uw pijp lijnen.

![Aantekeningen in Gantt-diagrammen](media/monitor-visually/gantt2.png)

De lengte van de balk informeert de duur van de pijp lijn. U kunt ook de balk selecteren om meer details weer te geven.

![Duur Gantt-diagram](media/monitor-visually/gantt3.png)

## <a name="guided-tours"></a>Rond leidingen
Selecteer het **informatie** pictogram linksonder. Selecteer vervolgens rond **leidingen** voor stapsgewijze instructies voor het bewaken van de uitvoering van uw pijp lijn en activiteit.

![Rond leidingen](media/monitor-visually/guided-tours.png)

## <a name="alerts"></a>Waarschuwingen

U kunt waarschuwingen activeren over ondersteunde metrische gegevens in Data Factory. Selecteer > waarschuwingen **bewaken** **& metrische gegevens** op de pagina Data Factory bewaking om aan de slag te gaan.

![Pagina Data Factory-monitor](media/monitor-visually/alerts01.png)

Bekijk de volgende video voor een inleiding en demonstratie van zeven minuten voor deze functie:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>Waarschuwingen maken

1.  Selecteer **nieuwe waarschuwings regel** om een nieuwe waarschuwing te maken.

    ![Knop nieuwe waarschuwings regel](media/monitor-visually/alerts02.png)

1.  Geef de naam van de regel op en selecteer de ernst van de waarschuwing.

    ![Vakken voor naam en ernst van de regel](media/monitor-visually/alerts03.png)

1.  Selecteer de waarschuwings criteria.

    ![Vak voor doel criteria](media/monitor-visually/alerts04.png)

    ![Lijst met criteria](media/monitor-visually/alerts05.png)

1.  De waarschuwings logica configureren. U kunt een waarschuwing maken voor de geselecteerde metrische gegevens voor alle pijp lijnen en bijbehorende activiteiten. U kunt ook een bepaald activiteitstype, de naam van de activiteit, de naam van de pijp lijn of het type fout selecteren.

    ![Opties voor het configureren van waarschuwings logica](media/monitor-visually/alerts06.png)

1.  E-mail-, SMS-, push-en voicemail meldingen configureren voor de waarschuwing. Een actie groep maken of een bestaande kiezen, voor de waarschuwings meldingen.

    ![Opties voor het configureren van meldingen](media/monitor-visually/alerts07.png)

    ![Opties voor het toevoegen van een melding](media/monitor-visually/alerts08.png)

1.  De waarschuwings regel maken.

    ![Opties voor het maken van een waarschuwings regel](media/monitor-visually/alerts09.png)

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het bewaken en beheren van pijp lijnen het [programmatische artikel pijp lijnen bewaken en beheren](https://docs.microsoft.com/azure/data-factory/monitor-programmatically) .
