---
title: Onderliggende runbooks in Azure Automation
description: Hierin worden de verschillende methoden beschreven voor het starten van een runbook in Azure Automation vanuit een ander runbook en het delen van gegevens ertussen.
services: automation
ms.subservice: process-automation
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 42362a170f493afd51a5d4ee139620ad25b54e79
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/14/2020
ms.locfileid: "79367360"
---
# <a name="child-runbooks-in-azure-automation"></a>Onderliggende runbooks in Azure Automation

Het is een aanbevolen procedure in Azure Automation om herbruikbare, modulaire runbooks te schrijven met een discrete functie die wordt aangeroepen door andere runbooks. Een bovenliggend runbook roept vaak een of meer onderliggende runbooks aan om de vereiste functionaliteit uit te voeren. Er zijn twee manieren om een onderliggend runbook aan te roepen en er zijn verschillende verschillen die u moet begrijpen om te bepalen welke het meest geschikt is voor uw scenario's.

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [de module Azure PowerShell installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor de installatie-instructies voor AZ module op uw Hybrid Runbook Worker. Voor uw Automation-account kunt u uw modules bijwerken naar de nieuwste versie met behulp van [het bijwerken van Azure PowerShell-modules in azure Automation](automation-update-azure-modules.md).

## <a name="invoking-a-child-runbook-using-inline-execution"></a>Aanroepen van een onderliggend runbook met inline-uitvoering

Als u een runbook inline vanuit een ander runbook wilt aanroepen, gebruikt u de naam van het runbook en geeft u waarden op voor de para meters, net zoals u een activiteit of cmdlet zou gebruiken. Alle runbooks in hetzelfde Automation-account zijn beschikbaar voor alle anderen die op deze manier moeten worden gebruikt. Het bovenliggende runbook wacht tot het onderliggende runbook is voltooid voordat het naar de volgende regel wordt verplaatst. alle uitvoer wordt rechtstreeks naar de bovenliggende opdracht geretourneerd.

Als u een runbook inline aanroept, wordt deze uitgevoerd in dezelfde taak als het bovenliggende runbook. Er is geen indicatie van de taak geschiedenis van het onderliggende runbook. Eventuele uitzonde ringen en stroom uitvoer van het onderliggende runbook zijn gekoppeld aan de bovenliggende. Dit gedrag resulteert in minder taken en maakt het eenvoudiger om te volgen en problemen op te lossen.

Wanneer een runbook wordt gepubliceerd, moeten alle onderliggende runbooks die worden aangeroepen, al worden gepubliceerd. De reden hiervoor is dat Azure Automation een koppeling bouwt met een wille keurige onderliggende runbooks wanneer het een runbook compileert. Als de onderliggende runbooks nog niet zijn gepubliceerd, lijkt het bovenliggende runbook goed te publiceren, maar genereert een uitzonde ring wanneer het wordt gestart. Als dit het geval is, kunt u het bovenliggende runbook opnieuw publiceren om naar behoren naar de onderliggende runbooks te verwijzen. U hoeft het bovenliggende runbook niet opnieuw te publiceren als een onderliggend runbook is gewijzigd, omdat de koppeling al is gemaakt.

De para meters van een onderliggend runbook met de naam inline kunnen van elk gegevens type zijn, inclusief complexe objecten. Er is geen [JSON-serialisatie](start-runbooks.md#runbook-parameters), omdat u het runbook start met behulp van de Azure portal of met de cmdlet [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) .

### <a name="runbook-types"></a>Runbooktypen

Welke typen runbook kunnen elkaar aanroepen?

* Een [Power shell-runbook](automation-runbook-types.md#powershell-runbooks) en een [grafisch runbook](automation-runbook-types.md#graphical-runbooks) kunnen elk ander inline aanroepen, zoals op Power shell is gebaseerd.
* Een [Power shell workflow-runbook](automation-runbook-types.md#powershell-workflow-runbooks) en een grafisch Power shell werk stroom-runbook kunnen elk ander inline aanroepen, zowel op Power shell-werk stroom gebaseerd.
* De Power shell-typen en de Power shell-werk stroom typen kunnen niet elk ander inline aanroepen en moeten `Start-AzAutomationRunbook`gebruiken.

Wanneer is de volg orde van publicatie belang rijk?

De publicatie volgorde van runbooks is alleen van belang voor Power shell-werk stromen en grafische runbooks in Power shell-werk stromen.

Als uw runbook een grafisch of een onderliggend runbook van een Power shell-werk stroom aanroept met inline-uitvoering, wordt de naam van het runbook gebruikt. De naam moet beginnen met `.\\` om op te geven dat het script zich in de lokale map bevindt.

### <a name="example"></a>Voorbeeld

In het volgende voor beeld wordt een onderliggend runbook voor een test gestart dat een complex object, een geheel getal en een Booleaanse waarde accepteert. De uitvoer van het onderliggende runbook is toegewezen aan een variabele. In dit geval is het onderliggende runbook een Power shell workflow-runbook.

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true
```

Hier volgt een voor beeld van het gebruik van een Power shell-runbook als onderliggend object.

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true
```

## <a name="starting-a-child-runbook-using-a-cmdlet"></a>Een onderliggend runbook starten met een cmdlet

> [!IMPORTANT]
> Als uw runbook een onderliggend runbook aanroept met de `Start-AzAutomationRunbook` cmdlet met de para meter `Wait` en het onderliggende runbook een object resultaat oplevert, kan er een fout optreden in de bewerking. Als u de fout wilt omzeilen, raadpleegt u [onderliggende runbooks met object uitvoer](troubleshoot/runbooks.md#child-runbook-object) voor meer informatie over het implementeren van de logica voor het controleren van de resultaten met behulp van de cmdlet [Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) .

U kunt `Start-AzAutomationRunbook` gebruiken om een runbook te starten zoals beschreven in [om een Runbook te starten met Windows Power shell](start-runbooks.md#start-a-runbook-with-powershell). Er zijn twee modi voor het gebruik van deze cmdlet. In de ene modus retourneert de cmdlet de taak-ID wanneer de taak voor het onderliggende runbook is gemaakt. In de andere modus, die door het script wordt ingeschakeld door het opgeven van de *wait* -para meter, wacht de cmdlet totdat de onderliggende taak is voltooid en de uitvoer van het onderliggende runbook wordt geretourneerd.

De taak uit een onderliggend runbook dat is gestart met een cmdlet, wordt afzonderlijk van de bovenliggende runbook-taak uitgevoerd. Dit gedrag resulteert in meer taken dan bij het starten van het runbook inline en maakt het lastig om de taken bij te houden. De bovenliggende site kan meer dan een onderliggend runbook asynchroon starten zonder te wachten tot de bewerking is voltooid. Voor deze parallelle uitvoering die de onderliggende runbooks inline aanroept, moet het bovenliggende runbook het [sleutel woord parallel](automation-powershell-workflow.md#parallel-processing)gebruiken.

De onderliggende runbook-uitvoer wordt niet betrouwbaar teruggestuurd naar het bovenliggende runbook vanwege een time-out. Bovendien kunnen variabelen zoals `$VerbosePreference`, `$WarningPreference`en anderen niet worden door gegeven aan de onderliggende runbooks. Als u deze problemen wilt voor komen, kunt u de onderliggende runbooks starten als afzonderlijke automatiserings taken met behulp van `Start-AzAutomationRunbook` met de `Wait`-para meter. Deze techniek blokkeert het bovenliggende runbook tot het onderliggende runbook is voltooid.

Als u niet wilt dat het bovenliggende runbook wordt geblokkeerd tijdens het wachten, kunt u het onderliggende runbook starten met `Start-AzAutomationRunbook` zonder de para meter `Wait`. In dit geval moet uw runbook [Get-AzAutomationJob](/powershell/module/az.automation/get-azautomationjob) gebruiken om te wachten tot de taak is voltooid. Het moet ook [Get-AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput) en [Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) gebruiken om de resultaten op te halen.

Para meters voor een onderliggend runbook dat is gestart met een cmdlet, worden opgegeven als een hash-tabel, zoals beschreven in [runbook-para meters](start-runbooks.md#runbook-parameters). Alleen eenvoudige gegevens typen kunnen worden gebruikt. Als het runbook een parameter met een complex gegevenstype heeft, klikt u vervolgens het moet dit inline worden aangeroepen.

De context van het abonnement kan verloren gaan bij het starten van onderliggende runbooks als afzonderlijke taken. Voor het onderliggende runbook om AZ module-cmdlets uit te voeren op basis van een specifiek Azure-abonnement, moet het onderliggend element worden geverifieerd bij dit abonnement onafhankelijk van het bovenliggende runbook.

Als taken binnen hetzelfde Automation-account werken met meer dan één abonnement, kan het selecteren van een abonnement in de ene taak de momenteel geselecteerde abonnements context voor andere taken wijzigen. Gebruik `Disable-AzContextAutosave –Scope Process` aan het begin van elk runbook om deze situatie te voor komen. Met deze actie wordt de context alleen opgeslagen bij de uitvoering van het runbook.

### <a name="example"></a>Voorbeeld

In het volgende voor beeld wordt een onderliggend runbook met para meters gestart en wordt gewacht tot het is voltooid met behulp van de cmdlet `Start-AzAutomationRunbook` met de para meter `Wait`. Zodra dit is voltooid, wordt de uitvoer van de cmdlet verzameld van het onderliggende runbook. Als u `Start-AzAutomationRunbook`wilt gebruiken, moet het script worden geverifieerd bij uw Azure-abonnement.

```azurepowershell-interactive
# Ensure that the runbook does not inherit an AzContext
Disable-AzContextAutosave –Scope Process

# Connect to Azure with Run As account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Get-AzSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzContext $AzureContext `
    –Parameters $params –Wait
```

## <a name="comparison-of-methods-for-calling-a-child-runbook"></a>Vergelijking van methoden voor het aanroepen van een onderliggend runbook

De volgende tabel bevat een overzicht van de verschillen tussen de twee manieren om een runbook vanuit een ander runbook aan te roepen.

|  | Inline | Cmdlet |
|:--- |:--- |:--- |
| Job |Onderliggende runbooks worden uitgevoerd in dezelfde taak als het bovenliggende item. |Een afzonderlijke taak is voor het onderliggende runbook gemaakt. |
| Uitvoering |Bovenliggend runbook wacht tot het onderliggende runbook om te voltooien voordat u doorgaat. |Het bovenliggende runbook blijft onmiddellijk nadat het onderliggende runbook is gestart *of* het bovenliggende runbook wacht totdat de onderliggende taak is voltooid. |
| Uitvoer |Bovenliggend runbook kan uitvoer rechtstreeks van onderliggend runbook ophalen. |Bovenliggend runbook moet uitvoer ophalen van onderliggend runbook-taak *of* bovenliggend runbook kan rechtstreeks uitvoer ophalen van onderliggend runbook. |
| Parameters |Waarden voor de parameters van onderliggend runbook worden afzonderlijk opgegeven en elk gegevenstype kunnen gebruiken. |Waarden voor de para meters van het onderliggende runbook moeten worden gecombineerd in één hashtabel. Deze hashtabel kan alleen eenvoudige, matrix-en object gegevens typen bevatten die gebruikmaken van JSON-serialisatie. |
| Automation-account |Bovenliggend runbook kan alleen een onderliggend runbook gebruiken in hetzelfde Automation-account. |Bovenliggende runbooks kunnen een onderliggend runbook gebruiken uit elk Automation-account, van hetzelfde Azure-abonnement en zelfs van een ander abonnement waarmee u verbinding hebt. |
| Publiceren |Onderliggend runbook moet worden gepubliceerd voordat bovenliggend runbook wordt gepubliceerd. |Onderliggend runbook wordt gepubliceerd telkens voordat het bovenliggende runbook wordt gestart. |

## <a name="next-steps"></a>Volgende stappen

* [Een runbook starten in Azure Automation](start-runbooks.md)
* [Runbook-uitvoer en berichten in Azure Automation](automation-runbook-output-and-messages.md)