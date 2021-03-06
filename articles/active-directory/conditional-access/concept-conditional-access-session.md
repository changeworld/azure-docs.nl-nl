---
title: Sessie besturings elementen in beleid voor voorwaardelijke toegang-Azure Active Directory
description: Wat zijn sessie besturings elementen in een beleid voor voorwaardelijke toegang van Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: e99b9b87f939d614679fdecf24c9d36d99bf2938
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671878"
---
# <a name="conditional-access-session"></a>Voorwaardelijke toegang: sessie

Binnen een beleid voor voorwaardelijke toegang kan een beheerder gebruik maken van sessie besturings elementen om beperkte ervaringen binnen specifieke Cloud toepassingen in te scha kelen.

![Beleid voor voorwaardelijke toegang met een granting Control waarvoor multi-factor Authentication is vereist](./media/concept-conditional-access-session/conditional-access-session.png)

## <a name="application-enforced-restrictions"></a>Door toepassing afgedwongen beperkingen

Organisaties kunnen met dit besturings element instellen dat Azure AD apparaatgegevens moet door geven aan de geselecteerde Cloud-apps. De apparaatgegevens maken het mogelijk om te bepalen of een verbinding tot stand wordt gebracht vanaf een apparaat dat compatibel is met of een domein. Dit besturings element ondersteunt alleen share point online en Exchange Online als geselecteerde Cloud-apps. Als deze functie is ingeschakeld, gebruikt de Cloud-app de apparaatgegevens om gebruikers, afhankelijk van de status van het apparaat, te voorzien van een beperkte of volledige ervaring.

Raadpleeg de volgende artikelen voor meer informatie over het gebruik en de configuratie van door apps afgedwongen beperkingen:

- [Beperkte toegang inschakelen met share point online](/sharepoint/control-access-from-unmanaged-devices)
- [Beperkte toegang inschakelen met Exchange Online](https://aka.ms/owalimitedaccess)

## <a name="conditional-access-application-control"></a>Toepassings beheer voor voorwaardelijke toegang

App-beheer voor voorwaardelijke toegang maakt gebruik van een reverse proxy-architectuur en is uniek geïntegreerd met voorwaardelijke toegang van Azure AD. Met voorwaardelijke toegang van Azure AD kunt u toegangs beheer op de apps van uw organisatie afdwingen op basis van bepaalde voor waarden. De voor waarden bepalen wie (gebruiker of groep gebruikers) en wat (welke Cloud-apps) en waar (de locaties en netwerken) een beleid voor voorwaardelijke toegang wordt toegepast. Nadat u de voor waarden hebt vastgesteld, kunt u gebruikers door sturen naar [Microsoft Cloud app Security](/cloud-app-security/what-is-cloud-app-security) waar u gegevens kunt beveiligen met app-beheer voor voorwaardelijke toegang door toegangs-en sessie besturings elementen toe te passen.

App-beheer voor voorwaardelijke toegang kunt app-toegang voor gebruikers en sessies moeten worden bewaakt en beheerd in realtime op basis van de toegangs-en-sessie. Toegangs-en-sessie worden gebruikt in de Cloud App Security-portal verder verfijnen filters en acties worden uitgevoerd voor een gebruiker ingesteld. Met de beleidsregels voor toegangs- en sessiebeleid, kunt u het volgende doen:

- Gegevens exfiltration voor komen: u kunt het downloaden, knippen, kopiëren en afdrukken van gevoelige documenten op, bijvoorbeeld niet-beheerde apparaten, blok keren.
- Beveiligen bij downloaden: in plaats van het downloaden van gevoelige documenten te blok keren, kunt u vereisen dat documenten worden gelabeld en beveiligd met Azure Information Protection. Met deze actie zorgt u ervoor dat het document wordt beveiligd en de gebruikers toegang wordt beperkt in een mogelijk Risk ante sessie.
- Uploaden van niet-gelabelde bestanden voor komen: voordat een vertrouwelijk bestand wordt geüpload, gedistribueerd en wordt gebruikt door anderen, is het belang rijk om ervoor te zorgen dat het bestand het juiste label en bescherming heeft. U kunt ervoor zorgen dat niet-gelabelde bestanden met gevoelige inhoud worden geüpload, totdat de gebruiker de inhoud heeft geclassificeerd.
- Gebruikers sessies controleren op naleving: risk ante gebruikers worden bewaakt wanneer ze zich aanmelden bij apps en hun acties worden geregistreerd binnen de sessie. U kunt onderzoeken en analyseren van gebruikersgedrag voor meer informatie over waar en onder welke voorwaarden, sessie-beleid in de toekomst moeten worden toegepast.
- Toegang blok keren: u kunt de toegang voor specifieke apps en gebruikers nauw keurig blok keren, afhankelijk van verschillende risico factoren. U kunt ze bijvoorbeeld blok keren als ze client certificaten gebruiken als een vorm van Apparaatbeheer.
- Aangepaste activiteiten blok keren: sommige apps hebben unieke scenario's die risico lopen, bijvoorbeeld het verzenden van berichten met gevoelige inhoud in apps zoals micro soft teams of vertraging. In dit soort scenario's kunt u berichten voor gevoelige inhoud scannen en deze in realtime blok keren.

Zie voor meer informatie het artikel [app-beheer voor voorwaardelijke toegang implementeren voor aanbevolen apps](/cloud-app-security/proxy-deployment-aad).

## <a name="sign-in-frequency-preview"></a>Aanmeldings frequentie (preview-versie)

Met de aanmeldings frequentie wordt de tijds periode gedefinieerd voordat een gebruiker wordt gevraagd om zich opnieuw aan te melden wanneer wordt geprobeerd om toegang te krijgen tot een bron.

De instelling voor de aanmeldings frequentie werkt met apps waarop OAUTH2 of OIDC-protocollen zijn geïmplementeerd volgens de standaarden. De meeste micro soft-apps voor Windows, Mac en Mobile met inbegrip van de volgende webtoepassingen voldoen aan de instelling.

- Word, Excel, Power Point online
- OneNote online
- Office.com
- O365-beheer Portal
- Exchange Online
- Share point en OneDrive
- Web-client voor teams
- Dynamics CRM Online
- Azure-portal

Zie voor meer informatie het artikel [verificatie sessie beheer met voorwaardelijke toegang configureren](howto-conditional-access-session-lifetime.md#user-sign-in-frequency).

## <a name="persistent-browser-session-preview"></a>Permanente browser sessie (preview-versie)

Met een permanente browser sessie kunnen gebruikers aangemeld blijven nadat ze het browser venster hebben gesloten en opnieuw hebben geopend.

Zie voor meer informatie het artikel [verificatie sessie beheer met voorwaardelijke toegang configureren](howto-conditional-access-session-lifetime.md#persistence-of-browsing-sessions).

## <a name="next-steps"></a>Volgende stappen

- [Algemeen beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)

- [Modus alleen rapport](concept-conditional-access-report-only.md)
