---
title: Een account maken dat door de klant beheerde sleutels voor tabellen en wacht rijen ondersteunt
titleSuffix: Azure Storage
description: Meer informatie over het maken van een opslag account dat ondersteuning biedt voor het configureren van door de klant beheerde sleutels voor tabellen en wacht rijen. Gebruik de Azure-CLI of een Azure Resource Manager sjabloon voor het maken van een opslag account dat afhankelijk is van de versleutelings sleutel van het account voor Azure Storage versleuteling. U kunt vervolgens door de klant beheerde sleutels configureren voor het account.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/05/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 09558a8d1e4e2dc68cefd2c870f54e008d10b97b
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2020
ms.locfileid: "77083570"
---
# <a name="create-an-account-that-supports-customer-managed-keys-for-tables-and-queues"></a>Een account maken dat door de klant beheerde sleutels voor tabellen en wacht rijen ondersteunt

Azure Storage versleutelt alle gegevens in een opslag account in rust. Wachtrij opslag en tabel opslag gebruiken standaard een sleutel die is toegewezen aan de service en die wordt beheerd door micro soft. U kunt er ook voor kiezen om door de klant beheerde sleutels te gebruiken om wachtrij-of tabel gegevens te versleutelen. Als u door de klant beheerde sleutels met wacht rijen en tabellen wilt gebruiken, moet u eerst een opslag account maken dat gebruikmaakt van een versleutelings sleutel die is afgestemd op het account, in plaats van naar de service. Nadat u een account hebt gemaakt dat gebruikmaakt van de account versleutelings sleutel voor wachtrij-en tabel gegevens, kunt u door de klant beheerde sleutels configureren met Azure Key Vault voor dat opslag account.

In dit artikel wordt beschreven hoe u een opslag account maakt dat afhankelijk is van een sleutel die is afgestemd op het account. Wanneer het account voor het eerst wordt gemaakt, gebruikt micro soft de account sleutel voor het versleutelen van de gegevens in het account en wordt de sleutel door micro soft beheerd. U kunt vervolgens door de klant beheerde sleutels voor het account configureren om gebruik te maken van deze voor delen, waaronder de mogelijkheid om uw eigen sleutels op te geven, de sleutel versie bij te werken, de sleutels te draaien en toegangs beheer in te trekken.

## <a name="about-the-feature"></a>Over de functie

Als u een opslag account wilt maken dat afhankelijk is van de account versleutelings sleutel voor wachtrij-en tabel opslag, moet u zich eerst registreren voor het gebruik van deze functie met Azure. Vanwege beperkte capaciteit moet u er rekening mee houden dat het enkele maanden kan duren voordat aanvragen voor toegang zijn goedgekeurd.

U kunt een opslag account maken dat afhankelijk is van de account versleutelings sleutel voor wachtrij-en tabel opslag in de volgende regio's:

- VS - oost
- VS - zuid-centraal
- VS - west 2  

### <a name="register-to-use-the-account-encryption-key"></a>Registreren voor het gebruik van de account versleutelings sleutel

Als u zich wilt registreren voor het gebruik van de account versleutelings sleutel met wachtrij-of tabel opslag, gebruikt u Power shell of Azure CLI.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Als u zich bij Power shell wilt registreren, roept u de opdracht [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) aan.

```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForQueues
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForTables
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Als u zich wilt registreren bij Azure CLI, roept u de opdracht [AZ feature REGI ster](/cli/azure/feature#az-feature-register) aan.

```azurecli
az feature register --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForQueues
az feature register --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForTables
```

# <a name="templatetabtemplate"></a>[Sjabloon](#tab/template)

N.v.t.

---

### <a name="check-the-status-of-your-registration"></a>Controleer de status van uw registratie

Gebruik Power shell of Azure CLI om de status van uw registratie voor wachtrij-of tabel opslag te controleren.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Als u de status van uw registratie met Power shell wilt controleren, roept u de opdracht [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) aan.

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForQueues
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForTables
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Als u de status van uw registratie met Azure CLI wilt controleren, roept u de opdracht [AZ functie](/cli/azure/feature#az-feature-show) aan.

```azurecli
az feature show --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForQueues
az feature show --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForTables
```

# <a name="templatetabtemplate"></a>[Sjabloon](#tab/template)

N.v.t.

---

### <a name="re-register-the-azure-storage-resource-provider"></a>De Azure Storage Resource provider opnieuw registreren

Nadat de registratie is goedgekeurd, moet u de Azure Storage Resource provider opnieuw registreren. Gebruik Power shell of Azure CLI om de resource provider opnieuw te registreren.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Als u de resource provider opnieuw wilt registreren bij Power shell, roept u de opdracht [REGI ster-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider) aan.

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Als u de resource provider opnieuw wilt registreren bij Azure CLI, roept u de opdracht [AZ provider REGI ster](/cli/azure/provider#az-provider-register) aan.

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

# <a name="templatetabtemplate"></a>[Sjabloon](#tab/template)

N.v.t.

---

## <a name="create-an-account-that-uses-the-account-encryption-key"></a>Een account maken dat gebruikmaakt van de versleutelings sleutel van het account

U moet een nieuw opslag account configureren voor het gebruik van de account versleutelings sleutel voor wacht rijen en tabellen op het moment dat u het opslag account maakt. Het bereik van de versleutelings sleutel kan niet worden gewijzigd nadat het account is gemaakt.

Het opslag account moet van het type voor algemeen gebruik v2 zijn. U kunt het opslag account maken en configureren om te vertrouwen op de account versleutelings sleutel met behulp van Azure CLI of een Azure Resource Manager sjabloon.

> [!NOTE]
> Alleen wachtrij-en tabel opslag kunnen eventueel worden geconfigureerd voor het versleutelen van gegevens met de versleutelings sleutel van het account wanneer het opslag account wordt gemaakt. Blob-opslag en Azure Files altijd de account versleutelings sleutel gebruiken voor het versleutelen van gegevens.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Als u Power shell wilt gebruiken voor het maken van een opslag account dat afhankelijk is van de versleutelings sleutel van het account, moet u ervoor zorgen dat u de Azure PowerShell module versie 3.4.0 of hoger hebt geïnstalleerd. Zie [de module Azure PowerShell installeren](/powershell/azure/install-az-ps)voor meer informatie.

Maak vervolgens een voor algemeen gebruik v2-opslag account door de opdracht [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) aan te roepen, met de juiste para meters:

- Neem de optie `-EncryptionKeyTypeForQueue` op en stel de waarde ervan in op `Account` om de account versleutelings sleutel te gebruiken voor het versleutelen van gegevens in de wachtrij opslag.
- Neem de optie `-EncryptionKeyTypeForTable` op en stel de waarde ervan in op `Account` om de account versleutelings sleutel te gebruiken voor het versleutelen van gegevens in de tabel opslag.

In het volgende voor beeld ziet u hoe u een v2-opslag account voor algemeen gebruik maakt dat is geconfigureerd voor geografisch redundante opslag met lees toegang (RA-GRS) en die gebruikmaakt van de account versleutelings sleutel voor het versleutelen van gegevens voor wachtrij-en tabel opslag. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden:

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -EncryptionKeyTypeForTable Account `
    -EncryptionKeyTypeForQueue Account
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Als u Azure CLI wilt gebruiken om een opslag account te maken dat afhankelijk is van de versleutelings sleutel van het account, moet u ervoor zorgen dat u Azure CLI-versie 2.0.80 of hoger hebt geïnstalleerd. Zie [de Azure cli installeren](/cli/azure/install-azure-cli)voor meer informatie.

Maak vervolgens een voor algemeen gebruik v2-opslag account door de opdracht [AZ Storage account create](/cli/azure/storage/account#az-storage-account-create) aan te roepen, met de juiste para meters:

- Neem de optie `--encryption-key-type-for-queue` op en stel de waarde ervan in op `Account` om de account versleutelings sleutel te gebruiken voor het versleutelen van gegevens in de wachtrij opslag.
- Neem de optie `--encryption-key-type-for-table` op en stel de waarde ervan in op `Account` om de account versleutelings sleutel te gebruiken voor het versleutelen van gegevens in de tabel opslag.

In het volgende voor beeld ziet u hoe u een v2-opslag account voor algemeen gebruik maakt dat is geconfigureerd voor geografisch redundante opslag met lees toegang (RA-GRS) en die gebruikmaakt van de account versleutelings sleutel voor het versleutelen van gegevens voor wachtrij-en tabel opslag. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden:

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_RAGRS \
    --kind StorageV2 \
    --encryption-key-type-for-table Account \
    --encryption-key-type-for-queue Account
```

# <a name="templatetabtemplate"></a>[Sjabloon](#tab/template)

In het volgende JSON-voor beeld wordt een v2-opslag account voor algemeen gebruik gemaakt dat is geconfigureerd voor geografisch redundante opslag met lees toegang (RA-GRS) en die gebruikmaakt van de account versleutelings sleutel voor het versleutelen van gegevens voor wachtrij-en tabel opslag. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen punt haken te vervangen door uw eigen waarden:

```json
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2019-06-01",
        "name": "[parameters('<storage-account>')]",
        "location": "[parameters('<location>')]",
        "dependsOn": [],
        "tags": {},
        "sku": {
            "name": "[parameters('Standard_RAGRS')]"
        },
        "kind": "[parameters('StorageV2')]",
        "properties": {
            "accessTier": "[parameters('<accessTier>')]",
            "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]",
            "largeFileSharesState": "[parameters('<largeFileSharesState>')]",
            "encryption": {
                "services": {
                    "queue": {
                        "keyType": "Account"
                    },
                    "table": {
                        "keyType": "Account"
                    }
                },
                "keySource": "Microsoft.Storage"
            }
        }
    }
],
```

---

Nadat u een account hebt gemaakt dat afhankelijk is van de versleutelings sleutel van het account, raadpleegt u een van de volgende artikelen voor het configureren van door de klant beheerde sleutels met Azure Key Vault:

- [Door de klant beheerde sleutels met Azure Key Vault configureren met behulp van de Azure Portal](storage-encryption-keys-portal.md)
- [Door de klant beheerde sleutels configureren met Azure Key Vault met behulp van Power shell](storage-encryption-keys-powershell.md)
- [Door de klant beheerde sleutels configureren met Azure Key Vault met behulp van Azure CLI](storage-encryption-keys-cli.md)

## <a name="verify-the-account-encryption-key"></a>De versleutelings sleutel voor het account controleren

Als u wilt controleren of een service in een opslag account gebruikmaakt van de versleutelings sleutel van het account, roept u de opdracht Azure CLI [AZ Storage account](/cli/azure/storage/account#az-storage-account-show) aan. Met deze opdracht wordt een set eigenschappen van het opslag account en de bijbehorende waarden geretourneerd. Zoek naar het veld `keyType` voor elke service binnen de versleutelings eigenschap en controleer of deze is ingesteld op `Account`.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Als u wilt controleren of een service in een opslag account gebruikmaakt van de account versleutelings sleutel, roept u de opdracht [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) aan. Met deze opdracht wordt een set eigenschappen van het opslag account en de bijbehorende waarden geretourneerd. Zoek naar het veld `KeyType` voor elke service in de eigenschap `Encryption` en controleer of deze is ingesteld op `Account`.

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.Services.Queue
$account.Encryption.Services.Table
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Als u wilt controleren of een service in een opslag account gebruikmaakt van de account versleutelings sleutel, roept u de opdracht [AZ Storage account](/cli/azure/storage/account#az-storage-account-show) aan. Met deze opdracht wordt een set eigenschappen van het opslag account en de bijbehorende waarden geretourneerd. Zoek naar het veld `keyType` voor elke service binnen de versleutelings eigenschap en controleer of deze is ingesteld op `Account`.

```azurecli
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="templatetabtemplate"></a>[Sjabloon](#tab/template)

N.v.t.

---

## <a name="next-steps"></a>Volgende stappen

- [Azure Storage versleuteling voor Data-at-rest](storage-service-encryption.md) 
- [Wat is Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
