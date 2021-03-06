---
title: Java script gebruiken om een chat ruimte te maken met Azure Functions en de signaal service
description: Een quickstart waarin u leert hoe u de service Azure SignalR en Azure Functions gebruikt om een chatruimte te maken.
author: sffamily
ms.service: signalr
ms.devlang: javascript
ms.topic: quickstart
ms.date: 12/14/2019
ms.author: zhshang
ms.openlocfilehash: 2726d5da2613be4ae2065246543d206cf814f353
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2020
ms.locfileid: "77083186"
---
# <a name="quickstart-use-javascript-to-create-a-chat-room-with-azure-functions-and-signalr-service"></a>Snelstartgids: Java script gebruiken om een chat ruimte te maken met Azure Functions en de signaal service

Met de Azure signalerings service kunt u eenvoudig real-time functionaliteit toevoegen aan uw toepassing en Azure Functions een serverloos platform is waarmee u uw code kunt uitvoeren zonder een infra structuur te beheren. In deze Quick Start gebruikt u Java script om een serverloze, realtime chat toepassing te maken met behulp van de seingevings service en-functies.

## <a name="prerequisites"></a>Vereisten

- Een code-editor, zoals [Visual Studio code](https://code.visualstudio.com/)
- Een Azure-account met een actief abonnement. [Maak gratis een account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Azure functions core tools](https://github.com/Azure/azure-functions-core-tools#installing), versie 2 of hoger. Wordt gebruikt om Azure function-apps lokaal uit te voeren.
- [Node. js](https://nodejs.org/en/download/), versie 10. x

   > [!NOTE]
   > De voor beelden moeten werken met andere versies van node. js, zie de [documentatie voor Azure functions runtime versies](../azure-functions/functions-versions.md#languages) voor meer informatie.

> [!NOTE]
> Deze quickstart kan worden uitgevoerd op macOS, Windows of Linux.

## <a name="log-in-to-azure"></a>Meld u aan bij Azure.

Meld u met uw Azure-account aan bij Azure Portal op <https://portal.azure.com/>.

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

## <a name="configure-and-run-the-azure-function-app"></a>De Azure-functie-app configureren en uitvoeren

1. Controleer in de browser waarin de Azure-portal is geopend, of het service-exemplaar van SignalR dat u eerder hebt geïmplementeerd, is gemaakt. Hiervoor typt u de naam van het exemplaar in het zoekvak boven in de portal. Selecteer het exemplaar om het te openen.

    ![Het service-exemplaar van SignalR zoeken](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. Selecteer **Sleutels** om de verbindingsreeksen voor het service-exemplaar van SignalR weer te geven.

1. Selecteer en kopieer de primaire verbindingsreeks.

    ![De service SignalR maken](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

1. Open in de code-editor de map *src/chat/java script* in de gekloonde opslag plaats.

1. Wijzig de naam *local.settings.sample.json* in *local.settings.json*.

1. Plak in **local.settings.json** de verbindingsreeks in de waarde van de instelling **AzureSignalRConnectionString**. Sla het bestand op.

1. JavaScript-functies zijn georganiseerd in mappen. In elke map zitten twee bestanden: *function.json* definieert de bindingen die worden gebruikt in de functie, en *index.js* is de hoofdtekst van de functie. Deze functie-app bevat twee met HTTP geactiveerde functies:

    - **negotiate**: gebruikt de invoerbinding *SignalRConnectionInfo* om geldige verbindingsgegevens te genereren en te retourneren.
    - **messages**: ontvangt een chatbericht in de hoofdtekst van de aanvraag en gebruikt de uitvoerbinding *SignalR* om het bericht uit te zenden naar alle verbonden clienttoepassingen.

1. Controleer in de terminal of u zich in de map *src/chat/java script* bevindt. Voer de functie-app uit.

    ```bash
    func start
    ```

    ![De service SignalR maken](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-run-application.png)

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een realtime serverloze toepassing gemaakt en uitgevoerd in VS code. Nu volgt meer informatie over het implementeren van Azure Functions vanuit VS Code.

> [!div class="nextstepaction"]
> [Azure Functions met VS Code implementeren](/azure/javascript/tutorial-vscode-serverless-node-01)
