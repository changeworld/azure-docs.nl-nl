---
title: 'Snelstartgids: Telemetrie verzenden naar Azure IoT Hub (Python) | Microsoft Docs'
description: In deze snelstart voert u een Python-voorbeeldtoepassing uit om gesimuleerde telemetrie te verzenden naar een IoT-hub en gebruikt u vervolgens een hulpprogramma om telemetrie uit de IoT-hub te lezen.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/17/2019
ms.openlocfilehash: 73722a7e5581d5e6275ec23f31351c2e4d4561ff
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78675377"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-python"></a>Quick Start: verzend telemetrie van een apparaat naar een IoT-hub en lees het met een back-end-toepassing (python)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

In deze Snelstartgids verzendt u telemetrie vanuit een gesimuleerd apparaat toepassing via Azure IoT Hub naar een back-end-toepassing voor verwerking. IoT Hub is een Azure-service waarmee grote hoeveelheden telemetrie van uw IoT-apparaten naar de cloud kunt opnemen voor opslag of verwerking. Deze Snelstartgids maakt gebruik van een vooraf geschreven python-toepassing voor het verzenden van de telemetrie en een CLI-hulp programma om de telemetrie van de hub te lezen. Voordat u deze twee toepassingen kunt uitvoeren, moet u een IoT-hub maken en een apparaat registreren bij de hub.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Maak er gratis een](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Python 3.7 +](https://www.python.org/downloads/). Zie [functies van Azure IOT-apparaten](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features)voor andere versies van python die worden ondersteund.

* [Een voor beeld van een python-project](https://github.com/Azure-Samples/azure-iot-samples-python/archive/master.zip).

* Poort 8883 is geopend in de firewall. Het voor beeld van het apparaat in deze Snelstartgids maakt gebruik van het MQTT-protocol, dat communiceert via poort 8883. Deze poort kan worden geblokkeerd in sommige bedrijfs-en educatieve netwerk omgevingen. Zie [verbinding maken met IOT hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)voor meer informatie en manieren om dit probleem te omzeilen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="add-azure-iot-extension"></a>Azure IoT-extensie toevoegen

Voer de volgende opdracht uit om de Microsoft Azure IoT-extensie voor Azure CLI toe te voegen aan uw Cloud Shell-exemplaar. De IoT-extensie voegt IoT Hub, IoT Edge en IoT Device Provisioning Service (DPS)-specifieke opdrachten toe aan Azure CLI.

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Een apparaat registreren

Een apparaat moet zijn geregistreerd bij uw IoT-hub voordat het verbinding kan maken. In deze snelstart gebruikt u Azure Cloud Shell om een gesimuleerd apparaat te registreren.

1. Voer de volgende opdracht uit in Azure Cloud Shell om de apparaat-id te maken.

    **YourIoTHubName**: vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub.

    **MyPythonDevice**: dit is de naam van het apparaat dat u wilt registreren. Het is raadzaam om **MyPythonDevice** te gebruiken zoals wordt weer gegeven. Als u een andere naam kiest voor uw apparaat, moet u deze naam ook in dit artikel gebruiken en de apparaatnaam bijwerken in de voorbeeld toepassingen voordat u ze uitvoert.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyPythonDevice
    ```

1. Voer de volgende opdracht uit in Azure Cloud Shell om de _apparaat Connection String_ te verkrijgen voor het apparaat dat u hebt geregistreerd:

    **YourIoTHubName**: vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyPythonDevice --output table
    ```

    Noteer de apparaatverbindingsreeks, die er ongeveer zo uitziet:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyPythonDevice;SharedAccessKey={YourSharedAccessKey}`

    U gebruikt deze waarde later in de Quick Start.

## <a name="send-simulated-telemetry"></a>Gesimuleerde telemetrie verzenden

De toepassing voor het gesimuleerde apparaat maakt verbinding met een apparaatspecifiek eindpunt op uw IoT-hub en verstuurt gesimuleerde telemetrie over temperatuur en luchtvochtigheid.

1. Navigeer in een lokaal terminalvenster naar de hoofdmap van het voorbeeldproject in Python. Navigeer vervolgens naar de map **iot-hub\Quickstarts\simulated-device**.

1. Open het bestand **SimulatedDevice.py** in een teksteditor van uw keuze.

    Vervang de waarde van de variabele `CONNECTION_STRING` door het apparaat connection string u eerder een notitie hebt gemaakt. Sla de wijzigingen vervolgens op in **SimulatedDevice.py**.

1. Voer in het lokale terminalvenster de volgende opdrachten uit om de vereiste bibliotheken te installeren voor de toepassing voor het gesimuleerde apparaat:

    ```cmd/sh
    pip install azure-iot-device
    ```

1. Voer in het lokale terminalvenster de volgende opdrachten uit om de toepassing voor het gesimuleerde apparaat uit te voeren:

    ```cmd/sh
    python SimulatedDevice.py
    ```

    In de volgende schermafbeelding ziet u de uitvoer op het moment dat de toepassing voor het gesimuleerde apparaat telemetriegegevens naar uw IoT-hub verzendt:

    ![Het gesimuleerde apparaat uitvoeren](media/quickstart-send-telemetry-python/SimulatedDevice.png)


## <a name="read-the-telemetry-from-your-hub"></a>De telemetrie van uw hub lezen

De IoT Hub CLI-extensie kan verbinding maken met het **Gebeurtenissen**-eindpunt aan de servicezijde van uw IoT Hub. De extensie ontvangt de berichten die van het gesimuleerde apparaat naar de cloud worden verzonden. Een back-endtoepassing van IoT Hub wordt meestal uitgevoerd in de cloud om berichten van apparaat naar cloud te ontvangen en verwerken.

Voer de volgende opdrachten uit in Azure Cloud Shell. Vervang daarbij `YourIoTHubName` door de naam van uw IoT-hub:

```azurecli-interactive
az iot hub monitor-events --hub-name {YourIoTHubName} --device-id MyPythonDevice 
```

In de volgende schermafbeelding ziet u de uitvoer op het moment dat de extensie telemetriegegevens ontvangt die door het gesimuleerde apparaat naar de hub zijn verzonden:

![Het hulpprogramma uitvoeren](media/quickstart-send-telemetry-python/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start kunt u een IoT-hub instellen, een apparaat registreren, gesimuleerde telemetrie naar de hub verzenden met behulp van een python-toepassing en de telemetrie van de hub lezen met behulp van een eenvoudige back-end-toepassing.

Ga verder met de volgende snelstartgids als u wilt weten hoe u een gesimuleerd apparaat beheert vanuit een back-endtoepassing.

> [!div class="nextstepaction"]
> [Snelstartgids: Een apparaat beheren dat is verbonden met een IoT-hub](quickstart-control-device-python.md)
