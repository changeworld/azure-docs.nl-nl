---
title: Veilige experimenten en demijnen in het virtuele netwerk
titleSuffix: Azure Machine Learning
description: meer informatie over het beveiligen van experimenten/trainings taken en het afleiden/scoren van taken in Azure Machine Learning binnen een Azure Virtual Network.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 01/13/2020
ms.openlocfilehash: 6e5571604e6154408f2005ab4804b4270041e4cf
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79270197"
---
# <a name="secure-azure-ml-experimentation-and-inference-jobs-within-an-azure-virtual-network"></a>Azure ML-experimenten beveiligen en taken in een Azure-Virtual Network afzorgen
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel vindt u informatie over het beveiligen van experimenten/trainings taken en de functies voor het afwijzen en bepalen van problemen in Azure Machine Learning binnen een Azure Virtual Network (vnet).

Een **virtueel netwerk** fungeert als beveiligings grens en isoleert uw Azure-resources van het open bare Internet. U kunt ook een virtueel Azure-netwerk toevoegen aan uw on-premises netwerk. Door netwerken aan te koppelen, kunt u uw modellen veilig trainen en toegang verkrijgen tot uw geïmplementeerde modellen.

Azure Machine Learning is afhankelijk van andere Azure-Services voor reken resources. Reken bronnen of [Compute-doelen](concept-compute-target.md)worden gebruikt voor het trainen en implementeren van modellen. De doelen kunnen worden gemaakt in een virtueel netwerk. U kunt bijvoorbeeld micro soft Data Science Virtual Machine gebruiken om een model te trainen en het model vervolgens te implementeren in azure Kubernetes service (AKS). Zie [overzicht van Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)voor meer informatie over virtuele netwerken.

Dit artikel bevat ook gedetailleerde informatie over *Geavanceerde beveiligings instellingen*, informatie die niet nodig is voor basis-of experimentele gebruiks gevallen. Bepaalde gedeelten van dit artikel bevatten informatie over de configuratie voor diverse scenario's. U hoeft de instructies in de aangegeven volg orde of in hun geheel niet te volt ooien.

> [!TIP]
> Tenzij specifiek wordt aangeroepen, werkt het gebruik van resources zoals opslag accounts of COMPUTE-doelen binnen een virtueel netwerk met zowel machine learning pijp lijnen als niet-pipeline-werk stromen, zoals script uitvoeringen.

> [!WARNING]
> Micro soft biedt geen ondersteuning voor het gebruik van de Azure Machine Learning Designer of geautomatiseerde machine learning (vanuit Studio) met resources in een virtueel netwerk.

## <a name="prerequisites"></a>Vereisten

+ Een Azure Machine Learning- [werk ruimte](how-to-manage-workspace.md).

+ Algemene werk ervaring van zowel de [Azure Virtual Network-Service](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) als [IP-netwerken](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm).

+ Een bestaand virtueel netwerk en subnet voor gebruik met uw reken resources.

## <a name="use-a-storage-account-for-your-workspace"></a>Een opslag account voor uw werk ruimte gebruiken

Als u een Azure-opslag account wilt gebruiken voor de werk ruimte in een virtueel netwerk, gebruikt u de volgende stappen:

1. Maak een reken resource (bijvoorbeeld een Machine Learning Reken instantie of cluster) achter een virtueel netwerk of koppel een reken resource aan de werk ruimte (bijvoorbeeld een HDInsight-cluster, een virtuele machine of een Azure Kubernetes service-cluster). De reken resource kan zijn voor experimenten of model implementaties.

   Zie de sectie [een machine learning Compute gebruiken](#amlcompute), [een virtuele machine of een HDInsight-cluster](#vmorhdi)gebruiken en [Azure Kubernetes-service](#aksvnet) in dit artikel voor meer informatie.

1. Ga in het Azure Portal naar de opslag die is gekoppeld aan uw werk ruimte.

   [![de opslag die aan de Azure Machine Learning-werk ruimte is gekoppeld](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. Selecteer op de pagina **Azure Storage** __firewalls en virtuele netwerken__.

   ![Het gebied firewalls en virtuele netwerken op de pagina Azure Storage in het Azure Portal](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. Voer op de pagina __firewalls en virtuele netwerken__ de volgende acties uit:
    - Selecteer __Geselecteerde netwerken__.
    - Selecteer onder __virtuele netwerken__de koppeling __bestaande virtuele netwerk toevoegen__ . Met deze actie wordt het virtuele netwerk waar uw Compute zich bevindt, toegevoegd (zie stap 1).

        > [!IMPORTANT]
        > Het opslag account moet zich in hetzelfde virtuele netwerk bevindt als de reken instanties of clusters die worden gebruikt voor training of deinterferentie.

    - Schakel het selectie vakje __vertrouwde micro soft-Services toegang geven tot dit opslag account__ in.

    > [!IMPORTANT]
    > Wanneer u werkt met de Azure Machine Learning SDK, moet uw ontwikkel omgeving verbinding kunnen maken met het Azure Storage-account. Wanneer het opslag account zich in een virtueel netwerk bevindt, moet de firewall toegang toestaan vanuit het IP-adres van de ontwikkel omgeving.
    >
    > Als u toegang tot het opslag account wilt inschakelen, gaat u naar de __firewalls en virtuele netwerken__ voor het opslag account *vanuit een webbrowser op de ontwikkelings-client*. Gebruik vervolgens het selectie vakje __uw client-IP-adres toevoegen__ om het IP-adres van de client toe te voegen aan het __adres bereik__. U kunt ook het veld __adres bereik__ gebruiken om hand matig het IP-adres van de ontwikkel omgeving in te voeren. Zodra het IP-adres voor de client is toegevoegd, heeft het toegang tot het opslag account met de SDK.

   [![het deel venster firewalls en virtuele netwerken in de Azure Portal](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

> [!IMPORTANT]
> U kunt het _standaard opslag account_ voor Azure machine learning of _niet-standaard opslag accounts_ in een virtueel netwerk plaatsen.
>
> Het standaard opslag account wordt automatisch ingericht wanneer u een werk ruimte maakt.
>
> Voor niet-standaard opslag accounts kunt u met de para meter `storage_account` in de [functie`Workspace.create()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) een aangepast opslag account opgeven op basis van de Azure-resource-id.

## <a name="use-azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage gen 2 gebruiken

Azure Data Lake Storage gen 2 is een reeks mogelijkheden voor big data Analytics, gebouwd op Azure Blob-opslag. Het kan worden gebruikt voor het opslaan van gegevens die worden gebruikt voor het trainen van modellen met Azure Machine Learning. 

Gebruik de volgende stappen om Data Lake Storage gen 2 te gebruiken in het virtuele netwerk van uw Azure Machine Learning-werk ruimte:

1. Maak een Azure Data Lake Storage gen 2-account. Zie [een Azure data Lake Storage Gen2 Storage-account maken](../storage/blobs/data-lake-storage-quickstart-create-account.md)voor meer informatie.

1. Volg de stappen 2-4 in de vorige sectie, [gebruik een opslag account voor uw werk ruimte](#use-a-storage-account-for-your-workspace)om het account in het virtuele netwerk te plaatsen.

Gebruik de volgende richt lijnen bij het gebruik van Azure Machine Learning met Data Lake Storage gen 2 in een virtueel netwerk:

* Als u de __SDK gebruikt om een gegevensset te maken__en het systeem waarop de code __wordt uitgevoerd, zich niet in het virtuele netwerk bevindt__, gebruikt u de para meter `validate=False`. Deze para meter slaat validatie over, wat mislukt als het systeem zich niet in hetzelfde virtuele netwerk bevindt als het opslag account. Zie de methode [from_files ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) voor meer informatie.

* Wanneer u Azure Machine Learning Compute-instantie of COMPUTE-cluster gebruikt voor het trainen van een model met behulp van de gegevensset, moet deze zich in hetzelfde virtuele netwerk bevinden als het opslag account.

## <a name="use-a-key-vault-instance-with-your-workspace"></a>Een sleutel kluis-exemplaar gebruiken met uw werk ruimte

Het sleutel kluis-exemplaar dat is gekoppeld aan de werk ruimte wordt gebruikt door Azure Machine Learning om de volgende referenties op te slaan:
* Het gekoppelde opslag account connection string
* Wacht woorden voor Azure container repository-instanties
* Verbindings reeksen naar gegevens archieven

Als u Azure Machine Learning experimenten wilt gebruiken met Azure Key Vault achter een virtueel netwerk, gebruikt u de volgende stappen:

1. Ga naar de sleutel kluis die is gekoppeld aan de werk ruimte.

   [![de sleutel kluis die is gekoppeld aan de Azure Machine Learning-werk ruimte](./media/how-to-enable-virtual-network/workspace-key-vault.png)](./media/how-to-enable-virtual-network/workspace-key-vault.png#lightbox)

1. Selecteer op de pagina **Key Vault** in het linkerdeel venster __firewalls en virtuele netwerken__.

   ![De sectie firewalls en virtuele netwerken in het deel venster Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)

1. Voer op de pagina __firewalls en virtuele netwerken__ de volgende acties uit:
    - Selecteer onder __toegang toestaan vanuit__de optie __geselecteerde netwerken__.
    - Selecteer onder __virtuele netwerken__ __bestaande virtuele netwerken toevoegen__ om het virtuele netwerk toe te voegen waarin uw experimenten worden berekend.
    - Onder __vertrouwde micro soft-Services toestaan deze firewall te omzeilen__, selecteert u __Ja__.

   [![de sectie firewalls en virtuele netwerken in het Key Vault deel venster](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)

<a id="amlcompute"></a>

## <a name="compute-instance"></a>Een Machine Learning Compute gebruiken

Als u een Azure Machine Learning Compute-exemplaar of reken cluster in een virtueel netwerk wilt gebruiken, moet aan de volgende netwerk vereisten worden voldaan:

> [!div class="checklist"]
> * Het virtuele netwerk moet zich in hetzelfde abonnement en dezelfde regio bevinden als de Azure Machine Learning-werk ruimte.
> * Het subnet dat is opgegeven voor het reken proces of het cluster moet voldoende niet-toegewezen IP-adressen hebben om het aantal Vm's te kunnen ondersteunen. Als het subnet onvoldoende niet-toegewezen IP-adressen heeft, wordt een reken cluster gedeeltelijk toegewezen.
> * Controleer of het beveiligings beleid of de vergren delingen in het abonnement of de resource groep van het virtuele netwerk beperkt zijn tot de machtigingen voor het beheren van het virtuele netwerk. Als u het virtuele netwerk wilt beveiligen door verkeer te beperken, moet u sommige poorten voor de compute-service geopend laten. Zie de sectie [vereiste poorten](#mlcports) voor meer informatie.
> * Als u meerdere reken instanties of clusters in één virtueel netwerk wilt plaatsen, moet u mogelijk een quotum verhoging aanvragen voor een of meer van uw resources.
> * Als de Azure Storage account (s) voor de werk ruimte ook worden beveiligd in een virtueel netwerk, moeten ze zich in hetzelfde virtuele netwerk bevinden als de Azure Machine Learning Reken instantie of het cluster. 

> [!TIP]
> Het Machine Learning Reken exemplaar of cluster wijst automatisch extra netwerk bronnen toe aan de resource groep die het virtuele netwerk bevat. Voor elk reken exemplaar of cluster wijst de service de volgende bronnen toe:
> 
> * Eén netwerk beveiligings groep
> * Eén openbaar IP-adres
> * Een load balancer
> 
> De beperkingen die voor deze resources gelden, worden bepaald door de [resourcequota](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) van het abonnement.


### <a id="mlcports"></a>Vereiste poorten

Machine Learning Compute maakt momenteel gebruik van de Azure Batch-service om virtuele machines in het opgegeven virtuele netwerk in te richten. Het subnet moet inkomende communicatie vanuit de batch-service toestaan. U gebruikt deze communicatie om uitvoeringen uit te voeren op de Machine Learning Compute knooppunten en te communiceren met Azure Storage en andere resources. De batch-service voegt netwerk beveiligings groepen (Nsg's) toe op het niveau van netwerk interfaces (Nic's) die zijn gekoppeld aan Vm's. Met deze netwerkbeveiligingsgroepen worden automatisch binnenkomende en uitgaande regels geconfigureerd om het volgende verkeer toe te staan:

- Binnenkomend TCP-verkeer op de poorten 29876 en 29877 van een __service label__ van __BatchNodeManagement__.

    ![Een regel voor binnenkomende verbindingen die gebruikmaakt van het BatchNodeManagement-service label](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- Beschrijving Binnenkomend TCP-verkeer op poort 22 om externe toegang toe te staan. Gebruik deze poort alleen als u verbinding wilt maken met behulp van SSH op het open bare IP-adres.

- Uitgaand verkeer op een willekeurige poort naar het virtuele netwerk.

- Uitgaand verkeer op een willekeurige poort naar internet.

- Voor het binnenkomende TCP-verkeer van reken instanties op poort 44224 van een __service-tag__ van __AzureMachineLearning__.

Wees voorzichtig als u binnenkomende of uitgaande regels toevoegt of wijzigt in netwerkbeveiligingsgroepen die door Batch zijn geconfigureerd. Als een NSG communicatie met de reken knooppunten blokkeert, stelt de compute-service de status van de reken knooppunten in op onbruikbaar.

U hoeft Nsg's niet op subnetniveau op te geven omdat de Azure Batch-service zijn eigen Nsg's configureert. Als het opgegeven subnet echter is gekoppeld Nsg's of een firewall, configureert u de regels voor binnenkomende en uitgaande verbindingen zoals eerder beschreven.

De NSG-regel configuratie in de Azure Portal wordt weer gegeven in de volgende installatie kopieën:

[![de regels voor binnenkomende NSG voor Machine Learning Compute](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png)](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png#lightbox)

![De regels voor uitgaande NSG voor Machine Learning Compute](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a id="limiting-outbound-from-vnet"></a>Uitgaande connectiviteit vanuit het virtuele netwerk beperken

Als u de standaard regels voor uitgaande verbindingen niet wilt gebruiken en u de uitgaande toegang van uw virtuele netwerk wilt beperken, gebruikt u de volgende stappen:

- Uitgaande Internet verbinding weigeren met behulp van de NSG-regels.

- Beperk het uitgaande verkeer voor een __reken instantie__ of een __berekenings cluster__tot de volgende items:
   - Azure Storage, door gebruik te maken van de __service tag__ __Storage. regionaam__. Waarbij `{RegionName}` de naam is van een Azure-regio.
   - Azure Container Registry, met behulp van de __service-tag__ __AzureContainerRegistry. regionaam__. Waarbij `{RegionName}` de naam is van een Azure-regio.
   - Azure Machine Learning, met behulp van het __service label__ __AzureMachineLearning__
   
- Voor een __reken instantie__voegt u ook de volgende items toe:
   - Azure Resource Manager, met behulp van het __service label__ __AzureResourceManager__
   - Azure Active Directory, met behulp van het __service label__ __AzureActiveDirectory__

De NSG-regel configuratie in de Azure Portal wordt weer gegeven in de volgende afbeelding:

[![de regels voor uitgaande NSG voor Machine Learning Compute](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png#lightbox)

> [!NOTE]
> Als u gebruikmaakt van standaard docker-installatie kopieën van micro soft, en het inschakelen van door de gebruiker beheerde afhankelijkheden, moet u ook een __service-tag__ van __MicrosoftContainerRegistry. Region_Name__ (bijvoorbeeld MicrosoftContainerRegistry. eastus) gebruiken.
>
> Deze configuratie is vereist wanneer u code die vergelijkbaar is met de volgende fragmentten als onderdeel van uw trainings scripts:
>
> __RunConfig-training__
> ```python
> # create a new runconfig object
> run_config = RunConfiguration()
> 
> # configure Docker 
> run_config.environment.docker.enabled = True
> # For GPU, use DEFAULT_GPU_IMAGE
> run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE 
> run_config.environment.python.user_managed_dependencies = True
> ```
>
> __Estimator-training__
> ```python
> est = Estimator(source_directory='.',
>                 script_params=script_params,
>                 compute_target='local',
>                 entry_script='dummy_train.py',
>                 user_managed=True)
> run = exp.submit(est)
> ```

### <a name="user-defined-routes-for-forced-tunneling"></a>Door de gebruiker gedefinieerde routes voor geforceerde tunneling

Als u gebruik wilt maken van geforceerde tunneling met de Machine Learning Compute, voegt u door de [gebruiker gedefinieerde routes (udr's)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) toe aan het subnet dat de reken resource bevat.

* Stel een UDR in voor elk IP-adres dat wordt gebruikt door de Azure Batch-service in de regio waar uw resources bestaan. Deze Udr's inschakelen de batch-service om te communiceren met reken knooppunten voor het plannen van taken. Gebruik een van de volgende methoden om een lijst met IP-adressen van de batch-service te verkrijgen:

    * Down load de [Azure IP-bereiken en-service Tags](https://www.microsoft.com/download/details.aspx?id=56519) en zoek het bestand op `BatchNodeManagement.<region>`, waarbij `<region>` uw Azure-regio is.

    * Gebruik de [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) om de informatie te downloaden. In het volgende voor beeld worden de IP-adres gegevens gedownload en worden de gegevens voor de regio VS Oost 2 gefilterd:

        ```azurecli-interactive
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
        ```

* Uitgaand verkeer naar Azure Storage mag niet worden geblokkeerd door uw on-premises netwerk apparaat. De Url's hebben de vorm `<account>.table.core.windows.net`, `<account>.queue.core.windows.net`en `<account>.blob.core.windows.net`.

Wanneer u de Udr's toevoegt, definieert u de route voor elk gerelateerde IP-adres voorvoegsel voor batch en stelt u het __type volgende hop__ in op __Internet__. In de volgende afbeelding ziet u een voor beeld van deze UDR in de Azure Portal:

![Voor beeld van een UDR voor een adres voorvoegsel](./media/how-to-enable-virtual-network/user-defined-route.png)

Zie [een Azure batch groep maken in een virtueel netwerk](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling)voor meer informatie.

### <a name="create-a-compute-cluster-in-a-virtual-network"></a>Een berekenings cluster maken in een virtueel netwerk

Als u een Machine Learning Compute cluster wilt maken, gebruikt u de volgende stappen:

1. Selecteer uw Azure Machine Learning-werk ruimte in de [Azure Portal](https://portal.azure.com).

1. Selecteer in de sectie __toepassing__ __Compute__en selecteer vervolgens __Compute toevoegen__.

1. Voer de volgende acties uit om deze Compute-resource te configureren voor het gebruik van een virtueel netwerk:

    a. Voor __netwerk configuratie__selecteert u __Geavanceerd__.

    b. Selecteer de resource groep met het virtuele netwerk in de vervolg keuzelijst __resource groep__ .

    c. Selecteer in de vervolg keuzelijst __virtueel netwerk__ het virtuele netwerk dat het subnet bevat.

    d. Selecteer in de vervolg keuzelijst __subnet__ het subnet dat u wilt gebruiken.

   ![De instellingen van het virtuele netwerk voor Machine Learning Compute](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

U kunt ook een Machine Learning Compute cluster maken met behulp van de Azure Machine Learning SDK. Met de volgende code wordt een nieuw Machine Learning Compute Cluster gemaakt in het `default` subnet van een virtueel netwerk met de naam `mynetwork`:

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# The Azure virtual network name, subnet, and resource group
vnet_name = 'mynetwork'
subnet_name = 'default'
vnet_resourcegroup_name = 'mygroup'

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print("Found existing cpucluster")
except ComputeTargetException:
    print("Creating new cpucluster")

    # Specify the configuration for the new cluster
    compute_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                           min_nodes=0,
                                                           max_nodes=4,
                                                           vnet_resourcegroup_name=vnet_resourcegroup_name,
                                                           vnet_name=vnet_name,
                                                           subnet_name=subnet_name)

    # Create the cluster with the specified name and configuration
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

    # Wait for the cluster to be completed, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

Wanneer het maken van het proces is voltooid, traint u uw model met behulp van het cluster in een experiment. Zie voor meer informatie [een berekenings doel selecteren en gebruiken voor training](how-to-set-up-training-targets.md).

## <a name="use-azure-databricks"></a>Azure Databricks gebruiken

Als u Azure Databricks wilt gebruiken in een virtueel netwerk met uw werk ruimte, moet aan de volgende vereisten worden voldaan:

> [!div class="checklist"]
> * Het virtuele netwerk moet zich in hetzelfde abonnement en dezelfde regio bevinden als de Azure Machine Learning-werk ruimte.
> * Als de Azure Storage account (s) voor de werk ruimte ook worden beveiligd in een virtueel netwerk, moeten ze zich in hetzelfde virtuele netwerk bevinden als het Azure Databricks-cluster.
> * Naast de __databricks-particuliere__ en __databricks-open bare__ subnetten die worden gebruikt door Azure Databricks, is ook het __standaard__ subnet dat voor het virtuele netwerk is gemaakt, vereist.

Zie [deploying Azure Databricks in uw Azure Virtual Network](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html)voor specifieke informatie over het gebruik van Azure Databricks met een virtueel netwerk.

<a id="vmorhdi"></a>

## <a name="use-a-virtual-machine-or-hdinsight-cluster"></a>Een virtuele machine of een HDInsight-cluster gebruiken

> [!IMPORTANT]
> Azure Machine Learning ondersteunt alleen virtuele machines waarop Ubuntu wordt uitgevoerd.

Als u een virtuele machine of een Azure HDInsight-cluster in een virtueel netwerk met uw werk ruimte wilt gebruiken, gebruikt u de volgende stappen:

1. Maak een virtuele machine of een HDInsight-cluster met behulp van de Azure Portal of de Azure CLI en plaats het cluster in een virtueel Azure-netwerk. Raadpleeg voor meer informatie de volgende artikelen:
    * [Virtuele Azure-netwerken voor Linux-Vm's maken en beheren](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [HDInsight uitbreiden met behulp van een virtueel Azure-netwerk](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. Als u Azure Machine Learning wilt toestaan te communiceren met de SSH-poort op de virtuele machine of het cluster, configureert u een bron vermelding voor de netwerk beveiligings groep. De SSH-poort is doorgaans poort 22. Voer de volgende acties uit om verkeer van deze bron toe te staan:

    * Selecteer in de vervolg keuzelijst __bron__ de optie __service label__.

    * Selecteer __AzureMachineLearning__in de vervolg keuzelijst __bron service label__ .

    * Selecteer __*__ in de vervolg keuzelijst __bron poort bereik__ .

    * Selecteer in de vervolg keuzelijst __bestemming__ __een__.

    * Selecteer __22__in de vervolg keuzelijst __doel poort bereik__ .

    * Onder __protocol__selecteert u __een__.

    * Selecteer onder __actie__ __toestaan__.

   ![Binnenkomende regels voor het experimenteren op een VM-of HDInsight-cluster in een virtueel netwerk](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    Behoud de standaard regels voor uitgaande verbindingen voor de netwerk beveiligings groep. Zie de standaard beveiligings regels in [beveiligings groepen](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)voor meer informatie.

    Als u de standaard regels voor uitgaand verkeer niet wilt gebruiken en u de uitgaande toegang van uw virtuele netwerk wilt beperken, raadpleegt u de sectie [uitgaande verbindingen beperken via het virtuele netwerk](#limiting-outbound-from-vnet) .

1. Koppel de virtuele machine of het HDInsight-cluster aan uw Azure Machine Learning-werk ruimte. Zie [Compute-doelen voor model training instellen](how-to-set-up-training-targets.md)voor meer informatie.

<a id="aksvnet"></a>

## <a name="use-azure-kubernetes-service-aks"></a>Azure Kubernetes service (AKS) gebruiken

Voer de volgende stappen uit om AKS toe te voegen aan uw werk ruimte in een virtueel netwerk:

> [!IMPORTANT]
> Voordat u aan de volgende procedure begint, volgt u de vereisten in het onderdeel [geavanceerde netwerken configureren in azure Kubernetes service (AKS)](https://docs.microsoft.com/azure/aks/configure-advanced-networking#prerequisites) hoe de IP-adres sering voor uw cluster kan worden gepland.
>
> Het AKS-exemplaar en het virtuele Azure-netwerk moeten zich in dezelfde regio bevinden. Als u de Azure Storage account (s) die door de werk ruimte in een virtueel netwerk worden gebruikt, beveiligt, moeten ze zich in hetzelfde virtuele netwerk bevinden als het AKS-exemplaar.

1. Zorg er in het [Azure Portal](https://portal.azure.com)voor dat de NSG die het virtuele netwerk beheert, een regel voor binnenkomende verbindingen heeft die is ingeschakeld voor Azure machine learning met behulp van __AzureMachineLearning__ als **bron**.

    [Reken venster ![Azure Machine Learning toevoegen](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-aml.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-aml.png#lightbox)

1. Selecteer uw Azure Machine Learning-werk ruimte.

1. Selecteer in de sectie __toepassing__ __Compute__en selecteer vervolgens __Compute toevoegen__.

1. Voer de volgende acties uit om deze Compute-resource te configureren voor het gebruik van een virtueel netwerk:

    - Voor __netwerk configuratie__selecteert u __Geavanceerd__.

    - Selecteer de resource groep met het virtuele netwerk in de vervolg keuzelijst __resource groep__ .

    - Selecteer in de vervolg keuzelijst __virtueel netwerk__ het virtuele netwerk dat het subnet bevat.

    - Selecteer in de vervolg keuzelijst __subnet__ het subnet.

    - Voer in het vak __Kubernetes service adres bereik__ het adres bereik van de Kubernetes-service in. Dit adres bereik maakt gebruik van een CIDR-notatie (Classless Inter-Domain Routing) IP-bereik voor het definiëren van de IP-adressen die beschikbaar zijn voor het cluster. Het mag niet overlappen met subnet IP-bereiken (bijvoorbeeld 10.0.0.0/16).

    - Voer in het vak __IP-adres van KUBERNETES DNS__ -service het IP-adres van de Kubernetes DNS-service in. Dit IP-adres wordt toegewezen aan de DNS-service Kubernetes. Deze moet zich in het adres bereik van de Kubernetes-service bevallen (bijvoorbeeld 10.0.0.10).

    - Voer in het vak __docker Bridge-adres__ het adres van de docker-brug in. Dit IP-adres wordt toegewezen aan docker-brug. De waarde mag zich niet in een IP-bereik van het subnet of in het Kubernetes (bijvoorbeeld 172.17.0.1/16) bevinden.

   ![Azure Machine Learning: Machine Learning Compute instellingen van het virtuele netwerk](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. Zorg ervoor dat de NSG-groep die het virtuele netwerk beheert, een binnenkomende beveiligings regel voor het Score-eind punt heeft ingeschakeld, zodat deze kan worden aangeroepen buiten het virtuele netwerk.
   > [!IMPORTANT]
   > Behoud de standaard regels voor uitgaande verbindingen voor de NSG. Zie de standaard beveiligings regels in [beveiligings groepen](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)voor meer informatie.

   [Een regel voor binnenkomende beveiliging ![](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png#lightbox)

U kunt ook de Azure Machine Learning SDK gebruiken om de Azure Kubernetes-service toe te voegen aan een virtueel netwerk. Als u al een AKS-cluster in een virtueel netwerk hebt, koppelt u dit aan de werk ruimte, zoals wordt beschreven in [Deploying to aks](how-to-deploy-and-where.md). Met de volgende code wordt een nieuw AKS-exemplaar gemaakt in het `default` subnet van een virtueel netwerk met de naam `mynetwork`:

```python
from azureml.core.compute import ComputeTarget, AksCompute

# Create the compute configuration and set virtual network information
config = AksCompute.provisioning_configuration(location="eastus2")
config.vnet_resourcegroup_name = "mygroup"
config.vnet_name = "mynetwork"
config.subnet_name = "default"
config.service_cidr = "10.0.0.0/16"
config.dns_service_ip = "10.0.0.10"
config.docker_bridge_cidr = "172.17.0.1/16"

# Create the compute target
aks_target = ComputeTarget.create(workspace=ws,
                                  name="myaks",
                                  provisioning_configuration=config)
```

Wanneer het maken van het proces is voltooid, kunt u het decoderen of model leren uitvoeren op een AKS-cluster achter een virtueel netwerk. Zie [implementeren op AKS](how-to-deploy-and-where.md)voor meer informatie.

### <a name="use-private-ips-with-azure-kubernetes-service"></a>Privé IPs gebruiken met de Azure Kubernetes-service

Standaard wordt een openbaar IP-adres toegewezen aan AKS-implementaties. Wanneer u AKS in een virtueel netwerk gebruikt, kunt u in plaats daarvan een privé-IP-adres gebruiken. Privé-IP-adressen zijn alleen toegankelijk vanuit het virtuele netwerk of de gekoppelde netwerken.

Een privé-IP-adres wordt ingeschakeld door AKS te configureren voor gebruik van een _interne Load Balancer_. 

> [!IMPORTANT]
> U kunt geen persoonlijk IP-adres inschakelen bij het maken van het Azure Kubernetes-service cluster. Het moet worden ingeschakeld als een update van een bestaand cluster.

Het volgende code fragment laat zien hoe u **een nieuw AKS-cluster maakt**en het vervolgens bijwerkt voor gebruik van een privé-IP/interne Load Balancer:

```python
import azureml.core
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute import AksCompute, ComputeTarget

# Verify that cluster does not exist already
try:
    aks_target = AksCompute(workspace=ws, name=aks_cluster_name)
    print("Found existing aks cluster")

except:
    print("Creating new aks cluster")

    # Create AKS configuration
    prov_config = AksCompute.provisioning_configuration(location = "eastus2")
    # Set info for existing virtual network to create the cluster in
    prov_config.vnet_resourcegroup_name = "myvnetresourcegroup"
    prov_config.vnet_name = "myvnetname"
    prov_config.service_cidr = "10.0.0.0/16"
    prov_config.dns_service_ip = "10.0.0.10"
    prov_config.subnet_name = "default"
    prov_config.docker_bridge_cidr = "172.17.0.1/16"

    # Create compute target
    aks_target = ComputeTarget.create(workspace = ws, name = “myaks”, provisioning_configuration = prov_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
    
    # Update AKS configuration to use an internal load balancer
    update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", "default")
    aks_target.update(update_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
```

__Azure CLI__

```azurecli-interactive
az rest --method put --uri https://management.azure.com"/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.ContainerService/managedClusters/<aks-resource-id>?api-version=2018-11-19 --body @body.json
```

De inhoud van het `body.json` bestand waarnaar wordt verwezen door de opdracht, is vergelijkbaar met het volgende JSON-document:

```json
{ 
    "location": “<region>”, 
    "properties": { 
        "resourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.ContainerService/managedClusters/<aks-resource-id>", 
        "computeType": "AKS", 
        "provisioningState": "Succeeded", 
        "properties": { 
            "loadBalancerType": "InternalLoadBalancer", 
            "agentCount": <agent-count>, 
            "agentVmSize": "vm-size", 
            "clusterFqdn": "<cluster-fqdn>" 
        } 
    } 
} 
```

> [!NOTE]
> Op dit moment kunt u de load balancer niet configureren wanneer u een __toevoeg__ bewerking uitvoert op een bestaand cluster. U moet eerst het cluster koppelen en vervolgens een update bewerking uitvoeren om de load balancer te wijzigen.

Zie voor meer informatie over het gebruik van de interne load balancer met AKS [interne Load Balancer gebruiken met de Azure Kubernetes-service](/azure/aks/internal-lb).

## <a name="use-azure-firewall"></a>Azure Firewall gebruiken

Wanneer u Azure Firewall gebruikt, moet u een netwerk regel configureren om verkeer toe te staan van en naar de volgende adressen:

- `*.batchai.core.windows.net`
- `ml.azure.com`
- `*.azureml.ms`
- `*.experiments.azureml.net`
- `*.modelmanagement.azureml.net`
- `mlworkspace.azure.ai`
- `*.aether.ms`

Wanneer u de regel toevoegt, stelt u het __protocol__ in op elke en de poorten die moeten worden `*`.

Zie [Azure firewall implementeren en configureren](/azure/firewall/tutorial-firewall-deploy-portal#configure-a-network-rule)voor meer informatie over het configureren van een netwerk regel.

## <a name="use-azure-container-registry"></a>Azure Container Registry gebruiken

Wanneer u een virtueel netwerk met Azure Machine Learning gebruikt, __moet u__ de Azure container Registry voor de werk ruimte in het virtuele netwerk niet plaatsen. Deze configuratie wordt niet ondersteund.

## <a name="next-steps"></a>Volgende stappen

* [Trainings omgevingen instellen](how-to-set-up-training-targets.md)
* [Implementatie van modellen](how-to-deploy-and-where.md)
* [Modellen veilig implementeren met SSL](how-to-secure-web-service.md)
