---
title: 'Prestaties afstemmen: Spark, HDInsight & Azure Data Lake Storage Gen2 | Microsoft Docs'
description: Richt lijnen voor het afstemmen van de Spark-Azure Data Lake Storage Gen2
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: a70b8112af201a49e7eece8b689e75102ec55880
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327550"
---
# <a name="tune-performance-spark-hdinsight--azure-data-lake-storage-gen2"></a>Prestaties afstemmen: Spark, HDInsight & Azure Data Lake Storage Gen2

Bij het afstemmen van de prestaties van Spark moet u rekening houden met het aantal apps dat op uw cluster wordt uitgevoerd.  Standaard kunt u vier apps gelijktijdig op uw HDI-cluster uitvoeren (Opmerking: de standaard instelling is onderhevig aan wijzigingen).  U kunt ervoor kiezen om minder apps te gebruiken, zodat u de standaard instellingen en het gebruik van het cluster voor deze apps kan onderdrukken.  

## <a name="prerequisites"></a>Vereisten

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
* **Een Azure data Lake Storage Gen2-account**. Zie [Quick Start: een Azure data Lake Storage Gen2 Storage-account maken](data-lake-storage-quickstart-create-account.md)voor instructies over het maken van een.
* **Azure HDInsight-cluster** met toegang tot een Data Lake Storage Gen2-account. Zie [Azure Data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2). Zorg ervoor dat Extern bureaublad voor het cluster is ingeschakeld.
* **Spark-cluster wordt uitgevoerd op Data Lake Storage Gen2**.  Zie [HDInsight Spark-cluster gebruiken voor het analyseren van gegevens in data Lake Storage Gen2](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-use-with-data-lake-store) voor meer informatie
* **Richt lijnen voor het afstemmen van de prestaties van data Lake Storage Gen2**.  Zie [Data Lake Storage Gen2 richt lijnen voor het afstemmen van prestaties](data-lake-storage-performance-tuning-guidance.md) voor algemene concepten. 

## <a name="parameters"></a>Parameters

Wanneer Spark-taken worden uitgevoerd, zijn dit de belangrijkste instellingen die kunnen worden afgestemd op de prestaties van Data Lake Storage Gen2:

* **Num-uitvoerende** modules: het aantal gelijktijdige taken dat kan worden uitgevoerd.

* Uitvoerder **-geheugen** : de hoeveelheid geheugen die aan elke uitvoerder is toegewezen.

* Uitvoerder- **kernen** : het aantal kernen dat aan elke uitvoerder is toegewezen.                     

**Num-uitvoerende modules** Num-uitvoerende modules instellen het maximum aantal taken dat parallel kan worden uitgevoerd.  Het werkelijke aantal taken dat parallel kan worden uitgevoerd, wordt begrensd door het geheugen en de CPU-bronnen die beschikbaar zijn in uw cluster.

Uitvoerder **-geheugen** Dit is de hoeveelheid geheugen die aan elke uitvoerder wordt toegewezen.  Het geheugen dat nodig is voor elke uitvoerder is afhankelijk van de taak.  Voor complexe bewerkingen moet het geheugen hoger zijn.  Voor eenvoudige bewerkingen, zoals lezen en schrijven, zijn de geheugen vereisten lager.  De hoeveelheid geheugen voor elke uitvoerder kan worden weer gegeven in Ambari.  Navigeer in Ambari naar Spark en Bekijk het tabblad Configuratie.  

Uitvoerder **-kernen** Hiermee stelt u het aantal kern geheugens in dat per uitvoerder wordt gebruikt. Hiermee wordt het aantal parallelle threads bepaald dat per bewerkings programma kan worden uitgevoerd.  Als de uitvoerder bijvoorbeeld-kernen = 2 is, kan elke uitvoerder twee parallelle taken uitvoeren in de uitvoerder.  De vereiste uitvoerder-cores is afhankelijk van de taak.  Voor I/O-intensieve taken is geen grote hoeveelheid geheugen per taak vereist, dus elke uitvoerder kan meer parallelle taken verwerken.

Standaard worden twee virtuele garen kernen gedefinieerd voor elke fysieke kern bij het uitvoeren van Spark op HDInsight.  Dit aantal biedt een goede balans van gelijktijdigheid en hoeveelheid context wisseling van meerdere threads.  

## <a name="guidance"></a>Richtlijnen

Bij het uitvoeren van Spark analytische werk belastingen voor het werken met gegevens in Data Lake Storage Gen2 raden wij u aan de meest recente HDInsight-versie te gebruiken om de beste prestaties te verkrijgen met Data Lake Storage Gen2. Wanneer uw taak meer I/O-intensief is, kunnen bepaalde para meters worden geconfigureerd om de prestaties te verbeteren.  Data Lake Storage Gen2 is een uiterst schaalbaar opslag platform dat hoge door Voer kan verwerken.  Als de taak voornamelijk uit Lees-of schrijf bewerkingen bestaat, kunnen de prestaties worden verbeterd door de gelijktijdige gelijktijdigheid te verhogen voor I/O van en naar Data Lake Storage Gen2.

Er zijn enkele algemene manieren om gelijktijdigheid te verhogen voor I/O-intensieve taken.

**Stap 1: Bepaal hoeveel apps worden uitgevoerd in uw cluster** . u moet weten hoeveel apps er op het cluster worden uitgevoerd, met inbegrip van het huidige.  Bij de standaard waarden voor elke Spark-instelling wordt ervan uitgegaan dat er vier apps gelijktijdig worden uitgevoerd.  Daarom is er slechts 25% van het cluster beschikbaar voor elke app.  Als u betere prestaties wilt, kunt u de standaard waarden onderdrukken door het aantal uitvoerender te wijzigen.  

**Stap 2: uitvoerder instellen-geheugen** : het eerste dat moet worden ingesteld, is de uitvoerder van het geheugen.  Het geheugen is afhankelijk van de taak die u wilt uitvoeren.  U kunt de gelijktijdigheid verhogen door minder geheugen per uitvoerder toe te wijzen.  Als er onvoldoende geheugen-uitzonde ringen worden weer geven wanneer u uw taak uitvoert, moet u de waarde voor deze para meter verhogen.  Het is ook mogelijk om meer geheugen te verkrijgen door gebruik te maken van een cluster met een grotere hoeveelheid geheugen of de grootte van het cluster te verg Roten.  Meer geheugens worden gebruikt om meer uitvoerders te gebruiken, wat betekent dat dit meer gelijktijdig is.

**Stap 3: de uitvoerder-kernen instellen** : voor I/O-intensieve workloads die geen complexe bewerkingen hebben, is het goed om te beginnen met een groot aantal uitvoeres-cores om het aantal parallelle taken per uitvoerder te verhogen.  Het instellen van de uitvoerder-kernen op 4 is een goed begin.   

    executor-cores = 4
Het verhogen van het aantal uitvoeringen-kern geheugens geeft u meer parallellisme zodat u kunt experimenteren met verschillende Voer-cores.  Voor taken met complexere bewerkingen moet u het aantal kernen per uitvoerder verminderen.  Als de uitvoerder is ingesteld op meer dan 4, wordt de garbagecollection mogelijk inefficiënt en wordt de prestaties verslechteren.

**Stap 4: de hoeveelheid garen geheugen in het cluster bepalen** : deze informatie is beschikbaar in Ambari.  Navigeer naar GARENs en Bekijk het tabblad Configuratie.  Het garen geheugen wordt in dit venster weer gegeven.  
Opmerking terwijl u zich in het venster bevindt, ziet u ook de standaard grootte van de garen container.  De grootte van de garen container is hetzelfde als het geheugen per uitvoer parameter.

    Total YARN memory = nodes * YARN memory per node
**Stap 5: berekenen van num-uitvoerende modules**

**Geheugen beperking berekenen** : de para meter voor de num-uitvoerende modules wordt beperkt door het geheugen of de CPU.  De geheugen beperking wordt bepaald door de hoeveelheid beschikbaar garen geheugen voor uw toepassing.  U moet het totaal aantal GARENs in de hele ruimte halen en deze delen door invoerder-geheugen.  De beperking moet worden geschaald voor het aantal apps, zodat we delen door het aantal apps.

    Memory constraint = (total YARN memory / executor memory) / # of apps   
**CPU-beperking berekenen** : de CPU-beperking wordt berekend als de totale virtuele kernen gedeeld door het aantal kernen per uitvoerder.  Er zijn twee virtuele kernen voor elke fysieke kern.  Net als bij de beperking van het geheugen moeten we delen door het aantal apps.

    virtual cores = (nodes in cluster * # of physical cores in node * 2)
    CPU constraint = (total virtual cores / # of cores per executor) / # of apps
**Stel num-uitvoerende modules** in: de para meter num-uitvoerende modules wordt bepaald door de minimale geheugen beperking en de CPU-beperking te nemen. 

    num-executors = Min (total virtual Cores / # of cores per executor, available YARN memory / executor-memory)   
Het instellen van een groter aantal num-uitvoerende modules is niet noodzakelijkerwijs de prestaties te verhogen.  Houd er rekening mee dat het toevoegen van meer uitvoerder extra overhead voor elke extra uitvoerder toevoegt, waardoor de prestaties mogelijk kunnen afnemen.  Num-uitvoerende modules worden begrensd door de cluster resources.    

## <a name="example-calculation"></a>Voorbeeld berekening

Stel dat u momenteel een cluster hebt dat bestaat uit 8 D4v2-knoop punten met 2 apps, met inbegrip van het abonnement dat u wilt uitvoeren.  

**Stap 1: bepalen hoeveel apps worden uitgevoerd in uw cluster** : u weet dat u 2 apps op uw cluster hebt, inclusief het abonnement dat u wilt uitvoeren.  

**Stap 2: uitvoerder instellen-geheugen** : voor dit voor beeld bepalen we dat 6GB van de uitvoerder-geheugen voldoende is voor de I/O-intensieve taak.  

    executor-memory = 6GB
**Stap 3: de uitvoerder instellen-kernen** – omdat dit een I/O-intensieve taak is, kunnen we het aantal kernen voor elke uitvoerder instellen op 4.  Het instellen van kernen per uitvoerder naar groter dan 4 kan leiden tot garbagecollection-problemen.  

    executor-cores = 4
**Stap 4: het aantal garens in het cluster bepalen** . Ga naar Ambari om erachter te komen dat elke D4V2 een 25 GB van het garen-geheugen heeft.  Omdat er 8 knoop punten zijn, wordt het beschik bare garen geheugen vermenigvuldigd met 8.

    Total YARN memory = nodes * YARN memory* per node
    Total YARN memory = 8 nodes * 25GB = 200GB
**Stap 5: berekenen van num-uitvoerende modules** : de para meter num-uitvoerende modules wordt bepaald door het minimum van de geheugen beperking en de CPU-beperking te nemen, gedeeld door het aantal apps dat op Spark wordt uitgevoerd.    

**Geheugen beperking berekenen** : de geheugen beperking wordt berekend als het totale aantal garens gedeeld door het geheugen per uitvoerder.

    Memory constraint = (total YARN memory / executor memory) / # of apps   
    Memory constraint = (200GB / 6GB) / 2   
    Memory constraint = 16 (rounded)
**CPU-beperking berekenen** : de CPU-beperking wordt berekend als de totale garen kernen gedeeld door het aantal kernen per uitvoerder.
    
    YARN cores = nodes in cluster * # of cores per node * 2   
    YARN cores = 8 nodes * 8 cores per D14 * 2 = 128
    CPU constraint = (total YARN cores / # of cores per executor) / # of apps
    CPU constraint = (128 / 4) / 2
    CPU constraint = 16
**Num-uitvoerende modules instellen**

    num-executors = Min (memory constraint, CPU constraint)
    num-executors = Min (16, 16)
    num-executors = 16    

