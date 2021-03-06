---
title: Azure Active Directory Identity Protection meldingen
description: Meer informatie over hoe meldingen uw onderzoeksactiviteiten ondersteunen.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d770336099611d69fa9e44d04fff4dd37696707
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77120124"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Azure Active Directory Identity Protection meldingen

Azure AD Identity Protection verzendt twee typen e-mail berichten voor automatische meldingen om u te helpen bij het beheren van risico-en risico detecties van gebruikers:

- Gebruikers met een risico voor een gedetecteerd e-mail bericht
- E-mail met wekelijkse samen vatting

In dit artikel vindt u een overzicht van beide e-mail meldingen.

>[!NOTE]
>E-mail meldingen zijn alleen beschikbaar in de open bare Cloud en zijn momenteel niet beschikbaar in de Amerikaanse overheids Cloud.

## <a name="users-at-risk-detected-email"></a>Gebruikers met een risico voor een gedetecteerd e-mail bericht

Als reactie op een gedetecteerd account dat risico loopt, genereert Azure AD Identity Protection een e-mail waarschuwing met **gebruikers die risico lopen** als onderwerp. Het e-mail bericht bevat een koppeling naar de gebruikers die zijn **[gemarkeerd voor een risico](../reports-monitoring/concept-user-at-risk.md)** rapport. Als best practice moet u onmiddellijk de gebruikers op risico onderzoeken.

Met de configuratie voor deze waarschuwing kunt u opgeven op welk gebruikers risico niveau u de waarschuwing wilt genereren. Het e-mail adres wordt gegenereerd wanneer het risico niveau van de gebruiker de opgegeven waarde bereikt. Er worden echter geen nieuwe gebruikers meer ontvangen die waarschuwingen hebben gevonden voor deze gebruiker nadat ze zijn overgezet naar dit risico niveau. Als u het beleid bijvoorbeeld instelt op een waarschuwing over gemiddeld risico van gebruikers en uw gebruiker John verplaatst naar gemiddeld risico, ontvangt u de gebruikers die risico lopen voor John. Er wordt echter geen waarschuwing weer gegeven dat de tweede gebruiker risico loopt als John vervolgens overgaat op een hoog risico of extra risico detecties heeft.

![Gebruikers met een risico voor een gedetecteerd e-mail bericht](./media/howto-identity-protection-configure-notifications/01.png)

### <a name="configure-users-at-risk-detected-alerts"></a>Gebruikers configureren met gedetecteerde risico waarschuwingen

Als beheerder kunt u het volgende instellen:

- **Het risico niveau van de gebruiker waarmee het genereren van deze e-mail wordt geactiveerd** : het risico niveau is standaard ingesteld op hoog risico.
- **De ontvangers van dit e-mail bericht** bevatten standaard alle globale beheerders. Globale beheerders kunnen ook andere globale beheerders, beveiligings beheerders en beveiligings lezers toevoegen als ontvangers.
   - U kunt desgewenst **extra e-mail berichten toevoegen om waarschuwings meldingen te ontvangen** . deze functie is een preview-versie en gebruikers die zijn gedefinieerd, moeten de juiste machtigingen hebben om de gekoppelde rapporten in de Azure portal weer te geven.

Configureer het e-mail adres voor gebruikers met een risico op de **Azure Portal** onder **Azure Active Directory** > **beveiliging** > **identiteits beveiliging** > **gebruikers die risico lopen voor gedetecteerde waarschuwingen**.

## <a name="weekly-digest-email"></a>E-mail met wekelijkse samen vatting

Het wekelijkse overzichts bericht bevat een samen vatting van nieuwe risico detecties.  
Het bevat:

- Gebruikers die risico lopen
- Verdachte activiteiten
- Gedetecteerde beveiligings problemen
- Koppelingen naar de gerelateerde rapporten in identiteits beveiliging

![E-mail met wekelijkse samen vatting](./media/howto-identity-protection-configure-notifications/400.png)

Ontvangers bevatten standaard alle globale beheerders. Globale beheerders kunnen ook andere globale beheerders, beveiligings beheerders en beveiligings lezers toevoegen als ontvangers.

### <a name="configure-weekly-digest-email"></a>Wekelijks overzicht van e-mail configureren

Als beheerder kunt u een wekelijks overzicht van e-mail verzenden naar of uit en de gebruikers selecteren die zijn toegewezen om het e-mail bericht te ontvangen.

Configureer de week overzichts-e-mail in de **Azure Portal** onder **Azure Active Directory** > **beveiliging** > **identiteits beveiliging** > **wekelijkse samen vatting**.

## <a name="see-also"></a>Zie ook

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
