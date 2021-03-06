---
title: Filteren van netwerkverkeer - Azure PowerShell | Microsoft Docs
description: In dit artikel leert u hoe u netwerkverkeer filtert naar een subnet, met een netwerkbeveiligingsgroep met behulp van PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/30/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 08031bc2ac29ea77374e21c4ce6f7bcf6151bcad
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66730037"
---
# <a name="filter-network-traffic-with-a-network-security-group-using-powershell"></a>Netwerkverkeer filteren met een netwerkbeveiligingsgroep met behulp van PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

U kunt het netwerkverkeer inkomend in en uitgaand naar een subnet van een virtueel netwerk filteren met een netwerkbeveiligingsgroep. Netwerkbeveiligingsgroepen bevatten beveiligingsregels die netwerkverkeer filteren op IP-adres, poort en protocol. Beveiligingsregels worden toegepast op resources die zijn geïmplementeerd in een subnet. In dit artikel leert u het volgende:

* Een netwerkbeveiligingsgroep en beveiligingsregels maken
* Een virtueel netwerk maken en een netwerkbeveiligingsgroep koppelen aan een subnet
* Virtuele machines (VM) implementeren in een subnet
* Verkeersfilters testen

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u wilt installeren en gebruiken van PowerShell lokaal, in dit artikel is vereist voor de Azure PowerShell-moduleversie 1.0.0 of hoger. Voer `Get-Module -ListAvailable Az` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="create-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken

Een netwerkbeveiligingsgroep bevat beveiligingsregels. Beveiligingsregels geven een bron en doel op. Bronnen en doelen kunnen toepassingsbeveiligingsgroepen zijn.

### <a name="create-application-security-groups"></a>Toepassingsbeveiligingsgroepen maken

Maakt u eerst een resourcegroep voor alle resources die zijn gemaakt in dit artikel met [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). In het volgende voorbeeld wordt een resourcegroep met de naam gemaakt op de locatie *eastus*:

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Maak een toepassingsbeveiligingsgroep met [New-AzApplicationSecurityGroup](/powershell/module/az.network/new-azapplicationsecuritygroup). Met een toepassingsbeveiligingsgroep kunt u servers met vergelijkbare poortfiltervereisten groeperen. In het volgende voorbeeld worden twee toepassingsbeveiligingsgroepen gemaakt.

```azurepowershell-interactive
$webAsg = New-AzApplicationSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Name myAsgWebServers `
  -Location eastus

$mgmtAsg = New-AzApplicationSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Name myAsgMgmtServers `
  -Location eastus
```

### <a name="create-security-rules"></a>Beveiligingsregels maken

Maak een beveiligingsregel met [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig). In het volgende voorbeeld wordt een regel gemaakt die inkomend verkeer van internet naar de toepassingsbeveiligingsgroep *myWebServers* toestaat via de poorten 80 en 443:

```azurepowershell-interactive
$webRule = New-AzNetworkSecurityRuleConfig `
  -Name "Allow-Web-All" `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix Internet `
  -SourcePortRange * `
  -DestinationApplicationSecurityGroupId $webAsg.id `
  -DestinationPortRange 80,443

The following example creates a rule that allows traffic inbound from the internet to the *myMgmtServers* application security group over port 3389:

$mgmtRule = New-AzNetworkSecurityRuleConfig `
  -Name "Allow-RDP-All" `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 110 `
  -SourceAddressPrefix Internet `
  -SourcePortRange * `
  -DestinationApplicationSecurityGroupId $mgmtAsg.id `
  -DestinationPortRange 3389
```

In dit artikel RDP wordt (poort 3389) wordt blootgesteld aan internet voor de *myAsgMgmtServers* VM. Voor productieomgevingen wordt u aanbevolen in plaats van poort 3389 voor internet te gebruiken, verbinding te maken met Azure-resources die u wilt beheren met behulp van een [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) of [persoonlijke](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) netwerkverbinding.

### <a name="create-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken

Maak een netwerkbeveiligingsgroep met [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup). In het volgende voorbeeld wordt een netwerkbeveiligingsgroep met de naam *myNsg* gemaakt:

```powershell-interactive
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myNsg `
  -SecurityRules $webRule,$mgmtRule
```

## <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

Maak een virtueel netwerk met [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). In het volgende voorbeeld wordt een virtueel netwerk met de naam *myVirtualNetwork* gemaakt:

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Maak een subnetconfiguratie met [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig), en schrijf de subnetconfiguratie vervolgens naar het virtuele netwerk met [Set AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork). In het volgende voorbeeld wordt een subnet met de naam *mySubnet* toegevoegd aan het virtuele netwerk en wordt de netwerkbeveiligingsgroep *myNsg* hieraan gekoppeld:

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -VirtualNetwork $virtualNetwork `
  -AddressPrefix "10.0.2.0/24" `
  -NetworkSecurityGroup $nsg
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-virtual-machines"></a>Virtuele machines maken

Voordat u de VM's, ophalen van het virtuele netwerkobject met het subnet met de [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork):

```powershell-interactive
$virtualNetwork = Get-AzVirtualNetwork `
 -Name myVirtualNetwork `
 -Resourcegroupname myResourceGroup
```

Maken van een openbaar IP-adres voor elke virtuele machine met [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress):

```powershell-interactive
$publicIpWeb = New-AzPublicIpAddress `
  -AllocationMethod Dynamic `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myVmWeb

$publicIpMgmt = New-AzPublicIpAddress `
  -AllocationMethod Dynamic `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myVmMgmt
```

Maak twee netwerkinterfaces met [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface), en een openbaar IP-adres toewijzen aan de netwerkinterface. In het volgende voorbeeld wordt een netwerkinterface gemaakt, wordt het openbare IP-adres van *myVmWeb* eraan gekoppeld en wordt het tot lid gemaakt van de toepassingsbeveiligingsgroep *myAsgWebServers*:

```powershell-interactive
$webNic = New-AzNetworkInterface `
  -Location eastus `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup `
  -SubnetId $virtualNetwork.Subnets[0].Id `
  -ApplicationSecurityGroupId $webAsg.Id `
  -PublicIpAddressId $publicIpWeb.Id
```

In het volgende voorbeeld wordt een netwerkinterface gemaakt, wordt het openbare IP-adres van *myVmMgmt* eraan gekoppeld en wordt het tot lid gemaakt van de toepassingsbeveiligingsgroep *myAsgMgmtServers*:

```powershell-interactive
$mgmtNic = New-AzNetworkInterface `
  -Location eastus `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  -SubnetId $virtualNetwork.Subnets[0].Id `
  -ApplicationSecurityGroupId $mgmtAsg.Id `
  -PublicIpAddressId $publicIpMgmt.Id
```

Maak twee virtuele machines in het virtuele netwerk, zodat u het filteren van verkeer in een latere stap kunt controleren.

Maak een VM-configuratie met [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig), maakt u de virtuele machine met [New-AzVM](/powershell/module/az.compute/new-azvm). In het volgende voorbeeld wordt een virtuele machine gemaakt die als een webserver fungeert. Met de optie `-AsJob` wordt de virtuele machine op de achtergrond gemaakt, zodat u met de volgende stap kunt doorgaan:

```azurepowershell-interactive
# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

$webVmConfig = New-AzVMConfig `
  -VMName myVmWeb `
  -VMSize Standard_DS1_V2 | `
Set-AzVMOperatingSystem -Windows `
  -ComputerName myVmWeb `
  -Credential $cred | `
Set-AzVMSourceImage `
  -PublisherName MicrosoftWindowsServer `
  -Offer WindowsServer `
  -Skus 2016-Datacenter `
  -Version latest | `
Add-AzVMNetworkInterface `
  -Id $webNic.Id
New-AzVM `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -VM $webVmConfig `
  -AsJob
```

Maak een VM die fungeert als een beheerserver:

```azurepowershell-interactive
# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create the web server virtual machine configuration and virtual machine.
$mgmtVmConfig = New-AzVMConfig `
  -VMName myVmMgmt `
  -VMSize Standard_DS1_V2 | `
Set-AzVMOperatingSystem -Windows `
  -ComputerName myVmMgmt `
  -Credential $cred | `
Set-AzVMSourceImage `
  -PublisherName MicrosoftWindowsServer `
  -Offer WindowsServer `
  -Skus 2016-Datacenter `
  -Version latest | `
Add-AzVMNetworkInterface `
  -Id $mgmtNic.Id
New-AzVM `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -VM $mgmtVmConfig
```

Het maken van de virtuele machine duurt een paar minuten. Ga pas verder met de volgende stap als Azure klaar is met het maken van de virtuele machine.

## <a name="test-traffic-filters"></a>Verkeersfilters testen

Gebruik [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) om het openbare IP-adres van een virtuele machine op te halen. In het volgende voorbeeld wordt het openbare IP-adres van de VM *myVmMgmt* opgehaald:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Gebruik de volgende opdracht om een sessie met een extern bureaublad te starten met de VM *myVmMgmt* vanaf uw lokale computer. Vervang `<publicIpAddress>` door het IP-adres dat is geretourneerd met de vorige opdracht.

```
mstsc /v:<publicIpAddress>
```

Open het gedownloade RDP-bestand. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd.

Voer de gebruikersnaam en het wachtwoord in die u bij het maken van de VM hebt opgegeven (mogelijk moet u **Meer opties** en **Een ander account gebruiken** selecteren om de aanmeldingsgegevens op te geven die u hebt ingevoerd tijdens het maken van de virtuele machine) en selecteer **OK**. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Selecteer **Ja** om door te gaan met de verbinding.

De verbinding slaagt, omdat inkomend verkeer van internet naar de toepassingsbeveiligingsgroep *myAsgMgmtServers* waarin de netwerkinterface die is gekoppeld aan de VM *myVmMgmt* zich bevindt, is toegestaan voor poort 3389.

Gebruik de volgende opdracht voor het maken van een externe bureaubladverbinding met de VM *myVmWeb*, uit de VM *myVmMgmt*, met de volgende opdracht, vanuit PowerShell:

``` 
mstsc /v:myvmWeb
```

De verbinding slaagt omdat een standaardbeveiligingsregel binnen elke netwerkbeveiligingsgroep verkeer via alle poorten tussen alle IP-adressen binnen een virtueel netwerk toestaat. U kunt geen externe bureaubladverbinding tot stand brengen met de VM *myVmWeb* via internet omdat de beveiligingsregel voor de *myAsgWebServers* geen inkomend verkeer van internet toestaat via poort 3389.

Gebruik de volgende opdracht voor het installeren van Microsoft IIS op de VM *myVmWeb* vanuit PowerShell:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Nadat de installatie van IIS is voltooid, verbreekt u de verbinding met de VM *myVmWeb*, waarbij u de externe bureaubladverbinding met VM *myVmMgmt* behoudt. Als u het IIS-welkomstscherm, open een internetbrowser en bladert u naar http:\//myVmWeb.

Verbreek de verbinding met de VM *myVmMgmt*.

Voer op de computer de volgende opdracht vanuit PowerShell uit om het openbare IP-adres van de *myVmWeb*-server op te halen:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Om te bevestigen dat u toegang hebt tot de *myVmWeb*-webserver buiten Azure, opent u een internetbrowser op uw computer en bladert u naar `http://<public-ip-address-from-previous-step>`. De verbinding slaagt, omdat inkomend verkeer van internet naar de toepassingsbeveiligingsgroep *myAsgWebServers* waarin de netwerkinterface die is gekoppeld aan de VM *myVmWeb* zich bevindt, is toegestaan voor poort 80.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u niet meer nodig hebt, kunt u [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) om de resourcegroep en alle resources die deze bevat te verwijderen:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een netwerkbeveiligingsgroep gemaakt en die aan een subnet van een virtueel netwerk gekoppeld. Zie [Overzicht van netwerkbeveiligingsgroepen](security-overview.md) en [Een beveiligingsgroep beheren](manage-network-security-group.md) voor meer informatie over netwerkbeveiligingsgroepen.

Azure routeert standaard verkeer tussen subnetten. In plaats daarvan kunt u verkeer routeren tussen subnetten via een virtuele machine, die bijvoorbeeld als een firewall fungeert. Voor meer informatie Zie [een routetabel maken](tutorial-create-route-table-powershell.md).
