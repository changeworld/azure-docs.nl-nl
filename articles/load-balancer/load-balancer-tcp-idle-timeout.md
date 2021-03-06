---
title: load balancer TCP-time-out voor inactiviteit in azure configureren
titleSuffix: Azure Load Balancer
description: In dit artikel vindt u informatie over het configureren van Azure Load Balancer TCP-time-out voor inactiviteit.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/09/2020
ms.author: allensu
ms.openlocfilehash: 39cd5b5d6e9d6007994ccc29732186ec6a8bdc2e
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79284133"
---
# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>Instellingen voor time-out voor TCP-inactiviteit configureren voor Azure Load Balancer

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u PowerShell lokaal wilt installeren en gebruiken, is voor dit artikel versie 5.4.1 of hoger van de Azure PowerShell-module vereist. Voer `Get-Module -ListAvailable Az` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="tcp-idle-timeout"></a>Time-out voor TCP-inactiviteit
In de standaard configuratie heeft Azure Load Balancer een time-outinstellingen voor inactiviteit van vier minuten. Als een periode van inactiviteit langer is dan de time-outwaarde, is er geen garantie dat de TCP-of HTTP-sessie tussen de client en de Cloud service wordt bewaard.

Wanneer de verbinding wordt gesloten, kan uw client toepassing het volgende fout bericht ontvangen: ' de onderliggende verbinding is gesloten: er is een verbinding die naar verwachting wordt gehouden door de server verbroken. '

Een veelvoorkomende procedure is het gebruik van een TCP-Keep-Alive. Deze procedure houdt de verbinding gedurende een langere periode actief. Zie voor meer informatie deze [.net-voor beelden](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). Als Keep-Alive is ingeschakeld, worden pakketten verzonden tijdens peri Oden van inactiviteit op de verbinding. Keep-Alive-pakketten zorgen ervoor dat de time-outwaarde niet wordt bereikt en de verbinding gedurende een lange periode wordt bewaard.

De instelling werkt alleen voor binnenkomende verbindingen. Om te voor komen dat de verbinding wordt verbroken, configureert u de TCP Keep-Alive met een interval van minder dan de instelling voor niet-actieve time-out of verhoogt u de time-outwaarde. Ter ondersteuning van deze scenario's is ondersteuning voor een Configureer bare time-out voor inactiviteit toegevoegd. U kunt deze nu instellen voor een duur van 4 tot 30 minuten.

TCP Keep-Alive werkt voor scenario's waarbij de levens duur van de accu geen beperking is. Het wordt niet aanbevolen voor mobiele toepassingen. Het gebruik van een TCP-Keep-Alive in een mobiele toepassing kan de accu van het apparaat sneller leeg laten.

![TCP-time-out](./media/load-balancer-tcp-idle-timeout/image1.png)

In de volgende secties wordt beschreven hoe u time-outinstellingen voor inactiviteit wijzigt voor open bare IP-en load balancer-resources.

## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>De TCP-time-out voor uw open bare IP-adres op exemplaar niveau configureren tot 15 minuten

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

`IdleTimeoutInMinutes` is optioneel. Als deze niet is ingesteld, is de standaard time-out 4 minuten. Het bereik van de acceptabele time-out is 4 tot 30 minuten.

## <a name="set-the-tcp-timeout-on-a-load-balanced-rule-to-15-minutes"></a>Stel de TCP-time-out in op een regel met gelijke taak verdeling tot 15 minuten

Als u de time-out voor inactiviteit voor een load balancer wilt instellen, wordt de IdleTimeoutInMinutes ingesteld op de regel met gelijke taak verdeling. Bijvoorbeeld:

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name "MyLoadBalancer" -ResourceGroup "MyResourceGroup"
$lb | Set-AzLoadBalancerRuleConfig -Name myLBrule -IdleTimeoutInMinutes 15
```
## <a name="next-steps"></a>Volgende stappen

[Overzicht van interne load balancer](load-balancer-internal-overview.md)

[Aan de slag met het configureren van een Internet gerichte load balancer](quickstart-create-standard-load-balancer-powershell.md)

[Een distributiemodus voor de load balancer configureren](load-balancer-distribution-mode.md)
