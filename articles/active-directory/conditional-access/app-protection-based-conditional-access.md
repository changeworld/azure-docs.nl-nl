---
title: App-beveiligings beleid met voorwaardelijke toegang-Azure Active Directory
description: Meer informatie over het vereisen van app-beveiligings beleid voor toegang tot Cloud-apps met voorwaardelijke toegang in Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 03/04/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol, rosssmi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9859c884f6a1e22a1ac2bd21106ef51ead23fa41
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2020
ms.locfileid: "79080066"
---
# <a name="how-to-require-app-protection-policy-and-an-approved-client-app-for-cloud-app-access-with-conditional-access"></a>Procedure: beveiligings beleid voor apps en een goedgekeurde client-app vereisen voor toegang tot Cloud-apps met voorwaardelijke toegang

Gebruikers gebruiken regel matig hun mobiele apparaten voor zowel privé-als werk taken. Het is ook belang rijk om te voor komen dat uw mede werkers productief kunnen zijn, maar dat er geen gegevens verloren gaan. Met voorwaardelijke toegang kunnen organisaties de toegang beperken tot goedgekeurde (moderne authenticatie mogelijkheden) client-apps met intune-beleid voor app-beveiliging.

Dit artikel bevat twee scenario's voor het configureren van beleid voor voorwaardelijke toegang voor resources zoals Office 365, Exchange Online en share point online.

- [Scenario 1: Office 365-apps vereist goedgekeurde apps met app-beveiligings beleid](#scenario-1-office-365-apps-require-approved-apps-with-app-protection-policies)
- [Scenario 2: voor Exchange Online en share point online is een goedgekeurde client-app en beveiligings beleid voor apps vereist](#scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app-and-app-protection-policy)

In de voorwaardelijke toegang zijn deze client-apps bekend om te worden beveiligd met een beveiligings beleid voor apps. Meer informatie over het beveiligings beleid voor apps vindt u in het artikel [overzicht van app-beveiligings beleid](/intune/apps/app-protection-policy)

Zie [vereisten voor app-beveiligings beleid](concept-conditional-access-grant.md)voor een lijst met in aanmerking komende client-apps.

> [!NOTE]
>    De or-component wordt in het beleid gebruikt om gebruikers in staat te stellen apps te gebruiken die ondersteuning bieden voor het **beleid voor app-beveiliging** of het **verplicht stellen van goedgekeurde client-app** -machtigingen. Zie [app-beveiligings beleid vereisten](concept-conditional-access-grant.md)voor meer informatie over de apps die ondersteuning bieden voor de machtiging beheer **beleid voor app-beveiliging vereisen** .

## <a name="scenario-1-office-365-apps-require-approved-apps-with-app-protection-policies"></a>Scenario 1: Office 365-apps vereist goedgekeurde apps met app-beveiligings beleid

In dit scenario heeft Contoso besloten dat voor alle mobiele toegang tot Office 365-bronnen goedgekeurde client-apps moeten worden gebruikt, zoals Outlook Mobile, OneDrive en micro soft-teams die worden beveiligd door een beveiligings beleid voor apps voordat ze toegang krijgen. Al hun gebruikers aanmelden met Azure AD-referenties en hebben licenties toegewezen die Azure AD Premium P1 of P2 en Microsoft Intune bevatten.

Organisaties moeten de volgende stappen uitvoeren om het gebruik van een goedgekeurde client-app op mobiele apparaten te vereisen.

**Stap 1: een beleid voor voorwaardelijke toegang voor Azure AD configureren voor Office 365**

1. Meld u aan bij de **Azure Portal** als globale beheerder, beveiligings beheerder of beheerder van de voorwaardelijke toegang.
1. Blader naar **Azure Active Directory** > **beveiligings** > **voorwaardelijke toegang**.
1. Selecteer **Nieuw beleid**.
1. Geef uw beleid een naam. Het is raadzaam dat organisaties een zinvolle norm maken voor de namen van hun beleid.
1. Onder **toewijzingen**selecteert u **gebruikers en groepen**
   1. Onder **opnemen**selecteert u **alle gebruikers** of de specifieke **gebruikers en groepen** waarop u dit beleid wilt Toep assen. 
   1. Selecteer **Done**.
1. Onder **Cloud-apps of acties** > **bevatten**, selecteert u **Office 365 (preview)** .
1. Onder **voor waarden**selecteert u **apparaat platforms**.
   1. Stel **configureren** in op **Ja**.
   1. Voeg **Android** en **IOS**toe.
1. Onder **voor waarden**selecteert u **client-apps (preview-versie)** .
   1. Stel **configureren** in op **Ja**.
   1. Selecteer **mobiele apps en desktop-clients** en **moderne verificatie-clients**.
1. Selecteer onder **toegangs beheer** > **verlenen**de volgende opties:
   - **Goedgekeurde client-app vereisen**
   - **Beveiligings beleid voor apps vereisen (preview-versie)**
   - **Een van de geselecteerde besturings elementen vereisen**
1. Bevestig de instellingen en stel **beleid inschakelen** in **op aan**.
1. Selecteer **maken** om uw beleid te maken en in te scha kelen.

**Stap 2: een beleid voor voorwaardelijke toegang voor Azure AD configureren voor Exchange Online met ActiveSync (EAS)**

Configureer voor het beleid voor voorwaardelijke toegang in deze stap de volgende onderdelen:

1. Blader naar **Azure Active Directory** > **beveiligings** > **voorwaardelijke toegang**.
1. Selecteer **Nieuw beleid**.
1. Geef uw beleid een naam. Het is raadzaam dat organisaties een zinvolle norm maken voor de namen van hun beleid.
1. Onder **toewijzingen**selecteert u **gebruikers en groepen**
   1. Onder **opnemen**selecteert u **alle gebruikers** of de specifieke **gebruikers en groepen** waarop u dit beleid wilt Toep assen. 
   1. Selecteer **Done**.
1. Selecteer **Office 365 Exchange Online**onder **Cloud-apps of-acties** > **bevatten**.
1. Onder **voor waarden**:
   1. **Client-apps (preview-versie)** :
      1. Stel **configureren** in op **Ja**.
      1. Selecteer **mobiele apps en desktop-clients** en **Exchange ActiveSync-clients**.
1. Selecteer onder **toegangs beheer** > **verlenen**de optie **toegang verlenen**, **beveiligings beleid voor apps vereisen**en selecteer **selecteren**.
1. Bevestig de instellingen en stel **beleid inschakelen** in **op aan**.
1. Selecteer **maken** om uw beleid te maken en in te scha kelen.

**Stap 3: het beveiligings beleid voor apps in intune configureren voor iOS-en Android-client toepassingen**

Raadpleeg het artikel het [maken en toewijzen van app-beveiligings beleid](/intune/apps/app-protection-policies)voor stappen voor het maken van app-beveiligings beleid voor Android en IOS. 

## <a name="scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app-and-app-protection-policy"></a>Scenario 2: voor Exchange Online en share point online is een goedgekeurde client-app en beveiligings beleid voor apps vereist

In dit scenario heeft Contoso besloten dat gebruikers alleen toegang hebben tot e-mail en share point-gegevens op mobiele apparaten, zolang ze een goedgekeurde client-app gebruiken, zoals Outlook Mobile die wordt beveiligd door een app-beveiligings beleid voordat ze toegang krijgen. Al hun gebruikers aanmelden met Azure AD-referenties en hebben licenties toegewezen die Azure AD Premium P1 of P2 en Microsoft Intune bevatten.

Organisaties moeten de volgende drie stappen uitvoeren om het gebruik van een goedgekeurde client-app op mobiele apparaten en Exchange ActiveSync-clients te vereisen.

**Stap 1: beleid voor Android-en iOS-gebaseerde moderne authenticatie clients waarvoor het gebruik van een goedgekeurde client-app en app-beveiligings beleid is vereist bij de toegang tot Exchange Online en share point online.**

1. Meld u aan bij de **Azure Portal** als globale beheerder, beveiligings beheerder of beheerder van de voorwaardelijke toegang.
1. Blader naar **Azure Active Directory** > **beveiligings** > **voorwaardelijke toegang**.
1. Selecteer **Nieuw beleid**.
1. Geef uw beleid een naam. Het is raadzaam dat organisaties een zinvolle norm maken voor de namen van hun beleid.
1. Onder **toewijzingen**selecteert u **gebruikers en groepen**
   1. Onder **opnemen**selecteert u **alle gebruikers** of de specifieke **gebruikers en groepen** waarop u dit beleid wilt Toep assen. 
   1. Selecteer **Done**.
1. Onder **Cloud-apps of acties** > **bevatten**, selecteert u **Office 365 Exchange Online** en **Office 365 share point online**.
1. Onder **voor waarden**selecteert u **apparaat platforms**.
   1. Stel **configureren** in op **Ja**.
   1. Voeg **Android** en **IOS**toe.
1. Onder **voor waarden**selecteert u **client-apps (preview-versie)** .
   1. Stel **configureren** in op **Ja**.
   1. Selecteer **mobiele apps en desktop-clients** en **moderne verificatie-clients**.
1. Selecteer onder **toegangs beheer** > **verlenen**de volgende opties:
   - **Goedgekeurde client-app vereisen**
   - **Beveiligings beleid voor apps vereisen (preview-versie)**
   - **Een van de geselecteerde besturings elementen vereisen**
1. Bevestig de instellingen en stel **beleid inschakelen** in **op aan**.
1. Selecteer **maken** om uw beleid te maken en in te scha kelen.

**Stap 2: beleid voor Exchange ActiveSync-clients waarvoor het gebruik van een goedgekeurde client-app is vereist.**

1. Blader naar **Azure Active Directory** > **beveiligings** > **voorwaardelijke toegang**.
1. Selecteer **Nieuw beleid**.
1. Geef uw beleid een naam. Het is raadzaam dat organisaties een zinvolle norm maken voor de namen van hun beleid.
1. Onder **toewijzingen**selecteert u **gebruikers en groepen**
   1. Onder **opnemen**selecteert u **alle gebruikers** of de specifieke **gebruikers en groepen** waarop u dit beleid wilt Toep assen. 
   1. Selecteer **Done**.
1. Selecteer **Office 365 Exchange Online**onder **Cloud-apps of-acties** > **bevatten**.
1. Onder **voor waarden**:
   1. **Client-apps (preview-versie)** :
      1. Stel **configureren** in op **Ja**.
      1. Selecteer **mobiele apps en desktop-clients** en **Exchange ActiveSync-clients**.
1. Selecteer onder **toegangs beheer** > **verlenen**de optie **toegang verlenen**, **beveiligings beleid voor apps vereisen**en selecteer **selecteren**.
1. Bevestig de instellingen en stel **beleid inschakelen** in **op aan**.
1. Selecteer **maken** om uw beleid te maken en in te scha kelen.

**Stap 3: Configureer het beveiligings beleid van de intune-app voor iOS-en Android-client toepassingen.**

Raadpleeg het artikel het [maken en toewijzen van app-beveiligings beleid](/intune/apps/app-protection-policies)voor stappen voor het maken van app-beveiligings beleid voor Android en IOS. 

## <a name="next-steps"></a>Volgende stappen

[Wat is voorwaardelijke toegang?](overview.md)

[Onderdelen voor voorwaardelijke toegang](concept-conditional-access-policies.md)

[Algemeen beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)

