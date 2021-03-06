---
title: 'Zelf studie: verificatie inschakelen in een app met één pagina'
titleSuffix: Azure AD B2C
description: In deze zelf studie leert u hoe u Azure Active Directory B2C kunt gebruiken om gebruikers aanmelding te bieden voor een op Java script gebaseerde toepassing met één pagina (SPA).
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/14/2019
ms.custom: mvc, seo-javascript-september2019
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 435800d9c6bfd9131d50681a9808f9836104fac0
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78183334"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-using-azure-active-directory-b2c-azure-ad-b2c"></a>Zelf studie: verificatie inschakelen in een toepassing met één pagina met behulp van Azure Active Directory B2C (Azure AD B2C)

In deze zelf studie wordt uitgelegd hoe u Azure Active Directory B2C (Azure AD B2C) gebruikt om u aan te melden en gebruikers aan te melden bij een toepassing met één pagina (SPA). Met Azure AD B2C zijn uw toepassingen in staat om zich met behulp van open-standaardprotocollen te verifiëren bij sociale accounts, Enterprise-accounts en Azure Active Directory-accounts.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De toepassing bijwerken in Azure AD B2C
> * Het voorbeeld configureren voor gebruik van de toepassing
> * Aanmelden met behulp van de gebruikersstroom

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

U hebt de volgende Azure AD B2C resources nodig om door te gaan met de stappen in deze zelf studie:

* [Azure AD B2C Tenant](tutorial-create-tenant.md)
* De [toepassing is geregistreerd](tutorial-register-applications.md) in uw Tenant
* [Gebruikers stromen die zijn gemaakt](tutorial-create-user-flows.md) in uw Tenant

Daarnaast hebt u het volgende nodig in uw lokale ontwikkel omgeving:

* Code-editor, bijvoorbeeld [Visual Studio code](https://code.visualstudio.com/) of [Visual Studio 2019](https://www.visualstudio.com/downloads/)
* [.NET Core SDK 2,2](https://dotnet.microsoft.com/download) of hoger
* [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>De toepassing bijwerken

In de tweede zelf studie die u als onderdeel van de vereisten hebt voltooid, hebt u een webtoepassing geregistreerd in Azure AD B2C. Om te communiceren met het voorbeeld in deze zelfstudie, moet u een omleidings-URI toevoegen aan de toepassing in Azure AD B2C.

U kunt de huidige **toepassingen** ervaring of onze nieuwe **Preview-ervaring (Unified app-registraties)** gebruiken om de toepassing bij te werken. [Meer informatie over de nieuwe ervaring](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Toepassingen](#tab/applications/)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Zorg ervoor dat u de map met uw Azure AD B2C-Tenant gebruikt door het filter **Directory + abonnement** te selecteren in het bovenste menu en de map te kiezen die uw Tenant bevat.
1. Selecteer **alle services** in de linkerbovenhoek van de Azure Portal, zoek naar en selecteer **Azure AD B2C**.
1. Selecteer **Toepassingen** en selecteer vervolgens de toepassing *webapp1*.
1. Voeg onder **Antwoord-URL**`http://localhost:6420` toe.
1. Selecteer **Opslaan**.
1. Noteer de **toepassings-id**op de pagina Eigenschappen. U gebruikt de App-ID in een latere stap wanneer u de code in de webtoepassing met één pagina bijwerkt.

#### <a name="app-registrations-preview"></a>[App-registraties (preview-versie)](#tab/app-reg-preview/)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het filter **Directory + abonnement** in het bovenste menu en selecteer vervolgens de map die uw Azure AD B2C Tenant bevat.
1. Selecteer in het linkermenu **Azure AD B2C**. U kunt ook **alle services** selecteren en **Azure AD B2C**zoeken en selecteren.
1. Selecteer **app-registraties (preview)** , selecteer het tabblad **toepassingen die eigendom** zijn en selecteer vervolgens de toepassing *webapp1* .
1. Selecteer **verificatie**en selecteer vervolgens **de nieuwe ervaring uitproberen** (indien weer gegeven).
1. Onder **Web**selecteert u de koppeling **URI toevoegen** , voert u `http://localhost:6420`in en selecteert u vervolgens **Opslaan**.
1. Selecteer **Overzicht**.
1. Noteer de **id van de toepassing (client)** voor gebruik in een latere stap wanneer u de code in de webtoepassing met één pagina bijwerkt.

* * *

## <a name="get-the-sample-code"></a>De voorbeeldcode halen

In deze zelf studie configureert u een code voorbeeld dat u kunt downloaden van GitHub. In het voor beeld ziet u hoe een toepassing met één pagina Azure AD B2C kan gebruiken voor aanmelding bij gebruikers en bij het aanmelden en voor het aanroepen van een beveiligde web-API.

[Download een ZIP-bestand ](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) of kloon de voorbeeld-web-app vanuit GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

## <a name="update-the-sample"></a>Het voor beeld bijwerken

Nu u het voor beeld hebt verkregen, werkt u de code bij met de naam van uw Azure AD B2C Tenant en de toepassings-ID die u in een eerdere stap hebt vastgelegd.

1. Open het `index.html`-bestand in de hoofdmap van de map met het voor beeld.
1. In de `msalConfig` definitie wijzigt u de **clientId** -waarde met de toepassings-id die u in een eerdere stap hebt vastgelegd. Werk vervolgens de URI-waarde van de **CA** bij met de naam van de Azure AD B2C Tenant. Werk de URI ook bij met de naam van de stroom voor het registreren en aanmelden van de gebruiker die u hebt gemaakt in een van de vereisten (bijvoorbeeld *B2C_1_signupsignin1*).

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "00000000-0000-0000-0000-000000000000", //This is your client ID
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi", //This is your tenant info
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

    De naam van de gebruikersstroom die in deze zelfstudie wordt gebruikt, is **B2C_1_signupsignin1**. Als u een andere gebruikers stroom naam gebruikt, geeft u die naam op in de waarde `authority`.

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

1. Open een console venster en ga naar de map met het voor beeld. Bijvoorbeeld:

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    ```
1. Voer de volgende opdrachten uit:

    ```
    npm install && npm update
    node server.js
    ```

    In het console venster wordt het poort nummer van de lokaal uitgevoerde node. js-server weer gegeven:

    ```
    Listening on port 6420...
    ```

1. Ga naar `http://localhost:6420` in uw browser om de toepassing weer te geven.

Het voorbeeld biedt ondersteuning voor registratie, aanmelding, het bewerken van een profiel en het opnieuw instellen van een wachtwoord. In deze zelfstudie leest u hoe gebruikers zich kunnen registreren met behulp van een e-mailadres.

### <a name="sign-up-using-an-email-address"></a>Aanmelden met een e-mailadres

> [!WARNING]
> Nadat u zich hebt aangemeld of aangemeld, ziet u mogelijk een [fout bericht over onvoldoende machtigingen](#error-insufficient-permissions). Als gevolg van de huidige implementatie van het code voorbeeld, wordt deze fout verwacht. Dit probleem wordt opgelost in een toekomstige versie van het code voorbeeld, op het moment dat deze waarschuwing wordt verwijderd.

1. Selecteer **Aanmelden** om de *B2C_1_signupsignin1* gebruikers stroom te initiëren die u in een eerdere stap hebt opgegeven.
1. Azure AD B2C toont een aanmeldingspagina met een koppeling voor registratie. Omdat u nog geen account hebt, selecteert u de koppeling **nu registreren** .
1. Tijdens de aanmeldingswerkstroom wordt een pagina weergegeven waarmee de identiteit wordt opgehaald en gecontroleerd van de gebruiker die een e-mailadres heeft gebruikt. Tijdens de aanmeldingswerkstroom worden ook het wachtwoord van de gebruiker en de aangevraagde kenmerken opgehaald die in de gebruikersstroom zijn gedefinieerd.

    Gebruik een geldig e-mailadres en voer de verificatie uit met de verificatiecode. Stel een wachtwoord in. Geef waarden voor de aangevraagde kenmerken op.

    ![Registratie pagina die wordt weer gegeven door de gebruikers stroom aanmelden/registreren](./media/tutorial-single-page-app/azure-ad-b2c-sign-up-workflow.png)

1. Selecteer **maken** om een lokaal account te maken in de Azure AD B2C Directory.

Wanneer u maken selecteert, wordt de pagina registreren gesloten en wordt de aanmeldings pagina **opnieuw**weer gegeven.

U kunt nu uw e-mail adres en wacht woord gebruiken om u aan te melden bij de toepassing.

### <a name="error-insufficient-permissions"></a>Fout: onvoldoende machtigingen

Nadat u zich hebt aangemeld, kan de toepassing een fout melding over onvoldoende machtigingen retour neren:

```Output
ServerError: AADB2C90205: This application does not have sufficient permissions against this web resource to perform the operation.
Correlation ID: ce15bbcc-0000-0000-0000-494a52e95cd7
Timestamp: 2019-07-20 22:17:27Z
```

U ontvangt deze fout omdat de webtoepassing probeert toegang te krijgen tot een web-API die wordt beveiligd door de demo Directory *fabrikamb2c*. Omdat uw toegangs token alleen geldig is voor uw Azure AD-adres lijst, is de API-aanroep niet toegestaan.

U kunt deze fout oplossen door door te gaan naar de volgende zelf studie in de reeks (Zie [volgende stappen](#next-steps)) om een beveiligde web-API te maken voor uw Directory.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u het volgende geleerd:

> [!div class="checklist"]
> * De toepassing bijwerken in Azure AD B2C
> * Het voorbeeld configureren voor gebruik van de toepassing
> * Aanmelden met behulp van de gebruikersstroom

Ga nu verder met de volgende zelf studie in de reeks om toegang te verlenen tot een beveiligde web-API van de beveiligd-wachtwoord verificatie:

> [!div class="nextstepaction"]
> [Zelf studie: toegang verlenen tot een ASP.NET Core Web-API van een beveiligd-wachtwoord verificatie met behulp van Azure AD B2C >](tutorial-single-page-app-webapi.md)
