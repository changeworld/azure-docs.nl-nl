---
title: Azure Disk Encryption scenario's op Windows-Vm's
description: In dit artikel vindt u instructies voor het inschakelen van Microsoft Azure schijf versleuteling voor Windows-Vm's voor verschillende scenario's
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: b4795eeb24d1d0ac373a700a6b60b8facec0e37d
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73064009"
---
# <a name="azure-disk-encryption-scenarios-on-windows-vms"></a>Azure Disk Encryption scenario's op Windows-Vm's

Azure Disk Encryption gebruikt de externe sleutel beveiliging van BitLocker om volume versleuteling te bieden voor het besturings systeem en de gegevens schijven van Azure virtual machines (Vm's), en is geïntegreerd met Azure Key Vault om u te helpen de coderings sleutels en geheimen van de schijf te beheren. Zie [Azure Disk Encryption voor Windows-vm's](disk-encryption-overview.md)voor een overzicht van de service.

Er zijn veel schijf versleutelings scenario's en de stappen kunnen variëren afhankelijk van het scenario. In de volgende secties worden de scenario's met meer Details voor Windows-Vm's besproken.

U kunt alleen schijf versleuteling Toep assen op virtuele machines met [ondersteunde VM-grootten en-besturings systemen](disk-encryption-overview.md#supported-vms-and-operating-systems). U moet ook aan de volgende vereisten voldoen:

- [Netwerk vereisten](disk-encryption-overview.md#networking-requirements)
- [groepsbeleid vereisten](disk-encryption-overview.md#group-policy-requirements)
- [Opslag vereisten voor de versleutelings sleutel](disk-encryption-overview.md#encryption-key-storage-requirements)

>[!IMPORTANT]
> - Als u eerder Azure Disk Encryption met Azure AD hebt gebruikt om een virtuele machine te versleutelen, moet u deze optie blijven gebruiken om uw virtuele machine te versleutelen. Zie [Azure Disk Encryption met Azure AD (vorige versie)](disk-encryption-overview-aad.md) voor meer informatie. 
>
> - U moet [een moment opname nemen](snapshot-copy-managed-disk.md) en/of een back-up maken voordat schijven worden versleuteld. Back-ups zorgen ervoor dat een herstel optie mogelijk is als er een onverwachte fout optreedt tijdens het versleutelen. Voor Vm's met Managed disks is een back-up vereist voordat versleuteling wordt uitgevoerd. Als er een back-up is gemaakt, kunt u de [cmdlet Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) gebruiken om beheerde schijven te versleutelen door de para meter-skipVmBackup op te geven. Zie [back-up en herstel van versleutelde virtuele Azure](../../backup/backup-azure-vms-encryption.md)-machines voor meer informatie over het maken van back-ups en het herstellen van versleutelde vm's. 
>
> - Het versleutelen of uitschakelen van versleuteling kan ertoe leiden dat een virtuele machine opnieuw wordt opgestart.

## <a name="install-tools-and-connect-to-azure"></a>Hulpprogram ma's installeren en verbinding maken met Azure

[!INCLUDE [disk-encryption-install-cli-powershell](../../../includes/disk-encryption-install-cli-powershell.md)]

## <a name="enable-encryption-on-an-existing-or-running-windows-vm"></a>Versleuteling inschakelen op een bestaande of actieve Windows-VM
In dit scenario kunt u versleuteling inschakelen met behulp van de Resource Manager-sjabloon, Power shell-cmdlets of CLI-opdrachten. Als u schema-informatie voor de extensie van de virtuele machine nodig hebt, raadpleegt u het artikel [Azure Disk Encryption voor Windows-extensie](../extensions/azure-disk-enc-windows.md) .

## <a name="enable-encryption-on-existing-or-running-iaas-windows-vms"></a>Versleuteling inschakelen op bestaande of actieve Windows-Vm's met IaaS
U kunt versleuteling inschakelen met behulp van een sjabloon, Power shell-cmdlets of CLI-opdrachten. Als u schema-informatie voor de extensie van de virtuele machine nodig hebt, raadpleegt u het artikel [Azure Disk Encryption voor Windows-extensie](../extensions/azure-disk-enc-windows.md) .

### <a name="enable-encryption-on-existing-or-running-vms-with-azure-powershell"></a>Versleuteling inschakelen op bestaande of actieve Vm's met Azure PowerShell 
Gebruik de cmdlet [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) om versleuteling in te scha kelen op een actieve IaaS-virtuele machine in Azure. 

-  **Een actieve VM versleutelen:** Het onderstaande script initialiseert uw variabelen en voert de cmdlet Set-AzVMDiskEncryptionExtension uit. De resource groep, de virtuele machine en de sleutel kluis moeten al zijn gemaakt als vereisten. Vervang MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM en MySecureVault door uw waarden.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **Een actieve VM versleutelen met behulp van KEK:** 

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = 'MyExtraSecureVM';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```
     
   >[!NOTE]
   > De syntaxis voor de waarde van de para meter voor schijf versleuteling-sleutel kluis is de volledige id-teken reeks:/Subscriptions/[abonnement-ID-GUID]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[sleutel kluis-naam]</br> De syntaxis voor de waarde van de para meter Key-Encryption-Key is de volledige URI naar de KEK zoals in: https://[sleutel kluis-name]. kluis. Azure. net/Keys/[kekname]/[Kek-unique-id] 

- **Controleer of de schijven zijn versleuteld:** Gebruik de cmdlet [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) om de versleutelings status van een IaaS-VM te controleren. 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Schijf versleuteling uitschakelen:** Als u de versleuteling wilt uitschakelen, gebruikt u de cmdlet [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . Versleuteling van de gegevens schijf op een Windows-VM uitschakelen wanneer zowel het besturings systeem als de gegevens schijven zijn versleuteld, werkt niet zoals verwacht. Schakel versleuteling uit op alle schijven.

     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-existing-or-running-vms-with-the-azure-cli"></a>Versleuteling inschakelen op bestaande of actieve Vm's met de Azure CLI
Gebruik de opdracht [AZ VM Encryption Enable](/cli/azure/vm/encryption#az-vm-encryption-enable) om versleuteling in te scha kelen op een actieve IaaS virtuele machine in Azure.

- **Een actieve VM versleutelen:**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **Een actieve VM versleutelen met behulp van KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > De syntaxis voor de waarde van de para meter voor schijf versleuteling-sleutel kluis is de volledige id-teken reeks:/Subscriptions/[abonnement-ID-GUID]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[sleutel kluis-naam] </br> De syntaxis voor de waarde van de para meter Key-Encryption-Key is de volledige URI naar de KEK zoals in: https://[sleutel kluis-name]. kluis. Azure. net/Keys/[kekname]/[Kek-unique-id] 

- **Controleer of de schijven zijn versleuteld:** Als u de versleutelings status van een IaaS-VM wilt controleren, gebruikt u de opdracht [AZ VM Encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) . 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Versleuteling uitschakelen:** Als u versleuteling wilt uitschakelen, gebruikt u de opdracht [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . Versleuteling van de gegevens schijf op een Windows-VM uitschakelen wanneer zowel het besturings systeem als de gegevens schijven zijn versleuteld, werkt niet zoals verwacht. Schakel versleuteling uit op alle schijven.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```

### <a name="using-the-resource-manager-template"></a>De Resource Manager-sjabloon gebruiken

U kunt schijf versleuteling inschakelen op bestaande of actieve IaaS Windows-Vm's in azure met behulp van de [Resource Manager-sjabloon om een actieve Windows-VM te versleutelen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad).


1. Klik in de Azure Quick Start-sjabloon op **implementeren naar Azure**.

2. Selecteer het abonnement, de resource groep, de locatie, de instellingen, de juridische voor waarden en de overeenkomst. Klik op **aanschaffen** om versleuteling in te scha kelen op de bestaande of actieve IaaS-VM.

De volgende tabel bevat de para meters voor de Resource Manager-sjabloon voor bestaande of actieve Vm's:

| Parameter | Beschrijving |
| --- | --- |
| vmName | De naam van de virtuele machine om de versleutelings bewerking uit te voeren. |
| keyVaultName | De naam van de sleutel kluis waarnaar de BitLocker-sleutel moet worden geüpload. U kunt deze ophalen met behulp van de cmdlet `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` of de Azure CLI-opdracht `az keyvault list --resource-group "MyKeyVaultResourceGroup"`|
| keyVaultResourceGroup | De naam van de resource groep die de sleutel kluis bevat|
|  keyEncryptionKeyURL | De URL van de Key Encryption Key, in de indeling https://&lt;&gt;naam van de vault.azure.net/key/.&lt;sleutel naam&gt;. Als u geen KEK wilt gebruiken, laat u dit veld leeg. |
| volumeType | Type volume waarop de versleutelings bewerking wordt uitgevoerd. Geldige waarden zijn _besturings systeem_, _gegevens_en _alle_. 
| Updatetag | Geef een unieke waarde op als een GUID elke keer dat de bewerking geforceerd moet worden uitgevoerd. |
| resizeOSDisk | Moet de grootte van het besturings systeem worden gewijzigd om een volledige besturingssysteem-VHD te maken voordat het systeem volume wordt gesplitst. |
| location | Locatie voor alle resources. |


## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Nieuwe IaaS-Vm's gemaakt op basis van door de klant versleutelde VHD-en versleutelings sleutels

In dit scenario kunt u een nieuwe virtuele machine maken op basis van een vooraf versleutelde VHD en de bijbehorende versleutelings sleutels met behulp van Power shell-cmdlets of CLI-opdrachten. 

Volg de instructies in [een vooraf versleutelde Windows-VHD voorbereiden](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd). Nadat de installatie kopie is gemaakt, kunt u de stappen in de volgende sectie gebruiken om een versleutelde Azure-VM te maken.


### <a name="encrypt-vms-with-pre-encrypted-vhds-with-azure-powershell"></a>Virtuele machines met vooraf versleutelde Vhd's versleutelen met Azure PowerShell
U kunt schijf versleuteling op uw versleutelde VHD inschakelen met behulp van de Power shell [-cmdlet Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples). In het onderstaande voor beeld vindt u enkele algemene para meters. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Versleuteling inschakelen op een nieuw toegevoegde gegevens schijf
U kunt [een nieuwe schijf toevoegen aan een Windows-VM met behulp van Power shell](attach-disk-ps.md)of [via de Azure Portal](attach-managed-disk-portal.md). 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Versleuteling inschakelen op een nieuw toegevoegde schijf met Azure PowerShell
 Wanneer u Power shell gebruikt om een nieuwe schijf voor Windows-Vm's te versleutelen, moet u een nieuwe reeks versie opgeven. De reeks versie moet uniek zijn. In het onderstaande script wordt een GUID voor de reeks versie gegenereerd. In sommige gevallen kan een nieuw toegevoegde gegevens schijf automatisch worden versleuteld door de extensie Azure Disk Encryption. Automatische versleuteling vindt doorgaans plaats wanneer de virtuele machine opnieuw wordt opgestart nadat de nieuwe schijf online is. Dit wordt meestal veroorzaakt doordat ' all ' is opgegeven voor het volume type wanneer schijf versleuteling eerder is uitgevoerd op de virtuele machine. Als Automatische versleuteling op een nieuw toegevoegde gegevens schijf plaatsvindt, raden we u aan om de cmdlet Set-AzVmDiskEncryptionExtension opnieuw uit te voeren met een nieuwe reeks versie. Als uw nieuwe gegevens schijf automatisch versleuteld is en u niet wilt versleutelen, moet u eerst alle stations ontsleutelen en vervolgens opnieuw versleutelen met een nieuwe reeks versie die het besturings systeem specificeert voor het volume type. 
  
 

-  **Een actieve VM versleutelen:** Het onderstaande script initialiseert uw variabelen en voert de cmdlet Set-AzVMDiskEncryptionExtension uit. De resource groep, de virtuele machine en de sleutel kluis moeten al zijn gemaakt als vereisten. Vervang MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM en MySecureVault door uw waarden. In dit voor beeld wordt ' all ' gebruikt voor de para meter-VolumeType, waarin zowel het besturings systeem als de gegevens volumes zijn opgenomen. Als u alleen het volume van het besturings systeem wilt versleutelen, gebruikt u ' OS ' voor de para meter-VolumeType. 

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;
    ```
- **Een actieve VM versleutelen met behulp van KEK:** In dit voor beeld wordt ' all ' gebruikt voor de para meter-VolumeType, waarin zowel het besturings systeem als de gegevens volumes zijn opgenomen. Als u alleen het volume van het besturings systeem wilt versleutelen, gebruikt u ' OS ' voor de para meter-VolumeType.

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = 'MyExtraSecureVM';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     $sequenceVersion = [Guid]::NewGuid();

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;

     ```

    >[!NOTE]
    > De syntaxis voor de waarde van de para meter voor schijf versleuteling-sleutel kluis is de volledige id-teken reeks:/Subscriptions/[abonnement-ID-GUID]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[sleutel kluis-naam]</br> De syntaxis voor de waarde van de para meter Key-Encryption-Key is de volledige URI naar de KEK zoals in: https://[sleutel kluis-name]. kluis. Azure. net/Keys/[kekname]/[Kek-unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Versleuteling inschakelen op een nieuw toegevoegde schijf met Azure CLI
 De Azure CLI-opdracht geeft automatisch een nieuwe reeks versie voor u wanneer u de opdracht uitvoert om versleuteling in te scha kelen. In het voor beeld wordt ' all ' gebruikt voor de volume-type parameter. U moet mogelijk de volume-type parameter wijzigen in besturings systeem als u alleen de besturingssysteem schijf versleutelt. In tegens telling tot de Power shell-syntaxis vereist de CLI niet dat de gebruiker een unieke volgorde versie moet opgeven bij het inschakelen van versleuteling. De CLI genereert en gebruikt automatisch een eigen unieke sequentie versie waarde.   

-  **Een actieve VM versleutelen:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Een actieve VM versleutelen met behulp van KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "All"
     ```


## <a name="disable-encryption"></a>Versleuteling uitschakelen
U kunt versleuteling uitschakelen met behulp van Azure PowerShell, de Azure CLI of met een resource manager-sjabloon. Versleuteling van de gegevens schijf op een Windows-VM uitschakelen wanneer zowel het besturings systeem als de gegevens schijven zijn versleuteld, werkt niet zoals verwacht. Schakel versleuteling uit op alle schijven.

- **Schijf versleuteling uitschakelen met Azure PowerShell:** Als u de versleuteling wilt uitschakelen, gebruikt u de cmdlet [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' -VolumeType "all"
     ```

- **Versleuteling uitschakelen met Azure cli:** Als u versleuteling wilt uitschakelen, gebruikt u de opdracht [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "all"
     ```
- **Versleuteling uitschakelen met een resource manager-sjabloon:** 

    1. Klik op **implementeren naar Azure** van de sjabloon [voor het uitvoeren van schijf versleuteling op een Windows-VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad) .
    2. Selecteer het abonnement, de resource groep, de locatie, de VM, het volume type, de juridische voor waarden en de overeenkomst.
    3.  Klik op **aanschaffen** om schijf versleuteling uit te scha kelen op een actieve Windows-VM. 

## <a name="unsupported-scenarios"></a>Niet-ondersteunde scenario's

Azure Disk Encryption werkt niet voor de volgende scenario's, functies en technologie:

- Het versleutelen van de virtuele machine van de Basic-laag of de virtuele machines die zijn gemaakt met de methode klassieke VM
- Het versleutelen van Vm's die zijn geconfigureerd met RAID-systemen op basis van software.
- Het versleutelen van Vm's die zijn geconfigureerd met Opslagruimten Direct (S2D) of Windows Server-versies vóór 2016, geconfigureerd met Windows-opslag ruimten.
- Integratie met een on-premises sleutel beheersysteem.
- Azure Files (gedeeld bestands systeem).
- NFS (Network File System).
- Dynamische volumes.
- Windows Server-containers, waarmee dynamische volumes worden gemaakt voor elke container.
- Tijdelijke OS-schijven.
- Versleuteling van gedeelde/gedistribueerde bestands systemen zoals (maar niet beperkt tot) DFS, GFS, DRDB en CephFS.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure Disk Encryption](disk-encryption-overview.md)
- [Voorbeeld scripts Azure Disk Encryption](disk-encryption-sample-scripts.md)
- [Azure Disk Encryption probleem oplossing](disk-encryption-troubleshooting.md)
