---
title: Een Windows-VHD downloaden vanuit Azure
description: Down load een Windows-VHD met behulp van de Azure Portal.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 01/13/2019
ms.author: cynthn
ms.openlocfilehash: d1c98fa4f3572c40279978d787b1719746478a06
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75940455"
---
# <a name="download-a-windows-vhd-from-azure"></a>Een Windows-VHD downloaden vanuit Azure

In dit artikel leert u hoe u een Windows-VHD-bestand (virtuele harde schijf) kunt downloaden van Azure met behulp van de Azure Portal.

## <a name="optional-generalize-the-vm"></a>Optioneel: de virtuele machine generaliseren

Als u de VHD als een [installatie kopie](tutorial-custom-images.md) wilt gebruiken om andere vm's te maken, moet u [Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) gebruiken om het besturings systeem te generaliseren. 

Als u de VHD als een installatie kopie wilt gebruiken om andere Vm's te maken, generaliseert u de virtuele machine.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) als u dat nog niet hebt gedaan.
2. [Maak verbinding met de virtuele machine](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
3. Open het opdracht prompt venster op de virtuele machine als beheerder.
4. Wijzig de Directory in *%windir%\system32\sysprep* en voer Sysprep. exe uit.
5. Selecteer in het dialoog venster hulp programma voor systeem voorbereiding de optie **systeem out-of-Box Experience (OOBE) opgeven**en zorg ervoor dat **generaliseren** is geselecteerd.
6. Selecteer in afsluit opties de optie **Afsluiten**en klik vervolgens op **OK**. 


## <a name="stop-the-vm"></a>De virtuele machine stoppen

Een VHD kan niet worden gedownload van Azure als deze is gekoppeld aan een actieve VM. U moet de virtuele machine stoppen om een VHD te downloaden. 

1. Klik in het menu hub in de Azure Portal op **virtual machines**.
1. Selecteer de virtuele machine in de lijst.
1. Klik op de Blade voor de virtuele machine op **stoppen**.


## <a name="generate-download-url"></a>Download-URL genereren

Als u het VHD-bestand wilt downloaden, moet u een [SAS-URL (Shared Access Signature)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) genereren. Wanneer de URL wordt gegenereerd, wordt een verloop tijd toegewezen aan de URL.

1. Klik op de pagina voor de virtuele machine op **schijven** in het menu links.
1. Selecteer de schijf met het besturings systeem voor de virtuele machine.
1. Selecteer op de pagina voor de schijf de optie **schijf exporteren** in het menu links.
1. De standaard verval tijd van de URL is *3600* seconden. Verhoog dit voor de schijven van **36000** voor Windows-besturings systemen.
1. Klik op **URL genereren**.

> [!NOTE]
> De verval tijd wordt verhoogd van de standaard waarde om voldoende tijd te bieden voor het downloaden van het grote VHD-bestand voor een Windows Server-besturings systeem. Afhankelijk van uw verbinding kunt u een VHD-bestand met het Windows Server-besturings systeem verwachten dat er enkele uren mee moeten worden gedownload. Als u een VHD voor een gegevens schijf downloadt, is de standaard tijd voldoende. 
> 
> 

## <a name="download-vhd"></a>VHD downloaden

1. Klik onder de gegenereerde URL op het VHD-bestand downloaden.
1. Mogelijk moet u in uw browser op **Opslaan** klikken om het downloaden te starten. De standaard naam voor het VHD-bestand is *ABCD*.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [uploaden van een VHD-bestand naar Azure](upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
- [Beheerde schijven maken op basis van niet-beheerde schijven in een opslag account](attach-disk-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- [Azure-schijven beheren met Power shell](tutorial-manage-data-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

