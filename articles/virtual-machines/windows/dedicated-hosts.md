---
title: Overzicht van voor Azure toegewezen hosts voor virtuele machines
description: Meer informatie over hoe met Azure toegewezen hosts kunnen worden gebruikt voor het implementeren van virtuele machines.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: 39d1c0ddb4961800e889346ec110ca629ae73546
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78251557"
---
# <a name="azure-dedicated-hosts"></a>Met Azure toegewezen hosts

De toegewezen Azure-host is een service die fysieke servers biedt en een of meer virtuele machines kan hosten die zijn toegewezen aan één Azure-abonnement. Toegewezen hosts zijn dezelfde fysieke servers die worden gebruikt in onze data centers als een resource. U kunt toegewezen hosts inrichten binnen een regio, een beschikbaarheids zone en een fout domein. Vervolgens kunt u Vm's rechtstreeks op uw ingerichte hosts plaatsen, in welke configuratie het beste voldoet aan uw behoeften.


[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]

## <a name="next-steps"></a>Volgende stappen

- U kunt een toegewezen host implementeren met behulp van [Azure PowerShell](dedicated-hosts-powershell.md), de [Portal](dedicated-hosts-portal.md)en [Azure cli](../linux/dedicated-hosts-cli.md).

- [Hier](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)vindt u een voor beeld van een sjabloon, die zowel zones als fout domeinen gebruikt voor maximale tolerantie in een regio.

- U kunt ook besparen op kosten met een [gereserveerd exemplaar van voor Azure toegewezen hosts](../prepay-dedicated-hosts-reserved-instances.md).
