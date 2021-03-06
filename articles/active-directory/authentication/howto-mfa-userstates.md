---
title: Multi-Factor Authentication per gebruiker-Azure Active Directory
description: Schakel MFA in door de gebruikers status te wijzigen in azure Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 61d7227c57422cfe2228002750ec29bffa385d44
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2020
ms.locfileid: "76756760"
---
# <a name="how-to-require-two-step-verification-for-a-user"></a>Verificatie in twee stappen vereisen voor een gebruiker

U kunt een van twee benaderingen nemen voor het vereisen van verificatie in twee stappen, beide waarvoor een algemeen beheerders account nodig is. De eerste mogelijkheid is het inschakelen van MFA voor een gebruiker. Wanneer gebruikers afzonderlijk zijn ingeschakeld, worden ze gevraagd zich te authenticeren met behulp van hun tweede stap telkens wanneer ze zich aanmelden (met enkele uitzonderingen, zoals wanneer ze zich vanaf vertrouwde-IP-adressen aanmelden of wanneer de functie _dit apparaat onhouden_ is ingeschakeld). De tweede optie is om een beleid voor voorwaardelijke toegang in te stellen waarvoor een verificatie in twee stappen onder bepaalde voor waarden vereist is.

> [!TIP]
> Het inschakelen van Azure Multi-Factor Authentication met beleids regels voor voorwaardelijke toegang is de aanbevolen methode. Het wijzigen van de gebruikers status wordt niet meer aanbevolen, tenzij uw licenties de voorwaardelijke toegang niet bevatten, omdat gebruikers elke keer dat ze zich aanmelden verplichten om MFA uit te voeren.

## <a name="choose-how-to-enable"></a>Kiezen hoe u kunt inschakelen

**Ingeschakeld door het wijzigen van de gebruikers status** : dit is de traditionele methode voor het vereisen van verificatie in twee stappen en wordt beschreven in dit artikel. Het werkt met Azure MFA in de Cloud en Azure MFA-server. Wanneer u deze methode gebruikt, moeten gebruikers **elke keer** dat ze zich aanmelden een verificatie in twee stappen uitvoeren en het beleid voor voorwaardelijke toegang negeren.

**Ingeschakeld door het beleid voor voorwaardelijke toegang** : dit is de meest flexibele manier om verificatie in twee stappen voor uw gebruikers in te scha kelen. Het gebruik van beleid voor voorwaardelijke toegang kan alleen worden ingeschakeld voor Azure MFA in de Cloud en is een Premium-functie van Azure AD. Meer informatie over deze methode vindt u in [Azure multi-factor Authentication op basis van de Cloud implementeren](howto-mfa-getstarted.md).

**Ingeschakeld door Azure AD Identity Protection** : deze methode maakt gebruik van het Azure AD Identity Protection risico beleid om verificatie in twee stappen alleen op basis van de aanmeldings Risico's voor alle Cloud toepassingen te vereisen. Voor deze methode is Azure Active Directory P2-licentie vereist. Meer informatie over deze methode vindt u in [Azure Active Directory Identity Protection](../identity-protection/howto-sign-in-risk-policy.md)

> [!Note]
> Meer informatie over licenties en prijzen kunt u vinden op de pagina met [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/
) -en [multi-factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) prijzen.

## <a name="enable-azure-mfa-by-changing-user-state"></a>Azure MFA inschakelen door de gebruikers status te wijzigen

Gebruikers accounts in azure Multi-Factor Authentication de volgende drie statussen hebben:

> [!IMPORTANT]
> Als Azure MFA wordt ingeschakeld via een beleid voor voorwaardelijke toegang, wordt de status van de gebruiker niet gewijzigd. Gebruikers die geen alarm zijn, worden weer gegeven als uitgeschakeld. Voorwaardelijke toegang heeft geen invloed op de status. **Organisaties moeten gebruikers niet in-of afdwingen als ze gebruikmaken van beleid voor voorwaardelijke toegang.**

| Status | Beschrijving | Betrokken niet-browser-apps | Browser-apps die worden beïnvloed | Betrokken moderne verificatie |
|:---:| --- |:---:|:--:|:--:|
| Uitgeschakeld | De standaard status voor een nieuwe gebruiker die niet is inge schreven bij Azure MFA. | Nee | Nee | Nee |
| Ingeschakeld | De gebruiker is inge schreven bij Azure MFA, maar is niet geregistreerd. Er wordt een prompt weer gegeven om de volgende keer dat ze zich aanmelden te registreren. | Nee.  Ze blijven werken totdat het registratie proces is voltooid. | Ja. Nadat de sessie is verlopen, is de Azure MFA-registratie vereist.| Ja. Nadat het toegangs token is verlopen, is de Azure MFA-registratie vereist. |
| Afgedwongen | De gebruiker is inge schreven en heeft het registratie proces voor Azure MFA voltooid. | Ja. Apps vereisen app-wacht woorden. | Ja. Azure MFA is vereist bij de aanmelding. | Ja. Azure MFA is vereist bij de aanmelding. |

De status van een gebruiker geeft aan of een beheerder deze heeft inge schreven in azure MFA en of het registratie proces is voltooid.

Alle gebruikers beginnen *Uitgeschakeld*. Als u een gebruiker zich inschrijft via Azure MFA, wordt zijn of haar status gewijzigd naar *Ingeschakeld*. Zodra de ingeschakelde gebruiker zich heeft aangemeld en het registratieproces heeft voltooid, wordt de status ingesteld op *Afgedwongen*.

> [!NOTE]
> Als MFA opnieuw wordt ingeschakeld voor een gebruikers object dat al registratie details, zoals een telefoon of e-mail adres, heeft, moeten beheerders die gebruiker MFA opnieuw registreren via Azure Portal of Power shell. Als de gebruiker zich niet opnieuw registreert, wordt de MFA-status niet overgezet van *ingeschakeld* om te worden *afgedwongen* in de gebruikers interface voor MFA-beheer.

### <a name="view-the-status-for-a-user"></a>De status van een gebruiker weer geven

Gebruik de volgende stappen om de pagina te openen waar u de status van een gebruiker kunt bekijken en beheren:

1. Meld u als beheerder aan bij [Azure Portal](https://portal.azure.com).
2. Zoek en selecteer *Azure Active Directory*. Selecteer **gebruikers** > **alle gebruikers**.
3. Selecteer **multi-factor Authentication**. Mogelijk moet u naar rechts schuiven om deze menu optie weer te geven. Selecteer de onderstaande scherm afbeelding om het volledige Azure Portal venster en de menu locatie weer te geven:[![](media/howto-mfa-userstates/selectmfa-cropped.png "Multi-Factor Authentication selecteren in het venster gebruikers in azure AD")](media/howto-mfa-userstates/selectmfa.png#lightbox)
4. Er wordt een nieuwe pagina geopend waarin de gebruikers statussen worden weer gegeven.
   ![gebruikers status van multi-factor Authentication-scherm afbeelding](./media/howto-mfa-userstates/userstate1.png)

### <a name="change-the-status-for-a-user"></a>De status voor een gebruiker wijzigen

1. Gebruik de voor gaande stappen om naar de pagina Azure Multi-Factor Authentication- **gebruikers** te gaan.
2. Zoek de gebruiker die u wilt inschakelen voor Azure MFA. Mogelijk moet u de weer gave bovenaan wijzigen.
   ![Selecteer de gebruiker waarvan u de status wilt wijzigen van het tabblad gebruikers](./media/howto-mfa-userstates/enable1.png)
3. Schakel het selectievakje naast de naam van de gebruiker in.
4. Klik aan de rechter kant onder **snelle stappen**op **inschakelen** of **uitschakelen**.
   ![geselecteerde gebruiker inschakelen door op inschakelen te klikken in het menu snelle stappen](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > *Ingeschakelde* gebruikers worden automatisch omgezet naar *Afgedwongen* zodra ze zich registreren voor Azure MFA. Wijzig de status van de gebruiker niet handmatig in *Afgedwongen*.

5. Bevestig uw selectie in het pop-upvenster dat wordt geopend.

Nadat u gebruikers hebt ingeschakeld, kunt u gebruikers informeren via e-mail Laat ze weten dat ze gevraagd worden om zich te registreren de volgende keer dat ze zich aanmelden. Als uw organisatie gebruikmaakt van niet-browser-apps die moderne verificatie niet ondersteunen, moeten zij ook app-wachtwoorden maken. U kunt ook een koppeling naar de [Azure MFA eindgebruiker handleiding](../user-help/multi-factor-authentication-end-user.md) meesturen waarmee gebruikers aan de slag kunnen.

### <a name="use-powershell"></a>PowerShell gebruiken

Als u de gebruikers status wilt wijzigen met behulp van [Azure AD Power shell](/powershell/azure/overview), wijzigt u `$st.State`. Er zijn drie mogelijke statussen:

* Ingeschakeld
* Afgedwongen
* Uitgeschakeld  

Verplaats gebruikers niet rechtstreeks naar de status *Afgedwongen*. Als u dit doet, werken apps die niet op een browser zijn gebaseerd niet meer, omdat de gebruiker het registratieproces voor Azure MFA niet heeft doorlopen en dus geen [app-wachtwoord](howto-mfa-mfasettings.md#app-passwords) heeft opgehaald.

Installeer de module eerst met behulp van:

   ```PowerShell
   Install-Module MSOnline
   ```

> [!TIP]
> Vergeet niet eerst verbinding te maken met **Connect-MsolService**

   ```PowerShell
   Connect-MsolService
   ```

In dit voor beeld Power shell-script wordt MFA ingeschakeld voor een afzonderlijke gebruiker:

   ```PowerShell
   Import-Module MSOnline
   Connect-MsolService
   $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
   $st.RelyingParty = "*"
   $st.State = "Enabled"
   $sta = @($st)
   Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
   ```

Het gebruik van Power shell is een goede optie wanneer u gebruikers bulksgewijs wilt kunnen inschakelen. Als voor beeld wordt met het volgende script een lijst met gebruikers door lopen en wordt MFA op hun accounts ingeschakeld:

   ```PowerShell
   $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
   foreach ($user in $users)
   {
       $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
       $st.RelyingParty = "*"
       $st.State = "Enabled"
       $sta = @($st)
       Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
   }
   ```

Als u MFA wilt uitschakelen, gebruikt u dit script:

   ```PowerShell
   Get-MsolUser -UserPrincipalName user@domain.com | Set-MsolUser -StrongAuthenticationRequirements @()
   ```

Dit kan ook worden inge kort tot:

   ```PowerShell
   Set-MsolUser -UserPrincipalName user@domain.com -StrongAuthenticationRequirements @()
   ```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Gebruikers converteren van MFA per gebruiker naar MFA op basis van voorwaardelijke toegang

De volgende Power shell kan u helpen bij het maken van de conversie naar Azure Multi-Factor Authentication op basis van voorwaardelijke toegang.

Voer deze Power shell uit in een ISE-venster of sla het bestand op als een. Het PS1-bestand dat lokaal moet worden uitgevoerd.

```PowerShell
# Sets the MFA requirement state
function Set-MfaState {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )

    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }

        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}

# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
```

> [!NOTE]
> Het gedrag en het Power shell-script hierboven zijn onlangs gewijzigd. Voorheen werd het script bespaard op de MFA-methoden, uitgeschakeld MFA en de methoden hersteld. Dit is nu niet langer nodig om de methoden niet te wissen met het standaard gedrag voor uitschakelen.
>
> Als MFA opnieuw wordt ingeschakeld voor een gebruikers object dat al registratie details, zoals een telefoon of e-mail adres, heeft, moeten beheerders die gebruiker MFA opnieuw registreren via Azure Portal of Power shell. Als de gebruiker zich niet opnieuw registreert, wordt de MFA-status niet overgezet van *ingeschakeld* om te worden *afgedwongen* in de gebruikers interface voor MFA-beheer.

## <a name="next-steps"></a>Volgende stappen

* Waarom wordt een gebruiker gevraagd of niet gevraagd MFA uit te voeren? Zie de sectie [Azure AD-aanmeld rapport in de rapporten in Azure multi-factor Authentication document](howto-mfa-reporting.md#azure-ad-sign-ins-report).
* Zie het artikel [Azure multi-factor Authentication-instellingen configureren](howto-mfa-mfasettings.md) voor meer informatie over het configureren van aanvullende instellingen, zoals vertrouwde IP-adressen, aangepaste spraak berichten en fraude waarschuwingen.
* Informatie over het beheren van gebruikers instellingen voor Azure Multi-Factor Authentication vindt u in het artikel [gebruikers instellingen beheren met Azure multi-factor Authentication in de Cloud](howto-mfa-userdevicesettings.md)
