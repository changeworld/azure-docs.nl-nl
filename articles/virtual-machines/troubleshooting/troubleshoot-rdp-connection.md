---
title: Kan geen verbinding maken met RDP met een virtuele Windows-machine in azure | Microsoft Docs
description: Problemen oplossen wanneer u in azure geen verbinding kunt maken met uw virtuele Windows-machine met behulp van Extern bureaublad
keywords: Fout met extern bureau blad, fout met verbinding met extern bureau blad, kan geen verbinding maken met VM, problemen oplossen met extern bureau blad
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: 0d740f8e-98b8-4e55-bb02-520f604f5b18
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 03/23/2018
ms.author: akjosh
ms.openlocfilehash: cbca8e631da8b99aa0ea4bdc6d099f3dbd2ed9b1
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77916605"
---
# <a name="troubleshoot-remote-desktop-connections-to-an-azure-virtual-machine"></a>Problemen met Extern bureaublad verbindingen met een virtuele machine van Azure oplossen
De RDP-verbinding (Remote Desktop Protocol) met uw Windows Azure VM (Virtual Machine) kan om verschillende redenen worden onderbroken, waardoor u geen toegang hebt tot uw VM. Het probleem kan in Extern bureaublad in de VM zitten, in de netwerkverbinding of in de Extern bureaublad-client in de hostcomputer. Dit artikel helpt u bij het oplossen van een aantal van de meest voorkomende methoden om problemen met de RDP-verbinding op te lossen. 

Als u op elk moment in dit artikel meer hulp nodig hebt, kunt u contact opnemen met de Azure-experts op [MSDN Azure en stack overflow forums](https://azure.microsoft.com/support/forums/). U kunt ook een Azure-ondersteuning-incident indienen. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer **ondersteuning verkrijgen**.

 

<a id="quickfixrdp"></a>

## <a name="quick-troubleshooting-steps"></a>Snelle probleemoplossings stappen
Probeer na elke stap voor het oplossen van problemen opnieuw verbinding te maken met de virtuele machine:

1. Extern bureaublad configuratie opnieuw instellen.
2. Controleer de regels voor de netwerk beveiligings groep/Cloud Services eind punten.
3. Bekijk de logboeken van de VM-console.
4. Stel de NIC voor de virtuele machine opnieuw in.
5. Controleer de Resource Health van de virtuele machine.
6. Stel uw VM-wacht woord opnieuw in.
7. Start de VM opnieuw op.
8. Implementeer uw VM opnieuw.

Blijf lezen als u gedetailleerde stappen en uitleg nodig hebt. Controleer of lokale netwerk apparatuur, zoals routers en firewalls, de uitgaande TCP-poort 3389 niet blokkeert, zoals vermeld in [gedetailleerde scenario's voor het oplossen van problemen met RDP](detailed-troubleshoot-rdp.md).

> [!TIP]
> Als de knop **verbinden** voor uw virtuele machine grijs wordt weer gegeven in de portal en u niet met Azure bent verbonden via een [snelle route](../../expressroute/expressroute-introduction.md) of een [site-naar-site-VPN-](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) verbinding, moet u een openbaar IP-adres maken en toewijzen aan uw virtuele machine voordat u RDP kunt gebruiken. Er is meer informatie over [openbare IP-adressen in Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) beschikbaar.


## <a name="ways-to-troubleshoot-rdp-issues"></a>Manieren om RDP-problemen op te lossen
U kunt virtuele machines die zijn gemaakt met het Resource Manager-implementatie model, met behulp van een van de volgende methoden oplossen:

* Azure Portal-geweldig als u de RDP-configuratie of gebruikers referenties snel opnieuw moet instellen en u de Azure-hulpprogram ma's niet hebt geïnstalleerd.
* Azure PowerShell: als u vertrouwd bent met een Power shell-prompt, stelt u de RDP-configuratie of gebruikers referenties snel opnieuw in met behulp van de Azure PowerShell-cmdlets.

U vindt hier ook stappen voor het oplossen van problemen met Vm's die zijn gemaakt met het [klassieke implementatie model](#troubleshoot-vms-created-using-the-classic-deployment-model).

<a id="fix-common-remote-desktop-errors"></a>

## <a name="troubleshoot-using-the-azure-portal"></a>Problemen oplossen met behulp van de Azure Portal
Probeer na elke stap voor het oplossen van problemen opnieuw verbinding te maken met uw VM. Als u nog steeds geen verbinding kunt maken, voert u de volgende stap uit.

1. **Stel de RDP-verbinding opnieuw**in. Met deze stap voor het oplossen van problemen wordt de RDP-configuratie opnieuw ingesteld wanneer externe verbindingen worden uitgeschakeld of door Windows Firewall regels RDP worden geblokkeerd.
   
    Selecteer uw virtuele machine in de Azure Portal. Schuif omlaag in het deel venster instellingen naar de sectie **ondersteuning en probleem oplossing** onder in de lijst. Klik op de knop **wacht woord opnieuw instellen** . Stel de **modus** in op **alleen configuratie herstellen** en klik vervolgens op de knop **bijwerken** :
   
    ![De RDP-configuratie opnieuw instellen in de Azure Portal](./media/troubleshoot-rdp-connection/reset-rdp.png)
2. **Controleer de regels voor de netwerk beveiligings groep**. Gebruik [IP-stroom controleren](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) om te bevestigen of verkeer naar of van een virtuele machine wordt geblokkeerd door een regel in een netwerkbeveiligingsgroep. U kunt ook de juiste regels voor beveiligings groepen controleren om ervoor te zorgen dat inkomende NSG regel bestaat en de prioriteit van de RDP-poort (standaard 3389) wordt weer gegeven. Zie [using effectief security rules to Troubleshooting VM Traffic Flow](../../virtual-network/diagnose-network-traffic-filter-problem.md)voor meer informatie.

3. **Diagnostische gegevens over opstarten van de virtuele machine bekijken**. Deze probleemoplossings stap controleert de logboeken van de VM-console om te bepalen of er een probleem is met de virtuele machine. Niet alle virtuele machines hebben diagnostische gegevens over opstarten ingeschakeld. deze stap voor probleem oplossing kan daarom optioneel zijn.
   
    Specifieke stappen voor probleem oplossing bevinden zich buiten het bereik van dit artikel, maar kunnen duiden op een breder probleem dat van invloed is op de RDP-connectiviteit. Zie [Diagnostische gegevens over opstarten voor virtuele machines](boot-diagnostics.md)voor meer informatie over het controleren van de console logboeken en de scherm afbeelding van de VM.

4. **Stel de NIC voor de virtuele machine opnieuw in**. Zie voor meer informatie [NIC opnieuw instellen voor Azure Windows VM](../windows/reset-network-interface.md).
5. **Controleer de resource Health van de virtuele machine**. Deze stap voor probleem oplossing verifieert of er geen bekende problemen zijn met het Azure-platform die van invloed kunnen zijn op de verbinding met de virtuele machine.
   
    Selecteer uw virtuele machine in de Azure Portal. Schuif omlaag in het deel venster instellingen naar de sectie **ondersteuning en probleem oplossing** onder in de lijst. Klik op de knop **resource status** . Een in orde zijnde VM-rapporten die **beschikbaar zijn**:
   
    ![Controleer de status van de VM-resource in de Azure Portal](./media/troubleshoot-rdp-connection/check-resource-health.png)
6. **Gebruikers referenties opnieuw instellen**. Met deze stap voor het oplossen van problemen wordt het wacht woord opnieuw ingesteld op een lokaal beheerders account wanneer u niet zeker weet of u de referenties hebt verg eten.  Wanneer u bent aangemeld bij de virtuele machine, moet u het wacht woord voor die gebruiker opnieuw instellen.
   
    Selecteer uw virtuele machine in de Azure Portal. Schuif omlaag in het deel venster instellingen naar de sectie **ondersteuning en probleem oplossing** onder in de lijst. Klik op de knop **wacht woord opnieuw instellen** . Zorg ervoor dat de **modus** is ingesteld op **wacht woord opnieuw instellen** en voer uw gebruikers naam en een nieuw wacht woord in. Klik ten slotte op de knop **bijwerken** :
   
    ![De gebruikers referenties in de Azure Portal opnieuw instellen](./media/troubleshoot-rdp-connection/reset-password.png)
7. **Start de VM opnieuw**op. Deze stap voor het oplossen van problemen kan eventuele onderliggende problemen met de VM zelf corrigeren.
   
    Selecteer uw virtuele machine in de Azure Portal en klik op het tabblad **overzicht** . Klik op de knop **opnieuw opstarten** :
   
    ![Start de VM opnieuw op in de Azure Portal](./media/troubleshoot-rdp-connection/restart-vm.png)
8. **Implementeer uw VM**opnieuw. Met deze stap voor probleem oplossing wordt uw virtuele machine opnieuw geïmplementeerd naar een andere host in azure om eventuele onderliggende problemen met het platform of netwerk op te lossen.
   
    Selecteer uw virtuele machine in de Azure Portal. Schuif omlaag in het deel venster instellingen naar de sectie **ondersteuning en probleem oplossing** onder in de lijst. Klik op de knop opnieuw **implementeren** en klik vervolgens op opnieuw **implementeren**:
   
    ![Implementeer de virtuele machine opnieuw in de Azure Portal](./media/troubleshoot-rdp-connection/redeploy-vm.png)
   
    Nadat deze bewerking is voltooid, gaan tijdelijke schijf gegevens verloren en worden dynamische IP-adressen die zijn gekoppeld aan de virtuele machine, bijgewerkt.

9. **Controleer de route ring**. Gebruik de [volgende hop](../../network-watcher/network-watcher-check-next-hop-portal.md) -mogelijkheid van Network Watcher om te controleren of een route niet voor komt dat verkeer wordt gerouteerd naar of van een virtuele machine. U kunt ook efficiënte routes bekijken om alle efficiënte routes voor een netwerk interface weer te geven. Zie [het gebruik van efficiënte routes voor het oplossen van problemen met de VM-verkeers stroom](../../virtual-network/diagnose-network-routing-problem.md)voor meer informatie.

10. Zorg ervoor dat een on-premises firewall, of firewall op uw computer, uitgaande TCP 3389-verkeer naar Azure toestaat.

Als u nog steeds RDP-problemen ondervindt, kunt u [een ondersteunings aanvraag openen](https://azure.microsoft.com/support/options/) of [meer informatie over het oplossen van problemen met interacties en stappen voor probleem oplossing](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)lezen.

## <a name="troubleshoot-using-azure-powershell"></a>Problemen oplossen met Azure PowerShell
Als u dat nog niet hebt [gedaan, installeert en configureert u de nieuwste Azure PowerShell](/powershell/azure/overview).

In de volgende voor beelden worden variabelen, zoals `myResourceGroup`, `myVM`en `myVMAccessExtension`, gebruikt. Vervang deze variabele namen en locaties door uw eigen waarden.

> [!NOTE]
> U kunt de gebruikers referenties en de RDP-configuratie opnieuw instellen met behulp van de [set-AzVMAccessExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmaccessextension) Power shell-cmdlet. In de volgende voor beelden is `myVMAccessExtension` een naam die u opgeeft als onderdeel van het proces. Als u eerder met de extensie vmaccessagent hebt gewerkt, kunt u de naam van de bestaande uitbrei ding ophalen met behulp van `Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"` om de eigenschappen van de virtuele machine te controleren. Als u de naam wilt weer geven, kijkt u in de sectie ' uitbrei dingen ' van de uitvoer.

Probeer na elke stap voor het oplossen van problemen opnieuw verbinding te maken met uw VM. Als u nog steeds geen verbinding kunt maken, voert u de volgende stap uit.

1. **Stel de RDP-verbinding opnieuw**in. Met deze stap voor het oplossen van problemen wordt de RDP-configuratie opnieuw ingesteld wanneer externe verbindingen worden uitgeschakeld of door Windows Firewall regels RDP worden geblokkeerd.
   
    In het volgende voor beeld wordt de RDP-verbinding opnieuw ingesteld op een virtuele machine met de naam `myVM` op de `WestUS` locatie en in de resource groep met de naam `myResourceGroup`:
   
    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location Westus -Name "myVMAccessExtension"
    ```
2. **Controleer de regels voor de netwerk beveiligings groep**. Deze stap voor het oplossen van problemen controleert of u een regel in uw netwerk beveiligings groep hebt om RDP-verkeer toe te staan. De standaard poort voor RDP is TCP-poort 3389. Een regel voor het toestaan van RDP-verkeer wordt mogelijk niet automatisch gemaakt wanneer u de virtuele machine maakt.
   
    Wijs eerst alle configuratie gegevens voor uw netwerk beveiligings groep toe aan de variabele `$rules`. In het volgende voor beeld wordt informatie opgehaald over de netwerk beveiligings groep met de naam `myNetworkSecurityGroup` in de resource groep met de naam `myResourceGroup`:
   
    ```powershell
    $rules = Get-AzNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
        -Name "myNetworkSecurityGroup"
    ```
   
    Bekijk nu de regels die zijn geconfigureerd voor deze netwerk beveiligings groep. Controleer als volgt of er een regel bestaat om TCP-poort 3389 voor binnenkomende verbindingen toe te staan:
   
    ```powershell
    $rules.SecurityRules
    ```
   
    In het volgende voor beeld ziet u een geldige beveiligings regel voor het toestaan van RDP-verkeer. U kunt zien `Protocol`, `DestinationPortRange`, `Access`en `Direction` correct zijn geconfigureerd:
   
    ```powershell
    Name                     : default-allow-rdp
    Id                       : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/default-allow-rdp
    Etag                     : 
    ProvisioningState        : Succeeded
    Description              : 
    Protocol                 : TCP
    SourcePortRange          : *
    DestinationPortRange     : 3389
    SourceAddressPrefix      : *
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 1000
    Direction                : Inbound
    ```
   
    Als u geen regel hebt waarmee RDP-verkeer is toegestaan, [maakt u een regel voor de netwerk beveiligings groep](../windows/nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). TCP-poort 3389 toestaan.
3. **Gebruikers referenties opnieuw instellen**. Deze stap voor het oplossen van problemen stelt het wacht woord voor het lokale beheerders account dat u opgeeft wanneer u niet zeker weet of u bent verg eten, de referenties.
   
    Geef eerst de gebruikers naam en het nieuwe wacht woord op door referenties toe te wijzen aan de `$cred` variabele als volgt:
   
    ```powershell
    $cred=Get-Credential
    ```
   
    Werk nu de referenties op uw virtuele machine bij. In het volgende voor beeld worden de referenties van een virtuele machine met de naam `myVM` op de `WestUS` locatie en in de resource groep met de naam `myResourceGroup`bijgewerkt:
   
    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location WestUS -Name "myVMAccessExtension" `
        -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password
    ```
4. **Start de VM opnieuw**op. Deze stap voor het oplossen van problemen kan eventuele onderliggende problemen met de VM zelf corrigeren.
   
    In het volgende voor beeld wordt de virtuele machine met de naam `myVM` opnieuw gestart in de resource groep met de naam `myResourceGroup`:
   
    ```powershell
    Restart-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```
5. **Implementeer uw VM**opnieuw. Met deze stap voor probleem oplossing wordt uw virtuele machine opnieuw geïmplementeerd naar een andere host in azure om eventuele onderliggende problemen met het platform of netwerk op te lossen.
   
    In het volgende voor beeld wordt de virtuele machine met de naam `myVM` op de `WestUS` locatie en in de resource groep met de naam `myResourceGroup`opnieuw geïmplementeerd:
   
    ```powershell
    Set-AzVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

6. **Controleer de route ring**. Gebruik de [volgende hop](../../network-watcher/network-watcher-check-next-hop-portal.md) -mogelijkheid van Network Watcher om te controleren of een route niet voor komt dat verkeer wordt gerouteerd naar of van een virtuele machine. U kunt ook efficiënte routes bekijken om alle efficiënte routes voor een netwerk interface weer te geven. Zie [het gebruik van efficiënte routes voor het oplossen van problemen met de VM-verkeers stroom](../../virtual-network/diagnose-network-routing-problem.md)voor meer informatie.

7. Zorg ervoor dat een on-premises firewall, of firewall op uw computer, uitgaande TCP 3389-verkeer naar Azure toestaat.

Als u nog steeds RDP-problemen ondervindt, kunt u [een ondersteunings aanvraag openen](https://azure.microsoft.com/support/options/) of [meer informatie over het oplossen van problemen met interacties en stappen voor probleem oplossing](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)lezen.

## <a name="troubleshoot-vms-created-using-the-classic-deployment-model"></a>Problemen met Vm's die zijn gemaakt met het klassieke implementatie model oplossen

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


Probeer na elke stap voor het oplossen van problemen opnieuw verbinding te maken met de virtuele machine.

1. **Stel de RDP-verbinding opnieuw**in. Met deze stap voor het oplossen van problemen wordt de RDP-configuratie opnieuw ingesteld wanneer externe verbindingen worden uitgeschakeld of door Windows Firewall regels RDP worden geblokkeerd.
   
    Selecteer uw virtuele machine in de Azure Portal. Klik op de **...** Klik op de knop meer en vervolgens op **externe toegang opnieuw instellen**:
   
    ![De RDP-configuratie opnieuw instellen in de Azure Portal](./media/troubleshoot-rdp-connection/classic-reset-rdp.png)
2. **Controleer Cloud Services-eind punten**. Deze stap voor het oplossen van problemen controleert of u eind punten hebt in uw Cloud Services om RDP-verkeer toe te staan. De standaard poort voor RDP is TCP-poort 3389. Een regel voor het toestaan van RDP-verkeer wordt mogelijk niet automatisch gemaakt wanneer u de virtuele machine maakt.
   
   Selecteer uw virtuele machine in de Azure Portal. Klik op de knop met **eind punten** om de eind punten weer te geven die momenteel zijn geconfigureerd voor uw VM. Controleer of er eind punten bestaan die RDP-verkeer op TCP-poort 3389 toestaan.
   
   In het volgende voor beeld ziet u geldige eind punten die RDP-verkeer toestaan:
   
   ![Cloud Services-eind punten in de Azure Portal controleren](./media/troubleshoot-rdp-connection/classic-verify-cloud-services-endpoints.png)
   
   Als u geen eind punt hebt waarmee RDP-verkeer is toegestaan, [maakt u een Cloud Services-eind punt](../windows/classic/setup-endpoints.md). TCP naar privé poort 3389 toestaan.
3. **Diagnostische gegevens over opstarten van de virtuele machine bekijken**. Deze probleemoplossings stap controleert de logboeken van de VM-console om te bepalen of er een probleem is met de virtuele machine. Niet alle virtuele machines hebben diagnostische gegevens over opstarten ingeschakeld. deze stap voor probleem oplossing kan daarom optioneel zijn.
   
    Specifieke stappen voor probleem oplossing bevinden zich buiten het bereik van dit artikel, maar kunnen duiden op een breder probleem dat van invloed is op de RDP-connectiviteit. Zie [Diagnostische gegevens over opstarten voor virtuele machines](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/)voor meer informatie over het controleren van de console logboeken en de scherm afbeelding van de VM.
4. **Controleer de resource Health van de virtuele machine**. Deze stap voor probleem oplossing verifieert of er geen bekende problemen zijn met het Azure-platform die van invloed kunnen zijn op de verbinding met de virtuele machine.
   
    Selecteer uw virtuele machine in de Azure Portal. Schuif omlaag in het deel venster instellingen naar de sectie **ondersteuning en probleem oplossing** onder in de lijst. Klik op de knop **resource Health** . Een in orde zijnde VM-rapporten die **beschikbaar zijn**:
   
    ![Controleer de status van de VM-resource in de Azure Portal](./media/troubleshoot-rdp-connection/classic-check-resource-health.png)
5. **Gebruikers referenties opnieuw instellen**. Met deze stap voor het oplossen van problemen wordt het wacht woord voor het lokale beheerders account dat u opgeeft, opnieuw ingesteld wanneer u niet zeker weet of u de referenties hebt verg eten.  Wanneer u bent aangemeld bij de virtuele machine, moet u het wacht woord voor die gebruiker opnieuw instellen.
   
    Selecteer uw virtuele machine in de Azure Portal. Schuif omlaag in het deel venster instellingen naar de sectie **ondersteuning en probleem oplossing** onder in de lijst. Klik op de knop **wacht woord opnieuw instellen** . Voer uw gebruikers naam en een nieuw wacht woord in. Klik ten slotte op de knop **Opslaan** :
   
    ![De gebruikers referenties in de Azure Portal opnieuw instellen](./media/troubleshoot-rdp-connection/classic-reset-password.png)
6. **Start de VM opnieuw**op. Deze stap voor het oplossen van problemen kan eventuele onderliggende problemen met de VM zelf corrigeren.
   
    Selecteer uw virtuele machine in de Azure Portal en klik op het tabblad **overzicht** . Klik op de knop **opnieuw opstarten** :
   
    ![Start de VM opnieuw op in de Azure Portal](./media/troubleshoot-rdp-connection/classic-restart-vm.png)

7. Zorg ervoor dat een on-premises firewall, of firewall op uw computer, uitgaande TCP 3389-verkeer naar Azure toestaat.

Als u nog steeds RDP-problemen ondervindt, kunt u [een ondersteunings aanvraag openen](https://azure.microsoft.com/support/options/) of [meer informatie over het oplossen van problemen met interacties en stappen voor probleem oplossing](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)lezen.

## <a name="troubleshoot-specific-rdp-errors"></a>Specifieke RDP-fouten oplossen
Er kan een specifiek fout bericht worden weer gegeven wanneer u probeert verbinding te maken met uw virtuele machine via RDP. Hier volgen de meest voorkomende fout berichten:

* [De externe sessie is beëindigd omdat er geen extern bureaublad licentie servers beschikbaar zijn om een licentie te bieden](troubleshoot-specific-rdp-errors.md#rdplicense).
* [Extern bureaublad kan de computer naam niet vinden](troubleshoot-specific-rdp-errors.md#rdpname).
* [Er is een verificatie fout opgetreden. Kan geen verbinding maken met de lokale beveiligings autoriteit](troubleshoot-specific-rdp-errors.md#rdpauth).
* [Windows-beveiligings fout: uw referenties werken niet](troubleshoot-specific-rdp-errors.md#wincred).
* [Deze computer kan geen verbinding maken met de externe computer](troubleshoot-specific-rdp-errors.md#rdpconnect).

## <a name="additional-resources"></a>Aanvullende resources
Lees de gedetailleerde [hand leiding voor het oplossen extern bureaublad van problemen](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)als er geen van deze fouten optrad en u nog steeds geen verbinding met de virtuele machine kunt maken via Extern bureaublad.
* Zie [problemen oplossen met toegang tot een toepassing die wordt uitgevoerd op](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)een virtuele machine van Azure voor informatie over het oplossen van problemen bij het openen van toepassingen die op een VM worden uitgevoerd.
* Zie [problemen met ssh-verbindingen met een virtuele Linux-machine in azure oplossen](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)als u problemen ondervindt met het gebruik van Secure Shell (SSH) om verbinding te maken met een virtuele Linux-machine in Azure.


