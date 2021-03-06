---
title: Apparaat inrichten met een virtuele TPM op Linux VM-Azure IoT Edge
description: Een gesimuleerd TPM gebruiken op een Linux-VM voor het testen van Azure Device Provisioning Service voor Azure IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6bb1282212ccff45f179b8750e3ed8aec27d129e
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76511056"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-virtual-tpm-on-a-linux-virtual-machine"></a>Een IoT Edge apparaat maken en inrichten met een virtuele TPM op een virtuele Linux-machine

Azure IoT Edge apparaten kunnen automatisch worden ingericht met behulp van de [Device Provisioning Service](../iot-dps/index.yml). Als u niet bekend met het proces van automatische inrichting bent, raadpleegt u de [concepten voor automatische inrichting](../iot-dps/concepts-auto-provisioning.md) voordat u doorgaat.

In dit artikel leest u hoe u automatisch inrichten op een gesimuleerd IoT Edge apparaat kunt testen met de volgende stappen:

* Maak een virtuele Linux virtuele machine (VM) in Hyper-V met een gesimuleerde Trusted Platform Module (TPM) voor hardwarebeveiliging.
* Maak een exemplaar van de IoT Hub Device Provisioning Service (DPS).
* Maken van een afzonderlijke inschrijving voor het apparaat
* Installeren van de IoT Edge-runtime en verbind het apparaat met IoT Hub

> [!TIP]
> In dit artikel wordt beschreven hoe u DPS inrichten kunt testen met behulp van een TPM-Simulator, maar veel hiervan is van toepassing op fysieke TPM-hardware, zoals [INFINEON OPTIGA&trade; TPM](https://catalog.azureiotsolutions.com/details?title=OPTIGA-TPM-SLB-9670-Iridium-Board), een Azure Certified voor IOT-apparaat.
>
> Als u een fysiek apparaat gebruikt, kunt u door gaan naar de sectie [inrichtings gegevens ophalen van een fysiek apparaat](#retrieve-provisioning-information-from-a-physical-device) in dit artikel.

## <a name="prerequisites"></a>Vereisten

* Een Windows-ontwikkelcomputer met [Hyper-V ingeschakeld](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v). In dit artikel maakt gebruik van Windows 10 waarop een Ubuntu-Server-VM wordt uitgevoerd.
* Een actieve IoT-Hub.
* Als u een gesimuleerde TPM, [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 of hoger, met de [' Desktop C++Development with '](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) -werk belasting ingeschakeld.

> [!NOTE]
> TPM 2,0 is vereist voor het gebruik van TPM-Attestation met DPS en kan alleen worden gebruikt voor het maken van afzonderlijke, niet-groeps registraties.

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>Een virtuele Linux-machine maken met een virtuele TPM

In deze sectie maakt u een nieuwe virtuele Linux-machine in Hyper-V. U hebt deze virtuele machine met een gesimuleerde TPM geconfigureerd, zodat u deze kunt gebruiken om te testen hoe automatische inrichting werkt met IoT Edge.

### <a name="create-a-virtual-switch"></a>Een virtuele switch maken

Een virtuele switch kunt uw virtuele machine verbinding maken met een fysiek netwerk.

1. Open Hyper-V-beheer op de Windows-computer.

2. In de **acties** in het menu **Virtual Switch Manager**.

3. Kies een **externe** virtuele switch, en selecteer vervolgens **virtuele Switch maken**.

4. Geef uw nieuwe virtuele switch een naam, bijvoorbeeld **EdgeSwitch**. Zorg ervoor dat het verbindingstype is ingesteld op **extern netwerk**en selecteer vervolgens **Ok**.

5. Een pop-upvenster waarschuwt u dat de verbinding met het netwerk kan worden onderbroken. Selecteer **Ja** om door te gaan.

Als u fouten ziet tijdens het maken van de nieuwe virtuele switch, zorg ervoor dat er geen andere switches van de ethernet-adapter gebruikmaakt, en dat er geen andere switches dezelfde naam gebruiken.

### <a name="create-virtual-machine"></a>Virtuele machine maken

1. Download een schijfimage-bestand wilt gebruiken voor uw virtuele machine en deze lokaal opslaat. Bijvoorbeeld, [Ubuntu server](https://www.ubuntu.com/download/server).

2. In Hyper-V-beheer opnieuw selecteert u **nieuw** > **virtuele machine** in het menu **acties** .

3. Voltooi de **Wizard Nieuwe virtuele Machine** met de volgende specifieke configuraties:

   1. **Generatie opgeven**: Selecteer **van de 2e generatie**. Voor virtuele machines van de 2e generatie is geneste virtualisatie ingeschakeld, wat vereist is om IoT Edge uit te voeren op een virtuele machine.
   2. **Configureren van netwerken**: Stel de waarde van **verbinding** aan de virtuele switch die u in de vorige sectie hebt gemaakt.
   3. **Opties voor de installatie**: Selecteer **een besturingssysteem installeren vanaf een opstartbare installatiekopie-bestand** en blader naar het schijfimage-bestand dat u lokaal hebt opgeslagen.

4. Selecteer in de wizard **volt ooien** om de virtuele machine te maken.

Het duurt een paar minuten aan de nieuwe virtuele machine maken.

### <a name="enable-virtual-tpm"></a>Virtuele TPM inschakelen

Zodra de VM is gemaakt, opent u de instellingen om de Virtual trusted platform module (TPM) in te scha kelen waarmee u het apparaat automatisch kunt inrichten.

1. Selecteer de virtuele machine en open vervolgens de bijbehorende **instellingen**.

2. Navigeer naar **Beveiliging**.

3. Schakel het selectievakje **beveiligd opstarten inschakelen**.

4. Controleer **inschakelen Trusted Platform Module**.

5. Klik op **OK**.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>Start de virtuele machine en TPM-gegevens verzamelen

Bouw in de virtuele machine een hulp programma dat u kunt gebruiken om de **registratie-id** en **goedkeurings sleutel**van het apparaat op te halen.

1. Start de virtuele machine en maak er verbinding mee.

1. Volg de aanwijzingen in de virtuele machine om het installatie proces te volt ooien en de computer opnieuw op te starten.

1. Meld u aan bij uw virtuele machine en volg de stappen in [een Linux-ontwikkel omgeving instellen](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) om de Azure IOT Device SDK voor C te installeren en te bouwen.

   >[!TIP]
   >In het kader van dit artikel kopieert u naar en plakt u de virtuele machine. Dit is niet eenvoudig via de verbindings toepassing Hyper-V-beheer. U kunt het beste een verbinding met de virtuele machine maken via Hyper-V-beheer om het IP-adres op te halen: `ifconfig`. Vervolgens kunt u het IP-adres gebruiken om verbinding te maken via SSH: `ssh <username>@<ipaddress>`.

1. Voer de volgende opdrachten uit om het SDK-hulp programma te bouwen dat de inrichtings gegevens van uw apparaat ophaalt uit de TPM-Simulator.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Ga vanuit een opdracht venster naar de map `azure-iot-sdk-c` en voer de TPM-Simulator uit. Deze luistert via een socket op poorten 2321 en 2322. Sluit dit opdracht venster niet. u moet deze simulator blijven uitvoeren.

   Voer in de `azure-iot-sdk-c` map de volgende opdracht uit om de Simulator te starten:

   ```bash
   ./provisioning_client/deps/utpm/tools/tpm_simulator/Simulator.exe
   ```

1. Open met behulp van Visual Studio de oplossing die is gegenereerd in de `cmake` map met de naam `azure_iot_sdks.sln`en bouw deze met behulp van de opdracht **Build Solution** in het menu **Build** .

1. In het deelvenster **Solution Explorer** van Visual Studio gaat u naar de map **Inrichten\_Extra**. Klik met de rechtermuisknop op het **tpm_device_provision**-project en selecteer **Set as Startup Project**.

1. Voer de oplossing uit met behulp van een van de **Start** opdrachten in het menu **fout opsporing** . In het uitvoer venster worden de **registratie-id** van de TPM-Simulator en de **goedkeurings sleutel**weer gegeven die u moet kopiëren voor later gebruik wanneer u een afzonderlijke inschrijving voor uw apparaat maakt in u kunt dit venster sluiten (met registratie-id en goedkeurings sleutel), maar het TPM-Simulator-venster actief laten.

## <a name="retrieve-provisioning-information-from-a-physical-device"></a>Inrichtings gegevens ophalen van een fysiek apparaat

Bouw op uw apparaat een hulp programma dat u kunt gebruiken om de inrichtings gegevens van het apparaat op te halen.

1. Volg de stappen in [een Linux-ontwikkel omgeving instellen](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) om de Azure IOT Device SDK voor C te installeren en te bouwen.

1. Voer de volgende opdrachten uit om het SDK-hulp programma te bouwen dat de inrichtings gegevens van uw apparaat ophaalt van het TPM-apparaat.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Kopieer de waarden voor de **registratie-id** en **goedkeurings sleutel**. Deze waarden kunt u een afzonderlijke inschrijving voor uw apparaat in DPS maken.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>De IoT Hub Device Provisioning Service instellen

Maak een nieuw exemplaar van de IoT Hub Device Provisioning Service in Azure en een koppeling naar uw IoT hub. U kunt de instructies in [instellen van de IoT Hub-DPS](../iot-dps/quick-setup-auto-provision.md).

Nadat u de Device Provisioning Service die wordt uitgevoerd hebt, kopieert u de waarde van **ID-bereik** van de overzichtspagina. U gebruikt deze waarde bij het configureren van de IoT Edge-runtime.

## <a name="create-a-dps-enrollment"></a>Maken van een DPS-inschrijving

Ophalen van de Inrichtingsgegevens van uw virtuele machine en maakt u een afzonderlijke inschrijving in Device Provisioning Service.

Wanneer u een inschrijving in DPS maakt, hebt u de mogelijkheid om te declareren een **oorspronkelijke Apparaatdubbelstatus**. U kunt in de apparaatdubbel labels instellen om apparaten te groeperen door elke meetwaarde die u nodig hebt in uw oplossing, zoals regio, omgeving, locatie, of het apparaat. Deze tags zijn gebruikt voor het maken [automatische implementaties](how-to-deploy-monitor.md).

1. Ga in het [Azure Portal](https://portal.azure.com)naar uw exemplaar van IOT hub Device Provisioning Service.

2. Onder **instellingen**, selecteer **registraties beheren**.

3. Selecteer **afzonderlijke registratie toevoegen** voltooit u de volgende stappen om de registratie te configureren:  

   1. Voor **mechanisme**, selecteer **TPM**.

   2. Geef de **goedkeurings sleutel** en **registratie-id** op die u van de virtuele machine hebt gekopieerd.

      > [!TIP]
      > Als u een fysiek TPM-apparaat gebruikt, moet u de **goedkeurings sleutel**bepalen, die uniek is voor elke TPM-chip en die is verkregen van de fabrikant van de TPM-chip die eraan is gekoppeld. U kunt een unieke **registratie-id** voor uw TPM-apparaat afleiden door bijvoorbeeld een SHA-256-hash van de goedkeurings sleutel te maken.

   3. Selecteer **waar** om te declareren dat deze virtuele machine een IOT edge apparaat is.

   4. Kies de gekoppelde **IoT-Hub** dat u wilt verbinding maken met uw apparaat. U kunt meerdere hubs kiezen en het apparaat wordt toegewezen aan een van deze op basis van het geselecteerde toewijzings beleid.

   5. Als u wilt, moet u een ID opgeven voor uw apparaat. Apparaat-id's kunt u richten op een afzonderlijk apparaat voor de implementatie van beleidsmodule. Als u geen apparaat-ID opgeeft, wordt de registratie-ID gebruikt.

   6. Een tagwaarde toevoegen aan de **oorspronkelijke Apparaatdubbelstatus** als u wilt. U kunt tags voor doelgroepen van apparaten gebruiken voor de implementatie van beleidsmodule. Bijvoorbeeld:

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

   7. Selecteer **Opslaan**.

Nu een inschrijving voor dit apparaat bestaat, kan de IoT Edge runtime automatisch het apparaat inrichten tijdens de installatie.

## <a name="install-the-iot-edge-runtime"></a>IoT Edge-runtime installeren

De IoT Edge-runtime wordt op alle IoT Edge-apparaten geïmplementeerd. De onderdelen in containers worden uitgevoerd, en kunnen u extra containers implementeren op het apparaat, zodat u kunt de code aan de rand uitvoeren. IoT Edge-runtime installeren op uw virtuele machine.

Kent uw DPS **ID-bereik** en apparaat **registratie-ID** voordat u begint met het artikel die overeenkomt met uw apparaattype. Als u het voorbeeld van de Ubuntu-server hebt geïnstalleerd, gebruikt u de **x64** instructies. Zorg ervoor dat u de IoT Edge-runtime voor het inrichten van automatische, niet handmatig configureren.

[Installeer de Azure IoT Edge runtime op Linux](how-to-install-iot-edge-linux.md)

## <a name="give-iot-edge-access-to-the-tpm"></a>IoT Edge toegang geven tot de TPM

In de volgorde voor de IoT Edge-runtime voor het automatisch inrichten van uw apparaat, moet deze toegang hebben tot de TPM.

U kunt TPM toegang verlenen tot de IoT Edge-runtime door te overschrijven de instellingen voor systemd zodat de **iotedge** service heeft bevoegdheden op hoofdniveau. Als u niet wilt en de service-bevoegdheden te verhogen, kunt u ook de volgende stappen gebruiken handmatig TPM om toegang te bieden.

1. Het pad naar de TPM-hardware-module vinden op uw apparaat en sla deze op als een lokale variabele.

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. Maak een nieuwe regel waarmee wordt de IoT Edge runtime toegang tot tpm0.

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

3. Open het regelbestand.

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

4. Kopieer de volgende toegang tot gegevens in het regelbestand.

   ```input
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", GROUP="iotedge", MODE="0660"
   ```

5. Opslaan en sluiten van het bestand.

6. Activeer de udev-systeem om te evalueren van de nieuwe regel.

   ```bash
   /bin/udevadm trigger $tpm
   ```

7. Controleer of dat de regel is toegepast.

   ```bash
   ls -l /dev/tpm0
   ```

   Geslaagde uitvoer ziet er als volgt uit:

   ```output
   crw-rw---- 1 root iotedge 10, 224 Jul 20 16:27 /dev/tpm0
   ```

   Als u niet ziet dat de juiste machtigingen zijn toegepast, probeer het opnieuw opstarten van uw computer om te vernieuwen udev.

## <a name="restart-the-iot-edge-runtime"></a>Opnieuw opstarten van de IoT Edge-runtime

IoT Edge-runtime opnieuw zodat deze neemt alle configuratiewijzigingen die u hebt aangebracht op het apparaat.

   ```bash
   sudo systemctl restart iotedge
   ```

Controleer of de IoT Edge-runtime wordt uitgevoerd.

   ```bash
   sudo systemctl status iotedge
   ```

Als u fouten bij het inrichten ziet, kan het zijn dat de wijzigingen in de configuratie zijn niet doorgevoerd nog. Probeer opnieuw de IoT Edge-daemon opnieuw op te starten.

   ```bash
   sudo systemctl daemon-reload
   ```

Of probeer het opnieuw opstarten van uw virtuele machine om te zien als de wijzigingen van kracht op een nieuwe start.

## <a name="verify-successful-installation"></a>Controleer of geslaagde installatie

Als de runtime is gestart, kunt u met ingang van uw IoT-Hub en zien dat het nieuwe apparaat automatisch is ingericht. Uw apparaat is nu gereed voor het IoT Edge-modules uitvoeren.

Controleer de status van de IoT Edge-Daemon.

```cmd/sh
systemctl status iotedge
```

Controleer de daemon-Logboeken.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Lijst met modules.

```cmd/sh
iotedge list
```

U kunt controleren of de afzonderlijke registratie die u hebt gemaakt in Device Provisioning Service is gebruikt. Navigeer naar het Device Provisioning service-exemplaar in het Azure Portal. Open de inschrijvings gegevens voor de afzonderlijke inschrijving die u hebt gemaakt. U ziet dat de status van de registratie is **toegewezen** en dat de apparaat-id wordt vermeld.

## <a name="next-steps"></a>Volgende stappen

Het inschrijvingsproces Device Provisioning Service kunt u instellen de apparaat-ID en het apparaat dubbele tags op hetzelfde moment als u het nieuwe apparaat inrichten. Deze waarden kunt u afzonderlijke apparaten of groepen van apparaten met behulp van automatische Apparaatbeheer. Meer informatie over het [implementeren en te bewaken IoT Edge-modules op schaal met Azure portal](how-to-deploy-monitor.md) of [met behulp van Azure CLI](how-to-deploy-monitor-cli.md).
