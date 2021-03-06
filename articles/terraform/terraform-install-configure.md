---
title: Snelstartgids-terraform installeren en configureren om Azure-resources in te richten
description: In deze quicstart installeert en configureert u terraform om Azure-resources te maken
keywords: Azure devops terraform installeren configureren
ms.topic: quickstart
ms.date: 03/09/2020
ms.openlocfilehash: 82635f59ec8165add2046a230a040b06f89d9898
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943509"
---
# <a name="quickstart-install-and-configure-terraform-to-provision-azure-resources"></a>Snelstartgids: terraform installeren en configureren om Azure-resources in te richten
 
Terraform biedt een eenvoudige manier om Cloud infrastructuur te definiëren, te bekijken en te implementeren met behulp van een [eenvoudige sjabloon-taal](https://www.terraform.io/docs/configuration/syntax.html). In dit artikel worden de stappen beschreven die nodig zijn om terraform te gebruiken om resources in te richten in Azure.

Ga naar de [terraform-hub](/azure/terraform)voor meer informatie over het gebruik van terraform met Azure.
> [!NOTE]
> Neem voor terraform specifieke ondersteuning contact op met terraform met behulp van een van hun community-kanalen:
>
>    * De [sectie terraform](https://discuss.hashicorp.com/c/terraform-core) van de portal van de Community bevat vragen, use cases en handige patronen.
>
>    * Ga naar de sectie [terraform providers](https://discuss.hashicorp.com/c/terraform-providers) van de community-portal voor vragen met betrekking tot providers.



[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Terraform wordt standaard geïnstalleerd in de [Cloud shell](/azure/terraform/terraform-cloud-shell). Als u ervoor kiest om terraform lokaal te installeren, voert u de volgende stap uit. Ga anders verder met het [instellen van terraform-toegang tot Azure](#set-up-terraform-access-to-azure).

## <a name="install-terraform"></a>Terraform installeren

Als u terraform wilt installeren, [downloadt](https://www.terraform.io/downloads.html) u het juiste pakket voor uw besturings systeem naar een afzonderlijke installatiemap. De down load bevat één uitvoerbaar bestand, waarvoor u ook een globaal pad moet definiëren. Ga naar [deze webpagina](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux)voor instructies over het instellen van het pad in Linux en Mac. Ga naar [deze webpagina](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows)voor instructies over het instellen van het pad in Windows.

Controleer de configuratie van het pad met de `terraform` opdracht. Er wordt een lijst met beschik bare terraform-opties weer gegeven, zoals in de volgende voorbeeld uitvoer:

```console
azureuser@Azure:~$ terraform
Usage: terraform [--version] [--help] <command> [args]
```

## <a name="set-up-terraform-access-to-azure"></a>Terraform toegang tot Azure instellen

Om terraform in te scha kelen voor het inrichten van resources in azure, maakt u een [Azure AD-Service-Principal](/cli/azure/create-an-azure-service-principal-azure-cli). De Service-Principal verleent uw terraform-scripts voor het inrichten van resources in uw Azure-abonnement.

Als u meerdere Azure-abonnementen hebt, moet u eerst een query uitvoeren op uw account met [AZ account list](/cli/azure/account#az-account-list) om een lijst met abonnements-id en Tenant-id-waarden op te halen:

```azurecli-interactive
az account list --query "[].{name:name, subscriptionId:id, tenantId:tenantId}"
```

Als u een geselecteerd abonnement wilt gebruiken, stelt u het abonnement voor deze sessie in met [AZ account set](/cli/azure/account#az-account-set). Stel de omgevings variabele `SUBSCRIPTION_ID` in om de waarde van het geretourneerde `id` veld van het abonnement dat u wilt gebruiken, op te slaan:

```azurecli-interactive
az account set --subscription="${SUBSCRIPTION_ID}"
```

U kunt nu een service-principal maken voor gebruik met terraform. Gebruik [AZ AD SP create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac)en stel het *bereik* als volgt in op uw abonnement:

```azurecli-interactive
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

Uw `appId`, `password`, `sp_name`en `tenant` worden geretourneerd. Noteer de `appId` en `password`.

## <a name="configure-terraform-environment-variables"></a>Terraform-omgevings variabelen configureren

Als u terraform wilt configureren voor het gebruik van uw Azure AD-Service-Principal, stelt u de volgende omgevings variabelen in, die vervolgens worden gebruikt door de [Azure terraform-modules](https://registry.terraform.io/modules/Azure). U kunt de omgeving ook instellen als u werkt met een andere Azure-Cloud dan Azure public.

- `ARM_SUBSCRIPTION_ID`
- `ARM_CLIENT_ID`
- `ARM_CLIENT_SECRET`
- `ARM_TENANT_ID`
- `ARM_ENVIRONMENT`

U kunt het volgende voor beeld-shell script gebruiken om deze variabelen in te stellen:

```bash
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_appId
export ARM_CLIENT_SECRET=your_password
export ARM_TENANT_ID=your_tenant_id

# Not needed for public, required for usgovernment, german, china
export ARM_ENVIRONMENT=public
```

## <a name="run-a-sample-script"></a>Een voorbeeld script uitvoeren

Maak een bestand `test.tf` in een lege map en plak het volgende script.

```hcl
provider "azurerm" {
  # The "feature" block is required for AzureRM provider 2.x. 
  # If you are using version 1.x, the "features" block is not allowed.
  version = "~>2.0"
  features {}
}
resource "azurerm_resource_group" "rg" {
        name = "testResourceGroup"
        location = "westus"
}
```

Sla het bestand op en Initialiseer vervolgens de terraform-implementatie. Met deze stap downloadt u de Azure-modules die vereist zijn voor het maken van een Azure-resource groep.

```bash
terraform init
```

De uitvoer lijkt op die in het volgende voorbeeld:

```console
* provider.azurerm: version = "~> 0.3"

Terraform has been successfully initialized!
```

U kunt een voor beeld bekijken van de acties die worden uitgevoerd door het terraform-script met `terraform plan`. Wanneer u klaar bent om de resource groep te maken, past u het terraform-plan als volgt toe:

```bash
terraform apply
```

De uitvoer lijkt op die in het volgende voorbeeld:

```console
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.rg
      id:       <computed>
      location: "westus"
      name:     "testResourceGroup"
      tags.%:   <computed>

azurerm_resource_group.rg: Creating...
  location: "" => "westus"
  name:     "" => "testResourceGroup"
  tags.%:   "" => "<computed>"
azurerm_resource_group.rg: Creation complete after 1s
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u terraform geïnstalleerd of de Cloud Shell gebruikt voor het configureren van Azure-referenties en het maken van resources in uw Azure-abonnement. Zie het volgende artikel voor meer informatie over het maken van een volledige terraform-implementatie in Azure:

> [!div class="nextstepaction"]
> [Een Azure-VM maken met terraform](terraform-create-complete-vm.md)