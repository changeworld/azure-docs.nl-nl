---
title: 'Azure VMware-oplossingen (AVS): een back-up maken van virtuele workload-machines op een automatische AVS-Cloud met Veeam'
description: Hierin wordt beschreven hoe u een back-up kunt maken van uw virtuele machines die worden uitgevoerd in een Privécloud op basis van Azure-AVS met Veeam B & R 9,5
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d8dc822ec07bdf061121b97384d0e2f9f239d6e2
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025126"
---
# <a name="back-up-workload-vms-on-avs-private-cloud-using-veeam-br"></a>Back-ups maken van werkbelasting Vm's op de AVS-privécloud met Veeam B & R

In deze hand leiding wordt beschreven hoe u een back-up kunt maken van uw virtuele machines die worden uitgevoerd in een Privécloud op basis van Azure-AVS met behulp van Veeam B & R 9,5.

## <a name="about-the-veeam-back-up-and-recovery-solution"></a>Over de Veeam back-up en herstel oplossing

De Veeam-oplossing omvat de volgende onderdelen.

**Back-upserver**

De back-upserver is een Windows-Server (VM) die fungeert als het beheer centrum voor Veeam en voert de volgende functies uit: 

* Coördineert de back-ups, replicatie, herstel verificatie en herstel taken
* Bepaalt de taak planning en resource toewijzing
* Hiermee kunt u back-upinfrastructuur onderdelen instellen en beheren en algemene instellingen opgeven voor de back-upinfrastructuur

**Proxy servers**

Proxy servers worden geïnstalleerd tussen de back-upserver en andere onderdelen van de back-upinfrastructuur. Ze beheren de volgende functies:

* Ophalen van VM-gegevens uit de productie opslag
* Compressie
* Ontdubbeling
* Versleuteling
* Verzen ding van gegevens naar de back-upopslagplaats

**Back-upopslagplaats**

De back-upopslagplaats is de opslag locatie waar Veeam back-upbestanden, VM-kopieën en meta gegevens voor gerepliceerde Vm's bewaard. De opslag plaats kan een Windows-of Linux-server zijn met lokale schijven (of gekoppelde NFS/SMB) of een apparaat voor hardwarematige ontdubbeling.

### <a name="veeam-deployment-scenarios"></a>Implementatie scenario's voor Veeam
U kunt Azure gebruiken om een back-upopslagplaats en een opslag doel te bieden voor back-ups op lange termijn en archivering. Alle back-upnetwerk verkeer tussen virtuele machines in de Privécloud van de AVS en de back-upopslagplaats in azure verloopt over een koppeling met een hoge band breedte, lage latentie. Replicatie verkeer tussen regio's wordt verplaatst naar het interne Azure-backplane netwerk, dat de bandbreedte kosten voor gebruikers verlaagt.

**Basis implementatie**

Voor omgevingen met een back-up van minder dan 30 TB, raadt AVS de volgende configuratie aan:

* Veeam back-upserver en proxy server geïnstalleerd op dezelfde VM in de Privécloud van de AVS.
* Een op Linux gebaseerde primaire back-upopslagplaats in azure die is geconfigureerd als een doel voor back-uptaken.
* `azcopy` gebruikt voor het kopiëren van de gegevens van de primaire back-upopslagplaats naar een Azure-Blob-container die wordt gerepliceerd naar een andere regio.

![Basis implementatie scenario's](media/veeam-basicdeployment.png)

**Geavanceerde implementatie**

Voor omgevingen met meer dan 30 TB om een back-up te maken, raadt AVS de volgende configuratie aan:

* Een proxy server per knoop punt in het vSAN-cluster, zoals aanbevolen door Veeam.
* De primaire back-upopslagplaats op basis van Windows in de AVS-privécloud om vijf dagen aan gegevens in de cache te plaatsen voor snel terugzetten.
* Linux backup-opslag plaats in azure als doel voor het maken van back-uptaken voor een langere retentie van de duur. Deze opslag plaats moet worden geconfigureerd als een scale-out back-upopslagplaats.
* `azcopy` gebruikt voor het kopiëren van de gegevens van de primaire back-upopslagplaats naar een Azure-Blob-container die wordt gerepliceerd naar een andere regio.

![Basis implementatie scenario's](media/veeam-advanceddeployment.png)

In de vorige afbeelding ziet u dat de back-upproxy een virtuele machine is met hot add Access to VM-werk belasting schijven in het vSAN-gegevens archief. Veeam maakt gebruik van de transport modus voor back-upproxy van virtuele apparaten voor vSAN.

## <a name="requirements-for-veeam-solution-on-avs"></a>Vereisten voor Veeam-oplossing op AVS

Voor de Veeam-oplossing moet u het volgende doen:

* Geef uw eigen Veeam-licenties op.
* Implementeer en beheer Veeam om een back-up te maken van de workloads die worden uitgevoerd in de AVS-Privécloud.

Deze oplossing biedt u volledige controle over het hulp programma Veeam backup en biedt de keuze om de native Veeam-interface of de Veeam vCenter-invoeg toepassing te gebruiken voor het beheren van VM-back-uptaken.

Als u een bestaande Veeam-gebruiker bent, kunt u de sectie over Veeam-oplossings onderdelen overs Laan en direct door gaan naar [Veeam-implementatie scenario's](#veeam-deployment-scenarios).

## <a name="install-and-configure-veeam-backups-in-your-avs-private-cloud"></a>Veeam-back-ups installeren en configureren in de Privécloud van uw AVS

In de volgende secties wordt beschreven hoe u een Veeam-back-upoplossing installeert en configureert voor de Privécloud van uw AVS.

Het implementatie proces bestaat uit de volgende stappen:

1. [vCenter-gebruikers interface: infrastructuur services in de Privécloud van uw AVS instellen](#vcenter-ui-set-up-infrastructure-services-in-your-avs-private-cloud)
2. [AVS-portal: AVS instellen voor persoonlijke Cloud netwerken voor Veeam](#avs-private-cloud-set-up-avs-private-cloud-networking-for-veeam)
3. [AVS-portal: bevoegdheden escaleren](#avs-private-cloud-escalate-privileges-for-cloudowner)
4. [Azure Portal: Verbind uw virtuele netwerk met de nieuwe AVS-Cloud](#azure-portal-connect-your-virtual-network-to-the-avs-private-cloud)
5. [Azure Portal: een back-upopslagplaats maken in azure](#azure-portal-connect-your-virtual-network-to-the-avs-private-cloud)
6. [Azure Portal: Azure Blob-opslag voor lange termijn gegevens retentie configureren](#configure-azure-blob-storage-for-long-term-data-retention)
7. [vCenter-gebruikers interface van AVS Privécloud: Installeer Veeam B & R](#vcenter-console-of-avs-private-cloud-install-veeam-br)
8. [Veeam-console: Veeam-back-up configureren & herstel software](#veeam-console-install-veeam-backup-and-recovery-software)
9. [AVS-portal: Veeam-toegang en de bevoegdheden van de escalatie instellen](#avs-portal-set-up-veeam-access-and-de-escalate-privileges)

### <a name="before-you-begin"></a>Voordat u begint

Het volgende is vereist voordat u begint met de implementatie van Veeam:

* Een Azure-abonnement dat eigendom is van u
* Een vooraf gemaakte Azure-resource groep
* Een virtueel Azure-netwerk in uw abonnement
* Een Azure-opslag account
* Een [persoonlijke AVS-Cloud](create-private-cloud.md) die is gemaakt met de AVS-Portal.  

De volgende items zijn vereist tijdens de implementatie fase:

* VMware-sjablonen voor Windows om Veeam te installeren (zoals Windows Server 2012 R2-64 bits installatie kopie)
* Eén beschik bare VLAN dat is geïdentificeerd voor het back-upnetwerk
* CIDR van het subnet dat moet worden toegewezen aan het back-upnetwerk
* Veeam 9,5 U3 Installeer bare media (ISO) geüpload naar de vSAN-gegevens opslag van de AVS-Privécloud

### <a name="vcenter-ui-set-up-infrastructure-services-in-your-avs-private-cloud"></a>vCenter-gebruikers interface: infrastructuur services in de Privécloud van uw AVS instellen

Configureer infrastructuur services in de cloud van de AVS, zodat u uw workloads en hulpprogram ma's eenvoudig kunt beheren.

* U kunt een externe ID-provider toevoegen, zoals wordt beschreven in [vCenter-identiteits bronnen instellen om Active Directory te gebruiken](set-vcenter-identity.md) als een van de volgende opties van toepassing is:
  * U wilt gebruikers van uw on-premises Active Directory (AD) in de Privécloud van uw AVS identificeren.
  * U wilt een AD-advertentie instellen in de Privécloud van uw AVS voor alle gebruikers.
  * U wilt Azure AD gebruiken.
* Als u IP-adres zoeken, IP-adres beheer en naamomzettingsservices wilt bieden voor uw workloads in de Privécloud, stelt u een DHCP-en DNS-server in zoals beschreven in [DNS-en DHCP-toepassingen en werk belastingen instellen in de privécloud van uw AVS](dns-dhcp-setup.md).

### <a name="avs-private-cloud-set-up-avs-private-cloud-networking-for-veeam"></a>Privécloud: een privécloud-netwerk instellen voor Veeam

Open de AVS-Portal om een automatische AVS-Cloud netwerk in te stellen voor de Veeam-oplossing.

Maak een VLAN voor het back-upnetwerk en wijs hieraan een subnet CIDR toe. Zie [vlan's en subnetten maken en beheren](create-vlan-subnet.md)voor instructies.

Maak firewall regels tussen het subnet van het beheer en het back-upnetwerk om netwerk verkeer toe te staan op poorten die worden gebruikt door Veeam. Zie de Veeam-onderwerp [gebruikte poorten](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95). Zie [firewall tabellen en-regels instellen](firewall.md)voor instructies over het maken van een firewall regel.

De volgende tabel bevat een lijst met poorten.

| Pictogram | Beschrijving | Pictogram | Beschrijving |
| ------------ | ------------- | ------------ | ------------- |
| Backup Server  | vCenter  | HTTPS/TCP  | 443 |
| Backup Server <br> *Vereist voor de implementatie van Veeam Backup &-replicatie onderdelen* | Back-upproxy  | TCP/UDP  | 135, 137 tot 139 en 445 |
    | Backup Server   | DNS  | UDP  | 53  | 
    | Backup Server   | Server voor Veeam-update meldingen  | TCP  | 80  | 
    | Backup Server   | Veeam-licentie-update server  | TCP  | 443  | 
    | Back-upproxy   | vCenter |   |   | 
    | Back-upproxy  | Opslag plaats voor Linux-back-ups   | TCP  | 22  | 
    | Back-upproxy  | Windows Backup-opslag plaats  | TCP  | 49152-65535   | 
    | Back-upopslagplaats  | Back-upproxy  | TCP  | 2500-5000  | 
    | Opslag plaats voor bron back-ups<br> *Gebruikt voor het maken van back-uptaken*  | Opslag plaats voor doel back-ups  | TCP  | 2500-5000  | 

Maak firewall regels tussen het subnet van de workload en het back-upnetwerk zoals beschreven in [firewall tabellen en-regels instellen](firewall.md). Voor toepassings bewuste back-up en herstel moeten [extra poorten](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95) worden geopend op de werk belasting-vm's die specifieke toepassingen hosten.

AVS biedt standaard een 1 Gbps ExpressRoute-koppeling. Voor grotere omgevings grootten is mogelijk een grotere band breedte-koppeling vereist. Neem contact op met de ondersteuning van Azure voor meer informatie over koppelingen voor hogere band breedte.

Als u wilt door gaan met de installatie, hebt u de verificatie sleutel en peer-circuit-URI nodig en hebt u toegang tot uw Azure-abonnement. Deze informatie is beschikbaar op de pagina Virtual Network verbinding in de AVS-Portal. Zie voor instructies [informatie over peering voor het virtuele netwerk van Azure verkrijgen](virtual-network-connection.md)voor de AVS-verbinding. [Neem contact op met de ondersteuning](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)als u problemen hebt met het verkrijgen van de informatie.

### <a name="avs-private-cloud-escalate-privileges-for-cloudowner"></a>Privécloud: machtigingen voor **Cloudowner** escaleren

De standaard gebruiker ' cloudowner ' beschikt niet over voldoende bevoegdheden in de AVS Private Cloud vCenter om VEEAM te installeren, dus de vCenter-bevoegdheden van de gebruiker moeten worden geëscaleerd. Zie [bevoegdheden voor escalatie](escalate-private-cloud-privileges.md)voor meer informatie.

### <a name="azure-portal-connect-your-virtual-network-to-the-avs-private-cloud"></a>Azure Portal: Verbind uw virtuele netwerk met de nieuwe AVS-Cloud

Verbind het virtuele netwerk met de Privécloud-privécloud door de instructies in [Azure Virtual Network-verbinding te volgen met behulp van ExpressRoute](azure-expressroute-connection.md).

### <a name="azure-portal-create-a-backup-repository-vm"></a>Azure Portal: een back-up maken van de opslag plaats-VM

1. Maak een Standard D2-v3-VM met (2 Vcpu's en 8 GB geheugen).
2. Selecteer de installatie kopie op basis van CentOS 7,4.
3. Een netwerk beveiligings groep (NSG) configureren voor de virtuele machine. Controleer of de virtuele machine geen openbaar IP-adres heeft en niet bereikbaar is vanaf het open bare Internet.
4. Maak een gebruikers naam en wacht woord op basis van het account voor de nieuwe virtuele machine. Zie [een virtuele Linux-machine maken in de Azure Portal](../virtual-machines/linux/quick-create-portal.md)voor instructies.
5. Maak een standaard HDD van 1x512 GiB en koppel deze aan de opslag plaats-VM. Zie [How to attach a Managed Data Disk to a Windows VM in the Azure Portal](../virtual-machines/windows/attach-managed-disk-portal.md)voor instructies.
6. [Maak een xfs-volume op de beheerde schijf](https://www.digitalocean.com/docs/volumes/how-to/). Meld u aan bij de virtuele machine met behulp van de eerder genoemde referenties. Voer het volgende script uit om een logisch volume te maken, voeg de schijf hieraan toe, maak een XFS-bestandssysteem [partitie](https://www.digitalocean.com/docs/volumes/how-to/partition/) en [koppel](https://www.digitalocean.com/docs/volumes/how-to/mount/) de partitie onder het pad/backup1.

    Voorbeeld script:

    ```
    sudo pvcreate /dev/sdc
    sudo vgcreate backup1 /dev/sdc
    sudo lvcreate -n backup1 -l 100%FREE backup1
    sudo mkdir -p /backup1
    sudo chown veeamadmin /backup1
    sudo chmod 775 /backup1
    sudo mkfs.xfs -d su=64k -d sw=1 -f /dev/mapper/backup1-backup1
    sudo mount -t xfs /dev/mapper/backup1-backup1 /backup1
    ```

7. Maak/backup1 beschikbaar als een NFS-koppel punt naar de Veeam-back-upserver die wordt uitgevoerd in de AVS-Privécloud. Zie voor instructies het artikel Digital-Oceaan [een NFS-koppeling instellen op CentOS 6](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nfs-mount-on-centos-6). Gebruik deze NFS-share naam wanneer u de back-upopslagplaats in de Veeam-back-upserver configureert.

8. Configureer filter regels in de NSG voor de back-up van de opslag plaats-VM zodat alle netwerk verkeer van en naar de virtuele machine expliciet wordt toegestaan.

> [!NOTE]
> Veeam Backup &-replicatie maakt gebruik van het SSH-protocol om te communiceren met Linux-back-upopslagplaatsen en vereist het SCP-hulp programma op Linux-opslag plaatsen. Controleer of de SSH-daemon juist is geconfigureerd en of het SCP beschikbaar is op de Linux-host.

### <a name="configure-azure-blob-storage-for-long-term-data-retention"></a>Azure Blob-opslag configureren voor het bewaren van lange termijn gegevens

1. Maak een opslag account voor algemeen gebruik (GPv2) van het standaard type en een BLOB-container zoals beschreven in de micro soft video [aan de slag met Azure Storage](https://azure.microsoft.com/resources/videos/get-started-with-azure-storage).
2. Maak een Azure storage-container, zoals beschreven in de verwijzing [Create container](https://docs.microsoft.com/rest/api/storageservices/create-container) .
2. Down load het `azcopy`-opdracht regel programma voor Linux van micro soft. U kunt de volgende opdrachten gebruiken in de bash-shell in CentOS 7,5.

    ```
    wget -O azcopy.tar.gz https://aka.ms/downloadazcopylinux64
    tar -xf azcopy.tar.gz
    sudo ./install.sh
    sudo yum -y install libunwind.x86_64
    sudo yum -y install icu
    ```

3. Gebruik de opdracht `azcopy` om back-upbestanden van en naar de BLOB-container te kopiëren. Zie [gegevens overdragen met AzCopy in Linux](../storage/common/storage-use-azcopy-linux.md) voor gedetailleerde opdrachten.

### <a name="vcenter-console-of-avs-private-cloud-install-veeam-br"></a>vCenter-console van de AVS-Privécloud: Installeer Veeam B & R

Toegang tot vCenter via de Privécloud van uw AVS om een Veeam-service account te maken, Veeam B & R 9,5 te installeren en Veeam te configureren met behulp van het service account.

1. Maak een nieuwe rol met de naam ' Veeam backup Role ' en wijs de benodigde machtigingen toe, zoals aanbevolen door Veeam. Zie voor meer informatie het onderwerp Veeam [vereist machtigingen](https://helpcenter.veeam.com/docs/backup/vsphere/required_permissions.html?ver=95).
2. Maak een nieuwe groep ' Veeam-gebruikers groep ' in vCenter en wijs deze toe aan de Veeam-back-upfunctie.
3. Maak een nieuwe gebruiker van het Veeam-service account en voeg deze toe aan de Veeam-gebruikers groep.

    ![Een Veeam-service account maken](media/veeam-vcenter01.png)

4. Maak een gedistribueerde poort groep in vCenter met behulp van het netwerk-VLAN van de back-up. Bekijk voor meer informatie de VMware-video die [een gedistribueerde poort groep maakt in de vSphere-webclient](https://www.youtube.com/watch?v=wpCd5ZbPOpA).
5. Maak de Vm's voor de Veeam-back-up en proxy servers in vCenter conform de [Veeam-systeem vereisten](https://helpcenter.veeam.com/docs/backup/vsphere/system_requirements.html?ver=95). U kunt Windows 2012 R2 of Linux gebruiken. Zie [vereisten voor het gebruik van Linux-back-upopslagplaatsen](https://www.veeam.com/kb2216)voor meer informatie.
6. Koppel de Installeer bare Veeam ISO als een cd-romstation in de VM van de Veeam-back-upserver.
7. Als u een RDP-sessie naar de Windows 2012 R2-computer (het doel voor de Veeam-installatie) wilt, [installeert u Veeam B & R 9.5 U3](https://helpcenter.veeam.com/docs/backup/vsphere/install_vbr.html?ver=95) in een Windows 2012 R2-VM.
8. Zoek het interne IP-adres van de Veeam-back-upserver-VM en configureer het IP-adres als statisch in de DHCP-server. De exacte stappen die vereist zijn om dit te doen, zijn afhankelijk van de DHCP-server. In het geval van een Netgate <a href="https://www.netgate.com/docs/pfsense/dhcp/dhcp-server.html" target="_blank">-artikel statische DHCP-toewijzingen</a> wordt uitgelegd hoe u een DHCP-server configureert met een pfSense-router.

### <a name="veeam-console-install-veeam-backup-and-recovery-software"></a>Veeam-console: Veeam-back-up en herstel software installeren

Configureer met behulp van de Veeam-console Veeam-back-up en herstel software. Zie [Veeam Backup & Replication v9-Installation and Deployment](https://www.youtube.com/watch?v=b4BqC_WXARk)(Engelstalig) voor meer informatie.

1. Voeg VMware vSphere toe als een beheerde server omgeving. Geef desgevraagd de referenties op van het Veeam-service account dat u hebt gemaakt aan het begin van de [vCenter-console van de AVS-privécloud: Installeer Veeam B & R](#vcenter-console-of-avs-private-cloud-install-veeam-br).

    * Gebruik de standaard instellingen voor laad beheer en standaard geavanceerde instellingen.
    * Stel de locatie van de koppelings server in als de back-upserver.
    * Wijzig de back-uplocatie voor de configuratie van de Veeam-server in de externe opslag plaats.

2. Voeg de Linux-server in azure toe als opslag plaats voor back-ups.

    * Gebruik de standaard instellingen voor Load Control en voor de geavanceerde instellingen. 
    * Stel de locatie van de koppelings server in als de back-upserver.
    * Wijzig de back-uplocatie voor de configuratie van de Veeam-server in de externe opslag plaats.

3. Versleuteling van configuratie back-up inschakelen met **back-upinstellingen voor de configuratie van de Home->** .

4. Voeg een Windows Server-VM toe als een proxy server voor VMware-omgeving. Met behulp van ' Traffic Rules ' voor een proxy, versleutelt u de back-upgegevens via de kabel.

5. Configureer back-uptaken.
    * Volg de instructies bij het [maken van een back-uptaak](https://www.youtube.com/watch?v=YHxcUFEss4M)voor het configureren van back-uptaken.
    * Schakel versleuteling van back-upbestanden onder **Geavanceerde instellingen > opslag**in.

6. Back-upkopie taken configureren.

    * Als u back-uptaken wilt configureren, volgt u de instructies in de video [maken van een back-upkopie taak](https://www.youtube.com/watch?v=LvEHV0_WDWI&t=2s).
    * Schakel versleuteling van back-upbestanden onder **Geavanceerde instellingen > opslag**in.

### <a name="avs-portal-set-up-veeam-access-and-de-escalate-privileges"></a>AVS-portal: Veeam-toegang en de bevoegdheden van de escalatie instellen
Maak een openbaar IP-adres voor de Veeam-back-up en herstel server. Zie [Public IP-adressen toewijzen](public-ips.md)voor instructies.

Maak een firewall regel met om de Veeam-back-upserver toe te staan een uitgaande verbinding te maken met de Veeam-website voor het downloaden van updates/patches op TCP-poort 80. Zie [firewall tabellen en-regels instellen](firewall.md)voor instructies.

Als u de bevoegdheden wilt deescaleren, raadpleegt u [bevoegdheden deescaleren](escalate-private-cloud-privileges.md#de-escalate-privileges).

## <a name="references"></a>Naslaginformatie

### <a name="avs-references"></a>AVS-verwijzingen

* [Een Privécloud maken](create-private-cloud.md)
* [VLAN'S/subnetten maken en beheren](create-vlan-subnet.md)
* [vCenter-identiteits bronnen](set-vcenter-identity.md)
* [DNS en DHCP-installatie van workload](dns-dhcp-setup.md)
* [Bevoegdheden escaleren](escalate-privileges.md)
* [Firewall tabellen en-regels instellen](firewall.md)
* [Machtigingen voor de persoonlijke cloud van AVS](learn-private-cloud-permissions.md)
* [Open bare IP-adressen toewijzen](public-ips.md)

### <a name="veeam-references"></a>Veeam-verwijzingen

* [Gebruikte poorten](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95)
* [Vereiste machtigingen](https://helpcenter.veeam.com/docs/backup/vsphere/required_permissions.html?ver=95)
* [Systeem vereisten](https://helpcenter.veeam.com/docs/backup/vsphere/system_requirements.html?ver=95)
* [Veeam Backup &-replicatie installeren](https://helpcenter.veeam.com/docs/backup/vsphere/install_vbr.html?ver=95)
* [Vereiste modules en machtigingen voor FLR-en opslag plaatsen-ondersteuning voor meerdere besturings systemen voor Linux](https://www.veeam.com/kb2216)
* [Veeam back-up & replicatie v9-installatie en implementatie-video](https://www.youtube.com/watch?v=b4BqC_WXARk)
* [Veeam v9 maken van een back-uptaak-video](https://www.youtube.com/watch?v=YHxcUFEss4M)
* [Veeam v9 maken van een back-uptaak-video](https://www.youtube.com/watch?v=LvEHV0_WDWI&t=2s)

### <a name="azure-references"></a>Azure-verwijzingen

* [Een virtuele netwerk gateway configureren voor ExpressRoute met behulp van de Azure Portal](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)
* [Een VNet verbinden met een circuit-ander abonnement](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription)
* [Een virtuele Linux-machine maken in de Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Een beheerde gegevens schijf koppelen aan een virtuele Windows-machine in de Azure Portal](../virtual-machines/windows/attach-managed-disk-portal.md)
* [Aan de slag met Azure Storage-video](https://azure.microsoft.com/resources/videos/get-started-with-azure-storage)
* [Container maken](https://docs.microsoft.com/rest/api/storageservices/create-container)
* [Gegevens overdragen met AzCopy voor Linux](../storage/common/storage-use-azcopy-linux.md)

### <a name="vmware-references"></a>VMware-verwijzingen

* [Een gedistribueerde poort groep maken in de vSphere-webclient-video](https://www.youtube.com/watch?v=wpCd5ZbPOpA)

### <a name="other-references"></a>Verwijzingen naar andere

* [Een XFS-volume maken op de beheerde schijf-RedHat](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/ch-xfs)
* [Een NFS-koppeling instellen op CentOS 7-HowToForge](https://www.howtoforge.com/nfs-server-and-client-on-centos-7)
* [De DHCP-server configureren-Netgate](https://www.netgate.com/docs/pfsense/dhcp/dhcp-server.html)
