---
title: 'Zelf studie: infor CloudSuite configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts op infor CloudSuite.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 3ea430bb-86a7-4bb4-8315-95434a660e88
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2019
ms.author: Zhchia
ms.openlocfilehash: 7b91b8418580717afaf8ddf176f934b3ff1d0c60
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77057538"
---
# <a name="tutorial-configure-infor-cloudsuite-for-automatic-user-provisioning"></a>Zelf studie: infor CloudSuite configureren voor automatische gebruikers inrichting

Het doel van deze zelf studie is om te demonstreren welke stappen moeten worden uitgevoerd in infor CloudSuite en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers en/of groepen op infor CloudSuite.

> [!NOTE]
> In deze zelf studie wordt een connector beschreven die boven op de Azure AD User Provisioning-Service is gebouwd. Zie [Gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md)voor belang rijke informatie over de werking van deze service, hoe deze werkt en veelgestelde vragen.
>
> Deze connector bevindt zich momenteel in de open bare preview. Zie [aanvullende gebruiksrecht overeenkomst voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)voor meer informatie over de algemene Microsoft Azure gebruiksrecht overeenkomst voor preview-functies.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-Tenant
* [Een infor CloudSuite-Tenant](https://www.infor.com/products/infor-os)
* Een gebruikers account in infor CloudSuite met beheerders machtigingen.

## <a name="assigning-users-to-infor-cloudsuite"></a>Gebruikers toewijzen aan infor CloudSuite

Azure Active Directory gebruikt een concept met de naam *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u automatische gebruikers inrichting configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in azure AD toegang nodig hebben tot infor CloudSuite. Nadat u hebt besloten, kunt u deze gebruikers en/of groepen toewijzen aan infor CloudSuite door de volgende instructies te volgen:
* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-infor-cloudsuite"></a>Belang rijke tips voor het toewijzen van gebruikers aan infor CloudSuite

* U wordt aangeraden één Azure AD-gebruiker toe te wijzen aan infor CloudSuite om de configuratie van automatische gebruikers inrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Wanneer u een gebruiker toewijst aan infor CloudSuite, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoog venster toewijzing. Gebruikers met de rol **standaard toegang** worden uitgesloten van het inrichten.

## <a name="set-up-infor-cloudsuite-for-provisioning"></a>Infor CloudSuite instellen voor inrichting

1. Meld u aan bij uw [infor CloudSuite-beheer console](https://www.infor.com/customer-center). Klik op het gebruikers pictogram en navigeer vervolgens naar **gebruikers beheer**.

    ![Infor CloudSuite-beheer console](media/infor-cloudsuite-provisioning-tutorial/admin.png)

2.  Klik op het menu pictogram in de linkerbovenhoek van het scherm. Klik op **beheren**.

    ![Infor CloudSuite SCIM toevoegen](media/infor-cloudsuite-provisioning-tutorial/manage.png)

3.  Navigeer naar **scim-accounts**.

    ![Infor CloudSuite SCIM-account](media/infor-cloudsuite-provisioning-tutorial/scim.png)

4.  Klik op het plus pictogram om een gebruiker met beheerders rechten toe te voegen. Geef een **scim-wacht woord** op en typ hetzelfde wacht woord onder **wacht woord bevestigen**. Klik op het mappictogram om het wacht woord op te slaan. Vervolgens ziet u een **gebruikers-id** die is gegenereerd voor de gebruiker met beheerders rechten.

    ![Infor CloudSuite admin-gebruiker](media/infor-cloudsuite-provisioning-tutorial/newuser.png)
    
    ![Infor CloudSuite-wacht woord](media/infor-cloudsuite-provisioning-tutorial/password.png)

    ![Infor CloudSuite-id](media/infor-cloudsuite-provisioning-tutorial/identifier.png)

5. Als u een Bearer-token wilt genereren, kopieert u het **wacht woord**voor de **gebruikers-id** en het scim. Plak ze in Klad blok en vervolgens gescheiden door een dubbele punt. Codeer de teken reeks waarde door te navigeren naar **Plugins > MIME-Hulpprogram ma's > Basic64-code ring**. 

    ![Infor CloudSuite-id](media/infor-cloudsuite-provisioning-tutorial/token.png)

3.  Kopieer het Bearer-token. Deze waarde wordt ingevoerd in het veld geheime token op het tabblad inrichten van uw infor CloudSuite-toepassing in de Azure Portal.

## <a name="add-infor-cloudsuite-from-the-gallery"></a>Infor CloudSuite toevoegen vanuit de galerie

Voordat u infor CloudSuite configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u infor CloudSuite van de Azure AD-toepassings galerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om infor CloudSuite toe te voegen vanuit de Azure AD-toepassings galerie:**

1. Selecteer in de **[Azure Portal](https://portal.azure.com)** in het navigatie venster links **Azure Active Directory**.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **bedrijfs toepassingen**en selecteer **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **nieuwe toepassing** boven aan het deel venster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer in het zoekvak **infor CloudSuite**in, selecteer **infor CloudSuite** in het deel venster resultaten en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

    ![Infor CloudSuite in de lijst met resultaten](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-infor-cloudsuite"></a>Automatische gebruikers inrichting configureren voor infor CloudSuite 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en/of groepen in infor CloudSuite te maken, bij te werken en uit te scha kelen op basis van gebruikers-en/of groeps toewijzingen in azure AD.

> [!TIP]
> U kunt er ook voor kiezen om eenmalige aanmelding op basis van SAML in te scha kelen voor infor CloudSuite, gevolgd door de instructies in de [zelf studie infor CloudSuite eenmalige aanmelding](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial). Eenmalige aanmelding kan onafhankelijk van automatische gebruikers inrichting worden geconfigureerd, hoewel deze twee functies elkaar behoeven.

> [!NOTE]
> Raadpleeg voor meer informatie [over het scim](https://docs.infor.com/mingle/12.0.x/en-us/minceolh/jho1449382121585.html#)-eind punt van infor CloudSuite.

### <a name="to-configure-automatic-user-provisioning-for-infor-cloudsuite-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor infor CloudSuite in azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **bedrijfs toepassingen**en selecteer **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **infor CloudSuite**.

    ![De koppeling infor CloudSuite in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **inrichten** .

    ![Tabblad inrichten](common/provisioning.png)

4. Stel de **inrichtings modus** in op **automatisch**.

    ![Tabblad inrichten](common/provisioning-automatic.png)

5. Selecteer in de sectie **beheerders referenties** de invoer `https://mingle-t20b-scim.mingle.awsdev.infor.com/INFORSTS_TST/v2/scim` in de **Tenant-URL**. Invoer van de Bearer-token waarde die eerder is opgehaald in het **geheime token**. Klik op **verbinding testen** om te controleren of Azure AD verbinding kan maken met infor CloudSuite. Als de verbinding mislukt, zorg er dan voor dat uw infor CloudSuite-account beheerders machtigingen heeft en probeer het opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **e-mail melding** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen en schakel het selectie vakje in om **een e-mail bericht te verzenden wanneer er een fout optreedt**.

    ![E-mail melding](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met infor CloudSuite**.

    ![Infor CloudSuite-gebruikers toewijzingen](media/infor-cloudsuite-provisioning-tutorial/usermappings.png)

9. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar infor CloudSuite in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in infor CloudSuite voor update bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Infor CloudSuite-gebruikers kenmerken](media/infor-cloudsuite-provisioning-tutorial/userattributes.png)

10. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory groepen synchroniseren met infor CloudSuite**.

    ![Infor CloudSuite-groeps toewijzingen](media/infor-cloudsuite-provisioning-tutorial/groupmappings.png)

11. Controleer de groeps kenmerken die zijn gesynchroniseerd vanuit Azure AD naar infor CloudSuite in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de groepen in infor CloudSuite voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Infor CloudSuite-groeps kenmerken](media/infor-cloudsuite-provisioning-tutorial/groupattributes.png)

12. Raadpleeg de volgende instructies in de [zelf studie](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)voor het filteren op bereik voor het configureren van bereik filters.

13. Als u de Azure AD-inrichtings service voor infor CloudSuite wilt inschakelen, **wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtings status inschakelt op](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u wilt inrichten voor infor CloudSuite door de gewenste waarden in het **bereik** te kiezen in de sectie **instellingen** .

    ![Inrichtings bereik](common/provisioning-scope.png)

15. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtings configuratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die in het **bereik** zijn gedefinieerd in de sectie **instellingen** . Het duurt langer voordat de initiële synchronisatie is uitgevoerd dan volgende synchronisaties, die ongeveer elke 40 minuten optreden, zolang de Azure AD-inrichtings service wordt uitgevoerd. U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen naar het rapport inrichtings activiteiten te volgen. Hiermee worden alle acties beschreven die worden uitgevoerd door de Azure AD Provisioning-Service op infor CloudSuite.

Zie [rapportage over het automatisch inrichten van gebruikers accounts](../app-provisioning/check-status-user-account-provisioning.md)voor meer informatie over het lezen van de Azure AD-inrichtings Logboeken.

## <a name="additional-resources"></a>Aanvullende resources

* [Inrichten van gebruikers accounts voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../app-provisioning/check-status-user-account-provisioning.md)