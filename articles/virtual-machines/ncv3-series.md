---
title: NCv3-serie-Azure Virtual Machines
description: Specificaties voor de virtuele machines uit de NCv3-serie.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 9ae3604a9ea82e6e50ba4d639d36572f7b052e4c
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302794"
---
# <a name="ncv3-series"></a>NCv3-serie

Virtuele machines uit de NCv3-serie worden aangedreven door [Nvidia Tesla V100](https://www.nvidia.com/data-center/tesla-v100/) gpu's. Deze Gpu's kunnen 1,5 x de reken prestaties van de NCv2-serie bieden. Klanten kunnen profiteren van deze bijgewerkte Gpu's voor traditionele HPC-workloads, zoals het maken van tank modellen, DNA-sequentiëren, eiwithoudende analyse, Monte Carlo-simulaties en andere. De NC24rs v3-configuratie biedt een netwerk interface met lage latentie en hoge door Voer die is geoptimaliseerd voor nauw gekoppelde werk belastingen voor parallelle berekeningen. Naast de Gpu's worden de virtuele machines uit de NCv3-serie ook aangedreven door de Intel Xeon E5-2690 v4-Cpu's (Broadwell).

Premium Storage: ondersteund

Premium Storage caching: ondersteund

Livemigratie: niet ondersteund

Updates voor het behouden van geheugen: niet ondersteund

> [!IMPORTANT]
> Voor deze VM-serie wordt het vCPU-quotum (core) in uw abonnement in eerste instantie ingesteld op 0 in elke regio. [Vraag een toename van een vCPU-quotum](../azure-supportability/resource-manager-core-quotas-request.md) aan voor deze reeks in een [beschik bare regio](https://azure.microsoft.com/regions/services/).
>
| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | GPU | GPU-geheugen: GiB | Max. aantal gegevensschijven | Maxi maal aantal niet-opgeslagen schijf doorvoer: IOPS/MBps | Max. aantal NIC's |
|---|---|---|---|---|---|---|---|---|
| Standard_NC6s_v3    | 6  | 112 | 736  | 1 | 16 | 12 | 20000/200 | 4 |
| Standard_NC12s_v3   | 12 | 224 | 1474 | 2 | 32 | 24 | 40000/400 | 8 |
| Standard_NC24s_v3   | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |
| Standard_NC24rs_v3* | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |

1 GPU = One V100-kaart.

*RDMA-compatibel

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Ondersteunde besturingssystemen en stuurprogramma’s

Om te profiteren van de GPU-mogelijkheden van Vm's uit de Azure N-serie, moeten de NVIDIA GPU-Stuur Programma's zijn geïnstalleerd.

Met de [uitbrei ding NVIDIA GPU-stuur programma](./extensions/hpccompute-gpu-windows.md) worden de juiste NVIDIA-CUDA of raster Stuur Programma's geïnstalleerd op een virtuele machine uit de N-serie. De uitbrei ding installeren of beheren met de Azure Portal of hulpprogram ma's, zoals Azure PowerShell of Azure Resource Manager sjablonen. Zie de [documentatie over NVIDIA GPU-Stuur Programma's](./extensions/hpccompute-gpu-windows.md) voor ondersteunde besturings systemen en implementaties tappen. Zie [extensies en functies van virtuele Azure-machines](./extensions/overview.md)voor algemene informatie over VM-extensies.

Als u ervoor kiest om de NVIDIA GPU-Stuur Programma's hand matig te installeren, raadpleegt u [het stuur programma voor](./windows/n-series-driver-setup.md) de installatie van de Windows-of [n-Series GPU-](./linux/n-series-driver-setup.md) stuur programma voor Linux voor ondersteunde besturings systemen, stuur Programma's, installatie en verificaties tappen.

## <a name="other-sizes"></a>Andere grootten

- [Algemeen doel](sizes-general.md)
- [Geoptimaliseerd geheugen](sizes-memory.md)
- [Geoptimaliseerde opslag](sizes-storage.md)
- [Geoptimaliseerde GPU](sizes-gpu.md)
- [Krachtig rekenvermogen](sizes-hpc.md)
- [Vorige generaties](sizes-previous-gen.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe [Azure Compute units (ACU)](acu.md) u kan helpen bij het vergelijken van de reken prestaties in azure-sku's.
