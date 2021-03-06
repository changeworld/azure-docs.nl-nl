---
title: Ondersteuningsmatrix voor Azure Backup
description: Bevat een samenvatting van ondersteuningsinstellingen en -beperkingen voor de Azure Backup-service.
ms.topic: conceptual
ms.date: 02/17/2019
ms.openlocfilehash: d036e527880a98d323e8de2f3a8721d7e12dbb07
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79273265"
---
# <a name="support-matrix-for-azure-backup"></a>Ondersteunings matrix voor Azure Backup

U kunt [Azure backup](backup-overview.md) gebruiken om een back-up te maken van gegevens naar het Microsoft Azure Cloud platform. In dit artikel vindt u een overzicht van de algemene ondersteunings instellingen en-beperkingen voor Azure Backup scenario's en implementaties.

Andere ondersteunings matrices zijn beschikbaar:

- Ondersteunings matrix voor [back-up van Azure virtual machine (VM)](backup-support-matrix-iaas.md)
- Ondersteunings matrix voor back-up met behulp van [System Center Data Protection Manager (DPM)/Microsoft Azure backup server (MABS)](backup-support-matrix-mabs-dpm.md)
- Ondersteunings matrix voor back-up met behulp van de [Microsoft Azure Recovery Services-agent (Mars)](backup-support-matrix-mars-agent.md)

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="vault-support"></a>Ondersteuning voor kluizen

Azure Backup gebruikt Recovery Services kluizen om back-ups te organiseren en te beheren. Er worden ook kluizen gebruikt voor het opslaan van back-upgegevens.

In de volgende tabel worden de functies van Recovery Services kluizen beschreven:

**Functie** | **Details**
--- | ---
**Kluizen in het abonnement** | Maximaal 500 Recovery Services-kluizen in één abonnement.
**Machines in een kluis** | Maxi maal 1.000 Azure-Vm's in één kluis.<br/><br/> Maxi maal 50 MABS-servers kunnen worden geregistreerd in één kluis.
**Gegevens bronnen** | De maximale grootte van een afzonderlijke [gegevens bron](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq#how-is-the-data-source-size-determined) is 54.400 GB. Deze limiet is niet van toepassing op back-ups van Azure-VM'S. Er gelden geen limieten voor de totale hoeveelheid gegevens waarvan u een back-up kunt maken naar de kluis.
**Back-ups naar de kluis** | **Azure-vm's:** Eenmaal per dag.<br/><br/>**Machines die worden beveiligd door DPM-MABS:** Twee keer per dag.<br/><br/> **Machines maken rechtstreeks back-ups met behulp van de Mars-agent:** Drie keer per dag.
**Back-ups tussen kluizen** | De back-up bevindt zich in een regio.<br/><br/> U hebt een kluis nodig in elke Azure-regio die virtuele machines bevat waarvan u een back-up wilt maken. U kunt geen back-up maken naar een andere regio.
**Kluizen verplaatsen** | U kunt [kluizen verplaatsen](https://docs.microsoft.com/azure/backup/backup-azure-move-recovery-services-vault) tussen abonnementen of tussen resource groepen in hetzelfde abonnement. Het verplaatsen van kluizen over regio's wordt echter niet ondersteund.
**Gegevens verplaatsen tussen kluizen** | Het verplaatsen van gegevens waarvan een back-up wordt gemaakt tussen kluizen, wordt niet ondersteund.
**Type kluis opslag wijzigen** | U kunt het type opslag replicatie (geografisch redundante opslag of lokaal redundante opslag) voor een kluis wijzigen voordat er back-ups worden opgeslagen. Nadat een back-ups in de kluis is begonnen, kan het replicatietype niet meer worden gewijzigd.

## <a name="on-premises-backup-support"></a>On-premises ondersteuning voor back-ups

Dit wordt what's ondersteund als u een back-up wilt maken van on-premises machines:

**Machine** | **Waarvan wordt een back-up gemaakt?** | **Locatie** | **Functies**
--- | --- | --- | ---
**Directe back-up van Windows-machine met MARS-agent** | Bestanden, mappen, systeemstatus | Maak een back-up naar Recovery Services kluis. | Drie keer per dag een back-up maken<br/><br/> Geen app-bewuste back-up<br/><br/> Bestand herstellen, map, volume
**Directe back-up van Linux-machine met MARS-agent** | Back-up wordt niet ondersteund
**Back-ups maken naar DPM** | Bestanden, mappen, volumes, systeem status, app-gegevens | Maak een back-up naar de lokale DPM-opslag. DPM voert vervolgens een back-up uit naar de kluis. | App-bewuste momentopnamen<br/><br/> Volledige granulatie voor back-up en herstel<br/><br/> Linux ondersteund voor virtuele machines (Hyper-V/VMware)<br/><br/> Oracle wordt niet ondersteund
**Back-up naar MABS** | Bestanden, mappen, volumes, systeem status, app-gegevens | Maak een back-up naar MABS lokale opslag. MABS voert vervolgens een back-up uit naar de kluis. | App-bewuste momentopnamen<br/><br/> Volledige granulatie voor back-up en herstel<br/><br/> Linux ondersteund voor virtuele machines (Hyper-V/VMware)<br/><br/> Oracle wordt niet ondersteund

## <a name="azure-vm-backup-support"></a>Ondersteuning voor Azure VM-back-ups

### <a name="azure-vm-limits"></a>Limieten voor Azure VM's

**Limiet** | **Details**
--- | ---
**Gegevens schijven van virtuele machines van Azure** | Limiet van 16 <br> Als u zich wilt aanmelden voor de persoonlijke preview-versie van VM's met meer dan 16 schijven (maximaal 32 schijven), stuurt u een bericht naar AskAzureBackupTeam@microsoft.com
**Grootte van de Azure VM-gegevens schijf** | De afzonderlijke schijf grootte kan Maxi maal 32 TB en Maxi maal 256 TB gecombineerd voor alle schijven in een VM zijn.

### <a name="azure-vm-backup-options"></a>Azure VM-back-upopties

Dit wordt what's ondersteund als u back-ups wilt maken van virtuele Azure-machines:

**Machine** | **Waarvan wordt een back-up gemaakt?** | **Locatie** | **Functies**
--- | --- | --- | ---
**Back-ups van Azure VM met behulp van VM-extensie** | Volledige VM | Maak een back-up naar de kluis. | De uitbrei ding wordt geïnstalleerd wanneer u back-up voor een virtuele machine inschakelt.<br/><br/> Eenmaal per dag een back-up maken.<br/><br/> App-bewuste back-up voor Windows-Vm's; bestands consistente back-up voor Linux Vm's. U kunt app-consistentie voor Linux-machines configureren met aangepaste scripts.<br/><br/> Herstel de VM of schijf.<br/><br/> Kan geen back-up maken van een virtuele machine van Azure naar een on-premises locatie.
**Back-ups van Azure-VM'S met behulp van MARS-agent** | Bestanden, mappen, systeemstatus | Maak een back-up naar de kluis. | Drie keer per dag een back-up maken.<br/><br/> Als u een back-up wilt maken van specifieke bestanden of mappen in plaats van de hele virtuele machine, kan de MARS-agent worden uitgevoerd naast de VM-extensie.
**Azure VM met DPM** | Bestanden, mappen, volumes, systeem status, app-gegevens | Maak een back-up naar de lokale opslag van de virtuele machine van Azure waarop DPM wordt uitgevoerd. DPM voert vervolgens een back-up uit naar de kluis. | App-bewuste moment opnamen.<br/><br/> Volledige granulariteit voor back-up en herstel.<br/><br/> Linux wordt ondersteund voor virtuele machines (Hyper-V-/VMware).<br/><br/> Oracle wordt niet ondersteund.
**Azure VM met MABS** | Bestanden, mappen, volumes, systeem status, app-gegevens | Maak een back-up naar de lokale opslag van de virtuele machine van Azure waarop MABS wordt uitgevoerd. MABS voert vervolgens een back-up uit naar de kluis. | App-bewuste moment opnamen.<br/><br/> Volledige granulariteit voor back-up en herstel.<br/><br/> Linux wordt ondersteund voor virtuele machines (Hyper-V-/VMware).<br/><br/> Oracle wordt niet ondersteund.

## <a name="linux-backup-support"></a>Ondersteuning voor Linux-back-ups

Dit wordt what's ondersteund als u een back-up wilt maken van Linux-machines:

**Back-uptype** | **Linux (goedgekeurd door Azure)**
--- | ---
**Directe back-up van on-premises machine waarop Linux wordt uitgevoerd** | Wordt niet ondersteund. De MARS-agent kan alleen worden geïnstalleerd op Windows-computers.
**Agent-extensie gebruiken om een back-up te maken van Azure VM waarop Linux wordt uitgevoerd** | App-consistente back-up met [aangepaste scripts](backup-azure-linux-app-consistent.md).<br/><br/> Herstel op bestandsniveau.<br/><br/> Herstellen door het maken van een VM vanaf een herstelpunt of schijf.
**DPM gebruiken om back-ups te maken van on-premises machines waarop Linux wordt uitgevoerd** | Bestands consistente back-up van Linux-gast-Vm's op Hyper-V en VMWare.<br/><br/> VM-herstel van Hyper-V-en VMWare Linux-gast-Vm's.
**MABS gebruiken om back-ups te maken van on-premises machines waarop Linux wordt uitgevoerd** | Bestands consistente back-up van Linux-gast-Vm's op Hyper-V en VMWare.<br/><br/> VM-herstel van Hyper-V-en VMWare Linux-gast-Vm's.
**MABS of DPM gebruiken om een back-up te maken van virtuele Linux Azure-machines** | Wordt niet ondersteund.

## <a name="daylight-saving-time-support"></a>Ondersteuning voor zomer tijd

Azure Backup biedt geen ondersteuning voor automatische klok aanpassing voor zomer-en winter tijd voor back-ups van Azure-VM'S. Het uur van de back-up wordt niet voorwaarts of achterwaarts geschoven. Om ervoor te zorgen dat de back-up op de gewenste tijd wordt uitgevoerd, wijzigt u het back-upbeleid als vereist hand matig.

## <a name="disk-deduplication-support"></a>Ondersteuning voor schijf ontdubbeling

Ondersteuning voor schijfontdubbeling is als volgt:

- Schijf ontdubbeling wordt on-premises ondersteund wanneer u DPM of MABs gebruikt om een back-up te maken van Hyper-V-Vm's waarop Windows wordt uitgevoerd. Windows Server voert gegevensontdubbeling (op het niveau van de host) uit op virtuele harde schijven (Vhd's) die als back-upopslag aan de VM zijn gekoppeld.
- Ontdubbeling wordt niet ondersteund in Azure voor Backup-onderdelen. Wanneer DPM en MABS in azure zijn geïmplementeerd, kunnen de opslag schijven die aan de virtuele machine zijn gekoppeld, niet worden ontdubbeld.

## <a name="security-and-encryption-support"></a>Ondersteuning voor beveiliging en versleuteling

Azure Backup ondersteunt versleuteling voor in-transit en op rest-gegevens.

### <a name="network-traffic-to-azure"></a>Netwerk verkeer naar Azure

- Back-upverkeer van servers naar de Recovery Services-kluis wordt versleuteld met behulp van Advanced Encryption Standard 256.
- Back-upgegevens worden verzonden via een beveiligde HTTPS-koppeling.
- Back-upgegevens worden in een versleutelde vorm opgeslagen in de Recovery Services kluis.
- Alleen u beschikt over de wachtwoordzin waarmee deze gegevens kunnen worden ontgrendeld. De back-upgegevens kunnen niet door Microsoft ontsleuteld.

    > [!WARNING]
    > Nadat u de kluis hebt ingesteld, hebt alleen u toegang tot de versleutelingssleutel. Microsoft bewaart nooit een kopie en heeft geen toegang tot de sleutel. Als de sleutel verkeerd wordt geplaatst, kan Microsoft de back-upgegevens niet herstellen.

### <a name="data-security"></a>Gegevensbeveiliging

- Wanneer u een back-up maakt van virtuele Azure-machines, moet u de versleuteling instellen *in* de Virtual Machine.
- Azure Backup biedt ondersteuning voor Azure Disk Encryption, dat gebruikmaakt van BitLocker op virtuele Windows-machines en **dm-crypt** op virtuele Linux-machines.
- Op de back-end gebruikt Azure Backup [Azure Storage-service versleuteling](../storage/common/storage-service-encryption.md), waarmee gegevens in rust worden beschermd.

**Machine** | **In-transit** | **Inactief**
--- | --- | ---
**On-premises Windows-computers zonder DPM-MABS** | ![Ja][green] | ![Ja][green]
**Azure VM's** | ![Ja][green] | ![Ja][green]
**On-premises Windows-machines of Azure-Vm's met DPM** | ![Ja][green] | ![Ja][green]
**On-premises Windows-machines of Azure-Vm's met MABS** | ![Ja][green] | ![Ja][green]

## <a name="compression-support"></a>Ondersteuning voor compressie

Backup ondersteunt de compressie van het back-upverkeer, zoals wordt beschreven in de volgende tabel.

- Voor virtuele Azure-machines leest de VM-extensie de gegevens rechtstreeks vanuit het Azure Storage-account via het opslag netwerk. Dit is dus niet nodig om dit verkeer te comprimeren.
- Als u DPM of MABS gebruikt, kunt u band breedte besparen door de gegevens te comprimeren voordat er een back-up van wordt gemaakt.

**Machine** | **Comprimeren naar MABS/DPM (TCP)** | **Comprimeren naar kluis (HTTPS)**
--- | --- | ---
**Directe back-ups van on-premises Windows-computers** | N.v.t. | ![Ja][green]
**Back-ups van virtuele Azure-machines maken met behulp van VM-extensie** | N.v.t. | N.v.t.
**Back-ups op on-premises/Azure-computers met behulp van MABS/DPM** | ![Ja][green] | ![Ja][green]

## <a name="retention-limits"></a>Bewaarlimieten

**Instelling** | **Limieten**
--- | ---
**Maximum aantal herstel punten per beveiligd exemplaar (computer of werk belasting)** | 9\.999
**Maximale verloop tijd voor een herstel punt** | Geen limiet
**Maximale back-upfrequentie naar DPM/MABS** | Om de 15 minuten voor SQL Server<br/><br/> Eenmaal per uur voor andere workloads
**Maximale back-upfrequentie naar kluis** | **On-premises Windows-machines of Azure vm's met Mars:** Drie per dag<br/><br/> **DPM-MABS:** Twee per dag<br/><br/> **Azure VM-back-up:** Eén per dag
**Bewaar periode van het herstel punt** | Dagelijks, wekelijks, maandelijks, jaarlijks
**Maximale Bewaar periode** | Afhankelijk van back-upfrequentie
**Herstel punten op DPM/MABS-schijf** | 64 voor bestands servers; 448 voor app-servers <br/><br/>Onbeperkte tape herstel punten voor on-premises DPM

## <a name="cross-region-restore"></a>Meerdere regio's herstellen

Azure Backup de functie voor het terugzetten van meerdere regio's heeft toegevoegd om de beschik baarheid en tolerantie van gegevens te versterken, waardoor klanten volledig beheer hebben over het herstellen van gegevens naar een secundaire regio. Als u deze functie wilt configureren, gaat u naar [het artikel set cross Region Restore.](backup-create-rs-vault.md#set-cross-region-restore). Deze functie wordt ondersteund voor de volgende beheer typen:

| Type back-upbeheer | Ondersteund                                                    | Ondersteunde regio's |
| ---------------------- | ------------------------------------------------------------ | ----------------- |
| Azure VM               | Ja. Open bare beperkte preview ondersteund voor versleutelde Vm's en Vm's met minder dan 4 TB schijven | VS - west-centraal   |
| MARS-agent/on-premises | Nee                                                           | N.v.t.               |
| SQL-/SAP HANA          | Nee                                                           | N.v.t.               |
| AFS                    | Nee                                                           | N.v.t.               |

## <a name="next-steps"></a>Volgende stappen

- [Raadpleeg de ondersteunings matrix](backup-support-matrix-iaas.md) voor Azure VM-back-up.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
