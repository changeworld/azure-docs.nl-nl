---
title: Toepassings typen voor micro soft Identity platform | Azure
description: De typen apps en scenario's die worden ondersteund door het micro soft Identity platform (v 2.0)-eind punt.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 494a06b8-0f9b-44e1-a7a2-d728cf2077ae
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/06/2019
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 94cddf097f2a9e51f061909f6bdd3dcd82f18bfe
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79262527"
---
# <a name="application-types-for-microsoft-identity-platform"></a>Toepassings typen voor micro soft Identity platform

Het micro soft Identity platform (v 2.0)-eind punt ondersteunt verificatie voor verschillende moderne app-architecturen, die allemaal zijn gebaseerd op gestandaardiseerde protocollen [OAuth 2,0 of OpenID Connect Connect](active-directory-v2-protocols.md). In dit artikel worden de typen apps beschreven die u kunt bouwen met behulp van micro soft Identity platform, ongeacht uw voorkeurs taal of-platform. De informatie is ontworpen om u te helpen bij het begrijpen van scenario's met een hoog niveau voordat u [met de code aan de slag gaat](v2-overview.md#getting-started).

> [!NOTE]
> Het micro soft Identity platform-eind punt biedt geen ondersteuning voor alle scenario's en functies van Azure Active Directory (Azure AD). Lees over [micro soft Identity platform-beperkingen](active-directory-v2-limitations.md)om te bepalen of u het micro soft Identity platform-eind punt moet gebruiken.

## <a name="the-basics"></a>De basisbeginselen

U moet elke app registreren die gebruikmaakt van het micro soft Identity platform-eind punt in de nieuwe [app-registraties-Portal](https://go.microsoft.com/fwlink/?linkid=2083908). Het registratie proces van de app verzamelt en wijst deze waarden voor uw app toe:

* Een **toepassings-id (client)** waarmee uw app op unieke wijze wordt geïdentificeerd
* Een **omleidings-URI** die u kunt gebruiken om antwoorden terug te sturen naar uw app
* Een paar andere scenario-specifieke waarden, zoals ondersteunde account typen

Meer informatie over hoe u [een app kunt registreren](quickstart-register-app.md).

Nadat de app is geregistreerd, communiceert de app met het micro soft-identiteits platform door aanvragen naar het eind punt te verzenden. We bieden open-source frameworks en bibliotheken die de details van deze aanvragen verwerken. U hebt ook de optie om de verificatie logica zelf te implementeren door aanvragen voor deze eind punten te maken:

```HTTP
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

## <a name="single-page-apps-javascript"></a>Apps met één pagina (Java script)

Veel moderne apps hebben een front-end van de app met één pagina die voornamelijk is geschreven in Java script. Vaak wordt deze geschreven met behulp van een kader zoals hoek, reageren of Vue. Het micro soft Identity platform-eind punt ondersteunt deze apps met behulp van de [impliciete OAuth 2,0-stroom](v2-oauth2-implicit-grant-flow.md).

In deze stroom ontvangt de app tokens rechtstreeks van het micro soft Identity platform Authorization-eind punt, zonder server-naar-server-uitwisselingen. Alle verificatie logica en sessie afhandeling vindt volledig plaats in de Java script-client, zonder extra pagina omleidingen.

![Toont de impliciete verificatie stroom](./media/v2-app-types/convergence-scenarios-implicit.svg)

Als u dit scenario in actie wilt zien, kunt u een van de app-code voorbeelden van één pagina proberen in de sectie [micro soft Identity platform aan de slag](v2-overview.md#getting-started) .

## <a name="web-apps"></a>Web-apps

Voor web-apps (.NET, PHP, Java, Ruby, Python, node) die de gebruiker via een browser opent, kunt u [OpenID Connect Connect](active-directory-v2-protocols.md) gebruiken voor aanmelding door gebruikers. In OpenID Connect Connect wordt de web-app een ID-token ontvangen. Een ID-token is een beveiligings token waarmee de identiteit van de gebruiker wordt geverifieerd en informatie wordt verstrekt over de gebruiker in de vorm van claims:

```JSON
// Partial raw ID token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded ID token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

Meer informatie over de verschillende typen tokens die worden gebruikt in het micro soft Identity platform-eind punt zijn beschikbaar in de naslag informatie voor het [toegangs token](access-tokens.md) en de [referentie id_token](id-tokens.md)

In web server-apps neemt de aanmeldings verificatie stroom de volgende stappen op hoog niveau:

![Toont de verificatie stroom van de web-app](./media/v2-app-types/convergence-scenarios-webapp.svg)

U kunt de identiteit van de gebruiker controleren door de ID-token te valideren met een open bare handtekening sleutel die wordt ontvangen van het micro soft Identity platform-eind punt. Er wordt een sessie cookie ingesteld die kan worden gebruikt om de gebruiker te identificeren op volgende pagina-aanvragen.

Als u dit scenario in actie wilt zien, kunt u een van de voor beelden van de aanmeldings code voor de web-app in het gedeelte [aan de slag met micro soft Identity platform](v2-overview.md#getting-started) uitproberen.

Naast het gebruik van een eenvoudige aanmelding moet een webserver-app mogelijk toegang hebben tot een andere webservice, zoals een REST API. In dit geval maakt de webserver-app deel uit van een gecombineerde OpenID connect-verbinding en OAuth 2,0-stroom met behulp van de [OAuth 2,0-autorisatie code stroom](active-directory-v2-protocols.md). Meer informatie over dit scenario vindt u [in aan de slag met web apps en Web-api's](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md).

## <a name="web-apis"></a>Web-API's

U kunt het micro soft Identity platform-eind punt gebruiken voor het beveiligen van webservices, zoals de REST Web API van uw app. Web-Api's kunnen worden geïmplementeerd in talloze platforms en talen. Ze kunnen ook worden geïmplementeerd met behulp van HTTP-triggers in Azure Functions. In plaats van ID-tokens en sessie cookies gebruikt een web-API een OAuth 2,0-toegangs token om de gegevens te beveiligen en inkomende aanvragen te verifiëren. De aanroeper van een web-API voegt een toegangs token toe in de autorisatie-header van een HTTP-aanvraag, zoals:

```HTTP
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

De Web-API gebruikt het toegangs token om de identiteit van de API-aanroeper te controleren en om informatie over de aanroeper op te halen uit claims die zijn gecodeerd in het toegangs token. Meer informatie over de verschillende typen tokens die worden gebruikt in het micro soft Identity platform-eind punt zijn beschikbaar in de naslag informatie voor het [toegangs token](access-tokens.md) en het [id_token](id-tokens.md) .

Een web-API kan gebruikers de bevoegdheid geven om zich aan te melden of om specifieke functionaliteit of gegevens uit te scha kelen door machtigingen (ook wel [scopes](v2-permissions-and-consent.md)genoemd) te bieden. Voor een aanroep-app om machtigingen voor een bereik te verkrijgen, moet de gebruiker toestemming geven voor het bereik tijdens een stroom. Het micro soft Identity platform-eind punt vraagt de gebruiker om toestemming en registreert vervolgens de machtigingen in alle toegangs tokens die de Web-API ontvangt. De Web-API valideert de toegangs tokens die op elke aanroep worden ontvangen en voert autorisatie controles uit.

Een web-API kan toegangs tokens ontvangen van alle typen apps, waaronder web server apps, desktop-en Mobile apps, apps met één pagina, daemons aan server zijde en zelfs andere web-Api's. De stroom op hoog niveau voor een web-API ziet er als volgt uit:

![Hiermee wordt de Web-API-verificatie stroom weer gegeven](./media/v2-app-types/convergence-scenarios-webapi.svg)

Als u wilt weten hoe u een web-API kunt beveiligen met behulp van OAuth2-toegangs tokens, raadpleegt u de voor beelden van de Web-API in de sectie aan de slag met [micro soft Identity platform](v2-overview.md#getting-started) .

In veel gevallen moeten Web-Api's ook uitgaande aanvragen indienen bij andere stroomafwaartse Web-Api's die worden beveiligd door het micro soft Identity-platform. Om dit te doen, kunnen Web-Api's gebruikmaken van de **namen van** de stroom, waarmee de Web-API een binnenkomend toegangs token kan uitwisselen voor een ander toegangs token dat in uitgaande aanvragen moet worden gebruikt. Zie voor meer informatie [micro soft Identity platform and OAuth 2,0 voor-of-flow](v2-oauth2-on-behalf-of-flow.md).

## <a name="mobile-and-native-apps"></a>Mobiele en systeemeigen apps

Apps die zijn geïnstalleerd op apparaten, zoals mobiele en desktop-apps, hebben vaak toegang tot back-end-services of Web-Api's waarmee gegevens worden opgeslagen en functies kunnen worden uitgevoerd namens een gebruiker. Deze apps kunnen aanmelding en autorisatie toevoegen aan back-end-services met behulp van de [OAuth 2,0-autorisatie code stroom](v2-oauth2-auth-code-flow.md).

In deze stroom ontvangt de app een autorisatie code van het micro soft Identity platform-eind punt wanneer de gebruiker zich aanmeldt. De autorisatie code vertegenwoordigt de machtiging van de app voor het aanroepen van back-end-services namens de gebruiker die zich heeft aangemeld. De app kan de autorisatie code op de achtergrond voor een OAuth 2,0-toegangs token en een vernieuwings token uitwisselen. De app kan het toegangs token gebruiken om te verifiëren bij Web-Api's in HTTP-aanvragen en het vernieuwings token gebruiken om nieuwe toegangs tokens op te halen wanneer oudere toegangs tokens verlopen.

![Toont de systeem eigen app-verificatie stroom](./media/v2-app-types/convergence-scenarios-native.svg)

## <a name="daemons-and-server-side-apps"></a>Daemons en apps aan de server zijde

Apps die langlopende processen hebben of die werken zonder interactie met een gebruiker, hebben ook een manier nodig om toegang te krijgen tot beveiligde bronnen, zoals web-Api's. Deze apps kunnen tokens verifiëren en ophalen met behulp van de identiteit van de app, in plaats van de gedelegeerde identiteit van een gebruiker, met de OAuth 2,0-client referenties stroom. U kunt de identiteit van de app bewijzen met behulp van een client geheim of certificaat. Zie de [.net core daemon-console toepassing met micro soft Identity platform](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2)voor meer informatie.

In deze stroom communiceert de app rechtstreeks met het `/token`-eind punt om toegang te verkrijgen:

![Toont de verificatie stroom van de daemon-app](./media/v2-app-types/convergence-scenarios-daemon.svg)

Als u een daemon-app wilt maken, raadpleegt u de [documentatie van client referenties](v2-oauth2-client-creds-grant-flow.md)of probeert u een [.net-voorbeeld toepassing](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).
