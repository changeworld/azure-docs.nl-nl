---
title: 'Zelfstudie: Azure Active Directory-integratie met Proxyclick | Microsoft Docs'
description: In deze zelfstudie leert u hoe het configureren van eenmalige aanmelding tussen Azure Active Directory en Proxyclick.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5c58a859-71c2-4542-ae92-e5f16a8e7f18
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 6a4d1c8a390ebd1194d14c057bb32d3111bf39be
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67093499"
---
# <a name="tutorial-azure-active-directory-integration-with-proxyclick"></a>Zelfstudie: Azure Active Directory-integratie met Proxyclick

In deze zelfstudie leert u hoe u Proxyclick integreert met Azure Active Directory (Azure AD).
Deze integratie biedt de volgende voordelen:

* U kunt Azure AD om te bepalen wie toegang tot Proxyclick heeft gebruiken.
* U kunt uw gebruikers kunnen automatisch worden aangemeld bij Proxyclick (eenmalige aanmelding) met hun Azure AD-accounts inschakelen.
* U kunt uw accounts in één centrale locatie kunt beheren: de Azure-portal.

Zie [Eenmalige aanmelding voor toepassingen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) voor meer informatie over de integratie van SaaS-apps met Azure AD.

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met Proxyclick, moet u beschikken over:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u zich aanmelden voor een [proefversie van één maand](https://azure.microsoft.com/pricing/free-trial/).
* Een Proxyclick-abonnement met eenmalige aanmelding ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en Azure AD eenmalige aanmelding testen in een testomgeving.

* Proxyclick ondersteunt SP geïnitieerde en IdP gestart door SSO.

## <a name="add-proxyclick-from-the-gallery"></a>Proxyclick uit de galerie toevoegen

Als u de integratie van Proxyclick in Azure AD instelt, moet u Proxyclick uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. In de [Azure-portal](https://portal.azure.com), selecteer in het linkerdeelvenster **Azure Active Directory**:

    ![Selecteer Azure Active Directory](common/select-azuread.png)

2. Ga naar **bedrijfstoepassingen** > **alle toepassingen**:

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u wilt een toepassing hebt toegevoegd, selecteert u **nieuwe toepassing** aan de bovenkant van het venster:

    ![Nieuwe toepassing selecteren](common/add-new-app.png)

4. Voer in het zoekvak **Proxyclick**. Selecteer **Proxyclick** in de zoekresultaten en selecteer vervolgens **toevoegen**.

     ![Zoekresultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie maakt u configureren en testen van Azure AD eenmalige aanmelding met Proxyclick met behulp van een testgebruiker met de naam Britta Simon.
Om in te schakelen eenmalige aanmelding, moet u een relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Proxyclick vast te stellen.

Als u wilt configureren en Azure AD eenmalige aanmelding met Proxyclick testen, moet u deze stappen:

1. **[Azure AD eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**  om in te schakelen van de functie voor uw gebruikers.
2. **[Configureren van eenmalige aanmelding Proxyclick](#configure-proxyclick-single-sign-on)**  aan de toepassing.
3. **[Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user)**  voor het testen van Azure AD eenmalige aanmelding.
4. **[Toewijzen van de Azure AD-testgebruiker](#assign-the-azure-ad-test-user)**  zodat Azure AD eenmalige aanmelding voor de gebruiker.
5. **[Maak een testgebruiker Proxyclick](#create-a-proxyclick-test-user)**  dat gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**  om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie schakelt u Azure AD eenmalige aanmelding in de Azure-portal.

Voor het configureren van Azure AD eenmalige aanmelding met Proxyclick, de volgende stappen uitvoeren:

1. In de [Azure-portal](https://portal.azure.com/), selecteert u op de pagina Proxyclick toepassingen integratie **eenmalige aanmelding**:

    ![Schakel eenmalige aanmelding](common/select-sso.png)

2. In de **selecteert u een methode voor eenmalige aanmelding** in het dialoogvenster, selecteer **SAML/WS-Federation** modus voor eenmalige aanmelding inschakelen:

    ![Selecteer een methode voor eenmalige aanmelding](common/select-saml-option.png)

3. Op de **instellen van eenmalige aanmelding met SAML** weergeeft, schakelt de **bewerken** pictogram opent de **SAML-basisconfiguratie** in het dialoogvenster:

    ![Pictogram bewerken](common/edit-urls.png)

4. In de **SAML-basisconfiguratie** in het dialoogvenster, als u wilt configureren van de toepassing in de modus voor IdP gestart door de volgende stappen uitvoeren.

    ![In het dialoogvenster van Basic SAML-configuratie](common/idp-intiated.png)

    1. In de **id** vak, een URL opgeven in dit patroon:
   
       `https://saml.proxyclick.com/init/<companyId>`

    1. In de **antwoord-URL** vak, een URL opgeven in dit patroon:

       `https://saml.proxyclick.com/consume/<companyId>`

5. Als u configureren van de toepassing in de modus SP geïnitieerde wilt, selecteert u **extra URL's instellen**. In de **aanmeldings-URL** vak, een URL opgeven in dit patroon:
   
   `https://saml.proxyclick.com/init/<companyId>`

    ![Proxyclick domein en URL's, eenmalige aanmelding informatie](common/metadata-upload-additional-signon.png)

    

    > [!NOTE]
    > Deze waarden zijn tijdelijke aanduidingen. U moet gebruiken de werkelijke-id, antwoord-URL en aanmeldings-URL. Stappen voor het ophalen van deze waarden worden later in deze zelfstudie beschreven.

6. Op de **instellen van eenmalige aanmelding met SAML** pagina, in de **SAML-handtekeningcertificaat** sectie, selecteer de **downloaden** koppelen naast **certificaat (Base64)** , overeenkomstig uw vereisten en sla het certificaat op uw computer:

    ![De koppeling om het certificaat te downloaden](common/certificatebase64.png)

7. In de **Proxyclick instellen** sectie, kopieert u de juiste URL's, op basis van uw vereisten:

    ![De configuratie van URL's kopiëren](common/copy-configuration-urls.png)

    1. **Aanmeldings-URL**.

    1. **Azure AD Identifier**.

    1. **Afmeldings-URL van**.

### <a name="configure-proxyclick-single-sign-on"></a>Proxyclick eenmalige aanmelding configureren

1. In een nieuw browservenster aanmelden bij uw bedrijf Proxyclick site als een beheerder.

2. Selecteer **& Accountinstellingen**:

    ![Selecteer het Account en -instellingen](./media/proxyclick-tutorial/configure1.png)

3. Schuif omlaag naar de **integraties** sectie en selecteer **SAML**:

    ![Selecteer SAML](./media/proxyclick-tutorial/configure2.png)

4. In de **SAML** sectie, de volgende stappen uitvoeren.

    ![Sectie voor SAML](./media/proxyclick-tutorial/configure3.png)

    1. Kopiëren de **URL voor SAML-consument** waarde en plak deze in de **antwoord-URL** vak de **SAML-basisconfiguratie** in het dialoogvenster in de Azure-portal.

    1. Kopiëren de **Omleidings-URL voor SAML SSO-** waarde en plak deze in de **aanmeldings-URL** en **id** dialoogvensters in de **SAML-basisconfiguratie** in het dialoogvenster in Azure portal.

    1. In de **SAML-aanvraag methode** in de lijst met **HTTP-omleiding**.

    1. In de **verlener** vak, plak de **Azure AD-id** waarde die u hebt gekopieerd uit de Azure-portal.

    1. In de **eindpunt-URL voor SAML 2.0** vak, plak de **aanmeldings-URL** waarde die u hebt gekopieerd uit de Azure-portal.

    1. Open in Kladblok, het certificaatbestand dat u hebt gedownload van de Azure-portal. Plak de inhoud van dit bestand in de **certificaat** vak.

    1. Selecteer **Save changes**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker Britta Simon met de naam in Azure portal.

1. Selecteer in de Azure portal, **Azure Active Directory** selecteren in het linkerdeelvenster **gebruikers**, en selecteer vervolgens **alle gebruikers**:

    ![Selecteer alle gebruikers](common/users.png)

2. Selecteer **nieuwe gebruiker** aan de bovenkant van het scherm:

    ![Nieuwe gebruiker selecteren](common/new-user.png)

3. In de **gebruiker** dialoogvenster vak, voer de volgende stappen uit.

    ![In het dialoogvenster](common/user-properties.png)

    1. Voer in het vak **Naam** **Britta Simon**in.
  
    1. In de **gebruikersnaam** Voer **BrittaSimon @\<uwbedrijfsdomein >.\< extensie >** . (Bijvoorbeeld BrittaSimon@contoso.com.)

    1. Selecteer **wachtwoord weergeven**, en noteer de waarde in de **wachtwoord** vak.

    1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Britta Simon Azure eenmalige aanmelding door haar toegang verlenen tot Proxyclick gebruiken.

1. Selecteer in de Azure portal, **bedrijfstoepassingen**, selecteer **alle toepassingen**, en selecteer vervolgens **Proxyclick**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst met toepassingen **Proxyclick**.

    ![Lijst met toepassingen](common/all-applications.png)

3. Selecteer in het linkerdeelvenster **gebruikers en groepen**:

    ![Gebruikers en groepen selecteren](common/users-groups-blade.png)

4. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Gebruiker toevoegen selecteren](common/add-assign-user.png)

5. In de **gebruikers en groepen** in het dialoogvenster, selecteer **Britta Simon** in de lijst met gebruikers, en klik op de **Selecteer** knop aan de onderkant van het venster.

6. Als u een waarde voor de rol in het SAML-verklaring verwacht in de **rol selecteren** dialoogvenster Selecteer de juiste rol voor de gebruiker in de lijst. Klik op de **Selecteer** knop aan de onderkant van het venster.

7. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

### <a name="create-a-proxyclick-test-user"></a>Maak een testgebruiker Proxyclick

Als u wilt dat Azure AD-gebruikers kunnen zich aanmelden bij Proxyclick, moet u toe te voegen aan Proxyclick. U moet deze handmatig toevoegen.

Voor het maken van een gebruikersaccount, de volgende stappen uitvoeren:

1. Aanmelden bij uw bedrijf Proxyclick site als een beheerder.

1. Selecteer **collega's** aan de bovenkant van het venster:

    ![Collega's selecteren](./media/proxyclick-tutorial/user1.png)

1. Selecteer **collega toevoegen**:

    ![Selecteer toevoegen collega](./media/proxyclick-tutorial/user2.png)

1. In de **toevoegen van een collega** sectie, de volgende stappen uitvoeren.

    ![Een collega sectie toevoegen](./media/proxyclick-tutorial/user3.png)

    1. In de **e** voert u het e-mailadres van de gebruiker. In dit geval **brittasimon\@contoso.com**.

    1. In de **voornaam** voert u de voornaam van de gebruiker. In dit geval **Julia**.

    1. In de **achternaam** voert u de achternaam van de gebruiker. In dit geval **Simon**.

    1. Selecteer **Add User**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

Nu moet u uw configuratie Azure AD eenmalige aanmelding testen met behulp van het toegangsvenster.

Wanneer u de tegel Proxyclick in het toegangsvenster selecteert, moet u worden automatisch aangemeld bij de Proxyclick-exemplaar waarvoor u eenmalige aanmelding hebt ingesteld. Zie voor meer informatie over het toegangsvenster, [toegang en gebruik apps op de portal mijn Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Aanvullende resources

- [Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

