---
title: OAuth 2,0 impliciete toekennings stroom-micro soft Identity-platform | Azure
description: Beveilig apps met één pagina met een impliciete stroom van micro soft Identity platform.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 3605931f-dc24-4910-bb50-5375defec6a8
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 4f35162be513873637f9a87260410d891fdace10
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78300465"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Micro soft Identity-platform en impliciete toekennings stroom

Met het micro soft Identity platform-eind punt kunt u gebruikers aan uw apps met één pagina ondertekenen met zowel privé-als werk-of school accounts van micro soft. Eén pagina en andere Java script-apps die voornamelijk worden uitgevoerd in een browser, hebben een paar interessante uitdagingen wanneer de verificatie is:

* De beveiligings kenmerken van deze apps zijn aanzienlijk anders dan traditionele webtoepassingen op de server.
* Veel autorisatie servers en id-providers bieden geen ondersteuning voor CORS-aanvragen.
* De volledige pagina browser omleidingen van de app worden met name invasief voor de gebruikers ervaring.

Voor deze toepassingen (AngularJS, wordt. js, reageren. js, enzovoort) ondersteunt het micro soft Identity-platform de OAuth 2,0 impliciete toekennings stroom. De impliciete stroom wordt beschreven in de [OAuth 2,0-specificatie](https://tools.ietf.org/html/rfc6749#section-4.2). Het belangrijkste voor deel is dat de app tokens van het micro soft Identity-platform kan ophalen zonder een back-end-server referentie-uitwisseling uit te voeren. Hiermee kan de app zich aanmelden bij de gebruiker, de sessie beheren en tokens ophalen voor andere web-Api's in de Java script-code van de client. Er zijn enkele belang rijke beveiligings overwegingen waarbij rekening moet worden gehouden bij het gebruik van de impliciete stroom om de [client](https://tools.ietf.org/html/rfc6749#section-10.3) -en [gebruikers imitatie](https://tools.ietf.org/html/rfc6749#section-10.3)te gebruiken.

In dit artikel wordt beschreven hoe u direct kunt Program meren met het protocol in uw toepassing.  Als dat mogelijk is, kunt u het beste de ondersteunde micro soft-verificatie bibliotheken (MSAL) gebruiken in plaats van [tokens te verkrijgen en beveiligde web-api's](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)aan te roepen.  Bekijk ook de voor beeld- [apps die gebruikmaken van MSAL](sample-v2-code.md).

Als u echter liever geen bibliotheek gebruikt in een app met één pagina en zelf protocol berichten verzendt, volgt u de algemene stappen hieronder.

> [!NOTE]
> Niet alle Azure Active Directory-scenario's en-functies (Azure AD) worden ondersteund door het micro soft Identity platform-eind punt. Lees over [micro soft Identity platform-beperkingen](active-directory-v2-limitations.md)om te bepalen of u het micro soft Identity platform-eind punt moet gebruiken.

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Geschikte scenario's voor de impliciete toekenning van OAuth2

De OAuth2-specificatie verklaart dat de impliciete toekenning is ontwikkeld voor het inschakelen van toepassingen voor de gebruikers agent, dat wil zeggen java script-toepassingen die worden uitgevoerd in een browser. Het definiëren van kenmerken van dergelijke toepassingen is dat Java script-code wordt gebruikt voor toegang tot Server bronnen (meestal een web-API) en om de gebruikers ervaring van de toepassing dienovereenkomstig bij te werken. Toepassingen zoals Gmail of Outlook Web Access beschouwen: wanneer u een bericht uit uw postvak in selecteert, wordt in alleen het venster visualisatie van berichten de nieuwe selectie weer gegeven, terwijl de rest van de pagina ongewijzigd blijft. Dit kenmerk is in tegens telling tot traditionele op omleiden gebaseerde web-apps, waarbij elke interactie van de gebruiker leidt tot een volledige pagina-terugposting en een volledige pagina-rendering van de nieuwe server reactie.

Toepassingen die gebruikmaken van de Java script-benadering, worden toepassingen met één pagina of SPAs genoemd. Het idee is dat deze toepassingen alleen een eerste HTML-pagina en bijbehorende java script gebruiken, waarbij alle volgende interacties worden aangedreven door Web API-aanroepen via Java script. Hybride benaderingen, waarbij de toepassing voornamelijk wordt teruggestuurd, maar af en toe wordt uitgevoerd, zijn niet ongebruikelijk: de discussie over impliciet stroom gebruik is ook relevant voor die.

Toepassingen op basis van omleiding beveiligen doorgaans hun aanvragen via cookies, maar deze benadering werkt ook niet voor Java script-toepassingen. Cookies werken alleen voor het domein waarvoor ze zijn gegenereerd, terwijl Java script-aanroepen kunnen worden omgeleid naar andere domeinen. Dit is in feite het geval: het aanroepen van Microsoft Graph-API, Office API, Azure API-alles dat zich buiten het domein bevindt waar de toepassing wordt aangeboden. Een groeiende trend voor Java script-toepassingen is om helemaal geen back-end te hebben, met 100% op Web-Api's van derden om hun zakelijke functie te implementeren.

Momenteel is de voorkeurs methode voor het beveiligen van een web-API het gebruik van de OAuth2 Bearer-token benadering, waarbij elke aanroep vergezeld gaat van een OAuth2-toegangs token. De Web-API onderzoekt het token voor inkomende toegang en, als dit de benodigde bereiken vindt, wordt toegang verleend tot de aangevraagde bewerking. De impliciete stroom biedt een handig mechanisme voor Java script-toepassingen voor het verkrijgen van toegangs tokens voor een web-API en biedt talrijke voor delen ten opzichte van cookies:

* Tokens kunnen betrouwbaar worden verkregen zonder dat dit nodig is voor cross-Origin-aanroepen: verplichte registratie van de omleidings-URI waarnaar tokens worden geretourneerd, garandeert dat de tokens niet zijn verplaatst
* Java script-toepassingen kunnen zoveel toegangs tokens verkrijgen als ze nodig hebben, voor net zoveel web-Api's waarop ze zijn gericht, zonder beperkingen voor domeinen
* HTML5-functies, zoals sessie of lokale opslag, hebben volledige controle over het opslaan van tokens en levensduur beheer, terwijl cookies beheer dekkend is voor de app
* Toegangs tokens zijn niet vatbaar voor CSRF-aanvallen (cross-site request vervalsing)

In de impliciete toekennings stroom worden geen vernieuwings tokens uitgegeven, voornamelijk om veiligheids redenen. Een vernieuwings token is niet zo smal als toegangs tokens, waardoor veel meer energie wordt toegewezen, waardoor veel meer schade wordt toegebracht bij het uitvallen van het probleem. In de impliciete stroom worden tokens geleverd in de URL, waardoor het risico van interceptie hoger is dan in de autorisatie code toekenning.

Een Java script-toepassing heeft echter een ander mechanisme voor het vernieuwen van toegangs tokens zonder dat de gebruiker herhaaldelijk om referenties wordt gevraagd. De toepassing kan een verborgen iframe gebruiken om nieuwe token aanvragen uit te voeren op het autorisatie-eind punt van Azure AD: zolang de browser nog steeds een actieve sessie heeft (gelezen: een sessie cookie heeft) voor het Azure AD-domein, kan de verificatie aanvraag zonder tussen komst van de gebruiker wordt uitgevoerd.

Dit model verleent de Java script-toepassing de mogelijkheid om afzonderlijke toegangs tokens te vernieuwen en kan zelfs nieuwe api's verkrijgen voor een nieuwe API (vooropgesteld dat de gebruiker deze eerder heeft gestemd). Dit voor komt dat de extra belasting van een hoogwaardige artefact, zoals een vernieuwings token, wordt verzorgd, onderhouden en beveiligd. Het artefact dat de Stille vernieuwing mogelijk maakt, wordt de Azure AD-sessie cookie beheerd buiten de toepassing. Een ander voor deel van deze benadering is dat een gebruiker zich afmeldt bij Azure AD, met behulp van een van de toepassingen die zijn aangemeld bij Azure AD en die wordt uitgevoerd op een van de browser tabbladen. Dit resulteert in het verwijderen van de Azure AD-sessie cookie en de Java script-toepassing verliest automatisch de mogelijkheid om tokens te vernieuwen voor de afgemelde gebruiker.

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>Is de impliciete toekenning geschikt voor mijn app?

De impliciete toekenning presenteert meer Risico's dan andere subsidies en de gebieden die u moet best Eden aan de goed gedocumenteerde (bijvoorbeeld [misbruik van het toegangs token om de resource-eigenaar in impliciete stroom te imiteren] [OAuth2-specificatie-impliciet misbruik] en [OAuth 2,0 Threat model en beveiligings overwegingen] [OAuth2-Threat-model en Security-implicaties]). Het profiel met een hoger risico is echter grotendeels te wijten aan het feit dat het de bedoeling is dat toepassingen die actieve code uitvoeren, worden geactiveerd door een externe bron in een browser. Als u een beveiligd-wachtwoord verificatie-architectuur plant, geen back-end-onderdelen hebt of een web-API wilt aanroepen via Java script, wordt het gebruik van de impliciete stroom voor het ophalen van tokens aanbevolen.

Als uw toepassing een systeem eigen client is, is de impliciete stroom niet geweldig. Het ontbreken van de Azure AD-sessie cookie in de context van een systeem eigen client zorgt dat uw toepassing een langdurige sessie kan onderhouden. Dit betekent dat uw toepassing herhaaldelijk de gebruiker vraagt wanneer er toegangs tokens voor nieuwe resources worden verkregen.

Als u een webtoepassing ontwikkelt die een back-end bevat en gebruikmaakt van een API van de bijbehorende back-upcode, is de impliciete stroom ook niet geschikt. Andere subsidies bieden u veel meer mogelijkheden. Zo biedt de OAuth2-client referenties toekenning de mogelijkheid om tokens te verkrijgen die overeenkomen met de machtigingen die zijn toegewezen aan de toepassing zelf, in tegens telling tot gebruikers delegaties. Dit betekent dat de client de mogelijkheid heeft om programmatisch toegang tot resources te behouden, zelfs wanneer een gebruiker niet actief in een sessie wordt onderneemt, enzovoort. Niet alleen die subsidies bieden hogere beveiligings garanties. Toegangs tokens worden bijvoorbeeld nooit door Voer via de browser van de gebruiker, maar worden niet opgeslagen in de geschiedenis van de browser, enzovoort. De client toepassing kan ook sterke verificatie uitvoeren bij het aanvragen van een token.

## <a name="protocol-diagram"></a>Protocol diagram

In het volgende diagram ziet u hoe de volledige impliciete aanmeldings stroom eruit ziet en de secties die volgen elke stap in meer detail beschrijven.

![Diagram waarin de impliciete aanmeldings stroom wordt weer gegeven](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>De aanmeldings aanvraag verzenden

Als u de gebruiker in de app wilt ondertekenen, kunt u een aanvraag voor [OpenID Connect Connect](v2-protocols-oidc.md) -verificatie verzenden en een `id_token` ophalen van het micro soft Identity platform-eind punt.

> [!IMPORTANT]
> Als u een ID-token en/of een toegangs token wilt aanvragen, moet de registratie van de app op de [Azure Portal-app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) pagina de bijbehorende impliciete toekennings stroom hebben ingeschakeld, door **id-tokens** en. of **toegangs tokens** te selecteren onder de sectie **impliciete toekenning** . Als deze niet is ingeschakeld, wordt er een `unsupported_response` fout geretourneerd: **de opgegeven waarde voor de invoer parameter response_type is niet toegestaan voor deze client. De verwachte waarde is ' code '**

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid
&response_mode=fragment
&state=12345
&nonce=678910
```

> [!TIP]
> Als u het aanmelden wilt testen met behulp van de impliciete stroom, klikt u op <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a> Nadat u zich hebt aangemeld, moet uw browser worden omgeleid naar `https://localhost/myapp/` met een `id_token` in de adres balk.
>

| Parameter |  | Beschrijving |
| --- | --- | --- |
| `tenant` | Vereist |De `{tenant}` waarde in het pad van de aanvraag kan worden gebruikt om te bepalen wie zich kan aanmelden bij de toepassing. De toegestane waarden zijn `common`, `organizations`, `consumers`en Tenant-id's. Zie [basis beginselen van protocollen](active-directory-v2-protocols.md#endpoints)voor meer informatie. |
| `client_id` | Vereist | De ID van de toepassing (client) die de [Azure Portal-app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) pagina die aan uw app is toegewezen. |
| `response_type` | Vereist |Moet `id_token` voor OpenID Connect Connect-aanmelding bevatten. Het kan ook de response_type `token`omvatten. Als u `token` hier gebruikt, kan uw app direct vanuit het eind punt voor toestemming een toegangs token ontvangen zonder dat hiervoor een tweede aanvraag moet worden verzonden naar het toestemming eind punt. Als u de `token`-response_type gebruikt, moet de `scope`-para meter een bereik bevatten dat aangeeft voor welke resource het token moet worden uitgegeven (bijvoorbeeld gebruiker. Read op Microsoft Graph).  |
| `redirect_uri` | aanbevelingen |De redirect_uri van uw app, waar verificatie reacties kunnen worden verzonden en ontvangen door uw app. Het moet exact overeenkomen met een van de redirect_uris die u in de portal hebt geregistreerd, behalve het moet een URL-code ring zijn. |
| `scope` | Vereist |Een lijst met door spaties gescheiden [bereiken](v2-permissions-and-consent.md). Voor OpenID Connect Connect (id_tokens) moet deze het bereik `openid`bevatten, dat wordt omgezet in de machtiging ' Meld u aan ' in de gebruikers interface van de toestemming. U kunt eventueel ook de `email` en `profile` bereiken voor het verkrijgen van toegang tot aanvullende gebruikers gegevens toevoegen. Als er een toegangs token wordt aangevraagd, kunt u ook andere bereiken in deze aanvraag voor het aanvragen van een toestemming voor verschillende bronnen toevoegen. |
| `response_mode` | Beschrijving |Hiermee geeft u de methode op die moet worden gebruikt om het resulterende token terug naar uw app te verzenden. De standaard instelling is een query alleen voor een toegangs token, maar fragment als de aanvraag een id_token bevat. |
| `state` | aanbevelingen |Een waarde die in de aanvraag is opgenomen en die ook wordt geretourneerd in de token reactie. Dit kan een teken reeks zijn van alle inhoud die u wilt. Een wille keurig gegenereerde unieke waarde wordt doorgaans gebruikt om [vervalsing van aanvragen op meerdere sites](https://tools.ietf.org/html/rfc6749#section-10.12)te voor komen. De status wordt ook gebruikt voor het coderen van informatie over de status van de gebruiker in de app voordat de verificatie aanvraag is uitgevoerd, zoals de pagina of weer gave waarin ze zich bevonden. |
| `nonce` | Vereist |Een waarde die is opgenomen in de aanvraag, gegenereerd door de app, die wordt opgenomen in de resulterende id_token als een claim. De app kan vervolgens deze waarde verifiëren om token replay-aanvallen te verhelpen. De waarde is doorgaans een wille keurige, unieke teken reeks die kan worden gebruikt om de oorsprong van de aanvraag te identificeren. Alleen vereist wanneer een id_token wordt aangevraagd. |
| `prompt` | Beschrijving |Hiermee wordt het type gebruikers interactie aangegeven dat vereist is. De enige geldige waarden op dit moment zijn ' aanmelding, ' none ', ' select_account ' en ' instemming '. `prompt=login` dwingt de gebruiker de referenties op die aanvraag in te voeren, waarbij eenmalige aanmelding wordt genegeerd. `prompt=none` het tegenovergestelde is, zorgt er dan voor dat de gebruiker niet wordt weer gegeven met een interactieve prompt. Als de aanvraag niet op de achtergrond kan worden voltooid via eenmalige aanmelding, wordt door het micro soft Identity platform-eind punt een fout geretourneerd. `prompt=select_account` stuurt de gebruiker naar een account kiezer waarbij alle accounts die in de sessie zijn onthouden, worden weer gegeven. `prompt=consent` wordt het OAuth-dialoog venster voor toestemming geactiveerd nadat de gebruiker zich heeft aangemeld, waarin de gebruiker wordt gevraagd om machtigingen te verlenen aan de app. |
| `login_hint`  |Beschrijving |Kan worden gebruikt om het veld gebruikers naam/e-mail adres vooraf in te vullen op de aanmeldings pagina voor de gebruiker als u de gebruikers naam van tevoren kent. Vaak gebruiken apps deze para meter tijdens het opnieuw verifiëren, waarbij de gebruikers naam al is geëxtraheerd van een eerdere aanmelding met behulp van de `preferred_username` claim.|
| `domain_hint` | Beschrijving |Indien opgenomen, wordt het op e-mail gebaseerde detectie proces overs Laan dat de gebruiker op de aanmeldings pagina doorloopt, waardoor er iets meer gestroomlijnde gebruikers ervaring is. Dit wordt vaak gebruikt voor line-of-Business-Apps die worden uitgevoerd in één Tenant, waar ze een domein naam binnen een bepaalde Tenant opgeven.  Hiermee wordt de gebruiker doorgestuurd naar de Federation-provider voor die Tenant.  Houd er rekening mee dat de gast gebruikers zich niet kunnen aanmelden bij deze toepassing.  |

Op dit moment wordt de gebruiker gevraagd om hun referenties in te voeren en de verificatie te volt ooien. Het micro soft Identity platform-eind punt zorgt er ook voor dat de gebruiker heeft ingestemd met de machtigingen die in de `scope` query parameter worden aangegeven. Als de gebruiker heeft ingestemd met **geen** van deze machtigingen, wordt de gebruiker gevraagd om toestemming te geven voor de vereiste machtigingen. Zie [machtigingen, toestemming en multi tenant-apps](v2-permissions-and-consent.md)voor meer informatie.

Zodra de gebruiker zich heeft geverifieerd en toestemming verleent, stuurt het micro soft Identity platform-eind punt een reactie op uw app bij de aangegeven `redirect_uri`, met de methode die is opgegeven in de para meter `response_mode`.

#### <a name="successful-response"></a>Geslaagde reactie

Een geslaagde reactie met `response_mode=fragment` en `response_type=id_token+token` ziet er als volgt uit (met regel einden voor de Lees baarheid):

```
GET https://localhost/myapp/#
&token_type=Bearer
&expires_in=3599
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Parameter | Beschrijving |
| --- | --- |
| `access_token` |Opgenomen als `response_type` `token`bevat. Het toegangs token dat door de app is aangevraagd. Het toegangs token mag niet worden gedecodeerd of anderszins worden geïnspecteerd, maar moet worden beschouwd als een ondoorzichtige teken reeks. |
| `token_type` |Opgenomen als `response_type` `token`bevat. Wordt altijd `Bearer`. |
| `expires_in`|Opgenomen als `response_type` `token`bevat. Hiermee wordt het aantal seconden aangegeven dat het token geldig is, voor caching-doel einden. |
| `scope` |Opgenomen als `response_type` `token`bevat. Hiermee worden de scope (s) aangegeven waarvoor de access_token geldig is. Mag niet alle aangevraagde bereiken omvatten als deze niet van toepassing zijn op de gebruiker (in het geval van alleen Azure AD-scopes die worden aangevraagd wanneer een persoonlijk account wordt gebruikt om zich aan te melden). |
| `id_token` | Een ondertekende JSON Web Token (JWT). De app kan de segmenten van deze token decoderen om informatie aan te vragen over de gebruiker die zich heeft aangemeld. De app kan de waarden in de cache opslaan en weer geven, maar deze hoeft niet te worden gebaseerd op autorisatie-of beveiligings grenzen. Zie de [`id_token reference`](id-tokens.md)voor meer informatie over id_tokens. <br> **Opmerking:** Alleen opgegeven als `openid` bereik is aangevraagd. |
| `state` |Als een para meter State is opgenomen in de aanvraag, moet dezelfde waarde in het antwoord worden weer gegeven. De app moet controleren of de status waarden in de aanvraag en het antwoord identiek zijn. |

#### <a name="error-response"></a>Fout bericht

Fout reacties kunnen ook worden verzonden naar de `redirect_uri` zodat de app deze op de juiste wijze kan afhandelen:

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parameter | Beschrijving |
| --- | --- |
| `error` |Een teken reeks voor fout codes die kan worden gebruikt voor het classificeren van typen fouten die optreden en kunnen worden gebruikt om te reageren op fouten. |
| `error_description` |Een specifiek fout bericht dat een ontwikkelaar kan helpen bij het identificeren van de hoofd oorzaak van een verificatie fout. |

## <a name="getting-access-tokens-silently-in-the-background"></a>Toegangs tokens op de achtergrond ontvangen

Nu u de gebruiker hebt ondertekend in een app met één pagina, kunt u op de achtergrond toegangs tokens ontvangen voor het aanroepen van web-Api's die zijn beveiligd met micro soft Identity platform, zoals de [Microsoft Graph](https://developer.microsoft.com/graph). Zelfs als u al een token hebt ontvangen met behulp van de `token` response_type, kunt u deze methode gebruiken om tokens te verkrijgen aan aanvullende bronnen zonder dat de gebruiker opnieuw moet worden omgeleid om zich opnieuw aan te melden.

In de normale OpenID Connect Connect-verbinding/OAuth-stroom kunt u dit doen door een aanvraag te doen voor het micro soft Identity platform `/token`-eind punt. Het micro soft Identity platform-eind punt biedt echter geen ondersteuning voor CORS-aanvragen, dus het maken van AJAX-aanroepen voor het verkrijgen en vernieuwen van tokens is niet in de vraag. In plaats daarvan kunt u de impliciete stroom in een verborgen iframe gebruiken om nieuwe tokens voor andere web-Api's op te halen: 

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read 
&response_mode=fragment
&state=12345
&nonce=678910
&prompt=none
&login_hint=myuser@mycompany.com
```

Zie [de aanmeldings aanvraag verzenden](#send-the-sign-in-request)voor meer informatie over de query parameters in de URL.

> [!TIP]
> Kopieer & de onderstaande aanvraag op een browser tabblad te plakken. (Vergeet niet om de `login_hint` waarden te vervangen door de juiste waarde voor uw gebruiker)
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint={your-username}`
>

Dankzij de para meter `prompt=none`, kan deze aanvraag onmiddellijk slagen of mislukken en terugkeren naar uw toepassing. Er wordt een geslaagde reactie verzonden naar uw app op de aangegeven `redirect_uri`, met behulp van de methode die is opgegeven in de para meter `response_mode`.

#### <a name="successful-response"></a>Geslaagde reactie

Een geslaagde reactie met `response_mode=fragment` ziet er als volgt uit:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fdirectory.read
```

| Parameter | Beschrijving |
| --- | --- |
| `access_token` |Opgenomen als `response_type` `token`bevat. Het toegangs token dat de app heeft aangevraagd, in dit geval voor de Microsoft Graph. Het toegangs token mag niet worden gedecodeerd of anderszins worden geïnspecteerd, maar moet worden beschouwd als een ondoorzichtige teken reeks. |
| `token_type` | Wordt altijd `Bearer`. |
| `expires_in` | Hiermee wordt het aantal seconden aangegeven dat het token geldig is, voor caching-doel einden. |
| `scope` | Hiermee worden de scope (s) aangegeven waarvoor de access_token geldig is. Mag niet alle aangevraagde bereiken omvatten als deze niet van toepassing zijn op de gebruiker (in het geval van alleen Azure AD-scopes die worden aangevraagd wanneer een persoonlijk account wordt gebruikt om zich aan te melden). |
| `id_token` | Een ondertekende JSON Web Token (JWT). Opgenomen als `response_type` `id_token`bevat. De app kan de segmenten van deze token decoderen om informatie aan te vragen over de gebruiker die zich heeft aangemeld. De app kan de waarden in de cache opslaan en weer geven, maar deze hoeft niet te worden gebaseerd op autorisatie-of beveiligings grenzen. Zie voor meer informatie over id_tokens de [`id_token` verwijzing](id-tokens.md). <br> **Opmerking:** Alleen opgegeven als `openid` bereik is aangevraagd. |
| `state` |Als een para meter State is opgenomen in de aanvraag, moet dezelfde waarde in het antwoord worden weer gegeven. De app moet controleren of de status waarden in de aanvraag en het antwoord identiek zijn. |

#### <a name="error-response"></a>Fout bericht

Fout reacties kunnen ook worden verzonden naar de `redirect_uri` zodat de app deze op de juiste wijze kan afhandelen. In het geval van `prompt=none`is een verwachte fout:

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parameter | Beschrijving |
| --- | --- |
| `error` |Een teken reeks voor fout codes die kan worden gebruikt voor het classificeren van typen fouten die optreden en kunnen worden gebruikt om te reageren op fouten. |
| `error_description` |Een specifiek fout bericht dat een ontwikkelaar kan helpen bij het identificeren van de hoofd oorzaak van een verificatie fout. |

Als u deze fout in de iframe-aanvraag ontvangt, moet de gebruiker zich opnieuw aanmelden om een nieuw token op te halen. U kunt ervoor kiezen om deze kwestie op een wille keurige manier af te handelen voor uw toepassing.

## <a name="refreshing-tokens"></a>Tokens vernieuwen

De impliciete toekenning biedt geen vernieuwings tokens. Zowel `id_token`s als `access_token`s verlopen na een korte periode, dus uw app moet worden voor bereid om deze tokens regel matig te vernieuwen. Als u een van beide typen tokens wilt vernieuwen, kunt u dezelfde verborgen iframe-aanvraag van boven uitvoeren met behulp van de para meter `prompt=none` om het gedrag van het identiteits platform te bepalen. Als u een nieuw `id_token`wilt ontvangen, moet u `id_token` gebruiken in de `response_type` en `scope=openid`, evenals een `nonce`-para meter.

## <a name="send-a-sign-out-request"></a>Een aanvraag voor een afmelding verzenden

Met de OpenID Connect Connect `end_session_endpoint` kan uw app een aanvraag verzenden naar het micro soft Identity platform-eind punt om de sessie van een gebruiker te beëindigen en cookies te wissen die zijn ingesteld door het micro soft Identity platform-eind punt. Als u een gebruiker volledig wilt ondertekenen vanuit een webtoepassing, moet uw app een eigen sessie met de gebruiker beëindigen (meestal door het wissen van een token cache of het verwijderen van cookies), en vervolgens de browser om te leiden naar:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Parameter |  | Beschrijving |
| --- | --- | --- |
| `tenant` |Vereist |De `{tenant}` waarde in het pad van de aanvraag kan worden gebruikt om te bepalen wie zich kan aanmelden bij de toepassing. De toegestane waarden zijn `common`, `organizations`, `consumers`en Tenant-id's. Zie [basis beginselen van protocollen](active-directory-v2-protocols.md#endpoints)voor meer informatie. |
| `post_logout_redirect_uri` | aanbevelingen | De URL waarnaar de gebruiker moet worden geretourneerd nadat de afmelding is voltooid. Deze waarde moet overeenkomen met een van de omleidings-Uri's die voor de toepassing zijn geregistreerd. Als dat niet het geval is, wordt de gebruiker een algemeen bericht weer gegeven door het micro soft Identity platform-eind punt. |

## <a name="next-steps"></a>Volgende stappen

* Ga naar de [MSAL js](sample-v2-code.md) -voor beelden om te beginnen met de code ring.
