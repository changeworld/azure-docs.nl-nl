---
title: Sjabloon implementatie scripts gebruiken | Microsoft Docs
description: Meer informatie over het gebruik van implementatie scripts in Azure Resource Manager-sjablonen.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: carmonm
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 01/24/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 21725e64bb359b2f11086baceb186605f010b796
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/22/2020
ms.locfileid: "77561456"
---
# <a name="tutorial-use-deployment-scripts-to-create-a-self-signed-certificate-preview"></a>Zelf studie: implementatie scripts gebruiken om een zelfondertekend certificaat te maken (preview)

Meer informatie over het gebruik van implementatie scripts in azure resource beheer sjablonen. Implementatie scripts kunnen worden gebruikt om aangepaste stappen uit te voeren die niet door Resource Manager-sjablonen kunnen worden uitgevoerd. U kunt bijvoorbeeld een zelfondertekend certificaat maken.  In deze zelf studie maakt u een sjabloon voor het implementeren van een Azure-sleutel kluis en gebruikt u vervolgens een `Microsoft.Resources/deploymentScripts` resource in dezelfde sjabloon om een certificaat te maken en vervolgens het certificaat toe te voegen aan de sleutel kluis. Zie [implementatie scripts gebruiken in azure Resource Manager-sjablonen](./deployment-script-template.md)voor meer informatie over het implementatie script.

> [!NOTE]
> Het implementatie script bevindt zich momenteel in de preview-versie. Als u deze wilt gebruiken, moet u [zich aanmelden voor de preview-versie](https://aka.ms/armtemplatepreviews).

> [!IMPORTANT]
> Er worden twee implementatie script resources, een opslag account en een container exemplaar, gemaakt in dezelfde resource groep voor het uitvoeren van scripts en het oplossen van problemen. Deze resources worden doorgaans verwijderd door de script service wanneer de uitvoering van het script wordt uitgevoerd in een Terminal status. Er worden kosten in rekening gebracht voor de resources totdat de resources zijn verwijderd. Zie [implementatie script bronnen opschonen](./deployment-script-template.md#clean-up-deployment-script-resources)voor meer informatie.

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Een snelstartsjabloon openen
> * De sjabloon bewerken
> * De sjabloon implementeren
> * Fouten opsporen in het script
> * Resources opschonen

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt voltooien, hebt u het volgende nodig:

* **[Visual Studio code](https://code.visualstudio.com/) met de extensie Resource Manager-hulpprogram ma's**. Zie [Visual Studio code gebruiken om Azure Resource Manager sjablonen te maken](./use-vs-code-to-create-template.md).

* **Een door de gebruiker toegewezen beheerde identiteit met de rol van Inzender op abonnements niveau**. Deze identiteit wordt gebruikt om implementatie scripts uit te voeren. Zie door de [gebruiker toegewezen beheerde identiteit](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#user-assigned-managed-identity)als u er een wilt maken. U hebt de identiteits-ID nodig wanneer u de sjabloon implementeert. De indeling van de identiteit is:

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  Gebruik het volgende Power shell-script om de ID op te halen door de naam van de resource groep en de naam van de identiteit op te geven.

  ```azurepowershell-interactive
  $idGroup = Read-Host -Prompt "Enter the resource group name for the managed identity"
  $idName = Read-Host -Prompt "Enter the name of the managed identity"

  $id = (Get-AzUserAssignedIdentity -resourcegroupname $idGroup -Name idName).Id
  ```

## <a name="open-a-quickstart-template"></a>Een snelstartsjabloon openen

In plaats van een sjabloon helemaal opnieuw te maken, opent u een sjabloon in [Azure-snelstartsjablonen](https://azure.microsoft.com/resources/templates/). Azure-snelstartsjablonen is een opslagplaats voor Resource Manager-sjablonen.

De sjabloon die in deze Quick Start wordt gebruikt, wordt [een Azure Key Vault en een geheim](https://azure.microsoft.com/resources/templates/101-key-vault-create/)genoemd. Met de sjabloon maakt u een sleutel kluis en voegt u vervolgens een geheim toe aan de sleutel kluis.

1. Selecteer in Visual Studio Code **Bestand**>**Bestand openen**.
2. Plak de volgende URL in **Bestandsnaam**:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-key-vault-create/azuredeploy.json
    ```

3. Selecteer **Openen** om het bestand te openen.
4. Selecteer **Bestand**>**Opslaan als** om het bestand op uw lokale computer op te slaan als **azuredeploy.json**.

## <a name="edit-the-template"></a>De sjabloon bewerken

Breng de volgende wijzigingen aan in de sjabloon:

### <a name="clean-up-the-template-optional"></a>De sjabloon opschonen (optioneel)

De oorspronkelijke sjabloon voegt een geheim toe aan de sleutel kluis.  Verwijder de volgende resource om de zelf studie te vereenvoudigen:

* **Micro soft. de sleutel kluis/kluizen/geheimen**

Verwijder de volgende twee parameter definities:

* **secretName**
* **secretValue**

Als u ervoor kiest om deze definities niet te verwijderen, moet u de parameter waarden opgeven tijdens de implementatie.

### <a name="configure-the-key-vault-access-policies"></a>Het toegangs beleid voor de sleutel kluis configureren

Het implementatie script voegt een certificaat toe aan de sleutel kluis. Configureer het toegangs beleid voor de sleutel kluis om toestemming te geven voor de beheerde identiteit:

1. Voeg een para meter toe om de beheerde identiteits-ID op te halen:

    ```json
    "identityId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the ID of the user-assigned managed identity."
      }
    },
    ```

    > [!NOTE]
    > De Resource Manager-sjabloon extensie van Visual Studio code kan nog geen implementatie scripts Format teren. Gebruik [SHIFT] + [ALT] + F niet om de deploymentScripts-resources op te maken, zoals in het volgende voor naam.

1. Voeg een para meter toe voor het configureren van het toegangs beleid voor de sleutel kluis zodat de beheerde identiteit certificaten kan toevoegen aan de sleutel kluis.

    ```json
    "certificatesPermissions": {
      "type": "array",
      "defaultValue": [
        "get",
        "list",
        "update",
        "create"
      ],
      "metadata": {
      "description": "Specifies the permissions to certificates in the vault. Valid values are: all, get, list, update, create, import, delete, recover, backup, restore, manage contacts, manage certificate authorities, get certificate authorities, list certificate authorities, set certificate authorities, delete certificate authorities."
      }
    }
    ```

1. Werk het bestaande sleutel kluis toegangs beleid bij naar:

    ```json
    "accessPolicies": [
      {
        "objectId": "[parameters('objectId')]",
        "tenantId": "[parameters('tenantId')]",
        "permissions": {
          "keys": "[parameters('keysPermissions')]",
          "secrets": "[parameters('secretsPermissions')]",
          "certificates": "[parameters('certificatesPermissions')]"
        }
      },
      {
        "objectId": "[reference(parameters('identityId'), '2018-11-30').principalId]",
        "tenantId": "[parameters('tenantId')]",
        "permissions": {
          "keys": "[parameters('keysPermissions')]",
          "secrets": "[parameters('secretsPermissions')]",
          "certificates": "[parameters('certificatesPermissions')]"
        }
      }
    ],
    ```

    Er zijn twee beleids regels gedefinieerd: één voor de aangemelde gebruiker en de andere voor de beheerde identiteit.  De aangemelde gebruiker heeft alleen de machtiging *lijst* nodig om de implementatie te verifiëren.  Om de zelf studie te vereenvoudigen, wordt hetzelfde certificaat toegewezen aan zowel de beheerde identiteit als de aangemelde gebruikers.

### <a name="add-the-deployment-script"></a>Het implementatie script toevoegen

1. Voeg drie para meters toe die worden gebruikt door het implementatie script.

    ```json
    "certificateName": {
      "type": "string",
      "defaultValue": "DeploymentScripts2019"
    },
    "subjectName": {
      "type": "string",
      "defaultValue": "CN=contoso.com"
    },
    "utcValue": {
      "type": "string",
      "defaultValue": "[utcNow()]"
    }
    ```

1. Een deploymentScripts-resource toevoegen:

    > [!NOTE]
    > Omdat de inline-implementatie scripts tussen dubbele aanhalings tekens staan, moeten de teken reeksen in de implementatie scripts in plaats daarvan tussen enkele aanhalings tekens worden geplaatst. Het escape-teken voor Power **&#92;** shell is.

    ```json
    {
      "type": "Microsoft.Resources/deploymentScripts",
      "apiVersion": "2019-10-01-preview",
      "name": "createAddCertificate",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.KeyVault/vaults', parameters('keyVaultName'))]"
      ],
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[parameters('identityId')]": {
          }
        }
      },
      "kind": "AzurePowerShell",
      "properties": {
        "forceUpdateTag": "[parameters('utcValue')]",
        "azPowerShellVersion": "3.0",
        "timeout": "PT30M",
        "arguments": "[format(' -vaultName {0} -certificateName {1} -subjectName {2}', parameters('keyVaultName'), parameters('certificateName'), parameters('subjectName'))]", // can pass an argument string, double quotes must be escaped
        "scriptContent": "
          param(
            [string] [Parameter(Mandatory=$true)] $vaultName,
            [string] [Parameter(Mandatory=$true)] $certificateName,
            [string] [Parameter(Mandatory=$true)] $subjectName
          )

          $ErrorActionPreference = 'Stop'
          $DeploymentScriptOutputs = @{}

          $existingCert = Get-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName

          if ($existingCert -and $existingCert.Certificate.Subject -eq $subjectName) {

            Write-Host 'Certificate $certificateName in vault $vaultName is already present.'

            $DeploymentScriptOutputs['certThumbprint'] = $existingCert.Thumbprint
            $existingCert | Out-String
          }
          else {
            $policy = New-AzKeyVaultCertificatePolicy -SubjectName $subjectName -IssuerName Self -ValidityInMonths 12 -Verbose

            # private key is added as a secret that can be retrieved in the Resource Manager template
            Add-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName -CertificatePolicy $policy -Verbose

            $newCert = Get-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName

            # it takes a few seconds for KeyVault to finish
            $tries = 0
            do {
              Write-Host 'Waiting for certificate creation completion...'
              Start-Sleep -Seconds 10
              $operation = Get-AzKeyVaultCertificateOperation -VaultName $vaultName -Name $certificateName
              $tries++

              if ($operation.Status -eq 'failed')
              {
                throw 'Creating certificate $certificateName in vault $vaultName failed with error $($operation.ErrorMessage)'
              }

              if ($tries -gt 120)
              {
                throw 'Timed out waiting for creation of certificate $certificateName in vault $vaultName'
              }
            } while ($operation.Status -ne 'completed')

            $DeploymentScriptOutputs['certThumbprint'] = $newCert.Thumbprint
            $newCert | Out-String
          }
        ",
        "cleanupPreference": "OnSuccess",
        "retentionInterval": "P1D"
      }
    }
    ```

    De `deploymentScripts` resource is afhankelijk van de bron van de sleutel kluis en de functie toewijzings resource.  Deze heeft de volgende eigenschappen:

    * **identiteit**: implementatie script maakt gebruik van een door de gebruiker toegewezen beheerde identiteit voor het uitvoeren van de scripts.
    * **soort**: Geef het type script op. Op dit moment wordt alleen het Power shell-script ondersteund.
    * **updatetag**: Bepaal of het implementatie script moet worden uitgevoerd, zelfs als de script bron niet is gewijzigd. Dit kan een huidige tijds tempel of een GUID zijn. Zie [script meer dan één keer uitvoeren](./deployment-script-template.md#run-script-more-than-once)voor meer informatie.
    * **azPowerShellVersion**: Hiermee geeft u de versie van de Azure PowerShell module op die moet worden gebruikt. Het implementatie script ondersteunt momenteel versie 2.7.0, 2.8.0 en 3.0.0.
    * **time-out**: Geef de maximale toegestane uitvoerings tijd voor het script op dat is opgegeven in de [ISO 8601-indeling](https://en.wikipedia.org/wiki/ISO_8601). De standaard waarde is **P1D**.
    * **argumenten**: Geef de parameter waarden op. De waarden worden gescheiden door spaties.
    * **scriptContent**: Geef de script inhoud op. Als u een extern script wilt uitvoeren, gebruikt u **primaryScriptURI** in plaats daarvan. Zie [extern script gebruiken](./deployment-script-template.md#use-external-scripts)voor meer informatie.
        Het declareren van **$DeploymentScriptOutputs** is alleen vereist bij het testen van het script op een lokale computer. Als u de variabele declareert, kan het script worden uitgevoerd op een lokale computer en in een deploymentScript-bron zonder dat er wijzigingen hoeven te worden aangebracht. De waarde die is toegewezen aan $DeploymentScriptOutputs is beschikbaar als uitvoer in de implementaties. Zie [werken met uitvoer van Power shell-implementatie scripts](./deployment-script-template.md#work-with-outputs-from-powershell-script) of [werken met uitvoer van CLI-implementatie scripts](./deployment-script-template.md#work-with-outputs-from-cli-script)voor meer informatie.
    * **cleanupPreference**: Geef de voor keur op wanneer u de resources van het implementatie script wilt verwijderen.  De standaard waarde is **altijd**, wat betekent dat de implementatie script bronnen worden verwijderd ondanks de status van de Terminal (geslaagd, mislukt, geannuleerd). In deze zelf studie wordt **OnSuccess** gebruikt zodat u de resultaten van de uitvoering van het script kunt bekijken.
    * **retentionInterval**: Geef het interval op waarvoor de service de script bronnen behoudt nadat het een Terminal status heeft bereikt. Resources worden verwijderd wanneer deze duur verloopt. De duur is gebaseerd op het ISO 8601-patroon. In deze zelf studie wordt P1D gebruikt. Dit betekent een dag.  Deze eigenschap wordt gebruikt wanneer **cleanupPreference** is ingesteld op **OnExpiration**. Deze eigenschap is momenteel niet ingeschakeld.

    Het implementatie script neemt drie para meters: sleutel kluis naam, certificaat naam en onderwerpnaam.  Er wordt een certificaat gemaakt en vervolgens het certificaat toegevoegd aan de sleutel kluis.

    **$DeploymentScriptOutputs** wordt gebruikt om de uitvoer waarde op te slaan.  Zie [werken met uitvoer van Power shell-implementatie scripts](./deployment-script-template.md#work-with-outputs-from-powershell-script) of [werken met uitvoer van CLI-implementatie scripts](./deployment-script-template.md#work-with-outputs-from-cli-script)voor meer informatie.

    De voltooide sjabloon kunt u [hier](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json)vinden.

1. Als u het proces voor fout opsporing wilt zien, plaatst u een fout in de code door de volgende regel toe te voegen aan het implementatie script:

    ```powershell
    Write-Output1 $keyVaultName
    ```

    De juiste opdracht is **Write-output** in plaats van **Write-Output1**.

1. Selecteer **Bestand**>**Opslaan** om het bestand op te slaan.

## <a name="deploy-the-template"></a>De sjabloon implementeren

Raadpleeg de sectie [de sjabloon implementeren](./quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell#deploy-the-template) in de Snelstartgids Visual Studio code voor het openen van Cloud shell en het uploaden van het sjabloon bestand naar de shell. En voer vervolgens het volgende Power shell-script uit:

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
$identityId = Read-Host -Prompt "Enter the user-assigned managed identity ID"

$adUserId = (Get-AzADUser -UserPrincipalName $upn).Id
$resourceGroupName = "${projectName}rg"
$keyVaultName = "${projectName}kv"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json" -identityId $identityId -keyVaultName $keyVaultName -objectId $adUserId

Write-Host "Press [ENTER] to continue ..."
```

De implementatie script service moet aanvullende implementatie script resources maken voor het uitvoeren van een script. Het volt ooien van de voor bereiding en het opschonen kan tot één minuut duren, naast de daad werkelijke uitvoerings tijd van het script.

De implementatie is mislukt vanwege de ongeldige opdracht, de **Write-Output1** wordt in het script gebruikt. Er wordt een fout bericht weer gegeven met de melding:

```error
The term 'Write-Output1' is not recognized as the name of a cmdlet, function, script file, or operable
program.\nCheck the spelling of the name, or if a path was included, verify that the path is correct and try again.\n
```

Het uitvoerings resultaat van het implementatie script wordt opgeslagen in de implementatie script resources voor het oplossen van het probleem.

## <a name="debug-the-failed-script"></a>Fouten opsporen in het script

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Open de resource groep. Het is de naam van het project waaraan **RG** is toegevoegd. Er worden twee extra resources weer geven in de resource groep. Deze resources worden bronnen voor het *implementatie script*genoemd.

    ![Resource Manager-sjabloon implementatie script resources](./media/template-tutorial-deployment-script/resource-manager-template-deployment-script-resources.png)

    Beide bestanden hebben het achtervoegsel **azscripts** . Een is een opslag account en de andere is een container exemplaar.

    Selecteer **verborgen typen tonen** om de deploymentScripts-resource weer te geven.

1. Selecteer het opslag account met het achtervoegsel **azscripts** .
1. Selecteer de tegel **Bestands shares** . Er wordt een map **azscripts** weer geven.  De map bevat de uitvoer bestanden van het implementatie script.
1. Selecteer **azscripts**. U ziet twee mappen **azscriptinput** en **azscriptoutput**.  De map invoer bevat een script bestand voor het systeem-Power shell en de script bestanden voor de gebruikers implementatie. De uitvoermap bevat een **executionresult. json** en het script uitvoer bestand. U ziet het fout bericht in **executionresult. json**. Het uitvoer bestand is niet beschikbaar omdat de uitvoering is mislukt.

Verwijder de regel **Write-Output1** en implementeer de sjabloon opnieuw.

Wanneer de tweede implementatie met succes wordt uitgevoerd, worden de resources van het implementatie script verwijderd door de script service, omdat de eigenschap **cleanupPreference** is ingesteld op **OnSuccess**.

## <a name="clean-up-resources"></a>Resources opschonen

Schoon de geïmplementeerd Azure-resources, wanneer u deze niet meer nodig hebt, op door de resourcegroep te verwijderen.

1. Selecteer **Resourcegroep** in het linkermenu van Azure Portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.  U ziet in totaal zes resources in de resourcegroep.
4. Selecteer **Resourcegroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u het implementatie script gebruikt in Azure Resource Manager sjablonen. Informatie over het implementeren van Azure-resources op basis van voorwaarden vindt u in:

> [!div class="nextstepaction"]
> [Voorwaarden gebruiken](./template-tutorial-use-conditions.md)
