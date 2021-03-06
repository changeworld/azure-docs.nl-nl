---
title: Service-updates voor de hostgroep voor Windows Virtual Desktop-Azure
description: Een groep met validatie-hosts maken om service-updates te bewaken voordat updates voor productie worden uitgevoerd.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/13/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f2b51213dfc6d7e55f76e78b92d12111f84736be
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/14/2020
ms.locfileid: "79365386"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>Zelf studie: een hostgroep maken om service-updates te valideren

Hostgroepen zijn een verzameling van een of meer identieke virtuele machines in Windows-Tenant omgevingen voor virtueel bureau blad. Voordat u hostgroepen in uw productie omgeving implementeert, raden we u ten zeerste aan een groep met validatie-hosts te maken. Updates worden eerst toegepast op groepen met validatie-hosts, zodat u service-updates kunt bewaken voordat deze naar uw productie omgeving worden uitgevouwen. Zonder een validatie groep detecteert u mogelijk geen wijzigingen die fouten introduceren. Dit kan leiden tot uitval tijd voor gebruikers in uw productie omgeving.

Om ervoor te zorgen dat uw apps samen werken met de nieuwste updates, moet de groep met validatie-hosts net zo vergelijkbaar zijn als de hostgroepen in uw productie omgeving. Gebruikers moeten net zo vaak verbinding maken met de groep met validatie-hosts als voor de hostgroep voor productie. Als u automatische tests voor uw hostgroep hebt, moet u geautomatiseerde tests voor de groep met validatie-hosts toevoegen.

U kunt problemen met de validatie hostgroep oplossen met behulp van [de diagnostische functie](diagnostics-role-service.md) of de Windows-procedure voor het [oplossen van problemen met virtueel bureau blad](troubleshoot-set-up-overview.md).

>[!NOTE]
> U wordt aangeraden de groep validatie-host te verlaten om alle toekomstige updates te testen.

Voordat u begint, moet u [de Power shell-module van Windows virtueel bureau blad downloaden en importeren](/powershell/windows-virtual-desktop/overview/), als u dat nog niet hebt gedaan. Daarna voert u de volgende cmdlet uit om u aan te melden bij uw account:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-your-host-pool"></a>Uw hostgroep maken

U kunt een hostgroep maken door de instructies in een van deze artikelen te volgen:
- [Zelf studie: een hostgroep maken met Azure Marketplace](create-host-pools-azure-marketplace.md)
- [Een hostgroep met een Azure Resource Manager-sjabloon maken](create-host-pools-arm-template.md)
- [Een hostgroep maken met Power shell](create-host-pools-powershell.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>Uw hostgroep definiëren als een groep met validatie-hosts

Voer de volgende Power shell-cmdlets uit om de nieuwe hostgroep te definiëren als een groep met validatie-hosts. Vervang de waarden tussen aanhalings tekens door de waarden die relevant zijn voor uw sessie:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Set-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool" -ValidationEnv $true
```

Voer de volgende Power shell-cmdlet uit om te controleren of de validatie-eigenschap is ingesteld. Vervang de waarden tussen aanhalings tekens door de waarden die relevant zijn voor uw sessie.

```powershell
Get-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool"
```

De resultaten van de cmdlet moeten er ongeveer als volgt uitzien:

```
    TenantName          : contoso 
    TenantGroupName     : Default Tenant Group
    HostPoolName        : contosoHostPool
    FriendlyName        :
    Description         :
    Persistent          : False 
    CustomRdpProperty    : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnv       : True
    Ring                :
```

## <a name="update-schedule"></a>Schema bijwerken

Service-updates gebeuren maandelijks. Als er sprake is van grote problemen, worden er op een meer frequente snelheid essentiële updates verstrekt.

## <a name="next-steps"></a>Volgende stappen

Nu u een groep met validatie-hosts hebt gemaakt, kunt u leren hoe u Azure Service Health gebruikt voor het bewaken van uw Windows-implementatie voor virtueel bureau blad. 

> [!div class="nextstepaction"]
> [Service waarschuwingen instellen](./set-up-service-alerts.md)
