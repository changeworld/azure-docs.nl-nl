---
title: Wijzigingen in SameSite-cookies afhandelen in de Chrome-browser | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het verwerken van wijzigingen in SameSite-cookies in de Chrome-browser.
services: active-directory
documentationcenter: ''
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: jmprieur
ms.reviewer: kkrishna
ms.custom: aaddev
ms.openlocfilehash: 8fc1fab89a89fbf7e20414f292a1b02f77ac7907
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76776363"
---
# <a name="handle-samesite-cookie-changes-in-chrome-browser"></a>SameSite cookie wijzigingen in Chrome browser verwerken

## <a name="what-is-samesite"></a>Wat is SameSite?

`SameSite` is een eigenschap die kan worden ingesteld in HTTP-cookies om CSRF-aanvallen (cross site request vervalsing) in webtoepassingen te voor komen:

- Als `SameSite` is ingesteld op **slordig**, wordt de cookie verzonden in aanvragen binnen dezelfde site en in Get-aanvragen van andere sites. Het wordt niet verzonden in GET-aanvragen die meerdere domeinen zijn.
- Een waarde van **strikt** zorgt ervoor dat de cookie alleen binnen dezelfde site in aanvragen wordt verzonden.

De `SameSite` waarde is standaard niet ingesteld in browsers en daarom zijn er geen beperkingen voor het verzenden van cookies in aanvragen. Een toepassing moet zich bij de CSRF-beveiliging aanmelden door het instellen van **slordig** of **strikt** volgens hun vereisten.

## <a name="samesite-changes-and-impact-on-authentication"></a>Wijzigingen in SameSite en invloed op verificatie

Recente [updates van de standaarden op SameSite](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00) stellen het beveiligen van apps voor, door het standaard gedrag van `SameSite` te maken wanneer er geen waarde is ingesteld op slordig. Deze beperking betekent dat cookies worden beperkt voor HTTP-aanvragen, behalve het ophalen van andere sites. Daarnaast wordt er geen waarde van **geen** geïntroduceerd om beperkingen te verwijderen voor het verzenden van cookies. Deze updates worden binnenkort uitgebracht in een toekomstige versie van de Chrome-browser.

Wanneer Web-apps met het micro soft-identiteits platform worden geverifieerd met de antwoord modus ' form_post ', reageert de aanmeldings server op de toepassing met behulp van een HTTP POST om de tokens of de verificatie code te verzenden. Omdat deze aanvraag een aanvraag voor meerdere domeinen is (van `login.microsoftonline.com` naar uw domein-voor exemplaar https://contoso.com/auth), vallen cookies die door uw app zijn ingesteld nu onder de nieuwe regels in Chrome. De cookies die moeten worden gebruikt in scenario's met meerdere sites zijn cookies die de *status* en *nonce* -waarden bevatten, die ook in de aanmeldings aanvraag worden verzonden. Er zijn andere cookies verwijderd door Azure AD om de sessie te bewaren.

Als u uw web-apps niet bijwerkt, zal dit nieuwe gedrag leiden tot mislukte verificaties.

## <a name="mitigation-and-samples"></a>Risico beperking en voor beelden

Om de verificatie fouten op te lossen, kunnen Web-apps die worden geverifieerd met het micro soft Identity-platform de eigenschap `SameSite` instellen op `None` voor cookies die worden gebruikt in scenario's met meerdere domeinen wanneer ze worden uitgevoerd in de Chrome-browser.
Andere browsers (Zie [hier](https://www.chromium.org/updates/same-site/incompatible-clients) voor een volledige lijst) Volg het vorige gedrag van `SameSite` en de cookies worden niet meegenomen als `SameSite=None` is ingesteld.
Daarom moet de `SameSite`-`None` waarde alleen op Chrome worden ingesteld om te ondersteunen voor verificatie op meerdere web-apps, en laat de waarde leeg in andere browsers.

Deze benadering wordt uitgelegd in onze code voorbeelden hieronder.

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

In de onderstaande tabel ziet u de pull-aanvragen die hebben gewerkt aan de SameSite-wijzigingen in onze ASP.NET en ASP.NET Core-voor beelden.

| Voorbeeld | Pull-aanvraag |
| ------ | ------------ |
|  [Incrementele zelf studie voor de web-app ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)  |  [Dezelfde site cookie oplossen #261](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/pull/261)  |
|  [Voor beeld van ASP.NET MVC-Web-app](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)  |  [Dezelfde site cookie oplossen #35](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/pull/35)  |
|  [Active-Directory-DotNet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2)  |  [Dezelfde site cookie oplossen #28](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2/pull/28)  |

Zie ook voor meer informatie over het afhandelen van SameSite-cookies in ASP.NET en ASP.NET Core:

- [Werken met SameSite-cookies in ASP.net core](https://docs.microsoft.com/aspnet/core/security/samesite) .
- [ASP.NET-blog op SameSite-probleem](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/)

# <a name="pythontabpython"></a>[Python](#tab/python)

| Voorbeeld |
| ------ |
|  [MS-Identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp)  |

# <a name="javatabjava"></a>[Java](#tab/java)

| Voorbeeld | Pull-aanvraag |
| ------ | ------------ |
|  [MS-Identity-Java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp)  | [Dezelfde site cookie oplossen #24](https://github.com/Azure-Samples/ms-identity-java-webapp/pull/24)
|  [MS-Identity-Java-webapi](https://github.com/Azure-Samples/ms-identity-java-webapi)  | [Dezelfde site cookie oplossen #4](https://github.com/Azure-Samples/ms-identity-java-webapi/pull/4)

---

## <a name="next-steps"></a>Volgende stappen

Meer informatie over SameSite en het scenario voor de web-app:

> [!div class="nextstepaction"]
> [Veelgestelde vragen over Google Chrome op SameSite](https://www.chromium.org/updates/same-site/faq)

> [!div class="nextstepaction"]
> [Pagina chroom SameSite](https://www.chromium.org/updates/same-site)

> [!div class="nextstepaction"]
> [Scenario: Web-app die gebruikers aantekent](scenario-web-app-sign-user-overview.md)