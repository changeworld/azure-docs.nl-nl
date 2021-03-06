---
title: Een virtuele machine implementeren vanuit Azure Marketplace
description: Hierin wordt uitgelegd hoe u een virtuele machine implementeert vanuit een vooraf geconfigureerde virtuele machine in azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/29/2018
ms.author: pabutler
ms.openlocfilehash: 2888d7643fd4f624634dc2ec520bec6e753382f1
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73816829"
---
# <a name="deploy-a-virtual-machine-from-the-azure-marketplace"></a>Een virtuele machine implementeren vanuit de Azure Marketplace

In dit artikel wordt uitgelegd hoe u een vooraf geconfigureerde virtuele machine (VM) implementeert vanuit een Azure Marketplace, met behulp van het meegeleverde Azure PowerShell script.  Met dit script worden ook de WinRM-HTTP-en HTTPS-eind punten op de virtuele machine weer gegeven.  Het script vereist dat u al een certificaat hebt geüpload naar Azure Key Vault, zoals beschreven in [certificaten voor Azure Key Vault maken](./cpp-create-key-vault-cert.md). 

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="vm-deployment-template"></a>VM-implementatie sjabloon

De Snelstartgids Azure VM-implementatie sjabloon is beschikbaar als online bestand [azuredeploy. json](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-vm-winrm-keyvault-windows/azuredeploy.json).  Het bevat de volgende para meters:

|  **Bepaalde**        |   **Beschrijving**                                 |
|  -------------        |   ---------------                                 |
| newStorageAccountName | De naam van het opslag account                       |
| dnsNameForPublicIP    | De DNS-naam voor het open bare IP-adres. Moet een kleine letter zijn.    |
| adminUserName         | Gebruikers naam van beheerder                          |
| adminPassword         | Wacht woord van beheerder                          |
| imagePublisher        | Uitgever van installatie kopie                                   |
| imageOffer            | Aanbieding van installatie kopie                                       |
| imageSKU              | Afbeeldings-SKU                                         |
| vmSize                | Grootte van de virtuele machine                                    |
| vmName                | Naam van de virtuele machine                                    |
| vaultName             | De naam van de sleutel kluis                             |
| vaultResourceGroup    | Resource groep van de sleutel kluis                   |
| certificateUrl        | URL voor het certificaat, inclusief versie in de sleutel kluis, bijvoorbeeld `https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7` |
|  |  |


## <a name="deployment-script"></a>Implementatie script

Bewerk het volgende Azure PowerShell script en voer het uit om de opgegeven Azure Marketplace-VM te implementeren.

```powershell

New-AzResourceGroupDeployment -Name "dplvm$postfix" -ResourceGroupName "$rgName" -TemplateUri "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-vm-winrm-keyvault-windows/azuredeploy.json" -newStorageAccountName "test$postfix" -dnsNameForPublicIP $vmName -adminUserName "isv" -adminPassword $pwd -vmSize "Standard_A2" -vmName $vmName -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id 

```


## <a name="next-steps"></a>Volgende stappen

Wanneer u een vooraf geconfigureerde virtuele machine hebt geïmplementeerd, kunt u de oplossingen en services die deze bevat configureren en openen, of deze gebruiken voor verdere ontwikkeling. 
