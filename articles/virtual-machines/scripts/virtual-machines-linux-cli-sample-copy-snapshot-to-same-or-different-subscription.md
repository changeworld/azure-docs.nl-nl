---
title: Moment opname van een beheerde schijf kopiëren naar een abonnement-CLI-voor beeld
description: 'Azure CLI-voorbeeld script: een moment opname van een beheerde schijf kopiëren (of verplaatsen) naar hetzelfde of een ander abonnement met CLI'
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 19c791cf1f394f5aab6ad2fcd7f98c4b30497286
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75458547"
---
# <a name="copy-snapshot-of-a-managed-disk-to-same-or-different-subscription-with-cli"></a>Met CLI een momentopname van een beheerde schijf kopiëren naar hetzelfde of een ander abonnement

Met dit script wordt een momentopname van een beheerde schijf gekopieerd naar hetzelfde of een ander abonnement. Gebruik dit script voor de volgende scenario's:

1. Migreer een moment opname in Premium Storage (Premium_LRS) naar de standaard opslag (Standard_LRS of Standard_ZRS) om uw kosten te verlagen.
1. Migreer een moment opname van lokaal redundante opslag (Premium_LRS, Standard_LRS) naar zone redundante opslag (Standard_ZRS) om te profiteren van de hogere betrouw baarheid van ZRS-opslag.
1. Een moment opname verplaatsen naar een ander abonnement in dezelfde regio voor een langere Bewaar periode.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.sh "Copy snapshot")]


## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt om een momentopname te maken in het doelabonnement met behulp van de id van de bronmomentopname. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az snapshot show](https://docs.microsoft.com/cli/azure/snapshot) | Hiermee haalt u alle eigenschappen van een momentopname op aan de hand van de naam en de eigenschappen van de resourcegroep van de momentopname. De id-eigenschap wordt gebruikt om de momentopname naar een ander abonnement te kopiëren.  |
| [az snapshot create](https://docs.microsoft.com/cli/azure/snapshot) | Hiermee wordt een momentopname gekopieerd door een momentopname te maken in een ander abonnement met dezelfde id en naam als de bovenliggende momentopname.  |

## <a name="next-steps"></a>Volgende stappen

[Een virtuele machine maken van een momentopname](./virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Zie de [documentatie van Azure CLI](https://docs.microsoft.com/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende CLI-scriptvoorbeelden voor virtuele machines en beheerde schijven vindt u in de [Azure-documentatie voor Linux-VM's](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
