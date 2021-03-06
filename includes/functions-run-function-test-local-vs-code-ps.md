---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/19/2020
ms.author: glenga
ms.openlocfilehash: edf5fc33ec14d41630462cca1a4ace0663473196
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78191014"
---
## <a name="run-the-function-locally"></a>De functie lokaal uitvoeren

Azure Functions Core Tools integreert met Visual Studio code zodat u een Azure Functions-project lokaal kunt uitvoeren en debuggen. Zie [debug Power shell Azure functions Local](../articles/azure-functions/functions-debug-powershell-local.md)(Engelstalig) voor meer informatie over fout opsporing in Visual Studio code. 

1. Druk op F5 om het functie-app-project te starten. De uitvoer van Core Tools wordt weergegeven in het deelvenster **Terminal**.

1. Kopieer het URL-eindpunt van de door HTTP getriggerde functie in het deelvenster **Terminal**.

    ![Lokale Azure-uitvoer](./media/functions-run-function-test-local-vs-code-ps/functions-vscode-f5.png)

1. Voeg de query reeks `?name=<yourname>` toe aan deze URL en gebruik `Invoke-RestMethod` in een tweede Power shell-opdracht prompt om de aanvraag uit te voeren, als volgt:

    ```powershell
    PS > Invoke-RestMethod -Method Get -Uri http://localhost:7071/api/HttpTrigger?name=PowerShell
    Hello PowerShell
    ```

    U kunt ook de GET-aanvraag vanuit een browser uitvoeren via de volgende URL:

    <http://localhost:7071/api/HttpExample?name=PowerShell>

    Wanneer u het http trigger-eind punt aanroept zonder een `name` para meter door te geven als query parameter of in de hoofd tekst, retourneert de functie een `BadRequest` fout. Wanneer u de code in run. ps1 bekijkt, ziet u dat deze fout wordt veroorzaakt door het ontwerp.

1. Informatie over de aanvraag wordt weer gegeven in het deel venster **Terminal** .

    ![Uitvoering van functies in het Terminal paneel](./media/functions-run-function-test-local-vs-code-ps/function-execution-terminal.png)

1. Wanneer u klaar bent, drukt u op **CTRL + C** om de kern hulpprogramma's te stoppen.

Nadat u hebt gecontroleerd of de functie correct wordt uitgevoerd op uw lokale computer, is het tijd om het project te publiceren in Azure.