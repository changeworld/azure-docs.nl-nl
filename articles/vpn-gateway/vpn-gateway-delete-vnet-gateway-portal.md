---
title: 'Azure VPN Gateway: een gateway verwijderen: Portal'
description: Verwijder een virtuele netwerk gateway met behulp van de Azure Portal in het Resource Manager-implementatie model.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.date: 10/23/2018
ms.author: cherylmc
ms.topic: conceptual
ms.openlocfilehash: fba213be2de3b747d3ba962674a03c0bc7df3ed5
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75863662"
---
# <a name="delete-a-virtual-network-gateway-using-the-portal"></a>Een virtuele netwerk gateway verwijderen met behulp van de portal

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (klassiek)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)

Dit artikel bevat de instructies voor het verwijderen van een Azure VPN-gateway die is geïmplementeerd met het Resource Manager-implementatie model. Er zijn een aantal verschillende benaderingen die u kunt nemen wanneer u een virtuele netwerk gateway wilt verwijderen voor een configuratie van een VPN-gateway.

- Als u alles wilt verwijderen en opnieuw wilt beginnen, zoals in het geval van een test omgeving, kunt u de resource groep verwijderen. Wanneer u een resource groep verwijdert, worden alle resources in de groep verwijderd. Deze methode wordt alleen aanbevolen als u geen van de resources in de resource groep wilt blijven gebruiken. Het is niet mogelijk om slechts een paar resources te verwijderen met behulp van deze methode.

- Als u een aantal resources in uw resource groep wilt blijven gebruiken, wordt het verwijderen van een virtuele netwerk gateway iets gecompliceerder. Voordat u de gateway van het virtuele netwerk kunt verwijderen, moet u eerst alle resources verwijderen die afhankelijk zijn van de gateway. De stappen die u volgt, zijn afhankelijk van het type verbindingen dat u hebt gemaakt en de afhankelijke bronnen voor elke verbinding.

> [!IMPORTANT]
> In de onderstaande instructies wordt beschreven hoe u Azure VPN-gateways verwijdert die zijn geïmplementeerd met het Resource Manager-implementatie model. Als u een VPN-gateway wilt verwijderen die is geïmplementeerd met het klassieke implementatie model, gebruikt u Azure PowerShell zoals [hier](vpn-gateway-delete-vnet-gateway-classic-powershell.md)wordt beschreven.


## <a name="delete-a-vpn-gateway"></a>Een VPN-gateway verwijderen

Als u een virtuele netwerk gateway wilt verwijderen, moet u eerst elke resource verwijderen die betrekking heeft op de gateway van het virtuele netwerk. Resources moeten in een bepaalde volg orde worden verwijderd als gevolg van afhankelijkheden.

[!INCLUDE [delete gateway](../../includes/vpn-gateway-delete-vnet-gateway-portal-include.md)]

Op dit punt wordt de gateway van het virtuele netwerk verwijderd. Met de volgende stappen kunt u alle resources verwijderen die niet meer worden gebruikt.

### <a name="to-delete-the-local-network-gateway"></a>De lokale netwerk gateway verwijderen

1. Zoek in **alle bronnen**de lokale netwerk gateways die zijn gekoppeld aan elke verbinding.
2. Klik op de Blade **overzicht** voor de gateway van het lokale netwerk op **verwijderen**.

### <a name="to-delete-the-public-ip-address-resource-for-the-gateway"></a>De resource voor het open bare IP-adres voor de gateway verwijderen

1. Zoek in **alle bronnen**de open bare IP-adres resource die aan de gateway is gekoppeld. Als de gateway van het virtuele netwerk actief was, worden er twee open bare IP-adressen weer geven. 
2. Klik op de pagina **overzicht** voor het open bare IP-adres op **verwijderen**en vervolgens op **Ja** om te bevestigen.

### <a name="to-delete-the-gateway-subnet"></a>Het gateway-subnet verwijderen

1. Zoek het virtuele netwerk in **alle resources**. 
2. Klik op de Blade **subnetten** op de **GatewaySubnet**en klik vervolgens op **verwijderen**. 
3. Klik op **Ja** om te bevestigen dat u het gateway-subnet wilt verwijderen.

## <a name="deleterg"></a>Een VPN-gateway verwijderen door de resource groep te verwijderen

Als u zich geen zorgen maakt over het bewaren van uw resources in de resource groep en u alleen opnieuw wilt beginnen, kunt u een hele resource groep verwijderen. Dit is een snelle manier om alles te verwijderen. De volgende stappen zijn alleen van toepassing op het Resource Manager-implementatie model.

1. Zoek de resource groep in **alle resources**en klik op om de Blade te openen.
2. Klik op **Verwijderen**. Bekijk de betrokken resources op de Blade verwijderen. Zorg ervoor dat u al deze resources wilt verwijderen. Als dat niet het geval is, gebruikt u de stappen in een VPN-gateway verwijderen boven aan dit artikel.
3. Als u wilt door gaan, typt u de naam van de resource groep die u wilt verwijderen en klikt u vervolgens op **verwijderen**.
