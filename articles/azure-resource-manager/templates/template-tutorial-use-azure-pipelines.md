---
title: Continue integratie met Azure-pijplijnen
description: Meer informatie over het continu bouwen, testen en implementeren van Azure Resource Manager sjablonen.
author: mumian
ms.date: 10/29/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: e7d6b23aa3f35c99cf03f855152b2b231a60a965
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/13/2020
ms.locfileid: "75921625"
---
# <a name="tutorial-continuous-integration-of-azure-resource-manager-templates-with-azure-pipelines"></a>Zelf studie: doorlopende integratie van Azure Resource Manager sjablonen met Azure-pijp lijnen

Meer informatie over het gebruik van Azure-pijp lijnen om voortdurend Azure Resource Manager-sjabloon projecten te bouwen en implementeren.

Azure DevOps biedt ontwikkelaars Services om teams te ondersteunen bij het plannen van werk, het samen werken met code ontwikkeling en het bouwen en implementeren van toepassingen. Ontwikkel aars kunnen werken in de Cloud met behulp van Azure DevOps Services. Azure DevOps biedt een geïntegreerde set functies die u via uw webbrowser of IDE-client kunt openen. Azure-pijp lijn is een van deze functies. Azure-pijp lijnen is een volledig uitgeruste service voor continue integratie (CI) en continue levering (CD). Het werkt met uw favoriete Git-provider en kan worden geïmplementeerd in de meeste belang rijke Cloud Services. Vervolgens kunt u het bouwen, testen en implementeren van uw code automatiseren naar Microsoft Azure, Google Cloud Platform of Amazon Web Services.

Deze zelf studie is ontworpen voor Azure Resource Manager sjabloon ontwikkelaars die nieuwe Azure DevOps Services en Azure-pijp lijnen zijn. Als u al bekend bent met GitHub en DevOps, kunt u door gaan met [het maken van een pijp lijn](#create-a-pipeline).

> [!NOTE]
> Kies een project naam. Wanneer u de zelf studie door lopen, vervangt u de **AzureRmPipeline** door de naam van uw project.

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Een GitHub-opslagplaats voorbereiden
> * Een Azure DevOps-project maken
> * Een Azure-pijp lijn maken
> * De implementatie van de pijp lijn controleren
> * De sjabloon bijwerken en opnieuw implementeren
> * Resources opschonen

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt voltooien, hebt u het volgende nodig:

* **Een github-account**, waar u het gebruikt voor het maken van een opslag plaats voor uw sjablonen. Als u nog geen account hebt, kunt u er [gratis een maken](https://github.com). Zie [Build github-opslag](/azure/devops/pipelines/repos/github)plaatsen voor meer informatie over het gebruik van github-opslag plaatsen.
* **Installeer Git**. In deze zelfstudie instructie wordt *Git Bash* of *git shell*gebruikt. Zie [git installeren]( https://www.atlassian.com/git/tutorials/install-git)voor instructies.
* **Een Azure DevOps-organisatie**. Als u er nog geen hebt, kunt u er gratis een maken. Zie [een organisatie-of project verzameling maken]( https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops).
* Visual Studio code met de extensie Resource Manager-Hulpprogram Ma's. Zie [Visual Studio code gebruiken om Azure Resource Manager sjablonen te maken](use-vs-code-to-create-template.md).

## <a name="prepare-a-github-repository"></a>Een GitHub-opslagplaats voorbereiden

GitHub wordt gebruikt voor het opslaan van de project bron code, inclusief Resource Manager-sjablonen. Zie [opslag plaatsen die worden ondersteund door Azure DevOps](/azure/devops/pipelines/repos/?view=azure-devops#supported-repository-types)voor andere ondersteunde opslag plaatsen.

### <a name="create-a-github-repository"></a>Een GitHub-opslag plaats maken

Als u geen GitHub-account hebt, raadpleegt u [vereisten](#prerequisites).

1. Meld u aan bij [github](https://github.com).
2. Selecteer uw account afbeelding in de rechter bovenhoek en selecteer vervolgens **uw opslag**plaatsen.

    ![GitHub-opslag plaats Azure Resource Manager Azure DevOps Azure-pijp lijnen maken](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-repository.png)

1. Selecteer **Nieuw**, een groene knop.
1. Voer in **naam van opslag plaats**een naam in voor de opslag plaats.  Bijvoorbeeld **AzureRmPipeline-opslag plaats**. Vergeet niet om een van de **AzureRmPipeline** te vervangen door de naam van uw project. U kunt **openbaar** of **privé** selecteren als u deze zelf studie wilt door lopen. En selecteer vervolgens **opslag plaats maken**.
1. Noteer de URL. De URL van de opslag plaats is de volgende indeling:

    ```url
    https://github.com/[YourAccountName]/[YourRepositoryName]
    ```

Deze opslag plaats wordt een *externe opslag plaats*genoemd. Elk van de ontwikkel aars van hetzelfde project kan zijn/haar eigen *lokale opslag plaats*klonen en de wijzigingen in de externe opslag plaats samen voegen.

### <a name="clone-the-remote-repository"></a>De externe opslag plaats klonen

1. Open git-shell of git-bash.  Zie [Vereisten](#prerequisites).
1. Controleer of de huidige map **github**is.
1. Voer de volgende opdracht uit:

    ```bash
    git clone https://github.com/[YourAccountName]/[YourGitHubRepositoryName]
    cd [YourGitHubRepositoryName]
    mkdir CreateAzureStorage
    cd CreateAzureStorage
    pwd
    ```

    Vervang **[YourAccountName]** door de naam van uw github-account en vervang **[YourGitHubRepositoryName]** door de naam van de opslag plaats die u in de vorige procedure hebt gemaakt.

    In de volgende scherm afbeelding ziet u een voor beeld.

    ![GitHub bash Azure Resource Manager Azure DevOps Azure-pijp lijnen maken](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-bash.png)

De map **CreateAzureStorage** is de map waarin de sjabloon is opgeslagen. De opdracht **pwd** toont het mappad. In de volgende procedure slaat u de sjabloon op in het pad.

### <a name="download-a-quickstart-template"></a>Een Quick Start-sjabloon downloaden

In plaats van een sjabloon te maken, kunt u een Quick Start- [sjabloon]( https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json)downloaden. Met deze sjabloon maakt u een Azure Storage-account.

1. Open Visual Studio code. Zie [Vereisten](#prerequisites).
2. Open de sjabloon met de volgende URL:

    ```URL
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. Sla het bestand op als **azuredeploy. json** in de map **CreateAzureStorage** . Zowel de naam van de map als de bestands naam worden gebruikt zoals in de pijp lijn.  Als u deze namen wijzigt, moet u de namen bijwerken die worden gebruikt in de pijp lijn.

### <a name="push-the-template-to-the-remote-repository"></a>De sjabloon naar de externe opslag plaats pushen

De azuredeploy. json is toegevoegd aan de lokale opslag plaats. Vervolgens uploadt u de sjabloon naar de externe opslag plaats.

1. Open *git-shell* of *Git-Bash*als deze niet is geopend.
1. Wijzig de map in de map CreateAzureStorage in uw lokale opslag plaats.
1. Controleer of het bestand **azuredeploy. json** zich in de map bevindt.
1. Voer de volgende opdracht uit:

    ```bash
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    Er kan een waarschuwing over LF worden weer gegeven. U kunt de waarschuwing negeren. **Master** is de Master vertakking.  Doorgaans maakt u een vertakking voor elke update. Voor het vereenvoudigen van de zelf studie gebruikt u de hoofd vertakking rechtstreeks.
1. Blader vanuit een browser naar uw GitHub-opslag plaats.  De URL is **https://github.com/ [YourAccountName]/[YourGitHubRepository]** . De map **CreateAzureStorage** en **Azuredeploy. json** worden weer geven in de map.

Tot nu toe hebt u een GitHub-opslag plaats gemaakt en een sjabloon geüpload naar de opslag plaats.

## <a name="create-a-devops-project"></a>Een DevOps-project maken

Er is een DevOps-organisatie vereist voordat u kunt door gaan met de volgende procedure.  Als u er nog geen hebt, raadpleegt u de [vereisten](#prerequisites).

1. Meld u aan bij [Azure DevOps](https://dev.azure.com).
1. Selecteer een DevOps-organisatie aan de linkerkant.

    ![Azure DevOps-project Azure Resource Manager Azure DevOps Azure-pijp lijnen maken](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-create-devops-project.png)

1. Selecteer **Project maken**. Als u geen projecten hebt, wordt de pagina project maken automatisch geopend.
1. Voer de volgende waarden in:

    * **Project naam**: Voer een project naam in. U kunt de project naam gebruiken die u aan het begin van de zelf studie hebt gekozen.
    * **Versie beheer**: Selecteer **Git**. Mogelijk moet u **Geavanceerd** uitvouwen om **versie beheer**weer te geven.

    Gebruik de standaard waarde voor de andere eigenschappen.
1. Selecteer **Project maken**.

Maak een service verbinding die wordt gebruikt voor het implementeren van projecten in Azure.

1. Selecteer **project instellingen** aan de onderkant van het linkermenu.
1. Selecteer **service verbindingen** onder **pijp lijnen**.
1. Selecteer **nieuwe service verbinding**en selecteer vervolgens **AzureResourceManager**.
1. Voer de volgende waarden in:

    * **Verbindings naam**: Voer een naam in voor de verbinding. Bijvoorbeeld **AzureRmPipeline-** verbindingen. Noteer deze naam, u hebt de naam nodig wanneer u de pijp lijn maakt.
    * **Bereik niveau**: Selecteer **abonnement**.
    * **Abonnement**: selecteer uw abonnement.
    * **Resource groep**: laat het veld leeg.
    * **Alle pijp lijnen toestaan deze verbinding te gebruiken**. geselecteerde
1. Selecteer **OK**.

## <a name="create-a-pipeline"></a>Een pijplijn maken

Tot nu toe hebt u de volgende taken voltooid.  Als u de vorige secties overs laat, omdat u bekend bent met GitHub en DevOps, moet u de taken volt ooien voordat u doorgaat.

- Maak een GitHub-opslag plaats en sla [deze sjabloon](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) op in de map **CreateAzureStorage** in de opslag plaats.
- Maak een DevOps-project en maak een Azure Resource Manager service verbinding.

Een pijp lijn maken met een stap voor het implementeren van een sjabloon:

1. Selecteer **pijp lijnen** in het menu links.
1. Selecteer **nieuwe pijp lijn**.
1. Op het tabblad **Verbinding maken** selecteert u **GitHub**. Voer uw GitHub-referenties in als u daarom wordt gevraagd en volg de instructies. Als het volgende scherm wordt weer gegeven, selecteert u **alleen opslag**plaatsen selecteren en controleert u of uw opslag plaats in de lijst voor komt voordat u **& installatie goed keuren**selecteert.

    ![Azure Resource Manager Azure DevOps Azure-pijp lijnen alleen opslag plaatsen selecteren](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-only-select-repositories.png)

1. Selecteer uw opslag plaats op het tabblad **selecteren** .  De standaard naam is **[YourAccountName]/[YourGitHubRepositoryName]** .
1. Selecteer op het tabblad **configureren** de optie **Start pijplijn**. Het bevat het pijplijn bestand **Azure-pipelines. yml** met twee script stappen.
1. Vervang de sectie **stappen** door de volgende YAML:

    ```yaml
    steps:
    - task: AzureResourceManagerTemplateDeployment@3
      inputs:
        deploymentScope: 'Resource Group'
        ConnectedServiceName: '[EnterYourServiceConnectionName]'
        subscriptionName: '[EnterTheTargetSubscriptionID]'
        action: 'Create Or Update Resource Group'
        resourceGroupName: '[EnterANewResourceGroupName]'
        location: 'Central US'
        templateLocation: 'Linked artifact'
        csmFile: 'CreateAzureStorage/azuredeploy.json'
        deploymentMode: 'Incremental'
    ```

    Het ziet er als volgt uit:

    ![Azure Resource Manager Azure DevOps Azure pipelines yaml](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-yml.png)

    Breng de volgende wijzigingen aan:

    * **deploymentScope**: Selecteer het implementatie bereik in de opties: `Management Group`, `Subscription` en `Resource Group`. Gebruik de **resource groep** in deze zelf studie. Zie [implementatie bereiken](deploy-rest.md#deployment-scope)voor meer informatie over de scopes.
    * **ConnectedServiceName**: Geef de naam van de service verbinding op die u eerder hebt gemaakt.
    * **Subscriptionname**: Geef de id van het doel abonnement op.
    * **actie**: de actie voor het **maken of bijwerken van de resource groep** heeft 2 acties-1. Maak een resource groep als er een nieuwe naam voor de resource groep wordt gegeven. twee. Implementeer de opgegeven sjabloon.
    * **resourceGroupName**: Geef een nieuwe naam op voor de resource groep. Bijvoorbeeld **AzureRmPipeline-RG**.
    * **locatie**: Geef de locatie voor de resource groep op.
    * **templateLocation**: als u een **gekoppeld artefact** hebt opgegeven, zoekt de taak het sjabloon bestand rechtstreeks vanuit de verbonden opslag plaats.
    * **csmFile** is het pad naar het sjabloon bestand. U hoeft geen sjabloon parameters bestand op te geven omdat alle para meters die in de sjabloon zijn gedefinieerd, standaard waarden hebben.

    Zie [implementatie taak voor Azure-resource groep](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment)en [Azure Resource Manager sjabloon implementatie taak](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md) voor meer informatie over de taak
1. Selecteer **Opslaan en uitvoeren**.
1. Selecteer **opslaan en voer** de bewerking opnieuw uit. Er wordt een kopie van het YAML-bestand opgeslagen in de verbonden opslag plaats. U kunt het YAML-bestand zien door naar uw opslag plaats te bladeren.
1. Controleer of de pijp lijn is uitgevoerd.

    ![Azure Resource Manager Azure DevOps Azure pipelines yaml](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-status.png)

## <a name="verify-the-deployment"></a>De implementatie controleren

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).
1. Open de resource groep. De naam is wat u hebt opgegeven in het YAML-bestand van de pijp lijn.  U ziet dat er één opslag account is gemaakt.  De naam van het opslag account begint met **Store**.
1. Selecteer de naam van het opslag account om deze te openen.
1. Selecteer **Eigenschappen**. Let op: de **replicatie** is **lokaal redundante opslag (LRS)** .

    ![Verificatie van de Azure DevOps Azure Pipelins-Portal Azure Resource Manager](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-portal-verification.png)

## <a name="update-and-redeploy"></a>Bijwerken en opnieuw implementeren

Wanneer u de sjabloon bijwerkt en de wijzigingen naar de externe opslag plaats pusht, worden de resources, het opslag account in dit geval automatisch bijgewerkt door de pijp lijn.

1. Open **azuredeploy. json** van uw lokale opslag plaats in Visual Studio code.
1. Werk de **DefaultValue** van **storageAccountType** bij naar **Standard_GRS**. Zie de volgende schermafbeelding:

    ![Azure Resource Manager Azure DevOps Azure pipelines-update yaml](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-update-yml.png)

1. Sla de wijzigingen op.
1. Push de wijzigingen naar de externe opslag plaats door de volgende opdrachten uit Git Bash/shell uit te voeren.

    ```bash
    git pull origin master
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    Met de eerste opdracht wordt de lokale opslag plaats gesynchroniseerd met de externe opslag plaats. Onthoud dat het YAML-bestand van de pijp lijn is toegevoegd aan de externe opslag plaats.

    Als de hoofd vertakking van de externe opslag plaats is bijgewerkt, wordt de pijp lijn opnieuw geactiveerd.

U kunt de wijzigingen controleren door de replicatie-eigenschap van het opslag account te controleren.  Zie [de implementatie controleren](#verify-the-deployment).

## <a name="clean-up-resources"></a>Resources opschonen

Schoon de geïmplementeerd Azure-resources, wanneer u deze niet meer nodig hebt, op door de resourcegroep te verwijderen.

1. Selecteer **Resourcegroep** in het linkermenu van Azure Portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.
4. Selecteer **Resourcegroep verwijderen** in het bovenste menu.

Misschien wilt u ook de GitHub-opslag plaats en het Azure DevOps-project verwijderen.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie maakt u een Azure DevOps-pijp lijn voor het implementeren van een Azure Resource Manager sjabloon. Voor meer informatie over hoe u Azure-resources implementeert in meerdere regio's en hoe u veilige implementatiemethoden gebruikt, raadpleegt u

> [!div class="nextstepaction"]
> [Veilige implementatiemethoden gebruiken](./deployment-manager-tutorial.md)
