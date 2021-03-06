---
title: Real-user-metingen in azure Traffic Manager
description: In deze inleiding leert u hoe Azure Traffic Manager Real-user-metingen.
services: traffic-manager
documentationcenter: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: rohink
ms.custom: ''
ms.openlocfilehash: e38e1255b1a84ab5d3fd37e16eb65c76001dbfa1
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76938438"
---
# <a name="traffic-manager-real-user-measurements-overview"></a>Overzicht van Traffic Manager Real-user-metingen

Wanneer u een profiel voor Traffic Manager instelt voor het gebruik van de methode voor de route ring van prestaties, wordt in de service gekeken naar waar de aanvragen voor de DNS-query afkomstig zijn en worden de routerings beslissingen doorgestuurd naar de Azure-regio die de laagste latentie geeft. Dit wordt bereikt door gebruik te maken van de netwerk latentie intelligentie die Traffic Manager onderhoudt voor verschillende netwerken van eind gebruikers.

Met Real-user-metingen kunt u meting van de netwerk latentie meten in azure-regio's, van de client toepassingen die door uw eind gebruikers worden gebruikt, en deze informatie ook Traffic Manager beschouwen bij het nemen van routerings beslissingen. Als u ervoor kiest om de Real-user-metingen te gebruiken, kunt u de nauw keurigheid van de route ring verg Roten voor aanvragen die afkomstig zijn van deze netwerken waar uw eind gebruikers zich bevinden. 

## <a name="how-real-user-measurements-work"></a>Hoe Real-user-metingen werken

Real-user-metingen werk uit door uw client toepassingen een latentie te geven voor Azure-regio's, zoals deze wordt weer gegeven in de netwerken van de eind gebruiker waarin ze worden gebruikt. Als u bijvoorbeeld een webpagina hebt die wordt geopend door gebruikers op verschillende locaties (bijvoorbeeld in de Noord-Amerikaanse regio's), kunt u Real-user-metingen gebruiken met de methode voor het routeren van prestaties om ze te downloaden naar de beste Azure-regio waarin uw server de toepassing wordt gehost.

U begint met het insluiten van een door Azure verschafte java script (met een unieke sleutel) in uw webpagina's. Wanneer een gebruiker die webpagina bezoekt, worden de query's van de Java script Traffic Manager om informatie over de Azure-regio's te verkrijgen die moeten worden gemeten. De service retourneert een set eind punten naar het script dat deze gebieden vervolgens opeenvolgend meet door een enkele pixel afbeelding te downloaden die wordt gehost in die Azure-regio's en de latentie te onderscheiden tussen het tijdstip waarop de aanvraag is verzonden en het tijdstip waarop de eerste byte is ontvangen. . Deze metingen worden vervolgens weer gegeven aan de Traffic Manager service.

In de loop van de tijd treedt dit veel tijd in beslag en veel netwerken leiden tot Traffic Manager meer nauw keurige informatie over de latentie kenmerken van de netwerken waarin uw eind gebruikers zich bevinden. Deze informatie wordt gestart om te worden opgenomen in de routerings beslissingen die zijn gemaakt door Traffic Manager. Als gevolg hiervan is het een grotere nauw keurigheid van deze beslissingen op basis van de Real-user-metingen verzonden.

Wanneer u Real-user-metingen gebruikt, wordt u gefactureerd op basis van het aantal metingen dat naar Traffic Manager wordt verzonden. Ga naar de [pagina met Traffic Manager prijzen](https://azure.microsoft.com/pricing/details/traffic-manager/)voor meer informatie over de prijzen.

## <a name="faqs"></a>Veelgestelde vragen

* [Wat zijn de voor delen van het gebruik van Real-user-metingen?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-the-benefits-of-using-real-user-measurements)

* [Kan ik Real-user-metingen gebruiken met niet-Azure-regio's?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-real-user-measurements-with-non-azure-regions)

* [Welke routerings methode heeft voor delen van Real-user-metingen?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#which-routing-method-benefits-from-real-user-measurements)

* [Moet ik elk profiel afzonderlijk inschakelen Real-user-metingen?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-enable-real-user-measurements-each-profile-separately)

* [Hoe kan ik Real-user-metingen voor mijn abonnement uitschakelen?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-do-i-turn-off-real-user-measurements-for-my-subscription)

* [Kan ik Real-user-metingen gebruiken met andere client toepassingen dan webpagina's?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-real-user-measurements-with-client-applications-other-than-web-pages)

* [Hoeveel metingen worden er uitgevoerd telkens wanneer mijn Real-user-metingen ingeschakelde webpagina wordt weer gegeven?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered)

* [Is er een vertraging voordat Real-user-metingen script wordt uitgevoerd op mijn webpagina?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage)

* [Kan ik Real-user-metingen gebruiken met alleen de Azure-regio's die ik wil meten?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-real-user-measurements-with-only-the-azure-regions-i-want-to-measure)

* [Kan ik het aantal metingen beperken dat is uitgevoerd op een specifiek aantal?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-limit-the-number-of-measurements-made-to-a-specific-number)

* [Kan ik zien welke metingen worden uitgevoerd door mijn client toepassing als onderdeel van Real-user-metingen?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements)

* [Kan ik het meting script van Traffic Manager wijzigen?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-modify-the-measurement-script-provided-by-traffic-manager)

* [Is het mogelijk dat anderen de sleutel zien die ik gebruik met Real-user-metingen?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements)

* [Kunnen anderen mijn RUM-sleutel misbruiken?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-others-abuse-my-rum-key)

* [Moet ik de meting java script in al mijn webpagina's plaatsen?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages)

* [Kan informatie over mijn eind gebruikers worden geïdentificeerd door Traffic Manager als ik Real-user-metingen gebruik?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements)

* [Moet de afmeting van de webpagina Real-user-metingen Traffic Manager voor route ring gebruiken?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing)

* [Moet ik een service op Azure-regio's hosten om met Real-user-metingen te gebruiken?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements)

* [Neemt mijn Azure-bandbreedte gebruik toe wanneer ik Real-user-metingen gebruik?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements)

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het gebruik [van Real-User-metingen met](traffic-manager-create-rum-web-pages.md) webpagina's
- Meer informatie [over de werking van Traffic Manager](traffic-manager-overview.md)
- Meer informatie over [mobiel centrum](https://docs.microsoft.com/mobile-center/)
- Meer informatie over de [routerings methoden voor verkeer](traffic-manager-routing-methods.md) die door Traffic Manager worden ondersteund
- Meer informatie over het [maken van een Traffic Manager profiel](traffic-manager-create-profile.md)

