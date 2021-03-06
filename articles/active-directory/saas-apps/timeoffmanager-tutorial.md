---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met TimeOffManager | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en TimeOffManager.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3685912f-d5aa-4730-ab58-35a088fc1cc3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 06aa2ddf3e7168147ec091ef6fb9826025f23364
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75561810"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-timeoffmanager"></a>Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met TimeOffManager

In deze zelf studie leert u hoe u TimeOffManager integreert met Azure Active Directory (Azure AD). Wanneer u TimeOffManager integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot TimeOffManager.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij TimeOffManager met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* TimeOffManager-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.


* TimeOffManager ondersteunt door **IDP** GEÏNITIEERDe SSO

* TimeOffManager ondersteunt **just-in-time** -gebruikers inrichting

> [!NOTE]
> De id van deze toepassing is een vaste teken reeks waarde zodat slechts één exemplaar in één Tenant kan worden geconfigureerd.


## <a name="adding-timeoffmanager-from-the-gallery"></a>TimeOffManager toevoegen uit de galerie

Als u de integratie van TimeOffManager in azure AD wilt configureren, moet u TimeOffManager uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **TimeOffManager** in het zoekvak.
1. Selecteer **TimeOffManager** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.


## <a name="configure-and-test-azure-ad-single-sign-on-for-timeoffmanager"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor TimeOffManager

Azure AD SSO met TimeOffManager configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in TimeOffManager.

Als u Azure AD SSO wilt configureren en testen met TimeOffManager, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[TIMEOFFMANAGER SSO configureren](#configure-timeoffmanager-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een TimeOffManager-test gebruiker](#create-timeoffmanager-test-user)** -om een equivalent van B. Simon in TimeOffManager te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **TimeOffManager** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **basis configuratie van SAML** de waarden in voor de volgende velden:

    In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon: `https://www.timeoffmanager.com/cpanel/sso/consume.aspx?company_id=<companyid>`

    > [!NOTE]
    > Deze waarde is niet echt. Werk deze waarde bij met de werkelijke antwoord-URL. U kunt deze waarde ophalen van de **pagina instellingen voor eenmalige aanmelding** die verderop in de zelf studie wordt uitgelegd of contact opnemen met [TimeOffManager-ondersteunings team](https://www.purelyhr.com/contact-us). U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. De TimeOffManager-toepassing verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/edit-attribute.png)

1. Daarnaast verwacht TimeOffManager toepassing nog maar weinig kenmerken die worden door gegeven in de SAML-respons die hieronder worden weer gegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze controleren volgens uw vereiste.

    | Name | Bronkenmerk|
    | --- | --- |
    | Voornaam |Gebruiker. voor rang |
    | Achternaam |Gebruikers naam |
    | E-mail |User.mail |

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Op de sectie **TimeOffManager instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **gebruikers naam** de username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan TimeOffManager.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **TimeOffManager**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-timeoffmanager-sso"></a>TimeOffManager SSO configureren

1. Meld u in een ander webbrowser venster als beheerder aan bij uw TimeOffManager-bedrijfs site.

2. Ga naar **account \> account opties \> instellingen voor eenmalige aanmelding**.
   
    ![Instellingen voor eenmalige aanmelding](./media/timeoffmanager-tutorial/ic795917.png "Instellingen voor eenmalige aanmelding")

3. Voer de volgende stappen uit in de sectie **instellingen voor eenmalige aanmelding** :
   
    ![Instellingen voor eenmalige aanmelding](./media/timeoffmanager-tutorial/ic795918.png "Instellingen voor eenmalige aanmelding")
   
    a. Open uw met base 64 versleutelde certificaat in Klad blok, kopieer de inhoud ervan naar het klem bord en plak het hele certificaat in het tekstvak **X. 509-certificaat** .
   
    b. Plak in het tekstvak **IDP Issuer** de waarde van de **Azure ad-id** die u van Azure Portal hebt gekopieerd.
   
    c. Plak in het tekstvak URL van het **IDP-eind punt** de waarde van de **aanmeldings-URL** die u hebt gekopieerd uit Azure Portal.
   
    d. Selecteer **Nee**bij **SAML afdwingen**.
   
    e. Selecteer **Ja**als **gebruikers automatisch maken**.
   
    f. Plak in het tekstvak **Logout URL** de waarde van **Afmeldings-URL** die u hebt gekopieerd uit de Azure-portal.
   
    g. Klik op **wijzigingen opslaan**.

4. Kopieer op de pagina **instellingen voor eenmalige aanmelding** de waarde van de service-URL van de **Assertion Consumer** en plak deze in het tekstvak **antwoord-URL** onder **Basic SAML-configuratie** in azure Portal. 

      ![Instellingen voor eenmalige aanmelding](./media/timeoffmanager-tutorial/ic795915.png "Instellingen voor eenmalige aanmelding")

### <a name="create-timeoffmanager-test-user"></a>TimeOffManager-test gebruiker maken

In deze sectie wordt een gebruiker met de naam Julia Simon gemaakt in TimeOffManager. TimeOffManager biedt ondersteuning voor Just-in-time-gebruikers inrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in TimeOffManager, wordt er een nieuwe gemaakt na verificatie.

>[!NOTE]
>U kunt alle andere hulpprogram ma's voor het maken van TimeOffManager-gebruikers accounts of Api's die worden geleverd door TimeOffManager, gebruiken om Azure AD-gebruikers accounts in te richten.

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel TimeOffManager in het toegangs venster klikt, moet u automatisch worden aangemeld bij de TimeOffManager waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer TimeOffManager met Azure AD](https://aad.portal.azure.com/)

