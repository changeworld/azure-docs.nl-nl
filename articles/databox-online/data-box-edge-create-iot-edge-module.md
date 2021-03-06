---
title: C#Module IoT Edge voor Azure Data Box Edge | Microsoft Docs
description: Meer informatie over het ontwikkelen C# van een IOT Edge module die op uw data Box Edge kan worden geïmplementeerd.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/06/2019
ms.author: alkohli
ms.openlocfilehash: 3aa1190fb713c2fbdedcb1ce84a65d4263693827
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2020
ms.locfileid: "78942546"
---
# <a name="develop-a-c-iot-edge-module-to-move-files-on-data-box-edge"></a>Een C# IOT Edge module ontwikkelen om bestanden te verplaatsen op Data Box Edge

In dit artikel wordt stapsgewijs beschreven hoe u een IoT Edge module maakt voor implementatie met uw Data Box Edge-apparaat. Azure Data Box Edge is een opslagoplossing waarmee u gegevens kunt verwerken en via een netwerk kunt verzenden naar Azure.

U kunt Azure IoT Edge modules met uw Data Box Edge gebruiken om de gegevens te transformeren wanneer deze zijn verplaatst naar Azure. De module die in dit artikel wordt gebruikt, implementeert de logica voor het kopiëren van een bestand van een lokale share naar een Cloud share op uw Data Box Edge-apparaat.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Maak een container register om uw modules (docker-installatie kopieën) op te slaan en te beheren.
> * Maak een IoT Edge module die op uw Data Box Edge apparaat moet worden geïmplementeerd.


## <a name="about-the-iot-edge-module"></a>Over de IoT Edge-module

Uw Data Box Edge-apparaat kan IoT Edge modules implementeren en uitvoeren. Edge-modules zijn in wezen docker-containers waarmee een specifieke taak wordt uitgevoerd, zoals het opnemen van een bericht van een apparaat, het transformeren van een bericht of het verzenden van een bericht naar een IoT Hub. In dit artikel maakt u een module die bestanden kopieert van een lokale share naar een Cloud share op uw Data Box Edge-apparaat.

1. Bestanden worden naar de lokale share op uw Data Box Edge-apparaat geschreven.
2. Bestands gebeurtenis Generator maakt een bestands gebeurtenis voor elk bestand dat naar de lokale share wordt geschreven. De bestands gebeurtenissen worden ook gegenereerd wanneer een bestand wordt gewijzigd. De bestands gebeurtenissen worden vervolgens naar IoT Edge hub verzonden (in IoT Edge runtime).
3. In de IoT Edge aangepaste module wordt de bestands gebeurtenis verwerkt voor het maken van een bestands gebeurtenis object dat ook een relatief pad voor het bestand bevat. De module genereert een absoluut pad met behulp van het relatieve bestandspad en kopieert het bestand van de lokale share naar de Cloud share. De module verwijdert vervolgens het bestand van de lokale share.

![De werking van Azure IoT Edge-module op Data Box Edge](./media/data-box-edge-create-iot-edge-module/how-module-works-1.png)

Zodra het bestand zich in de Cloud share bevindt, wordt het automatisch geüpload naar uw Azure Storage-account.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, controleert u of u over het volgende beschikt:

- Een Data Box Edge apparaat met.

    - Er is ook een IoT Hub resource gekoppeld aan het apparaat.
    - Er is een Edge Compute-rol geconfigureerd op het apparaat.
    Ga voor meer informatie naar [Configure Compute configureren](data-box-edge-deploy-configure-compute.md#configure-compute) voor uw data Box Edge.

- De volgende ontwikkel bronnen:

    - [Visual Studio Code](https://code.visualstudio.com/).
    - [De extensie C# voor Visual Studio Code (van OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
    - [Azure IoT Edge-extensie voor Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).
    - [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).
    - [Docker CE](https://store.docker.com/editions/community/docker-ce-desktop-windows). Mogelijk moet u een account maken om de software te downloaden en te installeren.

## <a name="create-a-container-registry"></a>Een containerregister maken

Een Azure-containerregister is een persoonlijk Docker-register in Azure waar u uw persoonlijke installatiekopieën van de Docker-container kunt opslaan en beheren. De twee populaire docker-register services die beschikbaar zijn in de Cloud, zijn Azure Container Registry en docker hub. In dit artikel wordt gebruikgemaakt van de Container Registry.

1. Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).
2. Selecteer **een resource maken > Containers > container Registry**. Klik op **Create**.
3. Geleverd

   1. Een unieke **register naam** in azure die 5 tot 50 alfanumerieke tekens bevat.
   2. Kies een **abonnement**.
   3. Nieuwe maken of een bestaande **resource groep**kiezen.
   4. Selecteer een **locatie**. U wordt aangeraden deze locatie te koppelen aan de Data Box Edge resource.
   5. Stel de **Gebruiker met beheerdersrechten** in op **Inschakelen**.
   6. Stel de SKU in op **Basic**.

      ![Containerregister maken](./media/data-box-edge-create-iot-edge-module/create-container-registry-1.png)
 
4. Selecteer **Maken**.
5. Nadat het containerregister is gemaakt, bladert u ernaartoe en selecteert u **Toegangssleutels**.

    ![Toegangs sleutels ophalen](./media/data-box-edge-create-iot-edge-module/get-access-keys-1.png)
 
6. Kopieer de waarden voor **Aanmeldingsserver**, **Gebruikersnaam** en **wachtwoord**. U gebruikt deze waarden later om de docker-installatie kopie naar het REGI ster te publiceren en de register referenties toe te voegen aan de Azure IoT Edge runtime.


## <a name="create-an-iot-edge-module-project"></a>Een IoT Edge-moduleproject creëren

Met de volgende stappen maakt u een IoT Edge module project op basis van de .NET Core 2,1 SDK. Het project maakt gebruik van Visual Studio code en de uitbrei ding Azure IoT Edge.

### <a name="create-a-new-solution"></a>Een nieuwe oplossing maken

Maak een C#-oplossingssjabloon die u met uw eigen code kunt aanpassen.

1. Selecteer in Visual Studio code **> opdracht palet weer geven** om het VS code-opdracht palet te openen.
2. Voer in het opdrachtpalet de opdracht **Azure: Sign in** in en voer deze uit. Volg vervolgens de instructies om u aan te melden bij uw Azure-account. Als u al bent aangemeld, kunt u deze stap overslaan.
3. Voer in het opdrachtpalet de opdracht **Azure IoT Edge: New IoT Edge solution** in en voer deze uit. Geef in het opdrachtpalet de volgende informatie op om de oplossing te maken:

    1. Selecteer de map waarin u de oplossing wilt maken.
    2. Geef een naam op voor de oplossing of houd de standaardnaam **EdgeSolution** aan.
    
        ![Nieuwe oplossing maken 1](./media/data-box-edge-create-iot-edge-module/create-new-solution-1.png)

    3. Kies **C# Module** als de modulesjabloon.
    4. Vervang de standaard module naam door de naam die u wilt toewijzen. in dit geval is het **FileCopyModule**.
    
        ![Nieuwe oplossing 2 maken](./media/data-box-edge-create-iot-edge-module/create-new-solution-2.png)

    5. Geef het container register op dat u in de vorige sectie hebt gemaakt als de opslag plaats voor installatie kopieën voor uw eerste module. Vervang **localhost:5000** door de gekopieerde waarde voor de aanmeldingsserver.

        De uiteindelijke teken reeks ziet eruit als `<Login server name>/<Module name>`. In dit voor beeld is de teken reeks: `mycontreg2.azurecr.io/filecopymodule`.

        ![Nieuwe oplossing 3 maken](./media/data-box-edge-create-iot-edge-module/create-new-solution-3.png)

4. Ga naar **bestand > map openen**.

    ![Nieuwe oplossing 4 maken](./media/data-box-edge-create-iot-edge-module/create-new-solution-4.png)

5. Blader naar de map **EdgeSolution** die u eerder hebt gemaakt. Het VS code-venster laadt uw IoT Edge Solution-werk ruimte met de vijf onderdelen op het hoogste niveau. U kunt de **vscode** -map, het. **gitignore** -bestand, het **. env** -bestand en de **implementatie. Temp late. json** in dit artikel niet bewerken.
    
    Het enige onderdeel dat u wijzigt, is de map modules. Deze map bevat de C# code voor uw module en docker-bestanden om uw module als container installatie kopie te maken.

    ![Nieuwe oplossing maken 5](./media/data-box-edge-create-iot-edge-module/create-new-solution-5.png)

### <a name="update-the-module-with-custom-code"></a>De module bijwerken met aangepaste code

1. Open in de VS code Explorer **modules > FileCopyModule > Program.cs**.
2. Voeg boven aan de **naam ruimte FileCopyModule**de volgende using-instructies toe voor typen die later worden gebruikt. **Micro soft. Azure. devices. client. Trans Port. Mqtt** is een protocol voor het verzenden van berichten naar IOT Edge hub.

    ```
    namespace FileCopyModule
    {
        using Microsoft.Azure.Devices.Client.Transport.Mqtt;
        using Newtonsoft.Json;
    ```
3. Voeg de variabele **InputFolderPath** en **OutputFolderPath** toe aan de klasse Program.

    ```
    class Program
        {
            static int counter;
            private const string InputFolderPath = "/home/input";
            private const string OutputFolderPath = "/home/output";
    ```

4. Voeg direct na de vorige stap de klasse **FileEvent** toe om de hoofd tekst van het bericht te definiëren.

    ```
    /// <summary>
    /// The FileEvent class defines the body of incoming messages. 
    /// </summary>
    private class FileEvent
    {
        public string ChangeType { get; set; }

        public string ShareRelativeFilePath { get; set; }

        public string ShareName { get; set; }
    }
    ```

5. In de **init-methode**maakt en configureert de code een **ModuleClient** -object. Met dit object kan de module verbinding maken met de lokale Azure IoT Edge runtime met behulp van het MQTT-protocol om berichten te verzenden en te ontvangen. De connection string die in de init-methode wordt gebruikt, wordt door de IoT Edge-runtime aan de module geleverd. Met de code wordt een FileCopy-call back geregistreerd voor het ontvangen van berichten van een IoT Edge hub via het **input1** -eind punt. Vervang de **init-methode** door de volgende code.

    ```
    /// <summary>
    /// Initializes the ModuleClient and sets up the callback to receive
    /// messages containing file event information
    /// </summary>
    static async Task Init()
    {
        MqttTransportSettings mqttSetting = new MqttTransportSettings(TransportType.Mqtt_Tcp_Only);
        ITransportSettings[] settings = { mqttSetting };

        // Open a connection to the IoT Edge runtime
        ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
        await ioTHubModuleClient.OpenAsync();
        Console.WriteLine("IoT Hub module client initialized.");

        // Register callback to be called when a message is received by the module
        await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FileCopy, ioTHubModuleClient);
    }
    ```

6. Verwijder de code voor de **methode PipeMessage** en plaats de code voor **FileCopy**.

    ```
        /// <summary>
        /// This method is called whenever the module is sent a message from the IoT Edge Hub.
        /// This method deserializes the file event, extracts the corresponding relative file path, and creates the absolute input file path using the relative file path and the InputFolderPath.
        /// This method also forms the absolute output file path using the relative file path and the OutputFolderPath. It then copies the input file to output file and deletes the input file after the copy is complete.
        /// </summary>
        static async Task<MessageResponse> FileCopy(Message message, object userContext)
        {
            int counterValue = Interlocked.Increment(ref counter);

            try
            {
                byte[] messageBytes = message.GetBytes();
                string messageString = Encoding.UTF8.GetString(messageBytes);
                Console.WriteLine($"Received message: {counterValue}, Body: [{messageString}]");

                if (!string.IsNullOrEmpty(messageString))
                {
                    var fileEvent = JsonConvert.DeserializeObject<FileEvent>(messageString);

                    string relativeFileName = fileEvent.ShareRelativeFilePath.Replace("\\", "/");
                    string inputFilePath = InputFolderPath + relativeFileName;
                    string outputFilePath = OutputFolderPath + relativeFileName;

                    if (File.Exists(inputFilePath))                
                    {
                        Console.WriteLine($"Moving input file: {inputFilePath} to output file: {outputFilePath}");
                        var outputDir = Path.GetDirectoryName(outputFilePath);
                        if (!Directory.Exists(outputDir))
                        {
                            Directory.CreateDirectory(outputDir);
                        }

                        File.Copy(inputFilePath, outputFilePath, true);
                        Console.WriteLine($"Copied input file: {inputFilePath} to output file: {outputFilePath}");
                        File.Delete(inputFilePath);
                        Console.WriteLine($"Deleted input file: {inputFilePath}");
                    } 
                    else
                    {
                        Console.WriteLine($"Skipping this event as input file doesn't exist: {inputFilePath}");   
                    }
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("Caught exception: {0}", ex.Message);
                Console.WriteLine(ex.StackTrace);
            }

            Console.WriteLine($"Processed event.");
            return MessageResponse.Completed;
        }
    ```

7. Dit bestand opslaan.
8. U kunt ook [een bestaand code voorbeeld](https://azure.microsoft.com/resources/samples/data-box-edge-csharp-modules/?cdn=disable) voor dit project downloaden. U kunt vervolgens het bestand dat u hebt opgeslagen in het **Program.cs** -bestand valideren in dit voor beeld.

## <a name="build-your-iot-edge-solution"></a>Uw IoT Edge-oplossing bouwen

In de vorige sectie hebt u een IoT Edge oplossing gemaakt en code toegevoegd aan de FileCopyModule om bestanden te kopiëren van een lokale share naar de Cloud share. Nu moet u de oplossing bouwen als een containerinstallatiekopie en deze naar het containerregister pushen.

1. Ga in VSCode naar Terminal > New terminal om een nieuwe, geïntegreerde Visual Studio code-Terminal te openen.
2. Meld u aan bij docker door de volgende opdracht in de geïntegreerde terminal in te voeren.

    `docker login <ACR login server> -u <ACR username>`

    Gebruik de aanmeldings server en de gebruikers naam die u hebt gekopieerd uit het container register.

    ![IoT Edge oplossing bouwen en pushen](./media/data-box-edge-create-iot-edge-module/build-iot-edge-solution-1.png)

2. Geef het wacht woord op wanneer u wordt gevraagd om het wacht woord. U kunt ook de waarden voor aanmeldings server, gebruikers naam en wacht woord ophalen uit de **toegangs sleutels** in het container register in het Azure Portal.
 
3. Zodra de referenties zijn opgegeven, kunt u de module-installatie kopie pushen naar uw Azure container Registry. Klik in de VS code Explorer met de rechter muisknop op het bestand **module. json** en selecteer **Build and push IOT Edge Solution**.

    ![IoT Edge oplossing bouwen en pushen](./media/data-box-edge-create-iot-edge-module/build-iot-edge-solution-2.png)
 
    Wanneer u Visual Studio code voor het bouwen van uw oplossing vertelt, worden er twee opdrachten uitgevoerd in de geïntegreerde terminal: docker-build en docker-push. Met deze twee opdrachten wordt uw code gebouwd, wordt de CSharpModule.dll in een container opgeslagen en wordt de code vervolgens naar het containerregister gepusht dat u hebt opgegeven toen u de oplossing initialiseerde.

    U wordt gevraagd om het module platform te kiezen. Selecteer *amd64* die overeenkomt met Linux.

    ![Platform selecteren](./media/data-box-edge-create-iot-edge-module/select-platform.png)

    > [!IMPORTANT] 
    > Alleen de linux-modules worden ondersteund.

    Mogelijk wordt de volgende waarschuwing weer gegeven die u kunt negeren:

    *Program. cs (77, 44): waarschuwing CS1998: deze async-methode heeft geen ' await ' Opera tors en wordt synchroon uitgevoerd. Overweeg het gebruik van de operator await om niet-blokkerende API-aanroepen of ' await-taak. run (...) ' te gebruiken voor het CPU-gebonden werk op een achtergrond thread.*

4. U kunt het volledige adres van de containerinstallatiekopie, inclusief de tag, zien in de geïntegreerde terminal van VS Code. Het adres van de installatie kopie is gebaseerd op informatie die zich in het bestand module. json bevindt met de indeling `<repository>:<version>-<platform>`. Voor dit artikel moet het er als volgt uitzien: `mycontreg2.azurecr.io/filecopymodule:0.0.1-amd64`.

## <a name="next-steps"></a>Volgende stappen

Zie de stappen in [een module toevoegen](data-box-edge-deploy-configure-compute.md#add-a-module)om deze module op Data Box Edge te implementeren en uit te voeren.
