---
title: Ontwikkelaars accounts met behulp van Azure Active Directory autoriseren
titleSuffix: Azure API Management
description: Meer informatie over het autoriseren van gebruikers met behulp van Azure Active Directory in API Management.
services: api-management
documentationcenter: API Management
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: df640f11e8a0e8af22c96a662a602e0de508715c
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76985047"
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Ontwikkel ontwikkelaars accounts met behulp van Azure Active Directory in azure API Management

Dit artikel laat u zien hoe u toegang tot de ontwikkelaars Portal kunt inschakelen voor gebruikers van Azure Active Directory (Azure AD). In deze hand leiding wordt ook beschreven hoe u groepen van Azure AD-gebruikers beheert door externe groepen toe te voegen die de gebruikers bevatten.

## <a name="prerequisites"></a>Vereisten

- Lees de volgende snelstartgids: [Een Azure API Management-exemplaar maken](get-started-create-service-instance.md).
- Importeer en publiceer een Azure API Management-exemplaar. Zie [importeren en publiceren](import-and-publish.md)voor meer informatie.

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Ontwikkel ontwikkelaars accounts met behulp van Azure AD

1. Meld u aan bij de [Azure Portal](https://portal.azure.com). 
2. Selecteer ![pijl](./media/api-management-howto-aad/arrow.png).
3. Typ **API** in het zoekvak.
4. Selecteer **API Management Services**.
5. Selecteer uw service-exemplaar van API Management.
6. Onder **beveiliging**selecteert u **identiteiten**.
7. Selecteer **+ toevoegen** aan de bovenkant.

    Het deel venster **ID-provider toevoegen** wordt aan de rechter kant weer gegeven.
8. Selecteer **Azure Active Directory**onder **provider type**.

    Besturings elementen waarmee u andere benodigde informatie kunt invoeren, worden weer gegeven in het deel venster. De besturings elementen omvatten **client-id** en **client geheim**. (U krijgt verderop in het artikel informatie over deze besturings elementen.)
9. Noteer de inhoud van de **omleidings-URL**.
    
   ![Stappen voor het toevoegen van een id-provider in de Azure Portal](./media/api-management-howto-aad/api-management-with-aad001.png)  
10. Open een ander tabblad in uw browser. 
11. Ga naar de [Azure Portal-app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) om een App in Active Directory te registreren.
12. Selecteer onder **beheren**de optie **app-registraties**.
13. Selecteer **nieuwe registratie**. Stel op de pagina **een toepassing registreren** de volgende waarden in:
    
    * Stel **name** in op een duidelijke naam. e.g., *developer-portal*
    * Stel **ondersteunde account typen** **in op accounts in deze directory van de organisatie**. 
    * Stel de **omleidings-URI** in op de waarde die u in stap 9 hebt ontvangen. 
    * Kies **registreren**. 

14.  Nadat de toepassing is geregistreerd, kopieert u de **toepassings-id (client)** van de pagina **overzicht** . 
15. Ga terug naar uw API Management-exemplaar. Plak in het venster **ID-provider toevoegen** de waarde van de **toepassing (client)** in het vak **client-id** .
16. Ga terug naar de Azure AD-configuratie en selecteer **certificaten & geheimen** onder **beheren**. Selecteer de knop **Nieuw client geheim** . Voer een waarde in het veld **Beschrijving**in, selecteer een optie voor **verloopt** en kies **toevoegen**. Kopieer de waarde van het client geheim voordat u de pagina verlaat. U hebt deze nodig in de volgende stap. 
17. Selecteer onder **beheren**de optie **verificatie** en selecteer **id-tokens** onder **impliciete toekenning**
18. Ga terug naar uw API Management-exemplaar en plak het geheim in het vak **client Secret** .

    > [!IMPORTANT]
    > Zorg ervoor dat u het **client geheim** bijwerkt voordat de sleutel verloopt. 
    >  
    >

19. Het venster **ID-provider toevoegen** bevat ook het tekstvak **toegestane tenants** . Hier geeft u de domeinen van de Azure AD-exemplaren op waaraan u toegang wilt verlenen tot de Api's van het API Management service-exemplaar. U kunt meerdere domeinen van elkaar scheiden met nieuwe voor-en spaties of komma's.

    > [!NOTE]
    > U kunt meerdere domeinen opgeven in de sectie **toegestane tenants** . Voordat een gebruiker zich kan aanmelden vanuit een ander domein dan het oorspronkelijke domein waarin de toepassing is geregistreerd, moet een globale beheerder van het andere domein toestemming verlenen voor toegang tot Directory gegevens van de toepassing. De globale beheerder moet het volgende doen om toestemming te verlenen: a. Ga naar `https://<URL of your developer portal>/aadadminconsent` (bijvoorbeeld https://contoso.portal.azure-api.net/aadadminconsent).
    > b. Typ de domein naam van de Azure AD-Tenant waarmee u toegang wilt verlenen.
    > c. Selecteer **Indienen**. 

20.  Nadat u de gewenste configuratie hebt opgegeven, selecteert u **toevoegen**.

Nadat de wijzigingen zijn opgeslagen, kunnen gebruikers in de opgegeven Azure AD-instantie zich aanmelden bij de ontwikkelaars portal door de stappen te volgen in [Aanmelden bij de ontwikkelaars Portal met behulp van een Azure ad-account](#log_in_to_dev_portal).

## <a name="add-an-external-azure-ad-group"></a>Een externe Azure AD-groep toevoegen

Nadat u toegang hebt ingeschakeld voor gebruikers in een Azure AD-Tenant, kunt u Azure AD-groepen toevoegen aan API Management. Hierdoor kunt u de product zichtbaarheid beheren met Azure AD-groepen.

Als u een externe Azure AD-groep wilt toevoegen aan APIM, moet u eerst de vorige sectie volt ooien. Daarnaast moet de toepassing die u hebt geregistreerd, toegang krijgen tot de Azure Active Directory Graph API met `Directory.ReadAll` machtiging door de volgende stappen te volgen: 

1. Ga terug naar de registratie van uw app die in de vorige sectie is gemaakt
2. Klik op het tabblad **API-machtigingen** en vervolgens op **+ een machtigings knop toevoegen** 
3. Selecteer in het deel venster **API-machtigingen aanvragen** het tabblad **micro soft api's** en schuif naar beneden om de tegel **Azure Active Directory grafiek** te vinden in de sectie Ondersteunde verouderde api's en klik erop. Klik vervolgens op de knop **toepassings machtigingen** en selecteer **Directory. ReadAll** -machtiging en voeg vervolgens die machtiging toe met behulp van de knop aan de onderkant. 
4. Klik op de **beheerder toestemming geven voor {tenantnaam}** zodat u toegang verleent aan alle gebruikers in deze map. 

U kunt nu externe Azure AD-groepen toevoegen via het tabblad **groepen** van uw API Management-exemplaar.

1. Selecteer de tab **Groepen**.
2. Selecteer de knop **Aad-groep toevoegen** .
    ![knop ' AAD-groep toevoegen '](./media/api-management-howto-aad/api-management-with-aad008.png)
3. Selecteer de groep die u wilt toevoegen.
4. Druk op de knop **selecteren** .

Nadat u een externe Azure AD-groep hebt toegevoegd, kunt u de eigenschappen ervan bekijken en configureren. Selecteer de naam van de groep op het tabblad **groepen** . Hier kunt u de **naam** en **Beschrijving** voor de groep bewerken.
 
Gebruikers van het geconfigureerde Azure AD-exemplaar kunnen zich nu aanmelden bij de ontwikkelaars Portal. Ze kunnen weer geven en zich abonneren op groepen waarvoor ze zicht baarheid hebben.

## <a name="a-idlog_in_to_dev_portal-developer-portal---add-azure-ad-account-authentication"></a><a id="log_in_to_dev_portal"/> ontwikkelaars Portal-Azure AD-account verificatie toevoegen

In de ontwikkelaars Portal kunt u zich aanmelden met AAD met de **aanmeldings knop: OAuth-** widget. De widget is al opgenomen op de aanmeldings pagina van de standaard inhoud van de ontwikkelaars Portal.

Hoewel een nieuw account automatisch wordt gemaakt wanneer een nieuwe gebruiker zich aanmeldt met AAD, kunt u overwegen om dezelfde widget toe te voegen aan de registratie pagina.

Het **registratie formulier: OAuth** -widget vertegenwoordigt een formulier dat wordt gebruikt om u aan te melden bij OAuth.

> [!IMPORTANT]
> U moet [de portal opnieuw publiceren](api-management-howto-developer-portal-customize.md#publish) om de Aad-wijzigingen van kracht te laten worden.

## <a name="legacy-developer-portal---how-to-sign-in-with-azure-ad"></a>Verouderde ontwikkelaars Portal-aanmelden met Azure AD

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

Meld u aan bij de ontwikkelaars Portal met behulp van een Azure AD-account dat u in de vorige secties hebt geconfigureerd:

1. Open een nieuw browser venster met behulp van de aanmeldings-URL van de Active Directory toepassings configuratie en selecteer **Azure Active Directory**.

   ![Aanmeldings pagina][api-management-dev-portal-signin]

1. Voer de referenties in van een van de gebruikers in azure AD en selecteer **Aanmelden**.

   ![Aanmelden met gebruikers naam en wacht woord][api-management-aad-signin]

1. U wordt mogelijk gevraagd om een registratie formulier als er aanvullende informatie vereist is. Vul het registratie formulier in en selecteer **Aanmelden**.

   ![De knop registreren op het registratie formulier][api-management-complete-registration]

Uw gebruiker is nu aangemeld bij de ontwikkelaars portal voor uw API Management service-exemplaar.

![Ontwikkelaars Portal na registratie is voltooid][api-management-registration-complete]

[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accessing the Graph API]: https://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Sign in to the developer portal by using an Azure AD account]: #Sign-in-to-the-developer-portal-by-using-an-Azure-AD-account
