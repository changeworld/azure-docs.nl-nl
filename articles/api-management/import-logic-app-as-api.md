---
title: Een logische app als API importeren met Azure Portal | Microsoft Azure Docs
description: Deze zelfstudie laat u zien hoe u API Management (APIM) moet gebruiken om handmatig een logische app als API te importeren.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 08/01/2019
ms.author: apimpm
ms.openlocfilehash: 4077187fe04e3be914a6f7fba84c03df1b79d06a
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74108391"
---
# <a name="import-a-logic-app-as-an-api"></a>Een logische app als API importeren

In dit artikel wordt uitgelegd hoe u een logische app als een API kunt importeren en de geïmporteerde API kunt testen.

In dit artikel leert u het volgende:

> [!div class="checklist"]
>
> -   Een logische app als API importeren
> -   De API testen in Azure Portal
> -   De API testen in de ontwikkelaarsportal

## <a name="prerequisites"></a>Vereisten

-   Lees de volgende snelstart: [Een Azure API Management-exemplaar maken](get-started-create-service-instance.md)
-   Zorg ervoor dat er een logische app in uw abonnement aanwezig is waarmee een HTTP-eindpunt zichtbaar wordt gemaakt. Raadpleeg [Werkstromen met HTTP-eindpunten activeren](../logic-apps/logic-apps-http-endpoint.md) voor meer informatie

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Een back-end-API importeren en publiceren

1. Selecteer **API's** bij **API MANAGEMENT**.
2. Selecteer **logische app** uit de lijst **Nieuwe API toevoegen**.

    ![Logische apps](./media/import-logic-app-as-api/logic-app-api.png)

3. Klik op **Bladeren** om de lijst met Logic apps met de http-trigger in uw abonnement weer te geven. (Houd er rekening mee dat Logic Apps zonder HTTP-trigger niet wordt weer gegeven in de lijst.)
4. Selecteer de app. API Management vindt de Swagger die is gekoppeld aan de geselecteerde app, haalt deze op en importeert deze.
5. Voeg een achtervoegsel toe van de URL voor de API. Het achtervoegsel is een naam die deze specifieke API in dit API Management-exemplaar identificeert. Deze moet uniek zijn in dit API Management-exemplaar.
6. Publiceer de API door deze aan een product te koppelen. In dit geval wordt het product _onbeperkt_ gebruikt. Als u wilt dat de API wordt gepubliceerd en beschikbaar is voor ontwikkelaars, kunt u deze toevoegen aan een product. U kunt dit doen tijdens het maken van de API of het later instellen.

    Producten zijn koppelingen van een of meer API's. U kunt een aantal API's opnemen en deze beschikbaar stellen voor ontwikkelaars via de ontwikkelaarsportal. Ontwikkelaars moeten zich eerst abonneren op een product om toegang tot de API te krijgen. Wanneer ontwikkelaars zich abonneren, ontvangen ze een abonnementssleutel die toegang biedt tot elke API in het betreffende product. Als u de API Management instantie hebt gemaakt, bent u al een beheerder en bent u standaard geabonneerd op elk product.

    Standaard wordt elk API Management-exemplaar geleverd met twee voorbeeldproducten:

    - **Starter**
    - **Onbeperkt**

7. Selecteer **Maken**.

## <a name="test-the-api-in-the-azure-portal"></a>De API testen in Azure Portal

Bewerkingen kunnen rechtstreeks vanuit Azure Portal worden aangeroepen. Dit is een handige manier om de bewerkingen van een API te bekijken en te testen.

1. Selecteer de API die u in de vorige stap hebt gemaakt.
2. Druk op het tabblad **Testen**.
3. Selecteer een willekeurige bewerking.

    De pagina geeft velden weer voor queryparameters en velden voor de headers. Een van de headers is Ocp-Apim-Subscription-Key voor de abonnementssleutel van het product dat is gekoppeld aan deze API. Als u het API Management-exemplaar hebt gemaakt, bent u al een beheerder en wordt de sleutel dus automatisch ingevuld.

4. Druk op **Verzenden**.

    Back-end reageert met **200 OK** en enkele gegevens.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

>[!NOTE]
>Elke logische app heeft functionaliteit voor **handmatig aanroepen**. Als u wilt dat uw API uit meerdere logische apps bestaat om geen conflicten te hebben, moet u de naam van de functie veranderen.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
>
> [Een gepubliceerde API transformeren en beveiligen](transform-api.md)
