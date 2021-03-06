---
title: Wat is een beveiligde score voor identiteit? - Azure Active Directory
description: Hoe u de beveiligde score voor identiteits beveiliging kunt gebruiken om de beveiligings postuur van uw directory te verbeteren
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: tilarso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f75dea2cffbe710bf2778ceab5eacc91ffcca9c
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523102"
---
# <a name="what-is-the-identity-secure-score-in-azure-active-directory"></a>Wat is de id-beveiligingsscore in Azure Active Directory?

Hoe veilig is uw Azure AD-tenant? Als u niet weet hoe u deze vraag beantwoordt, wordt in dit artikel uitgelegd hoe u met de beveiligde Score van de identiteit uw identiteits beveiligings postuur kunt bewaken en verbeteren.

## <a name="what-is-an-identity-secure-score"></a>Wat is een id-beveiligingsscore?

De beveiligde score voor identiteiten is een getal tussen 1 en 223 dat als indicator fungeert voor de mate waarin u met de best practice aanbevelingen van micro soft voor beveiliging werkt. Elke verbeterings actie in identiteits beveiligde Score is afgestemd op uw specifieke configuratie.  

![Beveiligingsscore](./media/identity-secure-score/identity-secure-score-overview.png)

De score helpt bij het volgende:

- Objectief meten van het beveiligingspostuur van uw identiteit
- Plannen van verbeteringen aan de identiteitsbeveiliging
- Evalueren van het succes van uw verbeteringen

De score en verwante informatie zijn toegankelijk via het dashboard Identity Secure Score. In dit dashboard vindt u deze informatie:

- Uw identiteits veilige Score
- Een vergelijkings grafiek waarin wordt getoond hoe uw identiteits veilige Score vergelijkt met andere tenants in dezelfde branche en soort gelijke grootte
- Een trend grafiek waarin wordt getoond hoe uw identiteits veilige Score na verloop van tijd is gewijzigd
- Een lijst met mogelijke verbeteringen

Door de verbeteringsacties te volgen, kunt u het volgende doen:

- Verbeter uw beveiligings postuur en uw Score
- Profiteer van de functies die beschikbaar zijn voor uw organisatie als onderdeel van uw identiteits investeringen

## <a name="how-do-i-get-my-secure-score"></a>Hoe kom ik aan mijn beveiligde score?

De beveiligde score voor identiteiten is beschikbaar in alle edities van Azure AD. Organisaties hebben toegang tot hun identiteits beveiligde Score van de **Azure Portal** > **Azure Active Directory** > **beveiliging** > **identiteit beveiligde Score**.

## <a name="how-does-it-work"></a>Hoe werkt het?

Elke 48 uur wordt uw beveiligingsconfiguratie geanalyseerd door Azure en worden uw instellingen vergeleken met de aanbevolen procedures. Op basis van het resultaat van deze evaluatie wordt een nieuwe score berekend voor uw Directory. Het is mogelijk dat uw beveiligings configuratie niet volledig is afgestemd op de best practice richt lijnen en dat de verbeterings acties slechts gedeeltelijk worden uitgevoerd. In deze scenario's krijgt u slechts een deel van de maximale score die beschikbaar is voor het besturings element.

Elke aanbeveling wordt gemeten op basis van uw Azure AD-configuratie. Als u producten van derden gebruikt om een best practice aanbeveling in te scha kelen, kunt u deze configuratie opgeven in de instellingen van een verbeterings actie. U hebt ook de optie om aanbevelingen in te stellen die moeten worden genegeerd als ze niet van toepassing zijn op uw omgeving. Een genegeerde aanbeveling draagt niet bij aan de berekening van de score.

![Actie negeren of markeren zoals gedekt door derden](./media/identity-secure-score/identity-secure-score-ignore-or-third-party-reccomendations.png)

## <a name="how-does-it-help-me"></a>Wat heb ik eraan?

De id-beveiligingsscore helpt bij het volgende:

- Objectief meten van het beveiligingspostuur van uw identiteit
- Plannen van verbeteringen aan de identiteitsbeveiliging
- Evalueren van het succes van uw verbeteringen

## <a name="what-you-should-know"></a>Wat u moet weten

### <a name="who-can-use-the-identity-secure-score"></a>Wie kan de id-beveiligingsscore gebruiken?

De id-beveiligingsscore kan worden gebruikt door de volgende rollen:

- Globale beheerder
- Beveiligingsbeheerder
- Beveiligingslezers

### <a name="how-are-controls-scored"></a>Hoe worden de besturings elementen gescoord?

Besturings elementen kunnen op twee manieren worden gescoord. Sommige worden op een binaire manier gescoord: u krijgt 100% van de score als u de functie of instelling hebt geconfigureerd op basis van onze aanbeveling. Andere scores worden berekend als een percentage van de totale configuratie. Als de aanbeveling voor verbetering bijvoorbeeld aangeeft dat u 30 punten krijgt als u al uw gebruikers met MFA beveiligt en u slechts 5 van 100 totaal gebruikers hebt beveiligd, krijgt u een gedeeltelijke Score van ongeveer twee punten (5 beveiligde/100 totaal * 30 Maxi maal pts = 2).

### <a name="what-does-not-scored-mean"></a>Wat betekent [Geen score]?

Acties met het label [Geen score] zijn acties die u kunt uitvoeren in uw organisatie, maar die geen score krijgen omdat ze nog niet zijn gekoppeld aan de tool. U kunt dus wel uw beveiliging verbeteren, maar u krijgt hier op dit moment geen punten voor.

### <a name="how-often-is-my-score-updated"></a>Hoe vaak wordt mijn score bijgewerkt?

De score wordt één keer per dag berekend (om ongeveer 01:00 PST). Als u een wijziging aanbrengt in een gemeten actie, wordt de score automatisch de volgende dag bijgewerkt. Het duurt maximaal 48 uur voordat een wijziging is verwerkt in de score.

### <a name="my-score-changed-how-do-i-figure-out-why"></a>Mijn score is gewijzigd. Hoe kom ik erachter waarom?

Ga naar het [Microsoft 365 Security Center](https://security.microsoft.com/), waar u uw volledige micro soft Secure Score kunt vinden. U kunt eenvoudig alle wijzigingen in uw beveiligde Score zien door de uitgebreide wijzigingen op het tabblad geschiedenis te bekijken.

### <a name="does-the-secure-score-measure-my-risk-of-getting-breached"></a>Meet de beveiligde Score mijn risico op het ontvangen van een schending?

Nee, dat niet. De beveiligde score geeft geen absolute meting van de waarschijnlijkheid dat u wordt geschonden. De score geeft aan in welke mate u functies hebt geïmplementeerd die het risico van een beveiligingsprobleem beperken. Geen enkele service kan garanderen dat u niet wordt overtreden en de beveiligde Score mag op geen enkele manier worden geïnterpreteerd als garantie.

### <a name="how-should-i-interpret-my-score"></a>Hoe moet ik mijn score interpreteren?

U krijgt punten voor het configureren van aanbevolen beveiligingsfuncties of het uitvoeren van beveiligingstaken (zoals het lezen van rapporten). Sommige acties krijgen een score voor gedeeltelijke uitvoering, zoals het inschakelen van meervoudige verificatie (MFA) voor uw gebruikers. Uw beveiligde Score is rechtstreeks representatief voor de micro soft-beveiligings services die u gebruikt. Houd er rekening mee dat de beveiliging moet worden uitgebalanceerd met de bruikbaarheid. Alle beveiligingsmechanismen hebben in meer of mindere mate impact op de gebruiker. Mechanismen met een kleine impact op de gebruiker hebben geen tot nauwelijks gevolgen voor de dagelijkse taken van uw gebruikers.

Als u de Score geschiedenis wilt zien, gaat u naar het [Microsoft 365 Security Center](https://security.microsoft.com/) en bekijkt u uw algemene micro soft-beveiligde Score. U kunt de wijzigingen van uw algemene beveiligde Score controleren door te klikken op de weer gave geschiedenis. Kies een specifieke datum om te zien welke mechanismen waren ingeschakeld voor die dag en wat dat voor punten heeft opgeleverd.

### <a name="how-does-the-identity-secure-score-relate-to-the-office-365-secure-score"></a>Hoe is id-beveiligingsscore gerelateerd aan de beveiligingsscore van Office 365?

De [micro soft Secure Score](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score) bevat vijf afzonderlijke besturings-en Score Categorieën:

- Identiteit
- Gegevens
- Apparaten
- Infrastructuur
- Apps

De beveiligde Score van de identiteit vertegenwoordigt het identiteits deel van de micro soft Secure Score. Deze overlap ping houdt in dat uw aanbevelingen voor de identiteits veilige Score en de identiteits Score in micro soft hetzelfde zijn.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over de beveiligde Score van micro soft](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score)
