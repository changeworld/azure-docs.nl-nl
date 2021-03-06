---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met Azure AD SAML Toolkit | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Azure AD SAML Toolkit.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3f4348e7-c34e-43c7-926e-f1b26ffacf6d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7902112c1694bacfeb45b5f20db80d5136642169
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/06/2020
ms.locfileid: "77047955"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-azure-ad-saml-toolkit"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met Azure AD SAML Toolkit

In deze zelf studie leert u hoe u Azure AD SAML Toolkit integreert met Azure Active Directory (Azure AD). Wanneer u Azure AD SAML Toolkit integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot de Azure AD SAML Toolkit.
* Uw gebruikers in staat stellen om automatisch te worden aangemeld bij Azure AD SAML Toolkit met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Azure AD SAML Toolkit-abonnement voor eenmalige aanmelding (SSO) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Azure AD SAML Toolkit ondersteunt door **SP** GEÏNITIEERDe SSO
* Zodra u Azure AD SAML Toolkit hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessie beheer is uitgebreid met voorwaardelijke toegang. [Meer informatie over het afdwingen van sessie beheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-azure-ad-saml-toolkit-from-the-gallery"></a>Azure AD SAML Toolkit toevoegen vanuit de galerie

Als u de integratie van Azure AD SAML Toolkit wilt configureren in azure AD, moet u Azure AD SAML Toolkit vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. In de sectie **toevoegen vanuit de galerie** typt u **Azure AD SAML Toolkit** in het zoekvak.
1. Selecteer **Azure AD SAML Toolkit** in het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-azure-ad-saml-toolkit"></a>Eenmalige aanmelding voor Azure AD SAML Toolkit configureren en testen

Azure AD SSO configureren en testen met Azure AD SAML Toolkit met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in azure AD SAML Toolkit.

Als u Azure AD SSO wilt configureren en testen met Azure AD SAML Toolkit, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Azure AD SAML Toolkit SSO configureren](#configure-azure-ad-saml-toolkit-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. Een **[test gebruiker voor Azure AD SAML Toolkit maken](#create-azure-ad-saml-toolkit-test-user)** : u hebt een equivalent van B. Simon in azure AD SAML Toolkit dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in de [Azure Portal](https://portal.azure.com/)op de pagina **Azure AD SAML Toolkit** Application Integration de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer op de pagina **basis configuratie van SAML** de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL: `https://samltoolkit.azurewebsites.net/`

    b. Typ een URL in het vak **Id (Entiteits-id)** : `https://samltoolkit.azurewebsites.net`

    c. In het tekstvak **Antwoord-URL** typt u een URL: `https://samltoolkit.azurewebsites.net/SAML/Consume`

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , **certificaat (RAW)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificateraw.png)

1. Op de sectie **Azure AD SAML Toolkit instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **gebruikers naam** de username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Azure AD SAML Toolkit.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Azure AD SAML Toolkit**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-azure-ad-saml-toolkit-sso"></a>Azure AD SAML Toolkit SSO configureren

1. Open een nieuw webbrowser venster, als u zich nog niet hebt geregistreerd op de Azure AD SAML Toolkit-website, moet u eerst registreren door te klikken op het **REGI ster**. Als u zich al hebt geregistreerd, meldt u zich aan bij uw Azure AD SAML Toolkit-bedrijfs site met de geregistreerde aanmeldings referenties.

    ![Azure AD SAML Toolkit registreren](./media/saml-toolkit-tutorial/register.png)

1. Klik op de **SAML-configuratie**.

    ![SAML-configuratie voor Azure AD SAML Toolkit](./media/saml-toolkit-tutorial/saml-configure.png)

1. Klik op **Create**.

    ![Azure AD SAML Toolkit SSO maken](./media/saml-toolkit-tutorial/createsso.png)

1. Voer de volgende stappen uit op de pagina **SAML SSO-configuratie** :

    ![Azure AD SAML Toolkit SSO maken](./media/saml-toolkit-tutorial/fill-details.png)

    1. Plak in het tekstvak **aanmeldings-URL** de waarde voor de **aanmeldings-URL** , die u hebt gekopieerd uit de Azure Portal.

    1. Plak in het tekstvak **Azure ad-id** de **Azure ad-id** -waarde die u van de Azure Portal hebt gekopieerd.

    1. Plak in het tekstvak voor de **afmeldings-URL** de waarde van de **afmeldings-URL** die u uit Azure Portal hebt gekopieerd.

    1. Klik op **bestand kiezen** en upload het **certificaat bestand (RAW)** dat u hebt gedownload van de Azure Portal.

    1. Klik op **Create**.

### <a name="create-azure-ad-saml-toolkit-test-user"></a>Test gebruiker voor Azure AD SAML Toolkit maken

In deze sectie wordt een gebruiker met de naam B. Simon gemaakt in azure AD SAML Toolkit. Azure AD SAML Toolkit ondersteunt just-in-time-gebruikers inrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in azure AD SAML Toolkit, wordt er na verificatie een nieuwe gemaakt.

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Azure AD SAML Toolkit klikt in het toegangs venster, moet u automatisch worden aangemeld bij de Azure AD SAML Toolkit waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Azure AD SAML Toolkit uit met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessie beheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Azure AD SAML Toolkit beveiligen met geavanceerde zicht baarheid en besturings elementen](https://docs.microsoft.com/cloud-app-security/protect-azure)