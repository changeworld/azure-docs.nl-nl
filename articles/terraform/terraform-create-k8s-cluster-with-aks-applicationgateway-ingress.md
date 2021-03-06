---
title: 'Zelf studie: een Application Gateway ingangs controller maken in azure Kubernetes service'
description: In deze zelf studie maakt u een Kubernetes-cluster met de Azure Kubernetes-service met Application Gateway als ingangs controller
keywords: Azure devops terraform Application Gateway-ingangs AKS kubernetes
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: 6b48d0acb654f0b0643c0754e53f6bc6ea76bb45
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2020
ms.locfileid: "78945324"
---
# <a name="tutorial-create-an-application-gateway-ingress-controller-in-azure-kubernetes-service"></a>Zelf studie: een Application Gateway ingangs controller maken in azure Kubernetes service

[Azure Kubernetes Service (AKS)](/azure/aks/) beheert uw gehoste Kubernetes-omgeving. Met AKS kunt u snel en eenvoudig in containers geplaatste toepassingen implementeren en beheren, zonder dat u kennis hoeft te hebben van het indelen van containers. AKS elimineert ook de last van het offline nemen van toepassingen voor operationele en onderhouds taken. Met behulp van AKS kunnen deze taken, waaronder het inrichten, upgraden en schalen, op aanvraag worden uitgevoerd.

Een ingangs controller biedt verschillende functies voor Kubernetes-Services. Deze functies omvatten reverse proxy, Configureer bare verkeers Routering en TLS-beëindiging. Kubernetes ingangs bronnen worden gebruikt voor het configureren van de ingangs regels voor afzonderlijke Kubernetes-Services. Met behulp van een ingangs controller en ingangs regels kan één IP-adres verkeer naar meerdere services in een Kubernetes-cluster routeren. Al deze functionaliteit wordt door Azure [Application Gateway](/azure/Application-Gateway/)verschaft, waardoor het een ideale ingangs controller is voor Kubernetes op Azure. 

In deze zelf studie leert u hoe u de volgende taken kunt uitvoeren:

> [!div class="checklist"]
> * Maak een [Kubernetes](https://www.redhat.com/en/topics/containers/what-is-kubernetes) -cluster met behulp van AKS met Application Gateway als ingangs controller.
> * Gebruik HCL (HashiCorp Language) om een Kubernetes-cluster te definiëren.
> * Gebruik terraform om Application Gateway resource te maken.
> * Gebruik terraform en AKS om een Kubernetes-cluster te maken.
> * Gebruik het hulp programma kubectl om de beschik baarheid van een Kubernetes-cluster te testen.

## <a name="prerequisites"></a>Vereisten

- **Azure-abonnement**: als u nog geen abonnement op Azure hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) aan voordat u begint.

- **Terraform configureren**: volg de aanwijzingen in het artikel [Terraform en toegang tot Azure configureren](terraform-install-configure.md)

- **Azure-resource groep**: als u geen Azure-resource groep hebt die u voor de demo kunt gebruiken, [maakt u een Azure-resource groep](/azure/azure-resource-manager/manage-resource-groups-portal#create-resource-groups). Noteer de naam van de resource groep en de locatie waar deze waarden worden gebruikt in de demo.

- **Azure service-principal**: volg de aanwijzingen in de sectie **Create the service principal** (de service-principal maken) in het artikel [Create an Azure service principal with Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest) (een Azure-service-principal maken met Azure CLI). Noteer de waarden voor appId, displayName en password.

- **De Service-Principal object-id ophalen**: Voer de volgende opdracht uit in Cloud Shell: `az ad sp list --display-name <displayName>`

## <a name="create-the-directory-structure"></a>De directorystructuur maken

De eerste stap is het maken van een map voor de Terraform-configuratiebestanden voor de oefening.

1. Blader naar de [Azure-portal](https://portal.azure.com).

1. Open [Azure Cloud Shell](/azure/cloud-shell/overview).

1. Ga naar de map `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Maak een directory met de naam `terraform-aks-appgw-ingress`.

    ```bash
    mkdir terraform-aks-appgw-ingress
    ```

1. Ga naar de nieuwe map:

    ```bash
    cd terraform-aks-appgw-ingress
    ```

## <a name="declare-the-azure-provider"></a>De Azure-provider declareren

Maak het Terraform-configuratiebestand waarin de Azure-provider wordt gedeclareerd.

1. Maak in Cloud Shell een bestand met de naam `main.tf`.

    ```bash
    code main.tf
    ```

1. Plak de volgende code in de editor:

    ```hcl
    provider "azurerm" {
      # The "feature" block is required for AzureRM provider 2.x. 
      # If you are using version 1.x, the "features" block is not allowed.
      version = "~>2.0"
      features {}
    }

    terraform {
        backend "azurerm" {}
    }
    ```

1. Sla het bestand op ( **&lt;Ctrl > S**) en sluit de editor af ( **&lt;CTRL > Q**).

## <a name="define-input-variables"></a>Invoervariabelen definiëren

Maak het terraform-configuratie bestand met een lijst met alle variabelen die vereist zijn voor deze implementatie.

1. Maak in Cloud Shell een bestand met de naam `variables.tf`.

    ```bash
    code variables.tf
    ```

1. Plak de volgende code in de editor:
    
    ```hcl
    variable "resource_group_name" {
      description = "Name of the resource group."
    }

    variable "location" {
      description = "Location of the cluster."
    }

    variable "aks_service_principal_app_id" {
      description = "Application ID/Client ID  of the service principal. Used by AKS to manage AKS related resources on Azure like vms, subnets."
    }

    variable "aks_service_principal_client_secret" {
      description = "Secret of the service principal. Used by AKS to manage Azure."
    }

    variable "aks_service_principal_object_id" {
      description = "Object ID of the service principal."
    }

    variable "virtual_network_name" {
      description = "Virtual network name"
      default     = "aksVirtualNetwork"
    }

    variable "virtual_network_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.0.0.0/8"
    }

    variable "aks_subnet_name" {
      description = "AKS Subnet Name."
      default     = "kubesubnet"
    }

    variable "aks_subnet_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.0.0.0/16"
    }

    variable "app_gateway_subnet_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.1.0.0/16"
    }

    variable "app_gateway_name" {
      description = "Name of the Application Gateway."
      default = "ApplicationGateway1"
    }

    variable "app_gateway_sku" {
      description = "Name of the Application Gateway SKU."
      default = "Standard_v2"
    }

    variable "app_gateway_tier" {
      description = "Tier of the Application Gateway SKU."
      default = "Standard_v2"
    }

    variable "aks_name" {
      description = "Name of the AKS cluster."
      default     = "aks-cluster1"
    }
    variable "aks_dns_prefix" {
      description = "Optional DNS prefix to use with hosted Kubernetes API server FQDN."
      default     = "aks"
    }

    variable "aks_agent_os_disk_size" {
      description = "Disk size (in GB) to provision for each of the agent pool nodes. This value ranges from 0 to 1023. Specifying 0 applies the default disk size for that agentVMSize."
      default     = 40
    }

    variable "aks_agent_count" {
      description = "The number of agent nodes for the cluster."
      default     = 3
    }

    variable "aks_agent_vm_size" {
      description = "The size of the Virtual Machine."
      default     = "Standard_D3_v2"
    }

    variable "kubernetes_version" {
      description = "The version of Kubernetes."
      default     = "1.11.5"
    }

    variable "aks_service_cidr" {
      description = "A CIDR notation IP range from which to assign service cluster IPs."
      default     = "10.0.0.0/16"
    }

    variable "aks_dns_service_ip" {
      description = "Containers DNS server IP address."
      default     = "10.0.0.10"
    }

    variable "aks_docker_bridge_cidr" {
      description = "A CIDR notation IP for Docker bridge."
      default     = "172.17.0.1/16"
    }

    variable "aks_enable_rbac" {
      description = "Enable RBAC on the AKS cluster. Defaults to false."
      default     = "false"
    }

    variable "vm_user_name" {
      description = "User name for the VM"
      default     = "vmuser1"
    }

    variable "public_ssh_key_path" {
      description = "Public key path for SSH."
      default     = "~/.ssh/id_rsa.pub"
    }

    variable "tags" {
      type = "map"

      default = {
        source = "terraform"
      }
    }
    ```

1. Sla het bestand op ( **&lt;Ctrl > S**) en sluit de editor af ( **&lt;CTRL > Q**).

## <a name="define-the-resources"></a>De resources definiëren 
Maak een Terraform-configuratiebestand waarin alle resources worden gemaakt. 

1. Maak in Cloud Shell een bestand met de naam `resources.tf`.

    ```bash
    code resources.tf
    ```

1. Plak het volgende code blok om een lokaal blok te maken voor berekende variabelen die opnieuw moeten worden gebruikt:

    ```hcl
    # # Locals block for hardcoded names. 
    locals {
        backend_address_pool_name      = "${azurerm_virtual_network.test.name}-beap"
        frontend_port_name             = "${azurerm_virtual_network.test.name}-feport"
        frontend_ip_configuration_name = "${azurerm_virtual_network.test.name}-feip"
        http_setting_name              = "${azurerm_virtual_network.test.name}-be-htst"
        listener_name                  = "${azurerm_virtual_network.test.name}-httplstn"
        request_routing_rule_name      = "${azurerm_virtual_network.test.name}-rqrt"
        app_gateway_subnet_name = "appgwsubnet"
    }
    ```

1. Plak het volgende code blok om een gegevens bron te maken voor de resource groep, nieuwe gebruikers-id:

    ```hcl
    data "azurerm_resource_group" "rg" {
      name = var.resource_group_name
    }

    # User Assigned Identities 
    resource "azurerm_user_assigned_identity" "testIdentity" {
      resource_group_name = data.azurerm_resource_group.rg.name
      location            = data.azurerm_resource_group.rg.location

      name = "identity1"

      tags = var.tags
    }
    ```

1. Plak het volgende code blok om basis netwerk bronnen te maken:

    ```hcl
    resource "azurerm_virtual_network" "test" {
      name                = var.virtual_network_name
      location            = data.azurerm_resource_group.rg.location
      resource_group_name = data.azurerm_resource_group.rg.name
      address_space       = [var.virtual_network_address_prefix]

      subnet {
        name           = var.aks_subnet_name
        address_prefix = var.aks_subnet_address_prefix
      }

      subnet {
        name           = "appgwsubnet"
        address_prefix = var.app_gateway_subnet_address_prefix
      }

      tags = var.tags
    }

    data "azurerm_subnet" "kubesubnet" {
      name                 = var.aks_subnet_name
      virtual_network_name = azurerm_virtual_network.test.name
      resource_group_name  = data.azurerm_resource_group.rg.name
    }

    data "azurerm_subnet" "appgwsubnet" {
      name                 = "appgwsubnet"
      virtual_network_name = azurerm_virtual_network.test.name
      resource_group_name  = data.azurerm_resource_group.rg.name
    }

    # Public Ip 
    resource "azurerm_public_ip" "test" {
      name                         = "publicIp1"
      location                     = data.azurerm_resource_group.rg.location
      resource_group_name          = data.azurerm_resource_group.rg.name
      allocation_method            = "Static"
      sku                          = "Standard"

      tags = var.tags
    }
    ```

1. Plak het volgende code blok om Application Gateway resource te maken:

    ```hcl
    resource "azurerm_application_gateway" "network" {
      name                = var.app_gateway_name
      resource_group_name = data.azurerm_resource_group.rg.name
      location            = data.azurerm_resource_group.rg.location

      sku {
        name     = var.app_gateway_sku
        tier     = "Standard_v2"
        capacity = 2
      }

      gateway_ip_configuration {
        name      = "appGatewayIpConfig"
        subnet_id = data.azurerm_subnet.appgwsubnet.id
      }

      frontend_port {
        name = local.frontend_port_name
        port = 80
      }

      frontend_port {
        name = "httpsPort"
        port = 443
      }

      frontend_ip_configuration {
        name                 = local.frontend_ip_configuration_name
        public_ip_address_id = azurerm_public_ip.test.id
      }

      backend_address_pool {
        name = local.backend_address_pool_name
      }

      backend_http_settings {
        name                  = local.http_setting_name
        cookie_based_affinity = "Disabled"
        port                  = 80
        protocol              = "Http"
        request_timeout       = 1
      }

      http_listener {
        name                           = local.listener_name
        frontend_ip_configuration_name = local.frontend_ip_configuration_name
        frontend_port_name             = local.frontend_port_name
        protocol                       = "Http"
      }

      request_routing_rule {
        name                       = local.request_routing_rule_name
        rule_type                  = "Basic"
        http_listener_name         = local.listener_name
        backend_address_pool_name  = local.backend_address_pool_name
        backend_http_settings_name = local.http_setting_name
      }

      tags = var.tags

      depends_on = ["azurerm_virtual_network.test", "azurerm_public_ip.test"]
    }
    ```

1. Plak het volgende code blok om roltoewijzingen te maken:

    ```hcl
    resource "azurerm_role_assignment" "ra1" {
      scope                = data.azurerm_subnet.kubesubnet.id
      role_definition_name = "Network Contributor"
      principal_id         = var.aks_service_principal_object_id 

      depends_on = ["azurerm_virtual_network.test"]
    }

    resource "azurerm_role_assignment" "ra2" {
      scope                = azurerm_user_assigned_identity.testIdentity.id
      role_definition_name = "Managed Identity Operator"
      principal_id         = var.aks_service_principal_object_id
      depends_on           = ["azurerm_user_assigned_identity.testIdentity"]
    }

    resource "azurerm_role_assignment" "ra3" {
      scope                = azurerm_application_gateway.network.id
      role_definition_name = "Contributor"
      principal_id         = azurerm_user_assigned_identity.testIdentity.principal_id
      depends_on           = ["azurerm_user_assigned_identity.testIdentity", "azurerm_application_gateway.network"]
    }

    resource "azurerm_role_assignment" "ra4" {
      scope                = data.azurerm_resource_group.rg.id
      role_definition_name = "Reader"
      principal_id         = azurerm_user_assigned_identity.testIdentity.principal_id
      depends_on           = ["azurerm_user_assigned_identity.testIdentity", "azurerm_application_gateway.network"]
    }
    ```

1. Plak het volgende code blok om het Kubernetes-cluster te maken:

    ```hcl
    resource "azurerm_kubernetes_cluster" "k8s" {
      name       = var.aks_name
      location   = data.azurerm_resource_group.rg.location
      dns_prefix = var.aks_dns_prefix

      resource_group_name = data.azurerm_resource_group.rg.name

      linux_profile {
        admin_username = var.vm_user_name

        ssh_key {
          key_data = file(var.public_ssh_key_path)
        }
      }

      addon_profile {
        http_application_routing {
          enabled = false
        }
      }

      default_node_pool {
        name            = "agentpool"
        node_count      = var.aks_agent_count
        vm_size         = var.aks_agent_vm_size
        os_disk_size_gb = var.aks_agent_os_disk_size
        vnet_subnet_id  = data.azurerm_subnet.kubesubnet.id
      }

      service_principal {
        client_id     = var.aks_service_principal_app_id
        client_secret = var.aks_service_principal_client_secret
      }

      network_profile {
        network_plugin     = "azure"
        dns_service_ip     = var.aks_dns_service_ip
        docker_bridge_cidr = var.aks_docker_bridge_cidr
        service_cidr       = var.aks_service_cidr
      }

      depends_on = ["azurerm_virtual_network.test", "azurerm_application_gateway.network"]
      tags       = var.tags
    }

    ```

1. Sla het bestand op ( **&lt;Ctrl > S**) en sluit de editor af ( **&lt;CTRL > Q**).

De code die in deze sectie wordt weer gegeven, stelt de naam in van het cluster, de locatie en de resource_group_name. De `dns_prefix` waarde: die deel uitmaakt van de Fully Qualified Domain Name (FQDN) die wordt gebruikt voor toegang tot het cluster, is ingesteld.

Met de `linux_profile` record kunt u de instellingen configureren waarmee het aanmelden bij de worker-knoop punten wordt ingeschakeld via SSH.

Met AKS betaalt u alleen voor de werkknooppunten. Met de `agent_pool_profile` record configureert u de Details voor deze worker-knoop punten. Het `agent_pool_profile record` bevat het aantal worker-knoop punten dat moet worden gemaakt en het type worker-knoop punten. Als u het cluster in de toekomst omhoog of omlaag wilt schalen, wijzigt u de `count` waarde in deze record.

## <a name="create-a-terraform-output-file"></a>Een Terraform-uitvoerbestand maken

Met [terraform-uitvoer](https://www.terraform.io/docs/configuration/outputs.html) kunt u waarden definiëren die zijn gemarkeerd voor de gebruiker wanneer terraform een plan toepast, en kan worden opgevraagd met behulp van de `terraform output` opdracht. In deze sectie maakt u een uitvoerbestand waarmee via [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/) toegang wordt verkregen tot het cluster.

1. Maak in Cloud Shell een bestand met de naam `output.tf`.

    ```bash
    code output.tf
    ```

1. Plak de volgende code in de editor:

    ```hcl
    output "client_key" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.client_key
    }

    output "client_certificate" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.client_certificate
    }

    output "cluster_ca_certificate" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.cluster_ca_certificate
    }

    output "cluster_username" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.username
    }

    output "cluster_password" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.password
    }

    output "kube_config" {
        value = azurerm_kubernetes_cluster.k8s.kube_config_raw
    }

    output "host" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.host
    }

    output "identity_resource_id" {
        value = azurerm_user_assigned_identity.testIdentity.id
    }

    output "identity_client_id" {
        value = azurerm_user_assigned_identity.testIdentity.client_id
    }
    ```

1. Sla het bestand op ( **&lt;Ctrl > S**) en sluit de editor af ( **&lt;CTRL > Q**).

## <a name="configure-azure-storage-to-store-terraform-state"></a>Azure Storage configureren om de terraform-status op te slaan

In Terraform wordt de status lokaal bijgehouden via het bestand `terraform.tfstate`. Dit patroon werkt goed in een omgeving met één persoon. In een praktischere omgeving met meerdere personen moet u echter met behulp van [Azure Storage](/azure/storage/) de status bijhouden op de server. In deze sectie leert u hoe u de benodigde gegevens voor het opslag account ophaalt en een opslag container maakt. De gegevens over de status van de terraform worden vervolgens opgeslagen in die container.

1. Selecteer **opslag accounts**onder **Azure-services**in het Azure Portal. (Als de optie **opslag accounts** niet op de hoofd pagina wordt weer gegeven, selecteert u **meer services** en zoekt en selecteert u deze.)

1. Selecteer op de pagina **opslag accounts** de naam van het opslag account waarin de status van de terraform moet worden opgeslagen. U kunt bijvoorbeeld het opslagaccount gebruiken dat u hebt gemaakt toen u Cloud Shell de eerste keer opende.  De opslagaccountnaam die door Cloud Shell wordt gemaakt, begint gewoonlijk met `cs`, gevolgd door een willekeurige tekenreeks met cijfers en letters. 

    Noteer het opslag account dat u hebt geselecteerd, zoals u dit later nodig hebt.

1. Selecteer op de opslagaccountpagina de optie **Toegangssleutels**.

    ![Opslagaccountmenu](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account.png)

1. Noteer de waarde van de **key1** - **sleutel** . (Als u het pictogram rechts van de sleutel selecteert, wordt de waarde gekopieerd naar het klembord.)

    ![Toegangssleutels voor opslagaccount](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account-access-key.png)

1. In Cloud Shell maakt u een container in uw Azure Storage-account. Vervang de tijdelijke aanduidingen door de juiste waarden voor uw Azure Storage-account.

    ```azurecli
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>Het Kubernetes-cluster maken
In deze sectie ziet u hoe u de opdracht `terraform init` gebruikt om de resources te maken die zijn gedefinieerd in de configuratiebestanden die u in de vorige secties hebt gemaakt.

1. Initialiseer terraform in Cloud Shell. Vervang de tijdelijke aanduidingen door de juiste waarden voor uw Azure Storage-account.

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
  
    De `terraform init` opdracht geeft het succes van het initialiseren van de back-end-en provider-invoeg toepassing:

    ![Voorbeeld van de resultaten van de opdracht 'terraform init'](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-init-complete.png)

1. Maak in Cloud Shell een bestand met de naam `terraform.tfvars`:

    ```bash
    code terraform.tfvars
    ```

1. Plak de volgende variabelen die u eerder in de editor hebt gemaakt. Gebruik `az account list-locations`om de locatie waarde voor uw omgeving op te halen.

    ```hcl
    resource_group_name = "<Name of the Resource Group already created>"

    location = "<Location of the Resource Group>"
      
    aks_service_principal_app_id = "<Service Principal AppId>"
      
    aks_service_principal_client_secret = "<Service Principal Client Secret>"
      
    aks_service_principal_object_id = "<Service Principal Object Id>"
        
    ```

1. Sla het bestand op ( **&lt;Ctrl > S**) en sluit de editor af ( **&lt;CTRL > Q**).

1. Voer de opdracht `terraform plan` uit om het Terraform-plan te maken waarmee de infrastructuurelementen worden gedefinieerd. 

    ```bash
    terraform plan -out out.plan
    ```

    Met de `terraform plan` opdracht worden de resources weer gegeven die worden gemaakt wanneer u de `terraform apply`-opdracht uitvoert:

    ![Voorbeeld van de resultaten van de opdracht 'terraform plan'](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-plan-complete.png)

1. Voer de opdracht `terraform apply` uit om het plan voor het maken van het Kubernetes-cluster toe te passen. Het proces voor het maken van een Kubernetes-cluster kan enkele minuten duren, wat resulteert in de time-out van de Cloud Shell-sessie. Als er een time-out optreedt voor de Cloud Shell-sessie, kunt u de stappen in het gedeelte ' herstellen vanaf een Cloud Shell-out ' volgen om de zelf studie te volt ooien.

    ```bash
    terraform apply out.plan
    ```

    Met de opdracht `terraform apply` geeft u de resultaten weer van het maken van de resources die zijn gedefinieerd in uw configuratiebestanden:

    ![Voorbeeld van de resultaten van de opdracht 'terraform apply'](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-apply-complete.png)

1. Selecteer in de Azure-portal **Resourcegroepen** in het linkermenu om alle resources in de geselecteerde resourcegroep weer te geven die voor uw nieuwe Kubernetes-cluster zijn gemaakt.

    ![Cloud Shell-prompt](./media/terraform-k8s-cluster-appgw-with-tf-aks/k8s-resources-created.png)

## <a name="recover-from-a-cloud-shell-timeout"></a>Herstellen na een time-out van Cloud Shell

Als er een time-out is opgetreden voor de Cloud Shell-sessie, kunt u de volgende herstelstappen uitvoeren:

1. Start een Cloud Shell-sessie.

1. Ga naar de map met uw Terraform-configuratiebestanden.

    ```bash
    cd /clouddrive/terraform-aks-k8s
    ```

1. Voer de volgende opdracht uit:

    ```bash
    export KUBECONFIG=./azurek8s
    ```
  
## <a name="test-the-kubernetes-cluster"></a>Het Kubernetes-cluster testen
U kunt het gemaakte cluster controleren met de Kubernetes-hulpprogramma's.

1. Haal de Kubernetes-configuratie op uit de Terraform-status en sla deze op in een bestand dat door kubectl kan worden gelezen.

    ```bash
    echo "$(terraform output kube_config)" > ./azurek8s
    ```

1. Stel een omgevingsvariabele in zodat de juiste configuratie wordt opgehaald door kubectl.

    ```bash
    export KUBECONFIG=./azurek8s
    ```

1. Controleer de status van het cluster.

    ```bash
    kubectl get nodes
    ```

    De details van de werkknooppunten worden weergegeven. De status van al deze werkknooppunten moet **Gereed** zijn, zoals in de volgende afbeelding is weergegeven:

    ![Met het hulpprogramma kubectl kunt u de status van uw Kubernetes-cluster controleren](./media/terraform-k8s-cluster-appgw-with-tf-aks/kubectl-get-nodes.png)

## <a name="install-azure-ad-pod-identity"></a>Azure AD pod-identiteit installeren

Azure Active Directory pod-identiteit biedt toegang tot [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview)op basis van tokens.

[Azure AD pod-identiteit](https://github.com/Azure/aad-pod-identity) voegt de volgende onderdelen toe aan uw Kubernetes-cluster:

  - Kubernetes [CRDs](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/): `AzureIdentity`, `AzureAssignedIdentity``AzureIdentityBinding`
  - Onderdeel [Managed Identity controller (MIC)](https://github.com/Azure/aad-pod-identity#managed-identity-controllermic)
  - [NMI-onderdeel (node Managed Identity)](https://github.com/Azure/aad-pod-identity#node-managed-identitynmi)

Als RBAC is **ingeschakeld**, voert u de volgende opdracht uit om de Azure AD pod-identiteit voor uw cluster te installeren:

```bash
kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
```

Als RBAC is **uitgeschakeld**, voert u de volgende opdracht uit om de Azure AD pod-identiteit voor uw cluster te installeren:

```bash
kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
```

## <a name="install-helm"></a>Helm installeren

De code in deze sectie maakt gebruik van [helm](/azure/aks/kubernetes-helm) -Kubernetes Package Manager-om het `application-gateway-kubernetes-ingress`-pakket te installeren:

1. Als RBAC is **ingeschakeld**, voert u de volgende reeks opdrachten uit om helm te installeren en configureren:

    ```bash
    kubectl create serviceaccount --namespace kube-system tiller-sa
    kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
    helm init --tiller-namespace kube-system --service-account tiller-sa
    ```

1. Als RBAC is **uitgeschakeld**, voert u de volgende opdracht uit om helm te installeren en configureren:

    ```bash
    helm init
    ```

1. Voeg de AGIC helm-opslag plaats toe:

    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

## <a name="install-ingress-controller-helm-chart"></a>De helm-grafiek van ingress-controller installeren

1. Down load `helm-config.yaml` om AGIC te configureren:

    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```

1. Bewerk de `helm-config.yaml` en voer de juiste waarden in voor de secties `appgw` en `armAuth`.

    ```bash
    code helm-config.yaml
    ```

    De waarden worden als volgt beschreven:

    - `verbosityLevel`: Hiermee stelt u het uitbreidings niveau in van de infra structuur voor AGIC-logboek registratie. Zie [registratie niveaus](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/463a87213bbc3106af6fce0f4023477216d2ad78/docs/troubleshooting.md#logging-levels) voor mogelijke waarden.
    - `appgw.subscriptionId`: de ID van het Azure-abonnement voor de app-gateway. Voorbeeld: `a123b234-a3b4-557d-b2df-a0bc12de1234`
    - `appgw.resourceGroup`: de naam van de Azure-resource groep waarin de app-gateway is gemaakt. 
    - `appgw.name`: de naam van de Application Gateway. Voor beeld: `applicationgateway1`.
    - `appgw.shared`: deze Booleaanse vlag moet worden standaard ingesteld op `false`. Stel deze in op `true` als u een [gedeelde app-gateway](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-existing.md#multi-cluster--shared-app-gateway)nodig hebt.
    - `kubernetes.watchNamespace`: Geef de naam ruimte op die AGIC moet volgen. De naam ruimte kan één teken reeks waarde of een door komma's gescheiden lijst met naam ruimten zijn. Als u deze variabele weglaat, of als u deze instelt op lege of lege teken reeks, worden alle toegankelijke naam ruimten door de ingangs controller geobserveerd.
    - `armAuth.type`: een waarde van zowel `aadPodIdentity` als `servicePrincipal`.
    - `armAuth.identityResourceID`: de resource-ID van de beheerde identiteit.
    - `armAuth.identityClientId`: de client-ID van de identiteit.
    - `armAuth.secretJSON`: alleen nodig wanneer het geheim type van de Service-Principal is gekozen (wanneer `armAuth.type` is ingesteld op `servicePrincipal`).

    Opmerkingen bij de sleutel:
    - De `identityResourceID` waarde wordt gemaakt in het terraform-script en kan worden gevonden door het volgende uit te voeren: `echo "$(terraform output identity_resource_id)"`.
    - De `identityClientID` waarde wordt gemaakt in het terraform-script en kan worden gevonden door het volgende uit te voeren: `echo "$(terraform output identity_client_id)"`.
    - De `<resource-group>` waarde is de resource groep van uw app-gateway.
    - De `<identity-name>` waarde is de naam van de gemaakte identiteit.
    - Alle identiteiten voor een bepaald abonnement kunnen worden weer gegeven met behulp van: `az identity list`.

1. Installeer het Application Gateway ingangs controller pakket:

    ```bash
    helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure
    ```

### <a name="install-a-sample-app"></a>Een voor beeld-app installeren

Zodra u de app-gateway, AKS en AGIC hebt geïnstalleerd, kunt u een voor beeld-app installeren via [Azure Cloud shell](https://shell.azure.com/):

1. Gebruik de krul opdracht om het YAML-bestand te downloaden:

    ```bash
    curl https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml -o aspnetapp.yaml
    ```

2. Het YAML-bestand Toep assen:

    ```bash
    kubectl apply -f aspnetapp.yaml
    ```

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze niet meer nodig hebt, verwijdert u de resources die u in dit artikel hebt gemaakt.  

Vervang de tijdelijke aanduiding door de juiste waarde. Alle resources in de opgegeven resource groep worden verwijderd.

```azurecli
az group delete -n <resource-group>
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"] 
> [Application Gateway ingangs controller](https://azure.github.io/application-gateway-kubernetes-ingress/)