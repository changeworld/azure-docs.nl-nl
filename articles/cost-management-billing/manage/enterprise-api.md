---
title: Facturerings-API's voor Azure Enterprise-klanten | Microsoft Docs
description: Lees hier alles over de rapportage-API's waarmee klanten met een Azure Enterprise Agreement verbruiksgegevens programmatisch kunnen ophalen.
author: mumami
tags: billing
ms.service: cost-management-billing
ms.topic: reference
ms.date: 02/14/2020
ms.author: banders
ms.openlocfilehash: 9f9552c134fa7653e4100cae3c731e4c670d44e3
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/22/2020
ms.locfileid: "77560538"
---
# <a name="overview-of-reporting-apis-for-enterprise-customers"></a>Overzicht van rapportage-API's voor Enterprise-klanten
Met de rapportage-API's kunnen klanten van Azure Enterprise programmatisch verbruiks-en factureringsgegevens ophalen en gebruiken in de tools voor gegevensanalyse van hun voorkeur. Enterprise-klanten hebben een [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) ondertekend met Azure om overeengekomen monetaire toezeggingen te doen en toegang te krijgen tot aangepaste prijzen voor Azure-resources.

Alle parameters voor datum en tijd die zijn vereist voor API’s, moeten worden weergegeven in UTC-waarden (Coordinated Universal Time). Waarden die worden geretourneerd via API’s, hebben de UTC-indeling.

## <a name="enabling-data-access-to-the-api"></a>Gegevenstoegang tot de API inschakelen
* **De API-sleutel genereren of ophalen**: meld u aan bij de Enterprise-portal en navigeer naar Reports > Download Usage > API Access Key om de API-sleutel te genereren of op te halen.
* **Sleutels doorgeven aan de API**: de API-sleutel moet voor elke aanroep worden door gegeven voor verificatie en autorisatie. De volgende eigenschap moet aanwezig zijn in de HTTP-headers:

|Aanvraagheadersleutel | Waarde|
|-|-|
|Autorisatie| Geef de waarde in deze indeling op: **bearer {API_SLEUTEL}** <br/> Voorbeeld: bearer eyr....09|

## <a name="consumption-apis"></a>Verbruiks-API's
U vindt [hier](https://consumption.azure.com/swagger/ui/index) een Swagger-eindpunt voor de hieronder beschreven API's waarmee eenvoudig introspectie van de API mogelijk is, naast de mogelijkheid om client-SDK's te genereren met behulp van [AutoRest](https://github.com/Azure/AutoRest) of [Swagger CodeGen](https://swagger.io/swagger-codegen/). Gegevens vanaf 1 mei 2014 zijn beschikbaar via deze API.

* **Saldo en overzicht**: de [API voor saldo en overzicht](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) biedt een maandelijks overzicht van de informatie over saldi, nieuwe aankopen, Azure Marketplace-servicekosten, correcties en overschrijdingskosten.

* **Gebruiksgegevens**: de [API voor gebruiksgegevens](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) biedt een dagelijkse uitsplitsing van de verbruikte hoeveelheden en de geschatte kosten per inschrijving. Het resultaat bevat ook informatie over instanties, meters en afdelingen. Via de API kunnen gegevens worden opgevraagd per factureringsperiode of aan de hand van een opgegeven begin- en einddatum.

* **Marketplace Store-kosten**: de [API voor Marketplace Store-kosten](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) retourneert op gebruik gebaseerde Marketplace-kosten, uitgesplitst per dag van de opgegeven factureringsperiode of op basis van de begin- en einddatum (eenmalige kosten zijn niet inbegrepen).

* **Prijzenoverzicht:** de [API voor prijzenoverzicht](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) biedt het toepasselijke tarief voor elke meter voor de aangegeven inschrijvings- en factureringsperiode.

* **Details van gereserveerde instantie**: de [API voor het gebruik van gereserveerde instanties](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) retourneert het gebruik van de aankopen van gereserveerde instanties. De [API voor kosten van gereserveerde instanties](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) toont de gemaakte factureringstransacties.

## <a name="data-freshness"></a>Nieuwheid van gegevens
In de respons van alle bovenstaande API zijn Etags opgenomen. Een wijziging van een Etag geeft aan dat de gegevens zijn vernieuwd.  In volgende aanroepen naar dezelfde API met dezelfde parameters geeft u de vastgelegde Etag door met de sleutel 'If-None-Match' in de header van de HTTP-aanvraag. De statuscode van de respons is 'NotModified' als de gegevens niet meer zijn vernieuwd en er geen gegevens worden geretourneerd. De API retourneert de volledige gegevensset voor de vereiste periode wanneer er sprake is van een Etag-wijziging.

## <a name="helper-apis"></a>Helper-API's
 **Factureringsperioden opvragen:** de [API voor factureringsperioden](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) retourneert een lijst met factureringsperioden met verbruiksgegevens voor een inschrijving (in omgekeerd chronologische volgorde). Elke periode bevat een eigenschap die verwijst naar de API-route voor de vier sets gegevens: BalanceSummary, UsageDetails, Marketplace Charges en Price Sheet.


## <a name="api-response-codes"></a>API-responscodes   
|Statuscode respons|Bericht|Beschrijving|
|-|-|-|
|200| OK|Geen fout|
|401| Niet geautoriseerd| De API-sleutel is niet gevonden, is ongeldig, is verlopen, enzovoort.|
|404| Niet beschikbaar| Rapporteindpunt niet gevonden|
|400| Onjuiste aanvraag| Ongeldige parameters: datumbereiken, EA-nummers, enzovoort.|
|500| Serverfout| Onverwachte fout opgetreden bij het verwerken van de aanvraag|
