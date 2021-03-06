---
title: Azure Block Chain service beheren met Azure CLI
description: Azure Block Chain service beheren met Azure CLI
ms.date: 11/22/2019
ms.topic: article
ms.reviewer: janders
ms.openlocfilehash: ac75be644877905c1517395c1c789b1ea16fd49c
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455578"
---
# <a name="manage-azure-blockchain-service-using-azure-cli"></a>Azure Block Chain service beheren met Azure CLI

Naast de Azure Portal, kunt u Azure CLI gebruiken voor het beheren van Block Chain leden en transactie knooppunten voor uw Azure Block Chain-service.

Zorg ervoor dat u de nieuwste [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli) hebt geïnstalleerd en bent aangemeld bij een Azure-account in met `az login`.

Vervang in de volgende voor beelden het voor beeld `<parameter names>` door uw eigen waarden.

## <a name="create-blockchain-member"></a>Block Chain-lid maken

In het voor beeld wordt een Block Chain-lid gemaakt in de Azure Block Chain-service die het quorum grootboek protocol in een nieuw consortium uitvoert.

```azurecli
az resource create \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --is-full-object \
                     --properties '{ "location":"<myBlockchainLocation>", "properties": {"password":"<myStrongPassword>", "protocol":"Quorum","consortium":"<myConsortiumName>", "consortiumManagementAccountPassword":"<myConsortiumManagementAccountPassword>", "firewallRules":[{"ruleName":"<myRuleName>","startIpAddress":"<myStartIpAddress>", "endIpAddress":"<myEndIpAddress>"}]}, "sku":{"name":"<skuName>"}}'
```

| Parameter | Beschrijving |
|---------|-------------|
| **resource-groep** | De naam van de resource groep waar de Azure Block Chain-Service resources worden gemaakt. |
| **naam** | Een unieke naam die uw Azure Block Chain Service Block Chain-lid aanduidt. De naam wordt gebruikt voor het adres van het open bare eind punt. Bijvoorbeeld `myblockchainmember.blockchain.azure.com`. |
| **location** | Azure-regio waar het block Chain-lid wordt gemaakt. Bijvoorbeeld `eastus`. Kies de locatie die zich het dichtst bij uw gebruikers of uw andere Azure-toepassingen bevindt. |
| **wacht woord** | Het wacht woord voor het gebruikers account. Het wacht woord van het lid-account wordt gebruikt voor de verificatie van het open bare eind punt van het block Chain-lid met behulp van basis verificatie. Het wacht woord moet aan drie van de volgende vier vereisten voldoen: lengte moet tussen 12 & 72 tekens, 1 kleine letter, 1 hoofd letter, 1 cijfer en 1 speciaal teken zijn dat geen hekje (#), percentage (%), komma (,), ster (*), back-quote (\`), dubbele aanhalings tekens ("), één aanhalings teken ('), streepje (-) en semicolumn (;)|
| **Protocolsubstatus** | Open bare preview ondersteunt quorum. |
| **verband** | Naam van het consortium dat u wilt toevoegen of maken. |
| **consortiumManagementAccountPassword** | Het consortium beheer wachtwoord. Het wacht woord wordt gebruikt voor deelname aan een consortium. |
| **ruleName** | Regel naam voor white list een IP-adres bereik. Optionele para meter voor firewall regels.|
| **startIpAddress** | Begin van het IP-adres bereik voor white list. Optionele para meter voor firewall regels. |
| **u** | Het einde van het IP-adres bereik voor white list. Optionele para meter voor firewall regels. |
| **skuName** | Type laag. Gebruik S0 voor Standard en B0 voor Basic. |

## <a name="change-blockchain-member-password"></a>Block Chain lid van wacht woord wijzigen

In het voor beeld wordt het wacht woord van een Block Chain-lid gewijzigd.

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.password='<myStrongPassword>' \
                     --remove properties.consortiumManagementAccountAddress
```

| Parameter | Beschrijving |
|---------|-------------|
| **resource-groep** | De naam van de resource groep waar de Azure Block Chain-Service resources worden gemaakt. |
| **naam** | Naam die uw Azure Block Chain service-lid aanduidt. |
| **wacht woord** | Het wacht woord voor het gebruikers account. Het wacht woord moet aan drie van de volgende vier vereisten voldoen: de lengte moet tussen 12 & 72 tekens, 1 kleine letter, 1 hoofd letter, 1 cijfer en 1 speciaal teken zijn dat geen hekje (#), procent (%), komma (,), ster (*), back-quote (\`), dubbele aanhalings tekens ("), één aanhalings teken ('), streepje (-) en punt komma (;). |

## <a name="create-transaction-node"></a>Transactie knooppunt maken

Maak een transactie knooppunt in een bestaand Block Chain-lid. Door transactie knooppunten toe te voegen, kunt u de beveiligings isolatie verhogen en de belasting distribueren. U kunt bijvoorbeeld een eind punt voor een trans actie-knoop punt hebben voor verschillende client toepassingen.

```azurecli
az resource create \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --is-full-object \
                     --properties '{"location":"<myRegion>", "properties":{"password":"<myStrongPassword>", "firewallRules":[{"ruleName":"<myRuleName>", "startIpAddress":"<myStartIpAddress>", "endIpAddress":"<myEndIpAddress>"}]}}'
```

| Parameter | Beschrijving |
|---------|-------------|
| **resource-groep** | De naam van de resource groep waar de Azure Block Chain-Service resources worden gemaakt. |
| **naam** | De naam van het block Chain-lid van de Azure Block Chain-service dat ook de nieuwe naam van het transactie knooppunt bevat. |
| **location** | Azure-regio waar het block Chain-lid wordt gemaakt. Bijvoorbeeld `eastus`. Kies de locatie die zich het dichtst bij uw gebruikers of uw andere Azure-toepassingen bevindt. |
| **wacht woord** | Het wacht woord voor het transactie knooppunt. Het wacht woord moet aan drie van de volgende vier vereisten voldoen: de lengte moet tussen 12 & 72 tekens, 1 kleine letter, 1 hoofd letter, 1 cijfer en 1 speciaal teken zijn dat geen hekje (#), procent (%), komma (,), ster (*), back-quote (\`), dubbele aanhalings tekens ("), één aanhalings teken ('), streepje (-) en punt komma (;). |
| **ruleName** | Regel naam voor white list een IP-adres bereik. Optionele para meter voor firewall regels. |
| **startIpAddress** | Begin van het IP-adres bereik voor white list. Optionele para meter voor firewall regels. |
| **u** | Het einde van het IP-adres bereik voor white list. Optionele para meter voor firewall regels.|

## <a name="change-transaction-node-password"></a>Wacht woord van transactie knooppunt wijzigen

Voor beeld wijzigt u het wacht woord van een transactie knooppunt.

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.password='<myStrongPassword>'
```

| Parameter | Beschrijving |
|---------|-------------|
| **resource-groep** | De naam van de resource groep waarin de Azure Block Chain-Service resources bestaan. |
| **naam** | De naam van het block Chain-lid van de Azure Block Chain-service dat ook de nieuwe naam van het transactie knooppunt bevat. |
| **wacht woord** | Het wacht woord voor het transactie knooppunt. Het wacht woord moet aan drie van de volgende vier vereisten voldoen: de lengte moet tussen 12 & 72 tekens, 1 kleine letter, 1 hoofd letter, 1 cijfer en 1 speciaal teken zijn dat geen hekje (#), procent (%), komma (,), ster (*), back-quote (\`), dubbele aanhalings tekens ("), één aanhalings teken ('), streepje (-) en punt komma (;). |

## <a name="change-consortium-management-account-password"></a>Wacht woord van consortium beheer account wijzigen

Het consortium beheer-account wordt gebruikt voor het lidmaatschaps beheer van het consortium. Elk lid wordt uniek geïdentificeerd door een consortium beheer account en u kunt het wacht woord van dit account wijzigen met de volgende opdracht.

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.consortiumManagementAccountPassword='<myConsortiumManagementAccountPassword>' \
                     --remove properties.consortiumManagementAccountAddress
```

| Parameter | Beschrijving |
|---------|-------------|
| **resource-groep** | De naam van de resource groep waar de Azure Block Chain-Service resources worden gemaakt. |
| **naam** | Naam die uw Azure Block Chain service-lid aanduidt. |
| **consortiumManagementAccountPassword** | Het account wachtwoord voor het consortium beheer. Het wacht woord moet aan drie van de volgende vier vereisten voldoen: de lengte moet tussen 12 & 72 tekens, 1 kleine letter, 1 hoofd letter, 1 cijfer en 1 speciaal teken zijn dat geen hekje (#), procent (%), komma (,), ster (*), back-quote (\`), dubbele aanhalings tekens ("), één aanhalings teken ('), streepje (-) en punt komma (;). |
  
## <a name="update-firewall-rules"></a>Firewall regels bijwerken

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.firewallRules='[{"ruleName":"<myRuleName>", "startIpAddress":"<myStartIpAddress>", "endIpAddress":"<myEndIpAddress>"}]' \
                     --remove properties.consortiumManagementAccountAddress
```

| Parameter | Beschrijving |
|---------|-------------|
| **resource-groep** | De naam van de resource groep waarin de Azure Block Chain-Service resources bestaan. |
| **naam** | De naam van het block Chain-lid van de Azure Block Chain-service. |
| **ruleName** | Regel naam voor white list een IP-adres bereik. Optionele para meter voor firewall regels.|
| **startIpAddress** | Begin van het IP-adres bereik voor white list. Optionele para meter voor firewall regels.|
| **u** | Het einde van het IP-adres bereik voor white list. Optionele para meter voor firewall regels.|

## <a name="list-api-keys"></a>API-sleutels weer geven

API-sleutels kunnen worden gebruikt voor toegang tot knoop punten, vergelijkbaar met de gebruikers naam en het wacht woord. Er zijn twee API-sleutels ter ondersteuning van het draaien van sleutels. Gebruik de volgende opdracht om de API-sleutels weer te geven.

```azurecli
az resource invoke-action \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>/transactionNodes/<myTransactionNode> \
                            --action "listApiKeys" \
                            --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parameter | Beschrijving |
|---------|-------------|
| **resource-groep** | De naam van de resource groep waarin de Azure Block Chain-Service resources bestaan. |
| **naam** | De naam van het block Chain-lid van de Azure Block Chain-service dat ook de nieuwe naam van het transactie knooppunt bevat. |

## <a name="regenerate-api-keys"></a>API-sleutels opnieuw genereren

Gebruik de volgende opdracht om uw API-sleutels opnieuw te genereren.

```azurecli
az resource invoke-action \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>/transactionNodes/<myTransactionNode> \
                            --action "regenerateApiKeys" \
                            --resource-type Microsoft.Blockchain/blockchainMembers \
                            --request-body '{"keyName":"<keyValue>"}'
```

| Parameter | Beschrijving |
|---------|-------------|
| **resource-groep** | De naam van de resource groep waarin de Azure Block Chain-Service resources bestaan. |
| **naam** | De naam van het block Chain-lid van de Azure Block Chain-service dat ook de nieuwe naam van het transactie knooppunt bevat. |
| **keyName** | Vervang \<waarde\> door Key1 of Key2. |

## <a name="delete-a-transaction-node"></a>Een transactie knooppunt verwijderen

Voor beeld wordt een trans actie-knoop punt van het block Chain-lid verwijderd.

```azurecli
az resource delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parameter | Beschrijving |
|---------|-------------|
| **resource-groep** | De naam van de resource groep waarin de Azure Block Chain-Service resources bestaan. |
| **naam** | De naam van het block Chain-lid van de Azure Block Chain-service dat ook de naam van het transactie knooppunt bevat dat moet worden verwijderd. |

## <a name="delete-a-blockchain-member"></a>Een Block Chain-lid verwijderen

Voor beeld wordt een Block Chain-lid verwijderd.

```azurecli
az resource delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parameter | Beschrijving |
|---------|-------------|
| **resource-groep** | De naam van de resource groep waarin de Azure Block Chain-Service resources bestaan. |
| **naam** | De naam van het block Chain-lid van de Azure Block Chain-service dat moet worden verwijderd. |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>Toegang verlenen voor Azure AD-gebruiker

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee <assignee> \
                            --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| Parameter | Beschrijving |
|---------|-------------|
| **rolvak** | De naam van de Azure AD-rol. |
| **toegewezen gebruiker** | Gebruikers-ID voor Azure AD. Bijvoorbeeld: `user@contoso.com` |
| **ligt** | Het bereik van de roltoewijzing. Dit kan een Block Chain-lid of een transactie knooppunt zijn. |

**Voorbeeld:**

Toegang tot knoop punten verlenen aan block Chain- **lid**van Azure AD-gebruiker:

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**Voorbeeld:**

Toegang tot knoop punten verlenen voor Azure AD-gebruiker aan block Chain- **transactie knooppunt**:

```azurecli
az role assignment create \
                            --role 'MyRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Toegang tot knoop punten verlenen voor een Azure AD-groep of-toepassingsrol

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee-object-id <assignee_object_id>
```

| Parameter | Beschrijving |
|---------|-------------|
| **rolvak** | De naam van de Azure AD-rol. |
| **toegewezen gebruiker-object-id** | Groeps-ID of toepassings-ID van Azure AD. |
| **ligt** | Het bereik van de roltoewijzing. Dit kan een Block Chain-lid of een transactie knooppunt zijn. |

**Voorbeeld:**

Toegang tot knoop punten **verlenen voor toepassingsrol**

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee-object-id 22222222-2222-2222-2222-222222222222 \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Azure AD-knooppunt toegang verwijderen

```azurecli
az role assignment delete \
                            --role <myRole> \
                            --assignee <assignee> \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| Parameter | Beschrijving |
|---------|-------------|
| **rolvak** | De naam van de Azure AD-rol. |
| **toegewezen gebruiker** | Gebruikers-ID voor Azure AD. Bijvoorbeeld: `user@contoso.com` |
| **ligt** | Het bereik van de roltoewijzing. Dit kan een Block Chain-lid of een transactie knooppunt zijn. |

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [configureren van Azure Block Chain Service Trans Action-knoop punten met de Azure Portal](configure-transaction-nodes.md).
