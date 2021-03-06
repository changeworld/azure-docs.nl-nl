---
title: Zelf studie-configuratie van virtuele Linux-machines beheren in azure
description: In deze zelf studie leert u hoe u wijzigingen kunt identificeren en pakket updates kunt beheren op een virtuele Linux-machine
services: virtual-machines-linux
documentationcenter: virtual-machines
author: mgoedtel
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/27/2019
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 5dd35ed2f4533d19cc3a59788d6422416dce13f5
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79239292"
---
# <a name="tutorial-monitor-changes-and-update-a-linux-virtual-machine-in-azure"></a>Zelf studie: wijzigingen bewaken en een virtuele Linux-machine bijwerken in azure

Met Azure [Wijzigingen bijhouden](../../automation/change-tracking.md) kunt u eenvoudig wijzigingen identificeren en [updatebeheer](../../automation/automation-update-management.md) kunt u updates van het besturings systeem beheren voor uw virtuele machines met Azure Linux.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Windows-updates beheren
> * Wijzigingen en inventaris bewaken

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell starten

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u Azure CLI 2.0.30 of hoger gebruiken voor deze zelfstudie. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="create-vm"></a>VM maken

Om diagnostische gegevens en metrische gegevens in actie te zien, hebt u een virtuele machine nodig. Maak eerst een resourcegroep met [az group create](/cli/azure/group#az-group-create). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroupMonitor* gemaakt op de locatie *VS Oost*.

```azurecli-interactive
az group create --name myResourceGroupMonitor --location eastus
```

Maak een virtuele machine met [az vm create](/cli/azure/vm#az-vm-create). In het volgende voorbeeld wordt een VM gemaakt met de naam *myVM* en worden er in *~/.ssh/* SSH-sleutels gemaakt als deze nog niet bestaan:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="manage-software-updates"></a>Software-updates beheren

Met Updatebeheer kunt u updates en patches beheren voor een Azure Linux-VM.
U kunt rechtstreeks vanuit uw VM de status van de beschikbare updates beoordelen, de installatie van vereiste updates plannen en de implementatieresultaten bekijken om te controleren of updates correct zijn toegepast op de VM.

Zie [Automation-prijzen voor updatebeheer](https://azure.microsoft.com/pricing/details/automation/) voor prijsinformatie.

### <a name="enable-update-management"></a>Updatebeheer inschakelen

Updatebeheer inschakelen voor de VM:

1. Selecteer aan de linkerkant van het scherm **Virtuele machines**.
2. Selecteer een VM in de lijst.
3. Selecteer in de sectie **Bewerkingen** van het VM-scherm de optie **Updatebeheer**. Het scherm **Updatebeheer inschakelen** wordt weergegeven.

Er wordt een validatie uitgevoerd om te bepalen of updatebeheer is ingeschakeld voor deze virtuele machine.
De validatie bevat controles voor een Log Analytics-werkruimte en het gekoppelde Automation-account en controleert of de oplossing zich in de werkruimte bevindt.

Een [Log Analytics](../../log-analytics/log-analytics-overview.md)-werkruimte wordt gebruikt om gegevens te verzamelen die worden gegenereerd door functies en services zoals Updatebeheer.
De werkruimte biedt één locatie om gegevens uit meerdere bronnen te bekijken en te analyseren.
Als u aanvullende bewerkingen wilt uitvoeren op virtuele machines die updates vereisen, biedt Azure Automation de mogelijkheid runbooks uit te voeren met VM's, zoals updates downloaden en toepassen.

Tijdens het validatieproces wordt ook gecontroleerd of de virtuele machine is ingericht met de Log Analytics-agent en Automation Hybrid Runbook Worker. Deze agent wordt gebruikt om te communiceren met de VM en om informatie op te vragen over de status van de update.

Kies de Log Analytics-werk ruimte en het Automation-account en selecteer **inschakelen** om de oplossing in te scha kelen. Het duurt maximaal 15 minuten om de oplossing in te schakelen.

Als een van de volgende vereiste onderdelen ontbreekt na de onboarding, wordt dit automatisch toegevoegd:

* [Log Analytics](../../log-analytics/log-analytics-overview.md)-werkruimte
* [Automation-account](../../automation/automation-offering-get-started.md)
* Een [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md) wordt ingeschakeld op de virtuele machine.

Het scherm **Updatebeheer** wordt geopend. Configureer de locatie, Log Analytics werk ruimte en het Automation-account dat moet worden gebruikt en selecteer **inschakelen**. Als de velden lichtgrijs zijn, betekent dit dat een andere automatiseringsoplossing is ingeschakeld voor de VM en dat dezelfde werkruimte en hetzelfde Automation-account moeten worden gebruikt.

![Oplossing voor updatebeheer inschakelen](./media/tutorial-monitoring/manage-updates-update-enable.png)

Het inschakelen van de oplossing kan maximaal 15 minuten duren. Gedurende deze tijd mag u het browservenster niet sluiten. Nadat de oplossing is ingeschakeld, wordt informatie over ontbrekende updates op de VM naar Azure Monitor-logboeken verzonden. Het duurt tussen 30 minuten en 6 uur voordat de gegevens beschikbaar zijn voor analyse.

### <a name="view-update-assessment"></a>Update-evaluatie bekijken

Na **Updatebeheer** is ingeschakeld, wordt het scherm **Updatebeheer** weergegeven. Nadat de evaluatie van updates is voltooid, ziet u een lijst met ontbrekende updates op het tabblad **Ontbrekende updates**.

 ![Updatestatus bekijken](./media/tutorial-monitoring/manage-updates-view-status-linux.png)

### <a name="schedule-an-update-deployment"></a>Een update-implementatie plannen

Als u updates wilt installeren, plant u een implementatie na uw release-planning en servicevenster. U kunt kiezen welke typen updates moeten worden opgenomen in de implementatie. Zo kunt u belangrijke updates of beveiligingsupdates opnemen en updatepakketten uitsluiten.

Plan een nieuwe update-implementatie voor de VM door te klikken op **Update-implementatie plannen** boven aan het scherm **Updatebeheer**. Geef de volgende gegevens op in het scherm **Nieuwe update-implementatie**:

Selecteer **Update-implementatie plannen**om een nieuwe update-implementatie te maken. De pagina **nieuwe update-implementatie** wordt geopend. Voer waarden in voor de eigenschappen die in de volgende tabel worden beschreven en klik vervolgens op **maken**:

| Eigenschap | Beschrijving |
| --- | --- |
| Naam |Unieke naam voor het identificeren van de update-implementatie. |
|Besturingssysteem| Linux of Windows|
| Bij te werken groepen |Voor Azure-machines definieert u een query op basis van een combi natie van abonnement, resource groepen, locaties en tags om een dynamische groep virtuele Azure-machines samen te stellen die in uw implementatie moeten worden meegenomen. </br></br>Voor niet-Azure-machines selecteert u een bestaande opgeslagen zoek opdracht om een groep van niet-Azure-machines te selecteren die u in de implementatie wilt gebruiken. </br></br>Zie [Dynamische groepen](../../automation/automation-update-management-groups.md) voor meer informatie|
| Machines die moeten worden bijgewerkt |selecteer een opgeslagen zoekopdracht of geïmporteerde groep, of kies Computer in de vervolgkeuzelijst en selecteer de afzonderlijke computers. Als u **Computers** selecteert, wordt de gereedheid van de computer weergegeven in de kolom **GEREEDHEID VOOR UPDATE-AGENT**.</br> Zie [Computergroepen in Azure Monitorlogboeken](../../azure-monitor/platform/computer-groups.md) voor meer informatie over de verschillende manieren waarop u computergroepen kunt maken in Azure Monitor-logboeken |
|Update classificaties|Selecteer alle update classificaties die u nodig hebt|
|Updates opnemen/uitsluiten|Hiermee opent u de pagina **opnemen/uitsluiten** . Updates die moeten worden opgenomen of uitgesloten, worden op afzonderlijke tabbladen weergegeven. Zie [een update-implementatie plannen](../../automation/automation-tutorial-update-management.md#schedule-an-update-deployment) voor meer informatie over het verwerken van de opname. |
|Schema-instellingen|Selecteer het tijdstip waarop u wilt beginnen en selecteer een of meer keren of terugkerend voor het terugkeer patroon|
| Pre-scripts en post scripts|De scripts selecteren die voor en na de implementatie moeten worden uitgevoerd|
| Onderhouds venster |Aantal minuten dat is ingesteld voor updates. De waarde mag niet minder dan 30 minuten en Maxi maal 6 uur zijn |
| Besturings element opnieuw opstarten| Hiermee wordt bepaald hoe opnieuw opstarten moet worden afgehandeld. De volgende opties zijn beschikbaar:</br>Opnieuw opstarten indien nodig (standaard)</br>Altijd opnieuw opstarten</br>Nooit opnieuw opstarten</br>Alleen opnieuw opstarten - updates worden niet geïnstalleerd|

Update-implementaties kunnen ook programmatisch worden gemaakt. Zie [Software-update configuraties-maken](/rest/api/automation/softwareupdateconfigurations/create)voor meer informatie over het maken van een update-implementatie met behulp van de rest API. Er is ook een voor beeld van een runbook dat kan worden gebruikt voor het maken van een wekelijkse update-implementatie. Zie [een wekelijkse update-implementatie maken voor een of meer virtuele machines in een resource groep](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1)voor meer informatie over dit runbook.

Nadat u klaar bent met het configureren van de planning, klikt u op de knop **Maken** en gaat u terug naar het statusdashboard.
U ziet dat de tabel **Gepland** de implementatieplanning weergeeft die u hebt gemaakt.

### <a name="view-results-of-an-update-deployment"></a>Resultaten van een update-implementatie weergeven

Nadat de geplande implementatie is gestart, ziet u de status van deze implementatie op het tabblad **Update-implementaties** in het scherm **Updatebeheer**.
Als de implementatie momenteel wordt uitgevoerd, wordt de status weergegeven als **Wordt uitgevoerd**. Nadat deze is voltooid, verandert de status in **Geslaagd**.
Als er een fout is opgetreden met één of meer updates in de implementatie, wordt de status **Gedeeltelijk mislukt**.
Selecteer de voltooide update-implementatie om het dashboard voor de betreffende update-implementatie te bekijken.

![Statusdashboard voor update-implementatie voor specifieke implementatie](./media/tutorial-monitoring/manage-updates-view-results.png)

Op de tegel **Updateresultaten** ziet u een overzicht van het totale aantal updates en van de implementatieresultaten op de virtuele machine.
In de tabel aan de rechterkant vindt u gedetailleerde informatie over elke update en het resultaat van de installatie. Een van de volgende waarden wordt hier weergegeven:

* **Niet geprobeerd**: de update is niet geïnstalleerd omdat er onvoldoende tijd beschikbaar was op basis van de opgegeven onderhoudsperiode.
* **Geslaagd**: de update is voltooid.
* **Mislukt**: de update is mislukt.

Selecteer **Alle logboeken** als u alle logboekvermeldingen wilt zien die tijdens de implementatie zijn gemaakt.

Selecteer de tegel **Uitvoer** als u de taakstroom wilt bekijken van het runbook dat verantwoordelijk is voor het beheer van de implementatie van de updates op de doel-VM.

Selecteer **Fouten** voor gedetailleerde informatie over fouten die zijn opgetreden tijdens de implementatie.

## <a name="monitor-changes-and-inventory"></a>Wijzigingen en inventaris bewaken

U kunt inventarisgegevens verzamelen en weergeven voor software, bestanden, Linux-daemons, Windows-services en Windows-registersleutels op uw computers. Door de configuraties van uw computer bij te houden, is het voor u gemakkelijker vast te stellen waar in uw omgeving zich operationele problemen voordoen, en kunt u beter beoordelen wat de status van uw computers is.

### <a name="enable-change-and-inventory-management"></a>Wijzigings- en inventarisbeheer inschakelen

Wijzigings- en inventarisbeheer inschakelen voor de VM:

1. Selecteer aan de linkerkant van het scherm **Virtuele machines**.
2. Selecteer een VM in de lijst.
3. Selecteer in de sectie **Bewerkingen** van het VM-scherm de optie **Inventaris** of **Wijzigingen bijhouden**. Het scherm **Wijzigingen bijhouden en inventaris inschakelen** wordt geopend.

Configureer de locatie, Log Analytics werk ruimte en het Automation-account dat moet worden gebruikt en selecteer **inschakelen**. Als de velden lichtgrijs zijn, betekent dit dat een andere automatiseringsoplossing is ingeschakeld voor de VM en dat dezelfde werkruimte en hetzelfde Automation-account moeten worden gebruikt. Hoewel de oplossingen afzonderlijk worden weergegeven in het menu, is het dezelfde oplossing. Als u er één inschakelt, worden beide ingeschakeld voor de VM.

![Bijhouden van wijzigingen en inventaris inschakelen](./media/tutorial-monitoring/manage-inventory-enable.png)

Nadat de oplossing is ingeschakeld, kan het enige tijd duren voordat de gegevens zichtbaar zijn terwijl de inventaris wordt verzameld op de VM.

### <a name="track-changes"></a>Wijzigingen bijhouden

Selecteer op de VM de optie **Wijzigingen bijhouden** onder **BEWERKINGEN**. Selecteer **Instellingen bewerken**. De pagina **Wijzigingen bijhouden** wordt weergegeven. Selecteer het type instelling dat u wilt bijhouden en selecteer **+ Toevoegen** om de instellingen te configureren. De beschikbare optie voor Linux is **Linux-bestanden**

Zie [Problemen met wijzigingen op een VM oplossen](../../automation/automation-tutorial-troubleshoot-changes.md) voor gedetailleerde informatie over Wijzigingen bijhouden

### <a name="view-inventory"></a>Inventaris weergeven

Selecteer op de VM de optie **Inventaris** onder **BEWERKINGEN**. Op het tabblad **Software** wordt een lijst weergegeven met de software die is gevonden. De algemene details voor elke softwarerecord worden in de tabel weergegeven. Deze details omvatten de naam, versie, uitgever en laatste tijdstip van vernieuwen voor de software.

![Inventaris weergeven](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>Activiteitenlogboeken en wijzigingen bewaken

Selecteer **Verbinding met het activiteitenlogboek beheren** op de pagina **Wijzigingen bijhouden** op de virtuele machine. Hiermee opent u de pagina **Azure-activiteitenlogboek**. Selecteer **Verbinden** om Wijzigingen bijhouden te verbinden met het Azure-activiteitenlogboek voor uw virtuele machine.

Terwijl deze instelling is ingeschakeld, gaat u naar de pagina **Overzicht** voor de virtuele machine en selecteert u **Stoppen** om de virtuele machine te stoppen. Wanneer u daarom wordt gevraagd, selecteert u **Ja** om de virtuele machine te stoppen. Wanneer deze toewijzing ongedaan is gemaakt, selecteert u **Starten** om de virtuele machine opnieuw op te starten.

Wanneer een virtuele machine wordt gestart en gestopt, wordt een gebeurtenis geregistreerd in het activiteitenlogboek. Ga terug naar de pagina **Wijzigingen bijhouden**. Selecteer het tabblad **Gebeurtenissen** onderaan op de pagina. Na een tijdje worden de gebeurtenissen weergegeven in de grafiek en in de tabel. Elke gebeurtenis kan worden geselecteerd om gedetailleerde informatie over de gebeurtenis weer te geven.

![Wijzigingen in het activiteitenlogboek weergeven](./media/tutorial-monitoring/manage-activitylog-view-results.png)

De grafiek toont wijzigingen die in de loop der tijd hebben plaatsgevonden. Nadat u een verbinding met een Azure-activiteitenlogboek hebt toegevoegd, toont de lijngrafiek bovenaan gebeurtenissen uit het Azure-actitviteitenlogboek. Elke rij in het staafdiagram vertegenwoordigt een ander type wijziging dat kan worden gevolgd. Deze typen zijn daemons, bestanden en software van Linux. Het tabblad met wijzigingen bevat details over de wijzigingen in de visualisatie. Deze worden getoond in aflopende volgorde vanaf het moment waarop de wijziging optrad (meest recente eerst).

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u Wijzigingen bijhouden en Updatebeheer voor uw virtuele machine geconfigureerd en gecontroleerd. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een resourcegroep en VM maken
> * Linux-updates beheren
> * Wijzigingen en inventaris bewaken

Ga naar de volgende zelf studie voor meer informatie over het bewaken van uw VM.

> [!div class="nextstepaction"]
> [Virtuele machines bewaken](tutorial-monitor.md)
