---
title: Overzicht van virtuele Windows-machines in azure
description: Overzicht van virtuele Windows-machines in Azure.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: overview
ms.date: 11/14/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: efcbdb2ebd7f4830214dbd0f2ea2ea1cfe36c3de
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75893520"
---
# <a name="windows-virtual-machines-in-azure"></a>Virtuele Windows-machines in Azure

Azure Virtual Machines (VM) vormen een van de diverse typen [schaalbare on-demand computerresources](/azure/architecture/guide/technology-choices/compute-decision-tree) die Azure biedt. Normaal gesproken kiest u voor een VM wanneer u meer controle nodig hebt over de computeromgeving dan andere opties bieden. In dit artikel vindt u informatie over wat u moet overwegen voordat u een VM maakt, hoe u deze maakt en hoe u deze beheert.

Een VM in Azure biedt u de flexibiliteit van virtualisatie zonder dat u de fysieke hardware hoeft te kopen en te beheren waarop de VM wordt uitgevoerd. U moet de VM echter wel onderhouden door taken uit te voeren, zoals het configureren, patchen en onderhouden van de software die erop wordt uitgevoerd.

Virtuele machines in Azure kunnen op verschillende manieren worden gebruikt. Een aantal voorbeelden:

* **Ontwikkeling en tests**: Azure-VM’s bieden een snelle en eenvoudige manier om een computer te maken met specifieke configuraties die nodig zijn voor de code van een toepassing en het testen ervan.
* **Toepassingen in de cloud**: De vraag naar uw toepassing kan variëren, dus kan het financieel verstandig zijn om deze uit te voeren op een VM in Azure. U betaalt voor extra virtuele machines wanneer u ze nodig hebt en schakelt ze uit wanneer u ze niet meer nodig hebt.
* **Uitgebreid datacenter**: Virtuele machines in een virtueel Azure-netwerk kunnen eenvoudig worden verbonden met het netwerk van uw organisatie.

Het aantal virtuele machines dat uw toepassing gebruikt, kan omhoog worden geschaald naar wat is vereist om te voldoen aan uw behoeften.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>Waar moet ik over nadenken voordat ik een VM maak?
Er is altijd een groot aantal [overwegingen bij het ontwerpen](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/windows-vm) wanneer u de infrastructuur van een toepassing verder uitwerkt in Azure. Deze aspecten van een VM zijn belangrijk om over na te denken voordat u begint:

* De namen van uw toepassingsresources
* De locatie waar de resources worden opgeslagen
* De grootte van de VM
* Het maximumaantal VM's dat kan worden gemaakt
* Het besturingssysteem dat op de VM wordt uitgevoerd
* De configuratie van de VM nadat deze is gestart
* De gerelateerde resources die de VM nodig heeft

### <a name="locations"></a>Locaties
Alle resources die in Azure zijn gemaakt, worden verdeeld over meerdere [geografische regio's](https://azure.microsoft.com/regions/) over de hele wereld. Normaal gesproken heet de regio **locatie** wanneer u een VM maakt. Voor een VM geeft de locatie aan waar de virtuele harde schijven zijn opgeslagen.

In deze tabel staan enkele manieren om een lijst met beschikbare locaties te verkrijgen.

| Methode | Beschrijving |
| --- | --- |
| Azure Portal |Selecteer een locatie in de lijst bij het maken van een VM. |
| Azure PowerShell |Gebruik de opdracht [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation). |
| REST API |Gebruik de bewerking [Locaties vermelden](https://docs.microsoft.com/rest/api/resources/subscriptions). |
| Azure-CLI |Gebruik de bewerking [az account list-locations](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest). |

## <a name="availability"></a>Beschikbaarheid
Voor Azure is een toonaangevende serviceovereenkomst (SLA) van 99,9% aangekondigd voor één VM-instantie. Hiervoor geldt wel als voorwaarde dat de virtuele machine wordt geïmplementeerd met Premium-opslag voor alle schijven.  Als u wilt dat uw VM-implementatie in aanmerking komt voor de SLA van 99,95%, moet u bovendien een beschikbaarheidsset maken met ten minste twee VM's waarop uw workload wordt uitgevoerd. Dit zorgt ervoor dat uw VM's worden verdeeld over meerdere foutdomeinen in de Azure-datacenters en worden geïmplementeerd op hosts met verschillende onderhoudsvensters. In de volledige [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) wordt de gegarandeerde beschikbaarheid van Azure als geheel uitgelegd.


## <a name="vm-size"></a>VM-grootte
De [grootte](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) van de VM die u gebruikt, wordt bepaald door de workload die u wilt uitvoeren. De grootte die u vervolgens kiest, bepaalt factoren als processorsnelheid, geheugen en opslagcapaciteit. Azure biedt een groot aantal verschillende grootten voor verschillende manieren van gebruik.

Azure rekent een [uurprijs](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) op basis van de grootte en het besturingssysteem van de VM. Voor niet-hele uren worden alleen de minuten van gebruik in rekening gebracht. De opslag wordt afzonderlijk berekend en in rekening gebracht.

## <a name="vm-limits"></a>VM-limieten
Uw abonnement heeft een standaard [quotumlimiet](../../azure-resource-manager/management/azure-subscription-service-limits.md) ingebouwd die de implementatie van veel VM’s voor uw project kan beïnvloeden. De huidige limiet per abonnement is 20 VM's per regio. Limieten kunnen worden verhoogd door [een ondersteuningsticket in te dienen met een aanvraag voor een verhoging](../../azure-portal/supportability/resource-manager-core-quotas-request.md)

### <a name="operating-system-disks-and-images"></a>Schijven en installatiekopieën voor een besturingssysteem
Virtuele machines maken gebruik van [virtuele harde schijven (VHD's)](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) voor de opslag van het besturingssysteem (OS) en de gegevens. VHD's worden ook gebruikt voor de installatiekopieën waarmee u een besturingssysteem kunt installeren. 

Azure biedt veel [marketplace-installatiekopieën](https://azure.microsoft.com/marketplace/virtual-machines/) voor gebruik met verschillende versies en typen van de Windows Server-besturingssystemen. Marketplace-installatiekopieën worden aangeduid met uitgever, aanbieding, SKU en versie van de installatiekopie (de versie wordt meestal gespecificeerd als meest recente). Alleen 64-bits besturingssystemen worden ondersteund. Zie [Microsoft-serversoftwareondersteuning voor virtuele Microsoft Azure-machines](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) voor meer informatie over de ondersteunde gastbesturingssystemen, rollen en onderdelen.

In deze tabel ziet u een aantal manieren waarop u de gegevens voor een installatiekopie kunt vinden.

| Methode | Beschrijving |
| --- | --- |
| Azure Portal |De waarden worden automatisch opgegeven wanneer u een installatiekopie selecteert om te gebruiken. |
| Azure PowerShell |[Get-AzVMImagePublisher](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimagepublisher) -Location *location*<BR>[Get-AzVMImageOffer](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimageoffer) -Location *location* -Publisher *publisherName*<BR>[Get-AzVMImageSku](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimagesku) -Location *location* -Publisher *publisherName* -Offer *offerName* |
| REST API's |[Uitgevers van installatiekopieën weergeven](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publishers)<BR>[Aanbiedingen van installatiekopieën weergeven](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offers)<BR>[Installatiekopie-SKU's weergeven](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus) |
| Azure-CLI |[az vm image list-publishers](https://docs.microsoft.com/cli/azure/vm/image?view=azure-cli-latest) --locatie *location*<BR>[az vm image list-offers](https://docs.microsoft.com/cli/azure/vm/image?view=azure-cli-latest) --location *locatie* --publisher *publisherName*<BR>[az vm image list-skus](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest) --location *locatie* --publisher *publisherName* --offer *offerName*|

U kunt ervoor kiezen om [uw eigen installatiekopie te uploaden en te gebruiken](upload-generalized-managed.md). Wanneer u dit doet, worden de uitgeversnaam, aanbieding en SKU niet gebruikt.

### <a name="extensions"></a>Extensies
VM-[extensies](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) geven uw VM meer mogelijkheden via de post-implementatieconfiguratie en geautomatiseerde taken.

Deze algemene taken kunnen worden uitgevoerd met extensies:

* **Aangepaste scripts uitvoeren**: de [aangepaste scriptextensie](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) helpt u workloads op de VM te configureren door uw script uit te voeren wanneer de VM is ingericht.
* **Configuraties implementeren en beheren**: de [PowerShell Desired State Configuration (DSC)-extensie](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) helpt bij het instellen van DSC op een VM voor het beheren van configuraties en omgevingen.
* **Diagnostische gegevens verzamelen**: de [Azure Diagnostics-extensie](extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) helpt u bij het configureren van de VM voor het verzamelen van diagnostische gegevens die kunnen worden gebruikt voor het bewaken van de status van uw toepassing.

### <a name="related-resources"></a>Gerelateerde bronnen
De resources in deze tabel worden gebruikt door de VM en moeten bestaan of worden gemaakt wanneer de VM wordt gemaakt.

| Bron | Verplicht | Beschrijving |
| --- | --- | --- |
| [Resourcegroep](../../azure-resource-manager/management/overview.md) |Ja |De VM moet zijn opgenomen in een resourcegroep. |
| [Opslagaccount](../../storage/common/storage-create-storage-account.md) |Ja |De VM heeft het opslagaccount nodig voor het opslaan van de virtuele harde schijven. |
| [Virtueel netwerk](../../virtual-network/virtual-networks-overview.md) |Ja |De VM moet lid zijn van een virtueel netwerk. |
| [Openbaar IP-adres](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) |Nee |Aan de VM kan een openbaar IP-adres worden toegewezen voor externe toegang. |
| [Netwerkinterface](../../virtual-network/virtual-network-network-interface.md) |Ja |De netwerkinterface van de VM moet in het netwerk communiceren. |
| [Gegevensschijven](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |Nee |De VM kan gegevensschijven bevatten om opslagmogelijkheden uit te breiden. |

## <a name="next-steps"></a>Volgende stappen

Maak uw eerste VM.

- [Portal](quick-create-portal.md)
- [PowerShell](quick-create-powershell.md)
- [Azure-CLI](quick-create-cli.md)

