---
title: Aan de slag met Azure AD-verificatie met behulp van de Azure Portal | Microsoft Docs
description: Meer informatie over het gebruik van de Azure Portal om toegang te krijgen tot Azure Active Directory-verificatie (Azure AD) om de Azure Media Services API te gebruiken.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: ee04fa7120f5510d703d72e662036f4fe952cd66
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78330665"
---
# <a name="get-started-with-azure-ad-authentication-by-using-the-azure-portal"></a>Aan de slag met Azure AD-verificatie met behulp van de Azure Portal

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. <br/>Maak kennis met de nieuwste versie, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zie ook [migratie richtlijnen van v2 naar v3](../latest/migrate-from-v2-to-v3.md)

Meer informatie over het gebruik van de Azure Portal om toegang te krijgen tot Azure Active Directory-verificatie (Azure AD) voor toegang tot de Azure Media Services API.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account. Als u geen account hebt, kunt u beginnen met een [gratis proef versie van Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Een Media Services-account. Zie [een Azure Media Services-account maken met behulp van de Azure Portal](media-services-portal-create-account.md)voor meer informatie.

Wanneer u Azure AD-verificatie met Azure Media Services gebruikt, hebt u twee verificatie opties:

- **Service-Principal-verificatie**. Verifieer een service. Toepassingen die meestal gebruikmaken van deze verificatie methode zijn apps die daemon services, middelste laag Services of geplande taken uitvoeren: Web apps, functie-apps, Logic apps, Api's of een micro service.
- **Gebruikers verificatie**. Verifieer een persoon die de app gebruikt om te communiceren met Media Services-resources. De interactieve toepassing moet eerst de gebruiker vragen om referenties. Een voor beeld is een beheer console-app die door geautoriseerde gebruikers wordt gebruikt voor het bewaken van coderings taken of live streamen. 

## <a name="access-the-media-services-api"></a>Toegang krijgt tot de Media Services API

Op deze pagina kunt u de verificatie methode selecteren die u wilt gebruiken om verbinding te maken met de API. De pagina bevat ook de waarden die u nodig hebt om verbinding te maken met de API.

1. Selecteer uw Media Services account in de [Azure Portal](https://portal.azure.com/).
2. Selecteer hoe u verbinding wilt maken met de Media Services-API.
3. Selecteer onder **verbinding maken met Media Services-API**de versie van de Media Services-API waarmee u verbinding wilt maken.

## <a name="service-principal-authentication--recommended"></a>Service-Principal-verificatie (aanbevolen)

Verifieert een service met een Azure Active Directory-app (Azure AD) en een geheim. Dit wordt aanbevolen voor alle services in de middelste laag die de Media Services-API aanroepen. Voor beelden zijn Web Apps, functions, Logic Apps, Api's en micro Services. Dit is de aanbevolen verificatie methode.

### <a name="manage-your-azure-ad-app-and-secret"></a>Uw Azure AD-app en-geheim beheren

Met de sectie **uw Aad-app beheren en geheime** kunt u een nieuwe Azure AD-app selecteren of maken en een geheim genereren. Uit veiligheids overwegingen kan het geheim niet worden weer gegeven nadat de Blade is gesloten. De toepassing maakt gebruik van de toepassings-ID en het geheim voor verificatie om een geldig token voor Media Services te verkrijgen.

Zorg ervoor dat u voldoende machtigingen hebt om een toepassing te registreren bij uw Azure AD-Tenant en de toepassing toe te wijzen aan een rol in uw Azure-abonnement. Zie [vereiste machtigingen](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions)voor meer informatie.

### <a name="connect-to-media-services-api"></a>Verbinding maken met de Media Services-API

De **API Connect to Media Services** biedt u waarden die u gebruikt om verbinding te maken met uw Service Principal-toepassing. U kunt tekst waarden ophalen of de JSON-of XML-blokken kopiëren.

## <a name="user-authentication"></a>Gebruikersverificatie

Deze optie kan worden gebruikt voor het verifiëren van een werk nemer of lid van een Azure Active Directory die een app gebruikt om te communiceren met Media Services resources. De interactieve toepassing moet eerst de gebruiker vragen naar de referenties van de gebruiker. Deze verificatie methode mag alleen worden gebruikt voor beheer toepassingen.

### <a name="connect-to-media-services-api"></a>Verbinding maken met de Media Services-API

Kopieer uw referenties om uw gebruikers toepassing te verbinden via de sectie **verbinding maken met Media Services-API** . U kunt tekst waarden ophalen of de JSON-of XML-blokken kopiëren.

## <a name="next-steps"></a>Volgende stappen

Ga aan de slag met [het uploaden van bestanden naar uw account](media-services-portal-upload-files.md).
