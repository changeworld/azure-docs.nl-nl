---
title: Beveiligings aanbevelingen voor Azure IoT | Microsoft Docs
description: In dit artikel vindt u een overzicht van de extra stappen voor beveiliging in uw Azure IoT Hub-oplossing.
author: dsk-2015
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: article
ms.date: 11/13/2019
ms.author: dkshir
ms.custom: security-recommendations
ms.openlocfilehash: 0ada9a520a5be56444a1c3e746a68dbcf9275686
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048465"
---
# <a name="security-recommendations-for-azure-internet-of-things-iot-deployment"></a>Beveiligings aanbevelingen voor de implementatie van Azure Internet of Things (IoT)

Dit artikel bevat beveiligings aanbevelingen voor IoT. Als u deze aanbevelingen implementeert, kunt u voldoen aan uw beveiligings verplichtingen, zoals beschreven in ons gedeelde verantwoordelijkheids model. Lees de [gedeelde verantwoordelijkheden voor Cloud Computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)voor meer informatie over wat micro soft doet aan de verantwoordelijkheden van de service provider.

Enkele van de aanbevelingen in dit artikel kunnen automatisch worden bewaakt door Azure Security Center. Azure Security Center is de eerste verdedigings linie bij het beveiligen van uw resources in Azure. De beveiligings status van uw Azure-resources wordt periodiek geanalyseerd om mogelijke beveiligings problemen te identificeren. Vervolgens krijgt u aanbevelingen over hoe u deze kunt aanpakken.

- Zie [beveiligings aanbevelingen in azure Security Center](../security-center/security-center-recommendations.md)voor meer informatie over Azure Security Center aanbevelingen.
- Zie [Wat is er Azure Security Center?](../security-center/security-center-intro.md) voor informatie over Azure Security Center.

## <a name="general"></a>Algemeen

| Aanbeveling | Opmerkingen | Ondersteund door ASC |
|-|----|--|
| Blijf up-to-date | Gebruik de nieuwste versies van ondersteunde platforms, programmeer talen, protocollen en frameworks. | - |
| Verificatie sleutels veilig blijven | Zorg ervoor dat de apparaat-Id's en de bijbehorende verificatie sleutels fysiek veilig zijn na de implementatie. Zo voor komt u dat een schadelijk apparaat als een geregistreerd apparaat wordt beschouwd. | - |
| Apparaat-Sdk's zo mogelijk gebruiken | Apparaat-Sdk's implementeren diverse beveiligings functies, zoals versleuteling, verificatie, enzovoort, om u te helpen bij het ontwikkelen van een robuuste en veilige Device-toepassing. Zie [Azure IOT hub Sdk's begrijpen en gebruiken](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) voor meer informatie. | - |

## <a name="identity-and-access-management"></a>Identiteits- en toegangsbeheer 

| Aanbeveling | Opmerkingen | Ondersteund door ASC |
|-|----|--|
| Toegangs beheer voor de hub bepalen | [Begrijp en definieer het type toegang](iot-security-deployment.md#securing-the-cloud) dat elk onderdeel in uw IOT hub oplossing heeft, op basis van de functionaliteit. De toegestane machtigingen zijn *REGI ster lezen*, *RegistryReadWrite*, *ServiceConnect*en *DeviceConnect*. Standaard [beleid voor gedeelde toegang in uw IOT-hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#access-control-and-permissions) kan ook helpen bij het definiëren van de machtigingen voor elk onderdeel op basis van de bijbehorende rol. | - |
| Toegangs beheer voor back-end-services bepalen | Gegevens die door uw IoT Hub-oplossing zijn opgenomen, kunnen worden gebruikt door andere Azure-Services, zoals [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/), [Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/), [app service](https://docs.microsoft.com/azure/app-service/), [Logic apps](https://docs.microsoft.com/azure/logic-apps/)en [Blob-opslag](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction). Zorg ervoor dat u de juiste toegangs machtigingen begrijpt en toestaat, zoals gedocumenteerd voor deze services. | - |

## <a name="data-protection"></a>Gegevensbeveiliging

| Aanbeveling | Opmerkingen | Ondersteund door ASC |
|-|----|--|
| Authenticatie van het apparaat beveiligen | Zorg voor beveiligde communicatie tussen uw apparaten en uw IoT-hub met behulp van [een unieke identiteits sleutel of een beveiligings token](iot-security-deployment.md#iot-hub-security-tokens)of [een X. 509-certificaat op het apparaat](iot-security-deployment.md#x509-certificate-based-device-authentication) voor elk apparaat. Gebruik de juiste methode voor het [gebruik van beveiligings tokens op basis van het gekozen protocol (MQTT, AMQP of https)](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security). | - |
| Communicatie met apparaat beveiligen | IoT Hub beveiligt de verbinding met de apparaten die gebruikmaken van Transport Layer Security (TLS) Standard, die ondersteuning bieden voor versies 1,2 en 1,0. Gebruik [TLS 1,2](https://tools.ietf.org/html/rfc5246) om maximale beveiliging te garanderen. | - |
| Service communicatie beveiligen | IoT Hub biedt eind punten om verbinding te maken met back-end-services, zoals [Azure Storage](/azure/storage/) of [Event hubs](/azure/event-hubs) alleen het TLS-protocol gebruiken, en er wordt geen eind punt weer gegeven op een niet-versleuteld kanaal. Zodra deze gegevens deze back-upservices voor opslag of analyse bereiken, moet u ervoor zorgen dat u de juiste beveiligings-en versleutelings methoden voor die service hanteert en gevoelige informatie op de backend beveiligt. | - |

## <a name="networking"></a>Netwerken

| Aanbeveling | Opmerkingen | Ondersteund door ASC |
|-|----|--|
| Toegang tot uw apparaten beveiligen | Zorg ervoor dat de poorten op uw apparaten een bare minimum hebben om ongewenste toegang te voor komen. Daarnaast bouwt u mechanismen op om het apparaat te voor komen of fysiek te knoeien. Lees de [Best practices voor IOT-beveiliging](iot-security-best-practices.md) voor meer informatie. | - |
| Beveiligde hardware bouwen | Beveiligings functies zoals versleutelde opslag of Trusted Platform Module (TPM) opnemen om apparaten en infra structuur beter te beveiligen. Zorg ervoor dat het besturings systeem van het apparaat en de Stuur Programma's zijn bijgewerkt naar de meest recente versies en Installeer anti virus-en antimalware-mogelijkheden. Lees [IOT-beveiligings architectuur](iot-security-architecture.md) om te begrijpen hoe dit kan helpen bij het oplossen van verschillende beveiligings Risico's. | - |

## <a name="monitoring"></a>Bewaking

| Aanbeveling | Opmerkingen | Ondersteund door ASC |
|-|----|--|
| Onbevoegde toegang tot uw apparaten controleren |  De logboek functie van het besturings systeem van het apparaat gebruiken om inbreuken op de beveiliging of fysieke manipulatie van het apparaat of de poorten te controleren. | - |
| Uw IoT-oplossing bewaken vanuit de Cloud | Bewaak de algehele status van uw IoT Hub oplossing met behulp [van de metrische gegevens in azure monitor](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics). | - |
| Diagnostische gegevens instellen | Bekijk nauw keurig uw bewerkingen door gebeurtenissen in uw oplossing te registreren en vervolgens de diagnostische logboeken naar Azure Monitor te verzenden om inzicht te krijgen in de prestaties. Lees [monitor en diagnose problemen in uw IOT-hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitor-resource-health) voor meer informatie. | - |

## <a name="next-steps"></a>Volgende stappen

Voor geavanceerde scenario's met Azure IoT moet u mogelijk aanvullende beveiligings vereisten overwegen. Zie [IOT-beveiligings architectuur](iot-security-architecture.md) voor meer informatie.

