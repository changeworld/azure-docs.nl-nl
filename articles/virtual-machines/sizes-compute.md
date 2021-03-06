---
title: Azure VM-grootten-berekenings optimalisatie | Microsoft Docs
description: Geeft een lijst van de verschillende geoptimaliseerde berekenings groottes die beschikbaar zijn voor virtuele machines in Azure. Hiermee wordt informatie weer gegeven over het aantal Vcpu's, gegevens schijven en Nic's, evenals opslag doorvoer en netwerk bandbreedte voor grootten in deze serie.
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
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: d709d621341ef14ec158ed5af1c2df4297d66b8b
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77493629"
---
# <a name="compute-optimized-virtual-machine-sizes"></a>Grootte van geoptimaliseerde virtuele machines berekenen

De grootte van de virtuele machines met reken capaciteit is hoog voor CPU-geheugen. Deze grootten zijn geschikt voor webservers, netwerk apparaten, batch processen en toepassings servers met een gemiddeld verkeer. Dit artikel bevat informatie over het aantal Vcpu's, gegevens schijven en Nic's. Het bevat ook informatie over opslag doorvoer en netwerk bandbreedte voor elke grootte in deze groep.

De [Fsv2-serie](fsv2-series.md) is gebaseerd op de Intel® Xeon® Platinum 8168-processor. De IT-service heeft een zeer hoge Turbo klok snelheid van 3,4 GHz en een maximale Turbo frequentie van 3,7 GHz met één kern. Intel® AVX-512-instructies zijn nieuw op schaal bare Intel-processors. Deze instructies bieden een 2X hoge prestatie verbetering van de werk belasting voor vector verwerking op bewerkingen met een drijvende komma van zowel één als dubbele precisie. Met andere woorden, ze zijn heel snel voor elke reken werk belasting.

Voor een lagere prijs per uur is de Fsv2-serie de beste prijs-prestatie verhouding in de Azure-Port Folio op basis van de ACU (Azure Compute Unit) per vCPU.

## <a name="other-sizes"></a>Andere grootten

- [Algemeen doel](sizes-general.md)
- [Geoptimaliseerd geheugen](sizes-memory.md)
- [Geoptimaliseerde opslag](sizes-storage.md)
- [Geoptimaliseerde GPU](sizes-gpu.md)
- [Krachtig rekenvermogen](sizes-hpc.md)
- [Vorige generaties](sizes-previous-gen.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe [Azure Compute units (ACU)](acu.md) u kan helpen bij het vergelijken van de reken prestaties in azure-sku's.