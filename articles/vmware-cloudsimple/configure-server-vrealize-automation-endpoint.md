---
title: 'Azure VMware-oplossingen (AVS): vCenter instellen op een AVS-Privécloud voor vRealize Automation'
description: Hierin wordt beschreven hoe u een VMware vCenter-Server instelt in de Privécloud van uw AVS als eind punt voor VMware vRealize Automation
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 41106498594ac05b944323e5f5e63de739aedf37
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024837"
---
# <a name="set-up-vcenter-on-your-avs-private-cloud-for-vmware-vrealize-automation"></a>VCenter instellen in de Privécloud van uw AVS voor VMware vRealize Automation

U kunt een VMware vCenter-Server op uw AVS-Privécloud instellen als een eind punt voor VMware vRealize Automation.

## <a name="before-you-begin"></a>Voordat u begint

Voltooi deze taken voordat u de vCenter-Server configureert:

* Configureer een [site-naar-site-VPN-verbinding](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) tussen uw on-premises omgeving en de privécloud van uw AVS.
* Het [door sturen van DNS van lokale DNS-aanvragen](on-premises-dns-setup.md) naar de DNS-servers voor uw AVS-privécloud configureren.
* Dien een [ondersteunings aanvraag](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) in om een vRealize Automation IaaS-gebruiker met beheerders rechten te maken met de set machtigingen die in de volgende tabel worden vermeld.

| Kenmerk waarde | Machtiging |
------------ | ------------- |  
| Gegevensopslag |  Ruimte toewijzen <br> Door gegevens opslag bladeren |
| Data Store-cluster | Een Data Store-cluster configureren |
| Map | Map maken <br>Map verwijderen |
| Wereldwijd |  Aangepaste kenmerken beheren<br>Aangepast kenmerk instellen |
| Netwerk | Netwerk toewijzen |
| Machtigingen | Machtigingen wijzigen |
| Bron | Virtuele machine aan resource groep toewijzen<br>Virtuele machine die is uitgeschakeld migreren<br>De ingeschakelde virtuele machine wordt gemigreerd |
| Inventaris van virtuele machines |  Maken op basis van bestaande<br>Nieuwe maken<br>Verplaatsen<br>Verwijderen | 
| Interactie van de virtuele machine |  CD-media configureren<br>Interactie met de console<br>Apparaat-verbinding<br>Uitschakelen<br>Inschakelen<br>Opnieuw instellen<br>Tijdelijk intrekken<br>Hulpprogram ma's installeren | 
| Configuratie van virtuele machine |  Bestaande schijf toevoegen<br>Nieuwe schijf toevoegen<br>Toevoegen of verwijderen<br>Schijf verwijderen<br>Geavanceerd<br>CPU-aantal wijzigen<br>Resource wijzigen<br>Virtuele schijf uitbreiden<br>Schijf Wijzigingen bijhouden<br>Geheugen<br>Apparaatinstellingen wijzigen<br>Naam wijzigen<br>Annotatie instellen (versie 5,0 en hoger)<br>Instellingen<br>Swapfile-plaatsing |
| Inrichting |  Aanpassen<br>Sjabloon klonen<br>Virtuele machine klonen<br>Sjabloon implementeren<br>Aanpassings specificaties lezen |
| Status van de virtuele machine | Moment opname maken<br>Moment opname verwijderen<br>Herstellen naar moment opname |

## <a name="install-vrealize-automation-in-your-on-premises-environment"></a>VRealize Automation installeren in uw on-premises omgeving

1. Meld u aan bij het vRealize Automation IaaS-server apparaat als de IaaS-beheerder die voor u gemaakte AVS-ondersteuning heeft.
2. Implementeer een vSphere-agent voor het vRealize Automation-eind punt.
    1. Ga naar https://*vra-URL*: 5480/Installer, waarbij *vra-URL* de URL is die u gebruikt voor toegang tot de gebruikers interface van vRealize Automation-beheer.
    2. Klik op het **installatie programma IaaS** om het installatie programma te downloaden.<br>
    De naam Conventie voor het installatie bestand is setup_*vra-url*@5480.exe.
    3. Voer het installatie programma uit. Klik in het welkomst scherm op **volgende**.
    4. Accepteer de gebruiksrecht overeenkomst en klik op **volgende**.
    5. Geef de aanmeldings gegevens op, klik op **certificaat accepteren**en klik vervolgens op **volgende**.
    ![vRA-referenties](media/configure-vra-endpoint-login.png)
    6. Selecteer **aangepaste installatie** -en **proxy-agents** en klik op **volgende**.
    ![type installatie van vRA](media/configure-vra-endpoint-install-type.png)
    7. Voer de aanmeldings gegevens van de IaaS-server in en klik op **volgende**. Als u Active Directory gebruikt, voert u de gebruikers naam in de indeling **domein\gebruiker** in. Gebruik anders **user@domain** indeling.
    ![vRA-aanmeldings gegevens](media/configure-vra-endpoint-account.png)
    8. Voer voor de proxy-instellingen **vSphere** in voor het **agent type**. Voer een naam in voor de agent.
    9. Voer de FQDN van de IaaS-server in de velden **Manager servicehost** en **host Manager web service** in. Klik op **testen** om de verbinding voor elk van de FQDN-waarden te testen. Als de test mislukt, wijzigt u de DNS-instellingen zodat de hostnaam van de IaaS-server wordt opgelost.
    10. Voer een naam in voor het vCenter-Server eindpunt voor de Privécloud van de AVS. Noteer de naam voor later in het configuratie proces.

        ![vRA-installatie proxy](media/configure-vra-endpoint-proxy.png)

    11. Klik op **Volgende**.
    12. Klik op **Installeren**.

## <a name="configure-the-vsphere-agent"></a>De vSphere-agent configureren

1. Ga naar https://*vra-URL*/vcac en meld u aan als **ConfigurationAdmin**.
2. Selecteer **infrastructuur** > - **eind punten** > - **eind punten**.
3. Selecteer **nieuw** > **virtuele** > **vSphere**.
4. Voer de naam van het vSphere-eind punt in dat u in de vorige procedure hebt opgegeven.
5. Voer bij **adres**de vCenter Server URL van de AVS-Cloud in de indeling https://*vCenter-FQDN*/SDK, waarbij *vCenter-FQDN* de naam is van de vCenter-Server.
6. Voer de referenties in voor de gebruiker van de vRealize Automation IaaS die AVS-ondersteuning voor u heeft gemaakt.
7. Klik op **verbinding testen** om de gebruikers referenties te valideren. Als de test mislukt, controleert u de URL, account gegevens en de naam van het [eind punt](#verify-the-endpoint-name) en voert u de test opnieuw uit.
8. Klik na een geslaagde test op **OK** om het vSphere-eind punt te maken.
    toegang tot ![vRA endpoint config](media/configure-vra-endpoint-vra-edit.png)

### <a name="verify-the-endpoint-name"></a>Controleer de naam van het eind punt

Ga als volgt te werk om de juiste naam van het vCenter Server-eind punt te identificeren:

1. Open een opdracht prompt op het IaaS-apparaat.
2. Wijzig de map in C:\Program Files (x86) \VMware\vCAC\Agents\agent-name, waarbij de *Agent naam* de naam is die u aan het vCenter Server-eind punt hebt toegewezen.
3. Voer de volgende opdracht uit.

```
..\..\Server\DynamicOps.Vrm.VRMencrypt.exe VRMAgent.exe.config get
```

De uitvoer ziet er ongeveer als volgt uit. De waarde van het veld `managementEndpointName` is de naam van het eind punt.

```
managementEndpointName: cslab1pc3-vc
doDeletes: true
```
