---
title: Een Ubuntu Linux VHD maken en uploaden in azure
description: Meer informatie over het maken en uploaden van een virtuele harde schijf (VHD) van Azure die een Ubuntu Linux besturings systeem bevat.
author: mimckitt
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 06/24/2019
ms.author: mimckitt
ms.openlocfilehash: cbb10d544cb299e15022ae47f00d3887d03619c0
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970290"
---
# <a name="prepare-an-ubuntu-virtual-machine-for-azure"></a>Een virtuele Ubuntu-machine voorbereiden voor Azure


Ubuntu publiceert nu officiële Azure-Vhd's om te downloaden op [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/). Als u uw eigen gespecialiseerde Ubuntu-installatie kopie voor Azure moet bouwen, in plaats van de hand matige procedure hieronder te gebruiken, wordt u aangeraden te beginnen met deze bekende werk schijven en zo nodig aan te passen. U kunt de nieuwste afbeeldings releases altijd vinden op de volgende locaties:

* Ubuntu 12.04/nauw keurig: [Ubuntu-12,04-server-cloudimg-amd64-Disk1. VHD. zip](https://cloud-images.ubuntu.com/precise/current/precise-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 14.04/Trusty: [Ubuntu-14,04-server-cloudimg-amd64-Disk1. VHD. zip](https://cloud-images.ubuntu.com/releases/trusty/release/ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 16.04/Xenial: [Ubuntu-16,04-server-cloudimg-amd64-Disk1. vmdk](https://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vmdk)
* Ubuntu 18.04/Bionic: [Bionic-server-cloudimg-amd64. vmdk](https://cloud-images.ubuntu.com/bionic/current/bionic-server-cloudimg-amd64.vmdk)
* Ubuntu 18.10/Cosmic: [Cosmic-server-cloudimg-amd64. VHD. zip](http://cloud-images.ubuntu.com/releases/cosmic/release/ubuntu-18.10-server-cloudimg-amd64.vhd.zip)

## <a name="prerequisites"></a>Vereisten
In dit artikel wordt ervan uitgegaan dat u al een Ubuntu Linux besturings systeem hebt geïnstalleerd op een virtuele harde schijf. Er zijn meerdere hulpprogram ma's voor het maken van VHD-bestanden, zoals Hyper-V. Zie [de Hyper-V-functie installeren en een virtuele machine configureren](https://technet.microsoft.com/library/hh846766.aspx)voor instructies.

**Ubuntu-installatie notities**

* Zie ook [algemene Linux-installatie notities](create-upload-generic.md#general-linux-installation-notes) voor meer tips over het voorbereiden van Linux voor Azure.
* De VHDX-indeling wordt niet ondersteund in azure, alleen **vaste VHD**.  U kunt de schijf converteren naar VHD-indeling met behulp van Hyper-V-beheer of de cmdlet Convert-VHD.
* Bij de installatie van het Linux-systeem wordt aanbevolen om standaard partities te gebruiken in plaats van LVM (vaak de standaard instelling voor veel installaties). Hiermee wordt voor komen dat LVM naam strijdig is met gekloonde Vm's, met name als een besturingssysteem schijf ooit moet worden gekoppeld aan een andere virtuele machine voor het oplossen van problemen. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) of [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) kan worden gebruikt op gegevens schijven als dit de voor keur heeft.
* Configureer geen swap partitie op de besturingssysteem schijf. De Linux-agent kan worden geconfigureerd voor het maken van een wissel bestand op de tijdelijke bron schijf.  Meer informatie hierover vindt u in de volgende stappen.
* Alle Vhd's op Azure moeten een virtuele grootte hebben die is afgestemd op 1 MB. Wanneer u van een onbewerkte schijf naar VHD converteert, moet u ervoor zorgen dat de onbewerkte schijf grootte een meervoud van 1MB is vóór de conversie. Zie [installatie notities voor Linux](create-upload-generic.md#general-linux-installation-notes) voor meer informatie.

## <a name="manual-steps"></a>Hand matige stappen
> [!NOTE]
> Voordat u probeert uw eigen aangepaste Ubuntu-installatie kopie voor Azure te maken, kunt u in plaats daarvan de vooraf gemaakte en geteste installatie kopieën van [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/) gebruiken.
> 
> 

1. Selecteer de virtuele machine in het middelste deel venster van Hyper-V-beheer.

2. Klik op **verbinding maken** om het venster voor de virtuele machine te openen.

3. Vervang de huidige opslag plaatsen in de installatie kopie om de Azure-opslag plaats van Ubuntu te gebruiken. De stappen kunnen enigszins verschillen, afhankelijk van de Ubuntu-versie.
   
    Voordat u `/etc/apt/sources.list`bewerkt, wordt het aanbevolen om een back-up te maken:
   
        # sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

    Ubuntu 12,04:
   
        # sudo sed -i 's/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g' /etc/apt/sources.list
        # sudo apt-get update

    Ubuntu 14,04:
   
        # sudo sed -i 's/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g' /etc/apt/sources.list
        # sudo apt-get update

    Ubuntu 16.04:
   
        # sudo sed -i 's/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g' /etc/apt/sources.list
        # sudo apt-get update

4. De Ubuntu Azure-installatie kopieën volgen nu de HWE-kernel ( *Hardware-activering* ). Werk het besturings systeem bij naar de nieuwste kernel door de volgende opdrachten uit te voeren:

    Ubuntu 12,04:
   
        # sudo apt-get update
        # sudo apt-get install linux-image-generic-lts-trusty linux-cloud-tools-generic-lts-trusty
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade
   
        # sudo reboot
   
    Ubuntu 14,04:
   
        # sudo apt-get update
        # sudo apt-get install linux-image-virtual-lts-vivid linux-lts-vivid-tools-common
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade
   
        # sudo reboot

    Ubuntu 16.04:
   
        # sudo apt-get update
        # sudo apt-get install linux-generic-hwe-16.04 linux-cloud-tools-generic-hwe-16.04
        (recommended) sudo apt-get dist-upgrade

        # sudo reboot
    
    Ubuntu 18.04.04:
    
        # sudo apt-get update
        # sudo apt-get install --install-recommends linux-generic-hwe-18.04 xserver-xorg-hwe-18.04
        # sudo apt-get install --install-recommends linux-cloud-tools-generic-hwe-18.04
        (recommended) sudo apt-get dist-upgrade

        # sudo reboot
    
    **Zie ook:**
    - [https://wiki.ubuntu.com/Kernel/LTSEnablementStack](https://wiki.ubuntu.com/Kernel/LTSEnablementStack)
    - [https://wiki.ubuntu.com/Kernel/RollingLTSEnablementStack](https://wiki.ubuntu.com/Kernel/RollingLTSEnablementStack)


5. Wijzig de kernel-opstart regel voor grub zodat er aanvullende kernel-para meters voor Azure worden toegevoegd. Als u dit wilt doen `/etc/default/grub` in een tekst editor, zoekt u de variabele met de naam `GRUB_CMDLINE_LINUX_DEFAULT` (of voegt u deze toe, indien nodig) en bewerkt u deze zodat de volgende para meters worden toegevoegd:
   
        GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300"

    Sla het bestand op en sluit dit en voer `sudo update-grub`uit. Dit zorgt ervoor dat alle console berichten worden verzonden naar de eerste seriële poort, die ondersteuning biedt voor technische ondersteuning van Azure bij problemen met de fout opsporing.

6. Zorg ervoor dat de SSH-server is geïnstalleerd en geconfigureerd om te starten bij het opstarten.  Dit is doorgaans de standaard instelling.

7. De Azure Linux-agent installeren:
   
        # sudo apt-get update
        # sudo apt-get install walinuxagent

   > [!Note]
   >  Het `walinuxagent`-pakket kan de `NetworkManager` en `NetworkManager-gnome` pakketten verwijderen als deze zijn geïnstalleerd.


1. Voer de volgende opdrachten uit om de inrichting van de virtuele machine ongedaan te maken en deze voor te bereiden voor de inrichting van Azure:
   
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

1. Klik op **actie-> afgesloten** in Hyper-V-beheer. Uw Linux-VHD is nu gereed om te worden geüpload naar Azure.

## <a name="references"></a>Verwijzingen
[HWE-kernel (Ubuntu hardware-activering)](https://wiki.ubuntu.com/Kernel/LTSEnablementStack)

## <a name="next-steps"></a>Volgende stappen
U kunt nu uw Ubuntu Linux virtuele harde schijf gebruiken om nieuwe virtuele machines te maken in Azure. Zie [een virtuele Linux-machine maken op basis van een aangepaste schijf](upload-vhd.md#option-1-upload-a-vhd)als dit de eerste keer is dat u het VHD-bestand naar Azure uploadt.

