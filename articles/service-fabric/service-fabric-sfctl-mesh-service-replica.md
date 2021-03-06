---
title: Azure Service Fabric CLI-sfctl-mesh service-replica
description: Meer informatie over sfctl, de Azure Service Fabric-opdracht regel interface. Bevat een lijst met opdrachten voor het ophalen van replica Details voor een toepassings bronnen.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: ac604ec0a854964746ef34627b054e853662265f
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905954"
---
# <a name="sfctl-mesh-service-replica"></a>sfctl mesh service-replica
Replica Details ophalen en replica's van een bepaalde service in een toepassings bron weer geven.

## <a name="commands"></a>Opdrachten

|Opdracht|Beschrijving|
| --- | --- |
| list | Een lijst met alle replica's van een service. |
| weergeven | Hiermee wordt de opgegeven replica van de service van een toepassing opgehaald. |

## <a name="sfctl-mesh-service-replica-list"></a>sfctl-mesh service-replica lijst
Een lijst met alle replica's van een service.

Hiermee wordt de informatie opgehaald over alle replica's van een service. De informatie bevat de beschrijving en andere eigenschappen van de service replica.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --app-naam--Application name [required] | De naam van de toepassing. |
| --Service-naam [vereist] | De naam van de service. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --Help-h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden\: JSON, jsonc, Table, TSV.  Standaard\: JSON. |
| --query | JMESPath-query reeks. Zie http\://jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-mesh-service-replica-show"></a>sfctl-mesh service-replica show
Hiermee wordt de opgegeven replica van de service van een toepassing opgehaald.

Hiermee wordt de informatie opgehaald over de service replica met de opgegeven naam. De informatie bevat de beschrijving en andere eigenschappen van de service replica.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --app-naam--Application name [required] | De naam van de toepassing. |
| --naam-n [vereist] | De naam van de service replica. |
| --Service-naam [vereist] | De naam van de service. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --Help-h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden\: JSON, jsonc, Table, TSV.  Standaard\: JSON. |
| --query | JMESPath-query reeks. Zie http\://jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |


## <a name="next-steps"></a>Volgende stappen
- [Stel](service-fabric-cli.md) de service Fabric cli in.
- Meer informatie over het gebruik van de Service Fabric CLI met behulp van de [voorbeeld scripts](/azure/service-fabric/scripts/sfctl-upgrade-application).