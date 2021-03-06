---
title: Configuratie na de implementatie met behulp van extensies
description: Meer informatie over het gebruik van Azure Resource Manager-sjabloon uitbreidingen voor het leveren van configuraties na de implementatie.
author: mumian
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: jgao
ms.openlocfilehash: b3c4110c8761b3e8daf324d65ac7fa1dcbcdf61f
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023494"
---
# <a name="provide-post-deployment-configurations-by-using-extensions"></a>Configuraties na de implementatie opgeven met behulp van extensies

Sjabloon uitbreidingen zijn kleine toepassingen die configuratie en automatiserings taken na de implementatie leveren op Azure-resources. De populairste versie is de extensie van de virtuele machine. Zie [extensies en functies van virtuele machines voor Windows](../../virtual-machines/extensions/features-windows.md)en [virtuele-machine uitbreidingen en-functies voor Linux](../../virtual-machines/extensions/features-linux.md).

## <a name="extensions"></a>Extensies

De bestaande uitbrei dingen zijn:

- [Microsoft.Compute/virtualMachines/extensions](https://docs.microsoft.com/azure/templates/microsoft.compute/2018-10-01/virtualmachines/extensions)
- [Micro soft. Compute virtualMachineScaleSets/Extensions](https://docs.microsoft.com/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions)
- [Micro soft. HDInsight-clusters/-extensies](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/2018-06-01-preview/clusters)
- [Micro soft. SQL-servers/data bases/uitbrei dingen](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions) 
- [Microsoft.Web/sites/siteextensions](https://docs.microsoft.com/azure/templates/microsoft.web/2016-08-01/sites/siteextensions)

Als u de beschik bare uitbrei dingen wilt weten, bladert u naar de [sjabloon verwijzing](https://docs.microsoft.com/azure/templates/). Voer in **filteren op titel**de **extensie**in.

Zie voor meer informatie over het gebruik van deze uitbrei dingen:

- [Zelf studie: virtuele-machine uitbreidingen implementeren met Azure Resource Manager sjablonen](template-tutorial-deploy-vm-extensions.md).
- [Zelf studie: SQL BACPAC-bestanden met Azure Resource Manager sjablonen importeren](template-tutorial-deploy-sql-extensions-bacpac.md)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelf studie: extensies voor virtuele machines implementeren met Azure Resource Manager sjablonen](template-tutorial-deploy-vm-extensions.md)
