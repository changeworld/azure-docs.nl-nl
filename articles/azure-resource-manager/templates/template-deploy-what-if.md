---
title: Sjabloonimlementatie wat-als (preview)
description: Bepaal welke wijzigingen er in uw resources optreden voordat u een Azure Resource Manager sjabloon implementeert.
author: mumian
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: jgao
ms.openlocfilehash: b9d4150779842614a5dc284a2b3a489593fabfe1
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78388506"
---
# <a name="resource-manager-template-deployment-what-if-operation-preview"></a>Implementatie van een resource manager-sjabloon wat-als-bewerking (preview)

Voordat u een sjabloon implementeert, wilt u mogelijk een voor beeld bekijken van de wijzigingen die zich voordoen. Azure Resource Manager biedt de What-if-bewerking om te laten zien hoe resources worden gewijzigd als u de sjabloon implementeert. Met de bewerking What-if worden geen wijzigingen aangebracht in bestaande resources. In plaats daarvan worden de wijzigingen voor speld als de opgegeven sjabloon wordt geïmplementeerd.

> [!NOTE]
> De What-if-bewerking is momenteel beschikbaar als preview-versie. Als u deze wilt gebruiken, moet u [zich aanmelden voor de preview-versie](https://aka.ms/armtemplatepreviews). In het geval van een preview-versie kunnen de resultaten soms aangeven dat een resource wordt gewijzigd wanneer er niets wordt gewijzigd. We werken eraan om deze problemen te reduceren, maar we hebben uw hulp nodig. Meld deze problemen aan [https://aka.ms/whatifissues](https://aka.ms/whatifissues).

U kunt de What-if-bewerking gebruiken met de Power shell-opdrachten of de REST API bewerkingen.

In Power shell bevat de uitvoer kleur codes die u helpen de verschillende soorten wijzigingen te zien.

![Implementatie van Resource Manager-sjabloon wat-als-bewerking fullresourcepayload en type wijziging](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

De tekst ouptput is:

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

## <a name="what-if-commands"></a>Wat-als-opdrachten

U kunt Azure PowerShell of Azure REST API gebruiken voor de What-if-bewerking.

### <a name="azure-powershell"></a>Azure PowerShell

Als u een voor beeld van de wijzigingen wilt bekijken voordat u een sjabloon implementeert, voegt u de para meter `-Whatif` switch toe aan de implementatie opdracht.

* `New-AzResourceGroupDeployment -Whatif` voor implementaties van resource groepen
* `New-AzSubscriptionDeployment -Whatif` en `New-AzDeployment -Whatif` voor implementaties op abonnements niveau

U kunt ook de para meter `-Confirm` wijzigen gebruiken om de wijzigingen te bekijken en u wordt gevraagd om door te gaan met de implementatie.

* `New-AzResourceGroupDeployment -Confirm` voor implementaties van resource groepen
* `New-AzSubscriptionDeployment -Confirm` en `New-AzDeployment -Confirm` voor implementaties op abonnements niveau

De voor gaande opdrachten retour neren een tekst samenvatting die u hand matig kunt controleren. Als u een object wilt ophalen dat u programmatisch kunt controleren op wijzigingen, gebruikt u:

* `$results = Get-AzResourceGroupDeploymentWhatIf` voor implementaties van resource groepen
* `$results = Get-AzSubscriptionDeploymentWhatIf` of `$results = Get-AzDeploymentWhatIf` voor implementaties op abonnements niveau

> [!NOTE]
> Vóór de release van versie 2.0.1-alpha5 hebt u de opdracht `New-AzDeploymentWhatIf` gebruikt. Deze opdracht is vervangen door de opdrachten `Get-AzDeploymentWhatIf`, `Get-AzResourceGroupDeploymentWhatIf`en `Get-AzSubscriptionDeploymentWhatIf`. Als u een eerdere versie hebt gebruikt, moet u die syntaxis bijwerken. De para meter `-ScopeType` is verwijderd.

### <a name="azure-rest-api"></a>Azure REST API

Voor REST API gebruikt u:

* [Implementaties-What if](/rest/api/resources/deployments/whatif) voor implementaties van resource groepen
* [Implementaties-What if op abonnements bereik](/rest/api/resources/deployments/whatifatsubscriptionscope) voor implementaties op abonnements niveau

## <a name="change-types"></a>Wijzigings typen

Met de bewerking What-if wordt zes verschillende soorten wijzigingen vermeld:

- **Maken**: de resource bestaat momenteel niet, maar is in de sjabloon gedefinieerd. De resource wordt gemaakt.

- **Verwijderen**: dit wijzigings type is alleen van toepassing wanneer u de [modus volledig](deployment-modes.md) gebruikt voor de implementatie. De resource bestaat wel, maar is niet gedefinieerd in de sjabloon. In de modus volledig wordt de resource verwijderd. Alleen resources die de verwijdering van de [volledige modus ondersteunen](complete-mode-deletion.md) , zijn opgenomen in dit wijzigings type.

- **Negeren**: de resource bestaat wel, maar is niet gedefinieerd in de sjabloon. De resource wordt niet geïmplementeerd of gewijzigd.

- **Ongewijzigd**: de resource bestaat en is gedefinieerd in de sjabloon. De resource wordt opnieuw geïmplementeerd, maar de eigenschappen van de resource worden niet gewijzigd. Dit type wijziging wordt geretourneerd wanneer [ResultFormat](#result-format) is ingesteld op `FullResourcePayloads`. Dit is de standaard waarde.

- **Wijzigen**: de resource bestaat en is gedefinieerd in de sjabloon. De resource wordt opnieuw geïmplementeerd en de eigenschappen van de resource worden gewijzigd. Dit type wijziging wordt geretourneerd wanneer [ResultFormat](#result-format) is ingesteld op `FullResourcePayloads`. Dit is de standaard waarde.

- **Implementeren**: de resource bestaat en is gedefinieerd in de sjabloon. De resource wordt opnieuw geïmplementeerd. De eigenschappen van de resource kunnen of mogen niet worden gewijzigd. De bewerking retourneert dit wijzigings type wanneer het niet genoeg informatie heeft om te bepalen of eigenschappen worden gewijzigd. U ziet deze voor waarde alleen wanneer [ResultFormat](#result-format) is ingesteld op `ResourceIdOnly`.

## <a name="result-format"></a>Resultaat indeling

U kunt het detail niveau bepalen dat wordt geretourneerd over de voorspelde wijzigingen. Gebruik in de implementatie opdrachten (`New-Az*Deployment`) de para meter **-WhatIfResultFormat** . Gebruik in de programmatische-object opdrachten (`Get-Az*DeploymentWhatIf`) de para meter **ResultFormat** .

Stel de indelings parameter in op **FullResourcePayloads** om een lijst met resources op te halen die worden gewijzigd en Details over de eigenschappen die worden gewijzigd. Stel de indelings parameter in op **ResourceIdOnly** om een lijst met resources op te halen die worden gewijzigd. De standaard waarde is **FullResourcePayloads**.  

In de volgende resultaten ziet u de twee verschillende uitvoer indelingen:

- Volledige nettoladingen van resources

  ```powershell
  Resource and property changes are indicated with these symbols:
    - Delete
    + Create
    ~ Modify

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
      - tags.Owner: "Team A"
      ~ properties.addressSpace.addressPrefixes: [
        - 0: "10.0.0.0/16"
        + 0: "10.0.0.0/15"
        ]
      ~ properties.subnets: [
        - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

        ]

  Resource changes: 1 to modify.
  ```

- Alleen Resource-ID

  ```powershell
  Resource and property changes are indicated with this symbol:
    ! Deploy

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ! Microsoft.Network/virtualNetworks/vnet-001

  Resource changes: 1 to deploy.
  ```

## <a name="run-what-if-operation"></a>Wat-als-bewerking uitvoeren

### <a name="set-up-environment"></a>Omgeving instellen

Laten we een aantal tests uitvoeren om te zien hoe-als werkt. Implementeer eerst een [sjabloon waarmee een virtueel netwerk wordt gemaakt](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-before.json). U gebruikt dit virtuele netwerk om te testen hoe wijzigingen worden gerapporteerd met wat-als.

```azurepowershell
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

### <a name="test-modification"></a>Wijziging testen

Nadat de implementatie is voltooid, bent u klaar om de bewerking wat als' te testen. Met deze tijd implementeert u een [sjabloon waarmee het virtuele netwerk wordt gewijzigd](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-after.json). Er ontbreken een of meer oorspronkelijke Tags, een subnet is verwijderd en het adres voorvoegsel is gewijzigd.

```azurepowershell
New-AzResourceGroupDeployment `
  -Whatif `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

De What-if-uitvoer ziet er ongeveer als volgt uit:

![Implementatie van Resource Manager-sjabloon wat-als uitvoer bewerking](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

De tekst uitvoer is:

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

        name:                     "subnet001"
        properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

Boven aan de uitvoer wordt aangegeven dat kleuren worden gedefinieerd om het type wijzigingen aan te geven.

Onder aan de uitvoer ziet u dat de tag-eigenaar is verwijderd. Het adres voorvoegsel is gewijzigd van 10.0.0.0/16 naar 10.0.0.0/15. Het subnet met de naam subnet001 is verwijderd. Houd er rekening mee dat deze wijzigingen niet daad werkelijk zijn geïmplementeerd. U ziet een voor beeld van de wijzigingen die zich voordoen als u de sjabloon implementeert.

Sommige van de eigenschappen die worden weer gegeven als verwijderd, worden niet daad werkelijk gewijzigd. Eigenschappen kunnen niet worden gerapporteerd als verwijderd als ze niet in de sjabloon staan, maar worden tijdens de implementatie automatisch ingesteld als standaard waarden. Dit resultaat wordt als ' ruis ' beschouwd in het wat-als-antwoord. Voor de uiteindelijke geïmplementeerde resource zijn de waarden ingesteld voor de eigenschappen. Als de ' wat als'-bewerking is verouderd, worden deze eigenschappen uit het resultaat gefilterd.

## <a name="programmatically-evaluate-what-if-results"></a>Wat-als-resultaten programmatisch evalueren

Nu kunnen we de What-if-resultaten programmatisch evalueren door de opdracht in te stellen op een variabele.

```azurepowershell
$results = Get-AzResourceGroupDeploymentWhatIf `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

U kunt een samen vatting van elke wijziging bekijken.

```azurepowershell
foreach ($change in $results.Changes)
{
  $change.Delta
}
```

## <a name="confirm-deletion"></a>Verwijdering bevestigen

De bewerking What-if ondersteunt het gebruik van de [implementatie modus](deployment-modes.md). Als deze modus is ingesteld op volt ooien, worden de resources die niet in de sjabloon staan, verwijderd. In het volgende voor beeld wordt een [sjabloon geïmplementeerd waarvoor geen resources zijn gedefinieerd](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json) in de volledige modus.

Als u de wijzigingen wilt bekijken voordat u een sjabloon implementeert, gebruikt u de para meter `-Confirm` switch met de implementatie opdracht. Als de wijzigingen naar verwachting zijn, moet u bevestigen dat u de implementatie wilt volt ooien.

```azurepowershell
New-AzResourceGroupDeployment `
  -Confirm `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json" `
  -Mode Complete
```

Omdat er geen resources zijn gedefinieerd in de sjabloon en de implementatie modus is ingesteld op voltooid, wordt het virtuele netwerk verwijderd.

![Implementatie van de implementatie modus voor het uitvoeren van een resource manager-sjabloon](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

De tekst uitvoer is:

```powershell
Resource and property changes are indicated with this symbol:
  - Delete

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  - Microsoft.Network/virtualNetworks/vnet-001

      id:
"/subscriptions/./resourceGroups/ExampleGroup/providers/Microsoft.Network/virtualNet
works/vnet-001"
      location:        "centralus"
      name:            "vnet-001"
      tags.CostCenter: "12345"
      tags.Owner:      "Team A"
      type:            "Microsoft.Network/virtualNetworks"

Resource changes: 1 to delete.

Are you sure you want to execute the deployment?
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "Y"):
```

U ziet de verwachte wijzigingen en u kunt bevestigen dat u de implementatie wilt uitvoeren.

## <a name="next-steps"></a>Volgende stappen

- Als u onjuiste resultaten van de preview-versie van What-if ziet, kunt u de problemen melden op [https://aka.ms/whatifissues](https://aka.ms/whatifissues).
- Zie [resources implementeren met Resource Manager-sjablonen en Azure PowerShell](deploy-powershell.md)voor meer informatie over het implementeren van sjablonen met Azure PowerShell.
- Zie [resources implementeren met Resource Manager-sjablonen en resource manager rest API](deploy-rest.md)voor meer informatie over het implementeren van sjablonen met rest.
