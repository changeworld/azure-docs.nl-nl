---
title: Bestanden in Azure Cloud Shell persistent maken | Microsoft Docs
description: Overzicht van de wijze waarop Azure Cloud Shell bestanden persistent wilt maken.
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/24/2020
ms.author: damaerte
ms.openlocfilehash: 15a5770eb2964f0f2039fe93de904af65d4c81ed
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79252101"
---
# <a name="persist-files-in-azure-cloud-shell"></a>Bestanden in Azure Cloud Shell persistent maken
Cloud Shell maakt gebruik van Azure File Storage om bestanden in verschillende sessies op te slaan. Bij de eerste keer starten Cloud Shell u gevraagd om een nieuwe of bestaande bestands share te koppelen om bestanden in verschillende sessies te behouden.

> [!NOTE]
> Bash en Power shell delen dezelfde bestands share. Er kan slechts één bestands share worden gekoppeld aan automatische montage in Cloud Shell.

> [!NOTE]
> Azure Storage-firewall wordt niet ondersteund voor Cloud shell-opslag accounts.

## <a name="create-new-storage"></a>Nieuwe opslag maken

Wanneer u basis instellingen gebruikt en alleen een abonnement selecteert, maakt Cloud Shell drie resources namens u in de ondersteunde regio die het dichtst bij u ligt:
* Resourcegroep: `cloud-shell-storage-<region>`
* Opslag account: `cs<uniqueGuid>`
* Bestands share: `cs-<user>-<domain>-com-<uniqueGuid>`

![De abonnements instelling](media/persisting-shell-storage/basic-storage.png)

De bestands share koppelt als `clouddrive` in uw `$Home` map. Dit is een eenmalige actie, waarna de bestands share automatisch wordt gekoppeld in volgende sessies. 

De bestands share bevat ook een installatie kopie van 5 GB die voor u is gemaakt en die automatisch gegevens persistent maakt in uw `$Home` Directory. Dit geldt voor zowel bash als Power shell.

## <a name="use-existing-resources"></a>Bestaande resources gebruiken

U kunt bestaande resources koppelen met behulp van de geavanceerde optie. Wanneer u een Cloud Shell regio selecteert, moet u een opslag account voor back-ups in dezelfde regio selecteren. Als uw toegewezen regio bijvoorbeeld VS-West is, moet u een bestands share koppelen die zich ook in de Verenigde Staten bevindt.

Wanneer de prompt voor het instellen van de opslag wordt weer gegeven, selecteert u **Geavanceerde instellingen weer** geven om extra opties weer te geven. Het gevulde opslag opties filter voor lokaal redundante opslag (LRS), geo-redundante opslag (GRS) en ZRS-accounts (zone-redundante opslag). 

> [!NOTE]
> Het gebruik van GRS-of ZRS-opslag accounts wordt aanbevolen voor aanvullende tolerantie voor de back-upbestands share. Welk type redundantie is afhankelijk van uw doel stellingen en prijs voorkeur. Meer [informatie over replicatie opties voor Azure Storage accounts](https://docs.microsoft.com/azure/storage/common/storage-redundancy).

![De instelling van de resource groep](media/persisting-shell-storage/advanced-storage.png)

## <a name="securing-storage-access"></a>Toegang tot opslag beveiligen
Voor beveiliging moet elke gebruiker hun eigen opslag account inrichten.  Voor op rollen gebaseerd toegangs beheer (RBAC) moeten gebruikers Inzender toegang of hoger hebben op het niveau van het opslag account.

Cloud Shell gebruikt een Azure-bestands share in een opslag account binnen een opgegeven abonnement. Als gevolg van overgenomen machtigingen, hebben gebruikers met voldoende toegangs rechten voor het abonnement toegang tot alle opslag accounts en bestands shares in het abonnement.

Gebruikers moeten de toegang tot hun bestanden vergren delen door de machtigingen in te stellen op het opslag account of het abonnements niveau.

## <a name="supported-storage-regions"></a>Ondersteunde opslag regio's
Als u uw huidige regio wilt zoeken, kunt u `env` uitvoeren in bash en de variabele `ACC_LOCATION`vinden, of vanuit Power shell `$env:ACC_LOCATION`uitvoeren. Bestands shares ontvangen een installatie kopie van 5 GB die voor u is gemaakt om uw `$Home` Directory te behouden.

Cloud Shell machines bestaan in de volgende regio's:

|Onderwerp|Regio|
|---|---|
|Noord- en Zuid-Amerika|VS-Oost, Zuid-Centraal VS, VS-West|
|Europa|Europa - noord, Europa - west|
|Azië en Stille Oceaan|India, midden, Zuidoost-Azië|

Klanten moeten een primaire regio kiezen, tenzij ze een vereiste hebben dat hun gegevens in rust worden opgeslagen in een bepaalde regio. Als ze een dergelijke vereiste hebben, moet er een secundaire opslag regio worden gebruikt.

### <a name="secondary-storage-regions"></a>Secundaire opslag regio's
Als er een secundaire opslag regio wordt gebruikt, bevindt het gekoppelde Azure Storage-account zich in een andere regio als de Cloud Shell machine waarnaar u ze koppelt. Jeroen kan het opslag account bijvoorbeeld zodanig instellen dat het zich bevindt in Canada-oost, een secundaire regio, maar de machine waaraan deze is gekoppeld, bevindt zich nog in een primaire regio. Haar data-at-rest bevindt zich in Canada, maar wordt verwerkt in de Verenigde Staten.

> [!NOTE]
> Als er een secundaire regio wordt gebruikt, zijn de toegang tot bestanden en opstart tijd voor Cloud Shell mogelijk langzamer.

Een gebruiker kan `(Get-CloudDrive | Get-AzStorageAccount).Location` uitvoeren in Power shell om de locatie van de bestands share te zien.

## <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Het maken van resources beperken met een Azure-resource beleid
Opslag accounts die u in Cloud Shell maakt, worden gelabeld met `ms-resource-usage:azure-cloud-shell`. Als u wilt voor komen dat gebruikers opslag accounts maken in Cloud Shell, maakt u een [Azure-resource beleid voor Tags](../azure-policy/json-samples.md) die worden geactiveerd door deze specifieke tag.

## <a name="how-cloud-shell-storage-works"></a>Hoe Cloud Shell Storage werkt 
Cloud Shell bestanden persistent maken via de volgende methoden: 
* Er wordt een schijf kopie van uw `$Home` Directory gemaakt om alle inhoud in de map op te slaan. De schijf installatie kopie wordt opgeslagen in de opgegeven bestands share als `acc_<User>.img` op `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img`en de wijzigingen worden automatisch gesynchroniseerd. 
* Het koppelen van uw opgegeven bestands share als `clouddrive` in uw `$Home` Directory voor directe bestands share interactie. `/Home/<User>/clouddrive` is toegewezen aan `fileshare.storage.windows.net/fileshare`.
 
> [!NOTE]
> Alle bestanden in uw `$Home` Directory, zoals SSH-sleutels, blijven behouden in de schijf installatie kopie van uw gebruiker, die wordt opgeslagen in de gekoppelde bestands share. Pas aanbevolen procedures toe wanneer u gegevens in uw `$Home` Directory en gekoppelde bestands share persistent maken.

## <a name="clouddrive-commands"></a>clouddrive-opdrachten

### <a name="use-the-clouddrive-command"></a>De `clouddrive` opdracht gebruiken
In Cloud Shell kunt u een opdracht uitvoeren met de naam `clouddrive`, waarmee u de bestands share die is gekoppeld aan Cloud Shell hand matig kan bijwerken.
![de opdracht ' clouddrive ' uit te voeren](media/persisting-shell-storage/clouddrive-h.png)

### <a name="list-clouddrive"></a>`clouddrive` weer geven
Als u wilt weten welke bestands share is gekoppeld als `clouddrive`, voert u de `df` opdracht uit. 

Het bestandspad naar clouddrive toont de naam van uw opslag account en de bestands share in de URL. Bijvoorbeeld: `//storageaccountname.file.core.windows.net/filesharename`

```
justin@Azure:~$ df
Filesystem                                          1K-blocks   Used  Available Use% Mounted on
overlay                                             29711408 5577940   24117084  19% /
tmpfs                                                 986716       0     986716   0% /dev
tmpfs                                                 986716       0     986716   0% /sys/fs/cgroup
/dev/sda1                                           29711408 5577940   24117084  19% /etc/hosts
shm                                                    65536       0      65536   0% /dev/shm
//mystoragename.file.core.windows.net/fileshareName 5368709120    64 5368709056   1% /home/justin/clouddrive
justin@Azure:~$
```

### <a name="mount-a-new-clouddrive"></a>Een nieuwe clouddrive koppelen

#### <a name="prerequisites-for-manual-mounting"></a>Vereisten voor hand matig koppelen
U kunt de bestands share die is gekoppeld aan Cloud Shell bijwerken met behulp van de `clouddrive mount` opdracht.

Als u een bestaande bestands share koppelt, moeten de opslag accounts zich in de geselecteerde Cloud Shell regio bevinden. Haal de locatie op door `env` uit te voeren en de `ACC_LOCATION`te controleren.

#### <a name="the-clouddrive-mount-command"></a>De `clouddrive mount` opdracht

> [!NOTE]
> Als u een nieuwe bestands share koppelt, wordt er een nieuwe gebruikers installatie kopie gemaakt voor uw `$Home` Directory. Uw vorige `$Home`-installatie kopie wordt opgeslagen in de vorige bestands share.

Voer de `clouddrive mount` opdracht uit met de volgende para meters:

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

Als u meer informatie wilt weer geven, voert u `clouddrive mount -h`uit, zoals hier wordt weer gegeven:

![De clouddrive-mount'command uitvoeren](media/persisting-shell-storage/mount-h.png)

### <a name="unmount-clouddrive"></a>Clouddrive ontkoppelen
U kunt een bestands share die is gekoppeld aan Cloud Shell, op elk gewenst moment ontkoppelen. Omdat Cloud Shell een gekoppelde bestands share moet worden gebruikt, wordt u gevraagd een andere bestands share te maken en koppelen tijdens de volgende sessie.

1. Voer `clouddrive unmount` uit.
2. Bevestigings-en bevestigings prompts.

De bestands share blijft bestaan, tenzij u deze hand matig verwijdert. Cloud Shell kunt niet meer op volgende sessies naar deze bestands share zoeken. Als u meer informatie wilt weer geven, voert u `clouddrive unmount -h`uit, zoals hier wordt weer gegeven:

![De clouddrive-unmount'command uitvoeren](media/persisting-shell-storage/unmount-h.png)

> [!WARNING]
> Hoewel met deze opdracht geen resources worden verwijderd, hand matig verwijderen van een resource groep, opslag account of bestands share die is toegewezen aan Cloud Shell, worden uw `$Home` Directory-schijf kopie en alle bestanden in de bestands share gewist. Deze actie kan niet ongedaan worden gemaakt.
## <a name="powershell-specific-commands"></a>Power shell-specifieke opdrachten

### <a name="list-clouddrive-azure-file-shares"></a>`clouddrive` Azure-bestands shares weer geven
Met de cmdlet `Get-CloudDrive` worden de gegevens van de Azure-bestands share opgehaald die momenteel zijn gekoppeld door de `clouddrive` in de Cloud Shell. <br>
![met Get-CloudDrive](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

### <a name="unmount-clouddrive"></a>`clouddrive` ontkoppelen
U kunt op elk gewenst moment een Azure-bestands share ontkoppelen die aan Cloud Shell is gekoppeld. Als de Azure-bestands share is verwijderd, wordt u gevraagd om een nieuwe Azure-bestands share te maken en te koppelen tijdens de volgende sessie.

Met de cmdlet `Dismount-CloudDrive` wordt een Azure-bestands share ontkoppeld van het huidige opslag account. Als de `clouddrive` wordt ontkoppeld, wordt de huidige sessie beëindigd. De gebruiker wordt gevraagd een nieuwe Azure-bestands share te maken en koppelen tijdens de volgende sessie.
![het ontkoppelen-CloudDrive wordt uitgevoerd](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!INCLUDE [PersistingStorage-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

Opmerking: als u een functie in een bestand moet definiëren en deze aanroept vanuit de Power shell-cmdlets, moet de punt operator worden opgenomen. Bijvoorbeeld:. .\MyFunctions.ps1

## <a name="next-steps"></a>Volgende stappen
[Snelstartgids Cloud Shell](quickstart.md) <br>
[Meer informatie over de opslag van Microsoft Azure-bestanden](https://docs.microsoft.com/azure/storage/storage-introduction) <br>
[Meer informatie over opslag Tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>
