---
title: Gegevens kopiëren van SAP HANA
description: Informatie over het kopiëren van gegevens van SAP HANA naar ondersteunde Sink-gegevens archieven door gebruik te maken van een Kopieer activiteit in een Azure Data Factory-pijp lijn.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/17/2020
ms.openlocfilehash: fa165c21622110bb18476efdebf3264a11e26ad7
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79265881"
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>Gegevens kopiëren van SAP HANA met behulp van Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van Data Factory service die u gebruikt:"]
> * [Versie 1](v1/data-factory-sap-hana-connector.md)
> * [Huidige versie](connector-sap-hana.md)

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens uit een SAP HANA-data base te kopiëren. Het is gebaseerd op het artikel overzicht van de [Kopieer activiteit](copy-activity-overview.md) . Dit geeft een algemeen overzicht van de Kopieer activiteit.

>[!TIP]
>Zie [SAP Data Integration using Azure Data Factory White Paper](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) with introduction, comparsion en guidance (Engelstalig) voor meer informatie over de algemene ondersteuning van de ADF op SAP Data Integration scenario.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze SAP HANA-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

U kunt gegevens uit SAP HANA data base kopiëren naar elk ondersteund Sink-gegevens archief. Zie de tabel [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als bron/sinks op basis van de Kopieer activiteit.

Deze SAP HANA-connector ondersteunt met name:

- Gegevens kopiëren uit elke versie van SAP HANA data base.
- Gegevens kopiëren van **Hana-informatie modellen** (zoals analytische en berekenings weergaven) en **rij-en kolom tabellen**.
- Kopiëren van gegevens met behulp van **basis** -of **Windows** -verificatie.
- Parallelle kopieën van een SAP HANA bron. Zie de sectie [parallelle kopie van SAP Hana](#parallel-copy-from-sap-hana) voor meer informatie.

> [!TIP]
> Als u gegevens wilt kopiëren **naar** SAP Hana gegevens archief, gebruikt u de algemene ODBC-Connector. Zie [SAP Hana Sink](connector-odbc.md#sap-hana-sink) met details. Houd er rekening mee dat de gekoppelde services voor SAP HANA connector en ODBC-Connector met een ander type dan niet opnieuw kunnen worden gebruikt.

## <a name="prerequisites"></a>Vereisten

Als u deze SAP HANA-connector wilt gebruiken, moet u het volgende doen:

- Stel een zelf-hostende Integration Runtime in. Zie [zelf-hostende Integration runtime](create-self-hosted-integration-runtime.md) artikel voor meer informatie.
- Installeer het SAP HANA ODBC-stuur programma op de Integration Runtime computer. U kunt het ODBC-stuur programma van SAP HANA downloaden via het [SAP-software Download centrum](https://support.sap.com/swdc). Zoek met het tref woord **SAP Hana client voor Windows**.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor SAP HANA-connector.

## <a name="linked-service-properties"></a>Eigenschappen van de gekoppelde service

De volgende eigenschappen worden ondersteund voor SAP HANA gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op: **SapHana** | Ja |
| connectionString | Geef de gegevens op die nodig zijn om verbinding te maken met de SAP HANA met behulp van **basis verificatie** of **Windows-verificatie**. Raadpleeg de volgende voor beelden.<br>In connection string is server/poort verplicht (de standaard poort is 30015), en de gebruikers naam en het wacht woord zijn verplicht wanneer basis verificatie wordt gebruikt. Raadpleeg [SAP Hana ODBC-verbindings eigenschappen](<https://help.sap.com/viewer/0eec0d68141541d1b07893a39944924e/2.0.02/en-US/7cab593774474f2f8db335710b2f5c50.html>) voor aanvullende geavanceerde instellingen<br/>U kunt ook wacht woord in Azure Key Vault plaatsen en de wachtwoord configuratie uit de connection string halen. Raadpleeg de [referenties voor opslaan in azure Key Vault](store-credentials-in-key-vault.md) artikel met meer informatie. | Ja |
| userName | Geef een gebruikers naam op bij het gebruik van Windows-verificatie. Voorbeeld: `user@domain.com` | Nee |
| wachtwoord | Geef het wacht woord voor het gebruikers account op. Markeer dit veld als SecureString om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). | Nee |
| connectVia | Het [Integration runtime](concepts-integration-runtime.md) dat moet worden gebruikt om verbinding te maken met het gegevens archief. Een zelf-hostende Integration Runtime is vereist zoals vermeld in de [vereisten](#prerequisites). |Ja |

**Voor beeld: basis verificatie gebruiken**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;UID=<userName>;PWD=<Password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Voor beeld: Windows-verificatie gebruiken**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;",
            "userName": "<username>", 
            "password": { 
                "type": "SecureString", 
                "value": "<password>" 
            } 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Als u SAP HANA gekoppelde service met de volgende Payload gebruikt, wordt deze nog steeds ondersteund als-is, terwijl u wordt geadviseerd om het nieuwe item te gebruiken.

**Voorbeeld:**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server>:<port (optional)>",
            "authenticationType": "Basic",
            "userName": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. Deze sectie bevat een lijst met eigenschappen die door SAP HANA DataSet worden ondersteund.

De volgende eigenschappen worden ondersteund voor het kopiëren van gegevens uit SAP HANA:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **SapHanaTable** | Ja |
| schema | De naam van het schema in de SAP HANA-data base. | Nee (als 'query' in de activiteitbron is opgegeven) |
| table | De naam van de tabel in de SAP HANA-data base. | Nee (als 'query' in de activiteitbron is opgegeven) |

**Voorbeeld:**

```json
{
    "name": "SAPHANADataset",
    "properties": {
        "type": "SapHanaTable",
        "typeProperties": {
            "schema": "<schema name>",
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP HANA linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Als u `RelationalTable` getypte gegevensset gebruikt, wordt deze nog steeds ondersteund als-is, terwijl u wordt geadviseerd om het nieuwe item te gebruiken.

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Deze sectie bevat een lijst met eigenschappen die door SAP HANA bron worden ondersteund.

### <a name="sap-hana-as-source"></a>SAP HANA als bron

>[!TIP]
>Als u gegevens van SAP HANA efficiënt wilt opnemen met behulp van gegevens partitioneren, kunt u meer informatie vinden op basis van de [parallelle kopie van SAP Hana](#parallel-copy-from-sap-hana) sectie.

Als u gegevens wilt kopiëren uit SAP HANA, worden de volgende eigenschappen ondersteund in de sectie **bron** van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op: **SapHanaSource** | Ja |
| query | Hiermee geeft u de SQL-query voor het lezen van gegevens uit het SAP HANA-exemplaar. | Ja |
| partitionOptions | Hiermee geeft u de opties voor gegevenspartitionering op waarmee gegevens van SAP HANA worden opgenomen. Meer informatie over de [parallelle kopie van de SAP Hana](#parallel-copy-from-sap-hana) sectie.<br>Toegestane waarden zijn: **geen** (standaard), **PhysicalPartitionsOfTable**, **SapHanaDynamicRange**. Meer informatie over de [parallelle kopie van de SAP Hana](#parallel-copy-from-sap-hana) sectie. `PhysicalPartitionsOfTable` kan alleen worden gebruikt bij het kopiëren van gegevens uit een tabel, maar niet voor query's. <br>Wanneer een partitie optie is ingeschakeld (dat wil zeggen, niet `None`), is de mate van parallelle uitvoering om gegevens van SAP HANA gelijktijdig te laden, bepaald door de instelling [`parallelCopies`](copy-activity-performance.md#parallel-copy) van de Kopieer activiteit. | False |
| partitionSettings | Geef de groep van de instellingen voor het partitioneren van gegevens op.<br>Toep assen als de partitie optie `SapHanaDynamicRange`is. | False |
| partitionColumnName | Geef de naam op van de bron kolom die wordt gebruikt voor de partitie voor parallelle kopieën. Als u niets opgeeft, wordt de index of de primaire sleutel van de tabel automatisch gedetecteerd en gebruikt als de partitie kolom.<br>Toep assen wanneer de partitie optie is `SapHanaDynamicRange`. Als u een query gebruikt om de bron gegevens op te halen, Hook `?AdfHanaDynamicRangePartitionCondition` in de component WHERE. Zie voor beeld in [parallelle kopie van SAP Hana](#parallel-copy-from-sap-hana) sectie. | Ja bij het gebruik van `SapHanaDynamicRange` partitie. |
| packetSize | Hiermee geeft u de grootte van het netwerk pakket (in kilo bytes) op om gegevens te splitsen in meerdere blokken. Als u grote hoeveel heden gegevens moet kopiëren, kan de grootte van het pakket verhogen in de meeste gevallen de Lees snelheid van SAP HANA. Prestatie testen worden aanbevolen bij het aanpassen van de pakket grootte. | Nee.<br>De standaard waarde is 2048 (2 MB). |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromSAPHANA",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP HANA input dataset name>",
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
                "type": "SapHanaSource",
                "query": "<SQL query for SAP HANA>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Als u `RelationalSource` getypte Kopieer bron gebruikt, wordt deze nog steeds ondersteund als-is. u wordt aangeraden het nieuwe item te gebruiken.

## <a name="parallel-copy-from-sap-hana"></a>Parallelle kopie van SAP HANA

De Data Factory SAP HANA-connector biedt ingebouwde gegevenspartitionering om gegevens van SAP HANA parallel te kopiëren. U kunt opties voor gegevens partities vinden in de **bron** tabel van de Kopieer activiteit.

![Scherm opname van partitie opties](./media/connector-sap-hana/connector-sap-hana-partition-options.png)

Wanneer u gepartitioneerde kopie inschakelt, voert Data Factory parallelle query's uit op uw SAP HANA bron om gegevens op te halen op basis van partities. De parallelle graad wordt bepaald door de instelling [`parallelCopies`](copy-activity-performance.md#parallel-copy) op de Kopieer activiteit. Als u bijvoorbeeld `parallelCopies` instelt op vier, worden met Data Factory gelijktijdig vier query's gegenereerd en uitgevoerd op basis van uw opgegeven partitie optie en instellingen, en elke query haalt een deel van de gegevens op uit de SAP HANA.

U wordt aangeraden om parallelle kopieën in te scha kelen met gegevens partities met name wanneer u grote hoeveel heden gegevens uit uw SAP HANA opneemt. Hieronder vindt u de aanbevolen configuraties voor verschillende scenario's. Bij het kopiëren van gegevens naar gegevens opslag op basis van een bestand kunt u het beste naar een map schrijven als meerdere bestanden (Geef alleen de mapnaam op). in dat geval is de prestaties beter dan het schrijven naar één bestand.

| Scenario                                           | Voorgestelde instellingen                                           |
| -------------------------------------------------- | ------------------------------------------------------------ |
| Volledige belasting van een grote tabel.                        | **Partitie optie**: fysieke partities van tabel. <br><br/>Tijdens de uitvoering van Data Factory detecteert het fysieke partitie type van de opgegeven SAP HANA tabel automatisch en kiest de bijbehorende partitie strategie:<br>- **bereik partitioneren**: Haal de kolom Partition en de partitielay-adresbereiken op die zijn gedefinieerd voor de tabel en kopieer de gegevens vervolgens per bereik. <br>- **hash-partitionering**: hash-partitie sleutel gebruiken als partitie kolom, partitioneren en kopiëren van de gegevens op basis van berekende ADF-bereiken. <br>- **Round-Robin partitionering** of **geen partitie**: primaire sleutel als partitie kolom gebruiken, partitioneren en kopiëren van de gegevens op basis van berekende ADF-bereiken. |
| Laad grote hoeveelheid gegevens met behulp van een aangepaste query. | **Partitie optie**: partitie met dynamisch bereik.<br>**Query**: `SELECT * FROM <TABLENAME> WHERE ?AdfHanaDynamicRangePartitionCondition AND <your_additional_where_clause>`.<br>**Partitie kolom**: Geef de kolom op die wordt gebruikt voor het Toep assen van een dynamische bereik partitie. <br><br>Tijdens de uitvoering berekent Data Factory eerst de waardebereiken van de opgegeven partitie kolom, door de rijen in een aantal buckets gelijkmatig te verdelen op basis van het aantal afzonderlijke partitie kolom waarden en de instelling van de parallelle kopie van de ADF, waarna `?AdfHanaDynamicRangePartitionCondition` wordt vervangen door het bereik van kolom waarden voor elke partitie te filteren en naar SAP HANA wordt verzonden.<br><br>Als u meerdere kolommen als partitie kolom wilt gebruiken, kunt u de waarden van elke kolom als één kolom in de query samen voegen en opgeven als partitie kolom in ADF, zoals `SELECT * FROM (SELECT *, CONCAT(<KeyColumn1>, <KeyColumn2>) AS PARTITIONCOLUMN FROM <TABLENAME>) WHERE ?AdfHanaDynamicRangePartitionCondition`. |

**Voor beeld: query met fysieke partities van een tabel**

```json
"source": {
    "type": "SapHanaSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Voor beeld: query met een dynamische bereik partitie**

```json
"source": {
    "type": "SapHanaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHanaDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "SapHanaDynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<Partition_column_name>"
    }
}
```

## <a name="data-type-mapping-for-sap-hana"></a>Toewijzing van gegevens type voor SAP HANA

Bij het kopiëren van gegevens uit SAP HANA worden de volgende toewijzingen gebruikt van SAP HANA gegevens typen om tussenliggende gegevens typen te Azure Data Factory. Zie [schema-en gegevens type toewijzingen](copy-activity-schema-and-type-mapping.md) voor meer informatie over hoe kopieer activiteit het bron schema en het gegevens type aan de Sink koppelt.

| SAP HANA gegevens type | Data factory tussentijdse gegevenstype |
| ------------------ | ------------------------------ |
| ALPHANUM           | Tekenreeks                         |
| BIGINT             | Int64                          |
| BINARY             | Byte[]                         |
| BINTEXT            | Tekenreeks                         |
| BLOB               | Byte[]                         |
| BOOL               | Byte                           |
| CLOB               | Tekenreeks                         |
| DATE               | DateTime                       |
| DECIMAL            | decimaal                        |
| DOUBLE             | Double-waarde                         |
| FLOAT              | Double-waarde                         |
| INTEGER            | Int32                          |
| NCLOB              | Tekenreeks                         |
| NVARCHAR           | Tekenreeks                         |
| REAL               | Enkelvoudig                         |
| SECONDDATE         | DateTime                       |
| SHORTTEXT          | Tekenreeks                         |
| SMALLDECIMAL       | decimaal                        |
| SMALLINT           | Int16                          |
| STGEOMETRYTYPE     | Byte[]                         |
| STPOINTTYPE        | Byte[]                         |
| TEXT               | Tekenreeks                         |
| TIME               | TimeSpan                       |
| TINYINT            | Byte                           |
| VARCHAR            | Tekenreeks                         |
| TIMESTAMP          | DateTime                       |
| VARBINARY          | Byte[]                         |

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.

## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die worden ondersteund als bronnen en sinks op basis van de Kopieer activiteit in azure Data Factory.
