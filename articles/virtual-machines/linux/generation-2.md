---
title: Azure-ondersteuning voor virtuele machines van de 2e generatie
description: Overzicht van Azure-ondersteuning voor virtuele machines van de 2e generatie
author: ju-shim
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/11/2020
ms.author: jushiman
ms.openlocfilehash: b68179caed4df5efd3696d7d6a3739a1e07a3c80
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79267298"
---
# <a name="support-for-generation-2-vms-on-azure"></a>Ondersteuning voor virtuele machines van generatie 2 op Azure

Ondersteuning voor virtuele machines van generatie 2 (Vm's) is nu beschikbaar in Azure. U kunt de generatie van een virtuele machine niet wijzigen nadat u deze hebt gemaakt. Controleer de overwegingen op deze pagina voordat u een generatie kiest.

Vm's van generatie 2 ondersteunen belang rijke functies die niet worden ondersteund in virtuele machines van de eerste generatie. Tot deze functies behoren meer geheugen, Intel-software Guard Extensions (Intel SGX) en gevirtualiseerde permanent geheugen (vPMEM). Virtuele machines van de tweede generatie die on-premises worden uitgevoerd, hebben enkele functies die nog niet worden ondersteund in Azure. Zie de sectie [functies en mogelijkheden](#features-and-capabilities) voor meer informatie.

Vm's van generatie 2 gebruiken de nieuwe op UEFI gebaseerde opstart architectuur in plaats van de op BIOS gebaseerde architectuur die wordt gebruikt door virtuele machines van de eerste generatie. Vergeleken met virtuele machines van de eerste generatie, hebben virtuele machines van de tweede generatie mogelijk betere opstart-en installatie tijden. Zie voor een overzicht van de virtuele machines van de 2e generatie en enkele verschillen tussen de generatie 1 en generatie 2, [moet ik een generatie 1 of 2 maken in Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

## <a name="generation-2-vm-sizes"></a>VM-grootten van generatie 2

Virtuele machines van de eerste generatie worden ondersteund door alle VM-grootten in azure (met uitzonde ring van Vm's uit de Mv2-serie). Azure biedt nu ondersteuning voor generatie 2 voor de volgende geselecteerde VM-serie:

* [B-serie](https://docs.microsoft.com/azure/virtual-machines/linux/b-series-burstable)
* [DC-serie](../dcv2-series.md)
* [DSv2-serie](../dv2-dsv2-series.md) en [Dsv3-serie](../dv3-dsv3-series.md)
* [Esv3-serie](../ev3-esv3-series.md)
* [Fsv2-serie](../fsv2-series.md)
* [GS-serie](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-previous-gen#gs-series)
* [HB-serie](../hb-series.md)
* [HC-serie](../hc-series.md)
* [Ls-Series](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-previous-gen#ls-series) en [Lsv2-serie](../lsv2-series.md)
* [Mv2-serie](../mv2-series.md)
* [NCv2-serie](../ncv2-series.md) en [NCv3-serie](../ncv3-series.md)
* [ND-serie](../nd-series.md)
* [NVv3-serie](../nvv3-series.md)

> [!NOTE]
> Het gebruik van VM-installatie kopieën van de tweede generatie voor virtuele machines uit de Mv2-serie is algemeen beschikbaar omdat de Mv2-serie alleen werkt met installatie kopieën van de 2e generatie. VM-installatie kopieën van de eerste generatie worden niet ondersteund op Vm's uit de Mv2-serie. 

## <a name="generation-2-vm-images-in-azure-marketplace"></a>VM-installatie kopieën van generatie 2 in azure Marketplace

Vm's van generatie 2 ondersteunen de volgende installatie kopieën van Marketplace:

* Windows Server 2019, 2016, 2012 R2, 2012
* Windows 10
* SUSE Linux Enterprise Server 15 SP1
* SUSE Linux Enterprise Server 12 SP4
* Ubuntu Server 16,04, 18,04, 19,04, 19,10 
* RHEL 8,1, 8,0, 7,7, 7,6, 7,5, 7,4, 7,0
* Cent OS 8,0, 7,7, 7,6, 7,5, 7,4
* Oracle Linux 7,7, 7,7-CI

## <a name="on-premises-vs-azure-generation-2-vms"></a>On-premises versus Azure Generation 2 Vm's

Azure biedt momenteel geen ondersteuning voor enkele van de functies die on-premises Hyper-V ondersteunt voor virtuele machines van de tweede generatie.

| Functie van de 2e generatie                | On-premises Hyper-V | Azure |
|-------------------------------------|---------------------|-------|
| Beveiligd opstarten                         | :heavy_check_mark:  | BxDxH   |
| Afgeschermde VM                         | :heavy_check_mark:  | BxDxH   |
| vTPM                                | :heavy_check_mark:  | BxDxH   |
| Beveiliging op basis van virtualisatie (VBS) | :heavy_check_mark:  | BxDxH   |
| VHDX-indeling                         | :heavy_check_mark:  | BxDxH   |

## <a name="features-and-capabilities"></a>Functies en mogelijkheden

### <a name="generation-1-vs-generation-2-features"></a>Generatie 1 vs. generatie 2-functies

| Functie | Generatie 1 | Generatie 2 |
|---------|--------------|--------------|
| modus             | PCAT         | UEFI |
| Schijf controllers | HARDE          | SCSI |
| Formaten van virtuele machines         | Alle VM-grootten | Alleen Vm's die ondersteuning bieden voor Premium Storage |

### <a name="generation-1-vs-generation-2-capabilities"></a>Mogelijkheden van generatie 1 vs. generatie 2

| Mogelijkheid | Generatie 1 | Generatie 2 |
|------------|--------------|--------------|
| BESTURINGSSYSTEEM schijf > 2 TB                    | BxDxH                | :heavy_check_mark: |
| Besturings systeem voor aangepaste schijf/image/swap         | :heavy_check_mark: | :heavy_check_mark: |
| Ondersteuning voor schaal sets voor virtuele machines | :heavy_check_mark: | :heavy_check_mark: |
| Azure Site Recovery               | :heavy_check_mark: | :heavy_check_mark: |
| Back-up maken/herstellen                    | :heavy_check_mark: | :heavy_check_mark: |
| Galerie met gedeelde afbeeldingen              | :heavy_check_mark: | :heavy_check_mark: |
| Azure Disk Encryption             | :heavy_check_mark: | BxDxH                |

## <a name="creating-a-generation-2-vm"></a>Een virtuele machine van de tweede generatie maken

### <a name="marketplace-image"></a>Marketplace-installatie kopie

In de Azure Portal of Azure CLI kunt u virtuele machines van de tweede generatie maken op basis van een Marketplace-installatie kopie die ondersteuning biedt voor UEFI-opstart bewerkingen.

#### <a name="azure-portal"></a>Azure-portal

Hieronder vindt u de stappen voor het maken van een Gen2-VM (Generation 2) in Azure Portal.

1. Meld u aan bij Azure Portal op https://portal.azure.com.
1. Selecteer **Een resource maken**.
1. Klik op **alles weer geven** in azure Marketplace aan de linkerkant.
1. Selecteer een installatie kopie die Gen2 ondersteunt.
1. Klik op **Create**.
1. Selecteer op het tabblad **Geavanceerd** onder de sectie **VM-generatie** de optie **generatie 2** .
1. Ga naar het tabblad **basis** informatie en klik onder **Details van exemplaar**op **grootte** en open de Blade **VM-grootte selecteren** .
1. Selecteer een [ondersteunde virtuele machine van de tweede generatie](#generation-2-vm-sizes).
1. Ga door met de stroom voor het maken van een virtuele machine in [Azure Portal](quick-create-portal.md) .

![Selecteer generatie 1 of generatie 2 virtuele machine](./media/generation-2/gen1-gen2-select.png)

#### <a name="powershell"></a>PowerShell

U kunt ook Power shell gebruiken om een virtuele machine te maken door rechtstreeks te verwijzen naar de SKU van de eerste en 2e generatie.

Gebruik bijvoorbeeld de volgende Power shell-cmdlet om een lijst op te halen van de Sku's in de `WindowsServer` aanbieding.

```powershell
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```

U kunt ook de Azure CLI gebruiken voor een overzicht van alle beschik bare installatie kopieën van de tweede generatie, vermeld door **Publisher**.

```azurecli
az vm image list --publisher Canonical --sku gen2 --output table --all
```

Als u een virtuele machine met Windows Server 2012 als besturings systeem maakt, selecteert u de VM-SKU van generatie 1 (BIOS) of generatie 2 (UEFI), die er als volgt uitziet:

```powershell
2012-Datacenter
2012-datacenter-gensecond
```

Zie de sectie [functies en mogelijkheden](#features-and-capabilities) voor een huidige lijst met ondersteunde Marketplace-installatie kopieën.

### <a name="managed-image-or-managed-disk"></a>Beheerde installatie kopie of beheerde schijf

U kunt een virtuele machine van de tweede generatie van een beheerde installatie kopie of een beheerde schijf op dezelfde manier maken als een virtuele machine van de eerste generatie.

### <a name="virtual-machine-scale-sets"></a>Virtuele-machineschaalsets

U kunt ook virtuele machines van de tweede generatie maken met behulp van schaal sets voor Virtual Machine. Gebruik in de Azure CLI Azure-schaal sets om virtuele machines van generatie 2 te maken.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

* **Zijn er Vm's van generatie 2 beschikbaar in alle Azure-regio's?**  
    Ja. Maar niet alle [VM-grootten van generatie 2](#generation-2-vm-sizes) zijn in elke regio beschikbaar. De beschik baarheid van de generatie 2 virtuele machine is afhankelijk van de beschik baarheid van de VM-grootte.

* **Is er een prijs verschil tussen Vm's van generatie 1 en generatie 2?**  
    Nee.

* **Ik heb een VHD-bestand van mijn on-premises generatie 2 VM. Kan ik dat VHD-bestand gebruiken om een virtuele machine van de tweede generatie in azure te maken?**
  Ja, u kunt uw VHD-bestand van de tweede generatie naar Azure brengen en gebruiken om een virtuele machine van de tweede generatie te maken. Gebruik de volgende stappen om dit te doen:
    1. Upload het. VHD-archief naar een opslag account in dezelfde regio waarin u de virtuele machine wilt maken.
    1. Een beheerde schijf maken op basis van het VHD-bestand. Stel de eigenschap Hyper-V Generation in op v2. Met de volgende Power shell-opdrachten wordt de eigenschap Hyper-V Generation ingesteld bij het maken van een beheerde schijf.

        ```powershell
        $sourceUri = 'https://xyzstorage.blob.core.windows.net/vhd/abcd.vhd'. #<Provide location to your uploaded .vhd file>
        $osDiskName = 'gen2Diskfrmgenvhd'  #<Provide a name for your disk>
        $diskconfig = New-AzDiskConfig -Location '<location>' -DiskSizeGB 127 -AccountType Standard_LRS -OsType Windows -HyperVGeneration "V2" -SourceUri $sourceUri -CreateOption 'Import'
        New-AzDisk -DiskName $osDiskName -ResourceGroupName '<Your Resource Group>' -Disk $diskconfig
        ```

    1. Zodra de schijf beschikbaar is, maakt u een virtuele machine door deze schijf te koppelen. De virtuele machine die wordt gemaakt, is een VM van de tweede generatie.
    Bij het maken van de virtuele machine van de tweede generatie kunt u optioneel de installatie kopie van deze VM generaliseren. Door de installatie kopie te generaliseren, kunt u deze gebruiken om meerdere Vm's te maken.

* **De schijf grootte van het besturings systeem Hoe kan ik verg Roten?**  
  BESTURINGSSYSTEEM schijven van meer dan 2 TB zijn nieuw voor virtuele machines van de tweede generatie. BESTURINGSSYSTEEM schijven zijn standaard kleiner dan 2 TB voor virtuele machines van de tweede generatie. U kunt de schijf grootte verg Roten tot een aanbevolen maximum van 4 TB. Gebruik de Azure CLI of de Azure Portal om de schijf grootte van het besturings systeem te verg Roten. Zie het [formaat van een schijf wijzigen](expand-disks.md)voor meer informatie over het programmatisch uitvouwen van schijven.

  De schijf grootte van het besturings systeem verg Roten van de Azure Portal:

  1. Ga in het Azure Portal naar de pagina met VM-eigenschappen.
  1. Als u de virtuele machine wilt afsluiten en de toewijzing ongedaan wilt maken, selecteert u de knop **stoppen** .
  1. Selecteer in de sectie **schijven** de besturingssysteem schijf die u wilt verg Roten.
  1. Selecteer in de sectie **schijven** de optie **configuratie**en werk de **grootte** bij naar de gewenste waarde.
  1. Ga terug naar de pagina met eigenschappen van de virtuele machine en **Start** de virtuele machine.

  Mogelijk wordt er een waarschuwing weer gegeven voor besturingssysteem schijven die groter zijn dan 2 TB. De waarschuwing is niet van toepassing op virtuele machines van de tweede generatie. BESTURINGSSYSTEEM schijven die groter zijn dan 4 TB worden echter *niet aanbevolen.*

* **Ondersteunen virtuele machines van generatie 2 versneld netwerken?**  
    Ja. Zie [een virtuele machine maken met versneld netwerken](../../virtual-network/create-vm-accelerated-networking-cli.md)voor meer informatie.

* **Wordt VHDX ondersteund voor generatie 2?**  
    Nee; Vm's van generatie 2 ondersteunen alleen VHD.

* **Ondersteunen virtuele machines van generatie 2 Azure Ultra Disk Storage?**  
    Ja.

* **Kan ik een virtuele machine van generatie 1 migreren naar generatie 2?**  
    Nee, u kunt de generatie van een virtuele machine niet wijzigen nadat u deze hebt gemaakt. Als u wilt scha kelen tussen VM'S gegenereerd, maakt u een nieuwe VM van een andere generatie.

* **Waarom is mijn VM-grootte niet ingeschakeld in de formaat kiezer wanneer ik een Gen2-VM Maak?**

    Dit kan worden opgelost door de volgende handelingen uit te voeren:

    1. Controleer of de eigenschap voor het genereren van de **virtuele machine** is ingesteld op **gen 2** op het tabblad **Geavanceerd** .
    1. Controleer of u zoekt naar een [VM-grootte die Gen2 vm's ondersteunt](#generation-2-vm-sizes).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [virtuele machines van de 2e generatie in Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).
