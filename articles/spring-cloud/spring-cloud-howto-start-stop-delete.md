---
title: Uw Azure lente-Cloud toepassing starten, stoppen en verwijderen | Microsoft Docs
description: Uw Azure lente-Cloud toepassing starten, stoppen en verwijderen
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: brendm
ms.openlocfilehash: daa549e248668add54530e90174134c4e0059b3a
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76276827"
---
# <a name="start-stop-and-delete-your-azure-spring-cloud-application"></a>Uw Azure lente-Cloud toepassing starten, stoppen en verwijderen

In deze hand leiding wordt uitgelegd hoe u de status van een toepassing in azure lente Cloud kunt wijzigen met behulp van de Azure Portal of de Azure CLI.

## <a name="using-the-azure-portal"></a>Azure Portal gebruiken

Nadat u een toepassing hebt geïmplementeerd, kunt u deze starten, stoppen en verwijderen met behulp van de Azure Portal.

1. Ga naar het Azure veer Cloud service-exemplaar in het Azure Portal.
1. Selecteer het tabblad **toepassings dashboard** .
1. Selecteer de toepassing waarvan u de status wilt wijzigen.
1. Selecteer op de pagina **overzicht** voor die toepassing **starten/stoppen**, **opnieuw starten**of **verwijderen**.

## <a name="using-the-azure-cli"></a>Azure CLI gebruiken

> [!NOTE]
> U kunt optionele para meters gebruiken en standaard instellingen configureren met de Azure CLI. Lees [onze referentie documentatie](spring-cloud-cli-reference.md)voor meer informatie over de Azure cli.  

Installeer eerst de Azure veer Cloud-extensie voor de Azure CLI als volgt:

```azurecli
az extension add --name spring-cloud
```

Selecteer vervolgens een van de volgende Azure CLI-bewerkingen:

* Uw toepassing starten:

    ```azurecli
    az spring-cloud app start -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Uw toepassing stoppen:

    ```azurecli
    az spring-cloud app stop -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* De toepassing opnieuw starten:

    ```azurecli
    az spring-cloud app restart -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Uw toepassing verwijderen:

    ```azurecli
    az spring-cloud app delete -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```
