---
title: vCPU quota's voor Azure
description: Meer informatie over vCPU-quota's voor Azure.
keywords: ''
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: 20017f42031e126bbdae40f66bf4c36efd12f17c
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75893470"
---
# <a name="virtual-machine-vcpu-quotas"></a>vCPU-quota virtuele machines

De vCPU-quota's voor virtuele machines en virtuele-machine schaal sets worden gerangschikt in twee lagen voor elk abonnement, in elke regio. De eerste laag is het totale regionale Vcpu's en de tweede laag is de verschillende kernen van de VM-grootte, zoals de Vcpu's van de D-serie. Telkens wanneer een nieuwe virtuele machine wordt geïmplementeerd, mag de Vcpu's voor de virtuele machine niet groter zijn dan het vCPU quotum voor de VM-grootte familie of het totale regionale vCPU-quotum. Als een van deze quota wordt overschreden, is de implementatie van de VM niet toegestaan. Er is ook een quotum voor het totale aantal virtuele machines in de regio. De details van elk van deze quota's kunnen worden weer gegeven in de sectie **gebruik en quota's** van de pagina **abonnement** in de [Azure Portal](https://portal.azure.com), of u kunt een query uitvoeren voor de waarden met behulp van Power shell.

   
 
## <a name="check-usage"></a>Gebruik controleren

U kunt de cmdlet [Get-AzVMUsage](https://docs.microsoft.com/powershell/module/az.compute/get-azvmusage) gebruiken om het quota gebruik te controleren.

```azurepowershell-interactive
Get-AzVMUsage -Location "East US"
```

De uitvoer ziet er ongeveer als volgt uit:

```
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional vCPUs                         4   260 Count
Virtual Machines                             4 10000 Count
Virtual Machine Scale Sets                   1  2000 Count
Standard B Family vCPUs                      1    10 Count
Standard DSv2 Family vCPUs                   1   100 Count
Standard Dv2 Family vCPUs                    2   100 Count
Basic A Family vCPUs                         0   100 Count
Standard A0-A7 Family vCPUs                  0   250 Count
Standard A8-A11 Family vCPUs                 0   100 Count
Standard D Family vCPUs                      0   100 Count
Standard G Family vCPUs                      0   100 Count
Standard DS Family vCPUs                     0   100 Count
Standard GS Family vCPUs                     0   100 Count
Standard F Family vCPUs                      0   100 Count
Standard FS Family vCPUs                     0   100 Count
Standard NV Family vCPUs                     0    24 Count
Standard NC Family vCPUs                     0    48 Count
Standard H Family vCPUs                      0     8 Count
Standard Av2 Family vCPUs                    0   100 Count
Standard LS Family vCPUs                     0   100 Count
Standard Dv2 Promo Family vCPUs              0   100 Count
Standard DSv2 Promo Family vCPUs             0   100 Count
Standard MS Family vCPUs                     0     0 Count
Standard Dv3 Family vCPUs                    0   100 Count
Standard DSv3 Family vCPUs                   0   100 Count
Standard Ev3 Family vCPUs                    0   100 Count
Standard ESv3 Family vCPUs                   0   100 Count
Standard FSv2 Family vCPUs                   0   100 Count
Standard ND Family vCPUs                     0     0 Count
Standard NCv2 Family vCPUs                   0     0 Count
Standard NCv3 Family vCPUs                   0     0 Count
Standard LSv2 Family vCPUs                   0     0 Count
Standard Storage Managed Disks               2 10000 Count
Premium Storage Managed Disks                1 10000 Count
```


## <a name="reserved-vm-instances"></a>Gereserveerde VM-instanties
Gereserveerde VM-instanties, die zijn afgestemd op één abonnement zonder flexibiliteit voor VM-grootte, zullen een nieuw aspect toevoegen aan de vCPU quota's. Deze waarden beschrijven het aantal exemplaren van de opgegeven grootte dat in het abonnement moet worden geïmplementeerd. Ze werken als tijdelijke aanduiding in het quota systeem om ervoor te zorgen dat quota gereserveerd zijn om te zorgen dat gereserveerde VM-exemplaren in het abonnement kunnen worden geïmplementeerd. Als een specifiek abonnement bijvoorbeeld 10 Standard_D1 gereserveerde VM-exemplaren heeft, zijn de limieten voor het gebruik van Standard_D1 gereserveerde VM-instanties 10. Dit zorgt ervoor dat Azure ten minste 10 Vcpu's beschikbaar is in het totale regionale Vcpu's-quotum dat moet worden gebruikt voor Standard_D1-instanties en dat er ten minste 10 Vcpu's beschikbaar zijn in het vCPU quotum van de standaard D-serie om te worden gebruikt voor Standard_D1 exemplaren.

Als een quotum toename vereist is om één enkel abonnement RI te kopen, kunt u [een quotum verhoging aanvragen](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) voor uw abonnement.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure-abonnement en service limieten, quota's en beperkingen](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits?toc=/azure/billing/TOC.json)voor meer informatie over facturering en quota's.
