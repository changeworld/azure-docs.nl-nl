---
title: Niet-compatibele resources herstellen
description: Deze hand leiding helpt u bij het herstellen van resources die niet compatibel zijn met beleids regels in Azure Policy.
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 5cf26f5235fbc35cdc9bfc8527967c3cc5ca91b8
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79264529"
---
# <a name="remediate-non-compliant-resources-with-azure-policy"></a>Herstellen van niet-compatibele resources met Azure Policy

Resources die niet compatibel zijn met een **deployIfNotExists** -of **Modify** -beleid kunnen worden opgeslagen in een compatibele status via **herbemiddeling**. Herstel wordt uitgevoerd door Azure Policy het effect van de **deployIfNotExists** of de label **bewerkingen** van het toegewezen beleid voor uw bestaande resources uit te voeren, ongeacht of de toewijzing een beheer groep, een abonnement, een resource groep of een afzonderlijke resource is. In dit artikel worden de stappen beschreven die nodig zijn om het herstel met Azure Policy te begrijpen en uit te voeren.

## <a name="how-remediation-security-works"></a>Hoe herstel beveiliging werkt

Als Azure Policy de sjabloon uitvoert in de **deployIfNotExists** -beleids definitie, wordt een [beheerde identiteit](../../../active-directory/managed-identities-azure-resources/overview.md)gebruikt.
Azure Policy maakt een beheerde identiteit voor elke toewijzing, maar moet informatie over de rollen hebben om de beheerde identiteit te verlenen. Als de beheerde identiteit rollen ontbreekt, wordt deze fout weergegeven tijdens de toewijzing van het beleid of een initiatief. Wanneer u de portal gebruikt, wordt de beheerde identiteit door Azure Policy automatisch verleend aan de vermelde rollen zodra de toewijzing is gestart.

![Beheerde identiteit - ontbrekende functie](../media/remediate-resources/missing-role.png)

> [!IMPORTANT]
> Als een resource die is gewijzigd door **deployIfNotExists** of **Modify** zich buiten het bereik van de beleids toewijzing bevindt of als de sjabloon eigenschappen voor bronnen buiten het bereik van de beleids toewijzing heeft geopend, moet de beheerde identiteit van de toewijzing [hand matig worden verleend voor toegang](#manually-configure-the-managed-identity) of kan de herstel implementatie niet worden uitgevoerd.

## <a name="configure-policy-definition"></a>Beleidsdefinitie configureren

De eerste stap is het definiëren van de rollen die in de beleids definitie **deployIfNotExists** en **aanpassen** om de inhoud van de opgenomen sjabloon te implementeren. Voeg onder de eigenschap **Details** een eigenschap **roleDefinitionIds** toe. Deze eigenschap is een matrix met tekenreeksen die overeenkomen met de rollen in uw omgeving. Zie het [deployIfNotExists-voor beeld](../concepts/effects.md#deployifnotexists-example) of de [Modify-voor beelden](../concepts/effects.md#modify-examples)voor een volledig voor beeld.

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

De eigenschap **roleDefinitionIds** maakt gebruik van de volledige resource-id en de korte **rolnaam** van de rol. Als u de ID voor de rol 'Inzender in uw omgeving, gebruikt u de volgende code:

```azurecli-interactive
az role definition list --name 'Contributor'
```

## <a name="manually-configure-the-managed-identity"></a>Handmatig configureren van de beheerde identiteit

Wanneer u een toewijzing maakt met behulp van de portal, worden de beheerde identiteit door Azure Policy gegenereerd en wordt de rollen verleend die zijn gedefinieerd in **roleDefinitionIds**. Stappen voor het maken van de beheerde identiteit en machtigingen toewijzen moeten handmatig worden gedaan in de volgende voorwaarden:

- Tijdens het gebruik van de SDK (zoals Azure PowerShell)
- Wanneer een resource buiten het bereik van de roltoewijzing is gewijzigd door de sjabloon
- Wanneer een resource buiten het bereik van de roltoewijzing wordt gelezen door de sjabloon

> [!NOTE]
> Azure PowerShell en .NET zijn de enige SDK's die momenteel ondersteuning voor deze mogelijkheid.

### <a name="create-managed-identity-with-powershell"></a>Beheerde identiteit maken met PowerShell

Als u een beheerde identiteit tijdens de toewijzing van het beleid wilt maken, moet u de **locatie** definiëren en **AssignIdentity** gebruiken. In het volgende voor beeld wordt de definitie van het ingebouwde beleid **SQL DB transparent Data Encryption geïmplementeerd**, wordt de doel resource groep ingesteld en wordt de toewijzing gemaakt.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the built-in "Deploy SQL DB transparent data encryption" policy definition
$policyDef = Get-AzPolicyDefinition -Id '/providers/Microsoft.Authorization/policyDefinitions/86a912f6-9a06-4e26-b447-11b16ba8659f'

# Get the reference to the resource group
$resourceGroup = Get-AzResourceGroup -Name 'MyResourceGroup'

# Create the assignment using the -Location and -AssignIdentity properties
$assignment = New-AzPolicyAssignment -Name 'sqlDbTDE' -DisplayName 'Deploy SQL DB transparent data encryption' -Scope $resourceGroup.ResourceId -PolicyDefinition $policyDef -Location 'westus' -AssignIdentity
```

De variabele `$assignment` bevat nu de principal-ID van de beheerde identiteit samen met de standaard waarden die worden geretourneerd bij het maken van een beleids toewijzing. Deze kan worden geopend via `$assignment.Identity.PrincipalId`.

### <a name="grant-defined-roles-with-powershell"></a>Verleen gedefinieerd rollen met PowerShell

De nieuwe beheerde identiteit moet replicatie via Azure Active Directory voltooien voordat deze de vereiste rollen kan worden verleend. Zodra de replicatie is voltooid, wordt in het volgende voor beeld de beleids definitie in `$policyDef` voor de **roleDefinitionIds** herhaald en wordt [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) gebruikt om de nieuwe beheerde identiteit toe te kennen aan de rollen.

```azurepowershell-interactive
# Use the $policyDef to get to the roleDefinitionIds array
$roleDefinitionIds = $policyDef.Properties.policyRule.then.details.roleDefinitionIds

if ($roleDefinitionIds.Count -gt 0)
{
    $roleDefinitionIds | ForEach-Object {
        $roleDefId = $_.Split("/") | Select-Object -Last 1
        New-AzRoleAssignment -Scope $resourceGroup.ResourceId -ObjectId $assignment.Identity.PrincipalId -RoleDefinitionId $roleDefId
    }
}
```

### <a name="grant-defined-roles-through-portal"></a>Verleen rollen via portal gedefinieerd

Er zijn twee manieren om de beheerde identiteit van een toewijzing toe te kennen aan de gedefinieerde rollen met behulp van de portal, met behulp van **toegangs beheer (IAM)** of door de toewijzing van beleid of initiatief te bewerken en te klikken op **Opslaan**.

Een rol toevoegen aan de beheerde identiteit van de toewijzing, de volgende stappen uit:

1. Start de Azure Policy-service in Azure Portal door **Alle services** te selecteren en dan **Beleid** te zoeken en te selecteren.

1. Selecteer **Toewijzingen** in het linkerdeelvenster van de Azure Policy-pagina.

1. Zoek de toewijzing die een beheerde identiteit heeft en klik op de naam.

1. Zoek de eigenschap van de **toewijzings-id** op de pagina bewerken. De toewijzings-ID is ongeveer als volgt:

   ```output
   /subscriptions/{subscriptionId}/resourceGroups/PolicyTarget/providers/Microsoft.Authorization/policyAssignments/2802056bfc094dfb95d4d7a5
   ```

   De naam van de beheerde identiteit is het laatste deel van de resource-ID van de toewijzing, die `2802056bfc094dfb95d4d7a5` in dit voor beeld. Kopieer dit gedeelte van de toewijzing van resource-ID.

1. Navigeer naar de resource of de resources bovenliggende container (resourcegroep, abonnement, beheergroep) waarvoor de roldefinitie die handmatig zijn toegevoegd.

1. Klik op de koppeling **toegangs beheer (IAM)** op de pagina Resources en klik op **+ roltoewijzing toevoegen** boven aan de pagina toegangs beheer.

1. Selecteer de juiste rol die overeenkomt met een **roleDefinitionIds** van de beleids definitie.
   Zorg ervoor **dat de toewijzing** van de gebruiker, groep of toepassing van Azure AD is ingesteld op de standaard waarde. Plak of typ in het vak **selecteren** het gedeelte van de resource-id voor de toewijzing dat eerder is gevonden. Zodra de zoek opdracht is voltooid, klikt u op het object met dezelfde naam om ID te selecteren en klikt u op **Opslaan**.

## <a name="create-a-remediation-task"></a>Een herstel-taak maken

### <a name="create-a-remediation-task-through-portal"></a>Een herstel taak maken via de portal

Tijdens de evaluatie bepaalt de beleids toewijzing met **deployIfNotExists** -of **wijzigings** effecten of er resources zijn die niet compatibel zijn. Als niet-compatibele resources worden gevonden, worden de details op de pagina **herstel** vermeld. Naast de lijst met beleids regels met niet-compatibele resources is de optie om een **herstel taak**te activeren. Met deze optie maakt u een implementatie vanuit de **deployIfNotExists** -sjabloon of de **wijzigings** bewerkingen.

Voer de volgende stappen uit om een **herstel taak**te maken:

1. Start de Azure Policy-service in Azure Portal door **Alle services** te selecteren en dan **Beleid** te zoeken en te selecteren.

   ![Beleid zoeken in alle services](../media/remediate-resources/search-policy.png)

1. Selecteer aan de linkerkant van de Azure Policy pagina **herstel** .

   ![Herstel selecteren op de pagina beleid](../media/remediate-resources/select-remediation.png)

1. Alle **deployIfNotExists** -en **Modify** -beleids toewijzingen met niet-compatibele resources zijn opgenomen in het **beleid voor het herstellen** van het tabblad en de gegevens tabel. Klik op een beleid met de resources die niet compatibel zijn. De pagina **nieuwe herstel taak** wordt geopend.

   > [!NOTE]
   > Een andere manier om de **herstel taak** pagina te openen, is door te zoeken en te klikken op het beleid op de pagina **naleving** en vervolgens op de knop **herstel taak maken** te klikken.

1. Filter op de pagina **nieuwe herstel taak** de bronnen die u wilt herstellen met behulp van het **bereik** beletsel tekens om onderliggende resources te kiezen van waar het beleid is toegewezen (inclusief de afzonderlijke resource objecten). U kunt ook de vervolg keuzelijst **locaties** gebruiken om de resources verder te filteren. Alleen de resources die worden vermeld in de tabel worden hersteld.

   ![Herstellen: Selecteer welke resources moeten worden hersteld](../media/remediate-resources/select-resources.png)

1. Start de herstel taak zodra de resources zijn gefilterd door op **herstellen**te klikken. De pagina beleids naleving wordt geopend op het tabblad **herstel taken** om de status van de voortgang van de taken weer te geven. Implementaties die zijn gemaakt door de herstel taak worden meteen gestart.

   ![Herstellen-voortgang van herstel taken](../media/remediate-resources/task-progress.png)

1. Klik op de **herstel taak** op de pagina beleids naleving om meer informatie over de voortgang weer te geven. De filters die wordt gebruikt voor de taak wordt weergegeven, samen met een lijst van de resources die worden hersteld.

1. Klik op de pagina **herstel taak** met de rechter muisknop op een resource om de implementatie van de herstel taak of de resource weer te geven. Klik aan het einde van de rij op **gerelateerde gebeurtenissen** om details, zoals een fout bericht, weer te geven.

   ![Herstellen - contextmenu van de resource-taak](../media/remediate-resources/resource-task-context-menu.png)

Resources die worden geïmplementeerd via een **herstel taak** , worden toegevoegd aan het tabblad **geïmplementeerde resources** op de pagina naleving van beleid.

### <a name="create-a-remediation-task-through-azure-cli"></a>Een herstel taak maken via Azure CLI

Als u een **herstel taak** met Azure cli wilt maken, gebruikt u de `az policy remediation`-opdrachten. Vervang `{subscriptionId}` door uw abonnements-ID en `{myAssignmentId}` door de **deployIfNotExists** of **Wijzig** de toewijzings-id van het beleid.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Create a remediation for a specific assignment
az policy remediation create --name myRemediation --policy-assignment '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Zie voor andere herstel opdrachten en voor beelden [AZ Policy remediing](/cli/azure/policy/remediation) commands.

### <a name="create-a-remediation-task-through-azure-powershell"></a>Een herstel taak maken via Azure PowerShell

Als u een **herstel taak** met Azure PowerShell wilt maken, gebruikt u de `Start-AzPolicyRemediation`-opdrachten. Vervang `{subscriptionId}` door uw abonnements-ID en `{myAssignmentId}` door de **deployIfNotExists** of **Wijzig** de toewijzings-id van het beleid.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create a remediation for a specific assignment
Start-AzPolicyRemediation -Name 'myRemedation' -PolicyAssignmentId '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Zie de module [AZ. PolicyInsights](/powershell/module/az.policyinsights/#policy_insights) voor andere cmdlets en voor beelden voor herstel.

## <a name="next-steps"></a>Volgende stappen

- Bekijk voor beelden op [Azure Policy voor beelden](../samples/index.md).
- Lees over de [structuur van Azure Policy-definities](../concepts/definition-structure.md).
- Lees [Informatie over de effecten van het beleid](../concepts/effects.md).
- Meer informatie over het [programmatisch maken van beleids regels](programmatically-create.md).
- Meer informatie over het [ophalen van compatibiliteits gegevens](get-compliance-data.md).
- Bekijk wat een beheer groep is met [het organiseren van uw resources met Azure-beheer groepen](../../management-groups/overview.md).
