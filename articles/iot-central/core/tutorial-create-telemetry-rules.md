---
title: 'Zelf studie: regels maken en beheren in uw Azure IoT Central-toepassing'
description: Deze zelf studie laat zien hoe u met Azure IoT Central regels uw apparaten in bijna realtime kunt bewaken en automatisch acties kunt aanroepen, zoals het verzenden van een e-mail bericht wanneer de regel wordt geactiveerd.
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: f61a41fa89c7006341db928472f6b20d272bc550
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77167424"
---
# <a name="tutorial-create-a-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Zelf studie: een regel maken en meldingen instellen in uw Azure IoT Central-toepassing

*Dit artikel is van toepassing op operators, opbouwfuncties en beheerders.*

U kunt Azure IoT Central gebruiken om uw verbonden apparaten op afstand te bewaken. Met Azure IoT Central-regels kunt u uw apparaten in bijna realtime bewaken en automatisch acties aanroepen, zoals het verzenden van een e-mail bericht. Met slechts enkele muis klikken kunt u een voor waarde definiëren voor het controleren van telemetrie van uw apparaten en het configureren van een bijbehorende actie. In dit artikel wordt uitgelegd hoe u regels kunt maken voor het bewaken van de telemetrie die door het apparaat wordt verzonden.

Apparaten gebruiken telemetrie om numerieke gegevens van het apparaat te verzenden. Een regel die wordt geactiveerd wanneer de telemetrie van het geselecteerde apparaat een opgegeven drempel overschrijdt.

In deze zelf studie maakt u een regel voor het verzenden van een e-mail bericht wanneer de Tempe ratuur in een gesimuleerd omgevings sensor 70&deg; F.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Een regel maken
> * Een e-mail actie toevoegen

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u de [toepassing een Azure-IOT Central maken](./quick-deploy-iot-central.md) volt ooien en [een gesimuleerd apparaat toevoegen aan de](./quick-create-pnp-device.md) Snelstartgids van de IOT Central toepassing om de **MXChip IOT DevKit** -apparaatprofiel te maken waarmee u kunt werken.

## <a name="create-a-rule"></a>Een regel maken

Voor het maken van een telemetrie-regel moet er ten minste één telemetrie-meting zijn gedefinieerd. In deze zelf studie wordt een omgevings sensor apparaat gebruikt dat telemetrie van de Tempe ratuur en de vochtigheid verzendt. U hebt deze apparaat sjabloon toegevoegd en een gesimuleerd apparaat gemaakt in het Snelstartgids [een gesimuleerd apparaat toevoegen aan uw IOT Central-toepassing](./quick-create-pnp-device.md) Quick Start. De regel controleert de door het apparaat gemelde Tempe ratuur en verzendt een e-mail wanneer deze meer dan 70 graden gaat.

1. Selecteer in het linkerdeel venster **regels**.

1. Als u nog geen regels hebt gemaakt, ziet u het volgende scherm:

    ![Nog geen regels](media/tutorial-create-telemetry-rules/rules-landing-page1.png)

1. Selecteer **+** om een nieuwe regel toe te voegen.

1. Voer de naam _temperatuur monitor_ in om de regel te identificeren en druk op ENTER.

1. Selecteer de sjabloon **MXChip IOT DevKit** -apparaat. De regel is standaard automatisch van toepassing op alle apparaten die aan de sjabloon voor het apparaat zijn gekoppeld. Als u wilt filteren op een subset van de apparaten, selecteert u **+ filter** en gebruikt u de eigenschappen van het apparaat om de apparaten te identificeren. Als u de regel wilt uitschakelen, schakelt u de knop **ingeschakeld/uitgeschakeld** in de regel header in:

    ![Filters en inschakelen](media/tutorial-create-telemetry-rules/device-filters.png)

### <a name="configure-the-rule-conditions"></a>De regel voorwaarden configureren

Voor waarden worden de criteria gedefinieerd die de regel controleert. In deze zelf studie configureert u de regel die moet worden geactiveerd wanneer de Tempe ratuur 70&deg; F overschrijdt.

1. Selecteer **Tempe ratuur** in de vervolg keuzelijst **telemetrie** .

1. Vervolgens kiest u **groter dan** als de **operator** en voert u _70_ in als **waarde**.

    ![Voorwaarde](media/tutorial-create-telemetry-rules/condition-filled-out1.png)

1. U kunt desgewenst een **tijd aggregatie**instellen. Wanneer u een tijd aggregatie selecteert, moet u ook een samenvoegings type selecteren, zoals het gemiddelde of de som van de aggregatie vervolg keuzelijst.

    * Zonder aggregatie wordt de regel geactiveerd voor elk telemetrie-gegevens punt dat voldoet aan de voor waarde. Als de regel bijvoorbeeld zo is geconfigureerd dat deze wordt geactiveerd wanneer de Tempe ratuur hoger is dan 70, wordt de regel bijna direct geactiveerd wanneer het apparaat de Tempe ratuur > 70.
    * Met aggregatie wordt de regel geactiveerd als de cumulatieve waarde van de telemetriegegevens van de gegevens punten in het tijd venster voldoet aan de voor waarde. Als de regel bijvoorbeeld zo is geconfigureerd dat deze wordt geactiveerd wanneer de Tempe ratuur hoger is dan 70, wordt tijd aggregatie ingesteld op 10 minuten en het aggregatie type gemiddelde, vervolgens wordt de regel geactiveerd wanneer het apparaat een gemiddelde Tempe ratuur > 70, berekend over een 10 minuten rapporteert bereik.

     ![Aggregatie voorwaarde](media/tutorial-create-telemetry-rules/aggregate-condition-filled-out1.png)

U kunt meerdere voor waarden aan een regel toevoegen door **+ voor waarde**te selecteren. Wanneer er meerdere voor waarden worden opgegeven, moeten aan alle voor waarden worden voldaan om de regel te activeren. Elke voor waarde wordt gekoppeld door een impliciete `AND`-component. Als u tijd aggregatie met meerdere voor waarden gebruikt, moeten alle telemetrie-waarden worden geaggregeerd.

### <a name="configure-actions"></a>Acties configureren

Nadat u de voor waarde hebt gedefinieerd, stelt u de acties in die moeten worden uitgevoerd wanneer de regel wordt geactiveerd. Acties worden aangeroepen wanneer de voor waarden die in de regel zijn opgegeven, worden geëvalueerd als waar.

1. Selecteer **+ e-mail** in de sectie **acties** .

1. Voer een _temperatuur waarschuwing_ in als weergave naam voor de actie, uw e-mail adres in het veld **aan** en _Controleer het apparaat._ een notitie die moet worden weer gegeven in de hoofd tekst van het e-mail bericht.

    > [!NOTE]
    > E-mail berichten worden alleen verzonden naar gebruikers die zijn toegevoegd aan de toepassing en die ten minste één keer zijn aangemeld. Meer informatie over [gebruikers beheer](howto-administer.md) in azure IOT Central.

   ![Actie configureren](media/tutorial-create-telemetry-rules/configure-action1.png)

1. Als u de actie wilt opslaan, kiest u **gereed**. U kunt meerdere acties toevoegen aan een regel.

1. Klik op **Opslaan**om de regel op te slaan. De regel is binnen enkele minuten Live en begint met de bewaking van de telemetrie die naar uw toepassing wordt verzonden. Wanneer aan de voor waarde die in de regel is opgegeven, wordt voldaan, wordt de geconfigureerde e-mail actie door de regel geactiveerd.

Na een tijdje ontvangt u een e-mail bericht wanneer de regel wordt geactiveerd:

![Voor beeld-e-mail](media/tutorial-create-telemetry-rules/email.png)

## <a name="delete-a-rule"></a>Een regel verwijderen

Als u een regel niet meer nodig hebt, verwijdert u deze door de regel te openen en **verwijderen**te kiezen.

## <a name="enable-or-disable-a-rule"></a>Een regel in-of uitschakelen

Kies de regel die u wilt in-of uitschakelen. Scha kelen tussen de knop **inschakelen** of **uitschakelen** in de regel om de regel in of uit te scha kelen voor alle apparaten die in de regel zijn ingedeeld.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Een regel voor een apparaat in-of uitschakelen

Kies de regel die u wilt in-of uitschakelen. Voeg in de sectie **bereiken** een filter toe om een bepaald apparaat in de sjabloon op te nemen of uit te sluiten.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

* Een regel op basis van telemetrie maken
* Een actie toevoegen

Nu u een regel op basis van drempel hebt gedefinieerd, is de voorgestelde volgende stap om te leren hoe u:

> [!div class="nextstepaction"]
> [Continue gegevens export configureren](./howto-export-data.md).
