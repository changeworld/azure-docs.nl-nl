---
title: Gegevens uit een SAP-tabel kopiëren
description: Informatie over het kopiëren van gegevens uit een SAP-tabel naar ondersteunde Sink-gegevens archieven door gebruik te maken van een Kopieer activiteit in een Azure Data Factory-pijp lijn.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/02/2019
ms.openlocfilehash: fd363f7b685db5e309827a0c5e635264e676b388
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79281754"
---
# <a name="copy-data-from-an-sap-table-by-using-azure-data-factory"></a>Gegevens uit een SAP-tabel kopiëren met behulp van Azure Data Factory

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens uit een SAP-tabel te kopiëren. Zie [overzicht van Kopieer activiteiten](copy-activity-overview.md)voor meer informatie.

>[!TIP]
>Zie [SAP Data Integration using Azure Data Factory White Paper](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) with introduction, comparsion en guidance (Engelstalig) voor meer informatie over de algemene ondersteuning van de ADF op SAP Data Integration scenario.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze SAP-tabel connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

U kunt gegevens uit een SAP-tabel kopiëren naar elk ondersteund Sink-gegevens archief. Zie de tabel [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als bronnen of sinks op basis van de Kopieer activiteit.

Deze SAP-tabel connector ondersteunt met name:

- Gegevens uit een SAP-tabel kopiëren in:

  - SAP ERP Central component (SAP ECC) versie 7,01 of hoger (in een recente SAP-ondersteunings pakket stack uitgebracht na 2015).
  - SAP Business Warehouse (SAP BW) versie 7,01 of hoger (in een recente SAP-ondersteunings pakket stack uitgebracht na 2015).
  - SAP S/4HANA.
  - Andere producten in SAP Business Suite versie 7,01 of hoger (in een recente SAP-ondersteunings pakket stack uitgebracht na 2015).

- Het kopiëren van gegevens uit een SAP transparante tabel, een gegroepeerde tabel, een geclusterde tabel en een weer gave.
- Kopiëren van gegevens met behulp van basis verificatie of beveiligde netwerk communicatie (SNC), als SNC is geconfigureerd.
- Verbinding maken met een SAP-toepassings server of SAP-berichten server.

## <a name="prerequisites"></a>Vereisten

Als u deze SAP-tabel connector wilt gebruiken, moet u het volgende doen:

- Stel een zelf-hostende Integration runtime (versie 3,17 of hoger) in. Zie [een zelf-hostende Integration runtime maken en configureren](create-self-hosted-integration-runtime.md)voor meer informatie.

- Down load de 64-bits [SAP connector voor Microsoft .NET 3,0](https://support.sap.com/en/product/connectors/msnet.html) van de SAP-website en installeer deze op de zelf-hostende Integration runtime-computer. Zorg er tijdens de installatie voor dat u de optie **Assembly's installeren naar GAC** selecteert in het venster **optionele installatie stappen** .

  ![SAP-connector voor .NET installeren](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- De SAP-gebruiker die wordt gebruikt in de Data Factory SAP-tabel connector moet de volgende machtigingen hebben:

  - Autorisatie voor het gebruik van externe functie aanroepen (RFC)-doelen.
  - Machtigingen voor het uitvoeren van de activiteit van het S_SDSAUTH-autorisatie object.

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van de Data Factory entiteiten die specifiek zijn voor de SAP-tabel connector.

## <a name="linked-service-properties"></a>Eigenschappen van de gekoppelde service

De volgende eigenschappen worden ondersteund voor de SAP BW open hub gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| `type` | De eigenschap `type` moet worden ingesteld op `SapTable`. | Ja |
| `server` | De naam van de server waarop het SAP-exemplaar zich bevindt.<br/>Gebruiken om verbinding te maken met een SAP-toepassings server. | Nee |
| `systemNumber` | Het systeem nummer van het SAP-systeem.<br/>Gebruiken om verbinding te maken met een SAP-toepassings server.<br/>Toegestane waarde: een decimaal getal van twee cijfers dat wordt weer gegeven als een teken reeks. | Nee |
| `messageServer` | De hostnaam van de SAP-berichten server.<br/>Gebruiken om verbinding te maken met een SAP-berichten server. | Nee |
| `messageServerService` | De service naam of het poort nummer van de berichten server.<br/>Gebruiken om verbinding te maken met een SAP-berichten server. | Nee |
| `systemId` | De ID van het SAP-systeem waarin de tabel zich bevindt.<br/>Gebruiken om verbinding te maken met een SAP-berichten server. | Nee |
| `logonGroup` | De aanmeldings groep voor het SAP-systeem.<br/>Gebruiken om verbinding te maken met een SAP-berichten server. | Nee |
| `clientId` | De ID van de client in het SAP-systeem.<br/>Toegestane waarde: een decimaal getal met drie cijfers dat wordt weer gegeven als een teken reeks. | Ja |
| `language` | De taal die het SAP-systeem gebruikt.<br/>De standaard waarde is `EN`.| Nee |
| `userName` | De naam van de gebruiker die toegang heeft tot de SAP-server. | Ja |
| `password` | Het wachtwoord voor de gebruiker. Markeer dit veld met het `SecureString` type om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| `sncMode` | De activerings indicator SNC voor toegang tot de SAP-server waarop de tabel zich bevindt.<br/>Gebruik deze als u SNC wilt gebruiken om verbinding te maken met de SAP-server.<br/>Toegestane waarden zijn `0` (uit, de standaard instelling) of `1` (aan). | Nee |
| `sncMyName` | De SNC naam van de initiator om toegang te krijgen tot de SAP-server waarop de tabel zich bevindt.<br/>Van toepassing wanneer `sncMode` is ingeschakeld. | Nee |
| `sncPartnerName` | De SNC naam van de communicatie partner om toegang te krijgen tot de SAP-server waarop de tabel zich bevindt.<br/>Van toepassing wanneer `sncMode` is ingeschakeld. | Nee |
| `sncLibraryPath` | De bibliotheek van het externe beveiligings product voor toegang tot de SAP-server waarop de tabel zich bevindt.<br/>Van toepassing wanneer `sncMode` is ingeschakeld. | Nee |
| `sncQop` | De SNC kwaliteit van het beveiligings niveau dat moet worden toegepast.<br/>Van toepassing wanneer `sncMode` is ingeschakeld. <br/>Toegestane waarden zijn `1` (authenticatie), `2` (integriteit), `3` (privacy), `8` (standaard), `9` (maximum). | Nee |
| `connectVia` | De [Integration runtime](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevens archief. Een zelf-hostende Integration runtime is vereist, zoals eerder is vermeld in [vereisten](#prerequisites). |Ja |

**Voor beeld 1: verbinding maken met een SAP-toepassings server**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="example-2-connect-to-an-sap-message-server"></a>Voor beeld 2: verbinding maken met een SAP-berichten server

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "messageServer": "<message server name>",
            "messageServerService": "<service name or port>",
            "systemId": "<system ID>",
            "logonGroup": "<logon group>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="example-3-connect-by-using-snc"></a>Voor beeld 3: verbinding maken met behulp van SNC

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            },
            "sncMode": 1,
            "sncMyName": "<SNC myname>",
            "sncPartnerName": "<SNC partner name>",
            "sncLibraryPath": "<SNC library path>",
            "sncQop": "8"
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie [gegevens sets](concepts-datasets-linked-services.md)voor een volledige lijst van de secties en eigenschappen voor het definiëren van gegevens sets. De volgende sectie bevat een lijst met de eigenschappen die worden ondersteund door de SAP-tabel gegevensset.

De volgende eigenschappen worden ondersteund voor het kopiëren van gegevens van en naar de gekoppelde SAP BW hub-service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| `type` | De eigenschap `type` moet worden ingesteld op `SapTableResource`. | Ja |
| `tableName` | De naam van de SAP-tabel waaruit de gegevens moeten worden gekopieerd. | Ja |

### <a name="example"></a>Voorbeeld

```json
{
    "name": "SAPTableDataset",
    "properties": {
        "type": "SapTableResource",
        "typeProperties": {
            "tableName": "<SAP table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP table linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Voor een volledige lijst van de secties en eigenschappen voor het definiëren van activiteiten raadpleegt u [pijp lijnen](concepts-pipelines-activities.md). De volgende sectie bevat een lijst met de eigenschappen die worden ondersteund door de SAP-tabel bron.

### <a name="sap-table-as-source"></a>SAP-tabel als bron

Als u gegevens wilt kopiëren uit een SAP-tabel, worden de volgende eigenschappen ondersteund:

| Eigenschap                         | Beschrijving                                                  | Vereist |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| `type`                             | De eigenschap `type` moet worden ingesteld op `SapTableSource`.         | Ja      |
| `rowCount`                         | Het aantal rijen dat moet worden opgehaald.                              | Nee       |
| `rfcTableFields`                   | De velden (kolommen) die vanuit de SAP-tabel moeten worden gekopieerd. Bijvoorbeeld `column0, column1`. | Nee       |
| `rfcTableOptions`                  | De opties voor het filteren van de rijen in een SAP-tabel. Bijvoorbeeld `COLUMN0 EQ 'SOMEVALUE'`. Zie ook de SAP-query operator tabel verderop in dit artikel. | Nee       |
| `customRfcReadTableFunctionModule` | Een aangepaste RFC-functie module die kan worden gebruikt om gegevens uit een SAP-tabel te lezen.<br>U kunt een aangepaste RFC-functie module gebruiken om te bepalen hoe de gegevens worden opgehaald uit uw SAP-systeem en worden geretourneerd naar Data Factory. Voor de aangepaste functie module moet een interface zijn geïmplementeerd (import, export, Tables) die vergelijkbaar is met `/SAPDS/RFC_READ_TABLE2`. Dit is de standaard interface die door Data Factory wordt gebruikt. | Nee       |
| `partitionOption`                  | Het partitie mechanisme om te lezen uit een SAP-tabel. De ondersteunde opties zijn onder andere: <ul><li>`None`</li><li>`PartitionOnInt` (normale waarden voor geheel getal of geheel getal met een opvulling van nul aan de linkerkant, zoals `0000012345`)</li><li>`PartitionOnCalendarYear` (4 cijfers in de notatie "YYYY")</li><li>`PartitionOnCalendarMonth` (6 cijfers in de notatie "YYYYMM")</li><li>`PartitionOnCalendarDate` (8 cijfers in de notatie "JJJMMDD")</li></ul> | Nee       |
| `partitionColumnName`              | De naam van de kolom die wordt gebruikt voor het partitioneren van de gegevens.                | Nee       |
| `partitionUpperBound`              | De maximum waarde van de kolom die is opgegeven in `partitionColumnName` die wordt gebruikt om door te gaan met partitioneren. | Nee       |
| `partitionLowerBound`              | De minimum waarde van de kolom die is opgegeven in `partitionColumnName` die wordt gebruikt om door te gaan met partitioneren. | Nee       |
| `maxPartitionsNumber`              | Het maximum aantal partities waarop de gegevens moeten worden gesplitst.     | Nee       |

>[!TIP]
>Als uw SAP-tabel een grote hoeveelheid gegevens bevat, zoals meerdere miljard rijen, gebruikt u `partitionOption` en `partitionSetting` om de gegevens te splitsen in kleinere partities. In dit geval worden de gegevens per partitie gelezen en elke gegevens partitie wordt opgehaald van uw SAP-server via één RFC-aanroep.<br/>
<br/>
>Als u `partitionOption` `partitionOnInt` als een voor beeld neemt, wordt het aantal rijen in elke partitie berekend met deze formule: (totaal aantal rijen tussen `partitionUpperBound` en `partitionLowerBound`)/`maxPartitionsNumber`.<br/>
<br/>
>Als u gegevens partities parallel wilt laden om de kopie sneller te maken, wordt de parallelle graad bepaald door de [`parallelCopies`](copy-activity-performance.md#parallel-copy) instelling van de Kopieer activiteit. Als u bijvoorbeeld `parallelCopies` instelt op vier, worden met Data Factory gelijktijdig vier query's gegenereerd en uitgevoerd op basis van uw opgegeven partitie optie en instellingen, en met elke query wordt een deel van de gegevens uit uw SAP-tabel opgehaald. We raden u ten zeerste aan `maxPartitionsNumber` een veelvoud van de waarde van de eigenschap `parallelCopies` te maken. Bij het kopiëren van gegevens naar gegevens opslag op basis van een bestand, is het ook opnieuw opdracht om naar een map te schrijven als meerdere bestanden (Geef alleen de mapnaam op). in dat geval zijn de prestaties beter dan het schrijven naar één bestand.

In `rfcTableOptions`kunt u de volgende algemene SAP-query operators gebruiken om de rijen te filteren:

| Operator | Beschrijving |
| :------- | :------- |
| `EQ` | Gelijk aan |
| `NE` | Niet gelijk aan |
| `LT` | Kleiner dan |
| `LE` | Kleiner dan of gelijk aan |
| `GT` | Groter dan |
| `GE` | Groter dan of gelijk aan |
| `LIKE` | Als in `LIKE 'Emma%'` |

### <a name="example"></a>Voorbeeld

```json
"activities":[
    {
        "name": "CopyFromSAPTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP table input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SapTableSource",
                "partitionOption": "PartitionOnInt",
                "partitionSettings": {
                     "partitionColumnName": "<partition column name>",
                     "partitionUpperBound": "2000",
                     "partitionLowerBound": "1",
                     "maxPartitionsNumber": 500
                 }
            },
            "sink": {
                "type": "<sink type>"
            },
            "parallelCopies": 4
        }
    }
]
```

## <a name="data-type-mappings-for-an-sap-table"></a>Toewijzing van gegevens typen voor een SAP-tabel

Wanneer u gegevens uit een SAP-tabel kopieert, worden de volgende toewijzingen gebruikt vanuit de gegevens typen van de SAP-tabel naar het Azure Data Factory tussenliggende gegevens typen. Zie [schema en gegevens type toewijzingen](copy-activity-schema-and-type-mapping.md)voor meer informatie over hoe de Kopieer activiteit het bron schema en het gegevens type aan de Sink koppelt.

| SAP ABAP-type | Data Factory tussentijdse gegevenstype |
|:--- |:--- |
| `C` (teken reeks) | `String` |
| `I` (geheel getal) | `Int32` |
| `F` (vlotter) | `Double` |
| `D` (datum) | `String` |
| `T` (tijd) | `String` |
| `P` (ingepakte BCD, valuta, decimaal, hoeveelheid) | `Decimal` |
| `N` (numeriek) | `String` |
| `X` (binair en onbewerkt) | `String` |

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.


## <a name="next-steps"></a>Volgende stappen

Zie [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die worden ondersteund als bronnen en sinks op basis van de Kopieer activiteit in azure Data Factory.
