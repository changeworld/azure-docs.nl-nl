---
title: Azure Cloud Shell-functies | Microsoft Docs
description: Overzicht van functies in Azure Cloud Shell
services: Azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/26/2019
ms.author: damaerte
ms.openlocfilehash: 0aa71e4b78df8087093f183b146c525d2a8a0f99
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/14/2020
ms.locfileid: "79366237"
---
# <a name="features--tools-for-azure-cloud-shell"></a>Functies &-hulpprogram ma's voor Azure Cloud Shell

[!INCLUDE [features-introblock](../../includes/cloud-shell-features-introblock.md)]

Azure Cloud Shell wordt uitgevoerd op `Ubuntu 16.04 LTS`.

## <a name="features"></a>Functies

### <a name="secure-automatic-authentication"></a>Automatische verificatie beveiligen

Cloud Shell veilig en automatisch account toegang verifiëren voor de Azure CLI en Azure PowerShell.

### <a name="home-persistence-across-sessions"></a>$HOME persistentie over sessies

Cloud Shell begeleidt u bij het koppelen van een Azure-bestands share bij de eerste start om bestanden in verschillende sessies te behouden.
Zodra dit is voltooid, voegt Cloud Shell uw opslag (gekoppeld als `$HOME\clouddrive`) automatisch aan voor alle toekomstige sessies.
Bovendien wordt uw `$HOME` Directory persistent gemaakt als. img in uw Azure-bestands share.
Bestanden buiten `$HOME` en computer status blijven niet behouden in sessies. Gebruik aanbevolen procedures voor het opslaan van geheimen zoals SSH-sleutels. Services als [Azure Key Vault hebben zelf studies voor Setup](https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#prerequisites).

[Meer informatie over het persistent maken van bestanden in Cloud Shell.](persisting-shell-storage.md)

### <a name="azure-drive-azure"></a>Azure-station (Azure:)

Power shell in Cloud Shell begint u in azure drive (`Azure:`).
Het Azure-station biedt eenvoudige detectie en navigatie van Azure-resources, zoals compute, netwerk, opslag, enzovoort, vergelijkbaar met de navigatie van het bestands systeem.
U kunt de vertrouwde [Azure PowerShell-cmdlets](https://docs.microsoft.com/powershell/azure) blijven gebruiken om deze bronnen te beheren, ongeacht het station waarin u zich bevindt.
Wijzigingen die zijn aangebracht in de Azure-resources, direct in Azure Portal of via Azure PowerShell-cmdlets, worden weer gegeven in het Azure-station.  U kunt `dir -Force` uitvoeren om uw resources te vernieuwen.

![](media/features-powershell/azure-drive.png)

### <a name="manage-exchange-online"></a>Exchange Online beheren

Power shell in Cloud Shell bevat een persoonlijke build van de module Exchange Online.  Voer `Connect-EXOPSSession` uit om uw Exchange-cmdlets op te halen.

![](media/features-powershell/exchangeonline.png)

 Voer `Get-Command -Module tmp_*` uit.
> [!NOTE]
> De module naam moet beginnen met `tmp_`als u modules hebt geïnstalleerd met hetzelfde voor voegsel, worden de bijbehorende cmdlets ook weer gegeven. 

![](media/features-powershell/exchangeonlinecmdlets.png)

### <a name="deep-integration-with-open-source-tooling"></a>Diep gaande integratie met open source-hulp programma

Cloud Shell bevat vooraf geconfigureerde authenticatie voor open source-hulpprogram ma's, zoals terraform, Ansible en chef-specificatie. Probeer het uit in de voorbeeld scenario's.

## <a name="tools"></a>Hulpprogramma's

|Category   |Naam   |
|---|---|
|Linux-hulpprogram ma's            |bash<br> zsh<br> &<br> tmux<br> graven<br>               |
|Azure-hulpprogramma 's            |[Azure cli](https://github.com/Azure/azure-cli) en [Azure Classic cli](https://github.com/Azure/azure-xplat-cli)<br> [AzCopy](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy#writing-your-first-azcopy-command)<br> [Azure Functions CLI](https://github.com/Azure/azure-functions-core-tools)<br> [Service Fabric-CLI](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli)<br> [Batch Shipyard](https://github.com/Azure/batch-shipyard)<br> [blobxfer](https://github.com/Azure/blobxfer)|
|Tekst editors           |code (Cloud Shell-editor)<br> vim<br> nano<br> emacs    |
|Broncodebeheer         |git                    |
|Build-hulpprogram ma's            |geven<br> maven<br> npm<br> gooien         |
|Containers             |[Docker Machine](https://github.com/docker/machine)<br> [Kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [Helm](https://github.com/kubernetes/helm)<br> [DC/OS CLI](https://github.com/dcos/dcos-cli)         |
|Databases              |MySQL-client<br> PostgreSql-client<br> [Sqlcmd-hulp programma](https://docs.microsoft.com/sql/tools/sqlcmd-utility)<br> [MSSQL-scripter](https://github.com/Microsoft/sql-xplat-cli) |
|Overige                  |iPython-client<br> [Cloud Foundry CLI](https://github.com/cloudfoundry/cli)<br> [Terraform](https://www.terraform.io/docs/providers/azurerm/)<br> [Ansible](https://www.ansible.com/microsoft-azure)<br> [Chef-specificatie](https://www.chef.io/inspec/)<br> [Puppet-flits](https://puppet.com/docs/bolt/latest/bolt.html)<br> [HashiCorp Packer](https://www.packer.io/)|

## <a name="language-support"></a>Taalondersteuning

|Taal   |Version   |
|---|---|
|.NET Core  |2.2.402       |
|Aan de slag         |1.9        |
|Java       |1.8        |
|Node.js    |8.16.0      |
|PowerShell |[7.0.0](https://github.com/PowerShell/powershell/releases)       |
|Python     |2,7 en 3,5 (standaard)|

## <a name="next-steps"></a>Volgende stappen
[Bash in Cloud Shell Quick Start](quickstart.md) <br>
[Power shell in Cloud Shell Quick Start](quickstart-powershell.md) <br>
[Meer informatie over Azure CLI](https://docs.microsoft.com/cli/azure/) <br>
[Meer informatie over Azure PowerShell](https://docs.microsoft.com/powershell/azure/) <br>
