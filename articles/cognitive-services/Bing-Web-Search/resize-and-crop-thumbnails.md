---
title: Miniatuur weergaven verg Roten/verkleinen en bijsnijden-Bing Webzoekopdrachten-API
titleSuffix: Azure Cognitive Services
description: Sommige antwoorden van de Bing Zoeken-API's bevatten Url's naar miniatuur afbeeldingen die worden geleverd door Bing, die u kunt verg Roten/verkleinen en bijsnijden, en kan query parameters bevatten.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 05A08B01-89FF-4781-AFE7-08DA92F25047
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: aahi
ms.openlocfilehash: 630b86f55a537d109c851cb585cfccc34d229f83
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74110640"
---
# <a name="resize-and-crop-thumbnail-images"></a>Miniatuur afbeeldingen verg Roten of verkleinen en bijsnijden

Sommige antwoorden van de Bing Zoeken-API's bevatten Url's naar miniatuur afbeeldingen die worden geleverd door Bing, die u kunt verg Roten/verkleinen en bijsnijden, en kan query parameters bevatten. Bijvoorbeeld:

`https://<host>/th?id=AMMS_92772df988...&w=110&h=73&rs=1&qlt=80&cdv=1&pid=16.1`

Als u een subset van deze miniaturen weergeeft, geeft u een optie om de resterende installatie kopieën weer te geven.

> [!NOTE]
> Zorg ervoor dat het bijsnijden en aanpassen van miniatuur afbeeldingen een zoek scenario biedt met betrekking tot de rechten van derden, zoals wordt vereist door de vereisten voor het gebruik van de Bing Search-API [en de weer gave](use-display-requirements.md).

## <a name="resize-a-thumbnail"></a>Het formaat van een miniatuur wijzigen 

Als u het formaat van een miniatuur wilt wijzigen, raadt Bing u slechts één van de query parameters voor de `w` (breedte) of `h` (hoogte) op in de URL van de miniatuur. Als u alleen de hoogte of breedte opgeeft, kan het oorspronkelijke aspect van de afbeelding worden onderhouden. Geef de breedte en hoogte op in pixels. 

Als de oorspronkelijke miniatuur bijvoorbeeld 480x620 is:

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=480&h=620`

En u de grootte wilt verkleinen, stelt u de para meter `w` in op een nieuwe waarde (bijvoorbeeld `336`) en verwijdert u de `h` para meter:

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=336`

Als u alleen de hoogte of breedte van een miniatuur opgeeft, wordt de oorspronkelijke hoogte-breedte verhouding van de afbeelding gehandhaafd. Als u beide para meters opgeeft en de hoogte-breedte verhouding niet wordt behouden, voegt Bing witte opvulling toe aan de rand van de afbeelding.

Als u bijvoorbeeld het formaat van een 480x359-afbeelding wijzigt in 200x200 zonder dat deze is bijgesneden, bevat de volledige breedte de afbeelding, maar de hoogte bevat 25 pixels aan witte opvulling boven en onder aan de afbeelding. Als de afbeelding 359x480 is, bevatten de linker-en rechter rand witte opvulling. Als u de afbeelding bijsnijdt, wordt er geen witte opvulling toegevoegd.  

In de volgende afbeelding ziet u de oorspronkelijke grootte van een miniatuur afbeelding (480x300).  
  
![Oorspronkelijke liggende afbeelding](./media/resize-crop/bing-resize-crop-landscape.png)  
  
In de volgende afbeelding ziet u de afbeelding waarvan de grootte is gewijzigd in 200x200. De hoogte-breedte verhouding wordt gehandhaafd en de boven-en onderrand worden gevuld met wit (de zwarte rand hier is opgenomen om de opvulling weer te geven).  
  
![Grootte van liggende afbeelding gewijzigd](./media/resize-crop/bing-resize-crop-landscape-resized.png)  

Als u dimensies opgeeft die groter zijn dan de oorspronkelijke breedte en hoogte van de afbeelding, voegt Bing witte opvulling toe aan de linker-en bovenrand.  

## <a name="request-different-thumbnail-sizes"></a>Andere grootte aanvragen voor miniaturen

Als u een andere grootte voor een miniatuur afbeelding wilt aanvragen, verwijdert u alle query parameters uit de URL van de miniatuur, met uitzonde ring van de para meters `id` en `pid`. Voeg vervolgens de query parameter `&w` (Width) of `&h` (Height) met de gewenste grootte van de afbeelding in pixels toe, maar niet beide. Bing behoudt de oorspronkelijke hoogte-breedte verhouding van de afbeelding. 

Als u de breedte van de afbeelding die is opgegeven door de bovenstaande URL wilt verg Roten naar 165 pixels, gebruikt u de volgende URL:

`https://<host>/th?id=AMMS_92772df988...&w=165&pid=16.1`

Als u een afbeelding aanvraagt die groter is dan de oorspronkelijke grootte van de afbeelding, voegt Bing witruimte rond de afbeelding toe als dat nodig is. Als de oorspronkelijke grootte van de afbeelding bijvoorbeeld 474x316 is en u `&w` instelt op 500, wordt door Bing een 500x333-installatie kopie geretourneerd. Deze afbeelding krijgt een witte opvulling van 8,5 pixels langs de boven-en onderrand en 13 pixels aan de linker-en rechter rand.

Als u wilt voor komen dat Bing opvulling wordt toegevoegd als de aangevraagde grootte groter is dan de oorspronkelijke grootte van de afbeelding, stelt u de `&p` query parameter in op 0. Als u bijvoorbeeld de para meter `&p=0` opneemt in de bovenstaande URL, wordt in Bing een 474x316-installatie kopie geretourneerd in plaats van een 500x333-installatie kopie:

`https://<host>/th?id=AMMS_92772df988...&w=500&p=0&pid=16.1`

Als u de para meters `&w` en `&h` query opgeeft, wordt de hoogte-breedte verhouding van de afbeelding gehandhaafd en wordt er een witte opvulling toegevoegd als dat nodig is. Als de oorspronkelijke grootte van de afbeelding bijvoorbeeld 474x316 is en u de para meters voor breedte en hoogte instelt op 200x200 (`&w=200&h=200`), retourneert Bing een installatie kopie die 33 pixels aan witte opvulling aan de bovenkant en de onderkant bevat. Als u de `&p` query parameter opneemt, retourneert Bing een 200x134-installatie kopie.

## <a name="crop-a-thumbnail"></a>Een miniatuur bijsnijden 

Als u een afbeelding wilt bijsnijden, neemt u de query parameter `c` (gewas) op. U kunt de volgende waarden gebruiken:
  
- `4` &mdash; blinde verhouding  
- `7` &mdash; slimme verhouding  

### <a name="smart-ratio-cropping"></a>Slimme verhouding bijsnijden

Als u het bijsnijden van intelligente verhoudingen aanvraagt (door de `c` para meter in te stellen op `7`), wordt een afbeelding door Bing bijgesneden van het midden van het gedeelte van de interesse naar buiten, terwijl de hoogte-breedte verhouding van de afbeelding wordt behouden. De gewenste regio is het gebied van de installatie kopie die door Bing wordt bepaald en bevat de meeste import onderdelen. Hieronder ziet u een voor beeld-regio.  
  
![Interesse gebied](./media/resize-crop/bing-resize-crop-regionofinterest.png)

Als u het formaat van een afbeelding wijzigt en het bijsnijden van intelligente verhoudingen aanvraagt, wordt de installatie kopie beperkt tot de gevraagde grootte en blijft de hoogte-breedte verhouding behouden. Bing snijdt de afbeelding vervolgens bij op basis van de afmetingen waarvan het formaat is gewijzigd. Als de breedte van het formaat kleiner is dan of gelijk is aan de hoogte, snijdt Bing de afbeelding aan de linkerkant en rechts van het midden van de interesse. Als dat niet het geval is, wordt dit door Bing aan de boven-en onderkant van het midden van de gewenste regio bijgesneden.  
  
 
Hieronder ziet u de afbeelding die is gereduceerd tot 200x200 met behulp van slimme verhoudingen. Omdat Bing de afbeelding in de linkerbovenhoek meet, wordt het onderste deel van de afbeelding bijgesneden. 
  
![Liggende afbeelding bijgesneden tot 200x200](./media/resize-crop/bing-resize-crop-landscape200x200c7.png) 
  
Hieronder ziet u de afbeelding die is gereduceerd tot 200x100 met behulp van slimme verhoudingen. Omdat Bing de afbeelding in de linkerbovenhoek meet, wordt het onderste deel van de afbeelding bijgesneden. 
   
![Liggende afbeelding bijgesneden tot 200x100](./media/resize-crop/bing-resize-crop-landscape200x100c7.png)
  
Hieronder ziet u de afbeelding die is gereduceerd tot 100x200 met behulp van slimme verhoudingen. Omdat Bing de afbeelding van het midden meet, worden de linker-en rechter onderdelen van de afbeelding bijgesneden.
  
![Liggende afbeelding bijgesneden tot 100x200](./media/resize-crop/bing-resize-crop-landscape100x200c7.png) 

Als Bing de afbeeldings regio niet kan bepalen, gebruikt de service blinde verhouding bijsnijden.  

### <a name="blind-ratio-cropping"></a>Blind verhouding bijsnijden

Als u het bijsnijden van blinde verhouding wilt aanvragen (door de `c` para meter in te stellen op `4`), gebruikt Bing de volgende regels om de afbeelding bij te snijden.  
  
- Als `(Original Image Width / Original Image Height) < (Requested Image Width / Requested Image Height)`, wordt de afbeelding gemeten vanaf de linkerbovenhoek en aan de onderkant bijgesneden.  
- Als `(Original Image Width / Original Image Height) > (Requested Image Width / Requested Image Height)`, wordt de afbeelding gemeten vanuit het midden en aan de linker-en rechter kant bijgesneden.  

Hieronder ziet u een staande afbeelding 225x300.  
  
![Oorspronkelijke zonnebloem-afbeelding](./media/resize-crop/bing-resize-crop-sunflower.png)
  
Hieronder ziet u de afbeelding verkleind tot 200x200 met behulp van blinde verhouding bijsnijden. De afbeelding wordt gemeten vanaf de linkerbovenhoek, waardoor het onderste deel van de afbeelding wordt bijgesneden.  
  
![Zonnebloem-afbeelding bijgesneden tot 200x200](./media/resize-crop/bing-resize-crop-sunflower200x200c4.png)
  
Hieronder ziet u de afbeelding verkleind tot 200x100 met behulp van blinde verhouding bijsnijden. De afbeelding wordt gemeten vanaf de linkerbovenhoek, waardoor het onderste deel van de afbeelding wordt bijgesneden.  
  
![Zonnebloem-afbeelding bijgesneden tot 200x100](./media/resize-crop/bing-resize-crop-sunflower200x100c4.png)
  
Hieronder ziet u de afbeelding verkleind tot 100x200 met behulp van blinde verhouding bijsnijden. De afbeelding wordt gemeten vanuit het midden, waardoor de linker-en rechter delen van de afbeelding worden bijgesneden.  
  
![Zonnebloem-afbeelding bijgesneden tot 100x200](./media/resize-crop/bing-resize-crop-sunflower100x200c4.png)

## <a name="next-steps"></a>Volgende stappen

* [Wat zijn de Bing Zoeken-API's?](bing-api-comparison.md)
* [Vereisten voor het gebruik en de weer gave van Bing Search-API](use-display-requirements.md)
