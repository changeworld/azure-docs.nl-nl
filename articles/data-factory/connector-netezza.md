---
title: Gegevens kopiëren van Netezza met behulp van Azure Data Factory
description: Leer hoe u gegevens kopiëren van Netezza naar ondersteunde sink-gegevensopslag met behulp van een kopieeractiviteit in een Azure Data Factory-pijplijn.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: jingwang
ms.openlocfilehash: c51469997af23be7a5e1b88677ecadb37e10ac64
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79244535"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory"></a>Gegevens kopiëren van Netezza met behulp van Azure Data Factory

In dit artikel bevat een overzicht over het gebruik van de Kopieeractiviteit in Azure Data Factory om gegevens te kopiëren van Netezza. Het artikel bouwt voort op de [Kopieer activiteit in azure Data Factory](copy-activity-overview.md), waarin een algemeen overzicht van de Kopieer activiteit wordt weer gegeven.

>[!TIP]
>Voor het scenario voor gegevens migratie van Netezza naar Azure leert u meer over [het gebruik van Azure Data Factory voor het migreren van gegevens van een on-premises Netezza-server naar Azure](data-migration-guidance-netezza-azure-sqldw.md).

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Netezza-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)


U kunt gegevens uit Netezza kopiëren naar een ondersteunde sink-gegevensopslag. Zie [ondersteunde gegevens archieven en-indelingen](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die door de Kopieer activiteit worden ondersteund als bronnen en Sinks.

Netezza-connector ondersteunt parallelle Kopieer bewerking vanuit de bron. Zie de sectie [parallelle kopie van Netezza](#parallel-copy-from-netezza) voor meer informatie.

Azure Data Factory biedt een ingebouwde stuurprogramma als connectiviteit wilt inschakelen. U hoeft niet te installeren op een stuurprogramma voor het gebruik van deze connector.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Aan de slag

U kunt een pijplijn met behulp van een kopieeractiviteit met behulp van de .NET SDK, de Python-SDK, Azure PowerShell, de REST-API of een Azure Resource Manager-sjabloon maken. Raadpleeg de [zelf studie activiteit kopiëren](quickstart-create-data-factory-dot-net.md) voor stapsgewijze instructies voor het maken van een pijp lijn met een Kopieer activiteit.

De volgende secties bevatten meer informatie over eigenschappen die u gebruiken kunt voor het definiëren van Data Factory-entiteiten die specifiek voor de Netezza-connector zijn.

## <a name="linked-service-properties"></a>Eigenschappen van de gekoppelde service

De volgende eigenschappen worden ondersteund voor de service Netezza gekoppeld:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** moet worden ingesteld op **Netezza**. | Ja |
| connectionString | Een ODBC-verbindingsreeks verbinding maken met Netezza. <br/>U kunt ook wacht woord in Azure Key Vault plaatsen en de `pwd` configuratie uit de connection string halen. Raadpleeg de volgende voor beelden en [Sla referenties op in azure Key Vault](store-credentials-in-key-vault.md) artikel met meer informatie. | Ja |
| connectVia | De [Integration runtime](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevens archief. Meer informatie vindt u in de sectie [vereisten](#prerequisites) . Indien niet opgegeven, wordt de standaard Azure Integration Runtime wordt gebruikt. |Nee |

Een typische connection string is `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>`. De volgende tabel bevat meer eigenschappen die u kunt instellen:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| SecurityLevel | Het niveau van beveiliging (SSL/TLS) die gebruikmaakt van het stuurprogramma voor de verbinding met het gegevensarchief. Voor beeld: `SecurityLevel=preferredSecured`. Ondersteunde waarden zijn:<br/>Alleen - niet **beveiligd** (**OnlyUnSecured**): het stuur programma maakt geen gebruik van SSL.<br/>- **voor keur niet-beveiligd (preferredUnSecured) (standaard)** : als de server een keuze biedt, gebruikt het stuur programma geen SSL. <br/>- **Voorkeurs beveiliging (preferredSecured)** : als de server een keuze biedt, maakt het stuur programma gebruik van SSL. <br/>- **alleen beveiligd (onlySecured)** : het stuur programma kan geen verbinding maken tenzij er een SSL-verbinding beschikbaar is. | Nee |
| CaCertFile | Het volledige pad naar het SSL-certificaat dat wordt gebruikt door de server. Voorbeeld: `CaCertFile=<cert path>;`| Ja, als SSL is ingeschakeld |

**Voorbeeld**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Voor beeld: wacht woord opslaan in Azure Key Vault**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;",
            "pwd": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

Deze sectie bevat een lijst met eigenschappen die ondersteuning biedt voor de gegevensset Netezza.

Zie [gegevens sets](concepts-datasets-linked-services.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets.

Als u gegevens van Netezza wilt kopiëren, stelt u de eigenschap **type** van de gegevensset in op **NetezzaTable**. De volgende eigenschappen worden ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **NetezzaTable** | Ja |
| schema | De naam van het schema. |Nee (als 'query' in de activiteitbron is opgegeven)  |
| table | Naam van de tabel. |Nee (als 'query' in de activiteitbron is opgegeven)  |
| tableName | De naam van de tabel met schema. Deze eigenschap wordt ondersteund voor achterwaartse compatibiliteit. Gebruik `schema` en `table` voor nieuwe workloads. | Nee (als 'query' in de activiteitbron is opgegeven) |

**Voorbeeld**

```json
{
    "name": "NetezzaDataset",
    "properties": {
        "type": "NetezzaTable",
        "linkedServiceName": {
            "referenceName": "<Netezza linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Deze sectie bevat een lijst met eigenschappen die ondersteuning biedt voor de bron Netezza.

Zie [pijp lijnen](concepts-pipelines-activities.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten.

### <a name="netezza-as-source"></a>Netezza als bron

>[!TIP]
>Als u gegevens van Netezza efficiënt wilt laden met behulp van gegevens partitioneren, kunt u meer informatie vinden in de sectie [parallel kopiëren vanuit Netezza](#parallel-copy-from-netezza) .

Als u gegevens van Netezza wilt kopiëren, stelt u het **bron** type in de Kopieer activiteit in op **NetezzaSource**. De volgende eigenschappen worden ondersteund in de sectie **bron** van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** van de bron van de Kopieer activiteit moet zijn ingesteld op **NetezzaSource**. | Ja |
| query | Gebruik de aangepaste SQL-query om gegevens te lezen. Voorbeeld: `"SELECT * FROM MyTable"` | Nee (als de 'tableName' in de gegevensset is opgegeven) |
| partitionOptions | Hiermee geeft u de opties voor gegevens partities op die worden gebruikt voor het laden van gegevens uit Netezza. <br>Toegestane waarden zijn: **geen** (standaard), **DataSlice**en **DynamicRange**.<br>Wanneer een partitie optie is ingeschakeld (dat wil zeggen, niet `None`), is de mate van parallelle uitvoering om gegevens uit een Netezza-data base gelijktijdig te laden, beheerd door [`parallelCopies`](copy-activity-performance.md#parallel-copy) instelling van de Kopieer activiteit. | Nee |
| partitionSettings | Geef de groep van de instellingen voor het partitioneren van gegevens op. <br>Toep assen als de partitie optie niet `None`is. | Nee |
| partitionColumnName | Geef de naam op van de bron kolom **in een geheel getal** dat wordt gebruikt voor het partitioneren van het bereik voor parallelle kopieën. Als u niets opgeeft, wordt de primaire sleutel van de tabel automatisch gedetecteerd en gebruikt als de kolom partitie. <br>Toep assen wanneer de partitie optie is `DynamicRange`. Als u een query gebruikt om de bron gegevens op te halen, Hook `?AdfRangePartitionColumnName` in de component WHERE. Zie voor beeld in [parallelle kopie van](#parallel-copy-from-netezza) de sectie Netezza. | Nee |
| partitionUpperBound | De maximum waarde van de partitie kolom waaruit de gegevens moeten worden gekopieerd. <br>Toep assen als de partitie optie `DynamicRange`is. Als u query gebruikt om bron gegevens op te halen, Hook `?AdfRangePartitionUpbound` in de component WHERE. Zie de sectie [parallelle kopie van Netezza](#parallel-copy-from-netezza) voor een voor beeld. | Nee |
| partitionLowerBound | De minimum waarde van de partitie kolom waaruit de gegevens moeten worden gekopieerd. <br>Toep assen wanneer de partitie optie is `DynamicRange`. Als u een query gebruikt om de bron gegevens op te halen, Hook `?AdfRangePartitionLowbound` in de component WHERE. Zie de sectie [parallelle kopie van Netezza](#parallel-copy-from-netezza) voor een voor beeld. | Nee |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromNetezza",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Netezza input dataset name>",
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
                "type": "NetezzaSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-netezza"></a>Parallelle kopie van Netezza

De Data Factory Netezza-connector biedt ingebouwde gegevenspartitionering om gegevens van Netezza parallel te kopiëren. U kunt opties voor gegevens partities vinden in de **bron** tabel van de Kopieer activiteit.

![Scherm opname van partitie opties](./media/connector-netezza/connector-netezza-partition-options.png)

Wanneer u gepartitioneerde kopie inschakelt, voert Data Factory parallelle query's uit op uw Netezza-bron om gegevens per partitie te laden. De parallelle graad wordt bepaald door de instelling [`parallelCopies`](copy-activity-performance.md#parallel-copy) op de Kopieer activiteit. Als u bijvoorbeeld `parallelCopies` instelt op vier, worden met Data Factory gelijktijdig vier query's gegenereerd en uitgevoerd op basis van uw opgegeven partitie optie en instellingen, en elke query haalt een deel van de gegevens op uit uw Netezza-data base.

U wordt aangeraden om parallelle kopieën in te scha kelen met gegevens partities met name wanneer u grote hoeveel heden gegevens uit uw Netezza-data base laadt. Hieronder vindt u de aanbevolen configuraties voor verschillende scenario's. Bij het kopiëren van gegevens naar gegevens opslag op basis van een bestand, is het opnieuw opdracht om naar een map te schrijven als meerdere bestanden (Geef alleen de mapnaam op). in dat geval is de prestaties beter dan het schrijven naar één bestand.

| Scenario                                                     | Voorgestelde instellingen                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Volledige belasting van een grote tabel.                                   | **Partitie optie**: gegevens segment. <br><br/>Tijdens de uitvoering van Data Factory worden de gegevens automatisch gepartitioneerd op basis van [de ingebouwde gegevens segmenten van Netezza](https://www.ibm.com/support/knowledgecenter/en/SSULQD_7.2.1/com.ibm.nz.adm.doc/c_sysadm_data_slices_parts_disks.html)en worden gegevens gekopieerd op partities. |
| Laad grote hoeveelheid gegevens met behulp van een aangepaste query.                 | **Partitie optie**: gegevens segment.<br>**Query**: `SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>`.<br>Tijdens de uitvoering worden Data Factory vervangen door de `?AdfPartitionCount` (waarbij het parallelle kopie nummer is ingesteld op de Kopieer activiteit) en `?AdfDataSliceCondition` met de partitie logica van het gegevens segment en verzonden naar Netezza. |
| Laad een grote hoeveelheid gegevens met behulp van een aangepaste query, met een kolom met gehele getallen met een gelijkmatig gedistribueerde waarde voor bereik partitionering. | **Partitie opties**: partitie met dynamisch bereik.<br>**Query**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Partitie kolom**: Geef de kolom op die wordt gebruikt om gegevens te partitioneren. U kunt de kolom met het gegevens type geheel getal partitioneren.<br>**Bovengrens van partities** en partities met een **ondergrens**: Geef op of u wilt filteren op de partitie kolom om alleen gegevens tussen het onderste en het bovenste bereik op te halen.<br><br>Tijdens de uitvoering Data Factory worden `?AdfRangePartitionColumnName`, `?AdfRangePartitionUpbound`en `?AdfRangePartitionLowbound` vervangen door de werkelijke kolom naam en het waardebereik voor elke partitie, en verzonden naar Netezza. <br>Als uw partitie kolom "ID" bijvoorbeeld is ingesteld met de ondergrens als 1 en de bovengrens als 80, met een parallelle kopie ingesteld als 4, Data Factory worden gegevens opgehaald met vier partities. Hun Id's liggen respectievelijk tussen [1, 20], [21, 40], [41, 60] en [61, 80]. |

**Voor beeld: query met gegevens segment partitie**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>",
    "partitionOption": "DataSlice"
}
```

**Voor beeld: query met een dynamische bereik partitie**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.


## <a name="next-steps"></a>Volgende stappen

Zie [ondersteunde gegevens archieven en-indelingen](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die door de Kopieer activiteit worden ondersteund als bronnen en sinks in azure Data Factory.
