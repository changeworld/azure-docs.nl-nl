---
title: Meerdere resource-exemplaren maken
description: Leer hoe u een Azure Resource Manager-sjabloon maakt om meerdere resource-instanties te maken.
author: mumian
ms.date: 03/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 91a37178f8dc8ecc3c61ca16f193e2e52c309d46
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79238536"
---
# <a name="tutorial-create-multiple-resource-instances-with-resource-manager-templates"></a>Zelfstudie: Meerdere resource-instanties maken met Resource Manager-sjablonen

Leer hoe u een Azure Resource Manager-sjabloon herhaaldelijk gebruikt om meerdere instanties van een Azure-resource te maken. In deze zelfstudie wijzigt u een sjabloon om drie instanties van het opslagaccount te maken.

![Azure Resource Manager maakt meerdere exemplaren diagram](./media/template-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances-diagram.png)

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Een quickstartsjabloon openen
> * De sjabloon bewerken
> * De sjabloon implementeren

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt voltooien, hebt u het volgende nodig:

* Visual Studio code met de extensie Resource Manager-Hulpprogram Ma's. Zie [Visual Studio code gebruiken om Azure Resource Manager sjablonen te maken](use-vs-code-to-create-template.md).

## <a name="open-a-quickstart-template"></a>Een snelstartsjabloon openen

[Azure-snelstartsjablonen](https://azure.microsoft.com/resources/templates/) is een opslagplaats voor Resource Manager-sjablonen. In plaats van een sjabloon helemaal vanaf de basis te maken, kunt u een voorbeeldsjabloon zoeken en aanpassen. De in deze snelstart gebruikte sjabloon wordt [Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) (Standaardopslagaccount maken) genoemd. De sjabloon definieert een Azure Storage-accountresource.

1. Selecteer in Visual Studio Code **Bestand**>**Bestand openen**.
2. Plak de volgende URL in **Bestandsnaam**:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Selecteer **Openen** om het bestand te openen.
4. De resource Microsoft.Storage/storageAccounts is in de sjabloon gedefinieerd. Vergelijk de sjabloon met de [sjabloonverwijzing](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts). Het is handig om enige basiskennis te hebben van de sjabloon voordat u deze gaat aanpassen.
5. Selecteer **Bestand**>**Opslaan als** om het bestand op uw lokale computer op te slaan als **azuredeploy.json**.

## <a name="edit-the-template"></a>De sjabloon bewerken

Met de bestaande sjabloon wordt één opslagaccount gemaakt. U past de sjabloon aan om drie opslagaccounts te maken.

Breng vanuit Visual Studio Code de volgende vier wijzigingen aan:

![Azure Resource Manager maakt meerdere instanties](./media/template-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances.png)

1. Voeg een `copy`-element toe aan de resourcedefinitie van het opslagaccount. In het copy-element geeft u het aantal iteraties en een variabele voor deze lus op. Het aantal iteraties moet een positief geheel getal zijn en mag niet hoger zijn dan 800.
2. De functie `copyIndex()` retourneert de huidige iteratie in de lus. U gebruikt de index als het voorvoegsel van de naam. `copyIndex()` is gebaseerd op nul. Als u de indexwaarde wilt verschuiven, kunt u een waarde doorgeven in de functie copyIndex(). Bijvoorbeeld *copyIndex(1)* .
3. Verwijder het element **variables**, want dit wordt niet meer gebruikt.
4. Verwijder het element **outputs**. Dit is niet langer nodig.

De voltooide sjabloon ziet er als volgt uit:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "apiVersion": "2018-02-01",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": 3
      }
    }
  ]
}
```

Voor meer informatie over het maken van meerdere instanties raadpleegt u [Deploy multiple instances of a resource or property in Azure Resource Manager Templates](./copy-resources.md) (Meerdere instanties van een resource of eigenschap implementeren in Azure Resource Manager-sjablonen)

## <a name="deploy-the-template"></a>De sjabloon implementeren

Raadpleeg de sectie [De sjabloon implementeren](quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) in de snelstartgids van Visual Studio Code voor de implementatieprocedure.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Als u alle drie de opslagaccounts wilt weergeven, laat u de parameter --name weg:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az storage account list --resource-group $resourceGroupName
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Get-AzStorageAccount -ResourceGroupName $resourceGroupName
```

---

Vergelijk de namen van de opslagaccounts met de naamdefinitie in de sjabloon.

## <a name="clean-up-resources"></a>Resources opschonen

Schoon de geïmplementeerd Azure-resources, wanneer u deze niet meer nodig hebt, op door de resourcegroep te verwijderen.

1. Selecteer **Resourcegroep** in het linkermenu van Azure Portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.  U ziet in totaal zes resources in de resourcegroep.
4. Selecteer **Resourcegroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u meerdere instanties van opslagaccounts maakt.  In de volgende zelfstudie ontwikkelt u een sjabloon met meerdere resources en meerdere resourcetypen. Sommige resources hebben afhankelijke resources.

> [!div class="nextstepaction"]
> [Afhankelijke resources maken](./template-tutorial-create-templates-with-dependent-resources.md)
