---
title: Log Analytics VM-extensie voor Linux
description: De Log Analytics-agent op Linux-machine met behulp van de extensie van een virtuele machine implementeren.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: c7bbf210-7d71-4a37-ba47-9c74567a9ea6
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: akjosh
ms.openlocfilehash: 9ddac229fc38a91a8b97b24dc2807080b2295758
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79250554"
---
# <a name="log-analytics-virtual-machine-extension-for-linux"></a>Log Analytics VM-extensie voor Linux

## <a name="overview"></a>Overzicht

Azure Monitor Logboeken biedt bewakings-, waarschuwings-en waarschuwings functies voor het door voeren van waarschuwingen voor Cloud-en on-premises activa. De Log Analytics extensie van de virtuele machine voor Linux wordt gepubliceerd en ondersteund door micro soft. De extensie voor de Log Analytics-agent geïnstalleerd op virtuele Azure-machines en virtuele machines voor een bestaande Log Analytics-werkruimte worden ingeschreven. In dit document worden de ondersteunde platforms, configuraties en implementatie-opties voor de extensie van de Log Analytics-virtuele machine voor Linux.

>[!NOTE]
>Als onderdeel van de lopende overgang van Microsoft Operations Management Suite (OMS) naar Azure Monitor, wordt de OMS-Agent voor Windows of Linux worden aangeduid als de Log Analytics-agent voor Windows en de Log Analytics-agent voor Linux.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Vereisten

### <a name="operating-system"></a>Besturingssysteem

Raadpleeg het [overzichts artikel log Analytics agent](../../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems) voor meer informatie over de ondersteunde Linux-distributies.

### <a name="agent-and-vm-extension-version"></a>Versie agent en VM-extensie
De volgende tabel bevat een toewijzing van de versie van de Log Analytics VM-extensie en bundel van Log Analytics-agent voor elke versie. Een koppeling naar de opmerkingen bij de release voor de versie van Log Analytics-agent-bundel is opgenomen. Opmerkingen bij de release bevatten informatie over oplossingen voor problemen en nieuwe functies die beschikbaar zijn voor een opgegeven agent-release.  

| Versie van de extensie log Analytics virtuele Linux-machine | Log Analytics-Agent bundelversie krijgt | 
|--------------------------------|--------------------------|
| 1.12.25 | [1.12.15-0](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.12.15-0) |
| 1.11.15 | [1.11.0-9](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.11.0-9) |
| 1.10.0 | [1.10.0-1](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.10.0-1) |
| 1.9.1 | [1.9.0-0](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.9.0-0) |
| 1.8.11 | [1.8.1-256](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.8.1.256)| 
| 1.8.0 | [1.8.0-256](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/1.8.0-256)| 
| 1.7.9 | [1.6.1-3](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.6.1.3)| 
| 1.6.42.0 | [1.6.0-42](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.6.0-42)| 
| 1.4.60.2 | [1.4.4-210](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.4-210)| 
| 1.4.59.1 | [1.4.3-174](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.3-174)|
| 1.4.58.7 | [14.2-125](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.2-125)|
| 1.4.56.5 | [1.4.2-124](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.2-124)|
| 1.4.55.4 | [1.4.1-123](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.1-123)|
| 1.4.45.3 | [1.4.1-45](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.1-45)|
| 1.4.45.2 | [1.4.0-45](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.0-45)|
| 1.3.127.5 | [1.3.5-127](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201705-v1.3.5-127)| 
| 1.3.127.7 | [1.3.5-127](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201705-v1.3.5-127)|
| 1.3.18.7 | [1.3.4-15](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201704-v1.3.4-15)|  

### <a name="azure-security-center"></a>Azure Security Center

Azure Security Center richt de Log Analytics-agent automatisch en verbindt deze met een standaard Log Analytics-werkruimte gemaakt door ASC in uw Azure-abonnement. Als u van Azure Security Center gebruikmaakt, niet uitgevoerd door de stappen in dit document. In dat geval wordt de geconfigureerde werkruimte overschreven en de verbinding met Azure Security Center.

### <a name="internet-connectivity"></a>Internetconnectiviteit

De Log Analytics-Agent-extensie voor Linux is vereist dat de virtuele doelmachine is verbonden met internet. 

## <a name="extension-schema"></a>Extensieschema

De volgende JSON ziet u het schema voor de Log Analytics-Agent-extensie. De uitbrei ding vereist de werk ruimte-ID en de werkruimte sleutel van de doel Log Analytics werkruimte; deze waarden vindt u in de Azure Portal [log Analytics-werk ruimte](../../azure-monitor/learn/quick-collect-linux-computer.md#obtain-workspace-id-and-key) . Omdat de sleutel van de werkruimte moet worden behandeld als gevoelige gegevens, moet deze worden opgeslagen in de instellingsconfiguratie van een beveiligde. Azure-VM-extensie beveiligde instellingsgegevens versleuteld en alleen op de virtuele doelmachine worden ontsleuteld. Houd er rekening mee dat **workspaceId** en **workspaceKey** hoofdletter gevoelig zijn.

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.7",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

>[!NOTE]
>Het bovenstaande schema wordt ervan uitgegaan dat er op het hoogste niveau van de sjabloon worden geplaatst. Als u de virtuele machine in de sjabloon plaatst, moeten de eigenschappen `type` en `name` worden gewijzigd, [zoals hieronder beschreven](#template-deployment).

### <a name="property-values"></a>Waarden van eigenschappen

| Naam | Waarde / voorbeeld |
| ---- | ---- |
| apiVersion | 2018-06-01 |
| publisher | Microsoft.EnterpriseCloud.Monitoring |
| type | OmsAgentForLinux |
| typeHandlerVersion | 1.7 |
| werkruimte-id (bijvoorbeeld) | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (bijvoorbeeld) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |


## <a name="template-deployment"></a>Sjabloonimplementatie

Azure VM-extensies kunnen worden geïmplementeerd met Azure Resource Manager-sjablonen. Sjablonen zijn ideaal bij het implementeren van een of meer virtuele machines waarvoor de configuratie na implementatie vereist is, zoals het onboarden van Azure Monitor-Logboeken. Een voor beeld van een resource manager-sjabloon met de extensie van de Log Analytics agent-VM vindt u in de [Galerie van Azure Quick](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm)start. 

De JSON-configuratie voor een VM-extensie worden genest in de bron van de virtuele machine of geplaatst op de hoofdmap of het hoogste niveau van een Resource Manager JSON-sjabloon. De plaatsing van de JSON-configuratie is van invloed op de waarde van de resourcenaam en het type. Zie voor meer informatie [naam en type voor onderliggende resources instellen](../../azure-resource-manager/templates/child-resource-name-type.md). 

Het volgende voorbeeld wordt ervan uitgegaan dat de VM-extensie is genest in de bron van de virtuele machine. Bij het nesten van de extensie bron wordt de JSON in het `"resources": []`-object van de virtuele machine geplaatst.

```json
{
  "type": "extensions",
  "name": "OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.7",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

Bij het plaatsen van de JSON-extensie in de hoofdmap van de sjabloon, naam van de resource bevat een verwijzing naar de bovenliggende virtuele machine en het type weerspiegelt de geneste configuratie.  

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "<parentVmResource>/OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.7",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

## <a name="azure-cli-deployment"></a>Azure CLI-implementatie

De Azure CLI kan worden gebruikt voor het implementeren van de Log Analytics-Agent-VM-extensie op een bestaande virtuele machine. Vervang de waarde van *myWorkspaceKey* hieronder door de werkruimte sleutel en de *myWorkspaceId* -waarde met uw werk ruimte-id. Deze waarden zijn te vinden in uw Log Analytics-werk ruimte in de Azure Portal onder *Geavanceerde instellingen*. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.10.1 --protected-settings '{"workspaceKey":"myWorkspaceKey"}' \
  --settings '{"workspaceId":"myWorkspaceId"}'
```

## <a name="troubleshoot-and-support"></a>Problemen oplossen en ondersteuning

### <a name="troubleshoot"></a>Problemen oplossen

Gegevens over de status van extensie-implementaties kunnen worden opgehaald uit de Azure-portal en met behulp van de Azure CLI. Als wilt zien de implementatiestatus van extensies voor een bepaalde virtuele machine, voert u de volgende opdracht uit met de Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Extensie uitvoering uitvoer wordt vastgelegd in het volgende bestand:

```
/opt/microsoft/omsagent/bin/stdout
```

### <a name="error-codes-and-their-meanings"></a>Foutcodes en hun betekenis

| Foutcode | Betekenis | Mogelijke actie |
| :---: | --- | --- |
| 9 | Met de naam voortijdig inschakelen | [Werk de Azure Linux-agent](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent) bij naar de meest recente beschik bare versie. |
| 10 | Virtuele machine is al verbonden met een Log Analytics-werkruimte | StopOnMultipleConnections ingesteld op false in de openbare-instellingen voor de virtuele machine verbinding met de werkruimte die is opgegeven in het extensieschema, of verwijder deze eigenschap. Deze virtuele machine in rekening gebracht wanneer er voor elke werkruimte is verbonden met. |
| 11 | Ongeldige configuratie opgegeven voor de extensie | Volg de voorgaande voorbeelden om in te stellen alle eigenschapswaarden die nodig zijn voor implementatie. |
| 17 | Meld u Analytics-pakket-installatiefout | 
| 19 | Fout tijdens de installatie van de OMI-pakket | 
| 20 | Fout tijdens de installatie van de SCX-pakket |
| 51 | Deze extensie wordt niet ondersteund voor besturingssysteem van de virtuele machine | |
| 55 | Kan geen verbinding maken met de Azure Monitor-service of de vereiste pakketten ontbreken of met dpkg Package Manager is vergrendeld| Controleer of het systeem heeft toegang tot Internet of dat een geldige HTTP-proxy is opgegeven. Bovendien Controleer de juistheid van de werkruimte-ID en controleer of curl en tar-hulpprogramma's zijn geïnstalleerd. |

Meer informatie over het oplossen van problemen vindt u in de [log Analytics-agent-for-Linux Troubleshooting Guide (Engelstalig](../../azure-monitor/platform/vmext-troubleshoot.md)).

### <a name="support"></a>Ondersteuning

Als u op elk moment in dit artikel meer hulp nodig hebt, kunt u contact opnemen met de Azure-experts op [MSDN Azure en stack overflow forums](https://azure.microsoft.com/support/forums/). U kunt ook een Azure-ondersteuning-incident indienen. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer ondersteuning verkrijgen. Lees de [Veelgestelde vragen over ondersteuning voor Microsoft Azure](https://azure.microsoft.com/support/faq/)voor meer informatie over het gebruik van Azure-ondersteuning.
