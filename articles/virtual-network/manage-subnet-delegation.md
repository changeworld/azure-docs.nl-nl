---
title: Een subnet delegering toevoegen aan of verwijderen uit een virtueel Azure-netwerk
titlesuffix: Azure Virtual Network
description: Meer informatie over het toevoegen of verwijderen van een gedelegeerd subnet voor een service in Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2019
ms.author: kumud
ms.openlocfilehash: 6f767abdf8673e3adffc6c4e3748733054ba723d
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201863"
---
# <a name="add-or-remove-a-subnet-delegation"></a>Een subnet delegering toevoegen of verwijderen

Subnet delegering geeft expliciete machtigingen voor de service om servicespecifieke bronnen in het subnet te maken met behulp van een unieke id bij het implementeren van de service. In dit artikel wordt beschreven hoe u een gedelegeerd subnet toevoegt of verwijdert voor een Azure-service.

## <a name="portal"></a>Portal

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

### <a name="create-the-virtual-network"></a>Het virtuele netwerk maken

In deze sectie maakt u een virtueel netwerk en het subnet dat u later delegeert aan een Azure-service.

1. Selecteer linksboven in het scherm **Een resource maken** > **Netwerken** > **Virtueel netwerk**.
1. Typ of selecteer in **Virtueel netwerk maken** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Naam | Voer *MyVirtualNetwork*in. |
    | Adresruimte | Voer *10.0.0.0/16* in. |
    | Abonnement | Selecteer uw abonnement.|
    | Resourcegroep | Selecteer **Nieuwe maken**, voer *myResourceGroup* in en selecteer vervolgens **OK**. |
    | Locatie | Selecteer **oostus**.|
    | Subnet - naam | Voer *mySubnet*in. |
    | Subnet - adresbereik | Voer *10.0.0.0/24* in. |
    |||
1. Laat de rest als standaard staan en selecteer vervolgens **maken**.

### <a name="permissions"></a>Machtigingen

Als u het subnet dat u wilt delegeren aan een Azure-service niet hebt gemaakt, hebt u de volgende machtiging nodig: `Microsoft.Network/virtualNetworks/subnets/write`.

De ingebouwde rol [Network Inzender](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) bevat ook de benodigde machtigingen.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Een subnet overdragen aan een Azure-service

In deze sectie delegeert u het subnet dat u in de voor gaande sectie hebt gemaakt, naar een Azure-service.

1. Voer in de zoekbalk van de portal *myVirtualNetwork* in. Wanneer **myVirtualNetwork** wordt weergegeven in de zoekresultaten, selecteert u dit.
2. Selecteer *myVirtualNetwork*in de zoek resultaten.
3. Selecteer **subnetten**onder **instellingen**en selecteer vervolgens **mySubnet**.
4. Selecteer op de pagina *mySubnet* voor de lijst **subnet delegering** een van de services die worden vermeld onder **subnet delegeren aan een service** (bijvoorbeeld **micro soft. DBforPostgreSQL/serversv2**).  

### <a name="remove-subnet-delegation-from-an-azure-service"></a>Subnet delegering verwijderen van een Azure-service

1. Voer in de zoekbalk van de portal *myVirtualNetwork* in. Wanneer **myVirtualNetwork** wordt weergegeven in de zoekresultaten, selecteert u dit.
2. Selecteer *myVirtualNetwork*in de zoek resultaten.
3. Selecteer **subnetten**onder **instellingen**en selecteer vervolgens **mySubnet**.
4. Selecteer op de pagina *mySubnet* voor de lijst **subnet delegering** de optie **geen** van de services die worden vermeld onder **subnet overdragen aan een service**. 

## <a name="azure-cli"></a>Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om Azure CLI lokaal te installeren en te gebruiken, moet u voor dit artikel gebruikmaken van Azure CLI versie 2.0.28 of hoger. Voer `az --version` uit om na te gaan welke versie er is geïnstalleerd. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) voor installatie- of upgrade-informatie.

### <a name="create-a-resource-group"></a>Een resourcegroep maken
Maak een resourcegroep maken met [az group create](https://docs.microsoft.com/cli/azure/group). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

In het volgende voorbeeld wordt een resourcegroep met de naam **myResourceGroup** gemaakt op de locatie **eastus**:

```azurecli-interactive

  az group create \
    --name myResourceGroup \
    --location eastus

```

### <a name="create-a-virtual-network"></a>Een virtueel netwerk maken
Maak met **az network vnet create** een virtueel netwerk met de naam **myVnet** met een subnet met de naam **mySubnet** in [myResourceGroup](https://docs.microsoft.com/cli/azure/network/vnet).

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myVnet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 10.0.0.0/24
```
### <a name="permissions"></a>Machtigingen

Als u het subnet dat u wilt delegeren aan een Azure-service niet hebt gemaakt, hebt u de volgende machtiging nodig: `Microsoft.Network/virtualNetworks/subnets/write`.

De ingebouwde rol [Network Inzender](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) bevat ook de benodigde machtigingen.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Een subnet overdragen aan een Azure-service

In deze sectie delegeert u het subnet dat u in de voor gaande sectie hebt gemaakt, naar een Azure-service. 

Gebruik [AZ Network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update) om het subnet met de naam **mySubnet** bij te werken met een overdracht naar een Azure-service.  In dit voor beeld wordt **micro soft. DBforPostgreSQL/serversv2** gebruikt voor het voor beeld van de overdracht:

```azurecli-interactive
  az network vnet subnet update \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --delegations Microsoft.DBforPostgreSQL/serversv2
```

Gebruik [AZ Network vnet subnet show](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-show)om te controleren of de overdracht is toegepast. Controleer of de service wordt overgedragen aan het subnet onder de eigenschap **service**naam:

```azurecli-interactive
  az network vnet subnet show \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --query delegations
```

```json
[
  {
    "actions": [
      "Microsoft.Network/virtualNetworks/subnets/join/action"
    ],
    "etag": "W/\"8a8bf16a-38cf-409f-9434-fe3b5ab9ae54\"",
    "id": "/subscriptions/3bf09329-ca61-4fee-88cb-7e30b9ee305b/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet/delegations/0",
    "name": "0",
    "provisioningState": "Succeeded",
    "resourceGroup": "myResourceGroup",
    "serviceName": "Microsoft.DBforPostgreSQL/serversv2",
    "type": "Microsoft.Network/virtualNetworks/subnets/delegations"
  }
]
```

### <a name="remove-subnet-delegation-from-an-azure-service"></a>Subnet delegering verwijderen van een Azure-service

Gebruik [AZ Network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update) om de overdracht te verwijderen uit het subnet met de naam **mySubnet**:

```azurecli-interactive
  az network vnet subnet update \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --remove delegations
```
Gebruik [AZ Network vnet subnet show](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-show)om te controleren of de overdracht is verwijderd. Controleer of de service is verwijderd uit het subnet onder de eigenschap **service**naam:

```azurecli-interactive
  az network vnet subnet show \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --query delegations
```
De uitvoer van de opdracht is een lege beugel:
```json
[]
```

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="connect-to-azure"></a>Verbinding maken met Azure

```azurepowershell-interactive
  Connect-AzAccount
```

### <a name="create-a-resource-group"></a>Een resourcegroep maken
Maak een resourcegroep met behulp van de opdracht [New-AzResourceGroup](https://docs.microsoft.com/cli/azure/group). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *eastus*:

```azurepowershell-interactive
  New-AzResourceGroup -Name myResourceGroup -Location eastus
```
### <a name="create-virtual-network"></a>Virtueel netwerk maken

Maak een virtueel netwerk met de naam **myVnet** met een subnet met de naam **mySubnet** met behulp van [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) in de **MyResourceGroup** met behulp van [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest). De IP-adres ruimte voor het virtuele netwerk is **10.0.0.0/16**. Het subnet in het virtuele netwerk is **10.0.0.0/24**.  

```azurepowershell-interactive
  $subnet = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix "10.0.0.0/24"

  New-AzVirtualNetwork -Name myVnet -ResourceGroupName myResourceGroup -Location eastus -AddressPrefix "10.0.0.0/16" -Subnet $subnet
```
### <a name="permissions"></a>Machtigingen

Als u het subnet dat u wilt delegeren aan een Azure-service niet hebt gemaakt, hebt u de volgende machtiging nodig: `Microsoft.Network/virtualNetworks/subnets/write`.

De ingebouwde rol [Network Inzender](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) bevat ook de benodigde machtigingen.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Een subnet overdragen aan een Azure-service

In deze sectie delegeert u het subnet dat u in de voor gaande sectie hebt gemaakt, naar een Azure-service. 

Gebruik [add-AzDelegation](https://docs.microsoft.com/powershell/module/az.network/add-azdelegation?view=latest) om het subnet met de naam **mySubnet** bij te werken met een delegering met de naam **myDelegation** naar een Azure-service.  In dit voor beeld wordt **micro soft. DBforPostgreSQL/serversv2** gebruikt voor het voor beeld van de overdracht:

```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Add-AzDelegation -Name "myDelegation" -ServiceName "Microsoft.DBforPostgreSQL/serversv2" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
Gebruik [Get-AzDelegation](https://docs.microsoft.com/powershell/module/az.network/get-azdelegation?view=latest) om de overdracht te controleren:

```azurepowershell-interactive
  $subnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup" | Get-AzVirtualNetworkSubnetConfig -Name "mySubnet"
  Get-AzDelegation -Name "myDelegation" -Subnet $subnet

  ProvisioningState : Succeeded
  ServiceName       : Microsoft.DBforPostgreSQL/serversv2
  Actions           : {Microsoft.Network/virtualNetworks/subnets/join/action}
  Name              : myDelegation
  Etag              : W/"9cba4b0e-2ceb-444b-b553-454f8da07d8a"
  Id                : /subscriptions/3bf09329-ca61-4fee-88cb-7e30b9ee305b/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet/delegations/myDelegation

```
### <a name="remove-subnet-delegation-from-an-azure-service"></a>Subnet delegering verwijderen van een Azure-service

Gebruik [Remove-AzDelegation](https://docs.microsoft.com/powershell/module/az.network/remove-azdelegation?view=latest) om de overdracht te verwijderen uit het subnet met de naam **mySubnet**:

```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Remove-AzDelegation -Name "myDelegation" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
Gebruik [Get-AzDelegation](https://docs.microsoft.com/powershell/module/az.network/get-azdelegation?view=latest) om te controleren of de overdracht is verwijderd:

```azurepowershell-interactive
  $subnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup" | Get-AzVirtualNetworkSubnetConfig -Name "mySubnet"
  Get-AzDelegation -Name "myDelegation" -Subnet $subnet

  Get-AzDelegation: Sequence contains no matching element

```

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het [beheren van subnetten in azure](virtual-network-manage-subnet.md).
