---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met Coralogix | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Coralogix.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ba79bfc1-992e-4924-b76a-8eb0dfb97724
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7e56d2104fd5e82b60f4e6aaa32d1bbc191d67d4
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75638724"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-coralogix"></a>Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met Coralogix

In deze zelf studie leert u hoe u Coralogix integreert met Azure Active Directory (Azure AD). Wanneer u Coralogix integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Coralogix.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij Coralogix met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Coralogix-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Coralogix biedt ondersteuning voor met **SP** geïnitieerde eenmalige aanmelding

> [!NOTE]
> De id van deze toepassing is een vaste teken reeks waarde zodat slechts één exemplaar in één Tenant kan worden geconfigureerd.

## <a name="adding-coralogix-from-the-gallery"></a>Coralogix toevoegen vanuit de galerie

Als u de integratie van Coralogix in Azure AD wilt configureren, moet u Coralogix vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Coralogix** in het zoekvak.
1. Selecteer **Coralogix** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-coralogix"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor Coralogix

Azure AD SSO met Coralogix configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Coralogix.

Als u Azure AD SSO wilt configureren en testen met Coralogix, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[CORALOGIX SSO configureren](#configure-coralogix-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een Coralogix-test gebruiker](#create-coralogix-test-user)** -om een equivalent van B. Simon in Coralogix te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **Coralogix** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **basis configuratie van SAML** de waarden in voor de volgende velden:

    a. Voer in het vak **AANMELDINGS URL** een URL in met het volgende patroon: `https://<SUBDOMAIN>.coralogix.com`

    b. Voer in het tekstvak **id (Entiteits-ID)** een URL in, bijvoorbeeld:
    
    `https://api.coralogix.com/saml/metadata.xml`

    of

    `https://aws-client-prod.coralogix.com/saml/metadata.xml` 

    > [!NOTE]
    > De waarde voor de aanmeldings-URL is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [ondersteunings team van Coralogix-clients](mailto:info@coralogix.com) om de waarde op te halen. U kunt ook verwijzen naar de patronen in het gedeelte **basis configuratie van SAML** in de Azure Portal.

 1. De Coralogix-toepassing verwacht de SAML-beweringen in een specifieke indeling. Configureer de volgende claims voor deze toepassing. U kunt de waarden van deze kenmerken vanuit de sectie **Gebruikerskenmerken** op de integratiepagina van de toepassing-beheren. Selecteer op de pagina **eenmalige aanmelding met SAML instellen** de knop **bewerken** om het dialoog venster **gebruikers kenmerken** te openen.

    ![installatiekopie](common/edit-attribute.png)

1. Bewerk de claims in het gedeelte **gebruikers claims** in het dialoog venster **gebruikers kenmerken** met behulp van het **bewerkings** pictogram. U kunt de claims ook toevoegen met behulp van **nieuwe claim toevoegen** om het SAML-token kenmerk te configureren, zoals wordt weer gegeven in de vorige afbeelding. Voer dan de volgende stappen uit:
    
    a. Selecteer het **bewerkings pictogram** om het dialoog venster **gebruikers claims beheren** te openen.

    ![installatie kopie](./media/coralogix-tutorial/tutorial_usermail.png) ![installatie kopie](./media/coralogix-tutorial/tutorial_usermailedit.png)

    b. Selecteer in de lijst **Kies een indeling voor de naam-id** de optie **E-mailadres**.

    c. Selecteer in de lijst **Bronkenmerk** de optie **user.mail**.

    d. Selecteer **Opslaan**.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **federatieve meta gegevens-XML** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Op de sectie **Coralogix instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Coralogix.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst met toepassingen de optie **Coralogix**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-coralogix-sso"></a>Coralogix SSO configureren

Als u eenmalige aanmelding aan de **Coralogix**-zijde wilt configureren, moet u het gedownloade **Certificaat (Raw)** en de correcte uit de Azure-portal gekopieerde URL's verzenden naar het [Coralogix-ondersteuningsteam](mailto:info@coralogix.com). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-coralogix-test-user"></a>Coralogix-testgebruiker maken

In deze sectie maakt u een gebruiker in Coralogix met de naam Britta Simon. Werk samen met het  [Coralogix-ondersteuningsteam](mailto:info@coralogix.com) om gebruikers toe te voegen op het Coralogix-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Coralogix klikt, wordt u, als het goed is, automatisch aangemeld bij het Coralogix-exemplaar waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Coralogix met Azure AD](https://aad.portal.azure.com/)

