---
title: Overzicht van de Azure HPC-cache
description: Hierin wordt de Azure HPC-cache, een oplossing voor bestands toegang voor High-Performance Computing beschreven
author: ekpgh
ms.service: hpc-cache
ms.topic: overview
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 2a008d22de5df8d091e868153205697b4bb343ee
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79241190"
---
# <a name="what-is-azure-hpc-cache"></a>Wat is Azure HPC Cache?

De Azure HPC-cache versnelt de toegang tot uw gegevens voor HPC-taken (High-Performance Computing). Door bestanden in Azure in de cache op te slaan, brengt Azure HPC cache de schaal baarheid van Cloud Computing over aan uw bestaande werk stroom. Deze service kan ook worden gebruikt voor werk stromen waarbij uw gegevens op WAN-koppelingen worden opgeslagen, zoals in uw lokale Data Center-network-attached storage omgeving (NAS).

De Azure HPC-cache is eenvoudig te starten en te controleren vanaf de Azure Portal. Bestaande NFS-opslag of nieuwe BLOB-containers kunnen deel uitmaken van de geaggregeerde naam ruimte, waardoor client toegang eenvoudig kan worden gemaakt, zelfs als u het back-end-opslag doel wijzigt.

## <a name="use-cases"></a>Gebruiksvoorbeelden

Azure HPC cache verbetert de productiviteit die het beste is voor werk stromen als deze:

* Werk stroom voor lezen van zware bestands toegang
* Gegevens die zijn opgeslagen in met NFS toegankelijke opslag, Azure Blob of beide
* Compute-farms van Maxi maal 75.000 CPU-kernen

Een Azure HPC-cache kan worden toegevoegd aan een groot aantal werk stromen in veel branches. Elk systeem waarbij een groot aantal machines toegang moet hebben tot een set bestanden op schaal en met lage latentie voor deel is van deze service. De volgende secties geven specifieke voor beelden.

### <a name="visual-effects-vfx-rendering"></a>Rendering van visuele effecten (VFX)

In media en entertainment kan Azure HPC cache de toegang tot gegevens versnellen voor tijdgebonden rendering-projecten. VFX rendering-werk stromen vereisen vaak verwerking van de laatste minuut door grote aantallen reken knooppunten. Gegevens voor deze werk stromen bevinden zich doorgaans in een on-premises NAS-omgeving. Met de Azure HPC-cache kunt u de bestands gegevens in de Cloud opslaan om de latentie te verminderen en de flexibiliteit te verhogen voor rendering op aanvraag.

### <a name="life-sciences"></a>Biowetenschappen

Veel biowetenschappen-werk stromen kunnen profiteren van scale-out bestands caches.

Een onderzoek instituut dat de genomic-analyse werk stromen in azure wil bepoorten, kan deze eenvoudig verplaatsen met behulp van de Azure HPC-cache. Omdat de cache POSIX-bestands toegang biedt, zijn er geen wijzigingen aan de client zijde nodig om de bestaande client werk stroom in de Cloud uit te voeren.

De Azure HPC-cache kan ook worden gebruikt om de efficiëntie te verbeteren van taken zoals secundaire analyse, farmacologische simulatie of AI-gestuurde afbeeldings analyse.

### <a name="financial-services-analytics"></a>Analyse voor financiële dienstverlening

Een implementatie van een Azure HPC-cache kan bijdragen aan het versnellen van kwantitatieve analyse berekeningen, workloads voor risico analyse en Monte Carlo-simulaties om financiële diensten bedrijven beter inzicht te geven in het nemen van strategische beslissingen.

## <a name="region-availability"></a>Beschikbaarheid in regio’s

De Azure HPC-cache is beschikbaar in de volgende Azure-regio's:

* VS - oost
* VS - oost 2
* Europa - noord
* Europa -west
* Azië - zuidoost
* Sydney
* VS - west 2
* Korea - centraal

Controleer de [product pagina van de Azure HPC-cache](https://azure.microsoft.com/services/hpc-cache) voor de meest recente beschikbaarheids gegevens.

## <a name="service-availability"></a>Service beschikbaarheid

U moet toegang aanvragen voor elk abonnement dat u wilt gebruiken met de Azure HPC-cache. Deze beperking helpt de kwaliteit van de service in de eerste maanden van algemene Beschik baarheid te garanderen.

Vraag toegang aan door [dit formulier](https://aka.ms/onboard-hpc-cache)in te vullen. Nadat uw abonnement is toegevoegd aan de lijst met toegangs rechten, kunt u caches maken.

## <a name="next-steps"></a>Volgende stappen

* Lees de [product pagina van de Azure HPC-cache](https://azure.microsoft.com/services/hpc-cache) voor meer informatie over de mogelijkheden
* Meer informatie over product [vereisten](hpc-cache-prereqs.md)
* [Een HPC-cache van Azure maken](hpc-cache-create.md) op basis van de Azure Portal
