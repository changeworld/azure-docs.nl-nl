---
title: "Azure Premium Storage: ontwerpen voor prestaties op Windows-Vm's | Microsoft Docs"
description: Ontwerp krachtige toepassingen met Azure Premium SSD Managed disks. Premium Storage biedt schijf ondersteuning met hoge prestaties en lage latentie voor I/O-intensieve workloads die worden uitgevoerd op Azure Virtual Machines.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 12fb94bb4f98bde5c70343f18762cefe1ab120f9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75371327"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Azure Premium-opslag: ontwerpen voor hoge prestaties
[!INCLUDE [virtual-machines-common-premium-storage-introduction](../../../includes/virtual-machines-common-premium-storage-introduction.md)]

> [!NOTE]
> Soms is het een probleem met de prestaties van een schijf. In deze situaties moet u de [netwerk prestaties](../../virtual-network/virtual-network-optimize-network-bandwidth.md)optimaliseren.
>
> Als u een bench Mark-schijf wilt maken, raadpleegt u ons artikel over de [benchmarking van een schijf](disks-benchmarks.md).
>
> Als uw virtuele machine versneld netwerken ondersteunt, moet u ervoor zorgen dat deze is ingeschakeld. Als deze optie niet is ingeschakeld, kunt u deze inschakelen op al geïmplementeerde Vm's in [Windows](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) en [Linux](../../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

Voordat u begint, lees dan eerst de [Select a Azure-schijf type voor IaaS vm's](disks-types.md) en [schaalbaarheids doelen voor Premium Premium Storage-pagina-Blob-opslag accounts](../../storage/blobs/scalability-targets-premium-page-blobs.md).

[!INCLUDE [virtual-machines-common-premium-storage-performance.md](../../../includes/virtual-machines-common-premium-storage-performance.md)]

Als u een bench Mark-schijf wilt maken, raadpleegt u ons artikel over de [benchmarking van een schijf](disks-benchmarks.md).

Meer informatie over de beschik bare schijf typen: [Selecteer een schijf type](disks-types.md)  

Lees voor SQL Server gebruikers artikelen over best practices voor prestaties voor SQL Server:

* [Aanbevolen procedures voor de prestaties van SQL Server in azure Virtual Machines](sql/virtual-machines-windows-sql-performance.md)
* [Azure Premium Storage biedt de beste prestaties voor SQL Server in azure VM](https://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx)