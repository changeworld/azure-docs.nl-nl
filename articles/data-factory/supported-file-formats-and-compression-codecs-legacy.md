---
title: Ondersteunde bestands indelingen in Azure Data Factory (verouderd)
description: Dit onderwerp beschrijft de bestandsindelingen en compressie-codes die worden ondersteund door de connectors op basis van bestanden in Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: 423706c391e8d8c2c609798d9f50e5a22f5c39bb
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79260681"
---
# <a name="supported-file-formats-and-compression-codecs-in-azure-data-factory-legacy"></a>Ondersteunde bestands indelingen en compressie-codecs in Azure Data Factory (verouderd)

*Dit artikel is van toepassing op de volgende connectors: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [Bestands systeem](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)en [SFTP](connector-sftp.md).*

>[!IMPORTANT]
>In Data Factory nieuw op indeling gebaseerd gegevensset-model, zie het bijbehorende artikel betreffende indeling met Details: <br>- [Avro-indeling](format-avro.md)<br>- [binaire indeling](format-binary.md)<br>- [tekst indeling met scheidings tekens](format-delimited-text.md)<br>[JSON-indeling](format-json.md) - <br>- [Orc-indeling](format-orc.md)<br>- [Parquet-indeling](format-parquet.md)<br>De rest-configuraties die in dit artikel worden vermeld, worden nog steeds ondersteund voor achterwaartse compabitility. U wordt aangeraden het nieuwe model verder te gebruiken. 

## <a name="text-format"></a>Tekst indeling (verouderd)

>[!NOTE]
>Meer informatie over het nieuwe model van het artikel [tekst met scheidings tekens](format-delimited-text.md) . De volgende configuraties voor gegevens opslag gegevensset op basis van een bestand worden nog steeds ondersteund als-is voor achterwaartse compabitility. U wordt aangeraden het nieuwe model verder te gebruiken.

Als u wilt lezen uit een tekst bestand of naar een tekst bestand wilt schrijven, stelt u de eigenschap `type` in de sectie `format` van de gegevensset in op **TextFormat**. U kunt ook de volgende **optionele** eigenschappen opgeven in het gedeelte `format`. Raadpleeg het gedeelte [TextFormat-voorbeeld](#textformat-example) voor configuratie-instructies.

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| columnDelimiter |Het teken dat wordt gebruikt voor het scheiden van kolommen in een bestand. U kunt overwegen een zeldzaam niet-afdrukbaar teken die mogelijk niet aanwezig zijn in uw gegevens gebruiken. Geef bijvoorbeeld '\u0001', waarmee de Start van de kop (SOH). |Er is slechts één teken toegestaan. De **standaardwaarde** is een **komma (',')** . <br/><br/>Als u een Unicode-teken wilt gebruiken, raadpleegt u [Unicode-tekens](https://en.wikipedia.org/wiki/List_of_Unicode_characters) om de bijbehorende code te verkrijgen. |Nee |
| rowDelimiter |Het teken dat wordt gebruikt voor het scheiden van rijen in een bestand. |Er is slechts één teken toegestaan. De **standaardwaarde** is een van de volgende leeswaarden **['\r\n', '\r', '\n']** en de schrijfwaarde **'\r\n'** . |Nee |
| escapeChar |Dit speciale teken wordt gebruikt om een scheidingsteken voor kolommen van de inhoud van het invoerbestand om te zetten. <br/><br/>Het is niet mogelijk om zowel escapeChar als quoteChar voor een tabel op te geven. |Er is slechts één teken toegestaan. Er is geen standaardwaarde. <br/><br/>Voorbeeld: als u kolommen scheidt met komma's (', '), maar u het kommateken in een tekst wilt gebruiken (voorbeeld: 'Hallo, wereld'), kunt u '$' als het omzettingsteken opgeven en in de bron de tekenreeks 'Hallo$, wereld' gebruiken. |Nee |
| quoteChar |Het teken dat wordt gebruikt om een tekenreekswaarde te citeren. De scheidingstekens voor kolommen en rijen binnen de aanhalingstekens worden beschouwd als onderdeel van de tekenreekswaarde. Deze eigenschap is van toepassing op gegevenssets voor invoer en uitvoer.<br/><br/>Het is niet mogelijk om zowel escapeChar als quoteChar voor een tabel op te geven. |Er is slechts één teken toegestaan. Er is geen standaardwaarde. <br/><br/>Voorbeeld: als u kolommen scheidt met komma's (', '), maar u het kommateken in een tekst wilt gebruiken (voorbeeld: <Hallo, wereld>), kunt u " (dubbel aanhalingsteken) als het aanhalingsteken opgeven en de tekenreeks "Hallo, wereld" in de bron gebruiken. |Nee |
| nullValue |Een of meer tekens die worden gebruikt om een null-waarde te vertegenwoordigen. |Een of meer tekens. De **standaardwaarden** zijn **'\N' en 'NULL'** voor lezen en **'\N'** voor schrijven. |Nee |
| encodingName |Geef de coderingsnaam op. |Een geldige coderingsnaam. Zie [De eigenschap Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx). Voorbeeld: windows 1250 of shift_jis. De **standaardwaarde** is **UTF-8**. |Nee |
| firstRowAsHeader |Hiermee geeft u op of de eerste rij als een header moet worden gezien. Bij een gegevensset voor invoer leest Data Factory de eerste rij als een header. Bij een gegevensset voor uitvoer schrijft Data Factory de eerste rij als een header. <br/><br/>Zie [Gebruiksscenario's`firstRowAsHeader` en `skipLineCount`](#scenarios-for-using-firstrowasheader-and-skiplinecount) voor voorbeeldscenario's. |True<br/><b>False (standaard)</b> |Nee |
| skipLineCount |Hiermee wordt het aantal **niet-lege** rijen aangegeven dat moet worden overgeslagen bij het lezen van gegevens van invoer bestanden. Als zowel skipLineCount als firstRowAsHeader is opgegeven, worden de regels eerst overgeslagen en wordt de headerinformatie gelezen uit het invoerbestand. <br/><br/>Zie [Gebruiksscenario's`firstRowAsHeader` en `skipLineCount`](#scenarios-for-using-firstrowasheader-and-skiplinecount) voor voorbeeldscenario's. |Geheel getal |Nee |
| treatEmptyAsNull |Hiermee geeft u aan of null of lege tekenreeks moeten worden behandeld als een null-waarde bij het lezen van gegevens uit een invoerbestand. |**True (standaard)**<br/>False |Nee |

### <a name="textformat-example"></a>Voorbeeld van TextFormat

In de volgende JSON-definitie voor een gegevensset, worden enkele van de optionele eigenschappen opgegeven.

```json
"typeProperties":
{
    "folderPath": "mycontainer/myfolder",
    "fileName": "myblobname",
    "format":
    {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": ";",
        "quoteChar": "\"",
        "NullValue": "NaN",
        "firstRowAsHeader": true,
        "skipLineCount": 0,
        "treatEmptyAsNull": true
    }
},
```

Gebruik een `escapeChar` in plaats van `quoteChar`, vervang de regel door `quoteChar` met het volgende escapeChar:

```json
"escapeChar": "$",
```

### <a name="scenarios-for-using-firstrowasheader-and-skiplinecount"></a>Scenario's voor het gebruik van firstRowAsHeader en skipLineCount

* U kopieert vanuit een bron die geen bestand is, naar een tekstbestand en wilt een headerregel toevoegen die de metagegevens van het schema bevat (bijvoorbeeld: SQL-schema). Geef voor `firstRowAsHeader` 'True' op in de uitvoergegevensset voor dit scenario.
* U wilt kopiëren vanuit een tekstbestand met een headerregel naar een sink die geen bestand is en wilt die regel verwijderen. Geef voor `firstRowAsHeader` 'True' op in de invoergegevensset.
* U wilt kopiëren uit een tekstbestand en wilt een paar regels aan het begin overslaan die geen gegevens of headerinformatie bevatten. Geef `skipLineCount` op om aan te geven hoeveel regels er moeten worden overgeslagen. Als de rest van het bestand een headerregel bevat, kunt u ook `firstRowAsHeader` opgeven. Als zowel `skipLineCount` als `firstRowAsHeader` is opgegeven, worden de regels eerst overgeslagen en wordt de headerinformatie gelezen uit het invoerbestand

## <a name="json-format"></a>JSON-indeling (verouderd)

>[!NOTE]
>Meer informatie over het nieuwe model van het artikel [JSON-indeling](format-json.md) . De volgende configuraties voor gegevens opslag gegevensset op basis van een bestand worden nog steeds ondersteund als-is voor achterwaartse compabitility. U wordt aangeraden het nieuwe model verder te gebruiken.

Als u **een JSON-bestand wilt importeren/exporteren naar/van Azure Cosmos DB**, raadpleegt u de sectie JSON-documenten importeren/exporteren in [gegevens verplaatsen naar/van Azure Cosmos DB](connector-azure-cosmos-db.md) artikel.

Als u de JSON-bestanden wilt parseren of de gegevens in JSON-indeling wilt schrijven, stelt u de eigenschap `type` in de sectie `format` in op **JsonFormat**. U kunt ook de volgende **optionele** eigenschappen opgeven in het gedeelte `format`. Zie het gedeelte [JsonFormat-voorbeeld](#jsonformat-example) voor configuratie-instructies.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| filePattern |Hiermee geeft u het patroon aan van gegevens die zijn opgeslagen in elk JSON-bestand. Toegestane waarden zijn **setOfObjects** en **arrayOfObjects**. De **standaardwaarde** is **setOfObjects**. Zie het gedeelte [JSON-bestandpatronen](#json-file-patterns) voor meer informatie over deze patronen. |Nee |
| jsonNodeReference | Als u wilt bladeren en gegevens wilt ophalen uit de objecten in een matrixveld met hetzelfde patroon, geeft u het JSON-pad van die matrix op. Deze eigenschap wordt alleen ondersteund bij het kopiëren **van gegevens uit** json-bestanden. | Nee |
| jsonPathDefinition | Hiermee geeft u de JSON-padexpressie aan voor elke kolomtoewijzing met een aangepaste kolomnaam (begin met een kleine letter). Deze eigenschap wordt alleen ondersteund bij het kopiëren **van gegevens uit** json-bestanden en u kunt gegevens uit een object of matrix ophalen. <br/><br/> Voor velden onder het hoofdobject begint u met root $; voor velden binnen de matrix die is gekozen door de eigenschap `jsonNodeReference`, begint u vanaf het element van de matrix. Zie het gedeelte [JsonFormat-voorbeeld](#jsonformat-example) voor configuratie-instructies. | Nee |
| encodingName |Geef de coderingsnaam op. Zie voor de lijst met geldige namen voor versleuteling [De eigenschap Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx). Bijvoorbeeld: windows 1250 of shift_jis. De **standaardwaarde** is **UTF-8**. |Nee |
| nestingSeparator |Teken dat wordt gebruikt voor het scheiden van geneste niveaus. De standaardwaarde is '.' (punt). |Nee |

>[!NOTE]
>In het geval van cross-apply gegevens in een matrix in meerdere rijen (hoofdletter 1-> voor beeld 2 in [JsonFormat-voor beelden](#jsonformat-example)), kunt u er alleen voor kiezen om één matrix uit te breiden met behulp van eigenschap `jsonNodeReference`.

### <a name="json-file-patterns"></a>JSON-bestandpatronen

Kopieeractiviteit kan de volgende patronen van JSON-bestanden parseren:

- **Type I: setOfObjects**

    Elk bestand bevat één object of meerdere door regels gescheiden/samengevoegde objecten. Wanneer deze optie is geselecteerd in een uitvoergegevensset, produceert de kopieerbewerking een enkel JSON-bestand met één object per regel (door regels gescheiden).

    * **voorbeeld van JSON-bestand met één object**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        ```

    * **voorbeeld van JSON-bestand dat door regels is gescheiden**

        ```json
        {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
        {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
        {"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
        ```

    * **voorbeeld van JSON-bestand met samengevoegde objecten**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        }
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
        ```

- **Type II: arrayOfObjects**

    Elk bestand bevat een matrix met objecten.

    ```json
    [
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        },
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
    ]
    ```

### <a name="jsonformat-example"></a>Voorbeeld van JsonFormat

**Voorbeeld 1: gegevens uit JSON-bestanden kopiëren**

**Voorbeeld 1: gegevens ophalen uit object en matrix**

In dit voorbeeld kunt u verwachten dat één JSON-hoofdobject wordt toegewezen aan één record in het tabelresultaat. Als u een JSON-bestand hebt met de volgende inhoud:

```json
{
    "id": "ed0e4960-d9c5-11e6-85dc-d7996816aad3",
    "context": {
        "device": {
            "type": "PC"
        },
        "custom": {
            "dimensions": [
                {
                    "TargetResourceType": "Microsoft.Compute/virtualMachines"
                },
                {
                    "ResourceManagementProcessRunId": "827f8aaa-ab72-437c-ba48-d8917a7336a3"
                },
                {
                    "OccurrenceTime": "1/13/2017 11:24:37 AM"
                }
            ]
        }
    }
}
```

en u wilt het kopiëren naar een Azure SQL-tabel in de volgende indeling door gegevens te extraheren uit de objecten en matrix:

| Id | deviceType | targetResourceType | resourceManagementProcessRunId | occurrenceTime |
| --- | --- | --- | --- | --- |
| ed0e4960-d9c5-11e6-85dc-d7996816aad3 | Pc | Microsoft.Compute/virtualMachines | 827f8aaa-ab72-437c-ba48-d8917a7336a3 | 13/1/2017 11:24:37 uur |

De invoergegevensset met het type **JsonFormat** wordt als volgt gedefinieerd: (gedeeltelijke definitie met alleen belangrijke onderdelen). Met name:

- Het gedeelte `structure` definieert de aangepaste kolomnamen en het bijbehorende gegevenstype tijdens het converteren van gegevens in tabelvorm. Dit gedeelte is **optioneel**, tenzij u kolommen moet toewijzen. Zie [kolommen van bron gegevensset toewijzen aan kolommen voor doel gegevensset](copy-activity-schema-and-type-mapping.md)voor meer informatie.
- Met `jsonPathDefinition` geeft u het JSON-pad op voor elke kolom die aangeeft waar de gegevens moeten worden opgehaald. Als u gegevens wilt kopiëren uit een matrix, kunt u `array[x].property` gebruiken om de waarde van de gegeven eigenschap uit het `xth`-object op te halen. u kunt ook `array[*].property` gebruiken om de waarde te vinden van een wille keurig object dat dergelijke eigenschap bevat.

```json
"properties": {
    "structure": [
        {
            "name": "id",
            "type": "String"
        },
        {
            "name": "deviceType",
            "type": "String"
        },
        {
            "name": "targetResourceType",
            "type": "String"
        },
        {
            "name": "resourceManagementProcessRunId",
            "type": "String"
        },
        {
            "name": "occurrenceTime",
            "type": "DateTime"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat",
            "filePattern": "setOfObjects",
            "jsonPathDefinition": {"id": "$.id", "deviceType": "$.context.device.type", "targetResourceType": "$.context.custom.dimensions[0].TargetResourceType", "resourceManagementProcessRunId": "$.context.custom.dimensions[1].ResourceManagementProcessRunId", "occurrenceTime": " $.context.custom.dimensions[2].OccurrenceTime"}
        }
    }
}
```

**Voorbeeld 2: meerdere objecten met hetzelfde patroon uit een matrix toepassen**

In dit voorbeeld probeert u een JSON-hoofdobject te transformeren naar meerdere records in een tabelresultaat. Als u een JSON-bestand hebt met de volgende inhoud:

```json
{
    "ordernumber": "01",
    "orderdate": "20170122",
    "orderlines": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "sanmateo": "No 1" } ]
}
```

en u wilt het kopiëren naar een Azure SQL-tabel in de volgende indeling, door de gegevens binnen de matrix af te vlakken en samen te voegen met de algemene root-gegevens:

| `ordernumber` | `orderdate` | `order_pd` | `order_price` | `city` |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | `[{"sanmateo":"No 1"}]` |
| 01 | 20170122 | P2 | 13 | `[{"sanmateo":"No 1"}]` |
| 01 | 20170122 | P3 | 231 | `[{"sanmateo":"No 1"}]` |


De invoergegevensset met het type **JsonFormat** wordt als volgt gedefinieerd: (gedeeltelijke definitie met alleen belangrijke onderdelen). Met name:

- Het gedeelte `structure` definieert de aangepaste kolomnamen en het bijbehorende gegevenstype tijdens het converteren van gegevens in tabelvorm. Dit gedeelte is **optioneel**, tenzij u kolommen moet toewijzen. Zie [kolommen van bron gegevensset toewijzen aan kolommen voor doel gegevensset](copy-activity-schema-and-type-mapping.md)voor meer informatie.
- `jsonNodeReference` geeft aan dat gegevens worden herhaald en opgehaald uit de objecten met hetzelfde patroon onder **matrix** `orderlines`.
- Met `jsonPathDefinition` geeft u het JSON-pad op voor elke kolom die aangeeft waar de gegevens moeten worden opgehaald. In dit voor beeld bevinden `ordernumber`, `orderdate`en `city` zich onder het hoofd object met het JSON-pad, te beginnen met `$.`, terwijl `order_pd` en `order_price` worden gedefinieerd met een pad dat is afgeleid van het matrix element zonder `$.`.

```json
"properties": {
    "structure": [
        {
            "name": "ordernumber",
            "type": "String"
        },
        {
            "name": "orderdate",
            "type": "String"
        },
        {
            "name": "order_pd",
            "type": "String"
        },
        {
            "name": "order_price",
            "type": "Int64"
        },
        {
            "name": "city",
            "type": "String"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat",
            "filePattern": "setOfObjects",
            "jsonNodeReference": "$.orderlines",
            "jsonPathDefinition": {"ordernumber": "$.ordernumber", "orderdate": "$.orderdate", "order_pd": "prod", "order_price": "price", "city": " $.city"}
        }
    }
}
```

**Houd rekening met de volgende punten:**

* Als `structure` en `jsonPathDefinition` niet zijn gedefinieerd in de gegevensset van Data Factory, detecteert de kopieerbewerking het schema van het eerste object en wordt het hele object afgevlakt.
* Als de JSON-invoer een matrix heeft, zet de kopieerbewerking de volledige matrix-waarde standaard om in een tekenreeks. U kunt ervoor kiezen om er gegevens uit op te halen met behulp van `jsonNodeReference` en/of `jsonPathDefinition`, of deze stap over te slaan door deze niet op te geven in `jsonPathDefinition`.
* Als er dubbele namen op hetzelfde niveau voorkomen, gebruikt de kopieerbewerking de laatste.
* Eigenschapnamen zijn hoofdlettergevoelig. Twee eigenschappen met dezelfde naam maar met een verschil in hoofdletters en kleine letters worden behandeld als twee afzonderlijke eigenschappen.

**Voorbeeld 2: gegevens schrijven naar een JSON-bestand**

Als u de volgende tabel in SQL-Database hebt:

| Id | order_date | order_price | order_by |
| --- | --- | --- | --- |
| 1 | 20170119 | 2000 | David |
| 2 | 20170120 | 3500 | Patrick |
| 3 | 20170121 | 4000 | Jason |

en voor elke record die u verwacht te schrijven naar een JSON-object in de volgende indeling:

```json
{
    "id": "1",
    "order": {
        "date": "20170119",
        "price": 2000,
        "customer": "David"
    }
}
```

De uitvoergegevensset met het type **JsonFormat** wordt als volgt gedefinieerd: (gedeeltelijke definitie met alleen belangrijke onderdelen). `structure` sectie definieert de namen van aangepaste eigenschappen in het doel bestand, `nestingSeparator` (standaard is ".") worden gebruikt om de nest laag van de naam te identificeren. Dit gedeelte is **optioneel**, tenzij u de naam van de eigenschap wilt wijzigen ten opzichte van de naam van de bronkolom of sommige eigenschappen wilt nesten.

```json
"properties": {
    "structure": [
        {
            "name": "id",
            "type": "String"
        },
        {
            "name": "order.date",
            "type": "String"
        },
        {
            "name": "order.price",
            "type": "Int64"
        },
        {
            "name": "order.customer",
            "type": "String"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat"
        }
    }
}
```

## <a name="parquet-format"></a>Parquet-indeling (verouderd)

>[!NOTE]
>Meer informatie over het nieuwe model van [Parquet](format-parquet.md) -artikel. De volgende configuraties voor gegevens opslag gegevensset op basis van een bestand worden nog steeds ondersteund als-is voor achterwaartse compabitility. U wordt aangeraden het nieuwe model verder te gebruiken.

Als u de Parquet-bestanden wilt parseren of de gegevens in Parquet-indeling wilt schrijven, stelt u de eigenschap `format` `type` in op **ParquetFormat**. U hoeft geen eigenschappen op te geven in het gedeelte Indeling binnen het gedeelte typeProperties. Voorbeeld:

```json
"format":
{
    "type": "ParquetFormat"
}
```

Houd rekening met de volgende punten:

* Complexe gegevens typen worden niet ondersteund (kaart, lijst).
* Spaties in kolom naam wordt niet ondersteund.
* Parquet-bestanden hebben de volgende opties voor compressie: NONE, SNAPPY, GZIP en LZO. Data Factory ondersteunt het lezen van gegevens uit het Parquet-bestand in een van deze gecomprimeerde indelingen, met uitzonde ring van LZO, gebruikt de compressie-codec in de meta gegevens om de gegevens te lezen. Bij het schrijven naar een Parquet-bestand kiest Data Factory echter SNAPPY, de standaardinstelling voor Parquet. Er is momenteel geen optie om dit gedrag te overschrijven.

> [!IMPORTANT]
> Als u de kopie wilt voorzien van zelf-hostende Integration Runtime bijvoorbeeld tussen on-premises en gegevens opslag in de Cloud, moet u de **64-bits jre 8 (Java Runtime Environment) of openjdk** op uw IR-computer **installeren, als**u geen Parquet-bestanden kopieert. Raadpleeg de volgende alinea met meer informatie.

Voor kopieën die worden uitgevoerd op zelf-hostende IR met Parquet-serialisatie/deserialisatie, zoekt ADF de Java-runtime door de register *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* voor jre te controleren, indien niet gevonden, ter controle van de systeem variabele *`JAVA_HOME`* voor openjdk.

- **Jre gebruiken**: de 64-bits IR vereist een 64-bits jre. U kunt deze [hier](https://go.microsoft.com/fwlink/?LinkId=808605)vinden.
- **Als u openjdk wilt gebruiken**, wordt dit ondersteund sinds IR-versie 3,13. Verdeel het JVM. dll-bestand met alle andere vereiste assembly's van OpenJDK in een zelf-hostende IR-computer en stel de systeem omgevings variabele in JAVA_HOME dienovereenkomstig in.

>[!TIP]
>Als u gegevens kopieert naar/van Parquet-indeling met behulp van zelf-hostende Integration Runtime en de fout melding ' er is een fout opgetreden bij het aanroepen van Java, bericht: **Java. lang. OutOfMemoryError: Java-heapruimte**', kunt u een omgevings variabele `_JAVA_OPTIONS` toevoegen aan de computer die als host fungeert voor de zelf-hostende IR om een dergelijke kopie te maken,

![JVM-Heap-grootte instellen op zelf-hostende IR](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Voor beeld: Stel variabele `_JAVA_OPTIONS` in met de waarde `-Xms256m -Xmx16g`. Met de vlag `Xms` wordt de eerste geheugen toewijzings groep opgegeven voor een Java Virtual Machine (JVM), terwijl `Xmx` de maximale geheugen toewijzings groep opgeeft. Dit betekent dat JVM wordt gestart met `Xms` hoeveelheid geheugen en dat er Maxi maal `Xmx` hoeveelheid geheugen kan worden gebruikt. ADF gebruikt standaard min 64 MB en Max 1G.

### <a name="data-type-mapping-for-parquet-files"></a>Gegevenstype toewijzing voor Parquet-bestanden

| Data factory tussentijdse gegevenstype | Parquet primitief Type | Parquet oorspronkelijke Type (deserialiseren) | Parquet oorspronkelijke Type (serialiseren) |
|:--- |:--- |:--- |:--- |
| Booleaans | Booleaans | N.v.t. | N.v.t. |
| SByte | Int32 | Int8 | Int8 |
| Byte | Int32 | UInt8 | Int16 |
| Int16 | Int32 | Int16 | Int16 |
| UInt16 | Int32 | UInt16 | Int32 |
| Int32 | Int32 | Int32 | Int32 |
| UInt32 | Int64 | UInt32 | Int64 |
| Int64 | Int64 | Int64 | Int64 |
| UInt64 | Int64 binaire | UInt64 | decimaal |
| Enkelvoudig | Float | N.v.t. | N.v.t. |
| Double-waarde | Double-waarde | N.v.t. | N.v.t. |
| decimaal | Binair bestand | decimaal | decimaal |
| Tekenreeks | Binair bestand | Utf8 | Utf8 |
| DateTime | Int96 | N.v.t. | N.v.t. |
| TimeSpan | Int96 | N.v.t. | N.v.t. |
| DateTimeOffset | Int96 | N.v.t. | N.v.t. |
| ByteArray | Binair bestand | N.v.t. | N.v.t. |
| Guid | Binair bestand | Utf8 | Utf8 |
| char | Binair bestand | Utf8 | Utf8 |
| CharArray | Niet ondersteund | N.v.t. | N.v.t. |

## <a name="orc-format"></a>ORC-indeling (verouderd)

>[!NOTE]
>Meer informatie over het nieuwe model van [Orc](format-orc.md) -artikel. De volgende configuraties voor gegevens opslag gegevensset op basis van een bestand worden nog steeds ondersteund als-is voor achterwaartse compabitility. U wordt aangeraden het nieuwe model verder te gebruiken.

Als u de ORC-bestanden wilt parseren of de gegevens in ORC-indeling wilt schrijven, stelt u de eigenschap `format` `type` in op **OrcFormat**. U hoeft geen eigenschappen op te geven in het gedeelte Indeling binnen het gedeelte typeProperties. Voorbeeld:

```json
"format":
{
    "type": "OrcFormat"
}
```

Houd rekening met de volgende punten:

* Complexe gegevens typen worden niet ondersteund (STRUCT, kaart, lijst, samen VOEGing).
* Spaties in kolom naam wordt niet ondersteund.
* Een ORC-bestand heeft drie [opties voor compressie](https://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/): NONE, ZLIB, SNAPPY. Data Factory ondersteunt het lezen van gegevens uit ORC-bestanden in een van deze gecomprimeerde indelingen. Hierbij wordt de compressiecodec in de metagegevens gebruikt om de gegevens te lezen. Bij het schrijven naar een ORC-bestand kiest Data Factory echter ZLIB, de standaardinstelling voor ORC. Er is momenteel geen optie om dit gedrag te overschrijven.

> [!IMPORTANT]
> Als u de kopie wilt voorzien van zelf-hostende Integration Runtime bijvoorbeeld tussen on-premises en gegevens opslag in de Cloud, moet u de **64-bits jre 8 (Java Runtime Environment) of openjdk** op uw IR-computer **installeren, als**u geen Orc-bestanden kopieert. Raadpleeg de volgende alinea met meer informatie.

Voor kopieën die worden uitgevoerd op zelf-hostende IR met ORC-serialisatie/deserialisatie, zoekt ADF de Java-runtime door de register *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* voor jre te controleren, indien niet gevonden, ter controle van de systeem variabele *`JAVA_HOME`* voor openjdk.

- **Jre gebruiken**: de 64-bits IR vereist een 64-bits jre. U kunt deze [hier](https://go.microsoft.com/fwlink/?LinkId=808605)vinden.
- **Als u openjdk wilt gebruiken**, wordt dit ondersteund sinds IR-versie 3,13. Verdeel het JVM. dll-bestand met alle andere vereiste assembly's van OpenJDK in een zelf-hostende IR-computer en stel de systeem omgevings variabele in JAVA_HOME dienovereenkomstig in.

### <a name="data-type-mapping-for-orc-files"></a>Gegevenstype toewijzing voor ORC-bestanden

| Data factory tussentijdse gegevenstype | ORC-typen |
|:--- |:--- |
| Booleaans | Booleaans |
| SByte | Byte |
| Byte | Kort |
| Int16 | Kort |
| UInt16 | Int |
| Int32 | Int |
| UInt32 | Lang |
| Int64 | Lang |
| UInt64 | Tekenreeks |
| Enkelvoudig | Float |
| Double-waarde | Double-waarde |
| decimaal | decimaal |
| Tekenreeks | Tekenreeks |
| DateTime | Tijdstempel |
| DateTimeOffset | Tijdstempel |
| TimeSpan | Tijdstempel |
| ByteArray | Binair bestand |
| Guid | Tekenreeks |
| char | CHAR(1) |

## <a name="avro-format"></a>AVRO-indeling (verouderd)

>[!NOTE]
>Meer informatie over het nieuwe model van [Avro](format-avro.md) -artikel. De volgende configuraties voor gegevens opslag gegevensset op basis van een bestand worden nog steeds ondersteund als-is voor achterwaartse compabitility. U wordt aangeraden het nieuwe model verder te gebruiken.

Als u de Avro-bestanden wilt parseren of de gegevens in Avro-indeling wilt schrijven, stelt u de eigenschap `format` `type` in op **Avro Format**. U hoeft geen eigenschappen op te geven in het gedeelte Indeling binnen het gedeelte typeProperties. Voorbeeld:

```json
"format":
{
    "type": "AvroFormat",
}
```

Als u de Avro-indeling wilt gebruiken in een Hive-tabel, kunt u de [Zelfstudie voor Apache Hive](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe) raadplegen.

Houd rekening met de volgende punten:

* [Complexe gegevens typen](https://avro.apache.org/docs/current/spec.html#schema_complex) worden niet ondersteund (records, enums, matrices, kaarten, vakbonden en vast).

## <a name="compression-support"></a>Ondersteuning voor compressie (verouderd)

Azure Data Factory ondersteunt comprimeren/decomprimeren van gegevens tijdens het kopiëren. Wanneer u `compression` eigenschap in een invoer-gegevensset opgeeft, leest de Kopieer activiteit de gecomprimeerde gegevens van de bron en decomprimeert deze. en wanneer u de eigenschap in een uitvoer-gegevensset opgeeft, worden de gegevens door de Kopieer activiteit gecomprimeerd en vervolgens naar de Sink geschreven. Hier volgen enkele eenvoudige voorbeeldscenario's:

* Lezen GZIP gecomprimeerde gegevens uit een Azure-blob, het decomprimeren en resultaatgegevens schrijven naar een Azure SQL database. U definieert de invoer van de Azure Blob-gegevensset met de eigenschap `compression` `type` als GZIP.
* Gegevens lezen uit een bestand met tekst zonder opmaak van on-premises bestandssysteem, comprimeren met behulp van GZip-indeling en de gecomprimeerde gegevens schrijven naar een Azure-blob. U definieert een uitvoer van een Azure Blob-gegevensset met de eigenschap `compression` `type` als GZip.
* ZIP-bestand lezen van FTP-server, decomprimeren voor het ophalen van de bestanden in, en deze bestanden worden neergezet in Azure Data Lake Store. U definieert een invoer-FTP-gegevensset met de eigenschap `compression` `type` als ZipDeflate.
* Een gecomprimeerde GZIP-gegevens lezen uit een Azure-blob, het decomprimeren, comprimeren met behulp van BZIP2 en resultaatgegevens schrijven naar een Azure-blob. U definieert de invoer van de Azure Blob-gegevensset met `compression` `type` ingesteld op GZIP en de uitvoer gegevensset met `compression` `type` ingesteld op BZIP2.

Als u compressie voor een gegevensset wilt opgeven, gebruikt u de eigenschap **Compression** in de JSON van de gegevensset, zoals in het volgende voor beeld:

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "fileName": "pagecounts.csv.gz",
            "folderPath": "compression/file/",
            "format": {
                "type": "TextFormat"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

De sectie **compressie** heeft twee eigenschappen:

* **Type:** de compressie-codec, die **gzip**, **Deflate**, **bzip2**of **ZipDeflate**kan zijn. Opmerking Wanneer u Kopieer activiteit gebruikt om ZipDeflate-bestand (en) te decomprimeren en te schrijven naar op bestanden gebaseerde Sink-gegevens opslag, worden bestanden uitgepakt naar de map: `<path specified in dataset>/<folder named as source zip file>/`.
* **Niveau:** de compressie ratio, die **optimaal** of **snelst**kan zijn.

  * **Snelst:** De compressie bewerking moet zo snel mogelijk worden voltooid, zelfs als het resulterende bestand niet optimaal is gecomprimeerd.
  * **Optimaal**: de compressie bewerking moet optimaal worden gecomprimeerd, zelfs als het volt ooien van de bewerking langer duurt.

    Zie het onderwerp [compressie niveau](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) voor meer informatie.

> [!NOTE]
> Compressie-instellingen worden niet ondersteund voor gegevens in de **Avro Format**, **OrcFormat**of **ParquetFormat**. Bij het lezen van bestanden in de volgende indelingen, wordt in Data Factory wordt gedetecteerd en wordt de compressiecodec in de metagegevens. Bij het schrijven naar bestanden in de volgende indelingen, kiest Data Factory de compressiecodec standaard voor deze indeling. Bijvoorbeeld, ZLIB voor OrcFormat en SNAPPY voor ParquetFormat.

## <a name="unsupported-file-types-and-compression-formats"></a>Niet-ondersteunde bestands typen en compressie-indelingen

U kunt de uitbreidings functies van Azure Data Factory gebruiken om bestanden te transformeren die niet worden ondersteund.
Er zijn twee opties Azure Functions en aangepaste taken met behulp van Azure Batch.

U kunt een voor beeld zien waarin een Azure-functie wordt gebruikt om [de inhoud van een tar-bestand te extra heren](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction). Zie [Azure functions-activiteit](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity)voor meer informatie.

U kunt deze functionaliteit ook bouwen met behulp van een aangepaste DotNet-activiteit. Meer informatie is [hier](https://docs.microsoft.com/azure/data-factory/transform-data-using-dotnet-custom-activity) beschikbaar

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de meest recente ondersteunde bestands indelingen en compressie van [ondersteunde bestands indelingen en compressies](supported-file-formats-and-compression-codecs.md).
