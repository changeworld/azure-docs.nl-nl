---
title: 'Zelf studie: integratie Azure Active Directory met Jive | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Jive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9fc5659a-c116-4a1b-a601-333325a26b46
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ccdab373be4bab876ef52ba478076b6a8b6e0845
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2020
ms.locfileid: "76291172"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-jive"></a>Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met Jive

In deze zelf studie leert u hoe u Jive integreert met Azure Active Directory (Azure AD). Wanneer u Jive integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Jive.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij Jive met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Jive-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Jive ondersteunt door **SP** GEÏNITIEERDe SSO
* Jive ondersteunt [ **geautomatiseerde** gebruikers inrichting](jive-provisioning-tutorial.md)
* Zodra u de Jive hebt geconfigureerd, kunt u sessie besturings elementen afdwingen, waardoor de gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessie besturings elementen worden uitgebreid vanuit voorwaardelijke toegang. [Meer informatie over het afdwingen van sessie beheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-jive-from-the-gallery"></a>Jive toevoegen uit de galerie

Als u de integratie van Jive in azure AD wilt configureren, moet u Jive uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Jive** in het zoekvak.
1. Selecteer **Jive** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.


## <a name="configure-and-test-azure-ad-single-sign-on-for-jive"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor Jive

Azure AD SSO met Jive configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Jive.

Als u Azure AD SSO wilt configureren en testen met Jive, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    * **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    * **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Jive SSO configureren](#configure-jive-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    * **[Maak een Jive-test gebruiker](#create-jive-test-user)** -om een equivalent van B. Simon in Jive te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **Jive** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **basis configuratie van SAML** de waarden in voor de volgende velden:

a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<instance name>.jivecustom.com`

    b. In the **Identifier (Entity ID)** text box, type a URL using the following pattern:
    `https://<instance name>.jiveon.com`

    > [!NOTE]
    > These values are not real. Update these values with the actual Sign on URL and Identifier. Contact [Jive Client support team](https://www.jivesoftware.com/services-support/) to get these values. You can also refer to the patterns shown in the **Basic SAML Configuration** section in the Azure portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer op de sectie **Jive instellen** de gewenste URL ('s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Jive.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Jive**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-jive-sso"></a>Jive SSO configureren

1. Als u eenmalige aanmelding wilt configureren voor **Jive** , meldt u zich aan bij uw Jive-Tenant als beheerder.

1. Klik in het menu aan de bovenkant op **SAML**.

    ![Eenmalige aanmelding aan app-zijde configureren](./media/jive-tutorial/tutorial_jive_002.png)

    a. Selecteer **ingeschakeld** op het tabblad **Algemeen** .

    b. Klik op de knop **alle SAML-instellingen opslaan** .

1. Ga naar het tabblad **IDP-meta gegevens** .

    ![Eenmalige aanmelding aan app-zijde configureren](./media/jive-tutorial/tutorial_jive_003.png)

    a. Kopieer de inhoud van het gedownloade XML-bestand met meta gegevens en plak het in het tekstvak van de **ID-provider (IDP)** .

    b. Klik op de knop **alle SAML-instellingen opslaan** .

1. Selecteer het tabblad **toewijzing van gebruikers kenmerk** .

    ![Eenmalige aanmelding aan app-zijde configureren](./media/jive-tutorial/tutorial_jive_004.png)

    a. Kopieer en plak de kenmerk naam van de **e-mail** waarde in het tekstvak **e-mail** .

    b. In het tekstvak **voor naam** kopieert en plakt u de naam van het kenmerk van de waarde van de **OpgegevenNaam** .

    c. Kopieer en plak de kenmerk naam van **de waarde in** het tekstvak **Achternaam** .

### <a name="create-jive-test-user"></a>Jive-test gebruiker maken

Het doel van deze sectie is het maken van een gebruiker met de naam Julia Simon in Jive. Jive ondersteunt het automatisch inrichten van gebruikers, dat standaard is ingeschakeld. U kunt [hier](jive-provisioning-tutorial.md) meer informatie vinden over het configureren van het automatisch inrichten van gebruikers.

Als u hand matig een gebruiker moet maken, moet u samen werken met het [Jive-client ondersteunings team](https://www.jivesoftware.com/services-support/) om de gebruikers toe te voegen aan het Jive-platform.

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Jive in het toegangs venster klikt, moet u automatisch worden aangemeld bij de Jive waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Jive met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessie beheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Inrichten van gebruikers configureren](jive-provisioning-tutorial.md)

- [Jive beveiligen met geavanceerde zicht baarheid en controles](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
