---
title: Een nieuw Azure IoT Edge apparaat registreren | Microsoft Docs
description: De IoT-extensie voor Azure CLI gebruiken om te registreren van een nieuwe IoT Edge-apparaat en de verbindingsreeks ophalen
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/08/2020
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f7facb539a84d39e6659627e80ecc560fc87591c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79285095"
---
# <a name="register-an-azure-iot-edge-device"></a>Een Azure IoT Edge-apparaat registreren

Voordat u uw IoT-apparaten met Azure IoT Edge kunt gebruiken, moet u ze registreren bij uw IoT-hub. Zodra een apparaat is geregistreerd, kunt u een connection string ophalen om uw apparaat in te stellen voor IoT Edge werk belastingen.

U kunt kiezen uit een van de volgende hulpprogram ma's:

* [Registreer een apparaat in de Azure Portal](#register-in-the-azure-portal) als u de voor keur geeft aan een graphical user interface om Azure-resources te maken, weer te geven en te beheren.
* [Registreer een apparaat met Visual Studio code](#register-with-visual-studio-code) als u liever Azure IOT-resources wilt beheren op dezelfde locatie waar u uw IOT-oplossingen ontwikkelt.
* [Registreer een apparaat met de Azure cli](#register-with-the-azure-cli) als u de voor keur geeft aan opdracht regel Programma's voor het beheren van Azure-resources of als u taken wilt automatiseren.

## <a name="register-in-the-azure-portal"></a>Registreren in de Azure Portal

U kunt alle registratie taken uitvoeren in de Azure Portal.

### <a name="prerequisites-for-the-azure-portal"></a>Vereisten voor de Azure Portal

Een gratis of standaard [IOT-hub](../iot-hub/iot-hub-create-through-portal.md) in uw Azure-abonnement.

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>Een IoT Edge apparaat maken in de Azure Portal

In uw IoT Hub in de Azure Portal worden IoT Edge apparaten afzonderlijk gemaakt en beheerd van IOT-apparaten die niet Edge zijn ingeschakeld.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en navigeer naar uw IOT-hub.
2. Selecteer in het linkerdeel venster **IOT Edge** in het menu.
3. Selecteer **een IOT edge apparaat toevoegen**.
4. Geef een beschrijvende apparaat-ID. Gebruik de standaard instellingen voor het automatisch genereren van verificatie sleutels en het verbinden van het nieuwe apparaat met uw hub.
5. Selecteer **Opslaan**.

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>IoT Edge apparaten weer geven in de Azure Portal

Alle apparaten met rand mogelijkheden die verbinding maken met uw IoT-hub, worden weer gegeven op de pagina **IOT Edge** .

![Alle IoT Edge-apparaten in uw IoT-hub bekijken](./media/how-to-register-device/portal-view-devices.png)

### <a name="retrieve-the-connection-string-in-the-azure-portal"></a>De connection string in het Azure Portal ophalen

Wanneer u klaar bent om uw apparaat instellen, moet u de verbindingsreeks die is gekoppeld aan uw fysieke apparaat met de identiteit van de IoT-hub.

1. Klik op de pagina **IOT Edge** in de portal op de apparaat-id in de lijst met IOT edge apparaten.
2. Kopieer de waarde van de **primaire verbindings reeks** of **secundaire verbindings reeks**.

## <a name="register-with-visual-studio-code"></a>Registreren met Visual Studio code

Er zijn meerdere manieren om uit te voeren van de meeste bewerkingen in VS Code. In dit artikel wordt de Explorer gebruikt, maar u kunt ook het opdracht palet gebruiken om de stappen uit te voeren.

### <a name="prerequisites-for-visual-studio-code"></a>Vereisten voor Visual Studio code

* Een [IOT-hub](../iot-hub/iot-hub-create-through-portal.md) in uw Azure-abonnement
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure IOT-Hulpprogram ma's](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) voor Visual Studio code

### <a name="sign-in-to-access-your-iot-hub"></a>Meld u aan voor toegang tot uw IoT-hub

U kunt de Azure IoT-uitbrei dingen voor Visual Studio code gebruiken om bewerkingen met uw IoT Hub uit te voeren. Als u deze bewerkingen wilt gebruiken, moet u zich aanmelden bij uw Azure-account en uw IoT Hub selecteren.

1. Open in Visual Studio code de weer gave **Explorer** .
1. Vouw de sectie **Azure-IOT hub** aan de onderkant van de Explorer uit.

   ![Vouw de sectie Azure IoT Hub-apparaten](./media/how-to-register-device/azure-iot-hub-devices.png)

1. Klik in de koptekst van de Azure- **IOT hub** op de sectie **..** .. Als u het beletselteken niet ziet, klikt u op of Beweeg de muisaanwijzer over de header.
1. Kies **IOT hub selecteren**.
1. Als u niet bent aangemeld bij uw Azure-account, volgt u de aanwijzingen om dit te doen.
1. Selecteer uw Azure-abonnement.
1. Selecteer uw IoT-hub.

### <a name="create-an-iot-edge-device-with-visual-studio-code"></a>Een IoT Edge-apparaat maken met Visual Studio code

1. Vouw in de VS code Explorer het gedeelte **Azure IOT Hub-apparaten** uit.
1. Klik op de sectie **..** . in de koptekst van de **Azure IOT Hub-apparaten** . Als u het beletselteken niet ziet, klikt u op of Beweeg de muisaanwijzer over de header.
1. Selecteer **IOT edge apparaat maken**.
1. Geef in het tekstvak dat wordt geopend, uw apparaat een ID.

In het uitvoerscherm ziet u het resultaat van de opdracht. De apparaatgegevens worden afgedrukt, inclusief de **deviceId** die u hebt geleverd en de **Connections Tring** die u kunt gebruiken om uw fysieke apparaat te verbinden met uw IOT-hub.

In het uitvoerscherm ziet u het resultaat van de opdracht. De apparaatgegevens worden afgedrukt, inclusief de **deviceId** die u hebt geleverd en de **Connections Tring** die u kunt gebruiken om uw fysieke apparaat te verbinden met uw IOT-hub.

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>IoT Edge apparaten weer geven met Visual Studio code

Alle apparaten die verbinding maken met uw IoT-hub, worden weer gegeven in de sectie **Azure IOT hub** van Visual Studio code Explorer. IoT Edge apparaten zijn onderscheiden van niet-rand apparaten met een ander pictogram, en het feit dat de **$edgeAgent** en **$edgeHub** modules op elk IOT edge apparaat worden geïmplementeerd.

![Alle IoT Edge-apparaten in uw IoT-hub bekijken](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>De connection string ophalen met Visual Studio code

Wanneer u klaar bent om uw apparaat instellen, moet u de verbindingsreeks die is gekoppeld aan uw fysieke apparaat met de identiteit van de IoT-hub.

1. Klik met de rechter muisknop op de ID van uw apparaat in het gedeelte **Azure IOT hub** .
1. Selecteer **apparaat verbindings reeks kopiëren**.

   De verbindingsreeks is naar het Klembord gekopieerd.

U kunt ook **apparaatgegevens ophalen** selecteren in het menu met de rechter muisknop om alle apparaatgegevens, inclusief de Connection String, weer te geven in het uitvoer venster.

## <a name="register-with-the-azure-cli"></a>Registreren bij Azure CLI

[Azure cli](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) is een open source-opdracht regel programma voor meerdere platformen voor het beheer van Azure-resources, zoals IOT Edge. Hiermee kunt u voor het beheren van Azure IoT Hub-resources, device provisioning service-exemplaren en gekoppelde hubs buiten het vak. De IoT-extensie verrijkt Azure CLI met functies als Apparaatbeheer en volledige IoT Edge mogelijkheid.

### <a name="prerequisites-for-the-azure-cli"></a>Vereisten voor de Azure CLI

* Een [IOT-hub](../iot-hub/iot-hub-create-using-cli.md) in uw Azure-abonnement.
* [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli) in uw omgeving. Uw Azure CLI-versie moet mini maal 2.0.70 of hoger zijn. Gebruik `az --version` om de versie te valideren. In deze versie worden az-extensie-opdrachten ondersteund en is voor het eerst het Knack-opdrachtframework opgenomen.
* De [IOT-extensie voor Azure cli](https://github.com/Azure/azure-iot-cli-extension).

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>Een IoT Edge-apparaat maken met de Azure CLI

Gebruik de opdracht [AZ IOT hub apparaat-Identity Create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create) om een nieuwe apparaat-id te maken in uw IOT-hub. Bijvoorbeeld:

   ```cli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Met deze opdracht worden drie parameters:

* **apparaat-id**: Geef een beschrijvende naam op die uniek is voor uw IOT-hub.
* **hub-naam**: Geef de naam op van uw IOT-hub.
* **rand**: deze para meter geeft aan dat het apparaat moet worden gebruikt met IOT Edge.

   ![AZ iot hub device-identity maken uitvoer](./media/how-to-register-device/Create-edge-device.png)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>IoT Edge apparaten weer geven met de Azure CLI

Gebruik de opdracht [AZ IOT hub Device-Identity List](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-list) om alle apparaten in uw IOT-hub weer te geven. Bijvoorbeeld:

   ```cli
   az iot hub device-identity list --hub-name [hub name]
   ```

Elk apparaat dat is geregistreerd als IoT Edge apparaat heeft de eigenschaps **mogelijkheden. iotEdge** ingesteld op **True**.

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>De connection string ophalen met de Azure CLI

Wanneer u klaar bent om uw apparaat instellen, moet u de verbindingsreeks die is gekoppeld aan uw fysieke apparaat met de identiteit van de IoT-hub. Gebruik de opdracht [AZ IOT hub Device-identiteit show-Connection-String](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-show-connection-string) om de Connection String voor één apparaat te retour neren:

   ```cli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

De waarde voor de para meter `device-id` is hoofdletter gevoelig. Kopieer niet de aanhalingstekens rond de verbindingsreeks.

## <a name="next-steps"></a>Volgende stappen

Nu u een apparaat-id in uw IoT-hub hebt geregistreerd, bent u klaar om de IoT Edge runtime op uw apparaten te installeren. Installeer de runtime op basis van het besturings systeem van het apparaat:

* [Azure IoT Edge installeren in Windows](how-to-install-iot-edge-windows.md)
* [Installeer de Azure IoT Edge runtime op Linux](how-to-install-iot-edge-linux.md)
