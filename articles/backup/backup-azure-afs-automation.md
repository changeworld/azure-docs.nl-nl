---
title: Back-up maken van Azure Files met Power shell
description: In dit artikel vindt u informatie over het maken van een back-up van Azure Files met behulp van de Azure Backup-service en Power shell.
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: f85451e0da6458de34aea936836b46781f4c4a21
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79273538"
---
# <a name="back-up-azure-files-with-powershell"></a>Back-up maken van Azure Files met Power shell

In dit artikel wordt beschreven hoe u Azure PowerShell kunt gebruiken om een back-up te maken van een Azure Files bestands share met behulp van een [Azure Backup](backup-overview.md) Recovery Services kluis.

In dit artikel wordt uitgelegd hoe u:

> [!div class="checklist"]
>
> * Stel Power shell in en registreer de Azure Recovery Services-provider.
> * Maak een Recovery Services-kluis.
> * Configureer de back-up voor een Azure-bestands share.
> * Een back-uptaak uitvoeren.

## <a name="before-you-start"></a>Voordat u begint

* [Meer informatie](backup-azure-recovery-services-vault-overview.md) over Recovery Services-kluizen.
* Meer informatie over de preview-mogelijkheden voor het [maken van back-ups van Azure-bestands shares](backup-afs.md).
* Controleer de Power shell-object hiërarchie voor Recovery Services.

## <a name="recovery-services-object-hierarchy"></a>Object hiërarchie Recovery Services

De object hiërarchie wordt in het volgende diagram samenvatten.

![Object hiërarchie Recovery Services](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Raadpleeg de naslag informatie **AZ. Recovery Services** [cmdlet](/powershell/module/az.recoveryservices) in de Azure-bibliotheek.

## <a name="set-up-and-install"></a>Instellen en installeren

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Stel Power shell als volgt in:

1. [Down load de nieuwste versie van AZ Power shell](/powershell/azure/install-az-ps). De mini maal vereiste versie is 1.0.0.

> [!WARNING]
> De mini maal vereiste versie van PS voor de preview is AZ 1.0.0. Als gevolg van aanstaande wijzigingen voor GA, is de mini maal vereiste PS-versie ' AZ. Recovery Services 2.6.0 '. Het is belang rijk dat u alle bestaande PS-versies bijwerkt naar deze versie. Als dat niet het geval is, worden de bestaande scripts na GA verbroken. Installeer de minimale versie met de volgende PS-opdrachten

```powershell
Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
```

2. Zoek de Azure Backup Power shell-cmdlets met de volgende opdracht:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Controleer de aliassen en cmdlets voor Azure Backup, Azure Site Recovery en de Recovery Services kluis worden weer gegeven. Hier volgt een voor beeld van wat u kunt zien. Het is geen volledige lijst met cmdlets.

    ![Lijst met Recovery Services-cmdlets](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Meld u aan bij uw Azure-account met **Connect-AzAccount**.
5. Op de webpagina die wordt weer gegeven, wordt u gevraagd uw account referenties in te voeren.

    * U kunt ook uw account referenties als een para meter in de cmdlet **Connect-AzAccount** met **-Credential**toevoegen.
    * Als u een CSP-partner bent die namens een Tenant werkt, geeft u de klant op als Tenant met behulp van hun tenantID of Tenant primaire domein naam. Een voor beeld is **Connect-AzAccount-Tenant** fabrikam.com.

6. Koppel het abonnement dat u wilt gebruiken met het account, omdat een account verschillende abonnementen kan hebben.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Als u Azure Backup voor de eerste keer gebruikt, gebruikt u de cmdlet **REGI ster-AzResourceProvider** om de Azure Recovery Services provider bij uw abonnement te registreren.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. Controleer of de providers zijn geregistreerd:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. Controleer in de uitvoer van de opdracht of **RegistrationState** is gewijzigd in **geregistreerd**. Als dat niet het geval is, voert u de cmdlet **REGI ster-AzResourceProvider** opnieuw uit.

## <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

De Recovery Services kluis is een resource manager-resource, dus u moet deze in een resource groep plaatsen. U kunt een bestaande resource groep gebruiken, maar u kunt ook een resource groep maken met de cmdlet **New-AzResourceGroup** . Wanneer u een resource groep maakt, geeft u de naam en de locatie voor de resource groep op.

Volg deze stappen om een Recovery Services kluis te maken.

1. Een kluis wordt in een resource groep geplaatst. Als u geen bestaande resource groep hebt, maakt u een nieuwe met de [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0). In dit voor beeld maken we een nieuwe resource groep in de regio vs-West.

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```

2. Gebruik de cmdlet [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) om de kluis te maken. Geef dezelfde locatie op als de kluis die voor de resource groep is gebruikt.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Geef het type redundantie op dat moet worden gebruikt voor de kluis opslag.

   * U kunt [lokaal redundante opslag](../storage/common/storage-redundancy-lrs.md) of [geografisch redundante opslag](../storage/common/storage-redundancy-grs.md)gebruiken.
   * In het volgende voor beeld wordt de optie **-BackupStorageRedundancy** ingesteld voor de[set-AzRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) cmd voor **testvault** ingesteld op **georedundant**.

     ```powershell
     $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
     Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
     ```

### <a name="view-the-vaults-in-a-subscription"></a>De kluizen in een abonnement weer geven

Als u alle kluizen in het abonnement wilt weer geven, gebruikt u [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0).

```powershell
Get-AzRecoveryServicesVault
```

De uitvoer ziet er ongeveer als volgt uit. Houd er rekening mee dat de bijbehorende resource groep en locatie worden gegeven.

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>De kluis context instellen

Sla het kluis object op in een variabele en stel de kluis context in.

* Voor veel Azure Backup-cmdlets is het Recovery Services kluis-object vereist als invoer, zodat het handig is om het kluis object op te slaan in een variabele.
* De context van de kluis is het type gegevens dat in de kluis wordt beveiligd. Stel deze in met [set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0). Nadat de context is ingesteld, is deze van toepassing op alle volgende cmdlets.

In het volgende voor beeld wordt de kluis context voor **testvault**ingesteld.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>De kluis-ID ophalen

We zijn van plan de kluis context instelling af te nemen volgens Azure PowerShell richt lijnen. In plaats daarvan kunt u de kluis-ID opslaan of ophalen, en deze door geven aan relevante opdrachten. Als u dus de kluis context niet hebt ingesteld of de opdracht wilt opgeven die moet worden uitgevoerd voor een bepaalde kluis, geeft u de kluis-ID als volgt door aan alle relevante opdracht:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultID $vaultID
```

## <a name="configure-a-backup-policy"></a>Een back-upbeleid configureren

Met een back-upbeleid kunt u het schema voor back-ups opgeven en bepalen hoe lang back-ups van herstel punten moeten worden bewaard:

* Een back-upbeleid is gekoppeld aan ten minste één Bewaar beleid. Een Bewaar beleid bepaalt hoe lang een herstel punt wordt bewaard voordat het wordt verwijderd.
* Bekijk de standaard retentie van het back-upbeleid met [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
* Bekijk het standaard schema voor back-upbeleid met [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0).
* U kunt de cmdlet [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) gebruiken om een nieuw back-upbeleid te maken. U het schema en de Bewaar beleidsobjecten invoert.

Standaard wordt een begin tijd gedefinieerd in het object plannings beleid. Gebruik het volgende voor beeld om de begin tijd te wijzigen in de gewenste start tijd. De gewenste start tijd moet ook in UTC zijn. In het onderstaande voor beeld wordt ervan uitgegaan dat de gewenste start tijd 01:00 uur UTC is voor dagelijkse back-ups.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> U moet de start tijd binnen 30 minuten meerdere keer opgeven. In het bovenstaande voor beeld kan de waarde alleen ' 01:00:00 ' of ' 02:30:00 ' zijn. De begin tijd mag niet ' 01:15:00 ' zijn

In het volgende voor beeld worden het plannings beleid en het Bewaar beleid opgeslagen in variabelen. Vervolgens worden deze variabelen gebruikt als para meters voor een nieuw beleid (**NewAFSPolicy**). **NewAFSPolicy** neemt dagelijks een back-up en behoudt deze 30 dagen.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

De uitvoer ziet er ongeveer als volgt uit.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2019 1:30:00 AM
```

## <a name="enable-backup"></a>Back-up inschakelen

Nadat u het back-upbeleid hebt gedefinieerd, kunt u de beveiliging van de Azure-bestands share inschakelen met behulp van het beleid.

### <a name="retrieve-a-backup-policy"></a>Een back-upbeleid ophalen

U haalt het relevante beleids object op met [Get-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0). Gebruik deze cmdlet om een specifiek beleid op te halen of om de beleids regels weer te geven die zijn gekoppeld aan een type werk belasting.

#### <a name="retrieve-a-policy-for-a-workload-type"></a>Een beleid voor een type werk belasting ophalen

In het volgende voor beeld worden beleids regels opgehaald voor het type werk belasting **Azure files**.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

De uitvoer ziet er ongeveer als volgt uit.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> De tijd zone van het veld **BackupTime** in Power shell is Universal Coordinated Time (UTC). Wanneer de back-uptijd wordt weer gegeven in de Azure Portal, wordt de tijd aangepast aan uw lokale tijd zone.

### <a name="retrieve-a-specific-policy"></a>Een specifiek beleid ophalen

Het volgende beleid haalt het back-upbeleid met de naam **dailyafs**.

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-backup-and-apply-policy"></a>Back-ups inschakelen en beleid Toep assen

Schakel de beveiliging in met [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). Nadat het beleid is gekoppeld aan de kluis, worden back-ups geactiveerd volgens het beleids schema.

In het volgende voor beeld wordt de beveiliging van de Azure-bestands share **testAzureFileShare** in het opslag account **testStorageAcct**ingeschakeld met het beleid **dailyafs**.

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

De opdracht wacht totdat de taak beveiliging configureren is voltooid en een vergelijk bare uitvoer geeft, zoals wordt weer gegeven.

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

## <a name="important-notice---backup-item-identification-for-afs-backups"></a>Belang rijke kennisgeving-back-upitem-id voor AFS-back-ups

Deze sectie bevat een overzicht van een belang rijke wijziging in de voor bereiding op AFS-back-ups.

Bij het inschakelen van de back-up voor AFS levert de gebruiker de beschrijvende bestands share naam van de klant op als de naam van de entiteit en wordt er een back-upitem gemaakt. De naam van het back-upitem is een unieke id die is gemaakt door Azure Backup service. Normaal gesp roken is de id een gebruiks vriendelijke naam. Maar om het belang rijk scenario van zacht verwijderen te verwerken, waarbij een bestands share kan worden verwijderd en een andere bestands share met dezelfde naam kan worden gemaakt, is de unieke identiteit van de Azure-bestands share nu een ID in plaats van beschrijvende naam van de klant. Als u de unieke identiteit/naam van elk item wilt weten, voert u de ```Get-AzRecoveryServicesBackupItem``` opdracht uit met de relevante filters voor backupManagementType en WorkloadType om alle relevante items op te halen en bekijkt u vervolgens het veld naam in het geretourneerde PS-object/-antwoord. Het wordt altijd aanbevolen om items in een lijst weer te geven en vervolgens hun unieke naam op te halen uit het veld naam in antwoord. Gebruik deze waarde om de items te filteren met de naam parameter. Gebruik anders de para meter FriendlyName om het item op te halen met de beschrijvende naam/id van de klant.

> [!WARNING]
> Zorg ervoor dat de PS-versie is bijgewerkt naar de minimale versie van AZ. Recovery Services 2.6.0 voor AFS-back-ups. Met deze versie is het filter ' FriendlyName ' beschikbaar voor de opdracht ```Get-AzRecoveryServicesBackupItem```. Geef de naam van de Azure-bestands share door aan de FriendlyName-para meter. Als u de naam van de Azure-bestands share doorgeeft aan de naam parameter, genereert deze versie een waarschuwing om deze beschrijvende naam door te geven aan de beschrijvende naam parameter. Als u deze minimale versie niet installeert, kan dit leiden tot een fout in bestaande scripts. Installeer de minimale versie van PS met de volgende opdracht.

```powershell
Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
```

## <a name="trigger-an-on-demand-backup"></a>Een back-up op aanvraag activeren

Gebruik [Backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem?view=azps-1.4.0) om een back-up op aanvraag uit te voeren voor een beveiligde Azure-bestands share.

1. Haal het opslag account op uit de container in de kluis die uw back-upgegevens bevat met [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Als u een back-uptaak wilt starten, haalt u informatie op over de Azure-bestands share met [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Voer een back-up op aanvraag uit met[Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem).

Voer de back-up op aanvraag als volgt uit:

```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -FriendlyName "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

De opdracht retourneert een taak met een ID die moet worden bijgehouden, zoals wordt weer gegeven in het volgende voor beeld.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

De moment opnamen van Azure-bestands shares worden gebruikt tijdens het maken van de back-ups, dus de taak wordt uitgevoerd op het moment dat de opdracht deze uitvoer retourneert.

### <a name="using-on-demand-backups-to-extend-retention"></a>Back-ups op aanvraag gebruiken om Bewaar perioden uit te breiden

Back-ups op aanvraag kunnen worden gebruikt om uw moment opnamen gedurende tien jaar te bewaren. U kunt planners gebruiken om Power shell-scripts op aanvraag uit te voeren met de gekozen Bewaar periode en zo elke week, maand of jaar moment opnamen te maken. Raadpleeg de [beperkingen van back-ups op aanvraag](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#how-many-on-demand-backups-can-i-take-per-file-share) met Azure backup bij het maken van reguliere moment opnamen.

Als u een voor beeld van scripts zoekt, kunt u het voorbeeld script in GitHub (<https://github.com/Azure-Samples/Use-PowerShell-for-long-term-retention-of-Azure-Files-Backup>) raadplegen met Azure Automation runbook waarmee u periodiek back-ups kunt plannen en ze zelfs tot wel tien jaar behoudt.

> [!WARNING]
> Zorg ervoor dat de PS-versie is bijgewerkt naar de minimale versie van AZ. Recovery Services 2.6.0 voor AFS-back-ups in uw Automation-runbooks. U moet de oude ' AzureRM-module vervangen door de module AZ. Met deze versie is het filter ' FriendlyName ' beschikbaar voor de opdracht ```Get-AzRecoveryServicesBackupItem```. Geef de naam van de Azure-bestands share door aan de FriendlyName-para meter. Als u de naam van de Azure-bestands share doorgeeft aan de naam parameter, genereert deze versie een waarschuwing om deze beschrijvende naam door te geven aan de beschrijvende naam parameter.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over](backup-afs.md) het maken van een back-up van Azure files in de Azure Portal.
