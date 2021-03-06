---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met Netskope-gebruikers authenticatie | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Netskope-gebruikers verificatie.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e9bb71cd-aaf9-4403-aca5-c5a349ec562a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e33af932e405552cf9d8f5aaf6d42cbd095607b0
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74085371"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netskope-user-authentication"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met Netskope-gebruikers verificatie

In deze zelf studie leert u hoe u Netskope gebruikers verificatie kunt integreren met Azure Active Directory (Azure AD). Wanneer u Netskope gebruikers verificatie integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Netskope-gebruikers authenticatie.
* Stel in dat uw gebruikers automatisch worden aangemeld voor Netskope gebruikers verificatie met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Netskope-abonnement met eenmalige aanmelding (SSO) voor gebruikers verificatie.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Netskope-gebruikers verificatie ondersteunt SSO die door **SP en IDP** is geïnitieerd

## <a name="adding-netskope-user-authentication-from-the-gallery"></a>Netskope-gebruikers authenticatie toevoegen vanuit de galerie

Als u de integratie van Netskope-gebruikers authenticatie wilt configureren in azure AD, moet u Netskope-gebruikers authenticatie vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Netskope gebruikers verificatie** in het zoekvak.
1. Selecteer **Netskope gebruikers verificatie** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.


## <a name="configure-and-test-azure-ad-single-sign-on-for-netskope-user-authentication"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor Netskope-gebruikers authenticatie

Configureer en test Azure AD SSO met Netskope-gebruikers verificatie met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Netskope-gebruikers verificatie.

Als u Azure AD SSO wilt configureren en testen met Netskope-gebruikers verificatie, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    * **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    * **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[CONFIGUREER SSO van Netskope-gebruikers verificatie](#configure-netskope-user-authentication-sso)** -om de instellingen voor eenmalige aanmelding aan de kant van de toepassing te configureren.
    * **[Maak een Netskope-gebruikers verificatie test gebruiker](#create-netskope-user-authentication-test-user)** -om een tegen hanger te hebben van B. Simon in Netskope-gebruikers authenticatie die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Netskope-toepassings integratie voor **gebruikers verificatie** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Als u de toepassing in de gestarte modus **IDP** wilt configureren, voert u in de sectie **basis configuratie van SAML** de waarden voor de volgende velden in:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<tenantname>.goskope.com/<customer entered string>`

    b. In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon: `https://<tenantname>.goskope.com/nsauth/saml2/http-post/<customer entered string>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Deze waarden worden verderop in de zelf studie uitgelegd.

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<tenantname>.goskope.com`

    > [!NOTE]
    > De waarden voor de aanmeldings-URL zijn niet echt. URL-waarde voor aanmelding bijwerken met de werkelijke aanmeldings-URL. Neem contact op met het [ondersteunings team van Netskope User Authentication client](mailto:support@netskope.com) om de AANMELDINGS-URL-waarde op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Op de sectie **Netskope-gebruikers authenticatie instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam** `B.Simon` in.  
   1. Voer in het veld **gebruikers naam** de username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Netskope-gebruikers verificatie.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Netskope-gebruikers verificatie**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-netskope-user-authentication-sso"></a>SSO voor Netskope-gebruikers authenticatie configureren

1. Open een nieuw tabblad in uw browser en meld u aan bij de bedrijfs site van uw Netskope-gebruikers verificatie als beheerder.

1. Klik op het tabblad **actief platform** .

    ![Configuratie van Netskope-gebruikers verificatie](./media/netskope-user-authentication-tutorial/user1.png)

1. Schuif omlaag naar **Forward proxy** en selecteer **SAML**.

    ![Configuratie van Netskope-gebruikers verificatie](./media/netskope-user-authentication-tutorial/config-saml.png)

1. Voer de volgende stappen uit op de pagina **SAML-instellingen** :

    ![Configuratie van Netskope-gebruikers verificatie](./media/netskope-user-authentication-tutorial/configure-copyurls.png)

    a. Kopieer de waarde van de **SAML-entiteit-id** en plak deze in het tekstvak **id** in het gedeelte **basis configuratie van SAML** in de Azure Portal.

    b. Kopieer de waarde van de **SAML ACS-URL** en plak deze in het tekstvak **antwoord-URL** in het gedeelte basis- **SAML-configuratie** in de Azure Portal.

1. Klik op **account toevoegen**.

    ![Configuratie van Netskope-gebruikers verificatie](./media/netskope-user-authentication-tutorial/config-addaccount.png)

1. Voer de volgende stappen uit op de pagina **SAML-account toevoegen** :

    ![Configuratie van Netskope-gebruikers verificatie](./media/netskope-user-authentication-tutorial/config-settings1.png)

    a. Geef in het tekstvak **naam** de naam op zoals Azure AD.

    b. Plak in het tekstvak **IDP URL** de waarde voor de **aanmeldings-URL** , die u hebt gekopieerd uit de Azure Portal.

    c. Plak in het tekstvak **IDP entiteit-id** de waarde van de **Azure ad-id** , die u hebt gekopieerd uit de Azure Portal.

    d. Open het gedownloade meta gegevensbestand in Klad blok, kopieer de inhoud ervan naar het klem bord en plak het bestand vervolgens in het tekstvak **IDP certificaat** .

    e. Klik op **OPSLAAN**.

### <a name="create-netskope-user-authentication-test-user"></a>Test gebruiker voor Netskope-gebruikers verificatie maken

1. Open een nieuw tabblad in uw browser en meld u aan bij de bedrijfs site van uw Netskope-gebruikers verificatie als beheerder.

1. Klik op het tabblad **instellingen** in het navigatie deel venster aan de linkerkant.

    ![Gebruikers Netskope gebruikers authenticatie maken](./media/netskope-user-authentication-tutorial/config-settings.png)

1. Klik op het tabblad **actief platform** .

    ![Gebruikers Netskope gebruikers authenticatie maken](./media/netskope-user-authentication-tutorial/user1.png)

1. Klik op tabblad **gebruikers** .

    ![Gebruikers Netskope gebruikers authenticatie maken](./media/netskope-user-authentication-tutorial/add-user.png)

1. Klik op **gebruikers toevoegen**.

    ![Gebruikers Netskope gebruikers authenticatie maken](./media/netskope-user-authentication-tutorial/user-add.png)

1. Voer het e-mail adres in van de gebruiker die u wilt toevoegen en klik op **toevoegen**.

    ![Gebruikers Netskope gebruikers authenticatie maken](./media/netskope-user-authentication-tutorial/add-user-popup.png)

## <a name="test-sso"></a>SSO testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Netskope gebruikers verificatie in het toegangs venster klikt, moet u automatisch worden aangemeld bij de Netskope-gebruikers verificatie waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Netskope gebruikers verificatie uit met Azure AD](https://aad.portal.azure.com/)