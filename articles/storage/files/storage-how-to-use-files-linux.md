---
title: Azure Files met Linux gebruiken | Microsoft Docs
description: Meer informatie over het koppelen van een Azure-bestands share via SMB op Linux.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: b2469a2b5819b3011f919a2b483933bb030eed70
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79268195"
---
# <a name="use-azure-files-with-linux"></a>Azure Files gebruiken met Linux
[Azure Files ](storage-files-introduction.md) is het eenvoudig te gebruiken cloudbestandssysteem van Microsoft. Azure-bestands shares kunnen worden gekoppeld in Linux-distributies met behulp van de [SMB-kernel-client](https://wiki.samba.org/index.php/LinuxCIFS). In dit artikel ziet u twee manieren om een Azure-bestands share te koppelen: op aanvraag met de `mount`-opdracht en de opstart procedure door een vermelding in `/etc/fstab`te maken.

De aanbevolen manier om een Azure-bestands share te koppelen aan Linux maakt gebruik van SMB 3,0. Azure Files moet standaard versleuteling in transit hebben. dit wordt alleen ondersteund door SMB 3,0. Azure Files biedt ook ondersteuning voor SMB 2,1, dat geen ondersteuning biedt voor versleuteling tijdens de overdracht, maar u mag geen Azure-bestands shares met SMB 2,1 vanuit een andere Azure-regio of on-premises koppelen om veiligheids redenen. Tenzij uw toepassing specifiek SMB 2,1 vereist, is er weinig reden om deze te gebruiken sinds de populairste, recent uitgebrachte Linux-distributies SMB 3,0:  

| | SMB 2.1 <br>(Koppelt op Vm's binnen dezelfde Azure-regio) | SMB 3.0 <br>(Koppelt van on-premises en kruis regio's) |
| --- | :---: | :---: |
| Ubuntu | 14.04 + | 16.04 + |
| Red Hat Enterprise Linux (RHEL) | 7+ | 7.5 + |
| CentOS | 7+ |  7.5 + |
| Debian | 8 + | 10 + |
| openSUSE | 13.2 + | 42.3 + |
| SUSE Linux Enterprise Server | 12+ | 12 SP3+ |

Als u een Linux-distributie gebruikt die niet wordt vermeld in de bovenstaande tabel, kunt u controleren of uw Linux-distributie SMB 3,0 ondersteunt met versleuteling door de versie van de Linux-kernel te controleren. SMB 3,0 met versleuteling is toegevoegd aan linux-kernel versie 4,11. De `uname` opdracht retourneert de versie van de Linux-kernel die in gebruik is:

```bash
uname -r
```

## <a name="prerequisites"></a>Vereisten
<a id="smb-client-reqs"></a>

* <a id="install-cifs-utils"></a>**Controleer of het CIFS-utils-pakket is geïnstalleerd.**  
    Het CIFS-hulppr-pakket kan worden geïnstalleerd met behulp van package manager in de Linux-distributie van uw keuze. 

    Gebruik voor Ubuntu **-en Debian-** distributies de `apt` Package Manager:

    ```bash
    sudo apt update
    sudo apt install cifs-utils
    ```

    Gebruik de `dnf` Package Manager op **Fedora**, **Red Hat Enterprise Linux 8 +** , en **CentOS 8 +** :

    ```bash
    sudo dnf install cifs-utils
    ```

    Gebruik de `yum` Package Manager op oudere versies van **Red Hat Enterprise Linux** en **CentOS**:

    ```bash
    sudo yum install cifs-utils 
    ```

    Gebruik op **openSUSE**het `zypper`-pakket beheer:

    ```bash
    sudo zypper install cifs-utils
    ```

    Gebruik op andere distributies de juiste pakket beheer-of [compilatie van de bron](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download)

* **De meest recente versie van de Azure-opdracht regel interface (CLI).** Zie [de Azure cli installeren](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) en uw besturings systeem selecteren voor meer informatie over het installeren van de Azure cli. Als u liever de module Azure PowerShell gebruikt in Power shell 6 +, is het mogelijk dat de onderstaande instructies worden weer gegeven voor de Azure CLI.

* **Zorg ervoor dat poort 445 is geopend**: SMB communiceert via TCP-poort 445-Controleer of de firewall TCP-poorten 445 van de client computer niet blokkeert.  Vervang **< uw-resource groep >** en **< uw-opslag account >**
    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    
    # This command assumes you have logged in with az login
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))
    fileHost=$(echo $smbPath | tr -d "/")

    nc -zvw3 $fileHost 445
    ```

    Als de verbinding is geslaagd, ziet u een melding die vergelijkbaar is met de volgende uitvoer:

    ```
    Connection to <your-storage-account> 445 port [tcp/microsoft-ds] succeeded!
    ```

    Als u poort 445 niet kunt openen in uw bedrijfs netwerk of als dit door een Internet provider wordt geblokkeerd, kunt u een VPN-verbinding of ExpressRoute gebruiken om poort 445 te omzeilen. Zie [netwerk overwegingen voor directe toegang tot Azure bestands share](storage-files-networking-overview.md)voor meer informatie.

## <a name="mounting-azure-file-share"></a>Azure-bestands share koppelen
Als u een Azure-bestands share met uw Linux-distributie wilt gebruiken, moet u een directory maken die fungeert als het koppel punt voor de Azure-bestands share. U kunt een koppel punt maken op een wille keurige locatie in uw Linux-systeem, maar het is gebruikelijk om dit te doen onder/mnt. Na het koppel punt gebruikt u de opdracht `mount` om toegang te krijgen tot de Azure-bestands share.

U kunt dezelfde Azure-bestands share indien gewenst koppelen aan meerdere koppel punten.

### <a name="mount-the-azure-file-share-on-demand-with-mount"></a>De Azure-bestands share on-demand koppelen met `mount`
1. **Maak een map voor het koppel punt**: Vervang `<your-resource-group>`, `<your-storage-account>`en `<your-file-share>` door de juiste informatie voor uw omgeving:

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mnt/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **Gebruik de koppelings opdracht om de Azure-bestands share te koppelen**. In het onderstaande voor beeld zijn de machtigingen voor lokale Linux-bestanden en-mappen standaard 0755. Dit betekent dat lezen, schrijven en uitvoeren voor de eigenaar (op basis van de Linux-eigenaar van het bestand of de map), lezen en uitvoeren voor gebruikers in de groep eigenaar, en lezen en uitvoeren voor anderen op het systeem. U kunt de opties `uid` en `gid` koppelen gebruiken om de gebruikers-ID en groeps-ID voor de koppeling in te stellen. U kunt ook `dir_mode` en `file_mode` gebruiken om aangepaste machtigingen naar wens in te stellen. Zie voor meer informatie over het instellen van machtigingen voor de [UNIX-numerieke notatie](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) op Wikipedia. 

    ```bash
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

    storageAccountKey=$(az storage account keys list \
        --resource-group $resourceGroupName \
        --account-name $storageAccountName \
        --query "[0].value" | tr -d '"')

    sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
    ```

    > [!Note]  
    > De bovenstaande koppelings opdracht koppelt aan SMB 3,0. Als uw Linux-distributie geen SMB 3,0 ondersteunt met versleuteling of als deze alleen ondersteuning biedt voor SMB 2,1, kunt u alleen koppelen van een Azure-VM binnen dezelfde regio als het opslag account. Als u uw Azure-bestands share wilt koppelen aan een Linux-distributie die geen ondersteuning biedt voor SMB 3,0 met versleuteling, moet u [versleuteling voor het opslag account uitschakelen](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

Wanneer u klaar bent met het gebruik van de Azure-bestands share, kunt u `sudo umount $mntPath` gebruiken om de share te ontkoppelen.

### <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Een permanent koppel punt maken voor de Azure-bestands share met `/etc/fstab`
1. **Maak een map voor het koppel punt**: er kan een map voor een koppel punt worden gemaakt op een wille keurige locatie in het bestands systeem, maar dit is een algemene Conventie voor het maken van deze onder/mnt. Met de volgende opdracht maakt u bijvoorbeeld een nieuwe map, vervangt u `<your-resource-group>`, `<your-storage-account>`en `<your-file-share>` met de juiste informatie voor uw omgeving:

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mnt/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **Maak een referentie bestand voor het opslaan van de gebruikers naam (de naam van het opslag account) en het wacht woord (de sleutel van het opslag account) voor de bestands share.** 

    ```bash
    if [ ! -d "/etc/smbcredentials" ]; then
        sudo mkdir "/etc/smbcredentials"
    fi

    storageAccountKey=$(az storage account keys list \
        --resource-group $resourceGroupName \
        --account-name $storageAccountName \
        --query "[0].value" | tr -d '"')
    
    smbCredentialFile="/etc/smbcredentials/$storageAccountName.cred"
    if [ ! -f $smbCredentialFile ]; then
        echo "username=$storageAccountName" | sudo tee $smbCredentialFile > /dev/null
        echo "password=$storageAccountKey" | sudo tee -a $smbCredentialFile > /dev/null
    else 
        echo "The credential file $smbCredentialFile already exists, and was not modified."
    fi
    ```

1. **Wijzig de machtigingen voor het referentie bestand zodat alleen het toegangs punt het wachtwoord bestand kan lezen of wijzigen.** Omdat de sleutel van het opslag account in feite een super beheerders wachtwoord voor het opslag account is, is het instellen van de machtigingen voor het bestand, zodat alleen root toegang belang rijk is, zodat gebruikers met een lagere bevoegdheid de sleutel van het opslag account niet kunnen ophalen.   

    ```bash
    sudo chmod 600 $smbCredentialFile
    ```

1. **Gebruik de volgende opdracht om de volgende regel toe te voegen aan `/etc/fstab`** : in het volgende voor beeld is het lokale Linux-bestand en de mapmachtigingen standaard 0755, wat betekent dat lezen, schrijven en uitvoeren voor de eigenaar (op basis van de Linux-eigenaar van het bestand of de map), lezen en uitvoeren voor gebruikers in de groep eigenaar, en lezen en uitvoeren voor anderen op het systeem. U kunt de opties `uid` en `gid` koppelen gebruiken om de gebruikers-ID en groeps-ID voor de koppeling in te stellen. U kunt ook `dir_mode` en `file_mode` gebruiken om aangepaste machtigingen naar wens in te stellen. Zie voor meer informatie over het instellen van machtigingen voor de [UNIX-numerieke notatie](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) op Wikipedia.

    ```bash
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

    if [ -z "$(grep $smbPath\ $mntPath /etc/fstab)" ]; then
        echo "$smbPath $mntPath cifs nofail,vers=3.0,credentials=$smbCredentialFile,serverino" | sudo tee -a /etc/fstab > /dev/null
    else
        echo "/etc/fstab was not modified to avoid conflicting entries as this Azure file share was already present. You may want to double check /etc/fstab to ensure the configuration is as desired."
    fi

    sudo mount -a
    ```
    
    > [!Note]  
    > De bovenstaande koppelings opdracht koppelt aan SMB 3,0. Als uw Linux-distributie geen SMB 3,0 ondersteunt met versleuteling of als deze alleen ondersteuning biedt voor SMB 2,1, kunt u alleen koppelen van een Azure-VM binnen dezelfde regio als het opslag account. Als u uw Azure-bestands share wilt koppelen aan een Linux-distributie die geen ondersteuning biedt voor SMB 3,0 met versleuteling, moet u [versleuteling voor het opslag account uitschakelen](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="securing-linux"></a>Linux beveiligen
Voor het koppelen van een Azure-bestands share op Linux moet poort 445 toegankelijk zijn. Veel organisaties blokkeren poort 445 vanwege de beveiligingsrisico's die bij SMB 1 horen. SMB 1, ook wel CIFS (Common Internet File System) genoemd, is een verouderd bestandssysteem protocol dat is opgenomen in veel Linux-distributies. SMB 1 is een verouderd, inefficiënt en bovenal onveilig protocol. Het goede nieuws is dat Azure Files geen ondersteuning biedt voor SMB 1 en vanaf Linux kernel versie 4,18, Linux het mogelijk maakt om SMB 1 uit te scha kelen. We raden u altijd [ten zeerste](https://aka.ms/stopusingsmb1) aan de SMB 1 uit te scha kelen op uw Linux-clients voordat u SMB-bestands shares in productie gebruikt.

Vanaf Linux kernel 4,18, de SMB-kernel-module, met de naam `cifs` voor verouderde redenen, wordt een nieuwe module parameter (ook wel *parameter* door verschillende externe documentatie genoemd) met de naam `disable_legacy_dialects`genoemd. Hoewel het is geïntroduceerd in Linux kernel 4,18, hebben sommige leveranciers deze wijziging Backported aan oudere kernels die ze ondersteunen. Voor het gemak wordt in de volgende tabel de beschik baarheid van deze module parameter op algemene Linux-distributies beschreven.

| Distributie | Kan SMB 1 uitschakelen |
|--------------|-------------------|
| Ubuntu 14.04-16.04 | Nee |
| Ubuntu 18.04 | Ja |
| Ubuntu 19.04 + | Ja |
| Debian 8-9 | Nee |
| Debian 10 + | Ja |
| Fedora 29 + | Ja |
| CentOS 7 | Nee | 
| CentOS 8 + | Ja |
| Red Hat Enterprise Linux 6. x-7. x | Nee |
| Red Hat Enterprise Linux 8 + | Ja |
| openSUSE Schrikkel 15,0 | Nee |
| openSUSE Schrikkel 15.1 + | Ja |
| openSUSE Tumbleweed | Ja |
| SUSE Linux Enter prise 11. x-12. x | Nee |
| SUSE Linux Enter prise 15 | Nee |
| SUSE Linux Enter prise 15,1 | Nee |

U kunt controleren of uw Linux-distributie de para meter `disable_legacy_dialects` module ondersteunt met behulp van de volgende opdracht.

```bash
sudo modinfo -p cifs | grep disable_legacy_dialects
```

Met deze opdracht voert u het volgende bericht uit:

```Output
disable_legacy_dialects: To improve security it may be helpful to restrict the ability to override the default dialects (SMB2.1, SMB3 and SMB3.02) on mount with old dialects (CIFS/SMB1 and SMB2) since vers=1.0 (CIFS/SMB1) and vers=2.0 are weaker and less secure. Default: n/N/0 (bool)
```

Voordat u SMB 1 uitschakelt, moet u controleren of de SMB-module momenteel niet is geladen op uw systeem (dit gebeurt automatisch als u een SMB-share hebt gekoppeld). U kunt dit doen met de volgende opdracht, waardoor niets kan worden uitgevoerd als SMB niet is geladen:

```bash
lsmod | grep cifs
```

Als u de module wilt verwijderen, ontkoppelt u eerst alle SMB-shares (met behulp van de `umount` opdracht zoals hierboven beschreven). U kunt alle gekoppelde SMB-shares op uw systeem identificeren met de volgende opdracht:

```bash
mount | grep cifs
```

Zodra u alle SMB-bestands shares hebt ontkoppeld, is het veilig om de module te verwijderen. U kunt dit doen met de opdracht `modprobe`:

```bash
sudo modprobe -r cifs
```

U kunt de module hand matig laden met SMB 1 uit het geheugen met behulp van de `modprobe` opdracht:

```bash
sudo modprobe cifs disable_legacy_dialects=Y
```

Ten slotte kunt u controleren of de SMB-module is geladen met de para meter door de geladen para meters in `/sys/module/cifs/parameters`te bekijken:

```bash
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

Als u SMB 1 op Ubuntu-en Debian-gebaseerde distributies permanent wilt uitschakelen, moet u een nieuw bestand maken (als u nog geen aangepaste opties voor andere modules hebt) met de instelling `/etc/modprobe.d/local.conf`. U kunt dit doen met de volgende opdracht:

```bash
echo "options cifs disable_legacy_dialects=Y" | sudo tee -a /etc/modprobe.d/local.conf > /dev/null
```

U kunt controleren of dit werkt door de SMB-module te laden:

```bash
sudo modprobe cifs
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

## <a name="feedback"></a>Feedback
Linux-gebruikers willen we graag van u horen.

De groep Azure Files voor Linux-gebruikers biedt een forum waarmee u feedback kunt delen tijdens het evalueren en aannemen van bestands opslag in Linux. E-mail [Azure files Linux-gebruikers](mailto:azurefiles@microsoft.com) om lid te worden van de groep gebruikers.

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende koppelingen voor meer informatie over Azure Files:

* [Implementatie van Azure Files plannen](storage-files-planning.md)
* [Veelgestelde vragen](../storage-files-faq.md)
* [Problemen oplossen](storage-troubleshoot-linux-file-connection-problems.md)
