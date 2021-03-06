---
title: Verbinding maken met Box
description: Taken en werk stromen automatiseren waarmee u bestanden in box maakt en beheert met behulp van Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: c7f97ff33742eb545cbfbd7521ba135584851e5e
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666768"
---
# <a name="create-and-manage-files-in-box-by-using-azure-logic-apps"></a>Bestanden in box maken en beheren met behulp van Azure Logic Apps

In dit artikel wordt beschreven hoe u uw bestanden kunt maken en beheren in box vanuit een logische app met de box-connector. Op die manier kunt u logische apps maken voor het automatiseren van taken en werk stromen voor het beheren van uw bestanden en andere acties, bijvoorbeeld:

* Bouw uw bedrijfs stroom op basis van de gegevens die u uit Box haalt.

* Activeer geautomatiseerde taken en werk stromen wanneer een bestand wordt gemaakt of bijgewerkt.

* Een actie uitvoeren waarmee een bestand wordt gekopieerd of een bestand wordt verwijderd.

  Wanneer deze acties een reactie krijgen, maken ze de uitvoer beschikbaar voor andere acties. 
  Wanneer bijvoorbeeld een bestand wordt gewijzigd in box, kunt u dit bestand in een e-mail bericht verzenden met behulp van Office 365.

## <a name="prerequisites"></a>Vereisten

* Een [Box-account](https://www.box.com/home)

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/). 

* De logische app waartoe u toegang wilt krijgen tot uw Box-account. Als u uw logische app wilt starten met een box-trigger, hebt u een [lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)nodig.

* Basis kennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps](../logic-apps/logic-apps-overview.md).

## <a name="connector-reference"></a>Connector-verwijzing

Zie de [referentie pagina van de connector](/connectors/box/)voor technische details, zoals triggers, acties en limieten, zoals beschreven in het OpenAPI (voorheen Swagger)-bestand van de connector.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic apps-connectors](../connectors/apis-list.md)