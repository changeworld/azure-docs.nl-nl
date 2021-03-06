---
title: Ondersteunde kaart stijlen | Microsoft Azure kaarten
description: In dit artikel vindt u informatie over verschillende stijlen voor het weer geven van kaarten die worden ondersteund door Microsoft Azure kaarten.
author: farah-alyasari
ms.author: v-faalya
ms.date: 05/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: a65f20e04603403a1d3a180e364626d5b4440661
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77664488"
---
# <a name="azure-maps-supported-map-styles"></a>Azure Maps ondersteunde kaart stijlen
Azure Maps biedt ondersteuning voor verschillende ingebouwde kaart stijlen, zoals hieronder wordt beschreven.

## <a name="road"></a>Tour
Een **wegkaart is een standaard** kaart met wegen, natuurlijke en kunst matige functies, samen met de labels voor deze functies.

![kaart stijl weg](./media/supported-map-styles/road.png)

**Toepasselijke Api's:**
* [Kaart afbeelding](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Kaart tegel](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK-kaart besturings element
* Besturings element Android-kaart

## <a name="blank-and-blank_accessible"></a>leeg en blank_accessible

De **lege** en **blank_accessible** kaart stijlen bieden een leeg canvas waarop gegevens kunnen worden gevisualiseerd. De **blank_accessible** stijl blijft scherm lezers-updates bieden met de locatie Details van de kaart, ook al wordt de basis toewijzing niet weer gegeven.

> [!Note]
> In de Web-SDK kunt u de achtergrond kleur van de kaart wijzigen door de stijl CSS `background-color` van het element map DIV in te stellen.

**Toepasselijke Api's:**
* Web SDK-kaart besturings element

## <a name="satellite"></a>kantoor 
De **satelliet** stijl is een combi natie van satelliet-en lucht foto beelden.

![stijl van kaart voor satelliet tegel](./media/supported-map-styles/satellite.png)

**Toepasselijke Api's:**
* [Satelliet tegel](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Web SDK-kaart besturings element
* Besturings element Android-kaart

## <a name="satellite_road_labels"></a>satellite_road_labels
Deze kaart stijl bestaat uit een hybride van de weg en labels op satelliet-en lucht foto-afbeelding.

![stijl van satellite_road_labelse kaart](./media/supported-map-styles/satellite-road-labels.png)

**Toepasselijke Api's:**
* Web SDK-kaart besturings element
* Besturings element Android-kaart

## <a name="grayscale_dark"></a>grayscale_dark
het **donkere grijs tinten** is een donkere versie van het kaart type weg.

![stijl van gray_scalee kaart](./media/supported-map-styles/grayscale-dark.png)

**Toepasselijke Api's:**
* [Kaart afbeelding](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Kaart tegel](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK-kaart besturings element 
* Besturings element Android-kaart


## <a name="grayscale_light"></a>grayscale_light
**grijs waarden licht** is een lichte versie van het kaart type weg.

![stijl van licht grijs waarden diagram](./media/supported-map-styles/grayscale-light.png)

**Toepasselijke Api's:**
* Web SDK-kaart besturings element
* Besturings element Android-kaart


## <a name="night"></a>'S
**'s avonds** is een donkere versie van de kaart stijl met gekleurde wegen en symbolen.

![stijl van nacht kaart](./media/supported-map-styles/night.png)

**Toepasselijke Api's:**
* Web SDK-kaart besturings element
* Besturings element Android-kaart

## <a name="road_shaded_relief"></a>road_shaded_relief
**Uitlichting** van de weg is een Azure Maps hoofd stijl voltooid met conto uren van de aarde.

![stijl van de kaart met schaduw verlichting](./media/supported-map-styles/shaded-relief.png)

**Toepasselijke Api's:**
* [Kaart tegel](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK-kaart besturings element
* Besturings element Android-kaart

## <a name="high_contrast_dark"></a>high_contrast_dark

**high_contrast_dark** is een donkere kaart stijl met een hoger contrast dan de andere stijlen.

![donkere kaart stijl met hoog contrast](./media/supported-map-styles/high-contrast-dark.png)

**Toepasselijke Api's:**
* Web SDK-kaart besturings element

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het instellen van een kaart stijl in Azure Maps:

> [!div class="nextstepaction"]
> [Een kaart stijl kiezen](https://docs.microsoft.com/azure/azure-maps/choose-map-style)
