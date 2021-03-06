---
title: Azure Media Services gebeurtenissen controleren met Event Grid met behulp van de portal
description: In dit artikel wordt beschreven hoe u zich abonneert op Event Grid om Azure Media Services-gebeurtenissen te controleren.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: azure media services, stream, uitzenden, live, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 01/21/2020
ms.author: juliako
ms.openlocfilehash: 18503e64dc6f38daab61599153cd0e0fb6fadb20
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76509220"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-portal"></a>Media Services gebeurtenissen met Event Grid maken en bewaken met behulp van de Azure Portal

Azure Event Grid is een gebeurtenisservice voor de cloud. Deze service maakt gebruik van [gebeurtenis abonnementen](../../event-grid/concepts.md#event-subscriptions) om gebeurtenis berichten te routeren naar abonnees. Media Services gebeurtenissen bevatten alle informatie die u nodig hebt om te reageren op wijzigingen in uw gegevens. U kunt een Media Services gebeurtenis identificeren omdat de eigenschap Event type met ' micro soft. media. ' begint. Zie [Media Services-gebeurtenis schema's](media-services-event-schemas.md)voor meer informatie.

In dit artikel gebruikt u de Azure Portal om u te abonneren op gebeurtenissen voor uw Azure Media Services-account. Vervolgens triggert u gebeurtenissen om het resultaat weer te geven. Normaal gesproken verzendt u gebeurtenissen naar een eindpunt dat de gebeurtenisgegevens verwerkt en vervolgens in actie komt. In het artikel sturen we gebeurtenissen naar een web-app waarmee de berichten worden verzameld en weer gegeven.

Wanneer u klaar bent, ziet u dat de gebeurtenisgegevens naar de web-app zijn verzonden.

## <a name="prerequisites"></a>Vereisten 

* Een actief Azure-abonnement hebben.
* Maak een nieuw Media Services-account zoals beschreven in deze [snelstart](create-account-cli-quickstart.md).

## <a name="create-a-message-endpoint"></a>Het eindpunt van een bericht maken

Voordat u zich abonneert op de gebeurtenissen voor het Media Services-account, gaan we het eind punt voor het gebeurtenis bericht maken. Het eindpunt onderneemt normaal gesproken actie op basis van de gebeurtenisgegevens. In dit artikel implementeert u een [vooraf gemaakte web-app](https://github.com/Azure-Samples/azure-event-grid-viewer) waarin de gebeurtenis berichten worden weer gegeven. De geïmplementeerde oplossing omvat een App Service-plan, een App Service-web-app en broncode van GitHub.

1. Selecteer **Implementeren in Azure** om de oplossing voor uw abonnement te implementeren. Geef in Azure Portal waarden op voor de parameters.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

1. De implementatie kan enkele minuten duren. Controleer of uw web-app wordt uitgevoerd nadat de implementatie is voltooid. Navigeer in een webbrowser naar: `https://<your-site-name>.azurewebsites.net`

Als u overschakelt naar de site Azure Event Grid viewer, ziet u dat er nog geen gebeurtenissen zijn.
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-media-services-events"></a>Abonneren op Media Services gebeurtenissen

U abonneert u op een onderwerp om Event Grid te laten weten welke gebeurtenissen u wilt traceren en waar de gebeurtenissen naartoe moeten worden gestuurd.

1. Selecteer uw Media Services-account in de portal en selecteer **gebeurtenissen**.
1. Gebruik een webhook voor het eindpunt als u gebeurtenissen naar uw viewer-app wilt verzenden. 

   ![Webhook selecteren](./media/monitor-events-portal/select-web-hook.png)

1. Het gebeurtenis abonnement is vooraf ingevuld met waarden voor uw Media Services-account. 
1. Selecteer Web hook voor het **type eind punt**.
1. In dit onderwerp laten we het selectie vakje **Abonneren op alle gebeurtenis typen** ingeschakeld. U kunt het echter uitschakelen en filteren op specifieke gebeurtenis typen. 
1. Klik op de koppeling **een eind punt selecteren** .

    Voor het webhookeindpunt geeft u de URL van uw web-app op en voegt u `api/updates` toe aan de URL van de startpagina. 

1. Druk op **Bevestig selectie**.
1. Kies **Maken**.
1. Geef het abonnement een beschrijvende naam.

   ![Logboeken selecteren](./media/monitor-events-portal/create-subscription.png)

1. Bekijk opnieuw uw web-app en u zult zien dat er een validatiegebeurtenis voor een abonnement naartoe is verzonden. 

    Via Event Grid wordt de validatiegebeurtenis verzonden zodat het eindpunt kan controleren of de gebeurtenisgegevens in aanmerking komen om ontvangen te worden. Het eind punt moet `validationResponse` instellen op `validationCode`. Zie voor meer informatie, [Event Grid-beveiliging en verificatie](../../event-grid/security-authentication.md). U kunt de code van de web-app bekijken om te zien hoe het abonnement wordt gevalideerd.

Nu gaan we gebeurtenissen activeren om te zien hoe Event Grid het bericht distribueert naar uw eind punt.

## <a name="send-an-event-to-your-endpoint"></a>Een gebeurtenis verzenden naar het eindpunt

U kunt gebeurtenissen voor het Media Services-account activeren door een coderings taak uit te voeren. U kunt [deze Snelstartgids](stream-files-dotnet-quickstart.md) volgen om een bestand te coderen en te beginnen met het verzenden van gebeurtenissen. Als u zich hebt geabonneerd op alle gebeurtenissen, ziet u een scherm dat vergelijkbaar is met het volgende:

> [!TIP]
> Selecteer het oogpictogram om de gebeurtenisgegevens uit te breiden. Vernieuw de pagina niet als u alle gebeurtenissen wilt weer geven.

![Een abonnementgebeurtenis weergeven](./media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>Volgende stappen

[Uploaden, coderen en streamen](stream-files-tutorial-with-api.md)
