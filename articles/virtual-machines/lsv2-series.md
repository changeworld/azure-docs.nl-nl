---
title: Lsv2-serie-Azure Virtual Machines
description: Specificaties voor de virtuele machines uit de Lsv2-serie.
services: virtual-machines
author: sasha-melamed
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 103e19d6e299956b5ee1ad45b577e25f9f2de1c4
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "78164029"
---
# <a name="lsv2-series"></a>Lsv2-serie

De Lsv2-serie biedt een hoge door Voer, lage latentie, rechtstreeks toegewezen lokale NVMe-opslag die wordt uitgevoerd op de [AMD EPYC<sup>TM</sup> 7551-processor](https://www.amd.com/en/products/epyc-7000-series) met een alle kern versterking van 2.55 GHz en een maximale boost van 3,0 GHz. De virtuele machines uit de Lsv2-serie zijn beschikbaar in groottes van 8 tot 80 vCPU in een gelijktijdige configuratie met meerdere threads.  Er is 8 GiB geheugen per vCPU en één 1.92 TB NVMe SSD M. 2-apparaat per 8 Vcpu's, met Maxi maal 19.2 TB (10x 1.92 TB) dat beschikbaar is op de L80s v2.

> [!NOTE]
> De virtuele machines uit de Lsv2-serie zijn geoptimaliseerd voor het gebruik van de lokale schijf op het knoop punt dat rechtstreeks aan de virtuele machine is gekoppeld, in plaats van duurzame gegevens schijven te gebruiken. Dit biedt meer IOPs/door Voer voor uw workloads. De Lsv2 en LS-series bieden geen ondersteuning voor het maken van een lokale cache om het aantal IOPs dat kan worden behaald door duurzame gegevens schijven te verhogen.
>
> De hoge door Voer en IOPs van de lokale schijf maken de virtuele machines uit de Lsv2-serie ideaal voor NoSQL-archieven zoals Apache Cassandra en MongoDB die gegevens repliceren op meerdere Vm's om persistentie te krijgen in het geval van een storing in één virtuele machine.
>
> Zie prestaties optimaliseren voor de virtuele machines uit de Lsv2-serie voor [Windows](../virtual-machines/windows/storage-performance.md) of [Linux](../virtual-machines/linux/storage-performance.md)voor meer informatie.  

ACU: 150-175

Premium Storage: ondersteund

Premium Storage caching: niet ondersteund

Livemigratie: niet ondersteund

Updates voor het behouden van geheugen: niet ondersteund

| Grootte | vCPU | Geheugen (GiB) | Tijdelijke schijf<sup>1</sup> (GIB) | NVMe-schijven<sup>2</sup> | NVMe-schijf doorvoer<sup>3</sup> (IOPS lezen/Mbps) | Maximale door Voer van niet in cache opgeslagen gegevens schijven (IOPs/MBps)<sup>4</sup> | Maximum aantal gegevens schijven | Maximum aantal Nic's/verwachte netwerk bandbreedte (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_L8s_v2   |  8 |  64 |  80 |  1x 1.92 TB  | 400000/2000  | 8000/160   | 16 | 2 / 3200   |
| Standard_L16s_v2  | 16 | 128 | 160 |  2x 1.92 TB  | 800000/4000  | 16000/320  | 32 | 4 / 6400   |
| Standard_L32s_v2  | 32 | 256 | 320 |  4x 1.92 TB  | 1,5 m/8000    | 32000/640  | 32 | 8 / 12800  |
| Standard_L48s_v2  | 48 | 384 | 480 |  6x 1.92 TB  | 2,2 m/14000   | 48000/960  | 32 | 8/16000 + |
| Standard_L64s_v2  | 64 | 512 | 640 |  8x 1.92 TB  | 2,9 m/16000   | 64000/1280 | 32 | 8/16000 + |
| Standard_L80s_v2<sup>5</sup> | 80 | 640 | 800 | 10x 1.92 TB | 3.8 m/20000 | 80000/1400 | 32 | 8/16000 + |

<sup>1</sup> virtuele machines uit de Lsv2-serie hebben een standaard op SCSI gebaseerde tijdelijke bron schijf voor het gebruik van paging/wissel bestand (D: op Windows,/dev/sdb in Linux). Deze schijf biedt 80 GiB aan opslag, 4.000 IOPS en 80 MBps overdrachts snelheid voor elke 8 Vcpu's (bijvoorbeeld Standard_L80s_v2 biedt 800 GiB op 40.000 IOPS en 800 MBPS). Dit zorgt ervoor dat de NVMe-stations volledig kunnen worden gebruikt voor het gebruik van toepassingen. Deze schijf is tijdelijk en alle gegevens gaan verloren bij stoppen/toewijzing ongedaan maken.

<sup>2</sup> lokale NVMe-schijven zijn tijdelijk, gegevens op deze schijven gaan verloren als u de toewijzing van uw virtuele machine stopt/ongedaan maakt.

<sup>3</sup> Hyper-V NVMe direct-technologie biedt onbeperkte toegang tot lokale NVMe-stations die veilig zijn gekoppeld aan de gast-VM-ruimte.  Voor maximale prestaties moet u de nieuwste versie van WS2019 build of Ubuntu 18,04 of 16,04 van de Azure Marketplace gebruiken.  De schrijf prestaties zijn afhankelijk van de i/o-grootte, de belasting van het apparaat en het capaciteits gebruik.

<sup>4</sup> virtuele machines uit de Lsv2-serie bieden geen host-cache voor gegevens schijven omdat het geen voor deel is van de Lsv2-workloads.  Lsv2 Vm's kunnen echter worden voorzien van de tijdelijke VM-besturingssysteem schijf optie van Azure (Maxi maal 30 GiB).

<sup>5</sup> vm's met meer dan 64 vcpu's vereisen een van deze ondersteunde gast besturingssystemen:

- Windows Server 2016 of hoger
- Ubuntu 16,04 LTS of hoger, met door Azure afgestemde kernel (4,15 kernel of hoger)
- SLES 12 SP2 of hoger
- RHEL of CentOS versie 6,7 tot en met 6,10, met het door micro soft meegeleverde LIS-pakket 4.3.1 (of hoger)
- RHEL of CentOS versie 7,3, met het door micro soft meegeleverde LIS-pakket 4.2.1 (of hoger) geïnstalleerd
- RHEL of CentOS versie 7,6 of hoger
- Oracle Linux met UEK4 of hoger
- Debian 9 met de backports-kernel, Debian 10 of hoger
- CoreOS met een 4,14-kernel of hoger

## <a name="size-table-definitions"></a>De grootte van tabeldefinities wijzigen

- De opslagcapaciteit wordt weergegeven in GiB-eenheden of 1024^3 bytes. Wanneer u schijven die zijn gemeten in GB (1000^3 bytes), vergelijkt met schijven die zijn gemeten in GiB (1024^3), moet u voor ogen houden dat de capaciteit in GiB kleiner lijkt te zijn. 1023 GiB is bijvoorbeeld gelijk aan 1098,4 GB
- De schijfdoorvoer wordt gemeten in I/O-bewerkingen per seconde (IOPS) en MBps, waarbij MBps = 10^6 bytes per seconde.
- Als u de beste prestaties voor uw Vm's wilt krijgen, moet u het aantal gegevens schijven per vCPU beperken tot 2 schijven.
- **Verwachte netwerk bandbreedte** is het maximale aantal geaggregeerde [band breedte dat per VM-type](../virtual-network/virtual-machine-network-throughput.md) voor alle nic's voor alle doelen is toegewezen. Bovengrenzen worden niet gegarandeerd, maar zijn bedoeld als richtlijn voor het selecteren van het juiste VM-type voor de bedoelde toepassing. De werkelijke netwerkprestaties zijn afhankelijk van verschillende factoren, waaronder netwerkcongestie, toepassingsbelastingen en netwerkinstellingen. Zie [Netwerkdoorvoer optimaliseren voor Windows en Linux](../virtual-network/virtual-network-optimize-network-bandwidth.md) voor meer informatie over het optimaliseren van de netwerkdoorvoer. Om de verwachte netwerkprestaties op Linux en Windows te bereiken, kan het noodzakelijk zijn een specifieke versie te selecteren of de VM te optimaliseren. Zie [How to reliably test for virtual machine throughput](../virtual-network/virtual-network-bandwidth-testing.md) (Betrouwbaar testen van de doorvoer van virtuele machines) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe [Azure Compute units (ACU)](acu.md) u kan helpen bij het vergelijken van de reken prestaties in azure-sku's.
