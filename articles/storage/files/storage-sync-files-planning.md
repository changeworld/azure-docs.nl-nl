---
title: Een Azure File Sync-implementatie plannen | Microsoft Docs
description: Meer informatie over hoe u rekening moet houden bij het plannen van een Azure Files-implementatie.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 0684f626553946619a0db2cd895df39576bd17b9
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79255117"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>Planning voor de implementatie van Azure Files Sync
[Azure files](storage-files-introduction.md) kunnen op twee manieren worden geïmplementeerd: door de Serverloze Azure-bestands shares rechtstreeks te koppelen of door Azure-bestands shares on-premises in de cache op te maken met behulp van Azure file sync. Welke implementatie optie u kiest, wijzigt de dingen die u moet overwegen bij het plannen van uw implementatie. 

- **Directe koppeling van een Azure-bestands share**: omdat Azure files SMB-toegang biedt, kunt u Azure-bestands shares on-premises of in de Cloud koppelen met behulp van de standaard-SMB-client die beschikbaar is in Windows, MacOS en Linux. Omdat Azure-bestands shares serverloos zijn, is voor het implementeren van productie scenario's geen bestands server of een NAS-apparaat nodig. Dit betekent dat u geen software patches hoeft toe te passen of fysieke schijven hoeft uit te wisselen. 

- **Azure-bestands share op locatie opslaan in de cache met Azure file sync**: Azure File Sync kunt u de bestands shares van uw organisatie in azure files centraliseren en tegelijkertijd de flexibiliteit, prestaties en compatibiliteit van een on-premises Bestands server behouden. Azure File Sync transformeert een on-premises Windows-Server (of Cloud) naar een snelle cache van uw Azure-bestands share. 

Dit artikel bevat voornamelijk overwegingen bij de implementatie voor de implementatie van Azure File Sync. Als u een implementatie van Azure-bestands shares wilt plannen die rechtstreeks worden gekoppeld door een on-premises of cloud client, raadpleegt u [planning voor een Azure files-implementatie](storage-files-planning.md).

## <a name="management-concepts"></a>Beheer concepten
Een Azure File Sync-implementatie heeft drie fundamentele beheer objecten:

- **Azure-bestands**share: een Azure-bestands share is een serverloze Cloud bestands share die het *Cloud eindpunt* van een Azure file sync Sync-relatie bevat. Bestanden in een Azure-bestands share kunnen rechtstreeks worden geopend met SMB of het FileREST-protocol, maar we raden u aan om voornamelijk toegang te krijgen tot de bestanden via de Windows Server-cache wanneer de Azure-bestands share wordt gebruikt met Azure File Sync. Dit komt omdat Azure Files vandaag geen efficiënt mechanisme voor wijzigings detectie heeft, zoals Windows Server heeft, waardoor wijzigingen aan de Azure-bestands share rechtstreeks worden uitgevoerd om de server eindpunten te kunnen gebruiken.
- **Server eindpunt**: het pad op de Windows-Server dat wordt gesynchroniseerd met een Azure-bestands share. Dit kan een specifieke map op een volume zijn of de hoofdmap van het volume. Meerdere server eindpunten kunnen bestaan op hetzelfde volume als hun naam ruimten elkaar niet overlappen.
- **Synchronisatie groep**: het object dat de synchronisatie relatie tussen een **Cloud eindpunt**of een Azure-bestands share en een server eindpunt definieert. Eindpunten binnen een synchronisatiegroep worden onderling synchroon gehouden. Als u bijvoorbeeld twee verschillende sets bestanden hebt die u met Azure File Sync wilt beheren, maakt u twee synchronisatie groepen en voegt u verschillende eind punten toe aan elke synchronisatie groep.

### <a name="azure-file-share-management-concepts"></a>Concepten van Azure file share Management
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

### <a name="azure-file-sync-management-concepts"></a>Azure File Sync-beheer concepten
Synchronisatie groepen worden geïmplementeerd in **opslag synchronisatie Services**, die objecten van het hoogste niveau zijn die servers registreren voor gebruik met Azure file sync en de relaties van de synchronisatie groep bevatten. De resource van de opslag synchronisatie service is een peer van de bron van het opslag account en kan ook worden geïmplementeerd voor Azure-resource groepen. Een opslag synchronisatie service kan synchronisatie groepen maken die Azure-bestands shares bevatten in meerdere opslag accounts en meerdere geregistreerde Windows-servers.

Voordat u een synchronisatie groep in een opslag synchronisatie service kunt maken, moet u eerst een Windows-Server bij de opslag synchronisatie service registreren. Hiermee maakt u een **geregistreerd server** object dat een vertrouwens relatie tussen uw server of cluster en de opslag synchronisatie service vertegenwoordigt. Als u een opslag synchronisatie service wilt registreren, moet u eerst de Azure File Sync-agent op de server installeren. Een afzonderlijke server of cluster kan slechts met één opslag synchronisatie service tegelijk worden geregistreerd.

Een synchronisatie groep bevat één Cloud eindpunt, een Azure-bestands share en ten minste één server eindpunt. Het eindpunt object van de server bevat de instellingen die de functionaliteit voor **Cloud lagen** configureren, waarmee de cache functie van Azure File Sync wordt geboden. Om te synchroniseren met een Azure-bestands share moet het opslag account met de Azure-bestands share zich in dezelfde Azure-regio bevinden als de opslag synchronisatie service.

### <a name="management-guidance"></a>Beheer richtlijnen
Wanneer u Azure File Sync implementeert, kunt u het volgende doen:

- Deploying Azure file shares 1:1 met Windows-bestands shares. Met het object Server eindpunt hebt u een grote mate van flexibiliteit bij het instellen van de synchronisatie topologie aan de server zijde van de synchronisatie relatie. Zorg ervoor dat het pad van het server eindpunt overeenkomt met het pad van de Windows-bestands share om het beheer te vereenvoudigen. 

- Gebruik zoveel mogelijk opslag synchronisatie Services. Dit vereenvoudigt het beheer wanneer u synchronisatie groepen met meerdere server eindpunten hebt, omdat een Windows-Server per keer slechts voor één opslag synchronisatie service kan worden geregistreerd. 

- Let op de limieten voor de IOPS van een opslag account bij het implementeren van Azure-bestands shares. In het ideale geval wijst u bestands shares 1:1 met opslag accounts toe, maar dit is mogelijk niet altijd mogelijk als gevolg van verschillende limieten en beperkingen, zowel vanuit uw organisatie als vanuit Azure. Wanneer het niet mogelijk is om slechts één bestands share in één opslag account te implementeren, moet u overwegen welke shares Maxi maal actief zullen zijn en welke shares minder actief zijn om ervoor te zorgen dat de meest uitgebreide bestands shares niet samen in hetzelfde opslag account worden geplaatst.

## <a name="windows-file-server-considerations"></a>Aandachtspunten voor Windows-Bestands server
Als u de synchronisatie mogelijkheid wilt inschakelen op Windows Server, moet u de Azure File Sync Download bare agent installeren. De Azure File Sync-agent biedt twee hoofd onderdelen: `FileSyncSvc.exe`, de achtergrond Windows-service die verantwoordelijk is voor het bewaken van wijzigingen op de server-eind punten en het initiëren van synchronisatie sessies en `StorageSync.sys`, een bestandssysteem filter waarmee Cloud lagen en snelle herstel na nood gevallen kunnen worden gestart.  

### <a name="operating-system-requirements"></a>Besturingssysteemvereisten
Azure File Sync wordt ondersteund met de volgende versies van Windows Server:

| Version | Ondersteunde Sku's | Ondersteunde implementatie opties |
|---------|----------------|------------------------------|
| Windows Server 2019 | Data Center, Standard en IoT | Volledig en kern geheugen |
| Windows Server 2016 | Data Center, Standard en Storage Server | Volledig en kern geheugen |
| Windows Server 2012 R2 | Data Center, Standard en Storage Server | Volledig en kern geheugen |

Toekomstige versies van Windows Server worden toegevoegd zodra ze worden vrijgegeven.

> [!Important]  
> U wordt aangeraden alle servers die u gebruikt met Azure File Sync up-to-date te houden met de meest recente updates van Windows Update. 

### <a name="minimum-system-resources"></a>Minimale systeem bronnen
Azure File Sync vereist een server, fysiek of virtueel, met ten minste één CPU en mini maal 2 GiB geheugen.

> [!Important]  
> Als de server wordt uitgevoerd op een virtuele machine waarvoor dynamisch geheugen is ingeschakeld, moet de VM worden geconfigureerd met een minimum van 2048 MiB van het geheugen.

Voor de meeste productiewerk belastingen raden wij u aan om een Azure File Sync synchronisatie server niet te configureren met alleen de minimale vereisten. Raadpleeg de [Aanbevolen systeem bronnen](#recommended-system-resources) voor meer informatie.

### <a name="recommended-system-resources"></a>Aanbevolen systeem bronnen
Net als elke server functie of-toepassing worden de systeem resource vereisten voor Azure File Sync bepaald door de schaal van de implementatie. grotere implementaties op een server vereisen meer systeem bronnen. Voor Azure File Sync wordt de schaal bepaald door het aantal objecten in de eind punten van de server en het verloop op de gegevensset. Eén server kan server eindpunten hebben in meerdere synchronisatie groepen en het aantal objecten dat in de volgende tabel accounts wordt vermeld voor de volledige naam ruimte waaraan een server is gekoppeld. 

Bijvoorbeeld server eindpunt A met 10.000.000 objecten + server eindpunt B met 10.000.000 objecten = 20.000.000 objecten. Voor deze voorbeeld implementatie raden we 8 Cpu's, 16 GiB geheugen voor stabiele status aan en (indien mogelijk) 48 GiB van het geheugen voor de eerste migratie.
 
Naam ruimte gegevens worden in het geheugen opgeslagen om prestatie redenen. Als gevolg hiervan hebben grotere naam ruimten meer geheugen nodig om goede prestaties te behouden, en is er meer CPU vereist voor het uitvoeren van het verloop. 
 
In de volgende tabel hebben we zowel de grootte van de naam ruimte als de conversie naar capaciteit voor typische bestands shares voor algemene doel einden ingesteld, waarbij de gemiddelde bestands grootte 512 KiB is. Als de bestands grootten kleiner zijn, kunt u overwegen extra geheugen toe te voegen voor dezelfde hoeveelheid capaciteit. Baseer uw geheugen configuratie op de grootte van de naam ruimte.

| Grootte van de naam ruimte-bestanden & directory's (miljoenen)  | Typische capaciteit (TiB)  | CPU-kernen  | Aanbevolen geheugen (GiB) |
|---------|---------|---------|---------|
| 3        | 1.4     | 2        | 8 (initiële synchronisatie)/2 (standaard verloop)      |
| 5        | 2.3     | 2        | 16 (initiële synchronisatie)/4 (standaard verloop)    |
| 10       | 4,7     | 4        | 32 (initiële synchronisatie)/8 (standaard verloop)   |
| 30       | 14,0    | 8        | 48 (initiële synchronisatie)/16 (typische verloop)   |
| 50       | 23,3    | 16       | 64 (initiële synchronisatie)/32 (standaard verloop)  |
| 100 *     | 46,6    | 32       | 128 (initiële synchronisatie)/32 (standaard verloop)  |

\*het synchroniseren van meer dan 100.000.000 bestanden & mappen wordt op dit moment niet aanbevolen. Dit is een zachte limiet op basis van onze geteste drempel waarden. Zie [Azure files schaal baarheid en prestatie doelen](storage-files-scale-targets.md#azure-file-sync-scale-targets)voor meer informatie.

> [!TIP]
> De initiële synchronisatie van een naam ruimte is een intensieve bewerking. het is raadzaam meer geheugen toe te wijzen totdat de initiële synchronisatie is voltooid. Dit is niet vereist, maar kan de initiële synchronisatie versnellen. 
> 
> Normaal verloop is 0,5% van de naam ruimte gewijzigd per dag. Overweeg meer CPU toe te voegen voor een hogere mate van verloop. 

- Een lokaal gekoppeld volume dat is geformatteerd met het NTFS-bestands systeem.

### <a name="evaluation-cmdlet"></a>Evaluatie-cmdlet
Voordat u Azure File Sync implementeert, moet u evalueren of het compatibel is met uw systeem met behulp van de Azure File Sync Evaluation-cmdlet. Met deze cmdlet wordt gecontroleerd op mogelijke problemen met uw bestands systeem en gegevensset, zoals niet-ondersteunde tekens of een niet-ondersteunde versie van het besturings systeem. De controles dekken de meeste van de hieronder genoemde functies We raden u aan de rest van deze sectie zorgvuldig te lezen om ervoor te zorgen dat uw implementatie probleemloos verloopt. 

De evaluatie-cmdlet kan worden geïnstalleerd door de installatie van de AZ Power shell-module, die kan worden geïnstalleerd door de volgende instructies te volgen: [Installeer en configureer Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

#### <a name="usage"></a>Gebruik  
U kunt het evaluatie hulpprogramma op een aantal verschillende manieren aanroepen: u kunt de systeem controles, de gegevensset controleert of beide. Systeem-en gegevensset controles uitvoeren: 

```powershell
Invoke-AzStorageSyncCompatibilityCheck -Path <path>
```

Alleen uw gegevensset testen:
```powershell
Invoke-AzStorageSyncCompatibilityCheck -Path <path> -SkipSystemChecks
```
 
Alleen systeem vereisten testen:
```powershell
Invoke-AzStorageSyncCompatibilityCheck -ComputerName <computer name>
```
 
De resultaten weer geven in CSV:
```powershell
$errors = Invoke-AzStorageSyncCompatibilityCheck […]
$errors | Select-Object -Property Type, Path, Level, Description | Export-Csv -Path <csv path>
```

### <a name="file-system-compatibility"></a>Bestandssysteem compatibiliteit
Azure File Sync wordt alleen ondersteund op NTFS-volumes die rechtstreeks zijn gekoppeld. Direct gekoppelde opslag, of DAS, op Windows Server betekent dat het Windows Server-besturings systeem eigenaar is van het bestands systeem. DAS kan worden verschaft via fysiek gekoppelde schijven aan de bestands server, virtuele schijven koppelen aan een bestands Server-VM (zoals een virtuele machine die wordt gehost door Hyper-V) of zelfs via ISCSI.

Alleen NTFS-volumes worden ondersteund. ReFS, FAT, FAT32 en andere bestands systemen worden niet ondersteund.

De volgende tabel bevat de interop-status van NTFS-bestandssysteem functies: 

| Functie | Ondersteunings status | Opmerkingen |
|---------|----------------|-------|
| Toegangs beheer lijsten (Acl's) | Volledig ondersteund | Discretionaire toegangs beheer lijsten voor Windows-stijlen worden bewaard door Azure File Sync en worden afgedwongen door Windows Server op server-eind punten. U kunt ook Acl's afdwingen wanneer u de Azure-bestands share rechtstreeks koppelt, maar hiervoor is echter wel extra configuratie vereist. Zie de [sectie identiteit](#identity) voor meer informatie. |
| Vaste koppelingen | Overgeslagen | |
| Symbolische koppelingen | Overgeslagen | |
| Koppelpunten | Gedeeltelijk ondersteund | Koppel punten kunnen de hoofdmap van een server eindpunt zijn, maar ze worden overgeslagen als ze zijn opgenomen in de naam ruimte van een server eindpunt. |
| Koppelingen | Overgeslagen | Bijvoorbeeld Distributed File System mappen DfrsrPrivate en DFSRoots. |
| Reparsepunten | Overgeslagen | |
| NTFS-compressie | Volledig ondersteund | |
| Sparse bestanden | Volledig ondersteund | Verspreide bestanden worden gesynchroniseerd (worden niet geblokkeerd), maar gesynchroniseerd met de Cloud als een volledig bestand. Als de bestands inhoud in de Cloud (of op een andere server) wordt gewijzigd, wordt het bestand niet langer verspreid wanneer de wijziging wordt gedownload. |
| Alternatieve gegevens stromen (ADS) | Behouden, maar niet gesynchroniseerd | Classificatie Tags die zijn gemaakt door de infra structuur voor bestands classificatie worden bijvoorbeeld niet gesynchroniseerd. Bestaande classificatie Tags op bestanden op elk van de server-eind punten blijven ongewijzigd. |

<a id="files-skipped"></a>Azure File Sync worden ook bepaalde tijdelijke bestanden en systeem mappen overs Laan:

| Bestand/map | Opmerking |
|-|-|
| pagefile.sys | Bestand dat specifiek is voor systeem |
| Desktop.ini | Bestand dat specifiek is voor systeem |
| duims. db | Tijdelijk bestand voor miniatuur weergaven |
| ehthumbs. db | Tijdelijk bestand voor miniatuur weergaven van media |
| ~$\*.\* | Tijdelijk Office-bestand |
| \*. tmp | Tijdelijk bestand |
| \*. LACCDB | Access DB-bestand vergren delen|
| 635D02A9D91C401B97884B82B3BCDAEA.* | Intern synchronisatie bestand|
| Informatie over \\systeem volume | Map die specifiek is voor het volume |
| $RECYCLE. DOCKOPSLAGLOCATIE| Map |
| \\SyncShareState | Map voor synchronisatie |

### <a name="failover-clustering"></a>Failover Clustering
Windows Server Failover Clustering wordt ondersteund door Azure File Sync voor de implementatie optie ' Bestands server voor algemeen gebruik '. Failover Clustering wordt niet ondersteund op Scale-out bestandsserver voor toepassings gegevens (SOFS) of op geclusterde gedeelde volumes (Csv's).

> [!Note]  
> De Azure File Sync-agent moet worden geïnstalleerd op elk knoop punt in een failovercluster zodat synchronisatie goed kan worden uitgevoerd.

### <a name="data-deduplication"></a>Gegevensontdubbeling
**Windows server 2016 en Windows server 2019**   
Gegevensontdubbeling wordt ondersteund op volumes waarvoor Cloud lagen zijn ingeschakeld op Windows Server 2016 en Windows Server 2019. Door Gegevensontdubbeling in te scha kelen op een volume waarvoor Cloud lagen zijn ingeschakeld, kunt u meer bestanden on-premises opslaan zonder dat u meer opslag ruimte hoeft in te richten. 

Als Gegevensontdubbeling is ingeschakeld op een volume waarop Cloud lagen zijn ingeschakeld, worden geoptimaliseerde bestanden in de eindpunt locatie van het server niveau vergelijkbaar met een normaal bestand op basis van de beleids instellingen voor Cloud lagen. Zodra de geoptimaliseerde bestanden voor ontdubbeling zijn gelaagd, wordt de garbagecollection-taak voor Gegevensontdubbeling automatisch uitgevoerd om schijf ruimte vrij te maken door overbodige segmenten te verwijderen waarnaar niet meer wordt verwezen door andere bestanden op het volume.

Houd er rekening mee dat de besparing van volumes alleen van toepassing is op de-server. uw gegevens in de Azure-bestands share worden niet ontdubbeld.

> [!Note]  
> Voor de ondersteuning van Gegevensontdubbeling op volumes waarvoor Cloud lagen zijn ingeschakeld op Windows Server 2019, moet Windows Update [KB4520062](https://support.microsoft.com/help/4520062) zijn geïnstalleerd en moeten Azure file sync agent versie 9.0.0.0 of nieuwer zijn vereist.

**Windows Server 2012 R2**  
Azure File Sync biedt geen ondersteuning voor Gegevensontdubbeling en Cloud lagen op hetzelfde volume op Windows Server 2012 R2. Als Gegevensontdubbeling op een volume is ingeschakeld, moet Cloud lagen worden uitgeschakeld. 

**Opmerkingen**
- Als Gegevensontdubbeling is geïnstalleerd voordat de Azure File Sync-agent wordt geïnstalleerd, moet opnieuw opstarten zijn vereist voor de ondersteuning van Gegevensontdubbeling en Cloud lagen op hetzelfde volume.
- Als Gegevensontdubbeling is ingeschakeld op een volume nadat Cloud lagen zijn ingeschakeld, optimaliseert de eerste optimalisatie taak voor ontdubbeling bestanden op het volume dat nog niet al is gelaagd en heeft de volgende invloed op Cloud lagen:
    - Het beleid voor beschik bare ruimte gaat door met het heatmap met behulp van de beschik bare ruimte op het volume.
    - Met datum beleid wordt het trapsgewijs scha kelen van bestanden die mogelijk anderszins in aanmerking komen voor het maken van lagen, overgeslagen door de optimalisatie taak voor ontdubbeling om toegang te krijgen tot de bestanden.
- Voor voortdurende optimalisatie taken met ontdubbeling wordt de Cloud Tiering met het datum beleid vertraagd door de [MinimumFileAgeDays](https://docs.microsoft.com/powershell/module/deduplication/set-dedupvolume?view=win10-ps) -instelling voor gegevensontdubbeling als het bestand nog niet is gelaagd. 
    - Voor beeld: als de instelling MinimumFileAgeDays zeven dagen is en het beleid voor Cloud lagen 30 dagen is, worden de bestanden na 37 dagen in het datum beleid gelaagd.
    - Opmerking: wanneer een bestand wordt gelaagd door Azure File Sync, wordt het bestand door de optimalisatie taak voor ontdubbeling overgeslagen.
- Als een server met Windows Server 2012 R2 waarop de Azure File Sync-agent is geïnstalleerd, is bijgewerkt naar Windows Server 2016 of Windows Server 2019, moeten de volgende stappen worden uitgevoerd voor de ondersteuning van Gegevensontdubbeling en Cloud lagen op hetzelfde volume:  
    - Verwijder de Azure File Sync-agent voor Windows Server 2012 R2 en start de server opnieuw op.
    - Down load de Azure File Sync-agent voor de nieuwe versie van het besturings systeem van de server (Windows Server 2016 of Windows Server 2019).
    - Installeer de Azure File Sync agent en start de server opnieuw op.  
    
    Opmerking: de Azure File Sync configuratie-instellingen op de server blijven behouden wanneer de agent wordt verwijderd en opnieuw wordt geïnstalleerd.

### <a name="distributed-file-system-dfs"></a>Distributed File System (DFS)
Azure File Sync ondersteunt interop met DFS-naam ruimten (DFS-N) en DSF-replicatie (DFS-R).

**DFS-naam ruimten (DFS-n)** : Azure File Sync wordt volledig ondersteund op DFS-N-servers. U kunt de Azure File Sync-agent installeren op een of meer DFS-N-leden om gegevens te synchroniseren tussen de server eindpunten en het Cloud eindpunt. Zie overzicht van DFS- [naam ruimten](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview)voor meer informatie.
 
**DSF-replicatie (DFS-r)** : Aangezien DFS-r en Azure file sync beide replicatie oplossingen zijn, raden wij in de meeste gevallen DFS-r te vervangen door Azure file sync. Er zijn echter verschillende scenario's waarin u DFS-R en Azure File Sync tegelijk wilt gebruiken:

- U migreert van een DFS-R-implementatie naar een Azure File Sync-implementatie. Zie [een DSF-replicatie-implementatie (DFS-R) migreren naar Azure file sync](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync)voor meer informatie.
- Niet elke on-premises server waarvoor een kopie van uw bestands gegevens nodig is, kan rechtstreeks op internet worden aangesloten.
- Vertakkings servers consolideren gegevens op één hub-server waarvoor u Azure File Sync wilt gebruiken.

Voor Azure File Sync en DFS-R om naast elkaar te werken:

1. Azure File Sync Cloud lagen moeten worden uitgeschakeld op volumes met gerepliceerde DFS-R-mappen.
2. Server eindpunten mogen niet worden geconfigureerd in DFS-R-alleen-lezen replicatie mappen.

Zie [DSF-replicatie Overview](https://technet.microsoft.com/library/jj127250)voor meer informatie.

### <a name="sysprep"></a>Sysprep
Het gebruik van Sysprep op een server waarop de Azure File Sync-agent is geïnstalleerd, wordt niet ondersteund en kan leiden tot onverwachte resultaten. De agent installatie en-Server registratie moeten plaatsvinden na het implementeren van de server installatie kopie en het volt ooien van de Mini-Setup van Sysprep.

### <a name="windows-search"></a>Windows Search
Als Cloud lagen zijn ingeschakeld op een server eindpunt, worden de gelaagde bestanden overgeslagen en niet geïndexeerd met Windows Search. Niet-gelaagde bestanden worden correct geïndexeerd.

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Andere HSM-oplossingen (hiërarchische opslag beheer)
Er mogen geen andere HSM-oplossingen worden gebruikt met Azure File Sync.

## <a name="identity"></a>Identiteit
Azure File Sync werkt met uw standaard-op AD gebaseerde identiteit zonder dat dit meer hoeft te worden ingesteld dan bij het instellen van de synchronisatie. Wanneer u Azure File Sync gebruikt, is de algemene verwachting dat de meeste toegang tot de Azure File Sync cache servers gaat, in plaats van via de Azure-bestands share. Omdat de server-eind punten zich bevinden op Windows Server en Windows Server gedurende een lange periode AD-en Windows-stijl-Acl's ondersteunt, is er niets meer nodig dan ervoor te zorgen dat de Windows-bestands servers die zijn geregistreerd bij de opslag synchronisatie service lid zijn van een domein. Azure File Sync worden Acl's op de bestanden opgeslagen in de Azure-bestands share en worden deze gerepliceerd naar alle server eindpunten.

Hoewel wijzigingen die rechtstreeks aan de Azure-bestands share zijn aangebracht, langer zullen duren om te synchroniseren met de server eindpunten in de synchronisatie groep, kunt u er ook voor zorgen dat u uw AD-machtigingen op uw bestands share rechtstreeks in de Cloud afdwingt. Hiervoor moet u een domein toevoegen aan uw opslag account in uw on-premises AD, net zoals hoe uw Windows-bestands servers lid zijn van een domein. Zie [Azure Files Active Directory Overview](storage-files-active-directory-overview.md)voor meer informatie over het toevoegen van uw opslag account aan een Active Directory van de klant.

> [!Important]  
> Domein waarmee uw opslag account wordt toegevoegd aan Active Directory is niet vereist om Azure File Sync te kunnen implementeren. Dit is een strikt optionele stap waarmee de Azure-bestands share lokale Acl's kan afdwingen wanneer gebruikers de Azure-bestands share rechtstreeks koppelen.

## <a name="networking"></a>Netwerken
De Azure File Sync-Agent communiceert met uw opslag synchronisatie service en Azure-bestands share met behulp van het Azure File Sync REST-protocol en het FileREST-protocol, die beide altijd HTTPS gebruiken via poort 443. SMB wordt nooit gebruikt voor het uploaden of downloaden van gegevens tussen uw Windows-Server en de Azure-bestands share. Omdat de meeste organisaties HTTPS-verkeer via poort 443 toestaan, als vereiste voor het bezoeken van de meeste websites, is speciale netwerk configuratie doorgaans niet vereist voor de implementatie van Azure File Sync.

Op basis van het beleid of de unieke regelgeving van uw organisatie is het mogelijk dat u meer beperkend communicatie nodig hebt met Azure. Daarom biedt Azure File Sync verschillende mechanismen voor het configureren van netwerken. Op basis van uw vereisten kunt u het volgende doen:

- Tunnel Sync en uploaden/downloaden van bestanden via uw ExpressRoute of Azure VPN-verkeer. 
- Gebruik maken van Azure Files-en Azure-netwerk functies, zoals service-eind punten en privé-eind punten.
- Configureer Azure File Sync om uw proxy in uw omgeving te ondersteunen.
- Netwerk activiteit beperken van Azure File Sync.

Zie voor meer informatie over het configureren van de netwerk functionaliteit van Azure File Sync:
- [Azure File Sync-proxy en -firewallinstellingen](storage-sync-files-firewall-and-proxy.md)
- [Zorgen dat Azure File Sync een goede buur is in uw Data Center](storage-sync-files-server-registration.md)

## <a name="encryption"></a>Versleuteling
Wanneer u Azure File Sync gebruikt, moet u rekening houden met drie verschillende versleutelings lagen: versleuteling van de at-rest-opslag van Windows Server, versleuteling van de overdracht tussen de Azure File Sync agent en Azure en versleuteling van de rest van uw gegevens in de Azure-bestands share. 

### <a name="windows-server-encryption-at-rest"></a>Windows Server-versleuteling in rust 
Er zijn twee strategieën voor het versleutelen van gegevens op Windows Server die in het algemeen werken met Azure File Sync: versleuteling onder het bestands systeem, zodat het bestands systeem en alle gegevens die naar het bestand worden geschreven, worden versleuteld en versleuteld in de bestands indeling zelf. Deze methoden sluiten elkaar niet uit. ze kunnen samen worden gebruikt, omdat het doel van de versleuteling anders is.

Windows Server biedt BitLocker-postvak in om versleuteling onder het bestands systeem te bieden. BitLocker is volledig transparant voor Azure File Sync. De belangrijkste reden voor het gebruik van een versleutelings mechanisme zoals BitLocker is om te voor komen dat de fysieke exfiltration van gegevens van uw on-premises Data Center door iemand die de schijven stelen en om te voor komen dat een niet-geautoriseerd besturings systeem ongeoorloofde Lees-en schrijf bewerkingen naar uw gegevens kan uitvoeren. Zie [overzicht van BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview)voor meer informatie over BitLocker.

Producten van derden die op dezelfde manier werken als BitLocker, in die onder het NTFS-volume, moeten op vergelijk bare wijze volledig transparant werken met Azure File Sync. 

De andere belangrijkste methode voor het versleutelen van gegevens is het versleutelen van de gegevens stroom van het bestand wanneer het bestand door de toepassing wordt opgeslagen. Sommige toepassingen kunnen dit systeem eigen maken, maar dit is doorgaans niet het geval. Een voor beeld van een methode voor het versleutelen van de gegevens stroom van het bestand is Azure Information Protection (beheerders)/Azure Rights Management Services (Azure RMS)/Active Directory RMS. De belangrijkste reden voor het gebruik van een versleutelings mechanisme zoals beheerders/RMS is om gegevens exfiltration van de bestands share te voor komen door personen die de gegevens naar alternatieve locaties kopiëren, zoals naar een flash-station, of het e-mailen naar een niet-geautoriseerde persoon. Wanneer de gegevens stroom van een bestand is versleuteld als onderdeel van de bestands indeling, blijft dit bestand versleuteld op de Azure-bestands share. 

Azure File Sync werkt niet met NTFS Encrypted File System (NTFS EFS) of versleutelings oplossingen van derden die boven het bestands systeem zitten, maar onder de gegevens stroom van het bestand. 

### <a name="encryption-in-transit"></a>Versleuteling in transit
Azure File Sync Agent communiceert met de opslag synchronisatie service en de Azure-bestands share met behulp van het Azure File Sync REST-protocol en het FileREST-protocol, die beide altijd HTTPS gebruiken via poort 443. Azure File Sync verzendt geen niet-versleutelde aanvragen via HTTP. 

Azure Storage-accounts bevatten een switch voor het vereisen van versleuteling in transit, dat standaard is ingeschakeld. Zelfs als de switch op het niveau van het opslag account is uitgeschakeld, betekent dit dat er niet-versleutelde verbindingen met uw Azure-bestands shares mogelijk zijn Azure File Sync worden nog steeds alleen versleutelde kanalen gebruikt om toegang te krijgen tot uw bestands share.

De belangrijkste reden om versleuteling voor het opslag account uit te scha kelen, is het ondersteunen van een verouderde toepassing die moet worden uitgevoerd op een ouder besturings systeem, zoals Windows Server 2008 R2 of een oudere Linux-distributie, direct samen werken met een Azure-bestands share. Als de verouderde toepassing een besprekingen maakt met de Windows Server-cache van de bestands share, heeft deze instelling geen effect. 

We raden u ten zeerste aan te zorgen dat de versleuteling van gegevens in-transit is ingeschakeld.

Zie [veilige overdracht vereisen in azure Storage](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)voor meer informatie over het door sturen van versleuteling.

### <a name="azure-file-share-encryption-at-rest"></a>Versleuteling van Azure-bestands shares in rust
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>Opslag lagen
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>Schakel standaard bestands shares in om Maxi maal 100 TiB te beslaan
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="regional-availability"></a>Regionale beschikbaarheid
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="azure-file-sync-region-availability"></a>Beschik baarheid van Azure file sync-regio
Azure File Sync is beschikbaar in de volgende regio's:

| Azure-Cloud | Geografische regio | Azure-regio | Regio code |
|-------------|-------------------|--------------|-------------|
| Openbaar | Azië | Azië - oost | `eastasia` |
| Openbaar | Azië | Azië - zuidoost | `southeastasia` |
| Openbaar | Australië | Australië - oost | `australiaeast` |
| Openbaar | Australië | Australië - zuidoost | `australiasoutheast` |
| Openbaar | Brazilië | Brazilië - zuid | `brazilsouth` |
| Openbaar | Canada | Canada - midden | `canadacentral` |
| Openbaar | Canada | Canada - oost | `canadaeast` |
| Openbaar | Europa | Europa - noord | `northeurope` |
| Openbaar | Europa | Europa -west | `westeurope` |
| Openbaar | Frankrijk | Frankrijk - centraal | `francecentral` |
| Openbaar | Frankrijk | Frankrijk-zuid * | `francesouth` |
| Openbaar | India | India - centraal | `centralindia` |
| Openbaar | India | India - zuid | `southindia` |
| Openbaar | Japan | Japan - oost | `japaneast` |
| Openbaar | Japan | Japan - west | `japanwest` |
| Openbaar | Korea | Korea - centraal | `koreacentral` |
| Openbaar | Korea | Korea - zuid | `koreasouth` |
| Openbaar | Zuid-Afrika | Zuid-Afrika - noord | `southafricanorth` |
| Openbaar | Zuid-Afrika | Zuid-Afrika-west * | `southafricawest` |
| Openbaar | VAE | UAE-centraal * | `uaecentral` |
| Openbaar | VAE | UAE - noord | `uaenorth` |
| Openbaar | VK | Verenigd Koninkrijk Zuid | `uksouth` |
| Openbaar | VK | Verenigd Koninkrijk West | `ukwest` |
| Openbaar | VS | VS - centraal | `centralus` |
| Openbaar | VS | VS - oost | `eastus` |
| Openbaar | VS | VS - oost 2 | `eastus2` |
| Openbaar | VS | VS - noord-centraal | `northcentralus` |
| Openbaar | VS | VS - zuid-centraal | `southcentralus` |
| Openbaar | VS | VS - west-centraal | `westcentralus` |
| Openbaar | VS | VS - west | `westus` |
| Openbaar | VS | VS - west 2 | `westus2` |
| US Gov | VS | VS (overheid) - Arizona | `usgovarizona` |
| US Gov | VS | VS (overheid) - Texas | `usgovtexas` |
| US Gov | VS | VS (overheid) - Virginia | `usgovvirginia` |

Azure File Sync ondersteunt alleen synchronisatie met een Azure-bestands share die zich in dezelfde regio bevinden als de opslag synchronisatie service.

Voor de regio's die zijn gemarkeerd met sterretjes, moet u contact opnemen met de ondersteuning van Azure om toegang aan te vragen tot Azure Storage in die regio's. Het proces wordt beschreven in [dit document](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="redundancy"></a>Redundantie
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

> [!Important]  
> Met geografisch redundante en geo-zone redundante opslag kunt u hand matig failover-opslag naar de secundaire regio. U wordt aangeraden dit niet buiten een nood geval te doen wanneer u Azure File Sync gebruikt vanwege de verhoogde kans op gegevens verlies. In het geval van een nood geval dat u een hand matige failover van de opslag wilt initiëren, moet u een ondersteunings aanvraag openen bij micro soft om Azure File Sync te krijgen om de synchronisatie met het secundaire eind punt te hervatten.

## <a name="migration"></a>Migratie
Als u een bestaande Windows-Bestands server hebt, kan Azure File Sync rechtstreeks worden geïnstalleerd, zonder dat u gegevens naar een nieuwe server hoeft te verplaatsen. Als u van plan bent om te migreren naar een nieuwe Windows-Bestands server als onderdeel van het aannemen van Azure File Sync, zijn er verschillende mogelijke benaderingen om gegevens te verplaatsen:

- Maak server eindpunten voor uw oude bestands share en uw nieuwe bestands share en laat Azure File Sync de gegevens van de server-eind punten synchroniseren. Het voor deel van deze benadering is dat het heel eenvoudig is om de opslag op uw nieuwe bestands server te overschreven, omdat Azure File Sync Cloud Tiering heeft. Wanneer u klaar bent, kunt u de eind gebruikers naar de bestands share op de nieuwe server knippen en het server eindpunt van de oude bestands share verwijderen.

- Maak op de nieuwe bestands server alleen een server eindpunt en kopieer gegevens naar de oude bestands share met behulp van `robocopy`. Afhankelijk van de topologie van de bestands shares op de nieuwe server (hoeveel shares u op elk volume hebt, hoe gratis elk volume is, enzovoort), moet u mogelijk tijdelijk extra opslag ruimte inrichten omdat `robocopy` van uw oude server naar uw nieuwe server in uw on-premises Data Center sneller gaat dan Azure File Sync gegevens naar Azure verplaatst.

Het is ook mogelijk om Data Box te gebruiken om gegevens te migreren naar een Azure File Sync-implementatie. De meeste tijd, wanneer klanten Data Box willen gebruiken om gegevens op te nemen, doen ze dit omdat ze denken dat ze de snelheid van de implementatie verg Roten of dat ze hulp kunnen bieden bij beperkte bandbreedte scenario's. Hoewel het gebruik van een Data Box voor het opnemen van gegevens in uw Azure File Sync-implementatie het bandbreedte gebruik verlaagt, is het waarschijnlijk sneller voor de meeste scenario's om een online gegevens upload uit te voeren via een van de hierboven beschreven methoden. Zie voor meer informatie over het gebruik van Data Box om gegevens op te nemen in uw Azure File Sync-implementatie de [gegevens migreren naar Azure file sync met Azure data Box](storage-sync-offline-data-transfer.md).

Een veelvoorkomende fout bij het migreren van gegevens naar de nieuwe Azure File Sync-implementatie is het kopiëren van gegevens rechtstreeks naar de Azure-bestands share in plaats van op hun Windows-bestands servers. Hoewel Azure File Sync alle nieuwe bestanden in de Azure-bestands share herkent en deze weer synchroniseert met uw Windows-bestands shares, is dit doorgaans aanzienlijk langzamer dan het laden van gegevens via de Windows-Bestands server. Veel Azure Copy-hulpprogram ma's, zoals AzCopy, hebben het extra nadeel van het kopiëren van alle belang rijke meta gegevens van een bestand, zoals tijds tempels en Acl's.

## <a name="antivirus"></a>Leverancier
Omdat anti virus werkt door bestanden te scannen op bekende schadelijke code, kan een antivirus product ertoe leiden dat gelaagde bestanden worden teruggehaald. In versies 4,0 en hoger van de Azure File Sync-agent hebben gelaagde bestanden het beveiligde Windows-kenmerk FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS ingesteld. We raden u aan om te leren werken met uw software leverancier voor meer informatie over het configureren van de oplossing voor het overs laan van het lezen van bestanden met deze kenmerkset (veel doen dat automatisch). 

De interne anti-virus oplossingen van micro soft, Windows Defender en System Center Endpoint Protection (SCEP), over het automatisch overs laan van het lezen van bestanden waarvoor dit kenmerk is ingesteld. We hebben deze getest en een klein probleem geïdentificeerd: wanneer u een server aan een bestaande synchronisatie groep toevoegt, worden bestanden die kleiner zijn dan 800 bytes op de nieuwe server ingetrokken (gedownload). Deze bestanden blijven op de nieuwe server en worden niet getierd, omdat ze niet voldoen aan de vereiste voor de laag limiet (> 64 KB).

> [!Note]  
> Antivirus leveranciers kunnen de compatibiliteit controleren tussen hun product en Azure File Sync met behulp van de [Azure file sync anti virus Compatibility Test Suite](https://www.microsoft.com/download/details.aspx?id=58322), die beschikbaar is voor downloaden in het micro soft Download centrum.

## <a name="backup"></a>Back-up maken 
Net als bij antivirus oplossingen kunnen back-upoplossingen ertoe leiden dat gelaagde bestanden worden teruggehaald. U kunt het beste een Cloud back-upoplossing gebruiken om een back-up te maken van de Azure-bestands share in plaats van een on-premises back-upproduct

Als u een on-premises back-upoplossing gebruikt, moeten back-ups worden uitgevoerd op een server in de synchronisatie groep waarvoor Cloud lagen zijn uitgeschakeld. Wanneer u een herstel bewerking uitvoert, gebruikt u de opties op volume-of bestands niveau herstellen. Bestanden die zijn hersteld met de optie herstel op bestands niveau worden gesynchroniseerd naar alle eind punten in de synchronisatie groep en bestaande bestanden worden vervangen door de versie die wordt hersteld vanuit een back-up.  Herstel bewerkingen op volume niveau worden niet vervangen door nieuwere bestands versies in de Azure-bestands share of andere server eindpunten.

> [!Note]  
> Herstellen met Bare-Metal (BMR) kan leiden tot onverwachte resultaten en wordt op dit moment niet ondersteund.

> [!Note]  
> Met versie 9 van de Azure File Sync agent worden VSS-moment opnamen (inclusief het tabblad vorige versies) nu ondersteund op volumes waarvoor Cloud lagen zijn ingeschakeld. U moet echter compatibiliteit met eerdere versies inschakelen via Power shell. [Meer informatie](storage-files-deployment-guide.md).

## <a name="azure-file-sync-agent-update-policy"></a>Updatebeleid Azure File Sync-agent
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>Volgende stappen
* [Firewall-en proxy-instellingen overwegen](storage-sync-files-firewall-and-proxy.md)
* [Implementatie van Azure Files plannen](storage-files-planning.md)
* [Azure Files implementeren](storage-files-deployment-guide.md)
* [Azure Files SYNC implementeren](storage-sync-files-deployment-guide.md)
* [Azure File Sync bewaken](storage-sync-files-monitoring.md)