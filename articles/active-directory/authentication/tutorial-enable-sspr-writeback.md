---
title: Azure Active Directory wacht woord terugschrijven inschakelen
description: In deze zelf studie leert u hoe u terugschrijven van Azure AD selfservice voor wacht woord opnieuw instellen kunt inschakelen met Azure AD Connect om wijzigingen terug te synchroniseren naar een on-premises Active Directory Domain Services omgeving.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 02/18/2020
ms.author: iainfou
author: iainfoulds
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: ccc64fb8dd8bd8abc198d9bfc9d643ef618188ea
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2020
ms.locfileid: "78967790"
---
# <a name="tutorial-enable-azure-active-directory-self-service-password-reset-writeback-to-an-on-premises-environment"></a>Zelf studie: terugschrijven naar een on-premises omgeving inschakelen Azure Active Directory self-service voor wacht woord opnieuw instellen

Met Azure Active Directory (Azure AD) self-service voor wachtwoord herstel (SSPR) kunnen gebruikers hun wacht woord bijwerken of hun account ontgrendelen met een webbrowser. In een hybride omgeving waar Azure AD is verbonden met een on-premises Active Directory Domain Services-omgeving (AD DS), kan dit scenario ertoe leiden dat wacht woorden verschillen tussen de twee directory's.

Wacht woord terugschrijven kan worden gebruikt om wachtwoord wijzigingen in azure AD terug te synchroniseren naar uw on-premises AD DS omgeving. Azure AD Connect biedt een beveiligd mechanisme om deze wachtwoord wijzigingen terug te sturen naar een bestaande on-premises adres lijst vanuit Azure AD.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De vereiste machtigingen voor het terugschrijven van wacht woorden configureren
> * Schakel de optie voor het terugschrijven van wacht woorden in Azure AD Connect
> * Wacht woord terugschrijven inschakelen in azure AD SSPR

## <a name="prerequisites"></a>Vereisten

U hebt de volgende resources en bevoegdheden nodig om deze zelf studie te volt ooien:

* Een werkende Azure AD-tenant waarop minimaal een proeflicentie is ingeschakeld.
    * Maak indien nodig [een gratis versie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
    * Zie [licentie vereisten voor Azure AD SSPR](concept-sspr-licensing.md)voor meer informatie.
* Een account met *globale beheerders* bevoegdheden.
* Azure AD is geconfigureerd voor Self-service voor het opnieuw instellen van wacht woorden.
    * Voer, indien nodig, [de vorige zelf studie uit om Azure AD SSPR in te scha kelen](tutorial-enable-sspr.md).
* Een bestaande on-premises AD DS omgeving die is geconfigureerd met de huidige versie van Azure AD Connect.
    * Configureer zo nodig Azure AD Connect met behulp van de [snelle](../hybrid/how-to-connect-install-express.md) of [aangepaste](../hybrid/how-to-connect-install-custom.md) instellingen.
    * Als u wacht woord terugschrijven wilt gebruiken, moeten uw domein controllers Windows Server 2008 R2 of later zijn.

## <a name="configure-account-permissions-for-azure-ad-connect"></a>Account machtigingen voor Azure AD Connect configureren

Met Azure AD Connect kunt u gebruikers, groepen en referenties synchroniseren tussen een on-premises AD DS omgeving en Azure AD. Doorgaans installeert u Azure AD Connect op een computer met Windows Server 2012 of hoger die is gekoppeld aan het on-premises AD DS domein.

Als u wilt werken met SSPR write-back, moeten de juiste machtigingen en opties zijn ingesteld voor het account dat is opgegeven in Azure AD Connect. Als u niet zeker weet welk account momenteel in gebruik is, opent u Azure AD Connect en selecteert u de optie **huidige configuratie weer geven** . Het account waaraan u machtigingen moet toevoegen, wordt vermeld onder **gesynchroniseerde mappen**. De volgende machtigingen en opties moeten worden ingesteld op het account:

* **Wachtwoord opnieuw instellen**
* **Wachtwoord wijzigen**
* **Schrijf machtigingen** voor `lockoutTime`
* **Schrijf machtigingen** voor `pwdLastSet`
* **Uitgebreide rechten** op:
   * Het hoofd object van *elk domein* in het forest
   * De organisatie-eenheden (Ou's) van de gebruiker die u in het bereik van SSPR wilt hebben

Als u deze machtigingen niet toewijst, lijkt write-back correct te zijn geconfigureerd, maar kunnen gebruikers fouten tegen komen wanneer ze hun on-premises wacht woorden vanuit de Cloud beheren.

Voer de volgende stappen uit om de juiste machtigingen voor het terugschrijven van wacht woorden in te stellen:

1. Open in uw on-premises AD DS omgeving **Active Directory gebruikers en computers** met een account met de juiste machtigingen voor *domein beheerders* .
1. Controleer of in het menu **beeld** de optie **geavanceerde functies** is ingeschakeld.
1. Klik in het linkerdeel venster met de rechter muisknop op het object dat staat voor de hoofdmap van het domein en selecteer **eigenschappen** > **beveiliging** > **Geavanceerd**.
1. Selecteer **toevoegen**op het tabblad **machtigingen** .
1. Selecteer voor **Principal**het account waarmee machtigingen moeten worden toegepast (het account dat wordt gebruikt door Azure AD Connect).
1. Selecteer in de vervolg keuzelijst **van toepassing op** de optie **onderliggende gebruikers objecten**.
1. Schakel onder *machtigingen*de selectie vakjes in voor de volgende opties:
    * **Wachtwoord wijzigen**
    * **Wachtwoord opnieuw instellen**
1. Schakel onder *Eigenschappen*de selectie vakjes in voor de volgende opties. U moet door de lijst schuiven om deze opties te vinden, die mogelijk standaard al zijn ingesteld:
    * **LockoutTime schrijven**
    * **PwdLastSet schrijven**

    [![](media/tutorial-enable-sspr-writeback/set-ad-ds-permissions-cropped.png "Set the appropriate permissions in Active Users and Computers for the account that is used by Azure AD Connect")](media/tutorial-enable-sspr-writeback/set-ad-ds-permissions.png#lightbox)

1. Wanneer u klaar bent, selecteert u **Toep assen/OK** om de wijzigingen toe te passen en geopende dialoog vensters te sluiten.

Wanneer u machtigingen bijwerkt, kan het tot een uur of langer duren voordat deze machtigingen worden gerepliceerd naar alle objecten in uw Directory.

Wachtwoord beleid in de on-premises AD DS omgeving kan verhinderen dat het wacht woord opnieuw wordt ingesteld op de juiste manier. Voor een juiste werking van wacht woord terugschrijven moet groeps beleid voor *minimale wachtwoord duur* worden ingesteld op 0. U kunt deze instelling vinden onder **computer configuratie > beleid > Windows-instellingen > beveiligings instellingen > account beleid** in `gpedit.msc`.

Als u het groeps beleid bijwerkt, wacht u totdat het bijgewerkte beleid repliceert of gebruikt u de `gpupdate /force` opdracht.

## <a name="enable-password-writeback-in-azure-ad-connect"></a>Wacht woord terugschrijven inschakelen in Azure AD Connect

Een van de configuratie opties in Azure AD Connect is voor het terugschrijven van wacht woorden. Wanneer deze optie is ingeschakeld, worden door wachtwoord wijzigings gebeurtenissen Azure AD Connect de bijgewerkte referenties opnieuw gesynchroniseerd naar de on-premises AD DS omgeving.

Als u de functie voor het terugschrijven van wacht woord opnieuw instellen wilt inschakelen, schakelt u eerst de terugschrijf optie in Azure AD Connect. Voer vanaf uw Azure AD Connect-server de volgende stappen uit:

1. Meld u aan bij uw Azure AD Connect-server en start de wizard **Azure AD Connect** -configuratie.
1. Selecteer **Configureren** op de **welkomstpagina**.
1. Op de pagina **Extra taken** selecteert u **Synchronisatieopties aanpassen** en vervolgens **Volgende**.
1. Voer op de pagina **verbinding maken met Azure AD** een globale beheerders referenties in voor uw Azure-Tenant en selecteer **volgende**.
1. Op de pagina's **Verbinding maken met directory´s** en **domein/OE filteren** selecteert u **Volgende**.
1. Op de pagina **Optionele functies** schakelt u het selectievakje in naast **Wachtwoord terugschrijven** en selecteert u **Volgende**.

    ![Azure AD Connect voor het terugschrijven van wacht woorden configureren](media/tutorial-enable-sspr-writeback/enable-password-writeback.png)

1. Op de pagina **Gereed om te configureren** selecteert u **Configureren** wacht u tot het proces is voltooid.
1. Als u ziet dat de configuratie is voltooid, selecteert u **Afsluiten**.

## <a name="enable-password-writeback-for-sspr"></a>Wacht woord terugschrijven inschakelen voor SSPR

Als u wacht woord terugschrijven hebt ingeschakeld in Azure AD Connect, configureert u nu Azure AD SSPR voor write-back. Wanneer u SSPR inschakelt voor het terugschrijven van wacht woorden, moeten gebruikers die hun wacht woord wijzigen of opnieuw instellen, het wacht woord opnieuw synchroniseren naar de on-premises AD DS omgeving.

Als u het terugschrijven van wacht woorden in SSPR wilt inschakelen, voert u de volgende stappen uit:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een account voor globale beheerders.
1. Zoek en selecteer **Azure Active Directory**, selecteer **wacht woord opnieuw instellen**en kies vervolgens **on-premises integratie**.
1. Stel de optie voor het **terugschrijven van wacht woorden naar uw on-premises adres lijst** in op *Ja*.
1. Stel de optie voor **toestaan dat gebruikers accounts ontgrendelen zonder hun wacht woord opnieuw** in te stellen op *Ja*.

    ![Selfservice voor wachtwoord herstel van Azure AD inschakelen voor het terugschrijven van wacht woorden](media/tutorial-enable-sspr-writeback/enable-sspr-writeback.png)

1. Wanneer u klaar bent, selecteert u **Opslaan**.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de SSPR write-functionaliteit die u hebt geconfigureerd als onderdeel van deze zelf studie niet meer wilt gebruiken, voert u de volgende stappen uit:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Zoek en selecteer **Azure Active Directory**, selecteer **wacht woord opnieuw instellen**en kies vervolgens **on-premises integratie**.
1. Stel de optie voor het **terugschrijven van wacht woorden naar uw on-premises adres lijst** in op *Nee*.
1. Stel de optie voor **toestaan dat gebruikers accounts ontgrendelen zonder hun wacht woord opnieuw** in te stellen op *Nee*.

Als u geen wachtwoord functionaliteit meer wilt gebruiken, voert u de volgende stappen uit vanaf uw Azure AD Connect-server:

1. Meld u aan bij uw Azure AD Connect-server en start de wizard **Azure AD Connect** -configuratie.
1. Selecteer **Configureren** op de **welkomstpagina**.
1. Op de pagina **Extra taken** selecteert u **Synchronisatieopties aanpassen** en vervolgens **Volgende**.
1. Voer op de pagina **verbinding maken met Azure AD** een globale beheerders referenties in voor uw Azure-Tenant en selecteer **volgende**.
1. Op de pagina's **Verbinding maken met directory´s** en **domein/OE filteren** selecteert u **Volgende**.
1. Schakel op de pagina **optionele functies** het selectie vakje naast **wacht woord terugschrijven** uit en selecteer **volgende**.
1. Op de pagina **Gereed om te configureren** selecteert u **Configureren** wacht u tot het proces is voltooid.
1. Als u ziet dat de configuratie is voltooid, selecteert u **Afsluiten**.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u Azure AD SSPR Write naar een on-premises AD DS omgeving ingeschakeld. U hebt geleerd hoe u:

> [!div class="checklist"]
> * De vereiste machtigingen voor het terugschrijven van wacht woorden configureren
> * Schakel de optie voor het terugschrijven van wacht woorden in Azure AD Connect
> * Wacht woord terugschrijven inschakelen in azure AD SSPR

> [!div class="nextstepaction"]
> [Risico's beoordelen bij het aanmelden](tutorial-risk-based-sspr-mfa.md)
