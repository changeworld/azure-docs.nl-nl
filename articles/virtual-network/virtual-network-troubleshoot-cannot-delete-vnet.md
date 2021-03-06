---
title: Kan een virtueel netwerk in azure niet verwijderen | Microsoft Docs
description: Meer informatie over het oplossen van het probleem waarbij u een virtueel netwerk in azure niet kunt verwijderen.
services: virtual-network
documentationcenter: na
author: chadmath
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 2d427a8b40fcb537801ce76aae6bc32fcda3a307
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056925"
---
# <a name="troubleshooting-failed-to-delete-a-virtual-network-in-azure"></a>Meer Kan een virtueel netwerk in azure niet verwijderen

Er kunnen fouten optreden wanneer u probeert een virtueel netwerk te verwijderen in Microsoft Azure. Dit artikel bevat stappen voor probleem oplossing om dit probleem op te lossen. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-guidance"></a>Hulp bij het oplossen van problemen 

1. [Controleer of er een virtuele netwerk gateway in het virtuele netwerk wordt uitgevoerd](#check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network).
2. [Controleer of een toepassings gateway wordt uitgevoerd in het virtuele netwerk](#check-whether-an-application-gateway-is-running-in-the-virtual-network).
3. [Controleer of Azure Active Directory domein service is ingeschakeld in het virtuele netwerk](#check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network).
4. [Controleer of het virtuele netwerk is verbonden met een andere bron](#check-whether-the-virtual-network-is-connected-to-other-resource).
5. [Controleer of een virtuele machine nog steeds wordt uitgevoerd in het virtuele netwerk](#check-whether-a-virtual-machine-is-still-running-in-the-virtual-network).
6. [Controleer of het virtuele netwerk is vastgelopen in de migratie](#check-whether-the-virtual-network-is-stuck-in-migration).

## <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing

### <a name="check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network"></a>Controleren of een virtuele netwerk gateway wordt uitgevoerd in het virtuele netwerk

Als u het virtuele netwerk wilt verwijderen, moet u eerst de gateway van het virtuele netwerk verwijderen.

Voor klassieke virtuele netwerken gaat u naar de **overzichts** pagina van het klassieke virtuele netwerk in de Azure Portal. Als de gateway in het virtuele netwerk wordt uitgevoerd, ziet u in de sectie **VPN-verbindingen** het IP-adres van de gateway. 

![Controleren of de gateway wordt uitgevoerd](media/virtual-network-troubleshoot-cannot-delete-vnet/classic-gateway.png)

Voor virtuele netwerken gaat u naar de **overzichts** pagina van het virtuele netwerk. Controleer de **verbonden apparaten** voor de gateway van het virtuele netwerk.

![Het verbonden apparaat controleren](media/virtual-network-troubleshoot-cannot-delete-vnet/vnet-gateway.png)

Voordat u de gateway kunt verwijderen, moet u eerst alle **verbindings** objecten in de gateway verwijderen. 

### <a name="check-whether-an-application-gateway-is-running-in-the-virtual-network"></a>Controleren of een toepassings gateway wordt uitgevoerd in het virtuele netwerk

Ga naar de **overzichts** pagina van het virtuele netwerk. Controleer de **verbonden apparaten** voor de toepassings gateway.

![Het verbonden apparaat controleren](media/virtual-network-troubleshoot-cannot-delete-vnet/app-gateway.png)

Als er een toepassings gateway is, moet u deze verwijderen voordat u het virtuele netwerk kunt verwijderen.

### <a name="check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network"></a>Controleer of Azure Active Directory domein service is ingeschakeld in het virtuele netwerk

Als de Active Directory-domein-service is ingeschakeld en verbonden met het virtuele netwerk, kunt u dit virtuele netwerk niet verwijderen. 

![Het verbonden apparaat controleren](media/virtual-network-troubleshoot-cannot-delete-vnet/enable-domain-services.png)

Zie [Azure Active Directory Domain Services uitschakelen met behulp van de Azure Portal](../active-directory-domain-services/delete-aadds.md)om de service uit te scha kelen.

### <a name="check-whether-the-virtual-network-is-connected-to-other-resource"></a>Controleer of het virtuele netwerk is verbonden met een andere bron

Controleer op circuit koppelingen, verbindingen en peerings voor virtuele netwerken. Een van deze kan ertoe leiden dat het verwijderen van een virtueel netwerk mislukt. 

De aanbevolen verwijderings volgorde is als volgt:

1. Gateway verbindingen
2. Gateways
3. IP-adressen
4. Peerings voor virtuele netwerken
5. App Service Environment (ASE)

### <a name="check-whether-a-virtual-machine-is-still-running-in-the-virtual-network"></a>Controleren of een virtuele machine nog steeds wordt uitgevoerd in het virtuele netwerk

Zorg ervoor dat er geen virtuele machine in het virtuele netwerk is.

### <a name="check-whether-the-virtual-network-is-stuck-in-migration"></a>Controleer of het virtuele netwerk is vastgelopen in de migratie

Als het virtuele netwerk is vastgelopen in een migratie status, kan het niet worden verwijderd. Voer de volgende opdracht uit om de migratie af te breken en verwijder vervolgens het virtuele netwerk.

    Move-AzureVirtualNetwork -VirtualNetworkName "Name" -Abort

## <a name="next-steps"></a>Volgende stappen

- [Azure Virtual Network](virtual-networks-overview.md)
- [Veelgestelde vragen over virtuele Azure-netwerken (FAQ)](virtual-networks-faq.md)
