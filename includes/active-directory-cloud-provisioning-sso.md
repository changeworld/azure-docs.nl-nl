---
author: billmath
ms.service: active-directory
ms.subservice: cloud-provisioning
ms.topic: include
ms.date: 10/16/2019
ms.author: billmath
ms.openlocfilehash: ba1bdd60a3363cfab694bae9b8ee3cf63e24b054
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76907576"
---
## <a name="steps-to-enable-single-sign-on"></a>Stappen voor het inschakelen van eenmalige aanmelding
Cloud inrichting werkt met eenmalige aanmelding.  Er is momenteel geen optie om SSO in te scha kelen wanneer de agent is geïnstalleerd, maar u kunt de onderstaande stappen gebruiken om SSO in te scha kelen en te gebruiken. 

### <a name="step-1-download-and-extract-azure-ad-connect-files"></a>Stap 1: Azure AD Connect-bestanden downloaden en uitpakken
1.  Down load eerst de nieuwste versie van [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)
2.  Open een opdracht prompt met beheerders bevoegdheden en navigeer naar het MSI-bestand dat u zojuist hebt gedownload.
3.  Voer de volgende handelingen uit: `msiexec /a C:\filepath\AzureADConnect.msi /qb TARGETDIR=C:\filepath\extractfolder`
4. Wijzig filepath en extractfolder zodat deze overeenkomen met het bestandspad en de naam van de uitpak-map.  De inhoud moet zich nu in de map extra heren bevindt.

### <a name="step-2-import-the-seamless-sso-powershell-module"></a>Stap 2: de naadloze SSO Power shell-module importeren

1. Down load en Installeer [Azure AD Power shell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
2. Blader naar de map `%programfiles%\Microsoft Azure Active Directory Connect`.
3. Importeer de naadloze SSO Power shell-module met behulp van deze opdracht: `Import-Module .\AzureADSSO.psd1`.

### <a name="step-3-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>Stap 3: de lijst met Active Directory forests ophalen waarop naadloze SSO is ingeschakeld

1. Voer Power shell uit als beheerder. Roep `New-AzureADSSOAuthenticationContext`aan in Power shell. Voer de referenties van de globale beheerder van uw Tenant in wanneer dit wordt gevraagd.
2. `Get-AzureADSSOStatus`aanroepen. Met deze opdracht geeft u de lijst met Active Directory forests weer (Bekijk de lijst ' domeinen ') waarop deze functie is ingeschakeld.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>Stap 4: naadloze SSO inschakelen voor elke Active Directory-forest

1. `Enable-AzureADSSOForest`aanroepen. Wanneer u hierom wordt gevraagd, voert u de referenties voor de domein beheerder in voor het beoogde Active Directory-forest.

   > [!NOTE]
   >De gebruikers naam van de domein beheerder referenties moet worden opgegeven in de indeling SAM-account naam (contoso\johndoe of contoso. com\johndoe). We gebruiken het domein gedeelte van de gebruikers naam voor het zoeken van de domein controller van de domein beheerder met behulp van DNS.

   >[!NOTE]
   >Het domein beheerders account dat wordt gebruikt, mag geen lid zijn van de groep met beveiligde gebruikers. Als dit het geval is, mislukt de bewerking.

2. Herhaal de vorige stap voor elke Active Directory forest waarin u de functie wilt instellen.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>Stap 5. De functie inschakelen op uw Tenant

Als u de functie op uw Tenant wilt inschakelen, roept u `Enable-AzureADSSO -Enable $true`aan.
