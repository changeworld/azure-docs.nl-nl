---
title: WinRM configureren na het maken van virtuele Azure-machines | Azure Marketplace
description: Hierin wordt uitgelegd hoe u WinRM (Windows Remote Management) configureert na het maken van een virtuele machine die door Azure wordt gehost.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: pabutler
ms.openlocfilehash: 7d050b32b212f66623a24bcf87d40111fc5973a5
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77481371"
---
# <a name="configure-winrm-after-virtual-machine-creation"></a>WinRM configureren na het maken van de virtuele machine

In dit artikel wordt uitgelegd hoe u een bestaande, door Azure gehoste virtuele machine (VM) configureert om WinRM via HTTPS in te scha kelen.  Deze configuratie is alleen van toepassing op Vm's op basis van Windows en vereist het volgende proces in twee stappen:

1. Schakel poort verkeer in voor het WinRM via HTTPS-protocol.  U configureert deze instelling voor uw virtuele machine in de Azure Portal.
2. Configureer de virtuele machine om WinRM in te scha kelen door de opgegeven Power shell-scripts uit te voeren.


## <a name="enabling-port-traffic"></a>Poort verkeer inschakelen

Het WinRM via HTTPS-protocol gebruikt poort 5986, die niet standaard is ingeschakeld op vooraf geconfigureerde Windows-Vm's die worden aangeboden op de Azure Marketplace. Als u dit protocol wilt inschakelen, gebruikt u de volgende stappen om een nieuwe regel toe te voegen aan de netwerk beveiligings groep (NSG) met de [Azure Portal](https://portal.azure.com).  Zie voor meer informatie over Nsg's [beveiligings groepen](https://docs.microsoft.com/azure/virtual-network/security-overview).

1.  Ga naar de Blade **virtuele machines >**   <*vm-naam*>   **> instellingen/netwerken**.
2.  Klik op de naam van de NSG (in dit voor beeld **testvm11002**) om de eigenschappen weer te geven:

    ![Eigenschappen van netwerk beveiligings groep](./media/nsg-properties.png)
 
3. Selecteer bij **instellingen**de optie **regels voor binnenkomende beveiliging** om deze Blade weer te geven.
4. Klik op **+ toevoegen** om een nieuwe regel te maken met de naam `WinRM_HTTPS` voor TCP-poort 5986.

    ![Regel voor inkomende netwerk beveiliging toevoegen](./media/nsg-new-rule.png)

5. Klik op **OK** wanneer u klaar bent met het leveren van waarden.  De lijst met binnenkomende beveiligings regels moet de volgende nieuwe vermeldingen bevatten.

    ![Lijst met regels voor inkomende netwerk beveiliging](./media/nsg-new-inbound-listing.png)


## <a name="configure-vm-to-enable-winrm"></a>VM configureren voor het inschakelen van WinRM 

Gebruik de volgende stappen om de Windows-functie voor extern beheer op uw Windows-VM in te scha kelen en te configureren.   

1. Een Extern bureaublad verbinding maken met uw door Azure gehoste VM.  Zie [verbinding maken en aanmelden bij een virtuele Azure-machine met Windows](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon)voor meer informatie.  De resterende stappen worden uitgevoerd op uw VM.
2. Down load de volgende bestanden en sla deze op in een map op uw virtuele machine:
    - [ConfigureWinRM. ps1](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/ConfigureWinRM.ps1)
    - [Makecert. exe](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/makecert.exe)
    - [winrmconf. cmd](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/winrmconf.cmd)
3. Open de **Power shell-console** met verhoogde bevoegdheden (**als administrator uitvoeren**). 
4. Voer de volgende opdracht uit, waarbij u de vereiste para meter opgeeft: de Fully Qualified Domain Name (FQDN) voor uw VM: <br/>
   `ConfigureWinRM.ps1 <vm-domain-name>`

    ![Power shell-configuratie script 1](./media/powershell-file1.png)

    Dit script is afhankelijk van de andere twee bestanden die zich in dezelfde map bevindt.


## <a name="next-steps"></a>Volgende stappen

Zodra u WinRM hebt geconfigureerd, bent u klaar om [uw VM te implementeren vanaf de bijbehorende](./cpp-deploy-vm-vhd.md)samenstellende vhd's.
