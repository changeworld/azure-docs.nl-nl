---
title: Daemon-apps registreren die web-Api's aanroepen-micro soft Identity-platform | Azure
description: Meer informatie over het bouwen van een daemon-app die web-Api's aanroept-app-registratie
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 922a484d111746e5073c08a64d7c92e2b6b4a7c4
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773369"
---
# <a name="daemon-app-that-calls-web-apis---app-registration"></a>Daemon-app voor het aanroepen van web-Api's-app-registratie

Voor een daemon-toepassing hebt u het volgende nodig om te weten wanneer u de app registreert.

## <a name="supported-account-types"></a>Ondersteunde accounttypen

Daemon-toepassingen maken alleen indruk in azure AD-tenants. Wanneer u de toepassing maakt, moet u dus een van de volgende opties kiezen:

- **Accounts in deze organisatie-Directory alleen**. Dit is het meest voorkomende keuze omdat daemon-toepassingen meestal worden geschreven door LOB-ontwikkel aars (line-of-Business).
- **Accounts in een organisatorische Directory**. U maakt deze keuze als u een ISV bent die een hulp programma biedt aan uw klanten. U hebt de Tenant beheerders van uw klanten nodig om het goed te keuren.

## <a name="authentication---no-reply-uri-needed"></a>Verificatie-geen antwoord-URI vereist

Als uw vertrouwelijke client toepassing *alleen* de client referenties stroom gebruikt, hoeft de antwoord-URI niet te worden geregistreerd. Het is niet nodig voor de toepassings configuratie of-constructie. De client referenties stroom gebruikt deze niet.

## <a name="api-permissions---app-permissions-and-admin-consent"></a>API-machtigingen-app-machtigingen en beheerders toestemming

Een daemon-toepassing kan alleen toepassings machtigingen voor Api's aanvragen (niet-gedelegeerde machtigingen). Klik op de pagina **API-machtigingen** voor de registratie van de toepassing, nadat u **een machtiging toevoegen** hebt geselecteerd en de API-familie hebt gekozen, kies **toepassings machtigingen**en selecteer vervolgens uw machtigingen.

![App-machtigingen en beheerders toestemming](media/scenario-daemon-app/app-permissions-and-admin-consent.png)

> [!NOTE]
> De Web-API die u wilt aanroepen, moet de *toepassings machtigingen (app-rollen)* definiëren, niet de gedelegeerde machtigingen. Zie voor meer informatie over het beschikbaar maken van een dergelijke API [de beveiligde web-API: app-registratie-wanneer uw web-API wordt aangeroepen door een daemon-app](scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app).

Voor daemon-toepassingen moet een Tenant beheerder vooraf toestemming verlenen aan de toepassing die de Web-API aanroept. Tenant beheerders geven deze toestemming op dezelfde API- **machtigings** pagina door **toestemming van beheerder verlenen aan *onze organisatie***  te selecteren

Als u een ISV-toepassing met meerdere tenants maakt, leest u de sectie [implementatie van multi tenant-apps](scenario-daemon-production.md#deployment---multitenant-daemon-apps).

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Daemon-app-app-code configuratie](./scenario-daemon-app-configuration.md)
