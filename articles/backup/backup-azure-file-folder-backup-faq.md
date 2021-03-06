---
title: Back-ups maken van bestanden en mappen-Veelgestelde vragen
description: Behandelt Veelgestelde vragen over het maken van back-ups van bestanden en mappen met Azure Backup.
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: adcbf5c3b404de46634423f8f59c4798d44bebe0
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79273421"
---
# <a name="common-questions-about-backing-up-files-and-folders"></a>Veelgestelde vragen over het maken van back-ups van bestanden en mappen

In dit artikel vindt u antwoorden op veelgestelde vragen Abound het maken van back-ups van bestanden en mappen met de Microsoft Azure Recovery Services-agent (MARS) in de [Azure backup](backup-overview.md) -service.

## <a name="configure-backups"></a>Back-ups configureren

### <a name="where-can-i-download-the-latest-version-of-the-mars-agent"></a>Waar kan ik de nieuwste versie van de MARS-agent downloaden?

De meest recente MARS-agent die wordt gebruikt bij het maken van een back-up van Windows Server-computers, System Center DPM en Microsoft Azure Backup Server, kan worden [gedownload](https://aka.ms/azurebackup_agent).

### <a name="how-long-are-vault-credentials-valid"></a>Hoe lang zijn de kluis referenties geldig?

De kluisreferenties verlopen na 48 uur. Als het referenties bestand is verlopen, downloadt u het bestand opnieuw vanuit de Azure Portal.

### <a name="from-what-drives-can-i-back-up-files-and-folders"></a>Vanaf welke stations kan ik back-ups maken van bestanden en mappen?

U kunt geen back-up maken van de volgende typen stations en volumes:

* Verwissel bare media: alle bronnen van back-upitems moeten worden gerapporteerd als vast.
* Alleen-lezen volumes: het volume moet schrijfbaar zijn voor het functioneren van de Volume Shadow Copy service (VSS).
* Offline volumes: het volume moet online zijn om te kunnen werken met VSS.
* Netwerk shares: het volume moet lokaal op de server zijn om een back-up te maken met online back-ups.
* Met BitLocker beveiligde volumes: het volume moet worden ontgrendeld voordat de back-up kan worden uitgevoerd.
* Bestandssysteemidentificatie: NTFS is het enige bestandssysteem dat wordt ondersteund.

### <a name="what-file-and-folder-types-are-supported"></a>Welke typen bestanden en mappen worden ondersteund?

Meer [informatie](backup-support-matrix-mars-agent.md#supported-file-types-for-backup) over de typen bestanden en mappen die worden ondersteund voor back-ups.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-an-azure-vm"></a>Kan ik de MARS-agent gebruiken om een back-up te maken van bestanden en mappen op een virtuele Azure-machine?  

Ja. Azure Backup biedt back-up op VM-niveau voor virtuele Azure-machines met behulp van de VM-extensie voor de Azure VM-agent. Als u een back-up wilt maken van bestanden en mappen op het gast-Windows-besturings systeem op de virtuele machine, kunt u de MARS-agent installeren.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-temporary-storage-for-the-azure-vm"></a>Kan ik de MARS-agent gebruiken om een back-up te maken van bestanden en mappen op de tijdelijke opslag voor de virtuele Azure-machine?

Ja. Installeer de MARS-agent en maak een back-up van bestanden en mappen op het gast-Windows-besturings systeem naar de tijdelijke opslag.

* Back-uptaken mislukken wanneer tijdelijke opslag gegevens worden gewist.
* Als de tijdelijke opslag gegevens worden verwijderd, kunt u alleen herstellen naar niet-vluchtige opslag.

### <a name="how-do-i-register-a-server-to-another-region"></a>Hoe kan ik een server registreren bij een andere regio?

Back-upgegevens worden verzonden naar het Data Center van de kluis waarin de server is geregistreerd. De eenvoudigste manier om het Data Center te wijzigen is door de agent te verwijderen en opnieuw te installeren en de machine vervolgens te registreren bij een nieuwe kluis in de gewenste regio.

### <a name="does-the-mars-agent-support-windows-server-2012-deduplication"></a>Ondersteunt de MARS-agent Windows Server 2012-ontdubbeling?

Ja. De MARS-agent converteert de ontdubbelde gegevens naar normale gegevens wanneer de back-upbewerking wordt voor bereid. Vervolgens worden de gegevens voor het maken van de back-up geoptimaliseerd, worden de gegevens versleuteld en worden de versleutelde gegevens vervolgens naar de kluis verzonden.

## <a name="manage-backups"></a>Back-ups beheren

### <a name="what-happens-if-i-rename-a-windows-machine-configured-for-backup"></a>Wat gebeurt er als ik de naam Wijzig van een Windows-computer die is geconfigureerd voor back-up?

Wanneer u de naam van een Windows-computer wijzigt, worden alle momenteel geconfigureerde back-ups gestopt.

* U moet de nieuwe machine naam registreren bij de back-upkluis.
* Wanneer u de nieuwe naam bij de kluis registreert, is de eerste bewerking een *volledige* back-up.
* Als u gegevens moet herstellen waarvan een back-up naar de kluis is gemaakt met de oude server naam, gebruikt u de optie om terug te zetten naar een andere locatie in de wizard gegevens herstellen. [Meer informatie](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

### <a name="what-is-the-maximum-file-path-length-for-backup"></a>Wat is de maximale lengte voor het bestandspad voor back-up?

De MARS-agent is afhankelijk van NTFS en maakt gebruik van de lengte specificatie filepath, beperkt door de [Windows-API](/windows/desktop/FileIO/naming-a-file#fully-qualified-vs-relative-paths). Als de bestanden die u wilt beveiligen, langer zijn dan de toegestane waarde, maakt u een back-up van de bovenliggende map of van het schijf station.  

### <a name="what-characters-are-allowed-in-file-paths"></a>Welke tekens zijn toegestaan in bestands paden?

De MARS-agent is afhankelijk van NTFS en ondersteunt [tekens](/windows/desktop/FileIO/naming-a-file#naming-conventions) in bestands namen/paden.

### <a name="the-warning-azure-backups-have-not-been-configured-for-this-server-appears"></a>De waarschuwing ' Azure-back-ups zijn niet geconfigureerd voor deze server ' wordt weer gegeven

Deze waarschuwing kan worden weer gegeven ondanks dat u een back-upbeleid hebt geconfigureerd, wanneer de instellingen voor het back-upschema die zijn opgeslagen op de lokale server niet hetzelfde zijn als de instellingen die zijn opgeslagen in de back-upkluis.

* Wanneer de server of de instellingen zijn hersteld naar een bekende goede staat, kunnen back-upscheman niet meer worden gesynchroniseerd.
* Als deze waarschuwing wordt weer gegeven, [configureert](backup-azure-manage-windows-server.md) u het back-upbeleid opnieuw en voert u een back-up op aanvraag uit om de lokale server opnieuw te synchroniseren met Azure.

## <a name="manage-the-backup-cache-folder"></a>De map voor de back-upcache beheren

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder"></a>Wat is de vereiste minimumgrootte voor de cachemap?

De grootte van de cachemap bepaalt de hoeveelheid gegevens waarvan u een back-up maakt.

* De volumes in de cache-map moeten vrije ruimte hebben die gelijk is aan ten minste 5-10% van de totale grootte van de back-upgegevens.
* Als het volume minder dan 5% beschik bare ruimte heeft, verg root u de volume grootte of verplaatst u de cachemap naar een volume met voldoende ruimte door de volgende [stappen uit te voeren](#how-do-i-change-the-cache-location-for-the-mars-agent).
* Als u een back-up maakt van de systeem status van Windows, hebt u een extra beschik bare schijf ruimte van 30-35 GB nodig in het volume met de cachemap.

### <a name="how-to-check-if-scratch-folder-is-valid-and-accessible"></a>Controleren of de Scratch-map geldig is en toegankelijk is?

1. Standaard bevindt de map Scratch zich op `\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
2. Zorg ervoor dat het pad naar de locatie van de map Scratch overeenkomt met de waarden van de register sleutel vermeldingen die hieronder worden weer gegeven:

    | Registerpad | Registersleutel | Waarde |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Nieuwe locatie van de cachemap* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Nieuwe locatie van de cachemap* |

### <a name="how-do-i-change-the-cache-location-for-the-mars-agent"></a>Hoe kan ik de cache locatie van de MARS-agent wijzigen?

1. Voer deze opdracht uit in een opdracht prompt met verhoogde bevoegdheid om de back-upengine te stoppen:

    ```Net stop obengine```
2. Als u de systeem status back-up hebt geconfigureerd, opent u schijf beheer en ontkoppelt u de schijven met namen in de indeling `"CBSSBVol_<ID>"`.
3. De map Scratch bevindt zich standaard op `\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
4. Kopieer de volledige map `\Scratch` naar een ander station dat voldoende ruimte heeft. Zorg ervoor dat de inhoud wordt gekopieerd, niet verplaatst.
5. Werk de volgende Register vermeldingen bij met het pad van de zojuist verplaatste Scratch-map.

    | Registerpad | Registersleutel | Waarde |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Locatie van nieuwe Scratch map* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Locatie van nieuwe Scratch map* |

6. Start de backup-engine opnieuw op bij een opdracht prompt met verhoogde bevoegdheid:

    ```command
    Net stop obengine

    Net start obengine
    ```

7. Een back-up op aanvraag uitvoeren. Nadat de back-up is voltooid met behulp van de nieuwe locatie, kunt u de oorspronkelijke cachemap verwijderen.

### <a name="where-should-the-cache-folder-be-located"></a>Waar moet de cachemap zich bevinden?

De volgende locaties voor de cachemap worden niet aanbevolen:

* Netwerk share/Verwissel bare media: de cachemap moet lokaal zijn voor de server waarvan een back-up moet worden gemaakt met online back-ups. Netwerk locaties of verwissel bare media, zoals USB-stations, worden niet ondersteund.
* Offline volumes: de cachemap moet online zijn voor de verwachte back-up met Azure Backup Agent

### <a name="are-there-any-attributes-of-the-cache-folder-that-arent-supported"></a>Zijn er kenmerken van de cachemap die niet worden ondersteund?

De volgende kenmerken of combinaties van deze kenmerken worden niet ondersteund voor de cachemap:

* Versleuteld
* Ontdubbeld
* Gecomprimeerd
* Verspreide gegevens
* Reparsepunt

De cachemap en de meta gegevens-VHD hebben niet de benodigde kenmerken voor de Azure Backup-Agent.

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-for-backup"></a>Is er een manier om de hoeveelheid band breedte die voor de back-up wordt gebruikt, aan te passen?

Ja, u kunt de optie **Eigenschappen wijzigen** in de Mars-agent gebruiken om de band breedte en de timing aan te passen. [Meer informatie](backup-windows-with-mars-agent.md#enable-network-throttling).

## <a name="restore"></a>Herstellen

### <a name="manage"></a>Beheren

**Kan ik herstellen als ik mijn wachtwoordzin ben verg eten?**
De Azure Backup-Agent vereist een wachtwoordzin (die u hebt ingevoerd tijdens de registratie) om de back-upgegevens tijdens het herstellen te ontsleutelen. Bekijk de onderstaande scenario's om inzicht te krijgen in uw opties voor het afhandelen van een verloren wachtwoordzin:

| Oorspronkelijke computer <br> *(bron machine waar back-ups zijn gemaakt)* | Passphrase | Beschik bare opties |
| --- | --- | --- |
| Beschikbaar |Verdwenen |Als uw oorspronkelijke computer (waar back-ups zijn gemaakt) beschikbaar is en nog steeds is geregistreerd met dezelfde Recovery Services kluis, kunt u de wachtwoordzin opnieuw genereren door de volgende [stappen uit te voeren](https://docs.microsoft.com/azure/backup/backup-azure-manage-mars#re-generate-passphrase).  |
| Verdwenen |Verdwenen |Het is niet mogelijk om de gegevens te herstellen of de gegevens zijn niet beschikbaar |

Houd rekening met de volgende voor waarden:

* Als u de agent op dezelfde oorspronkelijke machine verwijdert en opnieuw registreert met
  * *Dezelfde wachtwoordzin*; vervolgens kunt u de back-upgegevens herstellen.
  * Een *andere wachtwoordzin*, dan kunt u de back-upgegevens niet herstellen.
* Als u de agent op een *andere computer* installeert met
  * *Dezelfde wachtwoordzin* (gebruikt in de oorspronkelijke machine), dan kunt u uw back-upgegevens herstellen.
  * *Verschillende wachtwoordzin*, u kunt uw back-upgegevens niet herstellen.
* Als de oorspronkelijke machine is beschadigd (voor komt dat u de wachtwoordzin opnieuw genereert via de MARS-console), maar u kunt de oorspronkelijke Scratch-map die wordt gebruikt door de MARS-agent, herstellen of openen, dan kan het zijn dat u kunt herstellen (als u het wacht woord bent verg eten). Neem contact op met de klant ondersteuning voor meer informatie.

**Hoe kan ik herstellen als ik mijn oorspronkelijke machine kwijt ben (waar de back-ups zijn gemaakt)?**

Als u dezelfde wachtwoordzin hebt (die u tijdens de registratie hebt ingesteld) van de oorspronkelijke computer, kunt u de gegevens waarvan een back-up is gemaakt herstellen naar een andere computer. Bekijk de onderstaande scenario's voor meer informatie over de opties voor terugzetten.

| Oorspronkelijke computer | Passphrase | Beschik bare opties |
| --- | --- | --- |
| Verdwenen |Beschikbaar |U kunt de MARS-agent op een andere computer installeren en registreren met dezelfde wachtwoordzin die u hebt ingevoerd tijdens de registratie van de oorspronkelijke computer. Kies **herstel optie** > **andere locatie** om uw herstel uit te voeren. Zie [dit artikel](https://docs.microsoft.com/azure/backup/backup-azure-restore-windows-server#use-instant-restore-to-restore-data-to-an-alternate-machine) voor meer informatie.
| Verdwenen |Verdwenen |Het is niet mogelijk om de gegevens te herstellen of de gegevens zijn niet beschikbaar |


### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>Wat gebeurt er als ik een actieve herstel taak Annuleer?

Als een actieve herstel taak wordt geannuleerd, wordt het herstel proces gestopt. Alle bestanden die vóór de annulering worden teruggezet, blijven geconfigureerd op de ingestelde bestemming (oorspronkelijke of alternatieve locatie), zonder terugdraai bewerkingen.

### <a name="does-the-mars-agent-back-up-and-restore-acls-set-on-files-folders-and-volumes"></a>Maakt de MARS-agent een back-up en herstelt u de Acl's die zijn ingesteld voor bestanden, mappen en volumes?

* De MARS-agent maakt een back-up van de Acl's die zijn ingesteld voor bestanden, mappen en volumes
* Voor de herstel optie voor volume herstel biedt de MARS agent een optie voor het overs laan van het terugzetten van ACL-machtigingen naar het bestand of de map die wordt hersteld
* Voor de afzonderlijke herstel optie voor bestanden en mappen wordt de MARS-agent teruggezet met ACL-machtigingen (er is geen optie voor het overs laan van het terugzetten van de toegangs beheer lijst).

## <a name="next-steps"></a>Volgende stappen

[Meer informatie](tutorial-backup-windows-server-to-azure.md) over het maken van een back-up van een Windows-computer.
