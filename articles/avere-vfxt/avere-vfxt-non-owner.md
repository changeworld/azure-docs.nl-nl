---
title: AVERE vFXT niet-eigenaars tijdelijke oplossing-Azure
description: Tijdelijke oplossing om gebruikers zonder abonnements eigenaar toestemming te geven om avere vFXT voor Azure te implementeren
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: 1b411fe465a67f8ea5421ac0dc93348b4e92e8ec
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76153272"
---
# <a name="authorize-non-owners-to-deploy-avere-vfxt"></a>Toestaan dat niet-eigenaren Avere vFXT implementeren

Deze instructies zijn een tijdelijke oplossing waarmee een gebruiker zonder abonnements eigenaars bevoegdheden een avere vFXT voor Azure-systeem kan maken.

(De aanbevolen manier om het avere vFXT-systeem te implementeren, is door een gebruiker met eigenaars bevoegdheden de aanmaak stappen uit te voeren, zoals wordt uitgelegd in [voorbereiden om de avere-vFXT te maken](avere-vfxt-prereqs.md).)  

De tijdelijke oplossing omvat het maken van een extra Access-rol die de gebruikers voldoende machtigingen geeft om het cluster te installeren. De rol moet worden gemaakt door een eigenaar van het abonnement en een eigenaar moet deze toewijzen aan de juiste gebruikers.

De eigenaar van een abonnement moet ook [de gebruiks voorwaarden accepteren](avere-vfxt-prereqs.md) voor de avere vFXT Marketplace-installatie kopie.

> [!IMPORTANT]
> Al deze stappen moeten worden uitgevoerd door een gebruiker met eigenaars bevoegdheden voor het abonnement dat wordt gebruikt voor het cluster.

1. Kopieer deze regels en sla ze op in een bestand (bijvoorbeeld `averecreatecluster.json`). Gebruik uw abonnements-ID in de `AssignableScopes`-instructie.

   ```json
   {
       "AssignableScopes": ["/subscriptions/<SUBSCRIPTION_ID>"],
       "Name": "avere-create-cluster",
       "IsCustom": "true"
       "Description": "Can create Avere vFXT clusters",
       "NotActions": [],
       "Actions": [
           "Microsoft.Authorization/*/read",
           "Microsoft.Authorization/roleAssignments/*",
           "Microsoft.Authorization/roleDefinitions/*",
           "Microsoft.Compute/*/read",
           "Microsoft.Compute/availabilitySets/*",
           "Microsoft.Compute/virtualMachines/*",
           "Microsoft.Network/*/read",
           "Microsoft.Network/networkInterfaces/*",
           "Microsoft.Network/routeTables/write",
           "Microsoft.Network/routeTables/delete",
           "Microsoft.Network/routeTables/routes/delete",
           "Microsoft.Network/virtualNetworks/subnets/join/action",
           "Microsoft.Network/virtualNetworks/subnets/read",

           "Microsoft.Resources/subscriptions/resourceGroups/read",
           "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
           "Microsoft.Storage/*/read",
           "Microsoft.Storage/storageAccounts/listKeys/action"
       ],
   }
   ```

1. Voer deze opdracht uit om de rol te maken:

   `az role definition create --role-definition <PATH_TO_FILE>`

    Voorbeeld:

    ```azurecli
    az role definition create --role-definition ./averecreatecluster.json
    ```

1. Wijs deze rol toe aan de gebruiker die het cluster gaat maken:

   `az role assignment create --assignee <USERNAME> --scope /subscriptions/<SUBSCRIPTION_ID> --role 'avere-create-cluster'`

Nadat dit proces is voltooid, geeft de rol aan de gebruiker de volgende machtigingen aan voor het abonnement:

* De netwerk infrastructuur maken en configureren
* De cluster controller maken
* Scripts voor het maken van clusters uitvoeren vanaf de cluster controller om het cluster te maken
