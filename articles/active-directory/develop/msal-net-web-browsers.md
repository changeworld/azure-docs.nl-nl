---
title: Webbrowsers (MSAL.NET) gebruiken | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over specifieke overwegingen bij het gebruik van Xamarin Android met de micro soft Authentication Library voor .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: ed1f47ae99f6346a932d0fe94be7586dc25a672f
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79262735"
---
# <a name="using-web-browsers-msalnet"></a>Webbrowsers (MSAL.NET) gebruiken

Webbrowsers zijn vereist voor interactieve verificatie. Standaard ondersteunt MSAL.NET de [systeem webbrowser](#system-web-browser-on-xamarinios-xamarinandroid) op Xamarin. IOS en Xamarin. Android. [U kunt echter ook de Inge sloten webbrowser inschakelen](#enable-embedded-webviews-on-ios-and-android) , afhankelijk van uw vereisten (UX, eenmalige aanmelding (SSO), beveiliging) in [Xamarin. IOS](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinios) en [Xamarin. Android](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) -apps. En u kunt zelfs [dynamisch kiezen](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) welke webbrowser u wilt gebruiken op basis van de aanwezigheid van Chrome of een browser die aangepaste Chrome-tabbladen in Android ondersteunt. MSAL.NET biedt alleen ondersteuning voor de systeem browser in .NET core desktop-toepassingen.

## <a name="web-browsers-in-msalnet"></a>Webbrowsers in MSAL.NET

### <a name="interaction-happens-in-a-web-browser"></a>Interactie plaatsvindt in een webbrowser

Het is belang rijk om te begrijpen dat bij het interactief ophalen van een token de inhoud van het dialoog venster niet door de bibliotheek wordt verschaft, maar door de STS (Security Token Service). Het verificatie-eind punt verzendt een aantal HTML-en Java script-code die de interactie beheert, die wordt weer gegeven in een webbrowser of Webbe sturings element. Het toestaan van de HTML-interactie door de STS kan worden afgehandeld op diverse voor delen:

- Het wacht woord (als er een is getypt), wordt nooit opgeslagen door de toepassing of de verificatie bibliotheek.
- Hiermee schakelt u de omleidingen naar andere id-providers (bijvoorbeeld aanmelden bij een werk school account of een persoonlijk account met MSAL of met een sociaal account met Azure AD B2C).
- Hiermee kan de STS voorwaardelijke toegang instellen, bijvoorbeeld door de gebruiker meerdere Factor Authentication (MFA) uit te voeren tijdens de verificatie fase (het invoeren van een Windows hello-pincode of het aanroepen van hun telefoon of op een verificatie-app op de telefoon). In gevallen waarin de vereiste multi factor Authentication nog niet is ingesteld, kan de gebruiker deze op dezelfde manier instellen in hetzelfde dialoog venster.  De gebruiker voert zijn of haar mobiele telefoon nummer in en wordt begeleid bij het installeren van een verificatie toepassing en het scannen van een QR-tag om hun account toe te voegen. Deze server gerichte interactie is een geweldige ervaring.
- Hiermee kan de gebruiker in hetzelfde dialoog venster hun wacht woord wijzigen wanneer het wacht woord is verlopen (extra velden opgeven voor het oude wacht woord en het nieuwe wacht woord).
- Hiermee schakelt u de huis stijl van de Tenant of de toepassing (installatie kopieën) in die door de Azure AD-Tenant beheerder/toepassings eigenaar worden beheerd.
- Hiermee kunnen gebruikers toestemming geven om de toepassing toegang te geven tot bronnen/bereiken in hun naam, net na de verificatie.

### <a name="embedded-vs-system-web-ui"></a>Inge sloten versus systeemweb-UI

MSAL.NET is een multi Framework-bibliotheek en heeft Framework-specifieke code voor het hosten van een browser in een besturings element in de gebruikers interface (bijvoorbeeld op de klassieke versie van .net maakt gebruik van WinForms, op Xamarin wordt gebruikgemaakt van systeem eigen mobiele besturings elementen, enzovoort). Dit besturings element wordt `embedded` web-gebruikers interface genoemd. Daarnaast kunt u met MSAL.NET ook de besturingssysteem browser starten.

Over het algemeen is het raadzaam om de standaard waarde van het platform te gebruiken. Dit is doorgaans de systeem browser. De systeem browser is beter dan de gebruikers die zich hebben aangemeld. Als u dit gedrag wilt wijzigen, gebruikt u `WithUseEmbeddedWebView(bool)`

### <a name="at-a-glance"></a>In één oogopslag

| Framework        | geïntegreerde | Systeem | Standaard |
| ------------- |-------------| -----| ----- |
| .NET-klassiek     | Ja | Ja ^ | geïntegreerde |
| .NET Core     | Nee | Ja ^ | Systeem |
| .NET Standard | Nee | Ja ^ | Systeem |
| UWP | Ja | Nee | geïntegreerde |
| Xamarin.Android | Ja | Ja  | Systeem |
| Xamarin.iOS | Ja | Ja  | Systeem |
| Xamarin. Mac| Ja | Nee | geïntegreerde |

^ Vereist de omleidings-URI http://localhost

## <a name="system-web-browser-on-xamarinios-xamarinandroid"></a>Systeem webbrowser op Xamarin. iOS, Xamarin. Android

Standaard ondersteunt MSAL.NET de systeem webbrowser op Xamarin. iOS, Xamarin. Android en .NET core. Voor alle platforms die een gebruikers interface bieden (dat wil zeggen, niet .NET core), wordt er een dialoog venster geleverd door de bibliotheek waarin een webbrowser besturings element wordt Inge sloten. MSAL.NET gebruikt ook een Inge sloten webweergave voor de .NET-Desktop en WAB voor het UWP-platform. Het maakt echter gebruik van de **systeem webbrowser** voor Xamarin IOS-en Xamarin Android-toepassingen. Op iOS kiest u zelfs de webweergave die moet worden gebruikt, afhankelijk van de versie van het besturings systeem (iOS12, iOS11 en eerder).

Het gebruik van de systeem browser heeft het grote voor deel van het delen van de SSO-status met andere toepassingen en met webtoepassingen zonder een Broker (bedrijfs portal/verificator). De systeem browser werd standaard gebruikt in MSAL.NET voor de Xamarin iOS-en Xamarin Android-platformen, omdat op deze platforms het hele scherm door de webbrowser van het systeem wordt ingen omen en de gebruikers ervaring beter is. De webweergave van het systeem kan niet worden onderscheiden van een dialoog venster. Op iOS moet de gebruiker echter mogelijk toestemming geven voor de browser om de toepassing terug te bellen. Dit kan irritant zijn.

## <a name="system-browser-experience-on-net-core"></a>Systeem browser-ervaring op .NET core

Op .NET core start MSAL.NET de systeem browser als een afzonderlijk proces. MSAL.NET heeft geen controle over deze browser, maar zodra de verificatie van de gebruiker is voltooid, wordt de webpagina zo omgeleid dat MSAL.NET de URI kan onderscheppen.

U kunt ook apps die zijn geschreven voor .NET Classic configureren voor het gebruik van deze browser door op te geven

```csharp
await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
```

MSAL.NET kan niet detecteren of de gebruiker naar een andere locatie navigeert of dat de browser wordt gesloten. Apps die gebruikmaken van deze techniek worden aanbevolen om een time-out (via `CancellationToken`) te definiëren. We raden u aan een time-out van ten minste enkele minuten in te voeren om rekening te houden met gevallen waarin de gebruiker wordt gevraagd om het wacht woord te wijzigen of multi-factor Authentication uit te voeren.

### <a name="how-to-use-the-default-os-browser"></a>De standaard browser van het besturings systeem gebruiken

MSAL.NET moet Luis teren naar `http://localhost:port` en de code die AAD verzendt, onderscheppen wanneer de verificatie van de gebruiker plaatsvindt (Zie [autorisatie code](v2-oauth2-auth-code-flow.md) voor meer informatie)

De systeem browser inschakelen:

1. Configureer tijdens de registratie van de app `http://localhost` als omleidings-URI (momenteel niet ondersteund door B2C)
2. Wanneer u uw PublicClientApplication bouwt, geeft u deze omleidings-URI op:

```csharp
IPublicClientApplication pca = PublicClientApplicationBuilder
                            .Create("<CLIENT_ID>")
                             // or use a known port if you wish "http://localhost:1234"
                            .WithRedirectUri("http://localhost")  
                            .Build();
```

> [!Note]
> Als u `http://localhost`configureert, wordt intern MSAL.NET een wille keurige open poort gevonden en gebruikt.

### <a name="linux-and-mac"></a>Linux en MAC

In Linux opent MSAL.NET de standaard besturingssysteem browser met het hulp programma xdg-open. Als u problemen wilt oplossen, voert u het hulp programma uit vanaf een Terminal bijvoorbeeld `xdg-open "https://www.bing.com"`  
Op Mac wordt de browser geopend door het aanroepen van `open <url>`

### <a name="customizing-the-experience"></a>De ervaring aanpassen

> [!NOTE]
> Aanpassing is beschikbaar in MSAL.NET 4.1.0 of hoger.

MSAL.NET kan reageren met een HTTP-bericht wanneer een token wordt ontvangen of in geval van een fout. U kunt een HTML-bericht weer geven of omleiden naar een URL van uw keuze:

```csharp
var options = new SystemWebViewOptions() 
{
    HtmlMessageError = "<p> An error occured: {0}. Details {1}</p>",
    BrowserRedirectSuccess = new Uri("https://www.microsoft.com");
}

await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
         .WithSystemWebViewOptions(options)
         .ExecuteAsync();
```

### <a name="opening-a-specific-browser-experimental"></a>Een specifieke browser openen (experimenteel)

U kunt de manier aanpassen waarop MSAL.NET de browser opent. Zo kunt u in plaats van de browser de standaard instelling te gebruiken, een specifieke browser geforceerd openen:

```csharp
var options = new SystemWebViewOptions() 
{
    OpenBrowserAsync = SystemWebViewOptions.OpenWithEdgeBrowserAsync
}
```

### <a name="uwp-doesnt-use-the-system-webview"></a>UWP maakt geen gebruik van de webweergave van het systeem

Voor desktop toepassingen leidt het starten van een webweergave van een systeem naar een subpar gebruikers ervaring, terwijl de gebruiker de browser ziet, waar ze mogelijk al andere tabbladen hebben geopend. En wanneer de verificatie heeft plaatsgevonden, krijgen de gebruikers een pagina om dit venster te sluiten. Als de gebruiker geen aandacht betaalt, kan hij het hele proces sluiten (inclusief andere tabbladen, die geen verband houden met de verificatie). Het gebruik van de systeem browser op bureau blad zou ook lokale poorten moeten openen en kunnen Luis teren. hiervoor zijn mogelijk geavanceerde machtigingen vereist voor de toepassing. Als ontwikkelaar, gebruiker of beheerder kunt u zich niet meer over deze vereiste houden.

## <a name="enable-embedded-webviews-on-ios-and-android"></a>Inge sloten webweergaven inschakelen op iOS en Android

U kunt ook Inge sloten webweergaven inschakelen in Xamarin. iOS en Xamarin. Android-apps. Vanaf MSAL.NET 2.0.0-Preview biedt MSAL.NET ook ondersteuning voor het gebruik van de **Inge sloten** webweergave-optie. Voor ADAL.NET is inge sloten webweergave de enige optie die wordt ondersteund.

Als ontwikkelaar met behulp van de MSAL.NET-Xamarin, kunt u ervoor kiezen om ofwel Inge sloten webweergaven als systeem browsers te gebruiken. Dit is uw keuze, afhankelijk van de gebruikers ervaring en beveiligings problemen die u wilt richten.

Momenteel biedt MSAL.NET nog geen ondersteuning voor de Android-en iOS-brokers. Als u eenmalige aanmelding (SSO) moet bieden, is het mogelijk dat de systeem browser nog steeds een betere optie is. Het ondersteunen van brokers met de Inge sloten webbrowser vindt u in de achterstand van MSAL.NET.

### <a name="differences-between-embedded-webview-and-system-browser"></a>Verschillen tussen de Inge sloten webweergave en de systeem browser
Er zijn enkele visuele verschillen tussen de Inge sloten webweergave en de systeem browser in MSAL.NET.

**Interactief aanmelden met MSAL.NET met behulp van de Inge sloten webweergave:**

![geïntegreerde](media/msal-net-web-browsers/embedded-webview.png)

**Interactief aanmelden met MSAL.NET met behulp van de systeem browser:**

![Systeem browser](media/msal-net-web-browsers/system-browser.png)

### <a name="developer-options"></a>Opties voor ontwikkel aars

Als ontwikkelaar met behulp van MSAL.NET hebt u verschillende opties voor het weer geven van het interactieve dialoog venster van STS:

- **Systeem browser.** De systeem browser is standaard ingesteld in de-bibliotheek. Als u Android gebruikt, lees dan [systeem browsers](msal-net-system-browser-android-considerations.md) voor specifieke informatie over welke browsers voor verificatie worden ondersteund. Wanneer u de systeem browser gebruikt in Android, raden we aan dat het apparaat een browser heeft die aangepaste Chrome-tabbladen ondersteunt.  Anders kan de verificatie mislukken.
- **Inge sloten webweergave.** Als u alleen een Inge sloten Webweergave wilt gebruiken in MSAL.NET, bevat de opbouw functie voor de `AcquireTokenInteractively` para meters een `WithUseEmbeddedWebView()` methode.

    iOS

    ```csharp
    AuthenticationResult authResult;
    authResult = app.AcquireTokenInteractively(scopes)
                    .WithUseEmbeddedWebView(useEmbeddedWebview)
                    .ExecuteAsync();
    ```

    Android:

    ```csharp
    authResult = app.AcquireTokenInteractively(scopes)
                .WithParentActivityOrWindow(activity)
                .WithUseEmbeddedWebView(useEmbeddedWebview)
                .ExecuteAsync();
    ```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinios"></a>Kiezen tussen Inge sloten webbrowser of systeem browser op Xamarin. iOS

In de iOS-app kunt u in `AppDelegate.cs` de `ParentWindow` initialiseren `null`. Het wordt niet gebruikt in iOS

```csharp
App.ParentWindow = null; // no UI parent on iOS
```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid"></a>Kiezen tussen Inge sloten webbrowser of systeem browser op Xamarin. Android

In uw Android-app kunt u in `MainActivity.cs` de bovenliggende activiteit instellen, zodat het verificatie resultaat weer wordt hersteld:

```csharp
 App.ParentWindow = this;
```

In de `MainPage.xaml.cs`:

```csharp
authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(true)
                      .ExecuteAsync();
```

#### <a name="detecting-the-presence-of-custom-tabs-on-xamarinandroid"></a>De aanwezigheid van aangepaste tabbladen op Xamarin. Android detecteren

Als u de systeem webbrowser wilt gebruiken om SSO in te scha kelen met de apps die worden uitgevoerd in de browser, maar wel over de gebruikers ervaring voor Android-apparaten die geen browser met ondersteuning voor aangepaste tabbladen hebben, kunt u kiezen of u de `IsSystemWebViewAvailable()`-methode aanroept in `IPublicClientApplication`. Deze methode retourneert `true` als de PackageManager aangepaste tabbladen en `false` detecteert als deze niet op het apparaat worden gedetecteerd.

Op basis van de waarde die is geretourneerd door deze methode en uw vereisten kunt u een beslissing nemen:

- U kunt een aangepast fout bericht retour neren aan de gebruiker. Bijvoorbeeld: ' Installeer Chrome om door te gaan met verificatie '-of-
- U kunt terugvallen op de Inge sloten optie webweergave en de gebruikers interface starten als een Inge sloten webweergave.

De volgende code toont de Inge sloten optie webweergave:

```csharp
bool useSystemBrowser = app.IsSystemWebviewAvailable();

authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(!useSystemBrowser)
                      .ExecuteAsync();
```

#### <a name="net-core-doesnt-support-interactive-authentication-with-an-embedded-browser"></a>.NET core biedt geen ondersteuning voor interactieve verificatie met een Inge sloten browser

Voor .NET Core is het verkrijgen van tokens interactief, alleen beschikbaar via de systeem webbrowser, niet met Inge sloten webweergaven. .NET core biedt nog geen gebruikers interface.
Als u de Surf ervaring wilt aanpassen met de systeem webbrowser, kunt u de [IWithCustomUI](scenario-desktop-acquire-token.md#withcustomwebui) -interface implementeren en zelfs uw eigen browser opgeven.
