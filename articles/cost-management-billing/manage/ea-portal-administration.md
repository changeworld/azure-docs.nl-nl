---
title: Beheer van Azure EA Portal
description: In dit artikel worden de algemene taken beschreven die een beheerder in Azure EA Portal uitvoert.
author: bandersmsft
ms.author: banders
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: boalcsva
ms.openlocfilehash: 79225d4dfe9e53da6936f8647c9f5a1dff0b4909
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78301469"
---
# <a name="azure-ea-portal-administration"></a>Beheer van Azure EA Portal

In dit artikel worden de algemene taken beschreven die een beheerder in Azure EA Portal uitvoert (https://ea.azure.com). Azure EA Portal is een online beheerportal waarmee klanten de kosten van hun Azure EA-services kunnen beheren. Zie het artikel [Aan de slag met Azure EA Portal](ea-portal-get-started.md) voor inleidende informatie over Azure EA Portal.

## <a name="add-a-new-enterprise-administrator"></a>Een nieuwe ondernemingsbeheerder toevoegen

Ondernemingsbeheerders beschikken over de meeste bevoegdheden voor het beheren van een Azure EA-inschrijving. De eerste Azure EA-beheerder is gemaakt toen de EA-overeenkomst is gesloten. U kunt echter op elk moment nieuwe beheerders toevoegen of verwijderen. Nieuwe beheerders kunnen alleen worden toegevoegd door bestaande beheerders. Zie [Een andere ondernemingsbeheerder maken](ea-portal-get-started.md#create-another-enterprise-administrator) voor meer informatie over het toevoegen van ondernemingsbeheerders. Zie [Rollen en taken voor factureringsprofiel ](understand-mca-roles.md#billing-profile-roles-and-tasks) voor meer informatie over de rollen en taken voor het factureringsprofiel.

## <a name="update-user-state-from-pending-to-active"></a>De status van een gebruiker bijwerken van in behandeling naar actief

Wanneer nieuwe accounteigenaren (AO) voor de eerste keer worden toegevoegd aan een Azure EA-inschrijving, krijgen ze de status _In behandeling_. Zodra een nieuwe accounteigenaar de welkomstmail voor de activering ontvangt, kan deze zich aanmelden om het account te activeren. Zodra het account wordt geactiveerd, wordt de accountstatus bijgewerkt van _In behandeling_ naar _Actief_. De accounteigenaar moet het waarschuwingsbericht lezen en **Doorgaan** selecteren. Nieuwe gebruikers worden mogelijk gevraagd hun voor- en achternaam op te geven om een Commerce-account te maken. Als dat het geval is, moeten ze de vereiste informatie toevoegen om door te gaan, waarna het account wordt geactiveerd.

## <a name="add-a-department-admin"></a>Een afdelingsbeheerder toevoegen

Nadat een Azure EA-beheerder een afdeling heeft gemaakt, kan de Azure Enterprise-beheerder afdelingsbeheerders toevoegen en deze koppelen aan een afdeling. Een afdelingsbeheerder kan nieuwe accounts maken. Nieuwe accounts zijn nodig om nieuwe Azure EA-abonnementen te maken.

Raadpleeg [Een Azure EA-afdelingsbeheerder](ea-portal-get-started.md#add-a-department-administrator) maken voor meer informatie over het toevoegen van een afdelingsbeheerder.

## <a name="associate-an-account-to-a-department"></a>Een account aan een afdeling koppelen

Ondernemingsbeheerders kunnen bestaande accounts aan afdelingen onder de inschrijving koppelen.

### <a name="to-associate-an-account-to-a-department"></a>Een account aan een afdeling koppelen

1. Meld u als ondernemingsbeheerder aan bij Azure EA Portal.
1. Selecteer **Beheren** in het navigatievenster aan de linkerkant.
1. Selecteer **Afdeling**.
1. Beweeg de muisaanwijzer over de rij met het account en selecteer het potloodpictogram aan de rechterkant.
1. Selecteer de afdeling in het vervolgkeuzemenu.
1. Selecteer **Opslaan**.

## <a name="department-spending-quotas"></a>Bestedingsquota van afdeling

EA-klanten kunnen bestedingsquota instellen of wijzigen voor elke afdeling onder een inschrijving. De hoogte van het bestedingsquotum wordt voor de huidige toezeggingstermijn ingesteld. Aan het einde van de huidige toezeggingstermijn wordt het bestaande bestedingsquotum naar de volgende toezeggingstermijn uitgebreid, tenzij de waarden worden bijgewerkt.

De afdelingsbeheerder kan het bestedingsquotum zien, maar de hoogte ervan kan uitsluitend door de ondernemingsbeheerder worden bijgewerkt. De ondernemingsbeheerder en de afdelingsbeheerder krijgen meldingen wanneer 50%, 75%, 90% en 100% van een quotum is bereikt.

### <a name="enterprise-administrator-to-set-the-quota"></a>Een ondernemingsbeheerder kan het quotum als volgt instellen:

 1. Open Azure EA Portal.
 1. Selecteer **Beheren** in het navigatievenster aan de linkerkant.
 1. Selecteer het tabblad **Afdeling**.
 1. Selecteer de afdeling.
 1. Selecteer in de sectie Afdelingsgegevens het potloodpictogram of selecteer het pictogram **+ Afdeling toevoegen** om een bestedingsquotum aan een nieuwe afdeling toe te voegen.
 1. Voer onder Afdelingsgegevens in het vak Bestedingsquotum $ de hoogte van het bestedingsquotum in (in de valuta van de inschrijving). Deze waarde moet hoger zijn dan 0.
    - Op dit punt kunt u ook de Afdelingsnaam en de Kostenplaats bewerken.
 1. Selecteer **Opslaan**.

Het bestedingsquotum voor de afdeling is nu zichtbaar in de weergave Afdelingslijst op het tabblad Afdeling. Aan het einde van de huidige toezegging blijven de bestedingsquota voor de volgende toezeggingstermijn door Azure EA Portal gehandhaafd.

De hoogte van het bestedingsquotum is onafhankelijk van de huidige financiële toezegging, en de hoogte van het quotum en meldingen zijn alleen van toepassing op gebruik van interne producten. Het bestedingsquotum voor de afdeling is alleen bedoeld ter informatie en kan niet worden gebruikt om bestedingslimieten af te dwingen.

### <a name="department-administrator-to-view-the-quota"></a>De afdelingsbeheerder kan het quotum als volgt bekijken:

1. Open Azure EA Portal.
1. Selecteer **Beheren** in het navigatievenster aan de linkerkant.
1. Selecteer het tabblad **Afdeling** en bekijk de weergave Afdelingslijst met daarin de bestedingsquota.

Als u een indirecte klant bent, moeten kostenfuncties door uw kanaalpartner zijn ingesteld.

## <a name="enterprise-user-roles"></a>Enterprise-gebruikersrollen

Met Azure EA Portal kunt u de kosten en het gebruik van Azure EA beheren. Er zijn drie hoofdrollen in Azure EA Portal:

- EA-beheerder
- Afdelingsbeheerder
- Accounteigenaar

Elke rol heeft een ander toegangs- en autoriteitsniveau.

Zie [Enterprise-gebruikersrollen](https://docs.microsoft.com/azure/billing/billing-ea-portal-get-started#enterprise-user-roles) voor meer informatie over rollen.

## <a name="add-an-azure-ea-account"></a>Een Azure EA-account toevoegen

Het Azure EA-account is een organisatie-eenheid in de Azure EA-portal. Dit account wordt gebruikt om abonnementen te beheren en rapporten te maken. Voor toegang tot en het gebruik van Azure-services moet u een account maken of laten maken.

Zie Een account toevoegen voor meer informatie over Azure-accounts.

## <a name="enterprise-devtest-offer"></a>Aanbieding voor Enterprise Dev/Test

Als Azure-ondernemingsbeheerder kunt u accounteigenaren in uw organisatie de mogelijkheid geven om abonnementen te maken op basis van de EA Dev/Test-aanbieding. Als u dit wilt doen, selecteert u het vak Dev/Test voor de accounteigenaar in de Azure EA-portal.

Zodra u het selectievakje Dev/Test hebt ingeschakeld, brengt u de accounteigenaar hiervan op de hoogte zodat hij of zij de EA Dev/Test-abonnementen kan instellen die voor zijn of haar teams van Dev/Test-abonnees benodigd zijn.

Hierdoor kunnen actieve Visual Studio-abonnees de ontwikkeling uitvoeren en de werklast op Azure met speciale Dev/Test-snelheden testen. Het biedt toegang tot de volledige galerie Dev/Test-installatiekopieën, waaronder Windows 8.1 en Windows 10.

### <a name="to-set-up-the-enterprise-devtest-offer"></a>U kunt de Enterprise Dev/Test-aanbieding als volgt instellen:

1. Meld u aan als de ondernemingsbeheerder.
1. Selecteer **Beheren** in het navigatievenster aan de linkerkant.
1. Selecteer het tabblad **Account**.
1. Selecteer de rij voor het account waarvoor u toegang tot Dev/Test wilt inschakelen.
1. Selecteer rechts naast de rij het potloodpictogram.
1. Schakel het selectievakje Dev/Test in.
1. Selecteer **Opslaan**.

Wanneer een gebruiker via Azure EA Portal als accounteigenaar wordt toegevoegd, worden alle Azure-abonnementen die aan de accounteigenaar zijn gekoppeld en zijn gebaseerd op ofwel de Dev/Test-aanbieding met betalen per gebruik of de aanbiedingen voor maandelijks tegoed voor Visual Studio-abonnees omgezet naar de EA Dev/Test-aanbieding. Abonnementen op basis van andere typen, zoals betalen per gebruik, die aan de accounteigenaar zijn gekoppeld, worden omgezet naar Microsoft Azure Enterprise-aanbiedingen.

De Dev/Test-aanbieding is op dit moment niet van toepassing op Azure Gov-klanten.

## <a name="transfer-an-enterprise-account-to-a-new-enrollment"></a>Een ondernemingsaccount overdragen naar een nieuwe inschrijving

Bij een accountoverdracht wordt een accounteigenaar verplaatst van de ene inschrijving naar een andere. Alle verwante abonnementen onder de accounteigenaar worden verplaatst naar de doelinschrijving. Gebruik een accountoverdracht wanneer u meerdere actieve inschrijvingen hebt en alleen de geselecteerde accounteigenaren wilt verplaatsen.

Deze sectie is alleen bedoeld voor informatieve doeleinden, omdat de actie niet kan worden uitgevoerd door een ondernemingsbeheerder. Er is een ondersteuningsaanvraag nodig om een ondernemingsaccount over te brengen naar een nieuwe inschrijving.

Houd rekening met de volgende punten wanneer u een Enterprise-account overdraagt naar een nieuwe inschrijving:

- Alleen de accounts in de aanvraag worden overgedragen. Als alle accounts worden gekozen, worden ze allemaal overgedragen.
- De status van de broninschrijving blijft actief of uitgebreid. U kunt de inschrijving blijven gebruiken totdat deze verloopt.

### <a name="prerequisites"></a>Vereisten

Wanneer u een accountoverdracht aanvraagt, verstrekt u de volgende gegevens:

- Het nummer van de doelinschrijving, de accountnaam, en het e-mailadres van de accounteigenaar voor het account dat moet worden overgedragen
- Voor de broninschrijving het inschrijvingsnummer en het account dat moet worden overgedragen

Andere punten waarmee u rekening moet houden voordat u een account overdraagt:

- Goedkeuring van een EA-beheerder is vereist voor de doel- en broninschrijving
- Als een accountoverdracht niet aan uw vereisten voldoet, kunt u een inschrijvingsoverdracht overwegen.
- De accountoverdracht draagt alle services en abonnementen over die betrekking hebben op de specifieke accounts.
- Nadat de overdracht is voltooid, verschijnt het overgedragen account als inactief onder de broninschrijving en als actief onder de doelinschrijving.
- In het account wordt de einddatum getoond die correspondeert met de effectieve overdrachtsdatum op de broninschrijving en als een begindatum op de doelinschrijving.
- Elk gebruik dat met het account is uitgevoerd vóór de effectieve overgangsdatum, blijft onder de broninschrijving bestaan.


## <a name="transfer-enterprise-enrollment-to-a-new-one"></a>Enterprise-inschrijving overdragen naar een nieuwe inschrijving

Een inschrijvingsoverdracht wordt overwogen wanneer:

- De toezeggingstermijn van een huidige inschrijving tot een einde is gekomen.
- Een inschrijving de status verlopen/uitgebreid heeft en er wordt onderhandeld over een nieuwe overeenkomst.
- U meerdere inschrijvingen hebt en alle accounts en facturering wilt samenvoegen onder één inschrijving.

Deze sectie is alleen bedoeld voor informatieve doeleinden, omdat de actie niet kan worden uitgevoerd door een ondernemingsbeheerder. Er is een ondersteuningsaanvraag nodig om een ondernemingsinschrijving over te brengen naar een nieuwe inschrijving.

Wanneer u de overdracht van een volledige Enterprise-inschrijving naar een inschrijving aanvraagt, worden de volgende acties uitgevoerd:

- Alle services, abonnementen, accounts en afdelingen van Azure, en de volledige inschrijvingsstructuur, inclusief alle EA-afdelingsbeheerders, worden overgedragen naar een nieuwe doelinschrijving.
- De inschrijvingsstatus wordt ingesteld op _Overgedragen_. De overgedragen inschrijving is alleen beschikbaar voor rapportage van het historische gebruik.
- U kunt geen rollen of abonnementen toevoegen aan een overgedragen inschrijving. De status Overgedragen voorkomt aanvullend gebruik van de inschrijving.
- Het saldo van de resterende financiële toezegging in de overeenkomst gaat verloren, met inbegrip van toekomstige voorwaarden.
-    Als de inschrijving van waaruit u de overdracht uitvoert over RI-aankopen beschikt, blijven de kosten voor de aanschaf van RI in de broninschrijving. Alle RI-voordelen worden echter overgedragen zodat u deze in de nieuwe inschrijving kunt gebruiken.
-    De eenmalige aanschafkosten via de marketplace en eventuele vaste maandelijkse kosten die al bij de oude inschrijving in rekening zijn gebracht, worden niet naar de nieuwe inschrijving overgedragen. Marketplace-kosten op basis van gebruik worden wel overgedragen.

### <a name="effective-transfer-date"></a>Werkelijke overdrachtsdatum

De werkelijke overdrachtsdatum kan zijn op of na de startdatum van de doelinschrijving.

Het gebruik van de broninschrijving wordt verrekend met de financiële toezegging of in rekening gebracht als overschrijding. Gebruik dat plaatsvindt na de werkelijke overdrachtsdatum, wordt overgedragen naar de nieuwe inschrijving en dienovereenkomstig in rekening gebracht.

### <a name="prerequisites"></a>Vereisten

Wanneer u een inschrijvingsoverdracht aanvraagt, verstrekt u de volgende gegevens:

- Voor de broninschrijving is dit het inschrijvingsnummer.
- Voor de doelinschrijving het inschrijvingsnummer waarnaar moet worden overgedragen.
- De werkelijke datum van de inschrijvingsoverdracht kan een datum op of na de begindatum van de doelinschrijving zijn. De gekozen datum is niet van invloed op het gebruik voor een overschrijdingsfactuur die al is uitgegeven.

Andere punten waarmee u rekening moet houden voordat u een inschrijving overdraagt:

- Goedkeuring van een EA-beheerder is vereist voor zowel de doel- als broninschrijving.
- Als een inschrijvingsoverdracht niet aan uw vereisten voldoet, kunt u een accountoverdracht overwegen.
- De status van de broninschrijving wordt bijgewerkt naar overgedragen, en is alleen beschikbaar voor rapportagedoeleinden over historisch gebruik.

### <a name="monetary-commitment"></a>Financiële toezegging

De financiële toezegging is niet overdraagbaar tussen inschrijvingen. De financiële toezeggingssaldi zijn contractueel gebonden aan de inschrijving waar ze zijn besteld. De financiële toezegging is niet overdraagbaar als onderdeel van het overdrachtsproces voor het account of de inschrijving.

### <a name="no-services-affected-for-account-and-enrollment-transfers"></a>Er worden geen services beïnvloed voor de overdracht van accounts en inschrijvingen

Er is geen downtime tijdens de overdracht van accounts en inschrijvingen. Indien alle vereiste gegevens worden verstrekt, kan de overdracht nog op de dag van uw aanvraag worden voltooid.

## <a name="change-account-owner"></a>Accounteigenaar wijzigen

In Azure EA Portal kunnen abonnementen worden overdragen van de ene accounteigenaar naar de andere. Zie [Accounteigenaar wijzigen](ea-portal-get-started.md#change-account-owner) voor meer informatie.

## <a name="subscription-transfer-effects"></a>Gevolgen van een abonnementsoverdracht

Wanneer een Azure-abonnement wordt overgedragen naar een account in een andere tenant, verliezen alle gebruikers, groepen en service-principals met [op rollen gebaseerde toegang (RBAC)](../../role-based-access-control/overview.md) voor het beheren van de resources in het abonnement, hun toegang.

Gebruikers met RBAC-toegang tot het abonnement weergeven:

1. Open in Azure Portal **Abonnementen**.
2. Selecteer het abonnement dat u wilt weergeven en selecteer vervolgens **Toegangsbeheer (IAM)** .
3. Selecteer **Roltoewijzingen**. De pagina met roltoewijzingen bevat een overzicht van alle gebruikers met RBAC-toegang tot het abonnement.

Als het abonnement wordt overgedragen naar een account in een andere Azure AD-tenant, verliezen alle gebruiker, groepen en service-principals met [RBAC](../../role-based-access-control/overview.md) om resources te beheren, _hun toegang_. Hoewel er geen RBAC-toegang aanwezig is, is het abonnement mogelijk toegankelijk via een beveiligingsmechanisme, inclusief:

- Beheercertificaten die de gebruiker beheerdersrechten verlenen voor de abonnementsresources. Zie [Een beheercertificaat voor Azure maken en uploaden](../../cloud-services/cloud-services-certs-create.md) voor meer informatie.
- Toegangssleutels voor services zoals Storage. Zie [Overzicht van Azure-opslagaccount](../../storage/common/storage-account-overview.md) voor meer informatie.
- Referenties voor externe toegang voor services zoals Azure Virtual Machines.

Als ontvangers de toegang tot hun Azure-resources willen beperken, moeten ze overwegen om de geheimen bij te werken die zijn gekoppeld aan de service. De meeste resources kunnen worden bijgewerkt door de volgende stappen uit te voeren:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Selecteer in het Hub-menu **Alle resources**.
3. Selecteer de resource.
4. Selecteer op de resourcepagina **Instellingen** om bestaande geheimen weer te geven en bij te werken.

## <a name="delete-subscription"></a>Abonnement verwijderen

Als u een abonnement waarvoor u de accounteigenaar bent, wilt verwijderen, doet u het volgende:

1. Meld u aan bij Azure Portal met de referenties die bij uw account horen.
1. Selecteer **Abonnementen** op het Hub-menu.
1. Ga naar het tabblad Abonnementen in de linkerbovenhoek van de pagina, selecteer het abonnement dat u wilt opzeggen en selecteer **Abonnement opzeggen** om het tabblad Opzeggen te openen.
1. Voer de abonnementsnaam in en kies een reden voor het opzeggen. Selecteer daarna de knop **Abonnement opzeggen**.

Alleen accountbeheerders kunnen abonnementen opzeggen.

Zie voor meer informatie [Wat gebeurt er nadat ik mijn abonnement heb opgezegd?](cancel-azure-subscription.md#what-happens-after-i-cancel-my-subscription)

## <a name="delete-an-account"></a>Een account verwijderen

De verwijdering van een account kan alleen worden voltooid voor actieve accounts zonder actieve abonnementen.

1. Selecteer in Enterprise-portal in het linkernavigatievenster de optie **Beheren**.
1. Selecteer het tabblad **Account**.
1. Ga naar de tabel Accounts en selecteer het account dat u wilt verwijderen.
1. Selecteer het X-pictogram rechts naast de rij Account.
1. Zodra het account geen actieve abonnementen meer heeft, selecteert u **Ja** onder de rij Account om te bevestigen dat u het account wilt verwijderen.

## <a name="update-notification-settings"></a>Instellingen voor meldingen bijwerken

Ondernemingsbeheerders worden automatisch ingeschreven voor het ontvangen van gebruiksmeldingen die aan hun inschrijving zijn gekoppeld. Iedere ondernemingsbeheerder kan het interval voor de afzonderlijke meldingen wijzigen of deze volledig uitschakelen.

Contactpersonen voor meldingen worden in Azure EA Portal weergegeven in het gedeelte **Contactpersoon voor meldingen**. Door uw contactpersonen voor meldingen te beheren, zorgt u ervoor dat de juiste personen in uw organisatie Azure EA-meldingen ontvangen.

De huidige instellingen voor meldingen weergeven:

1. Ga in Azure EA Portal naar **Beheren** > **Contactpersoon voor meldingen**.
2. E-mailadres: het e-mailadres dat is gekoppeld aan het Microsoft-, werk- of schoolaccount van de ondernemingsbeheerder waarop de meldingen worden ontvangen.
3. Meldingsfrequentie niet-gefactureerd saldo: de ingestelde frequentie waarmee de afzonderlijke ondernemingsbeheerders meldingen ontvangen.

Een contactpersoon toevoegen:

1. Selecteer **+Contactpersoon toevoegen**.
2. Voer het e-mailadres in en bevestig dit.
3. Selecteer **Opslaan**.

De nieuwe contactpersoon voor meldingen wordt weergegeven in het gedeelte **Contactpersoon voor meldingen**. Als u de meldingsfrequentie wilt wijzigen, selecteert u de contactpersoon voor meldingen en selecteert u rechts van de geselecteerde rij het potloodpictogram. Stel de frequentie in **dagelijks**, **wekelijks**, **maandelijks** of **geen**.

U kunt de _naderende einddatum van de dekkingsperiode_ onderdrukken en de meldingen over de levenscyclus _uitschakelen en de inrichting van de datum ongedaan maken_. Als u de meldingen over de levenscyclus uitschakelt, worden meldingen over de dekkingsperiode en de einddatum van de overeenkomst onderdrukt.

## <a name="manage-partner-administrators"></a>Partnerbeheerders beheren

Elke partnerbeheerder in Azure EA Portal beschikt over de mogelijkheid om andere partnerbeheerders toe te voegen of te verwijderen. Partnerbeheerders zijn gekoppeld aan de partnerorganisaties van indirecte inschrijvingen en zijn niet rechtstreeks aan de inschrijvingen gekoppeld.

### <a name="add-a-partner-administrator"></a>Een partnerbeheerder toevoegen

Selecteer het tabblad **Inschrijving** en schakel het gewenste inschrijvingsvakje in om een lijst weer te geven met alle inschrijvingen die als huidige gebruiker aan diezelfde partnerorganisatie zijn gekoppeld.

1. Meld u aan als partnerbeheerder.
1. Selecteer **Beheren** in het navigatievenster aan de linkerkant.
1. Selecteer het tabblad **Partner**.
1. Selecteer **+ Beheerder toevoegen** en vul het e-mailadres, de contactpersoon voor meldingen en meldingsgegevens in.
1. Selecteer **Toevoegen**.

### <a name="remove-a-partner-administrator"></a>Een partnerbeheerder verwijderen

Selecteer het tabblad **Inschrijving** en schakel het gewenste inschrijvingsvakje in om een lijst weer te geven met alle inschrijvingen die als huidige gebruiker aan diezelfde partnerorganisatie zijn gekoppeld.

1. Meld u aan als partnerbeheerder.
1. Selecteer **Beheren** in het navigatievenster aan de linkerkant.
1. Selecteer het tabblad **Partner**.
1. Selecteer in de sectie Beheerder de juiste rij voor de beheerder die u wilt verwijderen.
1. Selecteer het X-pictogram aan de rechterkant.
1. Bevestig dat u de beheerder wilt verwijderen.

## <a name="manage-partner-notifications"></a>Partnermeldingen beheren

Partnerbeheerders kunnen beheren met welke frequentie ze gebruiksmeldingen voor hun inschrijvingen ontvangen. Ze ontvangen automatisch wekelijkse meldingen over hun niet-gefactureerd saldo. Ze kunnen de frequentie van afzonderlijke meldingen wijzigen in maandelijks, wekelijks, dagelijks of ze volledig uitschakelen.

Als er geen melding door een gebruiker wordt ontvangen, controleert u aan de hand van de volgende stappen of de instellingen voor meldingen van de gebruiker juist.

1. Meld u als een partnerbeheerder aan bij Azure EA Portal.
2. Selecteer **Beheren** en selecteer vervolgens het tabblad **Partner**.
3. Bekijk in de sectie Beheerder de lijst met beheerders.
4. Als u de meldingsvoorkeuren wilt bewerken, plaatst u de muisaanwijzer op de desbetreffende beheerder en selecteert u het potloodpictogram.
5. Verhoog de meldingsfrequentie en het aantal meldingen over de levenscyclus naar wens.
6. Voeg indien nodig een contactpersoon toe en selecteer **Toevoegen**.
7. Selecteer **Opslaan**.

![Voorbeeld met het venster Contactpersoon toevoegen ](./media/ea-portal-administration/create-ea-manage-partner-notification.png)

## <a name="view-enrollments-for-partner-administrators"></a>Inschrijvingen voor partnerbeheerders weergeven

Partnerbeheerders kunnen een lijst met al hun directe en indirecte inschrijvingen in Azure EA Portal weergeven. Er worden vakken met een overzicht van elke inschrijving weergegeven, voorzien van het inschrijvingsnummer, de inschrijvingsnaam, het saldo en de hoogte van overschrijdingen.

### <a name="view-a-list-of-enrollments"></a>Een lijst met inschrijvingen weergeven

1. Meld u aan als partnerbeheerder.
1. Selecteer in het navigatiemenu aan de linkerkant van de pagina **Beheren**.
1. Selecteer het tabblad **Inschrijving**.
1. Schakel het selectievakje in voor de inschrijving.

Bovenaan de pagina blijft een lijst met alle inschrijvingen zichtbaar, waarbij elke inschrijving van een selectievakje is voorzien. Daarnaast kunt u tussen inschrijvingen navigeren door in het navigatiemenu aan de linkerkant van de pagina het nummer van de huidige inschrijving te selecteren. Er wordt een pop-upbericht weergegeven waarin u naar inschrijvingen kunt zoeken of een andere inschrijving kunt selecteren door het desbetreffende selectievakje te selecteren.

## <a name="azure-sponsorship-offer"></a>Azure Sponsorship-aanbieding

De Azure Sponsorship-aanbieding is een beperkt gesponsord Microsoft Azure-account. De aanbieding is beschikbaar via een e-mailuitnodiging en alleen voor een beperkt aantal, door Microsoft geselecteerde klanten. Als u recht hebt op de aanbieding voor Microsoft Azure Sponsorship, ontvangt u een e-mailuitnodiging voor uw account-id.

Maak een [ondersteuningsaanvraag voor activering van sponsoring](https://aka.ms/azrsponsorship) voor meer informatie.

## <a name="conversion-to-work-or-school-account-authentication"></a>Verificatie van de omzetting naar een werk- of schoolaccount

Azure Enterprise-gebruikers kunnen een Microsoft-account (MSA of Live ID) omzetten naar het verificatietype van een werk- of schoolaccount (waarvoor Active Directory in Azure wordt gebruikt).

U gaat als volgt aan de slag:

1. Voeg het werk- of schoolaccount toe aan Azure EA Portal bij de benodigde rol(len).
1. Indien er foutmeldingen worden weergegeven, is het account mogelijk niet geldig in de Active Directory.  Azure maakt gebruik van User Principal Name (UPN). Dit is niet altijd hetzelfde als het e-mailadres.
1. Voer een verificatie bij Azure EA Portal uit met het werk- of schoolaccount.

### <a name="to-convert-subscriptions-from-microsoft-accounts-to-work-or-school-accounts"></a>U kunt abonnementen van Microsoft-accounts als volgt omzetten naar werk- of schoolaccounts:

1. Meld u aan bij de beheerportal met het Microsoft-account dat eigenaar is van de abonnementen.
1. Voer een overdracht van het accounteigendom uit om naar het nieuwe account over te stappen.
1. Als het goed is, bevat het Microsoft-account nu geen actieve abonnementen meer en kan het account dus worden verwijderd.
1. Verwijderde accounts blijven voor historische factureringsredenen in de portal zichtbaar met de status Inactief.  U kunt deze accounts met behulp van een filter verbergen door een selectievakje in te schakelen zodat alleen actieve accounts worden weergegeven.

## <a name="account-subscription-ownership-faq"></a>Veelgestelde vragen over het eigendom van accountabonnementen

In dit document worden veelgestelde vragen beantwoord met betrekking tot het eigendom van accountabonnementen.

### <a name="how-many-azure-account-owners-can-you-have-per-subscription"></a>Hoeveel Azure-accounteigenaren mogen er per abonnement worden gebruikt?

Er is per abonnement maar één accounteigenaar toegestaan.  U kunt aanvullende rollen toevoegen met de opties Op rollen gebaseerde toegang of (Toegangsbeheer (IAM)) op het tabblad Abonnement in de linkerbovenhoek van de pagina op [portal.azure.com]](https://portal.azure.com).

### <a name="can-an-azure-account-owner-be-listed-under-more-than-one-department"></a>Kan een Azure-accounteigenaar worden vermeld onder meer dan één afdeling?

Nee, een accounteigenaar kan maar aan één afdeling worden gekoppeld. We hanteren dit beleid om ervoor te zorgen dat de kosten en uitgaven die aan de afdeling waarmee ze onder de EA-inschrijving in de Azure EA-portal zijn afgestemd, nauwkeurig worden bijgehouden en verdeeld.

### <a name="can-an-azure-account-owner-be-listed-as-a-security-group"></a>Kan een Azure-accounteigenaar als een beveiligingsgroep worden vermeld?

Nee, de eigenaar van een abonnement moet een uniek Microsoft-account (MSA) of een unieke Azure Active Directory-verificatie (AAD) zijn. Als u opvolging binnen uw organisatie wilt inzetten, kunt u generieke accounts maken en AAD gebruiken om toegang tot abonnementen te beheren.

### <a name="can-an-individual-user-own-multiple-subscriptions"></a>Kan een individuele gebruiker de eigenaar van meerdere abonnementen zijn?

Een Azure-accounteigenaar kan een onbeperkt aantal abonnementen maken en beheren.

### <a name="how-can-i-accessview-all-my-organizations-subscriptions"></a>Hoe krijg ik toegang tot alle abonnementen van mijn organisatie en hoe kan ik ze weergeven?

Tegenwoordig wordt dit vanuit een beleid geregeld. Dit houdt in dat u voor elk abonnement dat wordt gemaakt, moet vereisen dat uw account aan een abonnementsrol wordt toegevoegd met behulp van op rollen gebaseerde toegang.

### <a name="where-do-i-go-to-create-a-subscription"></a>Waar kan ik een abonnement maken?

Voordat u een abonnement voor een Enterprise Azure-aanbieding (EA) kunt maken, moet uw account door de beheerder van uw EA-inschrijving in de Azure EA-portal aan de rol van accounteigenaar worden toegevoegd. Vervolgens moet u zich bij Azure de EA-portal aanmelden om recht te krijgen op het maken van abonnementen van het EA-aanbiedingstype. U wordt aangeraden om uw eerste EA-abonnement te maken via de koppeling '+ Abonnement toevoegen', op het tabblad Abonnement in de EA-portal.  Zodra uw account echter over de juiste machtigingen beschikt, is het wellicht eenvoudiger om abonnementen te maken op het tabblad Abonnement in de linkerbovenhoek van de pagina op portal.azure.com. Hier kunt u in één stap zowel uw abonnement maken als de naam daarvan wijzigen.

### <a name="who-can-create-a-subscription"></a>Wie kan een abonnement maken?

Als u een abonnement van het Enterprise Azure-aanbiedingstype wilt maken, moet u zijn gemachtigd in de rol van accounteigenaar op [EA Portal](https://ea.azure.com).

## <a name="next-steps"></a>Volgende stappen

- Lees hoe u geld kunt besparen met [VM-reserveringen](ea-portal-vm-reservations.md).
- Zie [Problemen met toegang tot Azure EA Portal](ea-portal-troubleshoot.md) als u hulp nodig hebt met het oplossen van problemen Azure EA Portal.
