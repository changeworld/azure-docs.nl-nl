---
title: Resource limieten-beheerd exemplaar
description: Dit artikel bevat een overzicht van de Azure SQL Database resource limieten voor beheerde exemplaren.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, jovanpop, sachinp, sstein
ms.date: 02/25/2020
ms.openlocfilehash: 12d457d8d5e57dc4db16d9a191c7795a5f013574
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79268793"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Overzicht Azure SQL Database limieten voor beheerde exemplaar bronnen

Dit artikel bevat een overzicht van de technische kenmerken en resource limieten voor Azure SQL Database beheerde instantie en bevat informatie over het aanvragen van een verhoging van deze limieten.

> [!NOTE]
> Zie voor verschillen in ondersteunde functies en T-SQL-instructies [functie verschillen](sql-database-features.md) en [ondersteuning voor t-SQL-instructie](sql-database-managed-instance-transact-sql-information.md). Zie de [vergelijking](sql-database-service-tiers-general-purpose-business-critical.md#service-tier-comparison)van de servicelaag voor algemene verschillen tussen service lagen in één data base en een beheerd exemplaar.

## <a name="hardware-generation-characteristics"></a>Kenmerken voor het genereren van hardware

Het beheerde exemplaar heeft kenmerken en resource limieten die afhankelijk zijn van de onderliggende infra structuur en architectuur. Azure SQL Database Managed instance kan worden geïmplementeerd op twee hardware gegenereerd: Gen4 en GEN5. Hardware-generaties hebben verschillende kenmerken, zoals wordt beschreven in de volgende tabel:

|   | **Gen4** | **GEN5** |
| --- | --- | --- |
| Hardware | Intel E5-2673 v3 (Haswell) 2,4-GHz-processors, Attached SSD vCore = 1 PP (fysieke kern) | Intel E5-2673 v4 (Broadwell) 2,3-GHz en Intel SP-8160 (Skylake)-processors, Fast NVMe SSD, vCore = 1 LP (Hyper-Thread) |
| Aantal vCores | 8, 16, 24 vCores | 4, 8, 16, 24, 32, 40, 64, 80 vCores |
| Maxi maal geheugen (geheugen/kern percentage) | 7 GB per vCore<br/>Voeg meer vCores toe om meer geheugen te verkrijgen. | 5,1 GB per vCore<br/>Voeg meer vCores toe om meer geheugen te verkrijgen. |
| Maxi maal in-Memory OLTP-geheugen | Limiet voor instanties: 1-1,5 GB per vCore| Limiet voor instanties: 0,8-1,65 GB per vCore |
| Maximum aantal gereserveerde exemplaren |  Algemeen: 8 TB<br/>Bedrijfskritiek: 1 TB | Algemeen: 8 TB<br/> Bedrijfskritiek 1 TB, 2 TB of 4 TB afhankelijk van het aantal kernen |

> [!IMPORTANT]
> - Gen4-hardware wordt gefaseerd uitgevoerd en is niet meer beschikbaar voor de nieuwe implementaties. Alle nieuwe beheerde exemplaren moeten worden geïmplementeerd op GEN5-hardware.
> - Overweeg om [uw beheerde instanties te verplaatsen naar Gen 5](sql-database-service-tiers-vcore.md) -hardware, zodat u een breder scala aan vCore-en opslag schaal baarheid, versneld netwerken, beste IO-prestaties en minimale latentie kunt ervaren.

### <a name="in-memory-oltp-available-space"></a>Beschik bare ruimte in geheugen voor OLTP 

De hoeveelheid OLTP-ruimte in het geheugen in [bedrijfskritiek](sql-database-service-tier-business-critical.md) servicelaag is afhankelijk van het aantal vCores en de generatie van hardware. In de volgende tabel worden de limieten van het geheugen weer gegeven dat kan worden gebruikt voor OLTP-objecten in het geheugen.

| OLTP-ruimte in het geheugen  | **GEN5** | **Gen4** |
| --- | --- | --- |
| 4 vCores  | 3,14 GB | |   
| 8 vCores  | 6,28 GB | 8 GB |
| 16 vCores | 15,77 GB | 20 GB |
| 24 vCores | 25,25 GB | 36 GB |
| 32 vCores | 37,94 GB | |
| 40 vCores | 52,23 GB | |
| 64 vCores | 99,9 GB    | |
| 80 vCores | 131,68 GB| |

## <a name="service-tier-characteristics"></a>Kenmerken van servicelaag

Het beheerde exemplaar heeft twee service lagen: [Algemeen](sql-database-service-tier-general-purpose.md) en [bedrijfskritiek](sql-database-service-tier-business-critical.md). Deze lagen bieden [verschillende mogelijkheden](sql-database-service-tiers-general-purpose-business-critical.md), zoals wordt beschreven in de volgende tabel.

> [!Important]
> Bedrijfskritiek service-laag biedt een extra ingebouwde kopie van de instantie (secundaire replica) die kan worden gebruikt voor alleen-lezen werk belasting. Als u lees-en schrijf query's en query's met het kenmerk alleen-lezen/analyse kunt onderscheiden, krijgt u twee maal vCores en geheugen voor dezelfde prijs. De secundaire replica kan vertraging hebben op een paar seconden achter het primaire exemplaar, zodat het is ontworpen om rapportage/analytische werk belasting te offloaden die niet de exacte huidige status van gegevens nodig heeft. In de onderstaande tabel zijn **alleen-lezen query's** de query's die worden uitgevoerd op de secundaire replica.

| **Functie** | **Algemeen** | **Bedrijfskritiek** |
| --- | --- | --- |
| Aantal vCores-\* | Gen4:8, 16, 24<br/>GEN5:4, 8, 16, 24, 32, 40, 64, 80 | Gen4:8, 16, 24 <br/> GEN5:4, 8, 16, 24, 32, 40, 64, 80 <br/>\*hetzelfde aantal vCores is toegewezen voor alleen-lezen query's. |
| Maxi maal geheugen | Gen4:56 GB-168 GB (7GB/vCore)<br/>GEN5:20,4 GB-408 GB (5,1 GB/vCore)<br/>Voeg meer vCores toe om meer geheugen te verkrijgen. | Gen4:56 GB-168 GB (7GB/vCore)<br/>GEN5:20,4 GB-408 GB (5,1 GB/vCore) voor lees-en schrijf query's<br/>+ extra 20,4 GB-408 GB (5,1 GB/vCore) voor alleen-lezen query's.<br/>Voeg meer vCores toe om meer geheugen te verkrijgen. |
| Maximale opslag grootte van exemplaar (gereserveerd) | -2 TB voor 4 vCores (alleen GEN5)<br/>-8 TB voor andere grootten | Gen4:1 TB <br/> GEN5 <br/>-1 TB voor 4, 8, 16 vCores<br/>-2 TB voor 24 vCores<br/>-4 TB voor 32, 40, 64, 80 vCores |
| Maximale databasegrootte | Tot momenteel beschik bare instantie grootte (Maxi maal 2 TB-8 TB afhankelijk van het aantal vCores). | Maxi maal beschik bare instantie grootte (Maxi maal 1 TB-4 TB, afhankelijk van het aantal vCores). |
| Maximale grootte van tempDB | Beperkt tot 24 GB/vCore (96-1.920 GB) en momenteel beschik bare instantie opslag grootte.<br/>Voeg meer vCores toe om meer TempDB-ruimte te krijgen.<br/> De grootte van het logboek bestand is beperkt tot 120 GB.| Maxi maal beschik bare opslag grootte van exemplaar. |
| Maximum aantal data bases per exemplaar | 100, tenzij de maximale opslag grootte van het exemplaar is bereikt. | 100, tenzij de maximale opslag grootte van het exemplaar is bereikt. |
| Maximum aantal database bestanden per exemplaar | Maxi maal 280, tenzij de limiet voor de opslag ruimte van het exemplaar of het bereik voor de [toewijzing van Azure Premium-schijf opslag](sql-database-managed-instance-transact-sql-information.md#exceeding-storage-space-with-small-database-files) is bereikt. | 32.767 bestanden per data base, tenzij de maximale opslag grootte van het exemplaar is bereikt. |
| Maximale grootte van het gegevens bestand | Beperkt tot de momenteel beschik bare opslag grootte van het exemplaar (Maxi maal 2 TB-8 TB) en de [opslag ruimte voor Azure Premium-schijven](sql-database-managed-instance-transact-sql-information.md#exceeding-storage-space-with-small-database-files). | Beperkt tot de momenteel beschik bare opslag grootte van het exemplaar (Maxi maal 1 TB-4 TB). |
| Maximale grootte van logboek bestand | Beperkt tot 2 TB en momenteel beschik bare exemplaar opslag grootte. | Beperkt tot 2 TB en momenteel beschik bare exemplaar opslag grootte. |
| Gegevens/logboek IOPS (benadering) | Maxi maal 30-40 K IOPS per exemplaar *, 500-7500 per bestand<br/>\*[Bestands grootte verg Roten om meer IOPS te verkrijgen](#file-io-characteristics-in-general-purpose-tier)| 10 k-200 K (2500 IOPS/vCore)<br/>Voeg meer vCores toe om betere IO-prestaties te krijgen. |
| Doorvoer limiet voor schrijf bewerkingen in logboek (per instantie) | 3 MB/s per vCore<br/>Maxi maal 22 MB/s | 4 MB/s per vCore<br/>Max 48 MB/s |
| Gegevens doorvoer (bij benadering) | 100-250 MB/s per bestand<br/>\*[de bestands grootte verg Roten om betere IO-prestaties te krijgen](#file-io-characteristics-in-general-purpose-tier) | Niet beperkt. |
| I/o-latentie van opslag (ongeveer) | 5-10 MS | 1-2 MS |
| In-memory OLTP | Niet ondersteund | Beschikbaar, [grootte is afhankelijk van het aantal vCore](#in-memory-oltp-available-space) |
| Maximum aantal sessies | 30.000 | 30.000 |
| [Alleen-lezen replica's](sql-database-read-scale-out.md) | 0 | 1 (inclusief prijs) |

> [!NOTE]
> - De **momenteel beschik bare opslag grootte voor instanties** is het verschil tussen de gereserveerde exemplaar grootte en de gebruikte opslag ruimte.
> - De grootte van de gegevens en het logboek bestand in de gebruikers-en systeem databases zijn opgenomen in de opslag grootte van het exemplaar, vergeleken met de maximale opslag grootte. Gebruik <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">sys. master_files</a> systeem weergave om de totale hoeveelheid gebruikte ruimte te bepalen op basis van data bases. Fouten logboeken worden niet persistent gemaakt en zijn niet opgenomen in de grootte. Back-ups worden niet opgenomen in de opslag grootte.
> - De door Voer en IOPS op Algemeen laag zijn ook afhankelijk van de [Bestands grootte](#file-io-characteristics-in-general-purpose-tier) die niet expliciet wordt beperkt door een beheerd exemplaar.
> - U kunt een andere Lees bare replica maken in een andere Azure-regio met behulp van groepen voor automatische failover.
> - Het maximum aantal IOPS is afhankelijk van de bestands indeling en de distributie van de werk belasting. Als u bijvoorbeeld 7 x 1 TB bestanden maakt met Maxi maal 5K IOPS per en 7 kleine bestanden (kleiner dan 128 GB) met 500 IOPS per exemplaar, kunt u 38500 IOPS per instantie (7x5000 + 7x500) verkrijgen als uw werk belasting alle bestanden kan gebruiken. Houd er rekening mee dat een bepaalde hoeveelheid IOPS ook wordt gebruikt voor automatische back-ups.

> [!NOTE]
> Meer informatie over de [resource limieten vindt u in dit artikel in beheerde exemplaar groepen](sql-database-instance-pools.md#instance-pools-resource-limitations).

### <a name="file-io-characteristics-in-general-purpose-tier"></a>Bestands-IO-kenmerken in laag Algemeen

In Algemeen servicelaag krijgt elk database bestand toegewezen IOPS en door Voer die afhankelijk zijn van de bestands grootte. Grotere bestanden krijgen meer IOPS en door voer. I/o-kenmerken van de database bestanden worden weer gegeven in de volgende tabel:

| Bestandsgrootte | > = 0 en < = 128 GiB | > 128 en < = 256 GiB | > 256 en < = 512 GiB | > 0,5 en < = 1 TiB    | > 1 en < = 2 TiB    | > 2 en < = 4 TiB | > 4 en < = 8 TiB |
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| IOPS per bestand       | 500   | 1100 | 2300              | 5000              | 7500              | 7500              | 12.500   |
| Door Voer per bestand | 100-MiB/s | 125 MiB/s | 150 MiB/s | 200-MiB/s | 250 MiB/s | 250 MiB/s | 480-MiB/s | 

Als u een hoge IO-latentie krijgt bij een bepaald database bestand of als u ziet dat IOPS/door Voer de limiet bereikt, kunt u de prestaties verbeteren door [de bestands grootte te verg Roten](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Increase-data-file-size-to-improve-HammerDB-workload-performance/ba-p/823337).

Er zijn ook limieten op exemplaar niveau, zoals de maximale schrijf doorvoer van het logboek 22 MB/s, zodat u mogelijk geen bestand kunt bereiken in het logboek bestand omdat u de doorvoer limiet voor instanties bereikt.

## <a name="supported-regions"></a>Ondersteunde regio’s

Beheerde exemplaren kunnen alleen worden gemaakt in [ondersteunde regio's](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Als u een beheerd exemplaar wilt maken in een regio die momenteel niet wordt ondersteund, kunt u [via de Azure Portal een ondersteunings aanvraag verzenden](quota-increase-request.md).

## <a name="supported-subscription-types"></a>Ondersteunde abonnementstypen

Managed instance biedt momenteel alleen ondersteuning voor de implementatie van de volgende typen abonnementen:

- [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Betalen naar gebruik](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Cloud serviceprovider (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Pay-As-You-Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Abonnementen met een maandelijks Azure-tegoed voor Visual Studio-abonnees](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>Regionale resource beperkingen

Ondersteunde abonnements typen kunnen een beperkt aantal resources per regio bevatten. Het beheerde exemplaar heeft twee standaard limieten per Azure-regio (die op aanvraag kunnen worden verhoogd door een speciale [ondersteunings aanvraag te maken in het Azure Portal](quota-increase-request.md) afhankelijk van een type abonnements type:

- **Subnet limiet**: het maximum aantal subnetten waarop beheerde exemplaren in één regio worden geïmplementeerd.
- **limiet vCore**: het maximum aantal vCore-eenheden dat kan worden geïmplementeerd in alle instanties in één regio. Eén GP-vCore maakt gebruik van één vCore-eenheid en één BC vCore neemt vier vCore eenheden in beslag. Het totale aantal exemplaren is niet beperkt zolang het zich binnen de limiet van de vCore-eenheid bevindt.

> [!Note]
> Deze limieten zijn standaard instellingen en niet van technische beperkingen. De limieten kunnen op aanvraag worden verhoogd door een speciale [ondersteunings aanvraag te maken in de Azure Portal](quota-increase-request.md) als u meer beheerde instanties in de huidige regio nodig hebt. Als alternatief kunt u nieuwe beheerde instanties maken in een andere Azure-regio zonder ondersteunings aanvragen te verzenden.

De volgende tabel bevat de **standaard regionale limieten** voor ondersteunde abonnements typen (standaard limieten kunnen worden uitgebreid met ondersteunings aanvraag die hieronder wordt beschreven):

|Abonnementstype| Maximum aantal subnetten beheerde exemplaren | Maximum aantal vCore-eenheden * |
| :---| :--- | :--- |
|Betalen naar gebruik|3|320|
|CSP |8 (15 inch in sommige regio's * *)|960 (1440 in sommige regio's * *)|
|Pay-as-you-go Dev/Test|3|320|
|Enterprise Dev/Test|3|320|
|EA|8 (15 inch in sommige regio's * *)|960 (1440 in sommige regio's * *)|
|Visual Studio Enterprise|2 |64|
|Visual Studio Professional en MSDN Platforms|2|32|

\* bij het plannen van implementaties moet u rekening houden met het feit dat de service tier van Bedrijfskritiek (BC) vier (4) keer zoveel vCore capaciteit nodig heeft dan de servicelaag van Algemeen (GP). Bijvoorbeeld: 1 GP vCore = 1 vCore-eenheid en 1 BC vCore = 4 vCore-eenheden. Als u de verbruiks analyse wilt vereenvoudigen met de standaard limieten, bekijkt u de vCore-eenheden in alle subnetten in de regio waarin beheerde exemplaren worden geïmplementeerd en vergelijkt u de resultaten met de limieten van de exemplaar eenheid voor uw abonnements type. De limiet voor het **maximum aantal vCore-eenheden** is van toepassing op elk abonnement in een regio. Er is geen limiet per afzonderlijke subnetten, behalve dat de som van alle vCores die in meerdere subnetten zijn geïmplementeerd, kleiner of gelijk moet zijn aan het **maximum aantal vCore-eenheden**.

\*\* grotere subnet-en vCore-limieten zijn beschikbaar in de volgende regio's: Australië-oost, VS-Oost, VS-Oost 2, Europa-noord, Zuid-Centraal VS, Zuidoost-Azië, UK-zuid, Europa-west, VS-West 2.

## <a name="request-a-quota-increase-for-sql-managed-instance"></a>Een quotum verhoging aanvragen voor een SQL-beheerd exemplaar

Als u meer beheerde exemplaren in uw huidige regio's nodig hebt, verzendt u een ondersteunings aanvraag om het quotum uit te breiden met behulp van de Azure Portal. Zie [aanvraag quotum verhogingen voor Azure SQL database](quota-increase-request.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Zie [Wat is een beheerd exemplaar?](sql-database-managed-instance.md)voor meer informatie over het beheerde exemplaar.
- Zie [prijzen van beheerde exemplaren SQL database](https://azure.microsoft.com/pricing/details/sql-database/managed/)voor prijs informatie.
- Zie [de Snelstartgids](sql-database-managed-instance-get-started.md)voor meer informatie over het maken van uw eerste beheerde exemplaar.
