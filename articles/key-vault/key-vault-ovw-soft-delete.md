---
title: Azure Key Vault zacht verwijderen | Microsoft Docs
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 03/19/2019
ms.openlocfilehash: 31d3556609737212ee1257015d12e9e0621ea4ee
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197382"
---
# <a name="azure-key-vault-soft-delete-overview"></a>Overzicht van Azure Key Vault voorlopig verwijderen

Met de functie voor voorlopig verwijderen van Key Vault kunt u de verwijderde kluizen en kluis objecten herstellen, ook wel zacht verwijderen genoemd. We behandelen vooral de volgende scenario's:

- Ondersteuning voor herstel bare verwijdering van een sleutel kluis
- Ondersteuning voor herstel bare verwijdering van sleutel kluis-objecten (bijvoorbeeld sleutels, geheimen, certificaten)

## <a name="supporting-interfaces"></a>Ondersteunende interfaces

De functie voor voorlopig verwijderen is in eerste instantie beschikbaar via de [rest](/rest/api/keyvault/), [cli](key-vault-soft-delete-cli.md), [Power shell](key-vault-soft-delete-powershell.md) en [.net/C# ](/dotnet/api/microsoft.azure.keyvault?view=azure-dotnet) interfaces.

## <a name="scenarios"></a>Scenario 's

Azure-sleutel kluizen zijn bijgehouden resources die worden beheerd door Azure Resource Manager. Azure Resource Manager geeft ook een goed gedefinieerd gedrag op voor verwijdering, waardoor een geslaagde Verwijder bewerking ertoe moet leiden dat de bron niet meer toegankelijk is. De functie voor voorlopig verwijderen behandelt het herstel van het verwijderde object, of het verwijderen per ongeluk of opzettelijk is geslaagd.

1. In het typische scenario kan een gebruiker per ongeluk een sleutel kluis of een sleutel kluis-object hebben verwijderd. Als de sleutel kluis of het sleutel kluis object voor een vooraf bepaalde periode kan worden hersteld, kan de gebruiker de verwijdering ongedaan maken en de gegevens herstellen.

2. In een ander scenario kan een Rogue-gebruiker proberen een sleutel kluis of een sleutel kluis object te verwijderen, zoals een sleutel binnen een kluis, om een bedrijfs onderbreking te veroorzaken. Het verwijderen van de sleutel kluis of het sleutel kluis object van de daad werkelijke verwijdering van de onderliggende gegevens kan worden gebruikt als een veiligheids maatregel, bijvoorbeeld door de machtigingen voor het verwijderen van gegevens te beperken tot een andere vertrouwde rol. Deze benadering vereist een quorum voor een bewerking die anders kan leiden tot een onmiddellijk verlies van gegevens.

### <a name="soft-delete-behavior"></a>Gedrag bij zacht verwijderen

Als de functie voor voorlopig verwijderen is ingeschakeld, worden de resources die zijn gemarkeerd als verwijderde resources, gedurende een opgegeven periode (standaard 90 dagen) bewaard. De service biedt verder een mechanisme voor het herstellen van het verwijderde object, waardoor het verwijderen in feite ongedaan wordt.

Bij het maken van een nieuwe sleutel kluis is voorlopig verwijderen standaard ingeschakeld. U kunt een sleutel kluis maken zonder zacht te verwijderen via de [Azure cli](key-vault-soft-delete-cli.md) of [Azure Power shell](key-vault-soft-delete-powershell.md). Wanneer de functie voor het voorlopig verwijderen van een sleutel kluis is ingeschakeld, kan deze niet worden uitgeschakeld

De standaard retentie periode is 90 dagen, maar tijdens het maken van de sleutel kluis is het mogelijk om het interval voor het Bewaar beleid in te stellen op een waarde tussen 7 en 90 dagen via de Azure Portal. Het retentie beleid voor het leegmaken van de beveiliging gebruikt hetzelfde interval. Eenmaal ingesteld, kan het interval voor het Bewaar beleid niet worden gewijzigd.

U kunt de naam van een sleutel kluis die zacht is verwijderd, niet opnieuw gebruiken totdat de Bewaar periode is verstreken.

### <a name="purge-protection"></a>Beveiliging opschonen 

Het opschonen van de beveiliging is een optioneel Key Vault gedrag en is **standaard niet ingeschakeld**. Deze kan worden ingeschakeld via [cli](key-vault-soft-delete-cli.md#enabling-purge-protection) of [Power shell](key-vault-soft-delete-powershell.md#enabling-purge-protection).

Wanneer het leegmaken van de beveiliging is ingeschakeld, kan een kluis of een object in de verwijderde status pas worden verwijderd nadat de Bewaar periode is verstreken. Voorlopig verwijderde kluizen en objecten kunnen nog steeds worden hersteld, zodat het Bewaar beleid wordt gevolgd. 

De standaard retentie periode is 90 dagen, maar het is mogelijk om het interval voor het Bewaar beleid in te stellen op een waarde van 7 tot 90 dagen via de Azure Portal. Zodra het interval voor het Bewaar beleid is ingesteld en opgeslagen, kan het niet meer worden gewijzigd voor die kluis. 

### <a name="permitted-purge"></a>Toegestaan opschonen

Permanent verwijderen, opschonen, een sleutel kluis is mogelijk via een POST-bewerking op de proxy bron en vereist speciale bevoegdheden. Over het algemeen kan alleen de eigenaar van het abonnement een sleutel kluis opschonen. Met de POST-bewerking wordt de onmiddellijke en onherstelbare verwijdering van die kluis geactiveerd. 

Uitzonde ringen zijn:
- Wanneer het Azure-abonnement is gemarkeerd als niet- *verwijderd*. In dit geval kan de service de daad werkelijke verwijdering vervolgens uitvoeren, en dit als een gepland proces. 
- Wanneer de vlag--Enable-upbeveiliging is ingeschakeld op de kluis zelf. In dit geval wacht Key Vault 90 dagen vanaf het moment dat het oorspronkelijke geheime object werd gemarkeerd voor verwijdering, zodat het object definitief kan worden verwijderd.

### <a name="key-vault-recovery"></a>Herstel van sleutel kluis

Wanneer u een sleutel kluis verwijdert, maakt de service een proxy resource onder het abonnement en voegt hij voldoende meta gegevens toe voor herstel. De proxy resource is een opgeslagen object dat beschikbaar is op dezelfde locatie als de verwijderde sleutel kluis. 

### <a name="key-vault-object-recovery"></a>Sleutel kluis-object herstel

Wanneer u een sleutel kluis-object verwijdert, zoals een sleutel, plaatst de service het object in een verwijderde staat, waardoor het ontoegankelijk wordt voor alle ophaal bewerkingen. In deze status kan het sleutel kluis-object alleen worden weer gegeven, hersteld of geforceerd/permanent worden verwijderd. 

Tegelijkertijd plant Key Vault de verwijdering van de onderliggende gegevens die overeenkomen met de verwijderde sleutel kluis of het sleutel kluis-object voor uitvoering na een vooraf vastgesteld Bewaar interval. De DNS-record die overeenkomt met de kluis, wordt ook bewaard voor de duur van de Bewaar periode.

### <a name="soft-delete-retention-period"></a>Tijdelijke Bewaar periode voor verwijderen

Voorlopig verwijderde resources worden gedurende een bepaalde periode (90 dagen) bewaard. Het volgende is van toepassing tijdens het Bewaar interval voor voorlopig verwijderen:

- U kunt alle sleutel kluizen en sleutel kluis objecten in de verzachtte verwijderings status van uw abonnement vermelden, evenals de toegang voor het verwijderen en herstellen van de gegevens.
    - Alleen gebruikers met speciale machtigingen kunnen verwijderde kluizen weer geven. We raden aan dat onze gebruikers een aangepaste rol maken met deze speciale machtigingen voor het afhandelen van verwijderde kluizen.
- Een sleutel kluis met dezelfde naam kan niet op dezelfde locatie worden gemaakt. een sleutel kluis object kan niet worden gemaakt in een bepaalde kluis als deze sleutel kluis een object met dezelfde naam en een verwijderde status bevat 
- Alleen een specifiek bevoegde gebruiker kan een sleutel kluis of een sleutel kluis object herstellen door een herstel opdracht uit te geven voor de bijbehorende proxy resource.
    - De gebruiker, het lid van de aangepaste rol, met de bevoegdheid om een sleutel kluis te maken onder de resource groep kan de kluis herstellen.
- Alleen een specifiek bevoegde gebruiker kan een sleutel kluis of een sleutel kluis object verwijderen door een verwijder opdracht uit te geven voor de bijbehorende proxy resource.

Tenzij een sleutel kluis of sleutel kluis object wordt hersteld, wordt aan het einde van de Bewaar termijn de service een schone verwijdering van de tijdelijke, verwijderde sleutel kluis of het sleutel kluis-object en de bijbehorende inhoud uitgevoerd. Het verwijderen van resources kan niet opnieuw worden gepland.

### <a name="billing-implications"></a>Facturerings implicaties

In het algemeen zijn er slechts twee bewerkingen mogelijk: ' opschonen ' en ' herstellen ' wanneer een object (een sleutel kluis of een sleutel of een geheim) de status verwijderd heeft. Alle andere bewerkingen zullen mislukken. Daarom kunnen er geen bewerkingen worden uitgevoerd, zelfs als het object bestaat en er geen gebruik wordt gemaakt, dus geen factuur. Er zijn echter de volgende uitzonde ringen:

- de acties opschonen en herstellen tellen mee bij normale sleutel kluis bewerkingen en worden gefactureerd.
- Als het object een HSM-sleutel is, is de kosten voor de sleutel van de HSM Protected Key-versie per maand van toepassing als er in de afgelopen 30 dagen een sleutel versie is gebruikt. Daarna kunnen er geen bewerkingen worden uitgevoerd, omdat het object zich in de verwijderde status bevindt, zodat er geen kosten in rekening worden gebracht.

## <a name="next-steps"></a>Volgende stappen

De volgende twee hand leidingen bieden de primaire gebruiks scenario's voor het gebruik van voorlopig verwijderen.

- [De Key Vault-functie voor voorlopig verwijderen gebruiken met PowerShell](key-vault-soft-delete-powershell.md) 
- [De Key Vault-functie voor voorlopig verwijderen gebruiken met CLI](key-vault-soft-delete-cli.md)

