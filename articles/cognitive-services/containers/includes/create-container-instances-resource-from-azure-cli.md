---
title: Ondersteuning voor containers
titleSuffix: Azure Cognitive Services
description: Meer informatie over het maken van een Azure container Instance-bron vanuit de Azure CLI.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/06/2020
ms.author: dapine
ms.openlocfilehash: 700a04b58c13a9c7fd5301875226ca234cabeb96
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/07/2020
ms.locfileid: "75689465"
---
## <a name="create-an-azure-container-instance-resource-from-the-azure-cli"></a>Een Azure container Instance-bron maken vanuit de Azure CLI

In de onderstaande YAML wordt de resource van het Azure-container exemplaar gedefinieerd. Kopieer de inhoud en plak deze in een nieuw bestand met de naam `my-aci.yaml` en vervang de waarden van de opmerkingen door uw eigen. Raadpleeg de indeling van de [sjabloon][template-format] voor geldige YAML. Raadpleeg de [container opslagplaatsen en installatie kopieën][repositories-and-images] voor de beschik bare afbeeldings namen en de bijbehorende opslag plaats. Zie [yaml Reference: Azure container instances][aci-yaml-ref]voor meer informatie over de yaml-verwijzing voor container exemplaren.

```YAML
apiVersion: 2018-10-01
location: # < Valid location >
name: # < Container Group name >
imageRegistryCredentials: # This is required when pulling a non-public image
  - server: containerpreview.azurecr.io
    username: # < The username for the preview container registry >
    password: # < The password for the preview container registry >
properties:
  containers:
  - name: # < Container name >
    properties:
      image: # < Repository/Image name >
      environmentVariables: # These env vars are required
        - name: eula
          value: accept
        - name: billing
          value: # < Service specific Endpoint URL >
        - name: apikey
          value: # < Service specific API key >
      resources:
        requests:
          cpu: 4 # Always refer to recommended minimal resources
          memoryInGb: 8 # Always refer to recommended minimal resources
      ports:
        - port: 5000
  osType: Linux
  volumes: # This node, is only required for container instances that pull their model in at runtime, such as LUIS.
  - name: aci-file-share
    azureFile:
      shareName: # < File share name >
      storageAccountName: # < Storage account name>
      storageAccountKey: # < Storage account key >
  restartPolicy: OnFailure
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: 5000
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

> [!NOTE]
> Niet alle locaties hebben dezelfde Beschik baarheid van de CPU en het geheugen. Raadpleeg de tabel [locatie en resources][location-to-resource] voor de lijst met beschik bare resources voor containers per locatie en besturings systeem.

We vertrouwen op het YAML-bestand dat u hebt gemaakt voor de [`az container create`][azure-container-create] opdracht. Voer vanuit de Azure-CLI de `az container create` opdracht uit om de `<resource-group>` te vervangen door uw eigen. Voor het beveiligen van waarden binnen een implementatie van een YAML raadpleegt u ook [beveiligde waarden][secure-values].

```azurecli
az container create -g <resource-group> -f my-aci.yaml
```

De uitvoer van de opdracht is `Running...` indien geldig, nadat de uitvoer is gewijzigd in een JSON-teken reeks die de zojuist gemaakte ACI-resource vertegenwoordigt. De container installatie kopie is meer dan waarschijnlijk niet beschikbaar voor een tijdje, maar de resource wordt nu geïmplementeerd.

> [!TIP]
> Let op de locatie van de open bare preview-versie van Azure cognitieve Services, aangezien de YAML moet worden aangepast aan de plaats waar ze overeenkomen.

[azure-container-create]: https://docs.microsoft.com/cli/azure/container?view=azure-cli-latest#az-container-create
[template-format]: https://docs.microsoft.com/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups#template-format
[aci-yaml-ref]: ../../../container-instances/container-instances-reference-yaml.md
[repositories-and-images]: ../../cognitive-services-container-support.md#container-repositories-and-images
[location-to-resource]: ../../../container-instances/container-instances-region-availability.md#availability---general
[secure-values]: ../../../container-instances/container-instances-environment-variables.md#secure-values
