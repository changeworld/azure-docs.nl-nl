---
title: 'Azure AD Connect: naadloze eenmalige aanmelding | Microsoft Docs'
description: In dit onderwerp worden de naadloze eenmalige aanmelding van Azure Active Directory (Azure AD) beschreven en wordt uitgelegd hoe u een echte eenmalige aanmelding kunt bieden voor zakelijke desktop gebruikers binnen uw bedrijfs netwerk.
services: active-directory
keywords: Wat is Azure AD Connect, installeer Active Directory, vereiste onderdelen voor Azure AD, SSO, eenmalige aanmelding
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/13/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1b7e4716e731e6b73e3ac60b64baa71043906fc
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77483751"
---
# <a name="azure-active-directory-seamless-single-sign-on"></a>Azure Active Directory naadloze eenmalige aanmelding

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>Wat is Azure Active Directory naadloze eenmalige aanmelding?

Met Naadloze eenmalige aanmelding van Azure Active Directory (Naadloze SSO van Azure AD) worden gebruikers aangemeld als hun bedrijfsapparaten zijn verbonden met net bedrijfsnetwerk. Als u deze functie inschakelt, hoeven gebruikers hun wacht woord niet in te voeren om zich aan te melden bij Azure AD. dit gebeurt meestal in hun gebruikers naam. Deze functie biedt een gebruiker eenvoudig toegang tot cloudtoepassingen zonder dat er aanvullende on-premises onderdelen nodig zijn.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Naadloze SSO kan worden gecombineerd met de aanmeldings methoden voor [wachtwoord-hash](how-to-connect-password-hash-synchronization.md) of [Pass-Through-verificatie](how-to-connect-pta.md) . Naadloze SSO is _niet_ van toepassing op Active Directory Federation Services (ADFS).

![Naadloze eenmalige aanmelding](./media/how-to-connect-sso/sso1.png)

>[!IMPORTANT]
>Voor naadloze SSO moet het apparaat van de gebruiker alleen lid worden van een **domein** , maar dit wordt niet gebruikt op apparaten die zijn toegevoegd aan [Azure AD](../devices/concept-azure-ad-join.md) of [hybride Azure AD](../devices/concept-azure-ad-join-hybrid.md) . SSO op Azure AD join en hybride Azure AD zijn toegevoegd op basis van het [primaire vernieuwings token](../devices/concept-primary-refresh-token.md).

## <a name="key-benefits"></a>Belangrijkste voordelen

- *Goede gebruikerservaring*
  - Gebruikers worden automatisch aangemeld bij on-premises en Cloud toepassingen.
  - Gebruikers hoeven hun wacht woord niet herhaaldelijk in te voeren.
- *Eenvoudig te implementeren & beheren*
  - Er zijn geen extra onderdelen die on-premises nodig zijn om dit werk te doen.
  - Werkt met een wille keurige methode voor Cloud verificatie- [synchronisatie van wacht woord-hash](how-to-connect-password-hash-synchronization.md) of [Pass Through-verificatie](how-to-connect-pta.md).
  - Kan worden geïmplementeerd voor sommige of al uw gebruikers met behulp van groepsbeleid.
  - Registreer niet-Windows 10-apparaten met Azure AD zonder dat u een AD FS-infra structuur nodig hebt. Deze functie vereist dat u versie 2,1 of hoger van de [werk plek-client](https://www.microsoft.com/download/details.aspx?id=53554)gebruikt.

## <a name="feature-highlights"></a>Functie hoogtepunten

- De aanmeldings naam van de gebruiker kan de on-premises standaard gebruikersnaam (`userPrincipalName`) zijn of een ander kenmerk dat is geconfigureerd in Azure AD Connect (`Alternate ID`). Beide use-cases werken omdat naadloze SSO gebruikmaakt van de `securityIdentifier` claim in het Kerberos-ticket om het bijbehorende gebruikers object in azure AD op te zoeken.
- Naadloze SSO is een functie van opportunistisch. Als de gebruiker om welke reden dan ook niet kan worden uitgevoerd, gaat de aanmeldings procedure voor gebruikers terug naar het normale gedrag, dat wil zeggen dat de gebruiker het wacht woord moet invoeren op de aanmeldings pagina.
- Als een toepassing (bijvoorbeeld `https://myapps.microsoft.com/contoso.com`) een `domain_hint` (OpenID Connect Connect) of een `whr` (SAML)-para meter, die uw Tenant identificeert of `login_hint` para meter-identificatie van de gebruiker, doorstuurt, wordt gebruikers automatisch aangemeld zonder dat ze gebruikers namen of wacht woorden hoeven in te voeren.
- Gebruikers krijgen ook een stille aanmeldings ervaring als een toepassing (bijvoorbeeld `https://contoso.sharepoint.com`) aanmeldings aanvragen verzendt naar de eind punten van Azure AD die als tenants zijn ingesteld, dat wil zeggen, `https://login.microsoftonline.com/contoso.com/<..>` of `https://login.microsoftonline.com/<tenant_ID>/<..>` in plaats van het gemeen schappelijke eind punt van Azure AD, dat wil zeggen `https://login.microsoftonline.com/common/<...>`.
- Afmelden wordt ondersteund. Hiermee kunnen gebruikers een ander Azure AD-account kiezen om zich aan te melden met, in plaats van dat automatisch wordt aangemeld met naadloze SSO.
- Office 365 Win32-clients (Outlook, Word, Excel en andere) met versies 16.0.8730. xxxx en hoger worden ondersteund met behulp van een niet-interactieve stroom. Voor OneDrive moet u de [functie voor stil configuratie van onedrive](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894) activeren voor een stille aanmeldings ervaring.
- Het kan worden ingeschakeld via Azure AD Connect.
- Het is een gratis functie en u hebt geen betaalde versies van Azure AD nodig om deze te gebruiken.
- Het wordt ondersteund op webbrowsers en Office-clients die [moderne authenticatie](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) ondersteunen op platforms en browsers die geschikt zijn voor Kerberos-verificatie:

| OS\Browser |Internet Explorer|Microsoft Edge|Google Chrome|Mozilla Firefox|Safari|
| --- | --- |--- | --- | --- | -- 
|Windows 10|Ja\*|Ja|Ja|Ja\*\*\*|N.v.t.
|Windows 8.1|Ja\*|N.v.t.|Ja|Ja\*\*\*|N.v.t.
|Windows 8|Ja\*|N.v.t.|Ja|Ja\*\*\*|N.v.t.
|Windows 7|Ja\*|N.v.t.|Ja|Ja\*\*\*|N.v.t.
|Windows Server 2012 R2 of hoger|Ja\*\*|N.v.t.|Ja|Ja\*\*\*|N.v.t.
|Mac OS X|N.v.t.|N.v.t.|Ja\*\*\*|Ja\*\*\*|Ja\*\*\*


voor \*zijn Internet Explorer versie 10 of hoger vereist

voor \*\*is Internet Explorer versie 10 of hoger vereist. Uitgebreide beveiligde modus uitschakelen

voor \*\*\*is [aanvullende configuratie](how-to-connect-sso-quick-start.md#browser-considerations) vereist

>[!NOTE]
>Voor Windows 10 is het aanbeveling om [Azure AD samen](../devices/concept-azure-ad-join.md) te gebruiken voor de optimale ervaring voor eenmalige aanmelding met Azure AD.

## <a name="next-steps"></a>Volgende stappen

- [**Quick Start**](how-to-connect-sso-quick-start.md) : krijg Azure AD naadloze SSO en voer deze uit.
- [**Implementatie plan**](https://aka.ms/deploymentplans/sso) -stap-voor-stap implementatie plan.
- [**Technisch diep gaande**](how-to-connect-sso-how-it-works.md) kennis van de werking van deze functie.
- [**Veelgestelde vragen**](how-to-connect-sso-faq.md) : antwoorden op veelgestelde vragen.
- [**Problemen oplossen**](tshoot-connect-sso.md) : informatie over het oplossen van veelvoorkomende problemen met de functie.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) -voor het indienen van nieuwe functie aanvragen.

