---
title: Groottes van Azure VM-algemeen gebruik | Microsoft Docs
description: Hier worden de verschillende algemene doel grootten vermeld die beschikbaar zijn voor virtuele machines in Azure. Hiermee wordt informatie weer gegeven over het aantal Vcpu's, gegevens schijven en Nic's, evenals opslag doorvoer en netwerk bandbreedte voor grootten in deze serie.
services: virtual-machines
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/20/2020
ms.author: jonbeck
ms.openlocfilehash: 4b36f456f70eb79cff1f615c7c136b8fe4b1b3d4
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2020
ms.locfileid: "78226707"
---
# <a name="general-purpose-virtual-machine-sizes"></a>Grootte van virtuele machines voor algemene doel einden

VM-grootten voor algemeen gebruik bieden evenwichtige verhouding tussen CPU en geheugen. Ideaal voor testen en ontwikkelen, kleine tot middel grote data bases en webservers met weinig of gemiddeld verkeer. Dit artikel bevat informatie over de aanbiedingen voor algemeen gebruik.

- De virtuele machines uit de [Av2-serie](av2-series.md) kunnen worden geïmplementeerd op diverse typen hardware en processors. Vm's uit de A-serie hebben CPU-prestaties en geheugen configuraties die het meest geschikt zijn voor workloads op instap niveau, zoals ontwikkelen en testen. De grootte is afhankelijk van de hardware, zodat er consistente processorprestaties voor het actieve exemplaar kunnen worden geboden, ongeacht de hardware waarop deze is geïmplementeerd. Om de fysieke hardware te bepalen waarop deze grootte is geïmplementeerd, vraagt u vanuit de virtuele machine gegevens over de virtuele hardware op. Voor beelden van use cases zijn: ontwikkelings-en test servers, webservers met weinig verkeer, kleine tot middel grote data bases, controle van concepten en code opslagplaatsen.

  > [!NOTE]
  > De A8-A11-Vm's worden gepland voor buiten gebruiks telling op 3/2021. Zie voor meer informatie [HPC-migratie handleiding](https://azure.microsoft.com/resources/hpc-migration-guide/).

- [B-serie-Burstable](sizes-b-series-burstable.md) Vm's zijn ideaal voor werk belastingen die de volledige prestaties van de CPU niet continu nodig hebben, zoals webservers, kleine data bases en ontwikkel-en test omgevingen. Deze werk belastingen hebben doorgaans een burstieve prestatie vereisten. De B-serie biedt deze klanten de mogelijkheid om een VM-grootte aan te schaffen met een prijs bewuste basislijn prestaties waarmee de VM-instantie tegoeden kan bouwen wanneer de virtuele machine minder is dan de basis prestaties. Wanneer de virtuele machine tegoed heeft gecumuleerd, kan de virtuele machine op de basis lijn van de virtuele machine worden gebursteerd met Maxi maal 100% van de CPU wanneer voor uw toepassing hogere CPU-prestaties zijn vereist.

- [Dav4 en Dasv4-Series](dav4-dasv4-series.md) zijn nieuwe grootten die gebruikmaken van de 2.35 GHz EPYC<sup>TM</sup> 7452-processor van AMD in een configuratie met meerdere threads, met Maxi maal 256 MB L3-cache van 8 GB aan de L3-cache tot elke 8 kernen die de klant opties verhogen voor het uitvoeren van hun werk belastingen voor algemeen gebruik. De Dav4-serie en de Dasv4-serie hebben dezelfde geheugen-en schijf configuraties als de D & Dsv3-serie.

- De [DCv2-serie](dcv2-series.md) kan u helpen de vertrouwelijkheid en integriteit van uw gegevens en code te beschermen tijdens de verwerking in de open bare Cloud. Deze machines worden ondersteund door de nieuwste generatie Intel XEON E-2288G-processor met SGX-technologie. Met de Intel Turbo Boost-technologie kunnen deze machines tot wel 5.0 GHz gaan. Met DCv2-serie-instanties kunnen klanten veilige, op enclave gebaseerde toepassingen bouwen om hun code en gegevens te beschermen terwijl ze worden gebruikt.

- [Dv2 en Dsv2-serie](dv2-dsv2-series.md) Vm's, een follow-on van de oorspronkelijke D-serie, bevat een krachtigere CPU en optimale configuratie van CPU naar geheugen, zodat deze geschikt zijn voor de meeste productie werkbelastingen. De dv2-serie is ongeveer 35% sneller dan de D-serie. Dv2-serie wordt uitgevoerd op de Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) of de Intel® Xeon-® E5-2673 v3 2,4 GHz (Haswell)-processors met de Intel Turbo Boost-technologie 2,0. De Dv2-serie heeft dezelfde geheugen- en schijfconfiguraties als de D-serie.

- [Dv3 en Dsv3-serie](dv3-dsv3-series.md) Vm's worden uitgevoerd op Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell), of de Intel® Xeon-® E5-2673 v3 2,4 GHz (Haswell)-processors in een configuratie met Hyper-Threading, waardoor u een betere waarde hebt voor de meeste werk belastingen in het algemeen. Het geheugen is uitgevouwen (van ~ 3,5 GiB/vCPU naar 4 GiB/vCPU), terwijl de schijf-en netwerk limieten per kern zijn aangepast om te worden uitgelijnd met de verplaatsing van hyperthreading. De Dv3-serie heeft niet langer de VM-grootten van het hoge geheugen van de D/dv2-serie, die zijn verplaatst naar de Ev3 voor geoptimaliseerd geheugen [en de Esv3-serie](ev3-esv3-series.md).

Voor beelden van use-cases van de D-serie zijn zakelijke toepassingen, relationele data bases, caching in het geheugen en analyse.

## <a name="other-sizes"></a>Andere grootten

- [Geoptimaliseerde rekenkracht](sizes-compute.md)
- [Geoptimaliseerd geheugen](sizes-memory.md)
- [Geoptimaliseerde opslag](sizes-storage.md)
- [Geoptimaliseerde GPU](sizes-gpu.md)
- [Krachtig rekenvermogen](sizes-hpc.md)
- [Vorige generaties](sizes-previous-gen.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe [Azure Compute units (ACU)](acu.md) u kan helpen bij het vergelijken van de reken prestaties in azure-sku's.