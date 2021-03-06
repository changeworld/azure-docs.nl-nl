---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/12/2020
ms.author: dacoulte
ms.openlocfilehash: 4a6c0cad4a93d0d99573a348070823b909c75c7e
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299410"
---
|Naam |Beschrijving |Effect (s) |Version |GitHub |
|---|---|---|---|---|
|[Azure Backup moet zijn ingeschakeld voor Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Dit beleid helpt te controleren of Azure Backup-service is ingeschakeld voor alle virtuele machines. Azure Backup is een kosteneffectieve, back-upoplossing met één klik maakt gegevens herstel eenvoudiger en is eenvoudiger in te scha kelen dan andere Cloud back-upservices. |AuditIfNotExists, uitgeschakeld |1.0.0 |[Koppeling](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json)
|[Back-ups op Vm's van een locatie naar een bestaande centrale kluis op dezelfde locatie configureren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09ce66bc-1220-4153-8104-e3f51c936913) |Met dit beleid configureert u Azure Backup beveiliging op Vm's op een bepaalde locatie naar een bestaande centrale kluis op dezelfde locatie. Dit geldt alleen voor virtuele machines die nog niet zijn geconfigureerd voor back-up. Het is raadzaam dat dit beleid wordt toegewezen aan niet meer dan 200 Vm's. Als het beleid is toegewezen voor meer dan 200 Vm's, kan dit ertoe leiden dat de back-up enkele uren na het gedefinieerde schema wordt geactiveerd. Dit beleid wordt uitgebreid ter ondersteuning van meer VM-installatie kopieën. |deployIfNotExists, auditIfNotExists, uitgeschakeld |1.0.0 |[Koppeling](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineBackup_Backup_DeployIfNotExists.json)
