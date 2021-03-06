---
title: Power shell-script-kluis voor opslag account zoeken
description: Meer informatie over het gebruik van een Azure PowerShell script om de Recovery Services-kluis te vinden waar uw opslag account is geregistreerd.
ms.topic: sample
ms.date: 1/28/2020
ms.openlocfilehash: 786420ec8cef6516f7261c71b40641693efece07
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76775359"
---
# <a name="powershell-script-to-find-the-recovery-services-vault-where-a-storage-account-is-registered"></a>Power shell-script voor het vinden van de Recovery Services kluis waar een opslag account is geregistreerd

Met dit script kunt u de Recovery Services-kluis vinden waar uw opslag account is geregistreerd.

## <a name="sample-script"></a>Voorbeeldscript

```powershell
Param(
        [Parameter(Mandatory=$True)][System.String] $ResourceGroupName,
        [Parameter(Mandatory=$True)][System.String] $StorageAccountName,
        [Parameter(Mandatory=$True)][System.String] $SubscriptionId
    )

Connect-AzAccount
Select-AzSubscription -Subscription $SubscriptionId
$vaults = Get-AzRecoveryServicesVault
$found = $false
foreach($vault in $vaults)
{
  Write-Verbose "Checking vault: $($vault.Id)" -Verbose
  
  $containers = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -FriendlyName $StorageAccountName -ResourceGroupName $ResourceGroupName -VaultId $vault.Id -Status Registered
  
  if($containers -ne $null)
  {
    $found = $True
    Write-Information "Found Storage account $StorageAccountName registered in vault: $($vault.Id)" -InformationAction Continue
    break;
  }
}

if(!$found)
{
     Write-Information "Storage account: $StorageAccountName is not registered in any vault of this subscription" -InformationAction Continue
}
```

## <a name="how-to-execute-the-script"></a>Het script uitvoeren

1. Sla het bovenstaande script op uw computer op met de naam van uw keuze. In dit voor beeld hebben we het opgeslagen als *FindRegisteredStorageAccount. ps1*.
2. Voer het script uit door de volgende para meters op te geven:

    * **-ResourceGroupName** : de resource groep van het opslag account
    * **-StorageAccountName** -naam van opslag account
    * **-SubscriptionID** -de id van het abonnement waarin het opslag account zich bevindt.

In het volgende voor beeld wordt gezocht naar de Recovery Services-kluis waar het *afsaccount* -opslag account is geregistreerd:

```powershell
.\FindRegisteredStorageAccount.ps1 -ResourceGroupName AzureFiles -StorageAccountName afsaccount -SubscriptionId ef4ad5a7-c2c0-4304-af80-af49f49af3d1
```

## <a name="output"></a>Uitvoer

De uitvoer geeft het volledige pad van de Recovery Services-kluis weer waarin het opslag account is geregistreerd. Hier volgt een voorbeeld van uitvoer:

```output
Found Storage account afsaccount registered in vault: /subscriptions/ ef4ad5a7-c2c0-4304-af80-af49f49af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault123
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het maken van back-ups van Azure-bestands shares via de Azure Portal](https://docs.microsoft.com/azure/backup/backup-afs)
