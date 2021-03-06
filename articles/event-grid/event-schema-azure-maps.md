---
title: Azure Event Grid Azure Maps-gebeurtenis schema
description: Hierin worden de eigenschappen en schema's beschreven die voor Azure Maps gebeurtenissen worden gegeven met Azure Event Grid
services: event-grid
author: femila
ms.service: event-grid
ms.topic: reference
ms.date: 02/08/2019
ms.author: femila
ms.openlocfilehash: 9acef524521e8fac6ce6f8f61e5ff3fbbb81d18d
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77486356"
---
# <a name="azure-event-grid-event-schema-for-azure-maps"></a>Azure Event Grid-gebeurtenis schema voor Azure Maps

In dit artikel vindt u de eigenschappen en het schema voor Azure Maps gebeurtenissen. Zie [Azure Event grid-gebeurtenis schema](https://docs.microsoft.com/azure/event-grid/event-schema)voor een inleiding tot gebeurtenis schema's.

## <a name="available-event-types"></a>Beschik bare gebeurtenis typen

Een Azure Maps account verzendt de volgende gebeurtenis typen:

| Gebeurtenis type | Beschrijving |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | Deze gebeurtenis treedt op wanneer de ontvangen coördinaten van buiten een bepaalde geofence zijn verplaatst naar in |
| Microsoft.Maps.GeofenceExited | Deze gebeurtenis treedt op wanneer de ontvangen coördinaten van binnen een bepaalde geofence naar buiten zijn verplaatst |
| Microsoft.Maps.GeofenceResult | Deze gebeurtenis treedt elke keer op wanneer een geoomheinings query een resultaat retourneert, ongeacht de status |

## <a name="event-examples"></a>Gebeurtenis voorbeelden

In het volgende voor beeld wordt het schema van een **GeofenceEntered** -gebeurtenis weer gegeven

```JSON
{   
   "id":"7f8446e2-1ac7-4234-8425-303726ea3981", 
   "topic":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
   "subject":"/spatial/geofence/udid/{udid}/id/{eventId}", 
   "data":{   
      "geometries":[   
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":-999.0, 
            "nearestLat":47.618786, 
            "nearestLon":-122.132151 
         } 
      ], 
      "expiredGeofenceGeometryId":[   
      ], 
      "invalidPeriodGeofenceGeometryId":[   
      ] 
   }, 
   "eventType":"Microsoft.Maps.GeofenceEntered", 
   "eventTime":"2018-11-08T00:54:17.6408601Z", 
   "metadataVersion":"1", 
   "dataVersion":"1.0" 
}
```

In het volgende voor beeld wordt schema voor **GeofenceResult** weer gegeven 

```JSON
{   
   "id":"451675de-a67d-4929-876c-5c2bf0b2c000", 
   "topic":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
   "subject":"/spatial/geofence/udid/{udid}/id/{eventId}", 
   "data":{   
      "geometries":[   
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"1", 
            "distance":999.0, 
            "nearestLat":47.609833, 
            "nearestLon":-122.148274 
         }, 
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":999.0, 
            "nearestLat":47.621954, 
            "nearestLon":-122.131841 
         } 
      ], 
      "expiredGeofenceGeometryId":[   
      ], 
      "invalidPeriodGeofenceGeometryId":[   
      ] 
   }, 
   "eventType":"Microsoft.Maps.GeofenceResult", 
   "eventTime":"2018-11-08T00:52:08.0954283Z", 
   "metadataVersion":"1", 
   "dataVersion":"1.0" 
}
```

## <a name="event-properties"></a>Gebeurtenis eigenschappen

Een gebeurtenis heeft de volgende gegevens op het hoogste niveau:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| onderwerp | tekenreeks | Volledige bronpad naar de bron van de gebeurtenis. Dit veld kan niet worden geschreven. Event Grid levert deze waarde. |
| subject | tekenreeks | Het door de uitgever gedefinieerde pad naar het gebeurtenis onderwerp. |
| eventType | tekenreeks | Een van de geregistreerde gebeurtenis typen voor deze gebeurtenis bron. |
| eventTime | tekenreeks | Het tijdstip waarop de gebeurtenis is gegenereerd op basis van de UTC-tijd van de provider. |
| id | tekenreeks | De unieke id voor de gebeurtenis. |
| data | object | Gegevens van geoomheinings gebeurtenis. |
| dataVersion | tekenreeks | De schema versie van het gegevens object. De uitgever definieert de schema versie. |
| metadataVersion | tekenreeks | De schema versie van de meta gegevens van de gebeurtenis. Event Grid definieert het schema van de eigenschappen op het hoogste niveau. Event Grid levert deze waarde. |

Het gegevens object heeft de volgende eigenschappen:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| apiCategory | tekenreeks | De API-categorie van de gebeurtenis. |
| apiName | tekenreeks | De API-naam van de gebeurtenis. |
| issues | object | Geeft een lijst van problemen die zijn opgetreden tijdens de verwerking. Als er problemen worden geretourneerd, worden er geen geometrische elementen geretourneerd met het antwoord. |
| responseCode | getal | HTTP-antwoord code |
| geoelementen | object | Hiermee wordt een lijst weer gegeven met geometrieën die de coördinaten positie bevatten of de searchBuffer rond de positie overlappen. |

Het fout object wordt geretourneerd als er een fout optreedt in de Maps-API. Het object Error heeft de volgende eigenschappen:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| error | ErrorDetails |Dit object wordt geretourneerd als er een fout optreedt in de Maps-API  |

Het error Details-object wordt geretourneerd als er een fout optreedt in de Maps-API. De error Details of het object heeft de volgende eigenschappen:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| code | tekenreeks | De HTTP-status code. |
| message | tekenreeks | Indien beschikbaar, een door de mens lees bare beschrijving van de fout. |
| innererror | InnerError | Indien beschikbaar, een object met servicespecifieke informatie over de fout. |

De InnerError is een object dat informatie over de servicespecifieke bevat over de fout. Het object InnerError heeft de volgende eigenschappen: 

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| code | tekenreeks | Het fout bericht. |

Het object geometrische elementen bevat een lijst met geometrie-Id's van de geofences die zijn verlopen ten opzichte van de gebruikers tijd in de aanvraag. Het object geometrische elementen heeft geometrie-items met de volgende eigenschappen: 

| Eigenschap | Type | Beschrijving |
|:-------- |:---- |:----------- |
| deviceid | tekenreeks | ID van het apparaat. |
| afstand | tekenreeks | <p>Afstand van de coördinaat tot de dichtstbijzijnde rand van de geofence. Positief betekent dat de coördinaat zich buiten de geofence bevindt. Als de coördinaat zich buiten de geofence bevindt, maar meer dan de waarde van searchBuffer verwijderd van de dichtstbijzijnde geofence-rand, is de waarde 999. Negatief betekent dat de coördinaat zich binnen de geofence bevindt. Als de coördinaat zich binnen de veelhoek bevindt, maar meer dan de waarde van searchBuffer verwijderd van de dichtstbijzijnde rand van de geoomheining, is de waarde-999. Een waarde van 999 betekent dat het vertrouwen van de coördinaten goed buiten de geofence ligt. Een waarde van-999 betekent dat het vertrouwen van de coördinaten goed is binnen de geofence.<p> |
| geometryid |tekenreeks | De unieke id geeft de geometrie van de geofence aan. |
| nearestlat | getal | De breedte graad van het dichtstbijzijnde punt van de geometrie. |
| nearestlon | getal | De lengte graad van het dichtstbijzijnde punt van de geometrie. |
| udId | tekenreeks | De unieke id die wordt geretourneerd door de gebruikers-upload service bij het uploaden van een geofence. Wordt niet opgenomen in een geoomheining van post-API. |

Het gegevens object heeft de volgende eigenschappen:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| expiredGeofenceGeometryId | string[] | Lijsten van de geometrie-ID van de geofence die is verlopen ten opzichte van de gebruikers tijd in de aanvraag. |
| geoelementen | geometrieën [] |Hiermee wordt een lijst weer gegeven met geometrieën die de coördinaten positie bevatten of de searchBuffer rond de positie overlappen. |
| invalidPeriodGeofenceGeometryId | string[]  | Lijsten van de geometrie-ID van de geofence die zich in een ongeldige periode bevindt ten opzichte van de gebruikers tijd in de aanvraag. |
| isEventPublished | booleaans | Waar als ten minste één gebeurtenis is gepubliceerd naar de abonnee van de Azure Maps-gebeurtenis, False als er geen gebeurtenis is gepubliceerd naar de abonnee van de Azure Maps-gebeurtenis. |

## <a name="next-steps"></a>Volgende stappen

* Zie [Wat is Event grid?](overview.md) voor een inleiding tot Azure Event grid.
* Zie [Event grid Subscription schema](subscription-creation-schema.md)voor meer informatie over het maken van een Azure Event grid-abonnement.