---
title: Bestanden van apparaten uploaden naar Azure IoT Hub met .NET | Microsoft Docs
description: Bestanden van een apparaat naar de Cloud uploaden met behulp van Azure IoT Device SDK voor .NET. Geüploade bestanden worden opgeslagen in een Azure Storage-BLOB-container.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 07/04/2017
ms.author: robinsh
ms.openlocfilehash: b379f158672a9df3056acb09c63c392869a53283
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/09/2020
ms.locfileid: "77108708"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-net"></a>Bestanden van uw apparaat uploaden naar de Cloud met IoT Hub (.NET)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

In deze zelf studie wordt gebruikgemaakt van de code in de [IOT hub-zelf studie Cloud-naar-apparaat-berichten verzenden](iot-hub-csharp-csharp-c2d.md) om u te laten zien hoe u de mogelijkheden voor het uploaden van bestanden van IOT hub kunt gebruiken. U ziet hoe u het volgende kunt doen:

* Een apparaat veilig voorzien van een Azure Blob-URI voor het uploaden van een bestand.

* Gebruik de IoT Hub bestands upload meldingen om de verwerking van het bestand in de back-end van de app te activeren.

Het [verzenden van telemetrie van een apparaat naar een IOT hub](quickstart-send-telemetry-dotnet.md) Quick Start en het [verzenden van Cloud-naar-apparaat-berichten met IOT hub](iot-hub-csharp-csharp-c2d.md) zelf studie geven de basis functionaliteit van het apparaat-naar-Cloud-en Cloud-naar-apparaat-bericht van IOT hub weer. In de zelf studie [bericht routering configureren met IOT hub](tutorial-routing.md) wordt beschreven hoe u apparaat-naar-Cloud-berichten in Microsoft Azure Blob-opslag betrouwbaar kunt opslaan. In sommige gevallen kunt u de gegevens die uw apparaten verzenden echter niet eenvoudig toewijzen aan de relatief kleine apparaat-naar-Cloud-berichten die IoT Hub accepteren. Bijvoorbeeld:

* Grote bestanden die installatie kopieën bevatten

* Video's

* Bemonsterde trillings gegevens met hoge frequentie

* Een vorm van voorverwerkte gegevens

Deze bestanden worden meestal batch verwerkt in de Cloud met behulp van hulpprogram ma's als [Azure Data Factory](../data-factory/introduction.md) of de [Hadoop](../hdinsight/index.yml) -stack. Wanneer u bestanden van een apparaat wilt uploaden, kunt u nog steeds gebruikmaken van de beveiliging en betrouw baarheid van IoT Hub.

Aan het einde van deze zelf studie voert u twee .NET-console-apps uit:

* **SimulatedDevice**. Met deze app wordt een bestand geüpload naar Storage met behulp van een SAS-URI van uw IoT-hub. Het is een gewijzigde versie van de app die is gemaakt in het [verzenden van Cloud-naar-apparaat-berichten met IOT hub](iot-hub-csharp-csharp-c2d.md) zelf studie.

* **ReadFileUploadNotification**. Deze app ontvangt meldingen over het uploaden van bestanden van uw IoT-hub.

> [!NOTE]
> IoT Hub ondersteunt veel platformen en talen, waaronder C, Java, python en Java script, via een Azure IoT-apparaat-Sdk's. Raadpleeg het [Azure IOT-ontwikkelaars centrum](https://azure.microsoft.com/develop/iot) voor stapsgewijze instructies voor het verbinden van uw apparaat met Azure IOT hub.

## <a name="prerequisites"></a>Vereisten

* Visual Studio

* Een actief Azure-account. Als u geen account hebt, kunt u in slechts een paar minuten een [gratis account](https://azure.microsoft.com/pricing/free-trial/) maken.

* Zorg ervoor dat poort 8883 is geopend in uw firewall. Het voor beeld van het apparaat in dit artikel maakt gebruik van het MQTT-protocol, dat communiceert via poort 8883. Deze poort kan worden geblokkeerd in sommige bedrijfs-en educatieve netwerk omgevingen. Zie [verbinding maken met IOT hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)voor meer informatie en manieren om dit probleem te omzeilen.

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Een bestand uploaden vanuit een apparaat-app

In deze sectie wijzigt u de apparaat-app die u hebt gemaakt in [Cloud-naar-apparaat-berichten verzenden met IOT hub](iot-hub-csharp-csharp-c2d.md) om Cloud-naar-apparaat-berichten van de IOT-hub te ontvangen.

1. Klik in Visual Studio Solution Explorer met de rechter muisknop op het project **SimulatedDevice** en selecteer > **bestaand item** **toevoegen** . Zoek een afbeeldings bestand en voeg dit toe aan uw project. In deze zelf studie wordt ervan uitgegaan dat de afbeelding de naam `image.jpg`heeft.

1. Klik met de rechter muisknop op de afbeelding en selecteer vervolgens **Eigenschappen**. Zorg ervoor dat de **map kopiëren naar uitvoermap** is ingesteld op **kopiëren altijd**.

    ![Weer geven waar de afbeeldings eigenschap moet worden bijgewerkt voor de map kopiëren naar uitvoermap](./media/iot-hub-csharp-csharp-file-upload/image-properties.png)

1. Voeg in het bestand **Program.cs** de volgende instructies toe boven aan het bestand:

    ```csharp
    using System.IO;
    ```

1. Voeg de volgende methode toe aan de klasse **Program**:

    ```csharp
    private static async void SendToBlobAsync()
    {
        string fileName = "image.jpg";
        Console.WriteLine("Uploading file: {0}", fileName);
        var watch = System.Diagnostics.Stopwatch.StartNew();

        using (var sourceData = new FileStream(@"image.jpg", FileMode.Open))
        {
            await deviceClient.UploadToBlobAsync(fileName, sourceData);
        }

        watch.Stop();
        Console.WriteLine("Time to upload file: {0}ms\n", watch.ElapsedMilliseconds);
    }
    ```

    De `UploadToBlobAsync`-methode neemt de bestands naam en stroom bron van het bestand dat moet worden geüpload en verwerkt het uploaden naar de opslag. De console-app geeft de tijd weer die nodig is om het bestand te uploaden.

1. Voeg de volgende regel in de methode **Main** toe, rechts voor `Console.ReadLine()`:

    ```csharp
    SendToBlobAsync();
    ```

> [!NOTE]
> In het omwille van de eenvoud implementeert deze zelf studie geen beleid voor opnieuw proberen. In productie code moet u beleid voor opnieuw proberen implementeren, zoals exponentiële uitstel, zoals wordt voorgesteld in [tijdelijke fout afhandeling](/azure/architecture/best-practices/transient-faults).

## <a name="get-the-iot-hub-connection-string"></a>De IoT hub-connection string ophalen

In dit artikel maakt u een back-end-service om meldings berichten over het uploaden van bestanden te ontvangen van de IoT-hub die u hebt gemaakt in [telemetrie van een apparaat naar een IOT-hub verzenden](quickstart-send-telemetry-dotnet.md). Als u meldings berichten over het uploaden van bestanden wilt ontvangen, moet u de service **Connect** -machtiging hebben. Standaard wordt elke IoT Hub gemaakt met een gedeeld toegangs beleid met de naam **service** dat deze machtiging verleent.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Een melding over het uploaden van een bestand ontvangen

In deze sectie schrijft u een .NET-console-app die berichten over het uploaden van bestanden van IoT Hub ontvangt.

1. Selecteer in de huidige Visual Studio-oplossing **File** > **New** > **project**. Selecteer in **een nieuw project maken de**optie **console-app (.NET Framework)** en selecteer vervolgens **volgende**.

1. Geef het project de naam *ReadFileUploadNotification*. Onder **oplossing**selecteert **u toevoegen aan oplossing**. Selecteer **Maken** om het project te maken.

    ![Het ReadFileUploadNotification-project in Visual Studio configureren](./media/iot-hub-csharp-csharp-file-upload/read-file-upload-project-configure.png)

1. Klik in Solution Explorer met de rechter muisknop op het project **ReadFileUploadNotification** en selecteer **NuGet-pakketten beheren**.

1. Selecteer in **NuGet package manager**de optie **Browse**. Zoek en selecteer **micro soft. Azure. devices.** Selecteer vervolgens **installeren**.

    Deze stap downloadt, installeert en voegt een verwijzing toe naar het [Azure IOT Service SDK NuGet-pakket](https://www.nuget.org/packages/Microsoft.Azure.Devices/) in het **ReadFileUploadNotification** -project.

1. Voeg in het bestand **Program.cs** voor dit project de volgende instructie toe boven aan het bestand:

    ```csharp
    using Microsoft.Azure.Devices;
    ```

1. Voeg de volgende velden toe aan de klasse **Program**: Vervang de waarde van de tijdelijke aanduiding `{iot hub connection string}` door de IoT hub-connection string die u eerder hebt gekopieerd in [de IOT hub ophalen Connection String](#get-the-iot-hub-connection-string):

    ```csharp
    static ServiceClient serviceClient;
    static string connectionString = "{iot hub connection string}";
    ```

1. Voeg de volgende methode toe aan de klasse **Program**:

    ```csharp
    private async static void ReceiveFileUploadNotificationAsync()
    {
        var notificationReceiver = serviceClient.GetFileNotificationReceiver();

        Console.WriteLine("\nReceiving file upload notification from service");
        while (true)
        {
            var fileUploadNotification = await notificationReceiver.ReceiveAsync();
            if (fileUploadNotification == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received file upload notification: {0}", 
              string.Join(", ", fileUploadNotification.BlobName));
            Console.ResetColor();

            await notificationReceiver.CompleteAsync(fileUploadNotification);
        }
    }
    ```

    Houd er rekening mee dat dit ontvangst patroon hetzelfde is als het ontvangen van Cloud-naar-apparaat-berichten van de apparaat-app.

1. Voeg tot slot de volgende regels toe aan de methode **Main**:

    ```csharp
    Console.WriteLine("Receive file upload notifications\n");
    serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
    ReceiveFileUploadNotificationAsync();
    Console.WriteLine("Press Enter to exit\n");
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>De toepassingen uitvoeren

U kunt nu de toepassingen gaan uitvoeren.

1. Klik in Solution Explorer met de rechter muisknop op uw oplossing en selecteer vervolgens **opstart projecten instellen**.

1. In **algemene eigenschappen** > **project opstarten**selecteert u **meerdere opstart projecten**en selecteert u vervolgens de actie **starten** voor **ReadFileUploadNotification** en **SimulatedDevice**. Selecteer **OK** om uw wijzigingen op te slaan.

1. Druk op **F5**. Beide toepassingen moeten worden gestart. U ziet dat de Upload voltooid is in één console-app en het bericht voor het uploaden van meldingen dat is ontvangen door de andere console-app. U kunt de Server Explorer [Azure Portal](https://portal.azure.com/) of Visual Studio gebruiken om te controleren of het geüploade bestand aanwezig is in uw Azure Storage-account.

    ![Scherm opname van het uitvoer scherm](./media/iot-hub-csharp-csharp-file-upload/run-apps1.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u de functies voor het uploaden van bestanden van IoT Hub kunt gebruiken om Bestands uploads van apparaten te vereenvoudigen. U kunt door gaan met het verkennen van IoT Hub functies en scenario's met de volgende artikelen:

* [Een IoT-hub maken via een programma](iot-hub-rm-template-powershell.md)

* [Inleiding tot C SDK](iot-hub-device-sdk-c-intro.md)

* [SDK's voor Azure IoT](iot-hub-devguide-sdks.md)

Zie voor meer informatie over de mogelijkheden van IoT Hub:

* [AI implementeren op edge-apparaten met Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
