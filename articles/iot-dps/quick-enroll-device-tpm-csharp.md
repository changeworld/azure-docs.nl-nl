---
title: TPM-apparaat inschrijven bij Azure Device Provisioning Service met behulp vanC#
description: 'Quick Start: TPM-apparaat inschrijven bij Azure IoT Hub Device Provisioning Service (DPS C# ) met Service SDK. In deze snelstart wordt gebruikgemaakt van afzonderlijke inschrijvingen.'
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc
ms.openlocfilehash: ee1b803459e0c81b86021b617a29e0b29ee19909
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976839"
---
# <a name="quickstart-enroll-tpm-device-to-iot-hub-device-provisioning-service-using-c-service-sdk"></a>Quick Start: TPM-apparaat inschrijven voor C# IOT hub Device Provisioning Service met Service SDK

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]

In dit artikel wordt beschreven hoe u programmatisch een afzonderlijke inschrijving voor een TPM-apparaat in de Azure-IOT hub Device Provisioning Service kunt maken met behulp van C# de [ C# Service SDK](https://github.com/Azure/azure-iot-sdk-csharp) en een voor beeld van een .net core-toepassing. U kunt eventueel een gesimuleerd TPM-apparaat bij de inrichtings service inschrijven met behulp van deze afzonderlijke registratie vermelding. Hoewel deze stappen op zowel Windows-als Linux-computers werken, wordt in dit artikel een Windows-ontwikkel computer gebruikt.

## <a name="prepare-the-development-environment"></a>De ontwikkelomgeving voorbereiden

1. Controleer of [Visual Studio 2019](https://www.visualstudio.com/vs/) op uw computer is geïnstalleerd.

1. Controleer of de [.net core SDK](https://www.microsoft.com/net/download/windows) op uw computer is geïnstalleerd.

1. Voltooi de stappen in [Stel de IOT hub Device Provisioning Service in met de Azure Portal](./quick-setup-auto-provision.md) voordat u doorgaat.

1. Beschrijving Als u aan het eind van deze Quick Start een gesimuleerd apparaat wilt inschrijven, volgt u de procedure in [een gesimuleerd TPM-apparaat C# maken en inrichten met een apparaat-SDK](quick-create-simulated-device-tpm-csharp.md) tot de stap waarin u een goedkeurings sleutel voor het apparaat ontvangt. Sla de goedkeurings sleutel, registratie-ID en, optioneel, de apparaat-ID op omdat u deze later in deze Quick start moet gebruiken.

   > [!NOTE]
   > Volg niet de stappen voor het maken van een afzonderlijke inschrijving met behulp van de Azure Portal.

## <a name="get-the-connection-string-for-your-provisioning-service"></a>De verbindingsreeks voor de inrichtingsservice ophalen

Voor het voorbeeld in deze snelstart hebt u de verbindingsreeks voor de inrichtingsservice nodig.

1. Meld u aan bij de Azure Portal, selecteer **alle resources**en vervolgens uw Device Provisioning-Service.

1. Kies **beleid voor gedeelde toegang**en selecteer vervolgens het toegangs beleid dat u wilt gebruiken om de eigenschappen te openen. Kopieer en sla de primaire sleutel connection string op in het **toegangs beleid**.

    ![Verbindingsreeks voor de inrichtingsservice ophalen uit de portal](media/quick-enroll-device-tpm-csharp/get-service-connection-string-vs2019.png)

## <a name="create-the-individual-enrollment-sample"></a>Het voorbeeld van de afzonderlijke inschrijving maken

In deze sectie wordt beschreven hoe u een .NET Core-Console-app maakt die een afzonderlijke inschrijving voor een TPM-apparaat toevoegt aan uw inrichtings service. Met enkele aanpassingen kunt u deze stappen ook volgen om een [Windows IoT Core](https://developer.microsoft.com/en-us/windows/iot) console-app te maken om aan de afzonderlijke registratie toe te voegen. Zie [Windows IOT core-documentatie voor ontwikkel aars](https://docs.microsoft.com/windows/iot-core/)voor meer informatie over het ontwikkelen met IOT core.

1. Open Visual Studio en selecteer **een nieuw project maken**. Kies in **een nieuw project maken**de project sjabloon **console-app (.net core)** voor C# en selecteer **volgende**.

1. Geef het project de naam *CreateTpmEnrollment*en druk op **maken**.

    ![Klassiek C# Windows-bureau blad-project configureren](media/quick-enroll-device-tpm-csharp/configure-tpm-app-vs2019.png)

1. Wanneer de oplossing wordt geopend in Visual Studio, klikt u in het deel venster **Solution Explorer** met de rechter muisknop op het project **CreateTpmEnrollment** . Selecteer **NuGet-pakketten beheren**.

1. Selecteer in **NuGet package manager** **Bladeren**, zoek naar en kies **micro soft. Azure. devices. provisioning. service**en druk vervolgens op **install**.

   ![Sluit het venster Nuget Package Manager.](media//quick-enroll-device-tpm-csharp/add-nuget.png)

   Met deze stap wordt een verwijzing naar het [Azure IOT Provisioning Service client SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) NuGet-pakket en de bijbehorende afhankelijkheden gedownload, geïnstalleerd en toegevoegd.

1. Voeg de volgende `using`-instructies toe achter de andere `using`-instructies boven aan `Program.cs`:
  
   ```csharp
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Provisioning.Service;
   ```

1. Voeg de volgende velden toe aan de klasse `Program`, zodat u de hieronder vermelde wijzigingen kunt aanbrengen.

   ```csharp
   private static string ProvisioningConnectionString = "{ProvisioningServiceConnectionString}";
   private const string RegistrationId = "sample-registrationid-csharp";
   private const string TpmEndorsementKey =
       "AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAxsj2gUS" +
       "cTk1UjuioeTlfGYZrrimExB+bScH75adUMRIi2UOMxG1kw4y+9RW/IVoMl4e620VxZad0ARX2gUqVjYO7KPVt3d" +
       "yKhZS3dkcvfBisBhP1XH9B33VqHG9SHnbnQXdBUaCgKAfxome8UmBKfe+naTsE5fkvjb/do3/dD6l4sGBwFCnKR" +
       "dln4XpM03zLpoHFao8zOwt8l/uP3qUIxmCYv9A7m69Ms+5/pCkTu/rK4mRDsfhZ0QLfbzVI6zQFOKF/rwsfBtFe" +
       "WlWtcuJMKlXdD8TXWElTzgh7JS4qhFzreL0c1mI0GCj+Aws0usZh7dLIVPnlgZcBhgy1SSDQMQ==";
       
   // Optional parameters
   private const string OptionalDeviceId = "myCSharpDevice";
   private const ProvisioningStatus OptionalProvisioningStatus = ProvisioningStatus.Enabled;
   ```

   * Vervang de waarde van de tijdelijke aanduiding `ProvisioningServiceConnectionString` door de connection string van de inrichtings service waarvoor u de inschrijving wilt maken.

   * U kunt desgewenst de registratie-ID, goedkeuringssleutel, apparaat-ID en inrichtingsstatus wijzigen.

   * Als u deze Quick Start gebruikt in combi natie met het [maken en inrichten van C# een GEsimuleerd TPM-apparaat met apparaat SDK](quick-create-simulated-device-tpm-csharp.md) Quick Start om een gesimuleerd apparaat in te richten, vervangt u de goedkeurings sleutel en registratie-id door de waarden die u hebt genoteerd in die Snelstartgids. U kunt de apparaat-ID vervangen door de waarde die in die Snelstartgids is voorgesteld, uw eigen waarde gebruiken of de standaard waarde in dit voor beeld gebruiken.

1. Voeg de volgende methode toe aan de klasse `Program`.  Deze code maakt afzonderlijke inschrijvings vermelding en roept vervolgens de `CreateOrUpdateIndividualEnrollmentAsync`-methode op de `ProvisioningServiceClient` om de individuele inschrijving aan de inrichtings service toe te voegen.

   ```csharp
   public static async Task RunSample()
   {
       Console.WriteLine("Starting sample...");

       using (ProvisioningServiceClient provisioningServiceClient =
               ProvisioningServiceClient.CreateFromConnectionString(ProvisioningConnectionString))
       {
           #region Create a new individualEnrollment config
           Console.WriteLine("\nCreating a new individualEnrollment...");
           Attestation attestation = new TpmAttestation(TpmEndorsementKey);
           IndividualEnrollment individualEnrollment =
                   new IndividualEnrollment(
                           RegistrationId,
                           attestation);

           // The following parameters are optional. Remove them if you don't need them.
           individualEnrollment.DeviceId = OptionalDeviceId;
           individualEnrollment.ProvisioningStatus = OptionalProvisioningStatus;
           #endregion

           #region Create the individualEnrollment
           Console.WriteLine("\nAdding new individualEnrollment...");
           IndividualEnrollment individualEnrollmentResult =
               await provisioningServiceClient.CreateOrUpdateIndividualEnrollmentAsync(individualEnrollment).ConfigureAwait(false);
           Console.WriteLine("\nIndividualEnrollment created with success.");
           Console.WriteLine(individualEnrollmentResult);
           #endregion
        
       }
   }
   ```

1. Vervang tot slot de hoofd tekst van de `Main` methode door de volgende regels:

   ```csharp
   RunSample().GetAwaiter().GetResult();
   Console.WriteLine("\nHit <Enter> to exit ...");
   Console.ReadLine();
   ```

1. Bouw de oplossing.

## <a name="run-the-individual-enrollment-sample"></a>Het voorbeeld van de afzonderlijke inschrijving uitvoeren
  
Voer het voorbeeld uit in Visual Studio om de afzonderlijke registratie voor uw TPM-apparaat te maken.

Er wordt een opdracht prompt venster weer gegeven en u kunt de bevestigings berichten weer geven. Wanneer het maken is voltooid, worden in het opdracht prompt venster de eigenschappen van de nieuwe afzonderlijke registratie weer gegeven.

U kunt controleren of de afzonderlijke inschrijving is gemaakt. Ga naar de Device Provisioning Service-samen vatting en selecteer **inschrijvingen beheren**en selecteer vervolgens **afzonderlijke inschrijvingen**. U ziet nu een nieuwe registratievermelding die overeenkomt met de registratie-ID die u in het voorbeeld hebt gebruikt.

![Eigenschappen van de inschrijving in de portal](media/quick-enroll-device-tpm-csharp/verify-enrollment-portal-vs2019.png)

Selecteer de vermelding om de goedkeurings sleutel en andere eigenschappen voor de vermelding te controleren.

Als u de stappen hebt uitgevoerd in het apparaat [een gesimuleerd TPM maken en inrichten met C# behulp van Device SDK](quick-create-simulated-device-tpm-csharp.md) Quick Start, kunt u door gaan met de overige stappen in deze Snelstartgids om uw gesimuleerde apparaat in te schrijven. Volg niet de stappen voor het maken van een afzonderlijke inschrijving via Azure Portal.

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent het C# service voorbeeld te verkennen, moet u de resources die u in deze Quick Start hebt gemaakt, niet opschonen. Gebruik anders de volgende stappen om alle resources te verwijderen die door deze Quick start zijn gemaakt.

1. Sluit het C# voorbeeld venster uitvoer op de computer.

1. Navigeer naar uw Device Provisioning Service in de Azure Portal, selecteer **inschrijvingen beheren**en selecteer vervolgens het tabblad **afzonderlijke inschrijvingen** . Schakel het selectie vakje in naast de *registratie-id* voor de inschrijvings vermelding die u hebt gemaakt met behulp van deze Quick Start. Klik vervolgens op de knop **verwijderen** boven aan het deel venster.

1. Als u de stappen in [een gesimuleerd TPM-apparaat maken en inrichten C# met apparaat-SDK](quick-create-simulated-device-tpm-csharp.md) hebt gevolgd om een gesimuleerd TPM-apparaat te maken, voert u de volgende stappen uit:

    1. Sluit het venster van de TPM-simulator en het voorbeelduitvoervenster voor het gesimuleerde apparaat.

    1. Navigeer in Azure Portal naar de IoT Hub waar het apparaat is ingericht. Selecteer **IOT-apparaten**in het menu onder **verkenners**, schakel het selectie vakje in naast de *apparaat-id* van het apparaat dat u in deze Quick Start hebt geregistreerd en druk op de knop **verwijderen** boven aan het deel venster.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u programmatisch een afzonderlijke inschrijvings vermelding gemaakt voor een TPM-apparaat. U hebt eventueel een door TPM gesimuleerd apparaat op uw computer gemaakt en dit ingericht voor uw IoT-hub met behulp van de Azure-IoT Hub Device Provisioning Service. Voor meer informatie over device provisioning, gaat u verder met de zelfstudie voor het instellen van Device Provisioning Service in Azure Portal.

> [!div class="nextstepaction"]
> [Zelfstudies over Azure IoT Hub Device Provisioning Service](./tutorial-set-up-cloud.md)
