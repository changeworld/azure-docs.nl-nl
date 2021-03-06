---
title: Reageren op gebeurtenissen van de Azure signalerings service
description: Gebruik Azure Event Grid om u te abonneren op gebeurtenissen van de Azure signalerings service. Andere downstream-Services kunnen worden geactiveerd door deze gebeurtenissen.
services: azure-signalr,event-grid
author: chenyl
ms.author: chenyl
ms.reviewer: zhshang
ms.date: 11/13/2019
ms.topic: conceptual
ms.service: signalr
ms.openlocfilehash: a8e25907b40b910f2b91884d355b6ac85eeaa250
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158191"
---
# <a name="reacting-to-azure-signalr-service-events"></a>Reageren op gebeurtenissen van de Azure signalerings service

Met de service gebeurtenissen van Azure signalering kunnen toepassingen reageren op client verbindingen die zijn verbonden of losgekoppeld met moderne serverloze architecturen. Dit gebeurt zonder dat er complexe code of dure en inefficiënte polling services nodig zijn.  In plaats daarvan worden gebeurtenissen gepusht via [Azure Event grid](https://azure.microsoft.com/services/event-grid/) naar abonnees zoals [Azure functions](https://azure.microsoft.com/services/functions/), [Azure Logic apps](https://azure.microsoft.com/services/logic-apps/)of zelfs naar uw eigen aangepaste HTTP-listener en betaalt u alleen voor wat u gebruikt.

Gebeurtenissen van de Azure signalerings service worden betrouwbaar verzonden naar de Event Grid-Service. Dit biedt betrouw bare leverings Services voor uw toepassingen via een uitgebreid beleid voor opnieuw proberen en levering van onbestelbare berichten. Zie [Event grid aflevering van berichten en probeer het opnieuw](https://docs.microsoft.com/azure/event-grid/delivery-and-retry).

![Event Grid model](https://docs.microsoft.com/azure/event-grid/media/overview/functional-model.png)

## <a name="serverless-state"></a>Serverloze status
Gebeurtenissen van de Azure signalerings service zijn alleen actief wanneer client verbindingen de status serverloos hebben. In het algemeen is het zo dat als een client niet wordt doorgestuurd naar een hub-server, de status van de serverloze wordt bereikt. De klassieke modus werkt alleen wanneer de hub, waarmee client verbindingen verbinding maken, geen hub-server heeft. Serverloze modus wordt echter aanbevolen om een probleem te voor komen. Zie [Service modus kiezen](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose)voor meer informatie over de service modus.

## <a name="available-azure-signalr-service-events"></a>Beschik bare gebeurtenissen van de Azure signalerings service
Event grid gebruikt [gebeurtenis abonnementen](../event-grid/concepts.md#event-subscriptions) om gebeurtenis berichten te routeren naar abonnees. Gebeurtenis abonnementen van de Azure signalerings service ondersteunen twee typen gebeurtenissen:  

|Gebeurtenis naam|Beschrijving|
|----------|-----------|
|`Microsoft.SignalRService.ClientConnectionConnected`|Deze gebeurtenis treedt op wanneer een verbinding met een client tot stand is gebracht.|
|`Microsoft.SignalRService.ClientConnectionDisconnected`|Deze gebeurtenis treedt op wanneer de verbinding van een client verbinding wordt verbroken.|

## <a name="event-schema"></a>Gebeurtenisschema
De gebeurtenissen van de Azure signalerings service bevatten alle informatie die u nodig hebt om te reageren op de wijzigingen in uw gegevens. U kunt een Azure signalerings service-gebeurtenis identificeren met de eigenschap Event type begint met ' micro soft. SignalRService '. Meer informatie over het gebruik van Event Grid gebeurtenis eigenschappen wordt beschreven in [Event grid-gebeurtenis schema](../event-grid/event-schema.md).  

Hier volgt een voor beeld van een gebeurtenis die verbonden is met de client verbinding:
```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "eventType": "Microsoft.SignalRService.ClientConnectionConnected",
  "eventTime": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Zie voor meer informatie [schema voor seingevings service-gebeurtenissen](../event-grid/event-schema-azure-signalr.md).

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Event Grid en het geven van Azure signalerings service-gebeurtenissen een try:

> [!div class="nextstepaction"]
> [Probeer een voor beeld Event grid integratie met de Azure signalerings Service](./signalr-howto-event-grid-integration.md)
> [over Event grid](../event-grid/overview.md)
