---
title: Activering op basis van gebeurtenissen in Azure Data Factory maken
description: Meer informatie over het maken van een trigger in Azure Data Factory die een pijp lijn uitvoert in reactie op een gebeurtenis.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 10/18/2018
ms.openlocfilehash: e4301afafb48fb9a1b0c9e36dde9800e2b8390f1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443922"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>Een trigger maken waarmee een pijp lijn wordt uitgevoerd als reactie op een gebeurtenis

In dit artikel worden de activerings methoden beschreven die u kunt maken in uw Data Factory-pijp lijnen.

Event-aangedreven architectuur (EDA) is een gemeen schappelijk gegevens integratie patroon dat betrekking heeft op productie, detectie, verbruik en reactie op gebeurtenissen. Voor scenario's voor gegevens integratie zijn vaak Data Factory-klanten vereist om pijp lijnen te activeren op basis van gebeurtenissen, zoals de ontvangst of verwijdering van een bestand in uw Azure Storage-account. Data Factory is nu geïntegreerd met [Azure Event grid](https://azure.microsoft.com/services/event-grid/), waarmee u pijp lijnen kunt activeren voor een gebeurtenis.

Bekijk de volgende video voor een inleiding en demonstratie van tien minuten voor deze functie:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]


> [!NOTE]
> De integratie die in dit artikel wordt beschreven, is afhankelijk van [Azure Event grid](https://azure.microsoft.com/services/event-grid/). Zorg ervoor dat uw abonnement is geregistreerd bij de resource provider Event Grid. Zie [resource providers en-typen](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)voor meer informatie.

## <a name="data-factory-ui"></a>Gebruikersinterface van Data Factory

In deze sectie wordt beschreven hoe u een gebeurtenis trigger maakt in de gebruikers interface van Azure Data Factory.

1. Ga naar het **ontwerp canvas**

1. Klik in de linkerbenedenhoek op de knop **Triggers**

1. Klik op **+ Nieuw** , waardoor de linkernavigatie trigger maken wordt geopend

1. **Gebeurtenis** trigger type selecteren

    ![Nieuwe gebeurtenis trigger maken](media/how-to-create-event-trigger/event-based-trigger-image1.png)

1. Selecteer uw opslag account in de vervolg keuzelijst van het Azure-abonnement of hand matig met de resource-ID van het opslag account. Kies op welke container de gebeurtenissen moeten worden uitgevoerd. Container selectie is optioneel, maar mindful het selecteren van alle containers kan leiden tot een groot aantal gebeurtenissen.

   > [!NOTE]
   > De gebeurtenis trigger ondersteunt momenteel alleen Azure Data Lake Storage Gen2 en algemene opslag accounts voor versie 2. Als gevolg van een Azure Event Grid beperking, ondersteunt Azure Data Factory slechts een maximum van 500 gebeurtenis triggers per opslag account.

1. Het **BLOB-pad begint met** en het **BLOB-pad eindigt** op Eigenschappen, zodat u de containers, mappen en BLOB-namen kunt opgeven waarvoor u gebeurtenissen wilt ontvangen. Voor uw gebeurtenis trigger moet ten minste één van deze eigenschappen worden gedefinieerd. U kunt verschillende patronen gebruiken voor beide **BLOB-paden begint met** en het **BLOB-pad eindigt met** eigenschappen, zoals wordt weer gegeven in de voor beelden verderop in dit artikel.

    * **Pad van BLOB begint met:** Het BLOB-pad moet beginnen met een mappad. Geldige waarden zijn `2018/` en `2018/april/shoes.csv`. Dit veld kan niet worden geselecteerd als er geen container is geselecteerd.
    * **Pad naar BLOB eindigt op:** Het pad naar de BLOB moet eindigen met een bestands naam of-extensie. Geldige waarden zijn `shoes.csv` en `.csv`. De naam van de container en map zijn optioneel, maar als u deze opgeeft, moeten ze worden gescheiden door een `/blobs/` segment. Een container met de naam ' orders ' kan bijvoorbeeld de waarde `/orders/blobs/2018/april/shoes.csv`hebben. Als u een map in een container wilt opgeven, laat u het voorloop teken '/' weg. `april/shoes.csv` wordt bijvoorbeeld een gebeurtenis geactiveerd voor elk bestand met de naam `shoes.csv` in map a genaamd ' april ' in een wille keurige container. 

1. Selecteer of uw trigger reageert op een gebeurtenis **die** door een blob is gemaakt, een gebeurtenis voor het verwijderen van een **BLOB** of beide. In de opgegeven opslag locatie worden met elke gebeurtenis de Data Factory pijp lijnen geactiveerd die zijn gekoppeld aan de trigger.

    ![De gebeurtenis trigger configureren](media/how-to-create-event-trigger/event-based-trigger-image2.png)

1. Selecteer of blobs met nul bytes moeten worden genegeerd door de trigger.

1. Zodra u de trigger hebt geconfigureerd, klikt u op **volgende: voor beeld van gegevens**. In dit scherm ziet u de bestaande blobs die overeenkomen met de configuratie van de gebeurtenis trigger. Zorg ervoor dat u specifieke filters hebt. Het configureren van filters die te breed zijn, kan overeenkomen met een groot aantal bestanden dat is gemaakt/verwijderd. Dit kan aanzienlijk invloed hebben op uw kosten. Klik op **volt ooien**als de filter voorwaarden zijn gecontroleerd.

    ![Voor beeld van gebeurtenis trigger gegevens](media/how-to-create-event-trigger/event-based-trigger-image3.png)

1. Als u een pijp lijn aan deze trigger wilt koppelen, gaat u naar het pijp lijn-canvas en klikt u op **trigger toevoegen** en selecteert u **Nieuw/bewerken**. Wanneer de side-Navigator wordt weer gegeven, klikt u op de vervolg keuzelijst **Trigger selecteren...** en selecteert u de trigger die u hebt gemaakt. Klik op **volgende: voor beeld van gegevens** om te bevestigen dat de configuratie juist is en controleer vervolgens of de voorbeeld gegevens correct zijn.

1. Als uw pijp lijn para meters heeft, kunt u deze opgeven op de activerings parameter zijde navigatie. De gebeurtenis trigger legt het mappad en de bestands naam van de BLOB vast in de eigenschappen `@triggerBody().folderPath` en `@triggerBody().fileName`. Als u de waarden van deze eigenschappen in een pijp lijn wilt gebruiken, moet u de eigenschappen toewijzen aan pijplijn parameters. Nadat u de eigenschappen hebt toegewezen aan para meters, hebt u toegang tot de waarden die zijn vastgelegd door de trigger via de `@pipeline().parameters.parameterName`-expressie in de pijp lijn. Klik op **volt ooien** wanneer u klaar bent.

    ![Eigenschappen toewijzen aan pijplijn parameters](media/how-to-create-event-trigger/event-based-trigger-image4.png)

In het vorige voor beeld wordt de trigger zo geconfigureerd dat deze wordt geactiveerd wanneer een BLOB-pad dat eindigt op. csv wordt gemaakt in de map Event-test in de voorbeeld gegevens van de container. Met de eigenschappen **FolderPath** en **filename** wordt de locatie van de nieuwe BLOB vastgelegd. Als bijvoorbeeld MoviesDB. csv wordt toegevoegd aan het pad voor beeld/gebeurtenis-tests, `@triggerBody().folderPath` een waarde heeft van `sample-data/event-testing` en `@triggerBody().fileName` de waarde `moviesDB.csv`heeft. Deze waarden worden in het voor beeld toegewezen aan de pijplijn parameters `sourceFolder` en `sourceFile` die in de pijp lijn kunnen worden gebruikt als `@pipeline().parameters.sourceFolder` en `@pipeline().parameters.sourceFile`.

## <a name="json-schema"></a>JSON-schema

De volgende tabel bevat een overzicht van de schema-elementen die zijn gerelateerd aan triggers op basis van gebeurtenissen:

| **JSON-element** | **Beschrijving** | **Type** | **Toegestane waarden** | **Vereist** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **ligt** | De Azure Resource Manager Resource-ID van het opslag account. | Tekenreeks | Azure Resource Manager-ID | Ja |
| **evenementen** | Het type gebeurtenissen dat ervoor zorgt dat deze trigger wordt gestart. | Matrix    | Micro soft. storage. BlobCreated, micro soft. storage. BlobDeleted | Ja, een wille keurige combi natie van deze waarden. |
| **blobPathBeginsWith** | Het BLOB-pad moet beginnen met het patroon dat is ingesteld voor de trigger om te starten. `/records/blobs/december/` bijvoorbeeld alleen de trigger voor blobs in de map `december` onder de container `records` wordt geactiveerd. | Tekenreeks   | | U moet een waarde opgeven voor ten minste één van deze eigenschappen: `blobPathBeginsWith` of `blobPathEndsWith`. |
| **blobPathEndsWith** | Het BLOB-pad moet eindigen met het patroon dat is ingesteld voor de trigger om te starten. Zo wordt met `december/boxes.csv` alleen de trigger geactiveerd voor blobs met de naam `boxes` in een `december` map. | Tekenreeks   | | U moet een waarde opgeven voor ten minste één van deze eigenschappen: `blobPathBeginsWith` of `blobPathEndsWith`. |
| **ignoreEmptyBlobs** | Hiermee wordt aangegeven of nul-byte-Blobs een pijplijn uitvoering activeren. Deze instelling is standaard ingesteld op waar. | Booleaans | true of false | Nee |

## <a name="examples-of-event-based-triggers"></a>Voor beelden van triggers op basis van gebeurtenissen

Deze sectie bevat voor beelden van activerings instellingen op basis van gebeurtenissen.

> [!IMPORTANT]
> U moet het `/blobs/` segment van het pad toevoegen, zoals wordt weer gegeven in de volgende voor beelden, wanneer u container en map, container, bestand, container, map en bestand opgeeft. Voor **blobPathBeginsWith**voegt de Data Factory gebruikers interface automatisch `/blobs/` toe tussen de naam van de map en de container in de JSON van de trigger.

| Eigenschap | Voorbeeld | Beschrijving |
|---|---|---|
| **Pad van BLOB begint met** | `/containername/` | Hiermee ontvangt u gebeurtenissen voor alle blobs in de container. |
| **Pad van BLOB begint met** | `/containername/blobs/foldername/` | Hiermee ontvangt u gebeurtenissen voor alle blobs in de `containername` container en `foldername` map. |
| **Pad van BLOB begint met** | `/containername/blobs/foldername/subfoldername/` | U kunt ook verwijzen naar een submap. |
| **Pad van BLOB begint met** | `/containername/blobs/foldername/file.txt` | Hiermee ontvangt u gebeurtenissen voor een blob met de naam `file.txt` in de map `foldername` onder de `containername`-container. |
| **BLOB eindigt op** | `file.txt` | Hiermee ontvangt u gebeurtenissen voor een blob met de naam `file.txt` in een wille keurig pad. |
| **BLOB eindigt op** | `/containername/blobs/file.txt` | Hiermee ontvangt u gebeurtenissen voor een blob met de naam `file.txt` onder container `containername`. |
| **BLOB eindigt op** | `foldername/file.txt` | Hiermee ontvangt u gebeurtenissen voor een blob met de naam `file.txt` in `foldername` map onder een wille keurige container. |

## <a name="next-steps"></a>Volgende stappen
Zie [pijp lijnen uitvoeren en triggers](concepts-pipeline-execution-triggers.md#triggers)voor meer informatie over triggers.
