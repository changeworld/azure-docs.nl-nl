---
title: Zelf studie-Azure Cloud Shell configureren voor terraform
description: In deze zelf studie gebruikt u terraform met Azure Cloud Shell om de verificatie en sjabloon configuratie te vereenvoudigen.
keywords: Azure devops terraform Cloud shell
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: 3a9db1143ba07b549a271d53d610e0a4853467c6
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2020
ms.locfileid: "78945338"
---
# <a name="tutorial-configure-azure-cloud-shell-for-terraform"></a>Zelf studie: Azure Cloud Shell configureren voor terraform

Terraform werkt goed vanuit een bash-opdracht regel in macOS, Windows of Linux. Het uitvoeren van uw terraform-configuraties in de bash-ervaring van [Azure Cloud shell](/azure/cloud-shell/overview) heeft enkele unieke voor delen. In deze zelf studie ziet u hoe u terraform-scripts schrijft die in Azure worden geïmplementeerd met behulp van Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Automatische configuratie van referenties

Terraform wordt geïnstalleerd en is direct beschikbaar in Cloud Shell. Terraform-scripts worden geverifieerd met Azure wanneer ze zijn aangemeld bij Cloud Shell om de infra structuur te beheren zonder aanvullende configuratie. Automatische verificatie slaat twee hand matige processen over:
- Een Active Directory-Service-Principal maken
- De variabelen van de Azure terraform-provider configureren


## <a name="use-modules-and-providers"></a>Modules en providers gebruiken

Voor Azure terraform-modules zijn referenties vereist om Azure-resources te openen en te wijzigen. Als u terraform-modules in Cloud Shell wilt gebruiken, voegt u de volgende code toe:


```hcl
# Configure the Microsoft Azure Provider
provider "azurerm" {
    # The "feature" block is required for AzureRM provider 2.x. 
    # If you are using version 1.x, the "features" block is not allowed.
    version = "~>2.0"
    features {}
}
```

Cloud Shell geeft de vereiste waarden voor de `azurerm` provider door middel van omgevings variabelen wanneer u een van de `terraform` CLI-opdrachten gebruikt.

## <a name="other-cloud-shell-developer-tools"></a>Andere hulpmiddelen voor Cloud Shell-ontwikkelaars

Bestand en shellstatussen blijven bewaard in Azure Storage tussen verschillende Cloud Shell-sessies. Gebruik [Azure Storage Explorer](/azure/vs-azure-tools-storage-manage-with-storage-explorer) om bestanden te kopiëren en te uploaden naar Cloud shell vanaf uw lokale computer.

De Azure CLI is beschikbaar in Cloud Shell en is een uitstekend hulp programma voor het testen van configuraties en het controleren van uw werk nadat `terraform apply` of `terraform destroy` is voltooid.


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een klein VM-cluster maken met behulp van het module register](terraform-create-vm-cluster-module.md)