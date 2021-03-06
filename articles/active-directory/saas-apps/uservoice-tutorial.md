---
title: 'Zelf studie: integratie Azure Active Directory met UserVoice | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en UserVoice.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 684a405b-8932-46f6-b43a-4d97a42b6b87
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: 7a3302f1ca615fe5005be9ed1f09995ebf432eb7
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232008"
---
# <a name="tutorial-azure-active-directory-integration-with-uservoice"></a>Zelf studie: integratie met UserVoice Azure Active Directory

In deze zelf studie leert u hoe u UserVoice integreert met Azure Active Directory (Azure AD).
Het integreren van UserVoice met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot UserVoice.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij UserVoice (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met UserVoice wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) aanvragen
* Abonnement voor eenmalige aanmelding bij UserVoice ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* UserVoice ondersteunt door **SP** GEÏNITIEERDe SSO

## <a name="adding-uservoice-from-the-gallery"></a>UserVoice toevoegen vanuit de galerie

Als u de integratie van UserVoice in azure AD wilt configureren, moet u UserVoice vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u UserVoice wilt toevoegen vanuit de galerie, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **UserVoice**in het zoekvak, selecteer **UserVoice** in resultaat paneel en klik vervolgens op knop **toevoegen** om de toepassing toe te voegen.

     ![UserVoice in de resultaten lijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD met UserVoice configureren en testen op basis van een test gebruiker met de naam **Julia Simon**.
Voor een goede werking van eenmalige aanmelding moet een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in UserVoice tot stand worden gebracht.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met UserVoice, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige eenmalige aanmelding voor UserVoice configureren](#configure-uservoice-single-sign-on)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak een UserVoice-test gebruiker](#create-uservoice-test-user)** -om een equivalent van Julia Simon in UserVoice te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met UserVoice:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **UserVoice** -toepassings integratie de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over eenmalige aanmelding voor het UserVoice-domein en Url's](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<tenantname>.UserVoice.com`

    b. In het tekstvak **Id (entiteits-id)** typt u een URL met het volgende patroon: `https://<tenantname>.UserVoice.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [UserVoice-client ondersteunings team](https://www.uservoice.com/) om deze waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Klik in de sectie **SAML-handtekeningcertificaat** op de knop **Bewerken** om het dialoogvenster **SAML-handtekeningcertificaat** te openen.

    ![SAML-handtekeningcertificaat bewerken](common/edit-certificate.png)

6. Kopieer in de sectie **SAML-handtekeningcertificaat** de waarde voor **VINGERAFDRUK** en sla deze op de computer op.

    ![Waarde van vingerafdruk kopiëren](common/copy-thumbprint.png)

7. Kopieer op de sectie **UserVoice instellen** de gewenste URL ('s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-uservoice-single-sign-on"></a>Eenmalige eenmalige aanmelding voor UserVoice configureren

1. Meld u in een ander webbrowser venster als beheerder aan bij uw UserVoice-bedrijfs site.

2. Klik in de werk balk bovenaan op **instellingen**en selecteer vervolgens **webportal** in het menu.
   
    ![De sectie instellingen aan de kant van de app](./media/uservoice-tutorial/ic777519.png "Instellingen")

3. Klik op het tabblad **webportal** , in de sectie **gebruikers verificatie** , op **bewerken** om de dialoog venster **gebruikers verificatie bewerken** te openen.
   
    ![Tabblad webportal](./media/uservoice-tutorial/ic777520.png "Webportal")

4. Voer op de pagina dialoog venster **gebruikers verificatie bewerken** de volgende stappen uit:
   
    ![Gebruikers verificatie bewerken](./media/uservoice-tutorial/ic777521.png "Gebruikers verificatie bewerken")
   
    a. Klik op **eenmalige aanmelding (SSO)** .
 
    b. Plak de waarde van de **aanmeldings-URL** , die u hebt gekopieerd van de Azure Portal naar het tekstvak **SSO Remote Sign-in** .

    c. Plak de waarde van de **Afmeldings-URL** , die u hebt gekopieerd van de Azure Portal naar het **tekstvak SSO externe afmelding**.
 
    d. Plak de waarde van de **vinger afdruk** die u hebt gekopieerd van Azure Portal naar het tekstvak **SHA1-vinger afdruk van het huidige certificaat** .
    
    e. Klik op **verificatie-instellingen opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon**in.
  
    b. Typ brittasimon@yourcompanydomain.extensionin het veld **gebruikers naam** . Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Julia Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan UserVoice.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **UserVoice**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **UserVoice**.

    ![De UserVoice-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer **Gebruikers en groepen** in het menu aan de linkerkant.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-uservoice-test-user"></a>UserVoice-test gebruiker maken

Om ervoor te zorgen dat Azure AD-gebruikers zich kunnen aanmelden bij UserVoice, moeten ze worden ingericht in UserVoice. In het geval van UserVoice is inrichting een hand matige taak.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:

1. Meld u aan bij uw **UserVoice** -Tenant.

2. Ga naar **Settings**.
   
    ![Instellingen](./media/uservoice-tutorial/ic777811.png "Instellingen")

3. Klik op **Algemeen**.

4. Klik op **agents en machtigingen**.
   
    ![Agents en machtigingen](./media/uservoice-tutorial/ic777812.png "Agents en machtigingen")

5. Klik op **beheerders toevoegen**.
   
    ![Beheerders toevoegen](./media/uservoice-tutorial/ic777813.png "Beheerders toevoegen")

6. Voer de volgende stappen uit in het dialoog venster **beheerders uitnodigen** :
   
    ![Beheerders uitnodigen](./media/uservoice-tutorial/ic777814.png "Beheerders uitnodigen")
   
    a. Typ in het tekstvak E-mails het e-mail adres van het account dat u wilt inrichten en klik vervolgens op **toevoegen**.
   
    b. Klik op **Uitnodigen**.

> [!NOTE]
> U kunt alle andere hulpprogram ma's voor het maken van gebruikers accounts of Api's van UserVoice gebruiken om Azure AD-gebruikers accounts in te richten.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de UserVoice-tegel in het toegangs venster klikt, moet u automatisch worden aangemeld bij de UserVoice waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

