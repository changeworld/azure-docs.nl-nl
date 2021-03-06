---
title: Overzicht van automatische afstemming
description: Azure SQL Database analyseert SQL-query en past automatisch aan de werk belasting van de gebruiker aan.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: d4762c86268353ff0464ff3919250cd86f0038d4
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79214107"
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Azure SQL Database automatisch afstemmen

Azure SQL Database automatisch afstemmen biedt piek prestaties en stabiele workloads door continue prestaties afstemmen op basis van AI en machine learning.

Automatisch afstemmen is een volledig beheerde intelligente prestatie service die gebruikmaakt van ingebouwde intelligentie om continu query's te controleren die worden uitgevoerd op een Data Base, en de prestaties worden automatisch verbeterd. Dit wordt bereikt door de data base dynamisch aan te passen aan de veranderende werk belastingen en de aanbevelingen voor het afstemmen toe te passen. Automatische afstemming leert horizon taal van alle data bases op Azure tot en met AI, waardoor de afstemmings acties dynamisch worden verbeterd. Hoe meer data bases worden uitgevoerd met automatisch afstemmen op, hoe beter het werkt.

Azure SQL Database automatisch afstemmen kan een van de belangrijkste functies zijn die u kunt inschakelen om stabiele en piek uren voor de data base uit te voeren.

## <a name="what-can-automatic-tuning-do-for-you"></a>Wat kan automatisch afstemmen voor u doen

- Geautomatiseerde prestaties afstemmen van Azure SQL-data bases
- Automatische verificatie van prestatie verbeteringen
- Automatische terugdraai actie en automatische correctie
- Afstemmings geschiedenis
- T-SQL-scripts voor het afstemmen van acties voor hand matige implementaties
- Bewaking van proactieve werk belasting prestaties
- Mogelijkheden voor uitschalen op honderd duizenden data bases
- Positieve impact op DevOps resources en de total cost of ownership

## <a name="safe-reliable-and-proven"></a>Veilig, betrouwbaar en bewezen

Afstemmings bewerkingen die worden toegepast op data bases in Azure SQL Database zijn volledig veilig voor de prestaties van uw meest intense workloads. Het systeem is zo ontworpen dat de werk belasting van de gebruiker niet wordt verstoord. Automatische afstemmings aanbevelingen worden alleen toegepast op het tijdstip van een laag gebruik. Het systeem kan ook tijdelijk automatisch afstemmen uitschakelen om de prestaties van de werk belasting te beveiligen. In dit geval wordt het bericht ' uitgeschakeld door het systeem ' weer gegeven in Azure Portal. Automatisch afstemmen met werk belastingen met de hoogste resource prioriteit.

Automatische afstemmings mechanismen zijn verouderd en zijn perfect in verschillende miljoen data bases die worden uitgevoerd op Azure. Automatische afstemmings bewerkingen die worden toegepast, worden automatisch gecontroleerd om te zorgen voor een positieve verbetering van de prestaties van de werk belasting. Aanbevelingen voor teruggedraaide-prestaties worden dynamisch gedetecteerd en hersteld. Tijdens de registratie van de afstemmings geschiedenis bestaat een duidelijke tracering van de afstemmings verbeteringen die zijn aangebracht in elke Azure SQL Database.

![Hoe werkt automatisch afstemmen?](./media/sql-database-automatic-tuning/how-does-automatic-tuning-work.png)

Azure SQL Database automatisch afstemmen deelt de kern logica met de SQL Server Automatic tuning engine. Zie [SQL Server Automatic tuning](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)(Engelstalig) voor aanvullende technische informatie over het ingebouwde intelligentie-mechanisme.

Voor een overzicht van de werking van automatisch afstemmen en voor typische gebruiks scenario's raadpleegt u de Inge sloten video:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-automatic-tuning/player]

## <a name="enable-automatic-tuning"></a>Automatisch instellen inschakelen

U kunt [automatisch afstemmen inschakelen voor één en gegroepeerde Data bases in de Azure Portal](sql-database-automatic-tuning-enable.md) of met de T-SQL-instructie [ALTER data base](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) . U schakelt automatisch afstemmen in voor instantie-data bases in de implementatie van een beheerd exemplaar met behulp van de T-SQL-instructie [ALTER data base](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-mi-current) .

## <a name="automatic-tuning-options"></a>Opties voor automatisch afstemmen

Opties voor automatisch afstemmen die beschikbaar zijn in Azure SQL Database zijn:

| Optie voor automatisch afstemmen | Ondersteuning voor één data base en gepoolde data base | Ondersteuning voor instance data base |
| :----------------------------- | ----- | ----- |
| **Create Index** : identificeert indexen die de prestaties van uw workload kunnen verbeteren, maakt indexen en controleert automatisch of de prestaties van query's zijn verbeterd. | Ja | Nee |
| **Drop Index** : Hiermee worden dagelijks redundante en dubbele indexen geïdentificeerd, met uitzonde ring van unieke indexen en indexen die gedurende een lange periode niet zijn gebruikt (> 90 dagen). Houd er rekening mee dat deze optie niet compatibel is met toepassingen die gebruikmaken van partitie switches en index hints. Het verwijderen van niet-gebruikte indexen wordt niet ondersteund voor Premium-en Bedrijfskritiek-service lagen. | Ja | Nee |
| **Laatste goede planning afdwingen** (automatische plan correctie): identificeert SQL-query's met behulp van een uitvoerings plan dat lager is dan het vorige goede plan en voert query's uit met behulp van het laatst bekende goede plan in plaats van het teruggedraaide-abonnement. | Ja | Ja |

### <a name="automatic-tuning-for-single-and-pooled-databases"></a>Automatische afstemming voor afzonderlijke en gepoolde data bases

Automatisch afstemmen voor afzonderlijke en gegroepeerde Data bases maakt gebruik van de **Create Index**, **Drop Index**en **forceren van het laatste goede plan** data base Advisor-aanbevelingen om uw database prestaties te optimaliseren. Zie [aanbevelingen voor data base Advisor in de Azure Portal](sql-database-advisor-portal.md), in [Power shell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction)en in de [rest API](https://docs.microsoft.com/rest/api/sql/serverautomatictuning)voor meer informatie.

U kunt de aanbevelingen voor het afstemmen hand matig Toep assen met behulp van de Azure Portal of u kunt automatisch afstemmen op zichzelf laten afstemmen voor u. De voor delen van het autonoom Toep assen van het systeem om automatisch afstemmings aanbevelingen voor u toe te passen, is dat er een positieve toename van de prestaties van de werk belasting bestaat en als er geen belang rijke prestatie verbetering wordt gedetecteerd. de aanbeveling voor het afstemmen automatisch terugdraaien. In het geval van query's die worden beïnvloed door het afstemmen van aanbevelingen die niet vaak worden uitgevoerd, kan de validatie fase tot 72 uur duren.

Als u aanbevelingen voor het afstemmen toepast via T-SQL, zijn de mechanismen voor automatische prestatie validatie en omkering niet beschikbaar. Aanbevelingen die op een dergelijke manier worden toegepast, blijven actief en worden weer gegeven in de lijst met aanbevelingen voor 24-48 uur. voordat het systeem ze automatisch intrekt. Als u een aanbeveling eerder wilt verwijderen, kunt u deze uit Azure Portal verwijderen.

Automatische afstemmings opties kunnen onafhankelijk worden in-of uitgeschakeld per data base, maar ze kunnen worden geconfigureerd op SQL Database servers en toegepast op elke Data Base die instellingen overneemt van de server. SQL Database-servers kunnen Azure-standaard waarden overnemen voor instellingen voor automatisch afstemmen. De standaard instellingen van Azure op dit moment worden ingesteld op FORCE_LAST_GOOD_PLAN is ingeschakeld, CREATE_INDEX is ingeschakeld en DROP_INDEX is uitgeschakeld.

> [!IMPORTANT]
> Vanaf maart worden 2020 wijzigingen in de standaard instellingen van Azure voor automatische afstemming als volgt toegepast:
>
> - Nieuwe standaard waarden van Azure worden FORCE_LAST_GOOD_PLAN = ingeschakeld, CREATE_INDEX = uitgeschakeld en DROP_INDEX = uitgeschakeld.
> - Bestaande servers zonder automatisch afstemmen geconfigureerd voor keuren worden automatisch geconfigureerd om de nieuwe standaard waarden van Azure te overnemen. Dit geldt voor alle klanten die momenteel server instellingen voor automatisch afstemmen in een niet-gedefinieerde status hebben.
> - Nieuwe servers die worden gemaakt, worden automatisch geconfigureerd voor het overnemen van de nieuwe standaard waarden van Azure (in tegens telling tot eerdere versies van de configuratie voor automatisch afstemmen bij het maken van een nieuwe server).

Het configureren van opties voor automatisch afstemmen op een server en het overnemen van instellingen voor data bases die deel uitmaken van de bovenliggende server is een aanbevolen methode voor het configureren van automatische afstemming, omdat het beheer van automatische afstemmings opties voor een groot aantal data bases vereenvoudigt.

Zie [e-mail meldingen voor automatisch afstemmen](sql-database-automatic-tuning-email-notifications.md)voor meer informatie over het bouwen van e-mail meldingen voor aanbevelingen voor automatisch afstemmen.

### <a name="automatic-tuning-for-instance-databases"></a>Automatisch afstemmen voor exemplaar databases

Automatisch afstemmen voor instantie-data bases in de implementatie van een beheerd exemplaar ondersteunt alleen het **forceren van het laatste goede plan**. Zie voor meer informatie over het configureren van opties voor automatisch afstemmen via T-SQL [automatisch afstemmen introduceert automatisch corrigeren van plannen](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) en [automatische correctie van plannen](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning?view=sql-server-ver15#automatic-plan-correction).

## <a name="next-steps"></a>Volgende stappen

- Zie [kunst matige intelligentie voor Azure SQL-data bases](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/)voor meer informatie over de ingebouwde intelligentie die wordt gebruikt voor automatisch afstemmen.
