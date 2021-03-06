---
title: Overzicht van vCore-modellen
description: Met het vCore-aankoop model kunt u de reken-en opslag resources onafhankelijk van elkaar schalen, de on-premises prestaties afstemmen en de prijs optimaliseren.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/27/2019
ms.openlocfilehash: e53fb46b7c13e1feb0cc24663fb0782b4de06f2b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79255819"
---
# <a name="vcore-model-overview"></a>Overzicht van vCore-modellen

Het virtuele kern model (vCore) biedt diverse voor delen:

- Hogere reken-, geheugen-, i/o-en opslag limieten.
- Controle over de hardware-generatie om beter te voldoen aan de reken-en geheugen vereisten van de werk belasting.
- Prijs kortingen voor [Azure Hybrid Benefit (AHB)](sql-database-azure-hybrid-benefit.md) en [gereserveerde instanties (RI)](sql-database-reserved-capacity.md).
- Grotere transparantie in de hardware-details die de reken kracht stroomt; vereenvoudigt het plannen van migraties van on-premises implementaties.

## <a name="service-tiers"></a>Servicelagen

Opties voor de servicelaag in het vCore-model bevatten Algemeen, Bedrijfskritiek en grootschalige. De servicelaag definieert in het algemeen de opslag architectuur, ruimte-en IO-limieten en opties voor bedrijfs continuïteit die betrekking hebben op Beschik baarheid en herstel na nood gevallen.

||**Algemeen doel**|**Bedrijfs kritiek**|**Grootschalige**|
|---|---|---|---|
|Ideaal voor|De meeste zakelijke workloads. Biedt budget gerichte, evenwichtige en schaal bare reken-en opslag opties. |Biedt zakelijke toepassingen de hoogste flexibiliteit voor storingen met behulp van verschillende geïsoleerde replica's en biedt de hoogste I/O-prestaties per database replica.|De meeste zakelijke workloads met zeer schaal bare opslag-en lees vereisten.  Biedt meer flexibiliteit voor storingen door de configuratie van meer dan één geïsoleerde database replica toe te staan. |
|Opslag|Maakt gebruik van externe opslag.<br/>**Afzonderlijke data bases en geprovisionte elastische Pools**:<br/>5 GB – 4 TB<br/>**Serverloze Compute**:<br/>5 GB-3 TB<br/>**Beheerd exemplaar**: 32 GB-8 TB |Maakt gebruik van lokale SSD-opslag.<br/>**Afzonderlijke data bases en geprovisionte elastische Pools**:<br/>5 GB – 4 TB<br/>**Beheerd exemplaar**:<br/>32 GB - 4 TB |Flexibele Automatische toename van opslag als dat nodig is. Ondersteunt Maxi maal 100 TB aan opslag ruimte. Maakt gebruik van lokale SSD-opslag voor lokale buffer-pool cache en lokale gegevens opslag. Maakt gebruik van Azure externe opslag als definitieve gegevens opslag op lange termijn. |
|IOPS en door Voer (ongeveer)|**Afzonderlijke data bases en elastische Pools**: Zie resource limieten voor [afzonderlijke data bases](../sql-database/sql-database-vcore-resource-limits-single-databases.md) en [elastische Pools](../sql-database/sql-database-vcore-resource-limits-elastic-pools.md).<br/>**Beheerd exemplaar**: zie [overzicht Azure SQL database de resource limieten voor beheerde exemplaren](../sql-database/sql-database-managed-instance-resource-limits.md#service-tier-characteristics).|Zie resource limieten voor [afzonderlijke data bases](../sql-database/sql-database-vcore-resource-limits-single-databases.md) en [elastische Pools](../sql-database/sql-database-vcore-resource-limits-elastic-pools.md).|Grootschalige is een architectuur met meerdere lagen met caching op meerdere niveaus. Effectief IOPS en door Voer is afhankelijk van de werk belasting.|
|Beschikbaarheid|1 replica, geen replica's met lees schaal|3 replica's, 1 [replica met lees grootte](sql-database-read-scale-out.md),<br/>zone-redundante hoge Beschik baarheid (HA)|1 replica met lees-en schrijf bewerkingen, plus 0-4 [replica's met lees grootte](sql-database-read-scale-out.md)|
|Back-ups|[Geografisch redundante opslag met lees toegang (RA-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 dagen (standaard 7 dagen)|[Ra-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 dagen (standaard 7 dagen)|Back-ups op basis van moment opnamen in azure externe opslag. Herstelt het gebruik van deze moment opnamen voor snel herstel. Back-ups zijn onmiddellijk en zijn niet van invloed op de I/O-prestaties van compute. Herstel bewerkingen zijn snel en zijn geen omvang van de gegevens bewerking (minuten in plaats van uren of dagen).|
|In het geheugen|Niet ondersteund|Ondersteund|Niet ondersteund|
|||


### <a name="choosing-a-service-tier"></a>Het kiezen van een servicelaag

Raadpleeg de volgende artikelen voor meer informatie over het selecteren van een servicelaag voor uw specifieke workload:

- [Wanneer u de servicelaag voor algemeen gebruik kiest](sql-database-service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [Wanneer u de laag Bedrijfskritiek-service kiest](sql-database-service-tier-business-critical.md#when-to-choose-this-service-tier)
- [Wanneer u de grootschalige-servicelaag kiest](sql-database-service-tier-hyperscale.md#who-should-consider-the-hyperscale-service-tier)


## <a name="compute-tiers"></a>Reken lagen

Opties voor Compute-lagen in het vCore-model bevatten de ingerichte en serverloze reken lagen.


### <a name="provisioned-compute"></a>Ingerichte compute

De ingerichte Compute-laag biedt een specifieke hoeveelheid reken bronnen die continu worden ingericht, onafhankelijk van de werkbelasting activiteit, en facturen voor de hoeveelheid Compute die is ingericht tegen een vaste prijs per uur.


### <a name="serverless-compute"></a>Serverloze compute

Met de [serverloze Compute-laag](sql-database-serverless.md) worden reken resources automatisch geschaald op basis van de werkbelasting activiteit en worden er facturen in rekening gebracht voor de hoeveelheid reken kracht per seconde.



## <a name="hardware-generations"></a>Hardware gegenereerd

Opties voor het genereren van hardware in het vCore-model zijn gen 4/5, M-Series (preview) en Fsv2-Series (preview). Het genereren van de hardware definieert doorgaans de reken-en geheugen limieten en andere kenmerken die van invloed zijn op de prestaties van de werk belasting.

### <a name="gen4gen5"></a>Gen4/Gen5

- Gen4/Gen5-hardware biedt evenwichtige reken-en geheugen bronnen en is geschikt voor de meeste data base-workloads die niet meer geheugen, hogere vCore of snellere enkelvoudige vCore vereisten hebben zoals geleverd door de Fsv2-serie of M-serie.

Zie [Gen4/Gen5 Beschik baarheid](#gen4gen5-1)voor regio's waar Gen4/Gen5 beschikbaar is.

### <a name="fsv2-seriespreview"></a>Fsv2-serie (preview-versie)

- Fsv2-serie is een hardwarematige Compute-optie die lage CPU-latentie en hoge klok snelheid levert voor de meeste CPU-werk belastingen.
- Afhankelijk van de werk belasting, kan de Fsv2-serie meer CPU-prestaties leveren per vCore dan GEN5, en de grootte van 72 vCore kan meer CPU-prestaties bieden voor minder kosten dan 80 vCores op GEN5. 
- Fsv2 biedt minder geheugen en tempdb per vCore dan andere hardware, zodat werk belastingen die gevoelig zijn voor deze limieten wellicht in plaats daarvan Gen5 of M-serie willen overwegen.  

Zie de [Beschik baarheid van Fsv2-Series](#fsv2-series)voor regio's waar Fsv2-serie beschikbaar is.


### <a name="m-seriespreview"></a>M-serie (preview-versie)

- M-serie is een optie voor geoptimaliseerd voor geheugen voor werk belastingen die meer geheugen en hogere reken limieten hebben dan wordt verzorgd door GEN5.
- De M-serie biedt 29 GB per vCore en 128 vCores, waardoor de geheugen limiet wordt verhoogd ten opzichte van GEN5 van 8x tot bijna 4 TB.

Als u hardware van de M-serie wilt inschakelen voor een abonnement en regio, moet een ondersteunings aanvraag worden geopend. Het abonnement moet een betaald aanbod type zijn, inclusief betalen naar gebruik of Enterprise Agreement (EA).  Als de ondersteunings aanvraag is goedgekeurd, volgt de selectie-en inrichtings ervaring van de M-serie hetzelfde patroon als voor andere hardware-generaties. Zie de [Beschik baarheid van de m-serie](#m-series)voor regio's waar de m-serie beschikbaar is.


### <a name="compute-and-memory-specifications"></a>Specificaties van Compute en geheugen


|Hardware genereren  |Compute  |Geheugen  |
|:---------|:---------|:---------|
|Gen4     |-Intel E5-2673 v3 (Haswell) 2,4 GHz-processors<br>-Tot 24 vCores (1 vCore = 1 fysieke kern) inrichten  |-7 GB per vCore<br>-Maxi maal 168 GB inrichten|
|GEN5     |**Ingerichte compute**<br>-Intel E5-2673 v4 (Broadwell) 2,3-GHz en Intel SP-8160 (Skylake) * processors<br>-Maxi maal 80 vCores (1 vCore = 1 Hyper Thread) inrichten<br><br>**Serverloze compute**<br>-Intel E5-2673 v4 (Broadwell) 2,3-GHz en Intel SP-8160 (Skylake) * processors<br>-Schaal automatisch naar 16 vCores (1 vCore = 1 Hyper Thread)|**Ingerichte compute**<br>-5,1 GB per vCore<br>-Maxi maal 408 GB inrichten<br><br>**Serverloze compute**<br>-Automatisch schalen naar 24 GB per vCore<br>-Maxi maal 48 GB automatisch schalen|
|Fsv2-serie     |-Intel Xeon Platinum 8168-processors (SkyLake)<br>-Met een zeer hoge Turbo klok snelheid van 3,4 GHz en een maximale klok snelheid van Maxi maal één kern van 3,7 GHz.<br>-Provision 72 vCores (1 vCore = 1 Hyper Thread)|-1,9 GB per vCore<br>-Inrichting van 136 GB|
|M-serie     |-Intel Xeon E7-8890 v3 2,5 GHz-processors<br>-Provision 128 vCores (1 vCore = 1 Hyper Thread)|-29 GB per vCore<br>-Inrichting van 3,7 TB|

\* in de weer gave [sys. dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) Dynamic Management wordt het genereren van hardware voor GEN5-data bases met Intel SP-8160 (Skylake)-processors weer gegeven als Gen6. Resource limieten voor alle GEN5-data bases zijn hetzelfde, ongeacht het processor type (Broadwell of Skylake).

Zie [resource limieten voor afzonderlijke data bases (vCore)](sql-database-vcore-resource-limits-single-databases.md)of resource limieten [voor elastische Pools (vCore)](sql-database-vcore-resource-limits-elastic-pools.md)voor meer informatie over resource limieten.

### <a name="selecting-a-hardware-generation"></a>Een hardware-generatie selecteren

In de Azure Portal kunt u de hardware-generatie voor een SQL database of groep selecteren op het moment dat deze wordt gemaakt, of u kunt de hardware-generatie van een bestaande SQL database of groep wijzigen.

**Een hardwarematige generatie selecteren bij het maken van een SQL database of groep**

Zie [een SQL database maken](sql-database-single-database-get-started.md)voor gedetailleerde informatie.

Selecteer op het tabblad **basis beginselen** de koppeling **Data Base configureren** in de sectie **berekenings** -en opslag en selecteer vervolgens de koppeling **configuratie wijzigen** :

  ![database configureren](media/sql-database-service-tiers-vcore/configure-sql-database.png)

Selecteer de gewenste hardware-generatie:

  ![hardware selecteren](media/sql-database-service-tiers-vcore/select-hardware.png)


**Het genereren van de hardware van een bestaande SQL database of groep wijzigen**

Voor een Data Base selecteert u op de pagina overzicht de **prijs categorie** koppeling:

  ![hardware wijzigen](media/sql-database-service-tiers-vcore/change-hardware.png)

Voor een groep selecteert u op de pagina overzicht de optie **configureren**.

Volg de stappen voor het wijzigen van de configuratie en selecteer de hardware-generatie zoals beschreven in de vorige stappen.

**Een hardwarematige generatie selecteren bij het maken van een beheerd exemplaar**

Zie [een beheerd exemplaar maken](sql-database-managed-instance-get-started.md)voor gedetailleerde informatie.

Selecteer op het tabblad **basis beginselen** de koppeling **Data Base configureren** in de sectie **berekenings** -en opslag en selecteer vervolgens gewenste hardwarematige generatie:

  ![beheerd exemplaar configureren](media/sql-database-service-tiers-vcore/configure-managed-instance.png)
  
**Het genereren van de hardware van een bestaand beheerd exemplaar wijzigen**

# <a name="portal"></a>[Portal](#tab/azure-portal)

Selecteer op de pagina beheerd exemplaar de **prijs categorie** koppeling die in de sectie instellingen is geplaatst

![hardware van beheerd exemplaar wijzigen](media/sql-database-service-tiers-vcore/change-managed-instance-hardware.png)

Op de pagina **prijs categorie** kunt u de generatie van de hardware wijzigen, zoals beschreven in de vorige stappen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Gebruik het volgende Power shell-script:

```powershell-interactive
Set-AzSqlInstance -Name "managedinstance1" -ResourceGroupName "ResourceGroup01" -ComputeGeneration Gen5
```

Raadpleeg de opdracht [set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance) voor meer informatie.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Gebruik de volgende CLI-opdracht:

```azurecli-interactive
az sql mi update -g mygroup -n myinstance --family Gen5
```

Raadpleeg [AZ SQL mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update) Command voor meer informatie.

---

### <a name="hardware-availability"></a>Hardwarematige Beschik baarheid

#### <a name="gen4gen5-1"></a>Gen4/Gen5

Gen4-hardware wordt [gefaseerd uitgevoerd](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/) en is niet meer beschikbaar voor de nieuwe implementaties. Alle nieuwe data bases moeten worden geïmplementeerd op GEN5-hardware.

GEN5 is wereld wijd beschikbaar in de meeste regio's.

#### <a name="fsv2-series"></a>Fsv2-serie

Fsv2-serie is beschikbaar in de volgende regio's: Australië-centraal, Australië-centraal 2, Australië-oost, Australië-zuidoost, Brazilië-zuid, Canada-centraal, Azië-oost, VS-Oost, Frankrijk-centraal, India centraal, India-West, Korea-centraal, Korea-zuid, Noord Europa, Zuid-Afrika-noord, Zuidoost-Azië, UK-zuid, UK-west, Europa-west, VS-West 2.


#### <a name="m-series"></a>M-serie

De M-serie is beschikbaar in de volgende regio's: VS-Oost, Europa-noord, Europa-west, VS-West 2.
De M-serie kan ook een beperkte Beschik baarheid hebben in extra regio's. U kunt een andere regio aanvragen dan hier wordt vermeld, maar de uitvoering van een andere regio is mogelijk niet mogelijk.

Als u de beschik baarheid van de M-serie in een abonnement wilt inschakelen, moet u toegang [aanvragen door een nieuwe ondersteunings aanvraag](#create-a-support-request-to-enable-m-series)in te dienen.


##### <a name="create-a-support-request-to-enable-m-series"></a>Een ondersteunings aanvraag maken om de M-serie in te scha kelen: 

1. Selecteer **Help en ondersteuning** in de portal.
2. Selecteer **Nieuwe ondersteuningsaanvraag**.

Geef op de pagina **basis beginselen** het volgende op:

1. Selecteer voor **probleem type** **service-en abonnements limieten (quota's)** .
2. Voor **abonnement** = Selecteer het abonnement om de M-serie in te scha kelen.
3. Selecteer **SQL database**bij **quotum type**.
4. Selecteer **volgende** om naar de pagina **Details** te gaan.

Geef op de pagina **Details** het volgende op:

1. Selecteer in de sectie **probleem Details** de koppeling **Details opgeven** . 
2. Voor **SQL database quotum type** selecteert u **M-serie**.
3. Selecteer bij **regio**de regio om de M-serie in te scha kelen.
    Zie de [Beschik baarheid van de m-serie](#m-series)voor regio's waar de m-serie beschikbaar is.

Goedgekeurde ondersteunings aanvragen worden doorgaans binnen 5 werk dagen afgehandeld.


## <a name="next-steps"></a>Volgende stappen

- Als u een SQL database wilt maken, raadpleegt u [een SQL database maken met behulp van de Azure Portal](sql-database-single-database-get-started.md).
- Zie [SQL database resource limieten op basis van vCore voor afzonderlijke data](sql-database-vcore-resource-limits-single-databases.md)bases voor de specifieke berekenings grootte en beschik bare opslag grootte voor afzonderlijke data bases.
- Zie [SQL database op vCore gebaseerde resource limieten voor elastische Pools](sql-database-vcore-resource-limits-elastic-pools.md)voor de specifieke berekenings grootten en opties voor opslag grootte die beschikbaar zijn voor elastische Pools.
- Zie de pagina met prijzen voor [Azure SQL database](https://azure.microsoft.com/pricing/details/sql-database/single/)voor prijs informatie.
