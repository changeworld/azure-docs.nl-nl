---
title: 'Quick Start: gebruik terraform om een volledige virtuele Linux-machine in azure te maken'
description: In deze Quick Start gebruikt u terraform voor het maken en beheren van een volledige virtuele Linux-machine omgeving in azure
keywords: virtuele machine voor Azure devops terraform Linux VM
ms.topic: quickstart
ms.date: 03/09/2020
ms.openlocfilehash: 03974d68477855d4ff55b7179312c91ba7d0d055
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943529"
---
# <a name="quickstart-create-a-complete-linux-virtual-machine-infrastructure-in-azure-with-terraform"></a>Snelstartgids: een volledige infra structuur voor virtuele Linux-machines maken in azure met terraform

Met terraform kunt u volledige infrastructuur implementaties definiëren en maken in Azure. U bouwt terraform-sjablonen op in een Human-Lees bare indeling waarmee Azure-resources op een consistente en verreproduceerde manier worden gemaakt en geconfigureerd. In dit artikel wordt beschreven hoe u een volledige Linux-omgeving en ondersteunende resources maakt met terraform. U kunt ook leren hoe u [terraform installeert en configureert](terraform-install-configure.md).

> [!NOTE]
> Neem voor terraform specifieke ondersteuning contact op met terraform met behulp van een van hun community-kanalen:
>
>    * De [sectie terraform](https://discuss.hashicorp.com/c/terraform-core) van de portal van de Community bevat vragen, use cases en handige patronen.
>
>    * Ga naar de sectie [terraform providers](https://discuss.hashicorp.com/c/terraform-providers) van de community-portal voor vragen met betrekking tot providers.


## <a name="create-azure-connection-and-resource-group"></a>Een Azure-verbinding en-resource groep maken

We gaan elke sectie van een terraform-sjabloon door lopen. U kunt ook de volledige versie van de [terraform-sjabloon](#complete-terraform-script) zien die u kunt kopiëren en plakken.

De sectie `provider` vertelt terraform een Azure-provider te gebruiken. Zie [terraform installeren en configureren](terraform-install-configure.md)om waarden op te halen voor *subscription_id*, *client_id*, *client_secret*en *tenant_id*. 

> [!TIP]
> Als u omgevings variabelen voor de waarden maakt of de [Azure Cloud shell bash-ervaring](/azure/cloud-shell/overview) gebruikt, hoeft u de variabelen declaraties in deze sectie niet op te maken.

```hcl
provider "azurerm" {
    # The "feature" block is required for AzureRM provider 2.x. 
    # If you are using version 1.x, the "features" block is not allowed.
    version = "~>2.0"
    features {}
    
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

In het volgende gedeelte maakt u een resource groep met de naam `myResourceGroup` op de `eastus` locatie:

```hcl
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "eastus"

    tags = {
        environment = "Terraform Demo"
    }
}
```

In aanvullende secties verwijst u naar de resource groep met *$ {azurerm_resource_group. myterraformgroup. name}* .

## <a name="create-virtual-network"></a>Virtueel netwerk maken
In de volgende sectie wordt een virtueel netwerk met de naam *myVnet* gemaakt in de adres ruimte *10.0.0.0/16* :

```hcl
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name

    tags = {
        environment = "Terraform Demo"
    }
}
```

In het volgende gedeelte maakt u een subnet met de naam *mySubnet* in het virtuele netwerk *myVnet* :

```hcl
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = azurerm_resource_group.myterraformgroup.name
    virtual_network_name = azurerm_virtual_network.myterraformnetwork.name
    address_prefix       = "10.0.2.0/24"
}
```


## <a name="create-public-ip-address"></a>Openbaar IP-adres maken
Als u toegang wilt krijgen tot bronnen op het Internet, maakt en wijst u een openbaar IP-adres toe aan uw VM. In het volgende gedeelte maakt u een openbaar IP-adres met de naam *myPublicIP*:

```hcl
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "eastus"
    resource_group_name          = azurerm_resource_group.myterraformgroup.name
    allocation_method            = "Dynamic"

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-network-security-group"></a>Netwerk beveiligings groep maken
Met netwerk beveiligings groepen wordt de stroom van het netwerk verkeer in en uit uw virtuele machine beheerd. In de volgende sectie wordt een netwerk beveiligings groep gemaakt met de naam *myNetworkSecurityGroup* en wordt een regel gedefinieerd voor het toestaan van SSH-verkeer op TCP-poort 22:

```hcl
resource "azurerm_network_security_group" "myterraformnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name
    
    security_rule {
        name                       = "SSH"
        priority                   = 1001
        direction                  = "Inbound"
        access                     = "Allow"
        protocol                   = "Tcp"
        source_port_range          = "*"
        destination_port_range     = "22"
        source_address_prefix      = "*"
        destination_address_prefix = "*"
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-network-interface-card"></a>Virtuele netwerk interface kaart maken
Een virtuele netwerk interface kaart (NIC) verbindt uw VM met een bepaald virtueel netwerk, een openbaar IP-adres en een netwerk beveiligings groep. In de volgende sectie van een terraform-sjabloon wordt een virtuele NIC gemaakt met de naam *myNIC* verbonden met de virtuele netwerk resources die u hebt gemaakt:

```hcl
resource "azurerm_network_interface" "myterraformnic" {
    name                        = "myNIC"
    location                    = "eastus"
    resource_group_name         = azurerm_resource_group.myterraformgroup.name

    ip_configuration {
        name                          = "myNicConfiguration"
        subnet_id                     = "${azurerm_subnet.myterraformsubnet.id}"
        private_ip_address_allocation = "Dynamic"
        public_ip_address_id          = "${azurerm_public_ip.myterraformpublicip.id}"
    }

    tags = {
        environment = "Terraform Demo"
    }
}

# Connect the security group to the network interface
resource "azurerm_network_interface_security_group_association" "example" {
    network_interface_id      = azurerm_network_interface.myterraformnic.id
    network_security_group_id = azurerm_network_security_group.myterraformnsg.id
}
```


## <a name="create-storage-account-for-diagnostics"></a>Opslag account maken voor diagnostische gegevens
Als u Diagnostische gegevens over opstarten voor een virtuele machine wilt opslaan, hebt u een opslag account nodig. Deze diagnostische gegevens over opstarten kunnen u helpen problemen op te lossen en de status van uw virtuele machine te controleren. Het opslag account dat u maakt, is alleen om de diagnostische gegevens over opstarten op te slaan. Aangezien elk opslag account een unieke naam moet hebben, genereert de volgende sectie een wille keurige tekst:

```hcl
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = azurerm_resource_group.myterraformgroup.name
    }
    
    byte_length = 8
}
```

U kunt nu een opslag account maken. In de volgende sectie wordt een opslag account gemaakt met de naam op basis van de wille keurige tekst die in de vorige stap is gegenereerd:

```hcl
resource "azurerm_storage_account" "mystorageaccount" {
    name                        = "diag${random_id.randomId.hex}"
    resource_group_name         = azurerm_resource_group.myterraformgroup.name
    location                    = "eastus"
    account_replication_type    = "LRS"
    account_tier                = "Standard"

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-machine"></a>Virtuele machine maken

De laatste stap bestaat uit het maken van een virtuele machine en het gebruik van alle resources die zijn gemaakt. In het volgende gedeelte maakt u een virtuele machine met de naam *myVM* en koppelt u de virtuele NIC met de naam *myNIC*. De meest recente *Ubuntu 16,04-LTS-* installatie kopie wordt gebruikt en een gebruiker met de naam *azureuser* is gemaakt met wachtwoord verificatie uitgeschakeld.

 SSH-sleutel gegevens vindt u in de sectie *ssh_keys* . Geef een geldige open bare SSH-sleutel op in het veld *key_data* .

```hcl
resource "azurerm_linux_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "eastus"
    resource_group_name   = azurerm_resource_group.myterraformgroup.name
    network_interface_ids = [azurerm_network_interface.myterraformnic.id]
    size                  = "Standard_DS1_v2"

    os_disk {
        name              = "myOsDisk"
        caching           = "ReadWrite"
        storage_account_type = "Premium_LRS"
    }

    storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04.0-LTS"
        version   = "latest"
    }

    computer_name  = "myvm"
    admin_username = "azureuser"
    disable_password_authentication = true
        
    admin_ssh_key {
        username       = "azureuser"
        public_key     = file("/home/azureuser/.ssh/authorized_keys")
    }

    boot_diagnostics {
        storage_account_uri = azurerm_storage_account.mystorageaccount.primary_blob_endpoint
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```

## <a name="complete-terraform-script"></a>Terraform-script volt ooien

Als u al deze secties samen wilt brengen en terraform in actie wilt zien, maakt u een bestand met de naam *terraform_azure. tf* en plakt u de volgende inhoud:

```hcl
# Configure the Microsoft Azure Provider
provider "azurerm" {
    # The "feature" block is required for AzureRM provider 2.x. 
    # If you are using version 1.x, the "features" block is not allowed.
    version = "~>2.0"
    features {}

    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}

# Create a resource group if it doesn't exist
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "eastus"

    tags = {
        environment = "Terraform Demo"
    }
}

# Create virtual network
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name

    tags = {
        environment = "Terraform Demo"
    }
}

# Create subnet
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = azurerm_resource_group.myterraformgroup.name
    virtual_network_name = azurerm_virtual_network.myterraformnetwork.name
    address_prefix       = "10.0.1.0/24"
}

# Create public IPs
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "eastus"
    resource_group_name          = azurerm_resource_group.myterraformgroup.name
    allocation_method            = "Dynamic"

    tags = {
        environment = "Terraform Demo"
    }
}

# Create Network Security Group and rule
resource "azurerm_network_security_group" "myterraformnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name
    
    security_rule {
        name                       = "SSH"
        priority                   = 1001
        direction                  = "Inbound"
        access                     = "Allow"
        protocol                   = "Tcp"
        source_port_range          = "*"
        destination_port_range     = "22"
        source_address_prefix      = "*"
        destination_address_prefix = "*"
    }

    tags = {
        environment = "Terraform Demo"
    }
}

# Create network interface
resource "azurerm_network_interface" "myterraformnic" {
    name                      = "myNIC"
    location                  = "eastus"
    resource_group_name       = azurerm_resource_group.myterraformgroup.name

    ip_configuration {
        name                          = "myNicConfiguration"
        subnet_id                     = azurerm_subnet.myterraformsubnet.id
        private_ip_address_allocation = "Dynamic"
        public_ip_address_id          = azurerm_public_ip.myterraformpublicip.id
    }

    tags = {
        environment = "Terraform Demo"
    }
}

# Connect the security group to the network interface
resource "azurerm_network_interface_security_group_association" "example" {
    network_interface_id      = azurerm_network_interface.myterraformnic.id
    network_security_group_id = azurerm_network_security_group.myterraformnsg.id
}

# Generate random text for a unique storage account name
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = azurerm_resource_group.myterraformgroup.name
    }
    
    byte_length = 8
}

# Create storage account for boot diagnostics
resource "azurerm_storage_account" "mystorageaccount" {
    name                        = "diag${random_id.randomId.hex}"
    resource_group_name         = azurerm_resource_group.myterraformgroup.name
    location                    = "eastus"
    account_tier                = "Standard"
    account_replication_type    = "LRS"

    tags = {
        environment = "Terraform Demo"
    }
}

# Create virtual machine
resource "azurerm_linux_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "eastus"
    resource_group_name   = azurerm_resource_group.myterraformgroup.name
    network_interface_ids = [azurerm_network_interface.myterraformnic.id]
    size                  = "Standard_DS1_v2"

    os_disk {
        name              = "myOsDisk"
        caching           = "ReadWrite"
        storage_account_type = "Premium_LRS"
    }

    source_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04.0-LTS"
        version   = "latest"
    }

    computer_name  = "myvm"
    admin_username = "azureuser"
    disable_password_authentication = true
        
    admin_ssh_key {
        username       = "azureuser"
        public_key     = file("/home/azureuser/.ssh/authorized_keys")
    }

    boot_diagnostics {
        storage_account_uri = azurerm_storage_account.mystorageaccount.primary_blob_endpoint
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="build-and-deploy-the-infrastructure"></a>De infra structuur bouwen en implementeren
Als uw terraform-sjabloon is gemaakt, is de eerste stap het initialiseren van terraform. Met deze stap zorgt u ervoor dat terraform beschikt over alle vereisten voor het maken van uw sjabloon in Azure.

```bash
terraform init
```

De volgende stap is het terraform controleren en valideren van de sjabloon. In deze stap worden de aangevraagde bronnen vergeleken met de status informatie die is opgeslagen door terraform. vervolgens wordt de geplande uitvoering uitgevoerd. Er worden *geen* resources gemaakt in Azure.

```bash
terraform plan
```

Nadat u de vorige opdracht hebt uitgevoerd, ziet u iets zoals in het volgende scherm:

```console
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


...

Note: You didn't specify an "-out" parameter to save this plan, so when
"apply" is called, Terraform can't guarantee this is what will execute.
  + azurerm_resource_group.myterraform
      <snip>
  + azurerm_virtual_network.myterraformnetwork
      <snip>
  + azurerm_network_interface.myterraformnic
      <snip>
  + azurerm_network_security_group.myterraformnsg
      <snip>
  + azurerm_public_ip.myterraformpublicip
      <snip>
  + azurerm_subnet.myterraformsubnet
      <snip>
  + azurerm_virtual_machine.myterraformvm
      <snip>
Plan: 7 to add, 0 to change, 0 to destroy.
```

Als alles er goed uitziet en u klaar bent om de infra structuur in azure te maken, past u de sjabloon in terraform toe:

```bash
terraform apply
```

Zodra de terraform is voltooid, is uw VM-infra structuur klaar. Het open bare IP-adres van uw virtuele machine verkrijgen met [AZ VM show](/cli/azure/vm):

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
```

U kunt vervolgens SSHen naar uw virtuele machine:

```bash
ssh azureuser@<publicIps>
```

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Meer informatie over het gebruik van terraform in azure](/azure/terraform)