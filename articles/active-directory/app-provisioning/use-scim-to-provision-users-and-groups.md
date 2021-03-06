---
title: Een SCIM-eind punt ontwikkelen voor het inrichten van gebruikers voor apps vanuit Azure AD
description: Met het systeem voor Identity Management (SCIM) met meerdere domeinen wordt automatische gebruikers inrichting gestandaardiseerd. Leer hoe u een SCIM-eind punt ontwikkelt, uw SCIM-API integreert met Azure Active Directory en beginnen met het automatiseren van het inrichten van gebruikers en groepen in uw Cloud toepassingen.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/07/2020
ms.author: mimart
ms.reviewer: arvinh
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42fc10c1e7e88e36e4d2174671702e043fb96538
ms.sourcegitcommit: 9cbd5b790299f080a64bab332bb031543c2de160
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/08/2020
ms.locfileid: "78926851"
---
# <a name="build-a-scim-endpoint-and-configure-user-provisioning-with-azure-active-directory-azure-ad"></a>Een SCIM-eind punt bouwen en gebruikers inrichten configureren met Azure Active Directory (Azure AD)

Als ontwikkelaar van een toepassing kunt u het systeem gebruiken voor SCIM-gebruikers beheer-API (Cross-Domain Identity Management) om het automatisch inrichten van gebruikers en groepen tussen uw toepassing en Azure AD mogelijk te maken. In dit artikel wordt beschreven hoe u een SCIM-eind punt bouwt en integreert met de Azure AD-inrichtings service. De SCIM-specificatie biedt een gemeen schappelijk gebruikers schema voor het inrichten van. Bij gebruik in combi natie met Federatie standaarden zoals SAML of OpenID Connect Connect biedt beheerders een end-to-end, op standaarden gebaseerde oplossing voor toegangs beheer.

SCIM is een gestandaardiseerde definitie van twee eind punten: een/users-eind punt en een/groups-eind punt. Er worden algemene REST-werk woorden gebruikt om objecten te maken, bij te werken en te verwijderen, en een vooraf gedefinieerd schema voor algemene kenmerken zoals groeps naam, gebruikers naam, voor naam, achternaam en e-mail adres. Apps die een SCIM 2,0-REST API bieden, kunnen de pijn van het werken met een eigen API voor gebruikers beheer verminderen of elimineren. Elke compatibele SCIM-client weet bijvoorbeeld hoe u een HTTP POST van een JSON-object naar het/users-eind punt kunt maken om een nieuwe gebruikers vermelding te maken. Apps die voldoen aan de SCIM-standaard kunnen direct profiteren van bestaande clients, hulpprogram ma's en code, in plaats van een iets andere API voor dezelfde basis acties te hoeven gebruiken. 

![Inrichten vanuit Azure AD naar een app met SCIM](media/use-scim-to-provision-users-and-groups/scim-provisioning-overview.png)

Met het standaard gebruikers object schema en de rest Api's voor beheer dat is gedefinieerd in SCIM 2,0 (RFC [7642](https://tools.ietf.org/html/rfc7642), [7643](https://tools.ietf.org/html/rfc7643), [7644](https://tools.ietf.org/html/rfc7644)) kunnen id-providers en apps gemakkelijker met elkaar worden geïntegreerd. Toepassings ontwikkelaars die een SCIM-eind punt bouwen, kunnen worden geïntegreerd met elke SCIM-compatibele client zonder aangepaste werk.

Voor het automatiseren van de inrichting van een toepassing moet u een SCIM-eind punt bouwen en integreren met de Azure AD SCIM-client. Voer de volgende stappen uit om het inrichten van gebruikers en groepen in uw toepassing te starten. 
    
  * **[Stap 1: uw gebruikers-en groeps schema ontwerpen.](#step-1-design-your-user-and-group-schema)** Identificeer de objecten en kenmerken die uw toepassing nodig heeft en bepaal hoe deze worden toegewezen aan het gebruikers-en groeps schema dat wordt ondersteund door de Azure AD SCIM-implementatie.

  * **[Stap 2: inzicht in de implementatie van Azure AD SCIM.](#step-2-understand-the-azure-ad-scim-implementation)** Meer informatie over de implementatie van de Azure AD SCIM-client en het model leren van de verwerking van uw SCIM-protocol aanvragen en antwoorden.

  * **[Stap 3: een SCIM-eind punt bouwen.](#step-3-build-a-scim-endpoint)** Een eind punt moet SCIM 2,0-compatibel zijn om te kunnen worden geïntegreerd met de Azure AD-inrichtings service. Als optie kunt u gebruikmaken van micro soft CLI-bibliotheken en-code voorbeelden om uw eind punt te bouwen. Deze voor beelden zijn alleen ter referentie en alleen testen. u wordt aangeraden uw productie-app te coderen zodat deze afhankelijk is.

  * **[Stap 4: Integreer uw SCIM-eind punt met de Azure AD SCIM-client.](#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client)** Als uw organisatie gebruikmaakt van een toepassing van derden die het profiel van SCIM 2,0 dat door Azure AD wordt ondersteund, implementeert, kunt u het automatisch inrichten en het ongedaan maken van de inrichting van gebruikers en groepen direct starten.

  * **[Stap 5: Publiceer uw toepassing in de Azure AD-toepassings galerie.](#step-5-publish-your-application-to-the-azure-ad-application-gallery)** Maak het eenvoudig voor klanten om uw toepassing te detecteren en de inrichting eenvoudig te configureren. 

![Stappen voor het integreren van een SCIM-eind punt met Azure AD](media/use-scim-to-provision-users-and-groups/process.png)

## <a name="step-1-design-your-user-and-group-schema"></a>Stap 1: uw gebruikers-en groeps schema ontwerpen

Voor elke toepassing zijn verschillende kenmerken vereist om een gebruiker of groep te maken. Start uw integratie door de objecten (gebruikers, groepen) en kenmerken (naam, Manager, functie, enz.) te identificeren die nodig zijn voor uw toepassing. De SCIM-standaard definieert een schema voor het beheren van gebruikers en groepen. Voor het kern gebruikers schema zijn alleen drie kenmerken vereist: **id** (door de service provider gedefinieerde id), **externalId** (door client gedefinieerde id) en **META** (alleen-lezen meta gegevens die worden onderhouden door de service provider). Alle andere kenmerken zijn optioneel. Naast het kern gebruikers schema definieert de SCIM-standaard een gebruikers extensie voor ondernemingen en een model om het gebruikers schema uit te breiden om te voldoen aan de behoeften van uw toepassing. Als uw toepassing bijvoorbeeld een manager van een gebruiker vereist, kunt u het ondernemings gebruikers schema gebruiken om de Manager van de gebruiker en het kern schema te verzamelen voor het verzamelen van het e-mail adres van de gebruiker. Volg de onderstaande stappen om uw schema te ontwerpen:
  1. Geef een lijst van de kenmerken die uw toepassing nodig heeft. Het kan handig zijn om uw vereisten te onderverdelen in de kenmerken die nodig zijn voor authenticatie (bijvoorbeeld aanmeldings naam en e-mail adres), kenmerken die nodig zijn voor het beheren van de levens cyclus van de gebruiker (bijvoorbeeld status/actief) en andere kenmerken die nodig zijn om uw specifieke toepassing te laten werken (bijvoorbeeld Manager, tag).
  2. Controleer of deze kenmerken al zijn gedefinieerd in het basis gebruikers schema of het gebruikers schema van de onderneming. Als kenmerken die u nodig hebt en niet worden behandeld in de basis-of ENTER prise-gebruikers schema's, moet u een uitbrei ding definiëren voor het gebruikers schema dat betrekking heeft op de kenmerken die u nodig hebt. In het onderstaande voor beeld hebben we een extensie toegevoegd aan de gebruiker om het inrichten van een ' tag ' op een gebruiker toe te staan. U kunt het beste beginnen met alleen de basis-en ondernemings gebruikers schema's en later uitbreiden naar aanvullende aangepaste schema's.  
  3. Wijs de SCIM-kenmerken toe aan de gebruikers kenmerken in azure AD. Als een van de kenmerken die u hebt gedefinieerd in uw SCIM-eind punt geen duidelijke vergelijk bare soort heeft in het Azure AD-gebruikers schema, is de kans groot dat de gegevens niet in het gebruikers object op de meeste tenants worden opgeslagen. Bepaal of dit kenmerk optioneel kan zijn voor het maken van een gebruiker. Als het kenmerk essentieel is voor de werking van uw toepassing, kunt u de Tenant beheerder helpen hun schema uit te breiden of een uitbreidings kenmerk te gebruiken zoals hieronder wordt weer gegeven voor de eigenschap "Tags".

### <a name="table-1-outline-the-attributes-that-you-need"></a>Tabel 1: een overzicht van de kenmerken die u nodig hebt 
| Stap 1: kenmerken bepalen die uw app nodig heeft| Stap 2: app-vereisten toewijzen aan SCIM Standard| Stap 3: SCIM-kenmerken toewijzen aan de Azure AD-kenmerken|
|--|--|--|
|Aanmeldings naam|userName|userPrincipalName|
|voornaam|name.givenName|givenName|
|achternaam|naam. lastName|achternaam|
|workMail|E-mail berichten [type EQ "werk]. waarde|Mail|
|Manager|Manager|Manager|
|Tag|urn: IETF: params: scim: schemas: extension: 2.0: CustomExtension: tag|extensionAttribute1|
|status|actief|isSoftDeleted (berekende waarde niet opgeslagen op gebruiker)|

Het hierboven gedefinieerde schema wordt weer gegeven met behulp van de JSON-nettolading hieronder. Houd er rekening mee dat naast de vereiste kenmerken voor de toepassing, de JSON-weer gave de vereiste kenmerken ' id ', ' externalId ' en ' meta ' bevat.

```json
{
     "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User",
      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
      "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User"],
     "userName":"bjensen",
     "externalId":"bjensen",
     "name":{
       "familyName":"Jensen",
       "givenName":"Barbara"
     },
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User": {
     "Manager": "123456"
   },
     "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:CustomAttribute:User": {
     "tag": "701984",
   },
   "meta": {
     "resourceType": "User",
     "created": "2010-01-23T04:56:22Z",
     "lastModified": "2011-05-13T04:42:34Z",
     "version": "W\/\"3694e05e9dff591\"",
     "location":
 "https://example.com/v2/Users/2819c223-7f76-453a-919d-413861904646"
   }
 ```

### <a name="table-2-default-user-attribute-mapping"></a>Tabel 2: standaard toewijzing van gebruikers kenmerken
U kunt de onderstaande tabel gebruiken om te begrijpen hoe de kenmerken die uw toepassing nodig heeft, kunnen worden toegewezen aan een kenmerk in azure AD en de SCIM RFC. U kunt [aanpassen](customize-application-attributes.md) hoe kenmerken worden toegewezen tussen Azure AD en uw scim-eind punt. Houd er rekening mee dat u niet zowel gebruikers als groepen of alle hieronder weer gegeven kenmerken moet ondersteunen. Ze zijn een verwijzing naar de manier waarop kenmerken in azure AD vaak worden toegewezen aan eigenschappen in het SCIM-protocol. 

| Azure Active Directory-gebruiker | "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User" |
| --- | --- |
| IsSoftDeleted |actief |
|department|urn: IETF: params: scim: schemas: extension: Enter prise: 2.0: gebruiker: Department|
| displayName |displayName |
|employeeId|urn: IETF: params: scim: schemas: extensie: Enter prise: 2.0: gebruiker: employeeNumber|
| Facsimile-TelephoneNumber |phoneNumbers [type eq "fax"] .value |
| givenName |name.givenName |
| Functie |titel |
| mail |e-mailberichten [type eq 'werk'] .value |
| mailNickname |externalId |
| Manager |urn: IETF: params: scim: schemas: extensie: Enter prise: 2.0: gebruiker: Manager |
| mobiele |phoneNumbers [type eq 'mobiel'] .value |
| Postcode |adressen type eq 'werk'.postalCode |
| proxy-adressen |e-mailberichten [Typ eq 'ander']. Waarde |
| physical-Delivery-OfficeName |adressen [Typ eq 'ander']. Indeling |
| streetAddress |adressen type eq 'werk'.streetAddress |
| surname |name.familyName |
| Telefoonnummer |phoneNumbers [type eq 'werk'] .value |
| user-PrincipalName |userName |


### <a name="table-3-default-group-attribute-mapping"></a>Tabel 3: standaard toewijzing van groeps kenmerken

| Azure Active Directory-groep | urn: IETF: params: scim: schemas: kern: 2.0: groep |
| --- | --- |
| displayName |displayName |
| mail |e-mailberichten [type eq 'werk'] .value |
| mailNickname |displayName |
| leden |leden |
| object-id |externalId |
| proxyAddresses |e-mailberichten [Typ eq 'ander']. Waarde |

Er zijn verschillende eind punten gedefinieerd in de SCIM RFC. U kunt aan de slag met het/User-eind punt en vervolgens van daaruit uitbreiden. Het/schemas-eind punt is handig wanneer u aangepaste kenmerken gebruikt of als uw schema regel matig wordt gewijzigd. Hiermee kan een client automatisch het meest recente schema ophalen. Het/bulk-eind punt is vooral handig bij het ondersteunen van groepen. De volgende tabel beschrijft de verschillende eind punten die zijn gedefinieerd in de SCIM-standaard. Het/schemas-eind punt is handig wanneer u aangepaste kenmerken gebruikt of als uw schema regel matig wordt gewijzigd. Hiermee kan een client automatisch het meest recente schema ophalen. Het/bulk-eind punt is vooral handig bij het ondersteunen van groepen. De volgende tabel beschrijft de verschillende eind punten die zijn gedefinieerd in de SCIM-standaard. 
 
### <a name="table-4-determine-the-endpoints-that-you-would-like-to-develop"></a>Tabel 4: Bepaal de eind punten die u wilt ontwikkelen
|ENDPOINTS|BESCHRIJVING|
|--|--|
|/User|RUWE bewerkingen op een gebruikers object uit te voeren.|
|/Group|RUWE bewerkingen uitvoeren op een groeps object.|
|/ServiceProviderConfig|Bevat informatie over de functies van de SCIM-standaard die worden ondersteund, bijvoorbeeld de bronnen die worden ondersteund en de verificatie methode.|
|/ResourceTypes|Hiermee geeft u de meta gegevens van elke resource op|
|/Schemas|De set met kenmerken die door elke client en service provider wordt ondersteund, kan variëren. Hoewel een service provider ' naam, ' titel ' en ' e-mailen ' kan bevatten, terwijl een andere service provider ' naam ', ' titel ' en ' phoneNumbers ' gebruikt. Het eind punt voor schema's maakt het mogelijk om de kenmerken te detecteren die worden ondersteund.|
|/Bulk|Met bulk bewerkingen kunt u bewerkingen uitvoeren op een grote verzameling Resource objecten in één bewerking (zoals lidmaatschappen van updates voor een grote groep).|


## <a name="step-2-understand-the-azure-ad-scim-implementation"></a>Stap 2: inzicht in de implementatie van Azure AD SCIM
> [!IMPORTANT]
> Het gedrag van de Azure AD SCIM-implementatie is voor het laatst bijgewerkt op 18 december 2018. Zie [SCIM 2,0-protocol compatibiliteit van de Azure AD User Provisioning Service](application-provisioning-config-problem-scim-compatibility.md)voor meer informatie over wat er is gewijzigd.

Als u een toepassing bouwt die een SCIM 2,0-gebruikers beheer-API ondersteunt, wordt in deze sectie in detail beschreven hoe de Azure AD SCIM-client is geïmplementeerd. Ook wordt uitgelegd hoe u de verwerking van SCIM-protocol aanvragen en-antwoorden kunt model leren. Nadat u uw SCIM-eind punt hebt geïmplementeerd, kunt u dit testen door de procedure te volgen die in de vorige sectie is beschreven.

Binnen de [SCIM 2,0-protocol specificatie](http://www.simplecloud.info/#Specification)moet uw toepassing aan de volgende vereisten voldoen:

* Biedt ondersteuning voor het maken van gebruikers, en optioneel ook groepen, zoals wordt bepaald door para graaf [3,3 van het scim-protocol](https://tools.ietf.org/html/rfc7644#section-3.3).  
* Ondersteunt het wijzigen van gebruikers of groepen met PATCH aanvragen, zoals wordt bepaald door [de sectie 3.5.2 van het scim-protocol](https://tools.ietf.org/html/rfc7644#section-3.5.2).  
* Biedt ondersteuning voor het ophalen van een bekende resource voor een eerder gemaakte gebruiker of groep, zoals wordt bepaald door [de sectie 3.4.1 van het scim-protocol](https://tools.ietf.org/html/rfc7644#section-3.4.1).  
* Biedt ondersteuning voor het uitvoeren van query's op gebruikers of groepen, zoals wordt bepaald door sectie [3.4.2 van het scim-protocol](https://tools.ietf.org/html/rfc7644#section-3.4.2).  Gebruikers worden standaard opgehaald door hun `id` en hebben een query uitgevoerd op de `username` en de `externalid`, en groepen worden door `displayName`opgevraagd.  
* Biedt ondersteuning voor het uitvoeren van query's op de gebruiker door de ID en per Manager, zoals wordt bepaald door de sectie 3.4.2 van het SCIM-protocol.  
* Ondersteunt het opvragen van groepen op ID en op lid, zoals wordt bepaald door de sectie 3.4.2 van het SCIM-protocol.  
* Hiermee wordt één Bearer-token geaccepteerd voor verificatie en autorisatie van Azure AD voor uw toepassing.

Volg deze algemene richt lijnen bij het implementeren van een SCIM-eind punt om te zorgen voor compatibiliteit met Azure AD:

* `id` is een vereiste eigenschap voor alle resources. Elk antwoord dat een resource retourneert, moet ervoor zorgen dat elke resource deze eigenschap bevat, met uitzonde ring van `ListResponse` met nul leden.
* De reactie op een query/filter aanvraag moet altijd een `ListResponse`zijn.
* Groepen zijn optioneel, maar worden alleen ondersteund als de implementatie van de SCIM PATCH aanvragen ondersteunt.
* Het is niet nodig om de volledige resource op te neemen in de reactie van de PATCH.
* Microsoft Azure AD maakt alleen gebruik van de volgende Opera tors:  
    - `eq`
    - `and`
* Geen hoofdletter gevoelige overeenkomst voor structurele elementen in SCIM vereist, met name PATCH `op` bewerking waarden, zoals gedefinieerd in https://tools.ietf.org/html/rfc7644#section-3.5.2. Azure AD verzendt de waarden van ' op ' als `Add`, `Replace`en `Remove`.
* Microsoft Azure AD maakt aanvragen voor het ophalen van een wille keurige gebruiker en groep om ervoor te zorgen dat het eind punt en de referenties geldig zijn. Het wordt ook uitgevoerd als onderdeel van een **test verbindings** stroom in de [Azure Portal](https://portal.azure.com). 
* Het kenmerk waarin de resources kunnen worden opgevraagd, moet worden ingesteld als een overeenkomend kenmerk op de toepassing in de [Azure Portal](https://portal.azure.com). Zie voor meer informatie [aanpassen van kenmerk toewijzingen](customize-application-attributes.md) voor het inrichten van gebruikers

### <a name="user-provisioning-and-deprovisioning"></a>Gebruikers inrichten en de inrichting ongedaan maken

In de volgende afbeelding ziet u de berichten die Azure Active Directory verzendt naar een SCIM-service om de levens cyclus van een gebruiker in de identiteits opslag van uw toepassing te beheren.  

![toont de volg orde van de gebruikers inrichting en het ongedaan maken van de inrichting](media/use-scim-to-provision-users-and-groups/scim-figure-4.png)<br/>
*Volg orde voor het inrichten en opheffen van de inrichting van gebruikers*

### <a name="group-provisioning-and-deprovisioning"></a>Groeps inrichting en ongedaan maken van inrichting

Het inrichten en het ongedaan maken van de inrichting van groepen is optioneel. Wanneer de volgende afbeelding is geïmplementeerd en ingeschakeld, ziet u de berichten die Azure AD verzendt naar een SCIM-service om de levens cyclus van een groep in de identiteits opslag van uw toepassing te beheren.  Deze berichten verschillen van de berichten over gebruikers op twee manieren:

* Bij aanvragen voor het ophalen van groepen wordt opgegeven dat het kenmerk members moet worden uitgesloten van alle resources die zijn opgegeven in reactie op de aanvraag.  
* Verzoeken om te bepalen of een verwijzingskenmerk een bepaalde waarde heeft zijn aanvragen over het kenmerk leden.  

![toont de volg orde van de groeps inrichting en het ongedaan maken van de inrichting](media/use-scim-to-provision-users-and-groups/scim-figure-5.png)<br/>
*Volg orde van groeps inrichting en ongedaan maken van inrichting*

### <a name="scim-protocol-requests-and-responses"></a>Aanvragen en antwoorden van het SCIM-Protocol
Deze sectie bevat voor beelden van SCIM-aanvragen die worden verzonden door de Azure AD SCIM-client en voor beelden van verwachte reacties. Voor de beste resultaten moet u de app coderen om deze aanvragen in deze indeling af te handelen en de verwachte reacties te verzenden.

> [!IMPORTANT]
> Als u wilt weten hoe en wanneer de Azure AD User Provisioning-Service de hieronder beschreven bewerkingen afrondt, raadpleegt u de sectie [cycli inrichten: eerste en incrementeel](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) in de werking van [inrichten](how-provisioning-works.md).

[Gebruikers bewerkingen](#user-operations)
  - [Gebruiker maken](#create-user) ([aanvraag](#request) / [)](#response)
  - [Gebruiker ophalen](#get-user) ([verzoek om](#request-1) [ / )](#response-1)
  - [Gebruiker ophalen op basis van query](#get-user-by-query) ([aanvraag](#request-2) [ / )](#response-2)
  - [Gebruiker ophalen op basis van query-nul resultaten](#get-user-by-query---zero-results) ([aanvraag](#request-3)
/ [antwoord](#response-3))
  - [Gebruiker bijwerken [eigenschappen met meerdere waarden]](#update-user-multi-valued-properties) ([aanvraag](#request-4) /  [antwoord](#response-4))
  - [Update gebruiker [eigenschappen met één waarde]](#update-user-single-valued-properties) ([aanvraag](#request-5)
/ [antwoord](#response-5)) 
  - [Gebruiker uitschakelen](#disable-user) ( [reactie](#response-14)van / 
[aanvragen](#request-14) )
  - [Gebruiker verwijderen](#delete-user) ([aanvraag](#request-6) / 
[antwoord](#response-6))


[Groeps bewerkingen](#group-operations)
  - [Groep maken](#create-group) ( [vraag](#request-7) / [antwoord](#response-7))
  - [Groep ophalen](#get-group) ( [aanvraag](#request-8) / - [antwoord](#response-8))
  - [Groep ophalen op DisplayName](#get-group-by-displayname) ([aanvraag](#request-9) / [antwoord](#response-9))
  - [Update groep [niet-lidtype kenmerken]](#update-group-non-member-attributes) ([aanvraag](#request-10) /
 [antwoord](#response-10))
  - [Update groep [leden toevoegen]](#update-group-add-members) ( [aanvraag](#request-11) /
[antwoord](#response-11))
  - [Update groep [leden verwijderen]](#update-group-remove-members) ( [aanvraag](#request-12) /
[antwoord](#response-12))
  - [Groep verwijderen](#delete-group) ([aanvraag](#request-13) /
[antwoord](#response-13))

### <a name="user-operations"></a>Gebruikers bewerkingen

* Gebruikers kunnen worden doorzocht op `userName`-of `email[type eq "work"]`-kenmerken.  

#### <a name="create-user"></a>Gebruiker maken

###### <a name="request"></a>Aanvraag

*/Users plaatsen*
```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "active": true,
    "emails": [{
        "primary": true,
        "type": "work",
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com"
    }],
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName"
    },
    "roles": []
}
```

##### <a name="response"></a>Antwoord

*HTTP/1.1 201 gemaakt*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "48af03ac28ad4fb88478",
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="get-user"></a>Gebruiker ophalen

###### <a name="request-1"></a>Schot
*/Users/5d48a0a8e9f04aa38008 ophalen* 

###### <a name="response-1"></a>Antwoord (gebruiker gevonden)
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5d48a0a8e9f04aa38008",
    "externalId": "58342554-38d6-4ec8-948c-50044d0a33fd",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_feed3ace-693c-4e5a-82e2-694be1b39934",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_22370c1a-9012-42b2-bf64-86099c2a1c22@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

###### <a name="request"></a>Aanvraag
*/Users/5171a35d82074e068ce2 ophalen* 

###### <a name="response-user-not-found-note-that-the-detail-is-not-required-only-status"></a>Antwoord (gebruiker niet gevonden. Houd er rekening mee dat de details niet vereist zijn, alleen status.)

```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:Error"
    ],
    "status": "404",
    "detail": "Resource 23B51B0E5D7AE9110A49411D@7cca31655d49f3640a494224 not found"
}
```

#### <a name="get-user-by-query"></a>Gebruiker op query ophalen

##### <a name="request-2"></a>Schot

*/Users ophalen? filter = gebruikers naam EQ "Test_User_dfeef4c5-5681 -4387-b016-bdf221e82081"*

##### <a name="response-2"></a>Beantwoord

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
        "id": "2441309d85324e7793ae",
        "externalId": "7fce0092-d52e-4f76-b727-3955bd72c939",
        "meta": {
            "resourceType": "User",
            "created": "2018-03-27T19:59:26.000Z",
            "lastModified": "2018-03-27T19:59:26.000Z"
            
        },
        "userName": "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081",
        "name": {
            "familyName": "familyName",
            "givenName": "givenName"
        },
        "active": true,
        "emails": [{
            "value": "Test_User_91b67701-697b-46de-b864-bd0bbe4f99c1@testuser.com",
            "type": "work",
            "primary": true
        }]
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="get-user-by-query---zero-results"></a>Gebruiker ophalen op basis van query-nul resultaten

##### <a name="request-3"></a>Schot

*/Users ophalen? filter = gebruikers naam EQ "niet-bestaande gebruiker"*

##### <a name="response-3"></a>Beantwoord

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 0,
    "Resources": [],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="update-user-multi-valued-properties"></a>Gebruiker bijwerken [eigenschappen met meerdere waarden]

##### <a name="request-4"></a>Schot

*PATCH/users/6764549bef60420686bc HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [
            {
            "op": "Replace",
            "path": "emails[type eq \"work\"].value",
            "value": "updatedEmail@microsoft.com"
            },
            {
            "op": "Replace",
            "path": "name.familyName",
            "value": "updatedFamilyName"
            }
    ]
}
```

##### <a name="response-4"></a>Beantwoord

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "6764549bef60420686bc",
    "externalId": "6c75de36-30fa-4d2d-a196-6bdcdb6b6539",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_fbb9dda4-fcde-4f98-a68b-6c5599e17c27",
    "name": {
        "formatted": "givenName updatedFamilyName",
        "familyName": "updatedFamilyName",
        "givenName": "givenName"
    },
    "active": true,
    "emails": [{
        "value": "updatedEmail@microsoft.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="update-user-single-valued-properties"></a>Gebruiker bijwerken [eigenschappen met één waarde]

##### <a name="request-5"></a>Schot

*PATCH/users/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "userName",
        "value": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com"
    }]
}
```

##### <a name="response-5"></a>Beantwoord

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5171a35d82074e068ce2",
    "externalId": "aa1eca08-7179-4eeb-a0be-a519f7e5cd1a",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "userName": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_49dc1090-aada-4657-8434-4995c25a00f7@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

### <a name="disable-user"></a>Gebruiker uitschakelen

##### <a name="request-14"></a>Schot

*PATCH/users/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "Operations": [
        {
            "op": "Replace",
            "path": "active",
            "value": false
        }
    ],
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ]
}
```

##### <a name="response-14"></a>Beantwoord

```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User"
    ],
    "id": "CEC50F275D83C4530A495FCF@834d0e1e5d8235f90a495fda",
    "userName": "deanruiz@testuser.com",
    "name": {
        "familyName": "Harris",
        "givenName": "Larry"
    },
    "active": false,
    "emails": [
        {
            "value": "gloversuzanne@testuser.com",
            "type": "work",
            "primary": true
        }
    ],
    "addresses": [
        {
            "country": "ML",
            "type": "work",
            "primary": true
        }
    ],
    "meta": {
        "resourceType": "Users",
        "location": "/scim/5171a35d82074e068ce2/Users/CEC50F265D83B4530B495FCF@5171a35d82074e068ce2"
    }
}
```
#### <a name="delete-user"></a>Gebruiker verwijderen

##### <a name="request-6"></a>Schot

*/Users/5171a35d82074e068ce2 HTTP/1.1 verwijderen*

##### <a name="response-6"></a>Beantwoord

*HTTP/1.1 204 geen inhoud*

### <a name="group-operations"></a>Groeps bewerkingen

* Groepen moeten altijd worden gemaakt met een lijst met lege leden.
* Groepen kunnen worden opgevraagd met het kenmerk `displayName`.
* Update naar de groeps PATCH-aanvraag moet resulteren in een *HTTP 204 geen inhoud* in het antwoord. Het retour neren van een hoofd tekst met een lijst met alle leden is niet aanbevolen.
* Het is niet nodig om te ondersteunen bij het retour neren van alle leden van de groep.

#### <a name="create-group"></a>Groep maken

##### <a name="request-7"></a>Schot

*POST/groups HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group", "http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/2.0/Group"],
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "displayName": "displayName",
    "meta": {
        "resourceType": "Group"
    }
}
```

##### <a name="response-7"></a>Beantwoord

*HTTP/1.1 201 gemaakt*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "927fa2c08dcb4a7fae9e",
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "displayName": "displayName",
    "members": []
}
```

#### <a name="get-group"></a>Groep ophalen

##### <a name="request-8"></a>Schot

*/Groups/40734ae655284ad3abcc ophalen? excludedAttributes = leden HTTP/1.1*

##### <a name="response-8"></a>Beantwoord
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "40734ae655284ad3abcc",
    "externalId": "60f1bb27-2e1e-402d-bcc4-ec999564a194",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "displayName": "displayName",
}
```

#### <a name="get-group-by-displayname"></a>Groep op displayName ophalen

##### <a name="request-9"></a>Schot
*/Groups ophalen? excludedAttributes = leden & filter = DisplayName EQ "displayName" HTTP/1.1*

##### <a name="response-9"></a>Beantwoord

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
        "id": "8c601452cc934a9ebef9",
        "externalId": "0db508eb-91e2-46e4-809c-30dcbda0c685",
        "meta": {
            "resourceType": "Group",
            "created": "2018-03-27T22:02:32.000Z",
            "lastModified": "2018-03-27T22:02:32.000Z",
            
        },
        "displayName": "displayName",
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}
```

#### <a name="update-group-non-member-attributes"></a>Update groep [niet-lideigenschappen]

##### <a name="request-10"></a>Schot

*PATCH/groups/fa2ce26709934589afc5 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "displayName",
        "value": "1879db59-3bdf-4490-ad68-ab880a269474updatedDisplayName"
    }]
}
```

##### <a name="response-10"></a>Beantwoord

*HTTP/1.1 204 geen inhoud*

### <a name="update-group-add-members"></a>Groep bijwerken [leden toevoegen]

##### <a name="request-11"></a>Schot

*PATCH/groups/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Add",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response-11"></a>Beantwoord

*HTTP/1.1 204 geen inhoud*

#### <a name="update-group-remove-members"></a>Groep bijwerken [leden verwijderen]

##### <a name="request-12"></a>Schot

*PATCH/groups/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Remove",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response-12"></a>Beantwoord

*HTTP/1.1 204 geen inhoud*

#### <a name="delete-group"></a>Groep verwijderen

##### <a name="request-13"></a>Schot

*/Groups/cdb1ce18f65944079d37 HTTP/1.1 verwijderen*

##### <a name="response-13"></a>Beantwoord

*HTTP/1.1 204 geen inhoud*

### <a name="security-requirements"></a>Beveiligings vereisten
**TLS-protocol versies**

De enige acceptabele TLS-protocol versies zijn TLS 1,2 en TLS 1,3. Er zijn geen andere versies van TLS toegestaan. Er is geen SSL-versie toegestaan. 
- RSA-sleutels moeten ten minste 2.048 bits zijn.
- ECC-sleutels moeten ten minste 256 bits zijn en worden gegenereerd met een goedgekeurde elliptische curve


**Sleutel lengten**

Alle services moeten X. 509-certificaten gebruiken die zijn gegenereerd met cryptografische sleutels van voldoende lengte, wat betekent het volgende:

**Coderings suites**

Alle services moeten worden geconfigureerd voor het gebruik van de volgende coderings suites in de exacte volg orde die hieronder is opgegeven. Houd er rekening mee dat als u alleen een RSA-certificaat hebt, de installatie van de ECDSA-coderings suites geen effect heeft. </br>

Minimale staaf voor TLS 1,2-coderings suites:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384


## <a name="step-3-build-a-scim-endpoint"></a>Stap 3: een SCIM-eind punt bouwen

Nu u uw schema hebt ontworpen en de Azure AD SCIM-implementatie hebt begrepen, kunt u aan de slag met het ontwikkelen van uw SCIM-eind punt. In plaats van helemaal zelf te beginnen en de implementatie helemaal zelf te bouwen, kunt u vertrouwen op een aantal open source SCIM-bibliotheken die zijn gepubliceerd door de SCIM-commuinty.  
De open-source .NET core- [referentie code](https://aka.ms/SCIMReferenceCode) die door het Azure AD-inrichtings team is gepubliceerd, is een van de bronnen waarmee u uw ontwikkeling kunt starten. Nadat u het SCIM-eind punt hebt gemaakt, moet u het testen. U kunt de verzameling [postman-testen](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) gebruiken die deel uitmaakt van de referentie code of door loop de [hierboven](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#user-operations)vermelde voorbeeld aanvragen/antwoorden.  

Opmerking: de referentie code is bedoeld om aan de slag te gaan met het bouwen van uw SCIM-eind punt en wordt ' AS IS ' gegeven. Bijdragen van de community zijn welkom bij het bouwen en onderhouden van de code. 

## <a name="step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client"></a>Stap 4: uw SCIM-eind punt integreren met de Azure AD SCIM-client

Azure AD kan worden geconfigureerd voor het automatisch inrichten van toegewezen gebruikers en groepen aan toepassingen die een specifiek profiel van het [SCIM 2,0-protocol](https://tools.ietf.org/html/rfc7644)implementeren. De details van het profiel zijn beschreven in [stap 2: inzicht in de implementatie van Azure AD scim](#step-2-understand-the-azure-ad-scim-implementation).

Neem contact op met de provider van uw toepassing of de documentatie van de toepassingsprovider van uw voor overzichten van compatibiliteit met deze vereisten.

> [!IMPORTANT]
> De Azure AD SCIM-implementatie is gebaseerd op de Azure AD User Provisioning-Service, die is ontworpen om gebruikers voortdurend te synchroniseren tussen Azure AD en de doel toepassing, en een zeer specifieke set standaard bewerkingen uit te voeren. Het is belang rijk dat u dit gedrag begrijpt om inzicht te krijgen in het gedrag van de Azure AD SCIM-client. Zie voor meer informatie de sectie [cyclussen inrichten: eerste en incrementeel](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) in de werking van [inrichten](how-provisioning-works.md).

### <a name="getting-started"></a>Aan de slag

Toepassingen die ondersteuning bieden voor het SCIM-profiel dat wordt beschreven in dit artikel kunnen worden verbonden met Azure Active Directory met de functie 'buiten de galerie application' van de Azure AD-toepassingsgalerie. Eenmaal verbinding hebben, voert Azure AD een synchronisatieproces voor elke 40 minuten waar deze query's van de toepassing SCIM eindpunt voor toegewezen gebruikers en groepen, en maakt of wijzigt u ze op basis van de details van de toewijzing.

**Verbinding maken met een toepassing die ondersteuning biedt voor SCIM:**

1. Meld u aan bij de [Azure Active Directory Portal](https://aad.portal.azure.com). Houd er rekening mee dat u toegang krijgt tot een gratis proef versie voor Azure Active Directory met P2-licenties door u aan te melden voor het [ontwikkelaars programma](https://developer.microsoft.com/office/dev-program)
2. Selecteer **bedrijfs toepassingen** in het linkerdeel venster. Er wordt een lijst met alle geconfigureerde apps weer gegeven, met inbegrip van apps die zijn toegevoegd vanuit de galerie.
3. Selecteer **+ nieuwe toepassing** > **alle** > **niet-galerie toepassing**.
4. Voer een naam in voor uw toepassing en selecteer **toevoegen** om een app-object te maken. De nieuwe app wordt toegevoegd aan de lijst met bedrijfs toepassingen en wordt geopend op het scherm voor het beheren van apps.

   ![scherm afbeelding toont de Azure AD-toepassings galerie](media/use-scim-to-provision-users-and-groups/scim-figure-2a.png)<br/>
   *Azure AD-toepassings galerie*

5. Selecteer in het scherm voor het beheren van apps de optie **inrichten** in het linkerdeel venster.
6. Selecteer in het menu **inrichtings modus** de optie **automatisch**.

   ![voor beeld: de inrichtings pagina van een app in de Azure Portal](media/use-scim-to-provision-users-and-groups/scim-figure-2b.png)<br/>
   *Inrichting configureren in de Azure Portal*

7. Voer in het veld **Tenant-URL** de URL in van het scim-eind punt van de toepassing. Voorbeeld: https://api.contoso.com/scim/
8. Als het SCIM-eind punt een OAuth Bearer-token van een andere uitgever dan Azure AD vereist, kopieert u het vereiste OAuth Bearer-token naar het veld optionele **geheime token** . Als dit veld leeg blijft, bevat Azure AD een OAuth Bearer-token dat is uitgegeven door Azure AD met elke aanvraag. Apps die gebruikmaken van Azure AD als id-provider kunnen dit door Azure AD uitgegeven token valideren. 
   > [!NOTE]
   > Het is ***niet*** raadzaam dit veld leeg te laten en te vertrouwen op een token dat door Azure AD wordt gegenereerd. Deze optie is voornamelijk beschikbaar voor test doeleinden.
9. Selecteer **verbinding testen** om te laten proberen Azure Active Directory verbinding te maken met het scim-eind punt. Als de poging mislukt, wordt er informatie over de fout weer gegeven.  

    > [!NOTE]
    > **Test verbindings** QUERY'S het scim-eind punt voor een gebruiker die niet bestaat, met behulp van een wille keurige GUID als de overeenkomende eigenschap die is geselecteerd in de Azure AD-configuratie. Het verwachte juiste antwoord is HTTP 200 OK met een leeg SCIM ListResponse-bericht.

10. Als de pogingen om verbinding te maken met de toepassing slagen, selecteert u **Opslaan** om de beheerders referenties op te slaan.
11. In de sectie **toewijzingen** zijn er twee sets met [kenmerk toewijzingen](customize-application-attributes.md): één voor gebruikers objecten en één voor groeps objecten. Selecteren om te controleren van de kenmerken die worden gesynchroniseerd vanuit Active Directory van Azure aan uw app. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers en groepen in uw app voor bijwerk bewerkingen. Selecteer **Opslaan** om eventuele wijzigingen door te voeren.

    > [!NOTE]
    > U kunt desgewenst uitschakelen door het uitschakelen van de toewijzing van 'groepen'-synchronisatie van de groepsobjecten worden weergegeven.

12. Onder **instellingen**definieert het **bereik** veld welke gebruikers en groepen worden gesynchroniseerd. Selecteer **alleen toegewezen gebruikers en groepen synchroniseren** (aanbevolen) om gebruikers en groepen te synchroniseren die zijn toegewezen op het tabblad **gebruikers en groepen** .
13. Wanneer de configuratie is voltooid, stelt u de **inrichtings status** **in op aan**.
14. Selecteer **Opslaan** om de Azure AD-inrichtings service te starten.
15. Als u alleen toegewezen gebruikers en groepen wilt synchroniseren (aanbevolen), selecteert u het tabblad **gebruikers en groepen** en wijst u de gebruikers of groepen toe die u wilt synchroniseren.

Zodra de eerste cyclus is gestart, kunt u **inrichtings logboeken** selecteren in het linkerdeel venster om de voortgang te controleren. hier worden alle acties weer gegeven die door de inrichtings service in uw app worden uitgevoerd. Zie [rapportage over het automatisch inrichten van gebruikers accounts](check-status-user-account-provisioning.md)voor meer informatie over het lezen van de Azure AD-inrichtings Logboeken.

> [!NOTE]
> De eerste cyclus duurt langer dan de volgende synchronisaties, wat ongeveer elke 40 minuten gebeurt, zolang de service wordt uitgevoerd.

## <a name="step-5-publish-your-application-to-the-azure-ad-application-gallery"></a>Stap 5: Publiceer uw toepassing in de Azure AD-toepassings galerie

Als u een toepassing bouwt die wordt gebruikt door meer dan één Tenant, kunt u deze beschikbaar maken in de Azure AD-toepassings galerie. Dit maakt het eenvoudig voor organisaties om de toepassing te detecteren en inrichtingen te configureren. Het publiceren van uw app in de Azure AD-galerie en het beschikbaar maken van de inrichting voor anderen is eenvoudig. Bekijk de stappen die [hier](../develop/howto-app-gallery-listing.md)worden beschreven. Micro soft werkt samen met u om uw toepassing te integreren in onze galerie, uw eind punt te testen en onboarding- [documentatie](../saas-apps/tutorial-list.md) voor klanten te publiceren. 

### <a name="gallery-onboarding-checklist"></a>Controle lijst voor onboarding van galerie
Volg de onderstaande controle lijst om ervoor te zorgen dat uw toepassing voorbereid snelle en klanten een soepele implementatie-ervaring hebben. De gegevens worden verzameld van u bij het onboarden naar de galerie. 
> [!div class="checklist"]
> * Een [SCIM 2,0](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#step-2-understand-the-azure-ad-scim-implementation) -gebruikers-en-groeps eindpunt ondersteunen (er is slechts één vereist, maar beide worden aanbevolen)
> * Ten minste 25 aanvragen per seconde per Tenant ondersteunen (vereist)
> * Technische hulp en ondersteunings contacten tot stand brengen om klanten te begeleiden post galerie voorbereiden (vereist)
> * 3 niet-verlopende test referenties voor uw toepassing (vereist)
> * Ondersteuning voor de OAuth-autorisatie code subsidie of een lang bewaard token zoals hieronder wordt beschreven (vereist)
> * Een technisch en ondersteunings punt tot stand brengen om klanten te ondersteunen bij het voorbereiden van de galerie van berichten (vereist)
> * Ondersteuning voor het bijwerken van meerdere groepslid maatschappen met één PATCH (aanbevolen) 
> * Uw SCIM-eind punt openbaar documenteren (aanbevolen) 
> * [Ondersteuning voor schema detectie](https://tools.ietf.org/html/rfc7643#section-6) (aanbevolen)


### <a name="authorization-for-provisioning-connectors-in-the-application-gallery"></a>Autorisatie voor het inrichten van connectors in de toepassings galerie
De SCIM spec definieert geen SCIM-specifiek schema voor verificatie en autorisatie. Het is afhankelijk van het gebruik van bestaande industrie normen. De Azure AD-inrichtings client ondersteunt twee autorisatie methoden voor toepassingen in de galerie. 

|Autorisatie methode|Professionals|Nadelen|Ondersteuning|
|--|--|--|--|
|Gebruikers naam en wacht woord (niet aanbevolen of niet ondersteund door Azure AD)|Eenvoudig te implementeren|Onveilig: [uw PA $ $Word is niet van belang](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)|Per geval ondersteund voor galerij-apps. Niet ondersteund voor niet-galerij-apps.|
|Token met lange levens drager|Voor tokens met een lange levens duur hoeft geen gebruiker aanwezig te zijn. Ze kunnen eenvoudig worden gebruikt bij het instellen van de inrichting.|Tokens met een lange levens duur kunnen moeilijk worden gedeeld met een beheerder zonder gebruik te maken van onveilige methoden zoals e-mail. |Ondersteund voor Gallery-en niet-galerij-apps. |
|Verleende OAuth-autorisatie code|Toegangs tokens zijn veel korter dan wacht woorden en beschikken over een mechanisme voor automatisch vernieuwen die de tokens van de lange levens drager niet hebben.  Er moet een echte gebruiker aanwezig zijn tijdens de eerste autorisatie, waarbij een verantwoordelijkheids niveau wordt toegevoegd. |Hiervoor moet een gebruiker aanwezig zijn. Als de gebruiker de organisatie verlaat, is het token ongeldig en moet de autorisatie opnieuw worden uitgevoerd.|Ondersteund voor galerie-apps. Er wordt ondersteuning geboden voor niet-galerij-apps.|
|Verleende OAuth-client referenties|Toegangs tokens zijn veel korter dan wacht woorden en beschikken over een mechanisme voor automatisch vernieuwen die de tokens van de lange levens drager niet hebben. Zowel de autorisatie code Grant als de client referenties geven hetzelfde type toegangs token maken, zodat het verplaatsen tussen deze methoden transparant is voor de API.  Het inrichten kan volledig worden geautomatiseerd en nieuwe tokens kunnen zonder tussen komst van de gebruiker worden aangevraagd. ||Niet ondersteund voor apps uit de galerie en niet-galerij. Ondersteuning bevindt zich in onze achterstand.|

[!NOTE] Het is niet raadzaam om het veld token leeg te laten in de aangepaste app-gebruikers interface van de Azure AD-inrichtings configuratie. Het gegenereerde token is voornamelijk beschikbaar voor test doeleinden.

**Overdrachts stroom OAuth-autorisatie code:** De inrichtings service ondersteunt de [toekenning van autorisatie code](https://tools.ietf.org/html/rfc6749#page-24). Na het verzenden van uw aanvraag voor het publiceren van uw app in de galerie, zal ons team met u samen werken om de volgende informatie te verzamelen:
*  Autorisatie-URL: een URL door de client voor het verkrijgen van autorisatie van de resource-eigenaar via omleiding van gebruikers agent. De gebruiker wordt omgeleid naar deze URL om toegang te autoriseren. 
*  Token Exchange-URL: een URL door de client voor het uitwisselen van een autorisatie machtiging voor een toegangs token, meestal met client verificatie.
*  Client-ID: de autorisatie server geeft de geregistreerde client een client-id. Dit is een unieke teken reeks die de registratie gegevens vertegenwoordigt die door de client worden verstrekt.  De client-id is geen geheim. het wordt blootgesteld aan de resource-eigenaar en **mag niet** alleen voor client verificatie worden gebruikt.  
*  Client geheim: het client geheim is een geheim dat is gegenereerd door de autorisatie server. Dit moet een unieke waarde zijn die alleen bekend is bij de autorisatie server. 

OAuth v1 wordt niet ondersteund vanwege de bloot stelling aan het client geheim. OAuth v2 wordt ondersteund.  

Aanbevolen procedures (aanbevolen maar niet vereist):
* Ondersteuning voor meerdere omleidings-Url's. Beheerders kunnen het inrichten configureren van zowel ' portal.azure.com ' als ' aad.portal.azure.com '. Ondersteuning voor meerdere omleidings-Url's zorgt ervoor dat gebruikers toegang kunnen verlenen vanuit een van de portals.
* Meerdere geheimen ondersteunen om te zorgen voor een soepele geheime verlenging, zonder uitval tijd. 

**Lange levens duur van OAuth Bearer-tokens:** Als uw toepassing geen ondersteuning biedt voor de overdrachts stroom van de OAuth-autorisatie code, kunt u ook een lang bewaarde OAuth Bearer-token genereren dan die een beheerder kan gebruiken om de inrichtings integratie in te stellen. Het token moet permanent zijn, anders wordt de inrichtings taak in [quarantaine geplaatst](application-provisioning-quarantine-status.md) wanneer het token verloopt. Dit token moet kleiner zijn dan 1 KB.  

Laat het ons weten op [UserVoice](https://aka.ms/appprovisioningfeaturerequest)voor aanvullende verificatie-en autorisatie methoden.

### <a name="gallery-go-to-market-launch-check-list"></a>Controle lijst voor het starten van de galerie
We raden u aan om uw bestaande documentatie bij te werken en de integratie in uw marketing kanalen te verg Roten om de bewustmaking en vraag van onze gezamenlijke integratie te stimuleren.  Hieronder vindt u een aantal controle activiteiten die u het beste kunt volt ooien om de start te ondersteunen

* **Gereedheid voor verkoop-en klant ondersteuning.** Zorg ervoor dat uw verkoop-en ondersteunings teams op de hoogte zijn en dat ze kunnen praten met de integratie mogelijkheden. Geef uw verkoop-en ondersteunings team een korte beschrijving en voeg de integratie in uw verkoop materialen toe. 
* **Blog bericht en/of druk op release.** Maak een blog bericht of druk op release met een beschrijving van de gezamenlijke integratie, de voor delen en hoe u aan de slag gaat. [Voor beeld: Imprivata en Azure Active Directory pers release](https://www.imprivata.com/company/press/imprivata-introduces-iam-cloud-platform-healthcare-supported-microsoft) 
* **Sociale media.** Maak gebruik van uw sociale media, zoals Twitter, Facebook of LinkedIn, om de integratie van uw klanten te stimuleren. Zorg ervoor dat @AzureAD worden toegevoegd, zodat we uw post kunnen retweets. [Voor beeld: Imprivata Twitter post](https://twitter.com/azuread/status/1123964502909779968)
* **Marketing website.** Maak of werk uw marketing pagina's toe (bijvoorbeeld integratie pagina, partner pagina, prijs pagina enz.) om de beschik baarheid van de gezamenlijke integratie op te halen. [Voor beeld: Pingboard-integratie pagina](https://pingboard.com/org-chart-for), [Smart Sheet-integratie pagina](https://www.smartsheet.com/marketplace/apps/microsoft-azure-ad), pagina met [Monday.com prijzen](https://monday.com/pricing/) 
* **Technische documentatie.** Maak een Help Center-artikel of technische documentatie over hoe klanten aan de slag kunnen. [Voor beeld: Envoy + Microsoft Azure Active Directory-integratie.](https://envoy.help/en/articles/3453335-microsoft-azure-active-directory-integration/
) 
* **Communicatie van de klant.** Waarschuw klanten van de nieuwe integratie via uw klant communicatie (maandelijkse nieuws brieven, e-mail campagnes, opmerkingen bij de release van het product). 

### <a name="allow-ip-addresses-used-by-the-azure-ad-provisioning-service-to-make-scim-requests"></a>IP-adressen die worden gebruikt door de Azure AD-inrichtings service toestaan om SCIM-aanvragen te maken

Bepaalde apps staan inkomend verkeer naar hun app toe. De Azure AD-inrichtings service werkt alleen zoals verwacht als de gebruikte IP-adressen zijn toegestaan. Zie voor een lijst met IP-adressen voor elke servicetag/regio het JSON-bestand- [Azure IP-bereiken en de service Tags – open bare Cloud](https://www.microsoft.com/download/details.aspx?id=56519). U kunt deze Ip's naar behoefte downloaden en Program meren in uw firewall. De gereserveerde IP-bereiken voor Azure AD-inrichting vindt u onder ' AzureActiveDirectoryDomainServices '.

## <a name="related-articles"></a>Verwante artikelen

* [Gebruikers inrichten en het ongedaan maken van de inrichting van SaaS-apps automatiseren](user-provisioning.md)
* [Kenmerk toewijzingen voor gebruikers inrichting aanpassen](customize-application-attributes.md)
* [Expressies schrijven voor kenmerk toewijzingen](functions-for-customizing-application-data.md)
* [Filters voor het inrichten van gebruikers in bereik](define-conditional-rules-for-provisioning-user-accounts.md)
* [Meldingen voor het inrichten van accounts](user-provisioning.md)
* [Lijst met zelf studies voor het integreren van SaaS-apps](../saas-apps/tutorial-list.md)
