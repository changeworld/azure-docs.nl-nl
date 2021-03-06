---
title: Azure File Sync implementeren | Microsoft Docs
description: Meer informatie over het implementeren van Azure File Sync, van begin tot eind.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f2c4e762ebf10a5ca2120c13a52750a7781d60b9
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79268065"
---
# <a name="deploy-azure-file-sync"></a>Azure Files SYNC implementeren
Gebruik Azure File Sync om de bestands shares van uw organisatie in Azure Files te centraliseren, terwijl u de flexibiliteit, prestaties en compatibiliteit van een on-premises Bestands server bijhoudt. Door Azure File Sync wordt Windows Server getransformeerd in een snelle cache van uw Azure-bestandsshare. U kunt elk protocol dat beschikbaar is op Windows Server gebruiken voor toegang tot uw gegevens lokaal, zoals SMB, NFS en FTPS. U kunt zoveel caches hebben als u nodig hebt in de hele wereld.

We raden u ten zeerste [aan de planning voor een Azure files-implementatie](storage-files-planning.md) te lezen en te [plannen voor een Azure file sync-implementatie](storage-sync-files-planning.md) voordat u de stappen in dit artikel uitvoert.

## <a name="prerequisites"></a>Vereisten
* Een Azure-bestands share in dezelfde regio die u wilt implementeren Azure File Sync. Zie voor meer informatie:
    - [Beschik baarheid](storage-sync-files-planning.md#azure-file-sync-region-availability) van de regio voor Azure file sync.
    - [Maak een bestands share](storage-how-to-create-file-share.md) voor een stapsgewijze beschrijving van het maken van een bestands share.
* Ten minste één ondersteund exemplaar van Windows Server of Windows Server-cluster om te synchroniseren met Azure File Sync. Zie [interoperabiliteit met Windows Server](storage-sync-files-planning.md#windows-file-server-considerations)voor meer informatie over ondersteunde versies van Windows Server.
* U kunt de AZ Power shell-module gebruiken met Power shell 5,1 of Power shell 6 +. U kunt de AZ Power shell-module voor Azure File Sync gebruiken op elk ondersteund systeem, met inbegrip van niet-Windows-systemen, maar de cmdlet Server registratie moet altijd worden uitgevoerd op het Windows Server-exemplaar dat u registreert (dit kan rechtstreeks of via Power shell worden uitgevoerd). externe toegang). In Windows Server 2012 R2 kunt u controleren of u ten minste Power shell 5,1 uitvoert.\* door te kijken naar de waarde van de eigenschap **PSVersion** van het object **$PSVersionTable** :

    ```powershell
    $PSVersionTable.PSVersion
    ```

    Als uw PSVersion-waarde lager is dan 5,1.\*, zoals het geval is bij de meeste nieuwe installaties van Windows Server 2012 R2, kunt u eenvoudig bijwerken door [Windows Management Framework (WMF) 5,1](https://www.microsoft.com/download/details.aspx?id=54616)te downloaden en te installeren. Het juiste pakket om te downloaden en te installeren voor Windows Server 2012 R2 is **win 8.1 andw2k12r2-KB\*\*\*\*\*\*\*-x64. MSU**. 

    Power shell 6 + kan worden gebruikt met elk ondersteund systeem en kan worden gedownload via de [github-pagina](https://github.com/PowerShell/PowerShell#get-powershell). 

    > [!Important]  
    > Als u de gebruikers interface voor Server registratie wilt gebruiken in plaats van rechtstreeks vanuit Power shell te registreren, moet u Power shell 5,1 gebruiken.

* Als u hebt aangegeven dat u Power shell 5,1 wilt gebruiken, moet u ervoor zorgen dat er ten minste .NET 4.7.2 is geïnstalleerd. Meer informatie over [.NET Framework versies en afhankelijkheden](https://docs.microsoft.com/dotnet/framework/migration-guide/versions-and-dependencies) op uw systeem.

    > [!Important]  
    > Als u .NET 4.7.2 + installeert op Windows Server Core, moet u installeren met de `quiet`-en `norestart`-vlaggen of kan de installatie mislukken. Als u bijvoorbeeld .NET 4,8 installeert, ziet de opdracht er als volgt uit:
    > ```PowerShell
    > Start-Process -FilePath "ndp48-x86-x64-allos-enu.exe" -ArgumentList "/q /norestart" -Wait
    > ```

* De AZ Power shell-module, die kan worden geïnstalleerd door de volgende instructies te volgen: [Installeer en configureer Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).
     
    > [!Note]  
    > De module AZ. StorageSync wordt nu automatisch geïnstalleerd tijdens de installatie van de AZ Power shell-module.

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>Windows Server voorbereiden voor gebruik met Azure File Sync
Schakel de **Verbeterde beveiliging van Internet Explorer**uit voor elke server die u wilt gebruiken met Azure file sync, met inbegrip van elk server knooppunt in een failovercluster. Dit is alleen vereist voor de eerste registratie van de server. U kunt de optie opnieuw inschakelen nadat de server is geregistreerd.

# <a name="portal"></a>[Portal](#tab/azure-portal)
> [!Note]  
> U kunt deze stap overs Laan als u Azure File Sync implementeert op Windows Server Core.

1. Open Serverbeheer.
2. Klik op **lokale server**:  
    ![' lokale server ' aan de linkerkant van de Serverbeheer gebruikers interface](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
3. Selecteer in het deelvenster **Eigenschappen** de koppeling naar **Verbeterde beveiliging van Internet Explorer**.  
    ![het deel venster verbeterde beveiliging van Internet Explorer in de Serverbeheer gebruikers interface](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
4. Selecteer in het dialoog venster **Verbeterde beveiliging van Internet Explorer** de optie **uitschakelen** voor **beheerders** en **gebruikers**:  
    ![het pop-venster verbeterde beveiliging van Internet Explorer met ' uit ' geselecteerd](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Als u de verbeterde beveiliging van Internet Explorer wilt uitschakelen, voert u het volgende uit vanuit een Power shell-sessie met verhoogde bevoegdheid:

```powershell
$installType = (Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\").InstallationType

# This step is not required for Server Core
if ($installType -ne "Server Core") {
    # Disable Internet Explorer Enhanced Security Configuration 
    # for Administrators
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force
    
    # Disable Internet Explorer Enhanced Security Configuration 
    # for Users
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A8-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force
    
    # Force Internet Explorer closed, if open. This is required to fully apply the setting.
    # Save any work you have open in the IE browser. This will not affect other browsers,
    # including Microsoft Edge.
    Stop-Process -Name iexplore -ErrorAction SilentlyContinue
}
``` 

---

## <a name="deploy-the-storage-sync-service"></a>Opslagsynchronisatieservice implementeren 
De implementatie van Azure File Sync begint met het plaatsen van een **opslag synchronisatie service** resource in een resource groep van het geselecteerde abonnement. We raden u aan om zo weinig als nodig in te richten. U maakt een vertrouwens relatie tussen uw servers en deze bron en een server kan alleen worden geregistreerd voor één opslag synchronisatie service. Als gevolg hiervan wordt het aanbevolen om zoveel opslag synchronisatie services te implementeren als u nodig hebt om groepen servers te scheiden. Houd er wel bij dat servers van verschillende opslag synchronisatie Services niet met elkaar kunnen worden gesynchroniseerd.

> [!Note]
> De opslag synchronisatie service neemt toegangs machtigingen over van het abonnement en de resource groep waarin het is geïmplementeerd. We raden u aan om zorgvuldig te controleren wie er toegang tot heeft. Entiteiten met schrijf toegang kunnen beginnen met het synchroniseren van nieuwe sets bestanden van servers die zijn geregistreerd bij deze opslag synchronisatie service, waardoor gegevens stromen naar Azure-opslag die toegankelijk is voor hen.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Als u een opslag synchronisatie service wilt implementeren, gaat u naar de [Azure Portal](https://portal.azure.com/), klikt u op *een resource maken* en zoekt u naar Azure file sync. Selecteer in de zoek resultaten **Azure file sync**en selecteer vervolgens **maken** om het tabblad **opslag synchronisatie implementeren** te openen.

Voer de volgende gegevens in in het deelvenster dat verschijnt:

- **Naam**: een unieke naam (per abonnement) voor de opslag synchronisatie service.
- **Abonnement**: het abonnement waarin u de opslag synchronisatie service wilt maken. Afhankelijk van de configuratie strategie van uw organisatie hebt u mogelijk toegang tot een of meer abonnementen. Een Azure-abonnement is de meest eenvoudige container voor facturering voor elke Cloud service (zoals Azure Files).
- **Resource groep**: een resource groep is een logische groep Azure-resources, zoals een opslag account of een opslag synchronisatie service. U kunt een nieuwe resource groep maken of een bestaande resource groep gebruiken voor Azure File Sync. (We raden u aan om resource groepen als containers te gebruiken om resources logisch te isoleren voor uw organisatie, zoals het groeperen van resources of resources voor een specifiek project.)
- **Locatie**: de regio waarin u Azure File Sync wilt implementeren. In deze lijst worden alleen ondersteunde regio's beschikbaar.

Wanneer u klaar bent, selecteert u **maken** om de opslag synchronisatie service te implementeren.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Vervang `<Az_Region>`, `<RG_Name>`en `<my_storage_sync_service>` met uw eigen waarden en gebruik vervolgens de volgende opdrachten om een opslag synchronisatie service te maken en te implementeren:

```powershell
$hostType = (Get-Host).Name

if ($installType -eq "Server Core" -or $hostType -eq "ServerRemoteHost") {
    Connect-AzAccount -UseDeviceAuthentication
}
else {
    Connect-AzAccount
}

# this variable holds the Azure region you want to deploy 
# Azure File Sync into
$region = '<Az_Region>'

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = @()
Get-AzLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the selected Azure Region or the region is mistyped.")
}

# the resource group to deploy the Storage Sync Service into
$resourceGroup = '<RG_Name>'

# Check to ensure resource group exists and create it if doesn't
$resourceGroups = @()
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    New-AzResourceGroup -Name $resourceGroup -Location $region
}

$storageSyncName = "<my_storage_sync_service>"
$storageSync = New-AzStorageSyncService -ResourceGroupName $resourceGroup -Name $storageSyncName -Location $region
```

---

## <a name="install-the-azure-file-sync-agent"></a>Azure File Sync-agent installeren
De Azure File Sync-agent is een downloadbaar pakket waardoor Windows Server met een Azure-bestandsshare kan worden gesynchroniseerd. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
U kunt de agent downloaden via het [micro soft Download centrum](https://go.microsoft.com/fwlink/?linkid=858257). Wanneer het downloaden is voltooid, dubbelklikt u op het MSI-pakket om de installatie van de Azure File Sync-agent te starten.

> [!Important]  
> Als u Azure File Sync wilt gebruiken met een failovercluster, moet de Azure File Sync-agent worden geïnstalleerd op elk knoop punt in het cluster. Elk knoop punt in het cluster moet worden geregistreerd om met Azure File Sync te kunnen werken.

U wordt aangeraden het volgende te doen:
- Verlaat het standaardpad voor installatie (C:\Program Files\Azure\StorageSyncAgent), om het oplossen van problemen en server onderhoud te vereenvoudigen.
- Schakel Microsoft Update in om Azure File Sync up-to-date te houden. Alle updates, naar de Azure File Sync-agent, inclusief functie-updates en hotfixes, treden op via Microsoft Update. U kunt het beste de meest recente update installeren op Azure File Sync. Zie [Azure File Sync Update beleid](storage-sync-files-planning.md#azure-file-sync-agent-update-policy)voor meer informatie.

Wanneer de installatie van de Azure File Sync agent is voltooid, wordt de gebruikers interface voor Server registratie automatisch geopend. U moet een opslag synchronisatie service hebben voordat u zich kunt registreren. Zie de volgende sectie voor het maken van een opslag synchronisatie service.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Voer de volgende Power shell-code uit om de juiste versie van de Azure File Sync-agent voor uw besturings systeem te downloaden en op uw systeem te installeren.

> [!Important]  
> Als u Azure File Sync wilt gebruiken met een failovercluster, moet de Azure File Sync-agent worden geïnstalleerd op elk knoop punt in het cluster. Elk knoop punt in het cluster moet worden geregistreerd om met Azure File Sync te werken.

```powershell
# Gather the OS version
$osver = [System.Environment]::OSVersion.Version

# Download the appropriate version of the Azure File Sync agent for your OS.
if ($osver.Equals([System.Version]::new(10, 0, 17763, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2019 `
        -OutFile "StorageSyncAgent.msi" 
} elseif ($osver.Equals([System.Version]::new(10, 0, 14393, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2016 `
        -OutFile "StorageSyncAgent.msi" 
} elseif ($osver.Equals([System.Version]::new(6, 3, 9600, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2012R2 `
        -OutFile "StorageSyncAgent.msi" 
} else {
    throw [System.PlatformNotSupportedException]::new("Azure File Sync is only supported on Windows Server 2012 R2, Windows Server 2016, and Windows Server 2019")
}

# Install the MSI. Start-Process is used to PowerShell blocks until the operation is complete.
# Note that the installer currently forces all PowerShell sessions closed - this is a known issue.
Start-Process -FilePath "StorageSyncAgent.msi" -ArgumentList "/quiet" -Wait

# Note that this cmdlet will need to be run in a new session based on the above comment.
# You may remove the temp folder containing the MSI and the EXE installer
Remove-Item -Path ".\StorageSyncAgent.msi" -Recurse -Force
```

---

## <a name="register-windows-server-with-storage-sync-service"></a>Windows Server registreren met opslagsynchronisatieservice
Als u Windows Server registreert voor een opslagsynchronisatieservice, wordt er een vertrouwensrelatie ingesteld tussen uw server (of cluster) en de opslagsynchronisatieservice. Een server kan slechts voor één opslagsynchronisatieservice worden geregistreerd en kan worden gesynchroniseerd met andere servers en Azure-bestandsshares die aan dezelfde opslagsynchronisatieservice zijn gekoppeld.

> [!Note]
> De registratie van de server maakt gebruik van uw Azure-referenties voor het maken van een vertrouwens relatie tussen de opslag synchronisatie service en uw Windows-Server, maar vervolgens maakt en gebruikt de server een eigen identiteit die geldig is zolang de server geregistreerd blijft en de het huidige Shared Access Signature-token (opslag-SAS) is geldig. Er kan geen nieuw SAS-token aan de server worden verleend zodra de registratie van de server ongedaan is gemaakt, waardoor de server de mogelijkheid heeft om toegang te krijgen tot uw Azure-bestands shares en de synchronisatie te stoppen.

# <a name="portal"></a>[Portal](#tab/azure-portal)
De gebruikers interface voor Server registratie moet automatisch worden geopend na de installatie van de Azure File Sync-agent. Als dat niet gebeurt, kunt u deze handmatig openen vanuit de bestandslocatie: C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe. Wanneer de gebruikers interface voor Server registratie wordt geopend, selecteert u **Aanmelden** om te beginnen.

Nadat u zich hebt aangemeld, wordt u gevraagd de volgende gegevens op te vragen:

![Schermafbeelding van de gebruikersinterface van de serverregistratie](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Azure-abonnement**: het abonnement dat de opslag synchronisatie service bevat (Zie [de opslag synchronisatie service implementeren](#deploy-the-storage-sync-service)). 
- **Resource groep**: de resource groep die de opslag synchronisatie service bevat.
- **Opslag synchronisatie service**: de naam van de opslag synchronisatie service waarmee u zich wilt registreren.

Nadat u de benodigde gegevens hebt geselecteerd, selecteert u **registreren** om de server registratie te volt ooien. Als deel van het registratieproces wordt u gevraagd u nogmaals aan te melden.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
$registeredServer = Register-AzStorageSyncServer -ParentObject $storageSync
```

---

## <a name="create-a-sync-group-and-a-cloud-endpoint"></a>Synchronisatiegroep en cloudeindpunt maken
Een synchronisatiegroep definieert de synchronisatietopologie voor een verzameling bestanden. Eindpunten binnen een synchronisatiegroep worden onderling synchroon gehouden. Een synchronisatiegroep moet één cloudeindpunt bevatten, dat een Azure-bestandsshare en een of meer servereindpunten representeert. Een server-eind punt vertegenwoordigt een pad op een geregistreerde server. Een server kan server eindpunten in meerdere synchronisatie groepen hebben. U kunt zoveel synchronisatie groepen maken als u nodig hebt om de gewenste synchronisatie topologie op de juiste wijze te beschrijven.

Een Cloud-eind punt is een verwijzing naar een Azure-bestands share. Alle server eindpunten worden gesynchroniseerd met een Cloud eindpunt, waardoor het Cloud eindpunt de hub wordt. Het opslag account voor de Azure-bestands share moet zich in dezelfde regio bevinden als de opslag synchronisatie service. Het volledige gedeelte van de Azure-bestands share wordt gesynchroniseerd, met één uitzonde ring: een speciale map, vergelijkbaar met de verborgen map systeem volume gegevens op een NTFS-volume, wordt ingericht. Deze map heet. SystemShareInformation". Het bevat belang rijke synchronisatie-meta gegevens die niet worden gesynchroniseerd met andere eind punten. Niet gebruiken of verwijderen.

> [!Important]  
> U kunt wijzigingen aanbrengen in een eind punt van een Cloud of server-eind punt in de synchronisatie groep en uw bestanden synchroniseren met de andere eind punten in de synchronisatie groep. Als u een wijziging aanbrengt in het Cloud eindpunt (Azure-bestands share), moeten de wijzigingen eerst worden gedetecteerd door een Azure File Sync wijzigings detectie taak. Een wijzigings detectie taak wordt slechts eenmaal per 24 uur geïnitieerd voor een Cloud eindpunt. Zie [Azure files Veelgestelde vragen](storage-files-faq.md#afs-change-detection)voor meer informatie.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Als u een synchronisatie groep wilt maken, gaat u in het [Azure Portal](https://portal.azure.com/)naar de opslag synchronisatie service en selecteert u **+ synchronisatie groep**:

![Een nieuwe synchronisatiegroep in de Azure-portal maken](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

Voer in het deelvenster dat verschijnt de volgende gegevens in om een synchronisatiegroep met een cloudeindpunt te maken:

- **Naam van de synchronisatie groep**: de naam van de synchronisatie groep die moet worden gemaakt. Deze naam moet uniek zijn binnen de opslagsynchronisatieservice, maar het mag een willekeurige naam zijn die u makkelijk kunt onthouden.
- **Abonnement**: het abonnement waarin u de opslag synchronisatie service hebt geïmplementeerd in [de opslag synchronisatie service implementeren](#deploy-the-storage-sync-service).
- **Opslag account**: als u **opslag account selecteren**selecteert, wordt er een ander deel venster weer gegeven waarin u het opslag account kunt selecteren dat de Azure-bestands share heeft waarmee u wilt synchroniseren.
- **Azure-bestands share**: de naam van de Azure-bestands share waarmee u wilt synchroniseren.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Als u de synchronisatie groep wilt maken, voert u de volgende Power shell uit. Vergeet niet om `<my-sync-group>` te vervangen door de gewenste naam van de synchronisatie groep.

```powershell
$syncGroupName = "<my-sync-group>"
$syncGroup = New-AzStorageSyncGroup -ParentObject $storageSync -Name $syncGroupName
```

Zodra de synchronisatie groep is gemaakt, kunt u uw Cloud-eind punt maken. Zorg ervoor dat u `<my-storage-account>` vervangt en `<my-file-share>` door de verwachte waarden.

```powershell
# Get or create a storage account with desired name
$storageAccountName = "<my-storage-account>"
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup | Where-Object {
    $_.StorageAccountName -eq $storageAccountName
}

if ($storageAccount -eq $null) {
    $storageAccount = New-AzStorageAccount `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup `
        -Location $region `
        -SkuName Standard_LRS `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly:$true
}

# Get or create an Azure file share within the desired storage account
$fileShareName = "<my-file-share>"
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $fileShareName -and $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    $fileShare = New-AzStorageShare -Context $storageAccount.Context -Name $fileShareName
}

# Create the cloud endpoint
New-AzStorageSyncCloudEndpoint `
    -Name $fileShare.Name `
    -ParentObject $syncGroup `
    -StorageAccountResourceId $storageAccount.Id `
    -AzureFileShareName $fileShare.Name
```

---

## <a name="create-a-server-endpoint"></a>Servereindpunt maken
Een servereindpunt representeert een bepaalde locatie op een geregistreerde server, bijvoorbeeld een map op een servervolume. Een server eindpunt moet een pad op een geregistreerde server zijn (in plaats van een gekoppelde share). Als u Cloud lagen wilt gebruiken, moet het pad zich op een niet-systeem volume bevinden. Network Attached Storage (NAS) wordt niet ondersteund.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Als u een server eindpunt wilt toevoegen, gaat u naar de zojuist gemaakte synchronisatie groep en selecteert u vervolgens **Server eindpunt toevoegen**.

![Nieuw servereindpunt toevoegen in het deelvenster met de synchronisatiegroep](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

Voer in het deelvenster **Servereindpunt toevoegen** de volgende gegevens in om een servereindpunt te maken:

- **Geregistreerde server**: de naam van de server of het cluster waar u het server eindpunt wilt maken.
- **Pad**: het pad naar de Windows-Server dat moet worden gesynchroniseerd als onderdeel van de synchronisatie groep.
- **Cloud lagen**: een schakel optie om Cloud lagen in of uit te scha kelen. Met Cloud lagen kunnen zelden gebruikte of geopende bestanden worden getierd naar Azure Files.
- **Beschik bare volume ruimte**: de hoeveelheid beschik bare ruimte die moet worden gereserveerd op het volume waarop het server eindpunt zich bevindt. Als volume beschik bare ruimte bijvoorbeeld is ingesteld op 50% op een volume met één server eindpunt, wordt er ongeveer de helft van de hoeveelheid gegevens gelaagd tot Azure Files. Ongeacht of Cloud lagen zijn ingeschakeld, heeft uw Azure-bestands share altijd een volledige kopie van de gegevens in de synchronisatie groep.

Selecteer **maken**om het server eindpunt toe te voegen. Uw bestanden worden nu gesynchroniseerd op de Azure-bestands share en Windows-Server. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Voer de volgende Power shell-opdrachten uit om het server eindpunt te maken en zorg ervoor dat u `<your-server-endpoint-path>` vervangt en `<your-volume-free-space>` met de gewenste waarden.

```powershell
$serverEndpointPath = "<your-server-endpoint-path>"
$cloudTieringDesired = $true
$volumeFreeSpacePercentage = <your-volume-free-space>

if ($cloudTieringDesired) {
    # Ensure endpoint path is not the system volume
    $directoryRoot = [System.IO.Directory]::GetDirectoryRoot($serverEndpointPath)
    $osVolume = "$($env:SystemDrive)\"
    if ($directoryRoot -eq $osVolume) {
        throw [System.Exception]::new("Cloud tiering cannot be enabled on the system volume")
    }

    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath `
        -CloudTiering `
        -VolumeFreeSpacePercent $volumeFreeSpacePercentage
} else {
    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath 
}
```

---

## <a name="configure-firewall-and-virtual-network-settings"></a>Instellingen voor de firewall en het virtuele netwerk configureren

### <a name="portal"></a>Portal
Als u uw Azure-bestands synchronisatie wilt configureren voor gebruik met de instellingen voor de firewall en het virtuele netwerk, doet u het volgende:

1. Ga vanuit het Azure Portal naar het opslag account dat u wilt beveiligen.
1. Selecteer de knop **firewalls en virtuele netwerken** in het menu links.
1. Selecteer **geselecteerde netwerken** onder **toegang toestaan vanuit**.
1. Zorg ervoor dat het IP-adres of het virtuele netwerk van uw servers wordt vermeld in de betreffende sectie.
1. Zorg ervoor dat **vertrouwde micro soft-Services toegang tot dit opslag account toestaan** is ingeschakeld.
1. Selecteer **Opslaan** om uw instellingen op te slaan.

![Instellingen voor de firewall en het virtuele netwerk configureren om te werken met Azure-bestands synchronisatie](media/storage-sync-files-deployment-guide/firewall-and-vnet.png)

## <a name="onboarding-with-azure-file-sync"></a>Onboarding met Azure File Sync
De aanbevolen stappen voor het onboarden van Azure File Sync voor het eerst met een downtime van nul en het behoud van de volledige bestands kwaliteit en toegangs beheer lijst (ACL) zijn als volgt:
 
1. Implementeer een opslag synchronisatie service.
2. Maak een synchronisatiegroep.
3. Azure File Sync-agent op de server installeren met de volledige gegevensset.
4. Registreer de server en maak een server eindpunt op de share. 
5. Synchroniseer de volledige upload naar de Azure-bestands share (Cloud-eind punt).  
6. Nadat de eerste upload is voltooid, installeert u Azure File Sync agent op elk van de resterende servers.
7. Nieuwe bestands shares maken op elk van de resterende servers.
8. Server eindpunten maken op nieuwe bestands shares met het Cloud-laag beleid, indien gewenst. (Voor deze stap moet extra opslag ruimte beschikbaar zijn voor de eerste installatie.)
9. Laat Azure File Sync agent een snelle herstel bewerking van de volledige naam ruimte uitvoeren zonder de daad werkelijke gegevens overdracht. Nadat de volledige naam ruimte is gesynchroniseerd, wordt de lokale schijf ruimte door de synchronisatie-engine gevuld op basis van het beleid voor Cloud lagen voor het server eindpunt. 
10. Controleer of de synchronisatie is voltooid en test uw topologie naar wens. 
11. Gebruikers en toepassingen omleiden naar deze nieuwe share.
12. U kunt eventueel dubbele shares op de servers verwijderen.
 
Als u geen extra opslag ruimte hebt voor het eerste onboarding en u wilt koppelen aan de bestaande shares, kunt u de gegevens in de Azure-bestands shares vooraf seeden. Deze methode wordt alleen voorgesteld als en alleen als u uitval tijd kunt accepteren en absoluut geen gegevens wijzigingen op de server shares tijdens het oorspronkelijke voorbereidings proces gegarandeerd. 
 
1. Zorg ervoor dat de gegevens op een van de servers niet kunnen worden gewijzigd tijdens het voorbereidings proces.
2. Azure-bestands shares vooraf seeden met de server gegevens via elk hulp programma voor gegevens overdracht via het SMB, bijvoorbeeld Robocopy, direct SMB-kopie. Omdat AzCopy geen gegevens via de SMB uploadt, kan deze niet worden gebruikt voor pre-seeding.
3. Maak Azure File Sync topologie met de gewenste server eindpunten die verwijzen naar de bestaande shares.
4. Afstemmings proces voor synchronisatie volt ooien op alle eind punten toestaan. 
5. Zodra de afstemming is voltooid, kunt u shares openen voor wijzigingen.
 
Op dit moment heeft de pre-seeding-benadering een aantal beperkingen: 
- Volledige betrouw baarheid van bestanden blijft niet behouden. Zo kunnen bestanden Acl's en tijds tempels verloren gaan.
- Gegevens wijzigingen op de server voordat de synchronisatie topologie volledig actief is en wordt uitgevoerd, kunnen conflicten veroorzaken op de server eindpunten.  
- Nadat het Cloud-eind punt is gemaakt, wordt door Azure File Sync een proces uitgevoerd om de bestanden in de cloud te detecteren voordat de initiële synchronisatie wordt gestart. De tijd die nodig is om dit proces te volt ooien, is afhankelijk van de verschillende factoren, zoals de netwerk snelheid, de beschik bare band breedte en het aantal bestanden en mappen. Voor de ruwe schatting van de preview-versie voert het detectie proces ongeveer 10 bestanden per seconde uit.  Zelfs als vooraf seeding snel wordt uitgevoerd, kan de totale tijd voor het verkrijgen van een volledig uitgevoerd systeem aanzienlijk langer zijn wanneer gegevens in de Cloud vooraf worden geseed.

## <a name="self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service"></a>Self-service herstellen via eerdere versies en VSS (Volume Shadow Copy Service)

> [!IMPORTANT]
> De volgende informatie kan alleen worden gebruikt in combi natie met versie 9 (of hoger) van de opslag synchronisatie agent. Versies die lager zijn dan 9, hebben geen StorageSyncSelfService-cmdlets.

Eerdere versies is een Windows-functie waarmee u VSS-moment opnamen aan de server zijde van een volume kunt gebruiken voor het weer geven van herstelbare versies van een bestand naar een SMB-client.
Dit maakt een krachtig scenario mogelijk, ook wel selfservice herstel genoemd, rechtstreeks voor informatie medewerkers, in plaats van afhankelijk van het herstel van een IT-beheerder.

VSS-moment opnamen en eerdere versies werken onafhankelijk van Azure File Sync. Cloud lagen moeten echter worden ingesteld op een compatibele modus. Er kunnen veel Azure File Sync server-eind punten bestaan op hetzelfde volume. U moet de volgende Power shell-oproep per volume maken met zelfs één server eindpunt waar u Cloud lagen wilt plannen of gebruiken.

```powershell
Import-Module ‘<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll’
Enable-StorageSyncSelfServiceRestore [-DriveLetter] <string> [[-Force]] 
```

VSS-moment opnamen worden gemaakt van een volledig volume. Standaard kunnen Maxi maal 64 moment opnamen bestaan voor een bepaald volume, omdat er voldoende ruimte is om de moment opnamen op te slaan. VSS verwerkt dit automatisch. Het standaard schema voor moment opnamen neemt twee moment opnamen per dag, maandag tot en met vrijdag. Dat schema kan worden geconfigureerd via een geplande Windows-taak. De bovenstaande Power shell-cmdlet heeft twee dingen:
1. Azure-bestands synchronisaties worden geconfigureerd op het opgegeven volume zodat het compatibel is met eerdere versies en garandeert dat een bestand kan worden hersteld uit een eerdere versie, zelfs als het is gelaagd naar de Cloud op de server. 
2. Hiermee wordt het standaard VSS-schema ingeschakeld. Daarna kunt u besluiten om deze later te wijzigen. 

> [!Note]  
> Er zijn twee belang rijke dingen die u moet weten:
>- Als u de para meter-Force gebruikt en VSS momenteel is ingeschakeld, wordt het huidige VSS-momentopname schema overschreven en vervangen door de standaard planning. Zorg ervoor dat u uw aangepaste configuratie opslaat voordat u de cmdlet uitvoert.
> - Als u deze cmdlet op een cluster knooppunt gebruikt, moet u deze ook uitvoeren op alle andere knoop punten in het cluster. 

U kunt de volgende cmdlet uitvoeren om te controleren of de compatibiliteit van selfservice herstel is ingeschakeld.

```powershell
    Get-StorageSyncSelfServiceRestore [[-Driveletter] <string>]
```

Alle volumes op de server worden weer geven, evenals het aantal voor de Cloud lagen compatibele dagen voor elk volume. Dit aantal wordt automatisch berekend op basis van de Maxi maal mogelijke moment opnamen per volume en het standaard schema voor moment opnamen. Standaard kunnen alle vorige versies die aan een informatie medewerker worden gepresenteerd, worden gebruikt voor het herstellen van. Hetzelfde geldt als u het standaard schema wijzigt om meer moment opnamen te maken.
Als u de planning echter zodanig wijzigt dat er een beschik bare moment opname is op het volume dat ouder is dan de waarde voor de compatibele dagen, dan kunnen gebruikers deze oudere moment opname (vorige versie) niet gebruiken om te herstellen van.

> [!Note]
> Het inschakelen van selfservice herstel kan gevolgen hebben voor het verbruik en de factuur van uw Azure-opslag. Deze impact is beperkt tot bestanden die momenteel op de server zijn gelaagd. Als u deze functie inschakelt, zorgt u ervoor dat er een bestands versie beschikbaar is in de Cloud waarnaar kan worden verwezen via een eerdere versie (VSS-moment opname).
>
> Als u de functie uitschakelt, wordt het gebruik van de Azure-opslag langzaam afwijzen totdat het venster compatibele dagen is verstreken. Er is geen manier om dit te versnellen. 

Het standaard maximum aantal VSS-moment opnamen per volume (64) en het standaard schema dat u wilt nemen, resulteert in een maximum van 45 dagen van vorige versies waarvan een informatie medewerker kan herstellen, afhankelijk van het aantal VSS-moment opnamen dat u op uw volume kunt opslaan.

Als Max. 64 VSS-moment opnamen per volume niet de juiste instelling voor u zijn. u kunt [deze waarde wijzigen via een register sleutel](https://docs.microsoft.com/windows/win32/backup/registry-keys-for-backup-and-restore#maxshadowcopies).
Om de nieuwe limiet van kracht te laten worden, moet u de cmdlet opnieuw uitvoeren om de compatibiliteit van eerdere versies in te scha kelen op elk volume waarop deze eerder was ingeschakeld, met de vlag-Force om het nieuwe maximum aantal VSS-moment opnamen per volume in rekening te brengen. Dit leidt tot een nieuw berekend aantal compatibele dagen. Houd er rekening mee dat deze wijziging alleen van kracht wordt op nieuwe gelaagde bestanden en eventuele aanpassingen van de VSS-planning die u hebt aangebracht, worden overschreven.

## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>Een DSF-replicatie-implementatie (DFS-R) migreren naar Azure File Sync
Een DFS-R-implementatie migreren naar Azure File Sync:

1. Maak een synchronisatie groep die de DFS-R-topologie vertegenwoordigt die u wilt vervangen.
2. Start op de-server met de volledige set gegevens in de DFS-R-topologie die u wilt migreren. Installeer Azure File Sync op die server.
3. Registreer die server en maak een server eindpunt voor de eerste server die moet worden gemigreerd. Schakel Cloud lagen niet in.
4. Zorg ervoor dat alle gegevens worden gesynchroniseerd met uw Azure-bestands share (Cloud-eind punt).
5. Installeer en registreer de Azure File Sync agent op elk van de resterende DFS-R-servers.
6. Schakel DFS-R uit. 
7. Maak een server eindpunt op elke DFS-R-server. Schakel Cloud lagen niet in.
8. Controleer of de synchronisatie is voltooid en test uw topologie naar wens.
9. DFS-R buiten gebruik stellen.
10. Cloud lagen kunnen nu desgewenst op elk server eindpunt worden ingeschakeld.

Zie [Azure file sync interop met Distributed File System (DFS)](storage-sync-files-planning.md#distributed-file-system-dfs)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
- [Een Azure File Sync server-eind punt toevoegen of verwijderen](storage-sync-files-server-endpoint.md)
- [Een server met Azure File Sync registreren of de registratie ervan opheffen](storage-sync-files-server-registration.md)
- [Azure File Sync bewaken](storage-sync-files-monitoring.md)
