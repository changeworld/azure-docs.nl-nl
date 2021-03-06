---
title: Hybrid Runbook Worker voor Azure Automation in Linux
description: Dit artikel bevat informatie over het installeren van een Azure Automation Hybrid Runbook Worker, zodat u runbooks kunt uitvoeren op Linux-computers in uw lokale Data Center of in de cloud omgeving.
services: automation
ms.subservice: process-automation
ms.date: 03/02/2020
ms.topic: conceptual
ms.openlocfilehash: 2579748d9c68512e51fe46ec70084c30d06953bc
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79278764"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Een Linux-Hybrid Runbook Worker implementeren

U kunt de functie Hybrid Runbook Worker van Azure Automation gebruiken om runbooks rechtstreeks uit te voeren op de computer waarop de rol wordt gehost en aan resources in de omgeving om deze lokale resources te beheren. De Linux-Hybrid Runbook Worker voert runbooks uit als een speciale gebruiker die kan worden uitgebreid voor het uitvoeren van opdrachten die moeten worden uitgebreid. Runbooks worden opgeslagen en beheerd in Azure Automation en vervolgens aan een of meer aangewezen computers geleverd.

In dit artikel wordt beschreven hoe u de Hybrid Runbook Worker op een Linux-computer installeert.

## <a name="supported-linux-operating-systems"></a>Ondersteunde Linux-besturingssystemen

De functie Hybrid Runbook Worker ondersteunt de volgende distributies:

* Amazon Linux 2012,09 tot 2015,09 (x86/x64)
* CentOS Linux 5, 6 en 7 (x86/x64)
* Oracle Linux 5, 6 en 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6 en 7 (x86/x64)
* Debian GNU/Linux 6, 7 en 8 (x86/x64)
* Ubuntu 12,04 LTS, 14,04 LTS, 16,04 LTS en 18,04 (x86/x64)
* SUSE Linux Enterprise Server 11 en 12 (x86/x64)

## <a name="installing-a-linux-hybrid-runbook-worker"></a>Een Linux-Hybrid Runbook Worker installeren

Als u een Hybrid Runbook Worker wilt installeren en configureren op uw Linux-computer, volgt u een eenvoudig proces om de rol hand matig te installeren en te configureren. Hiervoor moet u de **automatisering-Hybrid worker** oplossing inschakelen in uw Azure log Analytics-werk ruimte en vervolgens een reeks opdrachten uitvoeren om de computer als een werk nemer te registreren en toe te voegen aan een groep.

De minimale vereisten voor een Linux-Hybrid Runbook Worker zijn:

* Twee kernen
* 4 GB aan RAM-geheugen
* Poort 443 (uitgaand)

### <a name="package-requirements"></a>Pakket vereisten

| **Vereist pakket** | **Beschrijving** | **Minimale versie**|
|--------------------- | --------------------- | -------------------|
|Glibc |GNU C-bibliotheek| 2.5-12 |
|Openssl| OpenSSL-bibliotheken | 1,0 (TLS 1,1 en TLS 1,2 worden ondersteund|
|Ezelsoor | Krul webclient | 7.15.5|
|Python-ctypes | Python 2. x is vereist |
|PAM | Pluggable verificatie modules|
| **Optioneel pakket** | **Beschrijving** | **Minimale versie**|
| PowerShell Core | Als u Power shell-runbooks wilt uitvoeren, moet Power shell zijn geïnstalleerd. Zie [Power shell Core installeren in Linux](/powershell/scripting/install/installing-powershell-core-on-linux) voor meer informatie over het installeren ervan.  | 6.0.0 |

### <a name="installation"></a>Installatie

Voordat u verder gaat, moet u de Log Analytics-werk ruimte zien waaraan uw Automation-account is gekoppeld. Let ook op de primaire sleutel voor uw Automation-account. U kunt beide van de Azure Portal vinden door uw Automation-account te selecteren, **werk ruimte** te selecteren voor de werk ruimte-id en **sleutels** te selecteren voor de primaire sleutel. Zie [uw netwerk configureren](automation-hybrid-runbook-worker.md#network-planning)voor meer informatie over de poorten en adressen die u nodig hebt voor de Hybrid Runbook Worker.

1. Gebruik een van de volgende methoden om de **automatisering-Hybrid worker** -oplossing in Azure in te scha kelen:

   * Voeg de **automatisering-Hybrid worker** oplossing toe aan uw abonnement met behulp van de procedure in [Azure monitor-logboeken-oplossingen toevoegen aan uw werk ruimte](../log-analytics/log-analytics-add-solutions.md).
   * Voer de volgende cmdlet uit:

        ```azurepowershell-interactive
         Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

1. Installeer de Log Analytics-agent voor Linux door de volgende opdracht uit te voeren. Vervang \<WorkspaceID\> en \<WorkspaceKey\> door de juiste waarden uit uw werk ruimte.

   [!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)]

   ```bash
   wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
   ```

1. Voer de volgende opdracht uit, waarbij u de waarden voor de para meters *-w*, *-k*, *-g*en *-e*wijzigt. Voor de para meter *-g* , vervang de waarde door de naam van de Hybrid Runbook worker groep waaraan de nieuwe Linux-Hybrid Runbook worker moet worden toegevoegd. Als de naam niet bestaat in uw Automation-account, wordt er een nieuwe Hybrid Runbook Worker groep met die naam gemaakt.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
   ```

1. Nadat de opdracht is voltooid, worden in de pagina **Hybrid worker groepen** in het Azure Portal de nieuwe groep en het aantal leden weer gegeven. Als dit een bestaande groep is, wordt het aantal leden verhoogd. U kunt de groep selecteren in de lijst op de pagina **Hybrid worker groepen** en de tegel **Hybrid Workers** selecteren. Op de pagina **Hybrid Workers** ziet u elk lid van de groep die wordt weer gegeven.

> [!NOTE]
> Als u de Azure Monitor extensie van de virtuele machine voor Linux voor een Azure-VM gebruikt, wordt u aangeraden om `autoUpgradeMinorVersion` in te stellen op False als de versies van automatische upgrades de Hybrid Runbook Worker kunnen veroorzaken. Zie [Azure cli-implementatie ](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment)voor meer informatie over het hand matig bijwerken van de extensie.

## <a name="turning-off-signature-validation"></a>Validatie van hand tekeningen uitschakelen

Voor Linux Hybrid Runbook Workers is standaard handtekening validatie vereist. Als u een niet-ondertekend runbook uitvoert op een werk nemer, ziet u een fout melding dat de validatie van de hand tekening is mislukt. Voer de volgende opdracht uit om handtekening validatie uit te scha kelen. Vervang de tweede para meter door uw log Analytics-werk ruimte-ID.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <LogAnalyticsworkspaceId>
 ```

## <a name="supported-runbook-types"></a>Ondersteunde typen runbook

Linux Hybrid Runbook Workers bieden geen ondersteuning voor de volledige set Runbook-typen in Azure Automation.

De volgende typen runbook werken op een Linux-Hybrid Worker:

* Python 2
* PowerShell

  > [!NOTE]
  > Power shell-runbooks vereisen dat Power shell core wordt geïnstalleerd op de Linux-machine. Zie [Power shell core in Linux installeren](/powershell/scripting/install/installing-powershell-core-on-linux) voor meer informatie over het installeren ervan.

De volgende typen runbook werken niet op een Linux-Hybrid Worker:

* PowerShell-werkstroom
* Grafieken
* Grafische power shell-werk stroom

## <a name="next-steps"></a>Volgende stappen

* Zie [Runbooks uitvoeren op een Hybrid Runbook worker](automation-hrw-run-runbooks.md)voor meer informatie over het configureren van uw runbooks om processen te automatiseren in uw on-premises Data Center of andere cloud omgeving.
* Zie [Azure Automation Hybrid Runbook Workers verwijderen](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker)voor instructies voor het verwijderen van Hybrid runbook Workers.
* Zie [problemen met Linux Hybrid Runbook Workers oplossen](troubleshoot/hybrid-runbook-worker.md#linux) voor meer informatie over het oplossen van problemen met uw Hybrid runbook Workers
