---
title: Een beheer programma voor virtueel bureau blad van Windows implementeren met Service-Principal-Azure
description: Het beheer programma voor Windows virtueel bureau blad implementeren met behulp van Power shell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0838edb03c4868548f3d09f14d71ec7016e670a4
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79127792"
---
# <a name="deploy-a-management-tool-with-powershell"></a>Een beheer programma implementeren met Power shell

In dit artikel wordt uitgelegd hoe u het beheer programma implementeert met behulp van Power shell.

## <a name="important-considerations"></a>Belang rijke overwegingen

Elk Azure Active Directory (Azure AD)-abonnement van de Tenant moet een eigen afzonderlijke implementatie van het beheer programma hebben. Dit hulp programma biedt geen ondersteuning voor B2B-scenario's (Business-to-Business) van Azure AD. 

Dit beheer programma is een voor beeld. Micro soft zal belang rijke updates voor de beveiliging en kwaliteit bieden. [De bron code is beschikbaar in github](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy). Of u nu een klant of partner bent, we raden u aan het hulp programma aan te passen aan de behoeften van uw bedrijf.

De volgende browsers zijn compatibel met het beheer programma:

- Google Chrome 68 of hoger
- Micro soft Edge 40,15063 of hoger
- Mozilla Firefox 52,0 of hoger
- Safari 10 of hoger (alleen macOS)

## <a name="what-you-need-to-deploy-the-management-tool"></a>Wat u nodig hebt om het beheer programma te implementeren

Voordat u het beheer programma implementeert, hebt u een Azure Active Directory-gebruiker (Azure AD) nodig voor het maken van een app-registratie en het implementeren van de beheer GEBRUIKERSINTERFACE. Deze gebruiker moet:

- Machtiging voor het maken van resources in uw Azure-abonnement
- Toestemming voor het maken van een Azure AD-toepassing. Volg deze stappen om te controleren of uw gebruiker over de vereiste machtigingen beschikt door de instructies in de [vereiste machtigingen](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)te volgen.

Als u het beheer programma wilt implementeren en configureren, moet u eerst de volgende Power shell-scripts downloaden van de [github opslag plaats van RDS-sjablonen](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy/scripts) en deze opslaan in dezelfde map op uw lokale computer.

  - createWvdMgmtUxAppRegistration. ps1
  - updateWvdMgmtUxApiUrl. ps1

Nadat u het beheer programma hebt geïmplementeerd en geconfigureerd, raden we u aan een gebruiker te vragen de beheer GEBRUIKERSINTERFACE te starten om ervoor te zorgen dat alles werkt. De gebruiker die de beheer GEBRUIKERSINTERFACE start, moet beschikken over een roltoewijzing waarmee ze de Windows Virtual Desktop-Tenant kunnen weer geven of bewerken.

## <a name="set-up-powershell"></a>Power shell instellen

Ga aan de slag door u aan te melden bij zowel de AZ-als Azure AD Power shell-modules. U kunt als volgt aanmelden:

1. Open Power shell als beheerder en navigeer naar de map waarin u de Power shell-scripts hebt opgeslagen.
2. Meld u aan bij Azure met een account met eigenaar-of Inzender machtigingen voor het Azure-abonnement dat u wilt gebruiken voor het maken van het beheer programma door de volgende cmdlet uit te voeren:

    ```powershell
    Login-AzAccount
    ```

3. Voer de volgende cmdlet uit om u aan te melden bij Azure AD met hetzelfde account dat u hebt gebruikt voor de AZ Power shell-module:

    ```powershell
    Connect-AzureAD
    ```

4. Daarna gaat u naar de map waar u de twee Power shell-scripts van de RDS-sjablonen GitHub opslag plaats hebt opgeslagen.

Houd het Power shell-venster dat u hebt gebruikt om u aan te melden, om extra Power shell-cmdlets uit te voeren terwijl u bent aangemeld.

## <a name="create-an-azure-active-directory-app-registration"></a>Een Azure Active Directory app-registratie maken

Voer de volgende opdrachten uit om de app-registratie met de vereiste API-machtigingen te maken:

```powershell
$appName = Read-Host -Prompt "Enter a unique name for the management tool's app registration. The name can't contain spaces or special characters."
$subscriptionId = Read-Host -Prompt "Enter the Azure subscription ID where you will be deploying the management tool."

.\createWvdMgmtUxAppRegistration.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

Nu u de registratie van de Azure AD-app hebt voltooid, kunt u het beheer programma implementeren.

## <a name="deploy-the-management-tool"></a>Hulpprogramma voor beheer implementeren

Voer de volgende Power shell-opdrachten uit om het beheer hulpprogramma te implementeren en dit te koppelen aan de service-principal die u zojuist hebt gemaakt:
     
```powershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateParameters = @{}
$templateParameters.Add('isServicePrincipal', $true)
$templateParameters.Add('azureAdminUserPrincipalNameOrApplicationId', $ServicePrincipalCredentials.UserName)
$templateParameters.Add('azureAdminPassword', $servicePrincipalCredentials.Password)
$templateParameters.Add('applicationName', $appName)

Get-AzSubscription -SubscriptionId $subscriptionId | Select-AzSubscription
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-management-ux/deploy/mainTemplate.json" `
    -TemplateParameterObject $templateParameters `
    -Verbose
```

Nadat u de web-app hebt gemaakt, moet u een omleidings-URI toevoegen aan de Azure AD-toepassing om gebruikers te kunnen aanmelden.

## <a name="set-the-redirect-uri"></a>De omleidings-URI instellen

Voer de volgende Power shell-opdrachten uit om de web-app-URL op te halen en in te stellen als de omleidings-URI voor verificatie (ook wel een antwoord-URL genoemd):

```powershell
$webApp = Get-AzWebApp -ResourceGroupName $resourceGroupName -Name $appName
$redirectUri = "https://" + $webApp.DefaultHostName + "/"
Get-AzureADApplication -All $true | where { $_.AppId -match $servicePrincipalCredentials.UserName } | Set-AzureADApplication -ReplyUrls $redirectUri  
```

Nu u een omleidings-URI hebt toegevoegd, moet u de API-URL bijwerken zodat het beheer programma kan communiceren met de API-back-end-service.

## <a name="update-the-api-url-for-the-web-application"></a>De API-URL voor de webtoepassing bijwerken

Voer het volgende script uit om de API-URL-configuratie bij te werken in de front-end van de webtoepassing:

```powershell
.\updateWvdMgmtUxApiUrl.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

Nu u de web-app voor het beheer programma volledig hebt geconfigureerd, is het tijd om de Azure AD-toepassing te controleren en toestemming te geven.

## <a name="verify-the-azure-ad-application-and-provide-consent"></a>De Azure AD-toepassing controleren en toestemming geven

De configuratie van de Azure AD-toepassing controleren en toestemming geven:

1. Open uw Internet browser en meld u aan bij de [Azure Portal](https://portal.azure.com/) met uw beheerders account.
2. Zoek in de zoek balk aan de bovenkant van de Azure Portal naar **app-registraties** en selecteer het item onder **Services**.
3. Selecteer **alle toepassingen** en zoek de unieke app-naam die u hebt ingevoerd voor het Power shell-script in [een registratie voor een Azure Active Directory-app maken](#create-an-azure-active-directory-app-registration).
4. Selecteer in het deel venster aan de linkerkant van de browser **verificatie** en zorg ervoor dat de omleidings-URI hetzelfde is als de web-app-URL voor het beheer programma, zoals wordt weer gegeven in de volgende afbeelding.
   
   [![de verificatie pagina met de ingevoerde omleidings-URI](media/management-ui-redirect-uri-inline.png)](media/management-ui-redirect-uri-expanded.png#lightbox)

5. Selecteer in het linkerdeel venster **API-machtigingen** om te bevestigen dat er machtigingen zijn toegevoegd. Als u een globale beheerder bent, selecteert u de **beheerder toestemming geven voor `tenantname`** en volgt u de aanwijzingen in het dialoog venster om toestemming van de beheerder voor uw organisatie op te geven.
    
    [![de pagina API-machtigingen](media/management-ui-permissions-inline.png)](media/management-ui-permissions-expanded.png#lightbox)

U kunt nu beginnen met het beheer programma.

## <a name="use-the-management-tool"></a>Het beheer programma gebruiken

Nu u het beheer programma op elk gewenst moment hebt ingesteld, kunt u dit altijd en overal starten. U kunt het hulp programma als volgt starten:

1. Open de URL van de web-app in een webbrowser. Als u de URL niet meer weet, kunt u zich aanmelden bij Azure, de app service vinden die u voor het beheer programma hebt geïmplementeerd en vervolgens de URL selecteren.
2. Meld u aan met de referenties van uw Windows-virtueel bureau blad.
   
   > [!NOTE]
   > Als u geen beheerder toestemming hebt gegeven tijdens het configureren van het beheer programma, moet elke gebruiker die zich aanmeldt een eigen toestemming van de gebruiker opgeven om het hulp programma te kunnen gebruiken.

3. Wanneer u wordt gevraagd om een Tenant groep te kiezen, selecteert u **standaard Tenant groep** in de vervolg keuzelijst.
4. Wanneer u **standaard Tenant groep**selecteert, wordt er een menu aan de linkerkant van het venster weer gegeven. Zoek in dit menu de naam van uw Tenant groep en selecteer deze.
   
   > [!NOTE]
   > Als u een aangepaste Tenant groep hebt, voert u de naam hand matig in in plaats van te kiezen in de vervolg keuzelijst.

## <a name="report-issues"></a>Problemen melden

Als u problemen ondervindt met het beheer programma of andere virtuele bureau blad-hulpprogram ma's van Windows, volgt u de instructies in [Azure Resource Manager sjablonen voor extern bureaublad-services](https://github.com/Azure/RDS-Templates/blob/master/README.md) om ze te rapporteren op github.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u het beheer programma implementeert en er verbinding mee maakt, leert u hoe u Azure Service Health kunt gebruiken om service problemen en status adviezen te bewaken. Zie onze [zelf studie Service waarschuwingen instellen](./set-up-service-alerts.md)voor meer informatie.
