---
title: Core i/o-bewerkingen | Microsoft Azure kaarten
description: Informatie over het efficiënt lezen en schrijven van XML en gescheiden gegevens met behulp van kern bibliotheken in de ruimtelijke IO-module.
author: farah-alyasari
ms.author: v-faalya
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: d2a82fd5d9ba958fd6490a83ecbbe0a4bdf820a0
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78370932"
---
# <a name="core-io-operations"></a>Core i/o-bewerkingen

Naast het leveren van hulpprogram ma's voor het lezen van ruimtelijke gegevens bestanden, worden in de ruimtelijke IO-module de belangrijkste onderliggende bibliotheken weer gegeven voor het lezen en schrijven van XML en het snel en efficiënt verkorten van gegevens.

De `atlas.io.core` naam ruimte bevat twee klassen op laag niveau die snel CSV-en XML-gegevens kunnen lezen en schrijven. Deze basis klassen zijn voorzien van de lezers en schrijvers van ruimtelijke gegevens in de ruimtelijke IO-module. U kunt ze gebruiken om extra lees-en schrijf ondersteuning toe te voegen voor CSV-of XML-bestanden.
 
## <a name="read-delimited-files"></a>Bestanden met scheidings tekens lezen

De klasse `atlas.io.core.CsvReader` leest teken reeksen die gescheiden gegevens sets bevatten. Deze klasse biedt twee methoden voor het lezen van gegevens:

- Met de functie `read` wordt de volledige gegevensset gelezen en wordt een tweedimensionale matrix geretourneerd van teken reeksen die alle cellen van de gescheiden gegevensset vertegenwoordigen.
- Met de functie `getNextRow` wordt elke regel tekst in een gescheiden gegevensset gelezen en wordt een matrix van teken reeks geretourneerd die alle cellen in die gegevensset vertegenwoordigt. De gebruiker kan de rij verwerken en overbodige geheugen uit die rij verwijderen voordat de volgende rij wordt verwerkt. Functie is dus meer geheugen efficiënt.

De Reader gebruikt standaard het punt komma als scheidings teken. Het scheidings teken kan echter worden gewijzigd in een wille keurig teken of ingesteld op `'auto'`. Als deze functie is ingesteld op `'auto'`, analyseert de lezer de eerste regel tekst in de teken reeks. Vervolgens wordt het meest voorkomende teken geselecteerd uit de onderstaande tabel om te gebruiken als scheidings tekens.

| | |
| :-- | :-- |
| Geplaatst | `,` |
| Tabbesturingselement | `\t` |
| Teer | `|` |

Deze lezer ondersteunt ook tekst indicators die worden gebruikt voor het afhandelen van cellen die het scheidings teken bevatten. Het aanhalings teken (`'"'`) is de standaard tekst indicator, maar kan worden gewijzigd in één wille keurig teken.

## <a name="write-delimited-files"></a>Bestanden met scheidings tekens schrijven

De `atlas.io.core.CsvWriter` schrijft een matrix met objecten als een teken reeks met scheidings tekens. Eén wille keurig teken kan worden gebruikt als scheidings tekens of als een tekst kwalificatie. Het standaard scheidings teken is komma (`','`) en de standaard tekst indicator is het aanhalings teken (`'"'`).

Als u deze klasse wilt gebruiken, volgt u de onderstaande stappen:

- Maak een instantie van de klasse en stel optioneel een aangepaste scheidings teken of tekst kwalificatie in.
- Gegevens schrijven naar de klasse met behulp van de functie `write` of de functie `writeRow`. Geef voor de functie `write` een 2-dimensionale matrix van objecten door die meerdere rijen en cellen vertegenwoordigen. Als u de functie `writeRow` wilt gebruiken, moet u een matrix met objecten door geven die een rij met gegevens met meerdere kolommen vertegenwoordigen.
- Roep de `toString` functie aan om de teken reeks met scheidings tekens op te halen. 
- Roep eventueel de `clear` methode aan om de schrijver te herbruikbaar maken en de resource toewijzing te verminderen, of roep de `delete`-methode aan om de instantie van de schrijver te verwijderen.

> [!Note]
> Het aantal geschreven kolommen wordt beperkt tot het aantal cellen in de eerste rij van de gegevens die worden door gegeven aan de schrijver.

## <a name="read-xml-files"></a>XML-bestanden lezen

De `atlas.io.core.SimpleXmlReader` klasse is sneller bij het parseren van XML-bestanden dan `DOMParser`. De klasse `atlas.io.core.SimpleXmlReader` vereist echter dat XML-bestanden goed zijn opgemaakt. XML-bestanden die geen goede indeling hebben, bijvoorbeeld het ontbreken van afsluit codes, resulteren waarschijnlijk in een fout.

De volgende code laat zien hoe u de `SimpleXmlReader`-klasse kunt gebruiken om een XML-teken reeks te parseren in een JSON-object en deze te serialiseren naar een gewenste indeling.

```javascript
//Create an instance of the SimpleXmlReader and parse an XML string into a JSON object.
var xmlDoc = new atlas.io.core.SimpleXmlReader().parse(xmlStringToParse);

//Verify that the root XML tag name of the document is the file type your code is designed to parse.
if (xmlDoc && xmlDoc.root && xmlDoc.root.tagName && xmlDoc.root.tagName === '<Your desired root XML tag name>') {

    var node = xmlDoc.root;

    //Loop through the child node tree to navigate through the parsed XML object.
    for (var i = 0, len = node.childNodes.length; i < len; i++) {
        childNode = node.childNodes[i];

        switch (childNode.tagName) {
            //Look for tag names, parse and serialized as desired.
        }
    }
}
```

## <a name="write-xml-files"></a>XML-bestanden schrijven

De klasse `atlas.io.core.SimpleXmlWriter` schrijft goed opgemaakte XML op een efficiënte manier van geheugen.

De volgende code laat zien hoe u de klasse `SimpleXmlWriter` kunt gebruiken om een XML-teken reeks met goed opgemaakte tekst te genereren.

```javascript
//Create an instance of the SimpleXmlWriter class.
var writer = new atlas.io.core.SimpleXmlWriter();

//Start writing the document. All write functions return a reference to the writer, making it easy to chain the function calls to reduce the code size.
writer.writeStartDocument(true)
    //Specify the root XML tag name, in this case 'root'
    .writeStartElement('root', {
        //Attributes to add to the root XML tag.
        'version': '1.0',
        'xmlns': 'http://www.example.com',
         //Example of a namespace.
        'xmlns:abc': 'http://www.example.com/abc'
    });

//Start writing an element that has the namespace abc and add other XML elements as children.
writer.writeStartElement('abc:parent');

//Write a simple XML element like <title>Azure Maps is awesome!</title>
writer.writeElement('title', 'Azure Maps is awesome!');

//Close the element that we have been writing children to.
writer.writeEndElement();

//Finish writing the document by closing the root tag and the document.
writer.writeEndElement().writeEndDocument();

//Get the generated XML string from the writer.
var xmlString = writer.toString();
```

De gegenereerde XML van de bovenstaande code zou er als volgt uitzien.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<root version="1.0" xmlns="http://www.example.com" xmlns:abc="http://www.example.com/abc">
    <abc:parent>
        <title>Azure Maps is awesome!</title>
    </abc:parent>
</root>
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de klassen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [CsvReader](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.csvreader)

> [!div class="nextstepaction"]
> [CsvWriter](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.csvwriter)

> [!div class="nextstepaction"]
> [SimpleXmlReader](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlreader)

> [!div class="nextstepaction"]
> [SimpleXmlWriter](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlwriter)

Raadpleeg de volgende artikelen voor meer code voorbeelden om toe te voegen aan uw kaarten:

> [!div class="nextstepaction"]
> [Details van ondersteunde gegevens indeling](spatial-io-supported-data-format-details.md)