---
title: Open bare IPv6-adressen en adresbereiken reserveren in een virtueel Azure-netwerk
titlesuffix: Azure Virtual Network
description: Meer informatie over het reserveren van open bare IPv6-adressen en adresbereiken in een virtueel Azure-netwerk.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/15/2019
ms.author: kumud
ms.openlocfilehash: 76d1ba2717ac3c8ac8e86687ef1754a8790f3e4d
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595074"
---
# <a name="reserve-public-ipv6-address-prefix"></a>Voor voegsel van een openbaar IPv6-adres reserveren
Met IPv6 voor Azure Virtual Network (VNet) kunt u toepassingen in azure hosten met IPv6-en IPv4-connectiviteit, zowel binnen een virtueel netwerk als vanaf het internet. Naast het reserveren van afzonderlijke IPv6-adressen, kunt u aaneengesloten bereiken van Azure IPv6-adressen (IP-voor voegsel) voor uw gebruik behouden. In deze artikelen wordt beschreven hoe u open bare IPv6-IP-adressen en adresbereiken maakt met behulp van Azure PowerShell en CLI.

> [!Important]
> IPv6 voor Azure Virtual Network is momenteel beschikbaar als open bare preview. Deze preview wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. De reden hiervoor is dat bepaalde functies mogelijk niet worden ondersteund of beperkte mogelijkheden hebben. Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-single-reserved-ipv6-public-ip"></a>Een enkel gereserveerd IPv6-openbaar IP-adres maken

### <a name="using-azure-powershell"></a>Azure PowerShell gebruiken

U kunt een enkel gereserveerd (statisch) IPv6-openbaar IP-adres maken met behulp van Azure PowerShell met [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) als volgt:

```azurepowershell  
 $myOwnIPv6Address = New-AzPublicIpAddress `
 -name PIPv6_WestUS `
 -ResourceGroup MyRG `
 -Location "West US" `
 -Sku Standard `
 -allocationMethod static `
 -IpAddressVersion IPv6
 ```

### <a name="using-azure-cli"></a>Azure CLI gebruiken

 U kunt een enkel gereserveerd (statisch) IPv6 openbaar IP-adres maken Azure CLI met [AZ Network Public-IP Create](/cli/azure/network/public-ip) als volgt:
  
```azurecli
 az network public-ip create \
 --name dsPublicIP_v6 \
 --resource-group UpgradeInPlace_CLI_RG1 \
 --location WestUS \
 --sku Standard  \
 --allocation-method static  \
 --version IPv6
```

## <a name="create-a-reserved-ipv6-prefix-range"></a>Een gereserveerde IPv6-voor voegsel maken (bereik)

Als u een IPv6-voor voegsel wilt reserveren, voegt u de IP-adres groep van IPv6 toe aan dezelfde opdracht die wordt gebruikt voor het maken van IPv4-voor voegsels. Met de volgende opdrachten maakt u een voor voegsel van grootte/125 (8 IPv6-adressen).  

### <a name="using-azure-powershell"></a>Azure PowerShell gebruiken

U kunt als volgt een openbaar IPv6-adres maken met behulp van de Azure CLI met [AZ Network Public-IP Create](/powershell/module/az.network/new-azpublicipprefix) :
```azurepowershell  
 $myOwnIPv6Prefix = New-AzPublicIpPrefix `
 -name IPv6PrefixWestUS `
 -ResourceGroupName MyRG `
 -Location "West US" `
 -Sku Standard `
 -IpAddressVersion IPv6 `
 -PrefixLength 125
```

### <a name="using-azure-cli"></a>Azure CLI gebruiken

U kunt als volgt een openbaar IPv6-adres maken met behulp van Azure CLI:

```azurecli  
az network public-ip prefix create \
--name IPv6PrefixWestUS \
--resource-group MyRG \
--location WestUS \
--version IPv6 \
--length 125
```

## <a name="allocate-a-public-ip-address-from-a-reserved-ipv6-prefix"></a>Een openbaar IP-adres toewijzen vanuit een gereserveerd IPv6-voor voegsel

### <a name="using-azure-powershell"></a>Azure PowerShell gebruiken

 U maakt een statisch IPv6-openbaar IP-adres van een gereserveerd voor voegsel door het argument `-PublicIpPrefix` toe te voegen bij het maken van het open bare IP-adres met Azure PowerShell. In het volgende voor beeld wordt ervan uitgegaan dat er een voor voegsel is gemaakt en opgeslagen in een Power shell-variabele met de naam: *$MyOwnIPv 6prefix*.

```azurepowershell:  
 $MyIPv6PublicIPFromMyReservedPrefix = New-AzPublicIpAddress \
 -name PIPv6_fromPrefix `
 -ResourceGroup DsStdLb04 `
 -Location "West Central US" `
 -Sku Standard `
 -allocationMethod static `
 -IpAddressVersion IPv6 `
 -PublicIpPrefix $myOwnIPv6Prefix
```

### <a name="using-azure-cli"></a>Azure CLI gebruiken
 
In het volgende voor beeld wordt ervan uitgegaan dat er een voor voegsel is gemaakt en opgeslagen in een CLI-variabele met de naam: *IPv6PrefixWestUS*.

```azurecli 
az network public-ip create \
--name dsPublicIP_v6 \
--resource-group UpgradeInPlace_CLI_RG1 \
--location WestUS \
--sku Standard \
--allocation-method static \
--version IPv6 \
--public-ip-prefix  IPv6PrefixWestUS
```

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [IPv6-adres voorvoegsels](ipv6-public-ip-address-prefix.md).
- Meer informatie over [IPv6-adressen](ipv6-overview.md).
