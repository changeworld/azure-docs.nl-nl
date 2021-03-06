---
title: Voor beelden van gebruikers scenario's voor de Text Analytics-API
titleSuffix: Azure Cognitive Services
description: In dit artikel vindt u enkele algemene scenario's voor het integreren van de Text Analytics-API in uw-services en-processen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: aahi
ms.openlocfilehash: 6847059de2a8685a56719f07a041a40456f2aa06
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79219245"
---
# <a name="example-user-scenarios-for-the-text-analytics-api"></a>Voor beelden van gebruikers scenario's voor de Text Analytics-API

De Text Analytics-API is een Cloud service die geavanceerde verwerking van natuurlijke taal voor tekst biedt. In dit artikel worden enkele voor beelden van gebruiks voorbeelden beschreven voor het integreren van de API in uw bedrijfs oplossingen en-processen. 

## <a name="analyze-survey-results"></a>Enquête resultaten analyseren

Teken inzichten van klant-en werknemers enquête resultaten door de onbewerkte tekst reacties te verwerken met behulp van Sentimentanalyse. Voeg de resultaten voor analyse, follow-up en Route beschrijvingen samen.

![Een afbeelding waarin wordt beschreven hoe u sentiment analyse kunt uitvoeren op enquêtes van klanten en werk nemers.](media/use-cases/survey-results.svg)

## <a name="analyze-recorded-inbound-customer-calls"></a>Geregistreerde inkomende klant oproepen analyseren

Haal inzichten uit de aanroepen van Customer Services met behulp van Text to Speech, Sentimentanalyse en Sleuteltermextractie. De resultaten in Power BI dash board of een portal weer geven om klanten beter te begrijpen, trends van klanten service te markeren en de klant betrokkenheid te stimuleren. API-aanvragen verzenden als een batch voor rapportage of in realtime voor interventie. Zie de voorbeeld code [op github](https://github.com/rlagh2/callcenteranalytics).

![Een afbeelding waarin wordt beschreven hoe u inzichten kunt automatiseren van de klant service aanroepen met behulp van sentiment-analyse](media/use-cases/azure-inbound.svg)

## <a name="process-and-categorize-support-incidents"></a>Ondersteunings incidenten verwerken en categoriseren

Gebruik Sleuteltermextractie en entiteits herkenning om ondersteunings aanvragen te verwerken die in ongestructureerde tekst indeling zijn verzonden. Gebruik de geëxtraheerde frasen en entiteiten om de aanvragen voor resource planning en trend analyse te categoriseren.

![Een afbeelding waarin wordt beschreven hoe u de sleutel woorden extractie en entiteits herkenning kunt gebruiken om incident rapporten en trends te categoriseren](media/use-cases/support-incidents.svg)

## <a name="monitor-your-products-social-media-feeds"></a>De feeds voor sociale media van uw product bewaken

Feedback van gebruikers producten op de Twitter-of Facebook-pagina van uw product bewaken. Gebruik de gegevens om klant sentiment te analyseren naar nieuwe producten, sleutel zinnen te extra heren over functies en functie aanvragen of klachten van klanten te adresseren wanneer deze zich voordoen. Zie het voor beeld [Microsoft flow sjabloon](https://flow.microsoft.com/galleries/public/templates/2680d2227d074c4d901e36c66e68f6f9/run-sentiment-analysis-on-tweets-and-push-results-to-a-power-bi-dataset/).

![Een afbeelding waarin wordt beschreven hoe u uw product-en bedrijfs feedback op sociale media kunt controleren met behulp van sleutel woorden extractie](media/use-cases/social-feed.svg)

## <a name="classify-and-redact-documents-that-have-sensitive-information"></a>Documenten met gevoelige informatie classificeren en redigeren

Gebruik benoemde entiteit herkenning om persoonlijke en gevoelige gegevens in documenten te identificeren. Gebruik de gegevens voor het classificeren van documenten of redactie ze zodanig dat ze veilig kunnen worden gedeeld.

![Een afbeelding waarin wordt beschreven hoe u NER kunt gebruiken om persoonlijke gegevens te detecteren en documenten te classificeren en te redigeren](media/use-cases/sensitive-docs.jpg)

## <a name="next-steps"></a>Volgende stappen

* [Wat is de Text Analytics-API?](overview.md)
* [Een aanvraag verzenden naar het Text Analytics-API met behulp van de client bibliotheek](quickstarts/text-analytics-sdk.md)
