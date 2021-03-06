---
title: Algemene VM-fout codes in azure | Microsoft Docs
description: Meer informatie over enkele veelvoorkomende fout codes die worden aangetroffen bij het inrichten en beheren van virtuele machines in azure
services: virtual-machines
documentationcenter: ''
author: xujing-ms
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure
ms.date: 5/22/2017
ms.author: xujing
ms.openlocfilehash: f5639d1cf94c77d699dc6de9841698b045ac1f96
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76543015"
---
# <a name="understand-common-error-messages-when-you-manage-virtual-machines-in-azure"></a>Algemene fout berichten over het beheren van virtuele machines in azure

In dit artikel worden enkele van de meest voorkomende fout codes en-berichten beschreven die u kunt tegen komen wanneer u virtuele machines (Vm's) in azure maakt of beheert.

>[!NOTE]
> U kunt opmerkingen op deze pagina achterlaten voor feedback of via [Azure feedback](https://feedback.azure.com/forums/216843-virtual-machines) met #azerrormessage-tag.

## <a name="error-response-format"></a>Indeling van fout bericht 
Virtuele Azure-machines gebruiken de volgende JSON-indeling voor fout Reacties:

```json
{
  "status": "status code",
  "error": {
    "code":"Top level error code",
    "message":"Top level error message",
    "details":[
     {
      "code":"Inner level error code",
      "message":"Inner level error message"
     }
    ]
   }
}
```

Een fout bericht bevat altijd een status code en een fout object. Elk fout object bevat altijd een fout code en een bericht. Als de virtuele machine is gemaakt met een sjabloon, bevat het fout object ook een sectie met details die een intern niveau aan fout codes en het bericht bevat. Normaal gesp roken is het meest interne niveau van fout berichten de hoofd fout.


## <a name="common-virtual-machine-management-errors"></a>Veelvoorkomende fouten bij het beheer van virtuele machines

In deze sectie vindt u de algemene fout berichten die u kunt tegen komen bij het beheren van virtuele machines:

|  Foutcode  |  Foutbericht  |  
|  :------| :-------------|  
|  AcquireDiskLeaseFailed  |  Kan geen lease verkrijgen tijdens het maken van de schijf{0}met behulp van een blob met URI-{1}. De BLOB wordt al gebruikt.  |  
|  AllocationFailed  |  Toewijzing is mislukt. Verklein de VM-grootte of het aantal Vm's, probeer het later opnieuw of probeer te implementeren naar een andere Beschikbaarheidsset of een andere Azure-locatie.  |  
|  AllocationFailed  |  De VM-toewijzing is mislukt vanwege een interne fout. Probeer het later opnieuw of probeer de implementatie uit te voeren op een andere locatie.  |
|  ArtifactNotFound  |  De VM-extensie met de Publisher{0}en het type{1}is niet gevonden op de locatie{2}.  |
|  ArtifactNotFound  |  De extensie met de Publisher{0}, het type{1}en de type-handler-versie{2}zijn niet gevonden in de uitbreidings opslagplaats.  |
|  ArtifactVersionNotFound  |  Er is geen versie gevonden in de artefact opslagplaats die voldoet aan de aangevraagde versie{0}.  |
|  ArtifactVersionNotFound  |  Er is geen versie gevonden in de artefact opslagplaats die voldoet aan de aangevraagde versie{0}voor de VM-extensie met de uitgever{1}en het type{2}.  |
|  AttachDiskWhileBeingDetached  |  Kan de gegevens schijf{0}niet koppelen aan de virtuele machine{1}omdat de schijf momenteel wordt ontkoppeld. Wacht tot de schijf volledig is losgekoppeld en probeer het vervolgens opnieuw.  |
|  BadRequest  |  De afgevulde beschikbaarheids sets worden nog niet ondersteund in deze regio.  |
|  BadRequest  |  Het toevoegen van een virtuele machine met beheerde schijven aan een niet-beheerde Beschikbaarheidsset of het toevoegen van een virtuele machine met op blobs gebaseerde schijven naar een beheerde Beschikbaarheidsset wordt niet ondersteund. Maak een Beschikbaarheidsset met de eigenschap Managed om een virtuele machine met Managed disks aan de set toe te voegen.  |
|  BadRequest  |  Managed Disks worden niet ondersteund in deze regio.  |
|  BadRequest  |  Meerdere VMExtensions per handler worden niet ondersteund voor het type{0}van het besturings systeem. VMExtension '{1}' met handler '{2}' is al toegevoegd of opgegeven in de invoer.  |
|  BadRequest  |  De bewerking{0}wordt niet ondersteund voor de resource{1}met Managed disks.  |
|  CertificateImproperlyFormatted  |  De JSON-weer gave van het geheim die is opgehaald uit {0} heeft een gegevens veld dat geen correct opgemaakt PFX-bestand is, of het PFX-bestand wordt niet correct gedecodeerd met het opgegeven wacht woord.  |
|  CertificateImproperlyFormatted  |  De gegevens die zijn opgehaald uit {0}, kunnen niet worden gedeserialiseerd in JSON.  |
|  Conflict  |  Het wijzigen van de grootte van de schijf is alleen toegestaan bij het maken van een VM of wanneer de toewijzing van de virtuele machine ongedaan wordt gemaakt  |
|  ConflictingUserInput  |  Schijf '{0}' kan niet worden gekoppeld omdat de schijf al eigendom is van de VM{1}.  |
|  ConflictingUserInput  |  Bron-en doel resource groepen zijn hetzelfde.  |
|  ConflictingUserInput  |  De bron-en doel opslag accounts voor schijf {0} verschillen.  |
|  ContainerAlreadyOnLease  |  Er is al een lease in de opslag container met de blob met de URI-{0}.  |
|  CrossSubscriptionMoveWithKeyVaultResources  |  De aanvraag voor het verplaatsen van resources bevat de bron-kluis resources waarnaar wordt verwezen door een of meer {0}s in de aanvraag. Dit wordt momenteel niet ondersteund tijdens het verplaatsen van cross-abonnementen. Raadpleeg de fout Details voor de resource-Id's van de sleutel kluis.  |
|  DiagnosticsOperationInternalError  |  Er is een interne fout opgetreden tijdens het verwerken van het diagnostische Profiel van de VM-{0}.  |
|  DiskBlobAlreadyInUseByAnotherDisk  |  BLOB {0} wordt al gebruikt door een andere schijf die deel uitmaakt van VM{1}. U kunt de BLOB-meta gegevens voor de schijf referentie gegevens controleren.  |
|  DiskBlobNotFound  |  Kan de VHD-blob met de URI-{0} niet vinden voor de schijf{1}.  |
|  DiskBlobNotFound  |  Kan de VHD-blob met de URI-{0}niet vinden.  |
|  DiskEncryptionKeySecretMissingTags  |  {0} Secret heeft geen {1}-Tags. Werk de geheime versie bij, voeg de vereiste labels toe en probeer het opnieuw.  |
|  DiskEncryptionKeySecretUnwrapFailed  |  Het teruglopen van de geheime {0} waarde met behulp van de sleutel {1} is mislukt.  |
|  DiskImageNotReady  |  Schijf installatie kopie {0} bevindt zich in {1} status. Probeer het opnieuw wanneer de installatie kopie gereed is.  |
|  DiskPreparationError  |  Er zijn een of meer fouten opgetreden bij het voorbereiden van VM-schijven. Zie weer gave van schijf instantie voor meer informatie.  |
|  DiskProcessingError  |  De schijf verwerking is gestopt omdat de virtuele machine andere schijven heeft dan de schijven waarop de fout is opgetreden.  |
|  ImageBlobNotFound  |  Kan de VHD-blob met de URI-{0} niet vinden voor de schijf{1}.  |
|  ImageBlobNotFound  |  Kan de VHD-blob met de URI-{0}niet vinden.  |
|  IncorrectDiskBlobType  |  Schijf-blobs kunnen alleen van het type pagina-BLOB zijn. De BLOB-{0} voor schijf '{1}' is van het type blok-blob.  |
|  IncorrectDiskBlobType  |  Schijf-blobs kunnen alleen van het type pagina-BLOB zijn. BLOB-{0} is van het type{1}.  |
|  IncorrectImageBlobType  |  Schijf-blobs kunnen alleen van het type pagina-BLOB zijn. De BLOB-{0} voor schijf '{1}' is van het type blok-blob.  |
|  IncorrectImageBlobType  |  Schijf-blobs kunnen alleen van het type pagina-BLOB zijn. BLOB-{0} is van het type{1}.  |
|  InternalOperationError  |  Kan het opslag account niet {0}omzetten. Zorg ervoor dat het is gemaakt via de opslag Resource provider op dezelfde locatie als de reken resource.  |
|  InternalOperationError  |  {0} doel om taken te zoeken is mislukt.  |
|  InternalOperationError  |  Er is een fout opgetreden bij het valideren van het netwerk profiel van de VM{0}.  |
|  InvalidAccountType  |  Het account type {0} is ongeldig.  |
|  InvalidParameter  |  De waarde van de para meter {0} is ongeldig.  |
|  InvalidParameter  |  Het opgegeven beheerders wachtwoord is niet toegestaan.  |
|  InvalidParameter  |  "Het opgegeven wacht woord moet tussen {0}{1} tekens lang zijn en moet voldoen aan ten minste {2} aan vereisten voor wachtwoord complexiteit van het volgende: <ol><li> Bevat een hoofd letter</li><li>Bevat een kleine letter</li><li>Bevat een numeriek cijfer</li><li>Bevat een speciaal teken.</li></ol>  |
|  InvalidParameter  |  De opgegeven gebruikers naam voor de beheerder is niet toegestaan.  |
|  InvalidParameter  |  Kan geen bestaande besturingssysteem schijf koppelen als de virtuele machine is gemaakt op basis van een platform of een gebruikers installatie kopie.  |
|  InvalidParameter  |  De container naam {0} is ongeldig. Container namen moeten 3-63 tekens lang zijn en mogen alleen alfanumerieke tekens met kleine letters en afbreek streepjes bevatten. Het afbreek streepje moet worden voorafgegaan en gevolgd door een alfanumeriek teken.  |
|  InvalidParameter  |  De container naam {0} in URL {1} is ongeldig. Container namen moeten 3-63 tekens lang zijn en mogen alleen alfanumerieke tekens met kleine letters en afbreek streepjes bevatten. Het afbreek streepje moet worden voorafgegaan en gevolgd door een alfanumeriek teken.  |
|  InvalidParameter  |  De BLOB-naam in de URL-{0} bevat een slash. Dit wordt momenteel niet ondersteund voor schijven.  |
|  InvalidParameter  |  De URI-{0} ziet niet de juiste BLOB-URI.  |
|  InvalidParameter  |  Een schijf met de naam{0}maakt al gebruik van dezelfde LUN: {1}.  |
|  InvalidParameter  |  Er bestaat al een schijf met de naam{0}.  |
|  InvalidParameter  |  Kan geen overschrijvingen voor gebruikers afbeeldingen opgeven voor een schijf die al is gedefinieerd in de opgegeven installatie kopie verwijzing.  |
|  InvalidParameter  |  Een schijf met de naam{0}gebruikt al dezelfde URL voor de VHD {1}.  |
|  InvalidParameter  |  Het opgegeven aantal fout domeinen {0} moet in het bereik {1} tot {2}vallen.  |
|  InvalidParameter  |  Het licentie type {0} is ongeldig. Geldige licentie typen zijn: Windows_Client of Windows_Server, hoofdletter gevoelig.  |
|  InvalidParameter  |  De naam van een Linux-host mag niet langer zijn dan {0} tekens of de volgende tekens bevatten: {1}.  |
|  InvalidParameter  |  Het doelpad voor open bare SSH-sleutels is momenteel beperkt tot de standaard waarde {0} vanwege een bekend probleem in de Linux-inrichtings agent.  |
|  InvalidParameter  |  Er bestaat al een schijf op LUN {0}.  |
|  InvalidParameter  |  Abonnements {0} van de aanvraag moeten overeenkomen met het abonnement {1} dat is opgenomen in de id van de beheerde schijf.  |
|  InvalidParameter  |  Aangepaste gegevens in OSProfile moeten een base64-code ring hebben en een maximale lengte van {0} tekens hebben.  |
|  InvalidParameter  |  De BLOB-naam in de URL {0} moet eindigen op de extensie{1}.  |
|  InvalidParameter  |  {0}' is geen geldig voor voegsel van een vastgelegde VHD-BLOB-naam. Een geldig voor voegsel komt overeen met regex{1}.  |
|  InvalidParameter  |  Er kunnen geen certificaten aan uw virtuele machine worden toegevoegd als de VM-agent niet is ingericht.  |
|  InvalidParameter  |  Er bestaat al een schijf op LUN {0}.  |
|  InvalidParameter  |  De virtuele machine kan niet worden gemaakt, omdat de aangevraagde grootte {0} niet beschikbaar is in het cluster met de beschikbaarheidsset die momenteel is toegewezen. De beschik bare grootten zijn: {1}. Meer informatie over de strategie voor het verg Roten/verkleinen van VM'S op https://aka.ms/azure-resizevm.  |
|  InvalidParameter  |  De aangevraagde VM-grootte {0} is niet beschikbaar in de huidige regio. De beschik bare grootten in de huidige regio zijn: {1}. Meer informatie over de beschik bare VM-grootten in elke regio op https://aka.ms/azure-regions.  |
|  InvalidParameter  |  De aangevraagde VM-grootte {0} is niet beschikbaar in de huidige regio. Meer informatie over de beschik bare VM-grootten in elke regio op https://aka.ms/azure-regions.  |
|  InvalidParameter  |  De gebruikers naam van de Windows-beheerder mag niet langer zijn dan {0} tekens lang zijn, eindigen met een punt (.) of de volgende tekens bevatten: {1}.  |
|  InvalidParameter  |  De Windows-computer naam mag niet langer zijn dan {0} tekens, volledig numeriek zijn of de volgende tekens bevatten: {1}.  |
|  MissingMoveDependentResources  |  De aanvraag voor het verplaatsen van resources bevat niet alle afhankelijke resources. Raadpleeg de fout Details voor ontbrekende resource-id's.  |
|  MoveResourcesHaveInvalidState  |  De aanvraag voor het verplaatsen van resources bevat Vm's die zijn gekoppeld aan ongeldige opslag accounts. Controleer de Details voor deze bron-id's en de namen van de opslag accounts waarnaar wordt verwezen.  |
|  MoveResourcesHavePendingOperations  |  De aanvraag voor het verplaatsen van resources bevat resources waarvoor een bewerking in behandeling is. Controleer de Details voor deze resource-id's. Voer de bewerking opnieuw uit nadat de in behandeling zijnde bewerkingen zijn voltooid.  |
|  MoveResourcesNotFound  |  De aanvraag voor het verplaatsen van resources bevat resources die niet kunnen worden gevonden. Controleer de Details voor deze resource-id's.  |
|  NetworkingInternalOperationError  |  Onbekende netwerk toewijzings fout.  |
|  NetworkingInternalOperationError  |  Fout bij onbekende netwerk toewijzing  |
|  NetworkingInternalOperationError  |  Er is een interne fout opgetreden bij het verwerken van het netwerk profiel van de virtuele machine.  |
|  NotFound  |  Kan de Beschikbaarheidsset {0} niet vinden.  |
|  NotFound  |  De virtuele bron machine{0}die in de aanvraag is opgegeven, bestaat niet op deze Azure-locatie.  |
|  NotFound  |  Kan de Tenant met de id {0} niet vinden.  |
|  NotFound  |  Kan de installatie kopie {0} niet vinden.  |
|  NotSupported  |  Het licentie type is {0}, maar de {1} van de afbeeldings-blob is niet van on-premises.  |
|  OperationNotAllowed  |  {0} van de beschikbaarheidsset kan niet worden verwijderd. Voordat u een Beschikbaarheidsset verwijdert, moet u ervoor zorgen dat deze geen virtuele machine bevat.  |
|  OperationNotAllowed  |  Het wijzigen van de SKU van de beschikbaarheidsset van ' uitgelijnd ' in ' klassiek ' is niet toegestaan.  |
|  OperationNotAllowed  |  Kan uitbrei dingen in de virtuele machine niet wijzigen wanneer de virtuele machine niet wordt uitgevoerd.  |
|  OperationNotAllowed  |  De opname actie wordt alleen ondersteund op een virtuele machine met schijven op basis van blobs. Gebruik de bron-Api's van de installatie kopie om een installatie kopie van een beheerde virtuele machine te maken.  |
|  OperationNotAllowed  |  De resource {0} kan niet worden gemaakt op basis van de installatie kopie {1} totdat de installatie kopie is gemaakt.  |
|  OperationNotAllowed  |  Updates voor encryptionSettings zijn niet toegestaan wanneer de virtuele machine is toegewezen. Probeer het opnieuw nadat de toewijzing van de VM ongedaan is gemaakt  |
|  OperationNotAllowed  |  Het toevoegen van een beheerde schijf aan een virtuele machine met op blobs gebaseerde schijven wordt niet ondersteund.  |
|  OperationNotAllowed  |  Het maximum aantal gegevens schijven dat kan worden gekoppeld aan een virtuele machine van deze grootte is {0}.  |
|  OperationNotAllowed  |  Het toevoegen van een BLOB op basis van een schijf naar een VM met beheerde schijven wordt niet ondersteund.  |
|  OperationNotAllowed  |  De bewerking{0}is niet toegestaan voor de installatie kopie{1}omdat de installatie kopie is gemarkeerd voor verwijdering. U kunt de Verwijder bewerking alleen opnieuw uitvoeren (of wachten totdat het proces is voltooid).  |
|  OperationNotAllowed  |  De bewerking{0}is niet toegestaan voor de VM{1}omdat de VM is gegeneraliseerd.  |
|  OperationNotAllowed  |  De bewerking{0}is niet toegestaan omdat de herstel punt verzameling{1}is gemarkeerd voor verwijdering.  |
|  OperationNotAllowed  |  De bewerking{0}is niet toegestaan voor de VM-extensie{1}omdat deze is gemarkeerd voor verwijdering. U kunt de Verwijder bewerking alleen opnieuw uitvoeren (of wachten totdat het proces is voltooid).  |
|  OperationNotAllowed  |  De bewerking{0}is niet toegestaan omdat de Virtual Machines{1}worden ingericht met de installatie kopie{2}.  |
|  OperationNotAllowed  |  De bewerking '{0}' is niet toegestaan omdat de installatie kopie '{2}' momenteel wordt gebruikt voor de virtuele-machine Schaalset '{1}'.  |
|  OperationNotAllowed  |  De bewerking{0}is niet toegestaan voor de VM{1}omdat de VM is gemarkeerd voor verwijdering. U kunt de Verwijder bewerking alleen opnieuw uitvoeren (of wachten totdat het proces is voltooid).  |
|  OperationNotAllowed  |  De bewerking{0}is niet toegestaan voor de VM{1}omdat de toewijzing van de virtuele machine ongedaan is gemaakt of is gemarkeerd om te worden toegewezen.  |
|  OperationNotAllowed  |  De bewerking{0}is niet toegestaan voor de VM{1}omdat de VM actief is. Schakel expliciet uit voor het geval u de virtuele machine afsluit vanuit het gast besturingssysteem.  |
|  OperationNotAllowed  |  De bewerking{0}is niet toegestaan voor de VM{1}omdat de toewijzing van de virtuele machine niet ongedaan is gemaakt.  |
|  OperationNotAllowed  |  De bewerking{0}is niet toegestaan voor de VM{1}, omdat de VM de extensie{2}heeft met de status mislukt.  |
|  OperationNotAllowed  |  De bewerking{0}is niet toegestaan voor de VM{1}, omdat er een andere bewerking wordt uitgevoerd.  |
|  OperationNotAllowed  |  Voor de bewerking{0}moet de virtuele machine{1}worden gegeneraliseerd.  |
|  OperationNotAllowed  |  Voor de bewerking moet de virtuele machine worden uitgevoerd (of ingesteld om te worden uitgevoerd).  |
|  OperationNotAllowed  |  De schijf met de grootte {0}GB, die kleiner is dan de grootte {1}GB van de bijbehorende schijf in de installatie kopie, is niet toegestaan.  |
|  OperationNotAllowed  |  Uitbrei dingen van de VM-Schaalset van de handler{0}kunnen alleen worden toegevoegd op het moment dat de VM-Schaalset wordt gemaakt.  |
|  OperationNotAllowed  |  Uitbrei dingen van de VM-Schaalset van de handler{0}kunnen alleen worden verwijderd op het moment dat de VM-Schaalset wordt verwijderd.  |
|  OperationNotAllowed  |  De VM{0}maakt al gebruik van beheerde schijven.  |
|  OperationNotAllowed  |  VM '{0}' behoort tot ' klassieke ' beschikbaarheidsset '{1}'. Werk de beschikbaarheidsset bij om de ' uitgelijnde ' SKU te gebruiken en voer de conversie vervolgens opnieuw uit.  |
|  OperationNotAllowed  |  Een VM die is gemaakt op basis van een installatie kopie kan geen schijven op basis van BLOB hebben Alle schijven moeten beheerde schijven zijn.  |
|  OperationNotAllowed  |  De opname bewerking kan niet worden voltooid omdat de virtuele machine niet is gegeneraliseerd.  |
|  OperationNotAllowed  |  Beheer bewerkingen op VM-{0}zijn niet toegestaan omdat VM-schijven worden geconverteerd naar Managed disks.  |
|  OperationNotAllowed  |  Bij een actieve bewerking wordt de energie status van de virtuele machine {0} gewijzigd in {1}. Voer de bewerking {2} na enige tijd uit.  |
|  OperationNotAllowed  |  De virtuele machine kan niet worden toegevoegd of bijgewerkt. De aangevraagde VM-grootte {0} mogelijk niet beschikbaar in de bestaande toewijzings eenheid. Meer informatie over de strategie voor het verg Roten/verkleinen van VM'S op https://aka.ms/azure-resizevm.  |
|  OperationNotAllowed  |  Kan de grootte van de virtuele machine niet wijzigen omdat de aangevraagde grootte {0} niet beschikbaar is in het cluster waar de beschikbaarheidsset momenteel is toegewezen. De beschik bare grootten zijn: {1}. Meer informatie over de strategie voor het verg Roten/verkleinen van VM'S op https://aka.ms/azure-resizevm.  |
|  OperationNotAllowed  |  Kan de grootte van de virtuele machine niet wijzigen omdat de aangevraagde grootte {0} niet beschikbaar is in het cluster waar de virtuele machine momenteel is toegewezen. Als u de grootte van uw virtuele machine wilt wijzigen in {1}, moet u de toewijzing ongedaan maken (dit is een stop bewerking in de Azure Portal) en de grootte van de bewerking opnieuw proberen. Meer informatie over de strategie voor het verg Roten/verkleinen van VM'S op https://aka.ms/azure-resizevm.  |
|  OSProvisioningClientError  |  Het inrichten van het besturings systeem is mislukt voor de VM{0}omdat het gast besturingssysteem momenteel wordt ingericht.  |
|  OSProvisioningClientError  |  Het inrichten van het besturings systeem voor de VM{0}is mislukt. Fout Details: {1} ervoor te zorgen dat de installatie kopie op de juiste wijze is voor bereid (gegeneraliseerd). <ul><li>Instructies voor Windows: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/  </li></ul> |
|  OSProvisioningClientError  |  Genereren van SSH-host is mislukt. Fout Details: {0}. Controleer of de Linux-agent correct is ingesteld om dit probleem op te lossen. <ul><li>U kunt de instructies controleren op: https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux/ </li></ul> |
|  OSProvisioningClientError  |  De gebruikers naam die voor de virtuele machine is opgegeven, is ongeldig voor deze Linux-distributie. Fout Details: {0}.  |
|  OSProvisioningInternalError  |  Het inrichten van het besturings systeem voor de virtuele machine{0}is mislukt vanwege een interne fout.  |
|  OSProvisioningTimedOut  |  Het inrichten van het besturings systeem voor de VM{0}is niet voltooid binnen de toegewezen tijd. De inrichting van de virtuele machine kan nog steeds worden voltooid. Controleer de inrichtings status later.  |
|  OSProvisioningTimedOut  |  Het inrichten van het besturings systeem voor de VM{0}is niet voltooid binnen de toegewezen tijd. De inrichting van de virtuele machine kan nog steeds worden voltooid. Controleer de inrichtings status later. Zorg er ook voor dat de installatie kopie op de juiste wijze is voor bereid (gegeneraliseerd).   <ul><li>Instructies voor Windows: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Instructies voor Linux: https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OSProvisioningTimedOut  |  Het inrichten van het besturings systeem voor de VM{0}is niet voltooid binnen de toegewezen tijd. De VM-gast agent is echter wel uitgevoerd. Dit geeft aan dat het gast besturingssysteem niet goed is voor bereid om te worden gebruikt als een VM-installatie kopie (met CreateOption = FromImage). Om dit probleem op te lossen, gebruikt u de VHD als is met CreateOption = koppelen of voorbereiden op de juiste manier voor gebruik als een installatie kopie:   <ul><li>Instructies voor Windows: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Instructies voor Linux: https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OverConstrainedAllocationRequest  |  De vereiste VM-grootte is momenteel niet beschikbaar op de geselecteerde locatie.  |
|  ResourceUpdateBlockedOnPlatformUpdate  |  De resource kan op dit moment niet worden bijgewerkt vanwege een voortdurende platform update. Probeer het later opnieuw.  |
|  StorageAccountLimitation  |  Het opslag account{0}biedt geen ondersteuning voor pagina-blobs die vereist zijn voor het maken van schijven.  |
|  StorageAccountLimitation  |  Het toegewezen quotum voor het opslag account{0}is overschreden.  |
|  StorageAccountLocationMismatch  |  Kan het opslag account niet {0}omzetten. Zorg ervoor dat het is gemaakt via de opslag Resource provider op dezelfde locatie als de reken resource.  |
|  StorageAccountNotFound  |  Kan het opslag account niet {0} vinden. Zorg ervoor dat het opslag account niet is verwijderd en tot dezelfde Azure-locatie behoort als de VM.  |
|  StorageAccountNotRecognized  |  Gebruik een opslag account dat wordt beheerd door de provider van de opslag resource. Het gebruik van {0} wordt niet ondersteund.  |
|  StorageAccountOperationInternalError  |  Er is een interne fout opgetreden tijdens het openen van het opslag account {0}.  |
|  StorageAccountSubscriptionMismatch  |  Het {0} van het opslag account hoort niet bij het abonnement {1}.  |
|  StorageAccountTooBusy  |  Het opslag account{0}is momenteel bezet. Overweeg het gebruik van een ander account.  |
|  StorageAccountTypeNotSupported  |  Schijf {0} gebruikt {1} die een Blob Storage-account is. Probeer het opnieuw met een opslag account voor algemene doel einden.  |
|  StorageAccountTypeNotSupported  |  Het {0} van het opslag account is van het type {1}. Diagnostische gegevens over opstarten ondersteunt {2} typen opslag accounts.  <ul><li>Deze fout treedt op als u het Premium-opslag account gebruikt voor diagnostische gegevens over opstarten. Zie voor meer informatie [over het gebruik van diagnostische gegevens over opstarten](boot-diagnostics.md). </li></ul> |
|  SubscriptionNotAuthorizedForImage  |  Het abonnement is niet geautoriseerd.  |
|  TargetDiskBlobAlreadyExists  |  Er bestaat al een BLOB {0}. Geef een andere blob-URI op om een nieuwe lege gegevens schijf te maken{1}.  |
|  TargetDiskBlobAlreadyExists  |  De opname bewerking kan niet worden voortgezet omdat de BLOB voor de doel installatie kopie {0} al bestaat en de vlag voor het overschrijven van VHD-blobs niet is ingesteld. Verwijder de BLOB of stel de vlag in op het overschrijven van de VHD-blobs en probeer het opnieuw.  |
|  TargetDiskBlobAlreadyExists  |  De opname bewerking kan niet worden voortgezet omdat er een actieve lease voor de blob van de doel afbeelding {0}.   |
|  TargetDiskBlobAlreadyExists  |  Er bestaat al een BLOB {0}. Geef een andere blob-URI op als doel voor de schijf{1}.  |
|  TooManyVMRedeploymentRequests  |  Er zijn te veel aanvragen voor herimplementaties ontvangen voor de virtuele machine '{0}' of de virtuele machines in dezelfde beschikbaarheidsset met deze virtuele machine. Probeer het later opnieuw.  |
|  VHDSizeInvalid  |  De opgegeven waarde voor schijf grootte van {0} voor schijf '{1}' met Blob {2} is ongeldig. De schijf grootte moet tussen {3} en {4}.  |
|  VMAgentStatusCommunicationError  |  De VM-{0}heeft geen status gerapporteerd voor de VM-agent of-uitbrei dingen. Controleer of de virtuele machine een actieve VM-agent heeft en of er uitgaande verbindingen met Azure Storage tot stand kunnen worden gebracht.  |
|  VMArtifactRepositoryInternalError  |  Er is een fout opgetreden tijdens de communicatie met de artefact opslagplaats om VM-artefact gegevens op te halen.  |
|  VMArtifactRepositoryInternalError  |  Er is een interne fout opgetreden tijdens het ophalen van de VM-artefact gegevens uit de artefact opslagplaats.  |
|  VMExtensionHandlerNonTransientError  |  De handler '{0}' heeft een fout gerapporteerd voor de VM-extensie '{1}' met de Terminal fout code '{2}' en het volgende fout bericht: '{3}'  |
|  VMExtensionManagementInternalError  |  Er is een interne fout opgetreden tijdens het verwerken van de VM-extensie{0}.  |
|  VMExtensionManagementInternalError  |  Er zijn meerdere fouten opgetreden bij het voorbereiden van de VM-extensies. Zie de weer gave van de VM-extensie-instantie voor meer informatie.  |
|  VMExtensionProvisioningError  |  De virtuele machine heeft een fout gerapporteerd bij het verwerken van de extensie{0}. Fout bericht:{1}.  |
|  VMExtensionProvisioningError  |  Meerdere VM-extensies kunnen niet worden ingericht op de VM. Raadpleeg de weer gave van de VM-extensie-instantie voor meer informatie.  |
|  VMExtensionProvisioningTimeout  |  Er is een time-out opgetreden tijdens het inrichten van de VM-extensie{0}. De installatie van de extensie kan te lang duren of de status van de extensie kan niet worden opgehaald.  |
|  VMMarketplaceInvalidInput  |  Het maken van een virtuele machine vanuit een niet-Marketplace-installatie kopie vereist geen plan gegevens, verwijder de plan gegevens in de aanvraag. De naam van de besturingssysteem schijf is {0}.  |
|  VMMarketplaceInvalidInput  |  De inkoop gegevens komen niet overeen. Kan niet implementeren vanuit de Marketplace-installatie kopie. De naam van de besturingssysteem schijf is {0}.  |
|  VMMarketplaceInvalidInput  |  Voor het maken van een virtuele machine vanuit een Marketplace-installatie kopie zijn plan gegevens in de aanvraag vereist. De naam van de besturingssysteem schijf is {0}.  |
|  VMNotFound  |  Kan de virtuele machine{0}niet vinden.  |
|  VMRedeploymentFailed  |  De implementatie van de VM{0}is mislukt vanwege een interne fout. Probeer het later opnieuw.  |
|  VMRedeploymentTimedOut  |  Het opnieuw implementeren van de VM '{0}' is niet voltooid binnen de toegewezen tijd. Het kan nu ook worden voltooid. Anders kunt u de aanvraag opnieuw proberen.  |
|  VMStartTimedOut  |  De VM '{0}' is niet gestart binnen de toegewezen tijd. De VM kan nog steeds worden gestart. Controleer de energie status op een later tijdstip.  |


## <a name="next-steps"></a>Volgende stappen
Als u meer hulp nodig hebt, kunt u contact opnemen met de Azure-experts op [MSDN Azure en stack overflow forums](https://azure.microsoft.com/support/forums/). U kunt ook een Azure-ondersteuning-incident indienen. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer **ondersteuning verkrijgen**.
