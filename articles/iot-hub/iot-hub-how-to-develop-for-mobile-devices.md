---
title: Ontwikkel voor mobiele apparaten met Azure IoT Sdk's | Microsoft Docs
description: "Ontwikkelaars handleiding: meer informatie over het ontwikkelen van mobiele apparaten met behulp van Azure IoT Hub Sdk's."
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/16/2018
ms.author: robinsh
ms.openlocfilehash: 945b02003a443c04e692fdc06ca5714de362d074
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883082"
---
# <a name="develop-for-mobile-devices-using-azure-iot-sdks"></a>Ontwikkel voor mobiele apparaten met Azure IoT Sdk's

Dingen in de Internet of Things kunnen verwijzen naar een breed scala aan apparaten met verschillende mogelijkheden: Sens oren, micro controllers, smart-apparaten, industriële gateways en zelfs mobiele apparaten.  Een mobiel apparaat kan een IoT-apparaat zijn, waarbij het apparaat-naar-Cloud-telemetrie wordt verzonden en door de Cloud wordt beheerd.  Het kan ook het apparaat met een back-end-service toepassing zijn, waarmee andere IoT-apparaten worden beheerd.  In beide gevallen kunnen [Azure IOT hub sdk's](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) worden gebruikt voor het ontwikkelen van toepassingen die geschikt zijn voor mobiele apparaten.  

## <a name="develop-for-native-ios-platform"></a>Ontwikkelen voor systeem eigen iOS-platform

Azure IoT Hub Sdk's bieden systeem eigen iOS-platform ondersteuning via Azure IoT Hub C SDK.  U kunt dit beschouwen als een iOS-SDK die u kunt opnemen in uw SWIFT-of objectief C XCode-project.  Er zijn twee manieren om de C SDK te gebruiken op iOS:

* Gebruik de CocoaPod-bibliotheken in XCode-project rechtstreeks.  
* Down load de bron code voor C SDK en bouwt voor het iOS-platform volgens de [Build-instructie](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) voor MacOS.  

Azure IoT Hub C SDK is in C99 geschreven voor een maximale portabiliteit voor verschillende platforms.  Het poort proces omvat het schrijven van een smalle acceptatie laag voor de platformspecifieke onderdelen, die hier kan worden gevonden voor [IOS](https://github.com/Azure/azure-c-shared-utility/tree/master/pal/ios-osx).  De functies van de C SDK kunnen worden gebruikt op iOS-platform, met inbegrip van de Azure IoT Hub primitieven ondersteunde en SDK-specifieke functies, zoals beleid voor opnieuw proberen voor de betrouw baarheid van het netwerk.  De interface voor iOS SDK is ook vergelijkbaar met de interface voor Azure IoT Hub C SDK.  

In deze documentatie wordt uitgelegd hoe u een toepassings-of service toepassing op een iOS-apparaat kunt ontwikkelen:

* [Snelstart: Telemetrie van een apparaat naar een IoT-hub verzenden](quickstart-send-telemetry-ios.md)  
* [Berichten vanuit de Cloud naar uw apparaat verzenden met IoT hub](iot-hub-ios-swift-c2d.md) 

### <a name="develop-with-azure-iot-hub-cocoapod-libraries"></a>Ontwikkelen met Azure IoT Hub CocoaPod-bibliotheken

Met Azure IoT Hub Sdk's wordt een set van objectief C CocoaPod-bibliotheken voor iOS-ontwikkeling vrijgegeven.  Zie [CocoaPods voor Microsoft Azure IOT voor](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/ios/CocoaPods.md)een overzicht van de meest recente lijst met CocoaPod-bibliotheken.  Zodra de relevante bibliotheken zijn opgenomen in uw XCode-project, zijn er twee manieren om IoT Hub gerelateerde code te schrijven:

* Doel functie C: Als uw project is geschreven in doel-C, kunt u Api's rechtstreeks aanroepen vanuit Azure IoT Hub C SDK.  Als uw project in SWIFT is geschreven, kunt u aanroepen `@objc func` voordat u de functie maakt en alle logica met betrekking tot Azure-IOT hub met c of de code van de doel groep c te schrijven.  Een reeks voor beelden die beide worden weer gegeven, vindt u in de voor [beeld-opslag plaats](https://github.com/Azure-Samples/azure-iot-samples-ios).  

* C-voor beelden opnemen: Als u een C Device-toepassing hebt geschreven, kunt u deze rechtstreeks in uw XCode-project raadplegen:
    * Voeg het voor beeld. c-bestand toe aan uw XCode-project vanuit XCode.  
    * Voeg het header-bestand toe aan uw afhankelijkheid.  Een header-bestand wordt als voor beeld opgenomen in de voor [beeld-opslag plaats](https://github.com/Azure-Samples/azure-iot-samples-ios) . Ga voor meer informatie naar de documentatie pagina van Apple voor [doel stelling-C](https://developer.apple.com/documentation/objectivec).

## <a name="develop-for-android-platform"></a>Ontwikkelen voor Android-platform
Azure IoT Hub Java SDK ondersteunt Android-platform.  Voor de geteste API-versie gaat u naar onze ondersteunings pagina voor het [platform](iot-hub-device-sdk-platform-support.md) voor de meest recente update.

Deze documenten begeleiden u bij het ontwikkelen van een toepassings-of service toepassing op een Android-apparaat met behulp van Gradle en Android Studio:

* [Snelstart: Telemetrie van een apparaat naar een IoT-hub verzenden](quickstart-send-telemetry-android.md)  
* [Snelstart: Een apparaat beheren](quickstart-control-device-android.md) 

## <a name="next-steps"></a>Volgende stappen

* [Naslag informatie over IoT Hub REST API](https://docs.microsoft.com/rest/api/iothub/)
* [Azure IoT C SDK-bron code](https://github.com/Azure/azure-iot-sdk-c)
