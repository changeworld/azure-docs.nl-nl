---
title: Gegevens kopiëren uit Sales Force marketing Cloud
description: Meer informatie over het kopiëren van gegevens uit Salesforce Marketing Cloud naar ondersteunde sink-gegevensopslag met behulp van een kopieeractiviteit in een Azure Data Factory-pijplijn.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/25/2019
ms.openlocfilehash: aa996ae14276ecf246104d8778fbb6b723448e04
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931724"
---
# <a name="copy-data-from-salesforce-marketing-cloud-using-azure-data-factory"></a>Gegevens kopiëren uit Sales Force marketing Cloud met behulp van Azure Data Factory

In dit artikel bevat een overzicht over het gebruik van de Kopieeractiviteit in Azure Data Factory om gegevens te kopiëren uit Salesforce Marketing Cloud. Dit is gebaseerd op de [overzicht kopieeractiviteit](copy-activity-overview.md) artikel met daarin een algemeen overzicht van de kopieeractiviteit.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Sales Force marketing-Cloud connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

U kunt gegevens uit Salesforce Marketing Cloud kopiëren naar een ondersteunde sink-gegevensopslag. Zie voor een lijst met gegevensarchieven die worden ondersteund als bronnen/put door de kopieeractiviteit, de [ondersteunde gegevensarchieven](copy-activity-overview.md#supported-data-stores-and-formats) tabel.

De Sales Force marketing Cloud connector ondersteunt OAuth 2-verificatie. Het is gebouwd op basis van de [Sales Force marketing Cloud rest API](https://developer.salesforce.com/docs/atlas.en-us.mc-apis.meta/mc-apis/index-api.htm).

>[!NOTE]
>Deze connector biedt geen ondersteuning voor het ophalen van aangepaste objecten of aangepaste gegevens extensies.

## <a name="getting-started"></a>Aan de slag

U kunt een pijplijn maken met copy activity in .NET SDK, Python-SDK, Azure PowerShell, REST-API of Azure Resource Manager-sjabloon. Zie [zelfstudie Kopieeractiviteit](quickstart-create-data-factory-dot-net.md) voor stapsgewijze instructies voor het maken van een pijplijn met een kopieeractiviteit.

De volgende secties bevatten meer informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory-entiteiten specifieke naar Salesforce Marketing Cloud-connector.

## <a name="linked-service-properties"></a>Eigenschappen van de gekoppelde service

De volgende eigenschappen worden ondersteund voor Salesforce Marketing Cloud gekoppelde service:

| Eigenschap | Beschrijving | Verplicht |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op: **salesforcemarketingcloud zijn** | Ja |
| clientId | De client-ID die is gekoppeld aan de Salesforce Marketing Cloud-toepassing.  | Ja |
| clientSecret | Het clientgeheim die zijn gekoppeld aan de Salesforce Marketing Cloud-toepassing. U kunt dit veld markeren als SecureString om het veilig op te slaan in ADF, of het wacht woord op te slaan in Azure Key Vault en de ADF-Kopieer activiteit uit te voeren tijdens het uitvoeren van de gegevens kopie: meer informatie over [referenties voor opslaan in Key Vault](store-credentials-in-key-vault.md). | Ja |
| useEncryptedEndpoints | Hiermee geeft u op of de eindpunten van de gegevensbron zijn versleuteld met behulp van HTTPS. De standaardwaarde is true.  | Nee |
| useHostVerification | Hiermee geeft u op of de hostnaam van de in het certificaat van de server zodat deze overeenkomen met de hostnaam van de server wanneer u verbinding maakt via SSL vereist. De standaardwaarde is true.  | Nee |
| usePeerVerification | Hiermee geeft u op of u wilt controleren of de identiteit van de server wanneer u verbinding maakt via SSL. De standaardwaarde is true.  | Nee |

**Voorbeeld:**

```json
{
    "name": "SalesforceMarketingCloudLinkedService",
    "properties": {
        "type": "SalesforceMarketingCloud",
        "typeProperties": {
            "clientId" : "<clientId>",
            "clientSecret": {
                 "type": "SecureString",
                 "value": "<clientSecret>"
            },
            "useEncryptedEndpoints" : true,
            "useHostVerification" : true,
            "usePeerVerification" : true
        }
    }
}

```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie voor een volledige lijst van de secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets, de [gegevenssets](concepts-datasets-linked-services.md) artikel. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door Marketing Cloud Salesforce-gegevensset.

Als u wilt kopiëren van gegevens uit Salesforce Marketing Cloud, stel de eigenschap type van de gegevensset in **SalesforceMarketingCloudObject**. De volgende eigenschappen worden ondersteund:

| Eigenschap | Beschrijving | Verplicht |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **SalesforceMarketingCloudObject** | Ja |
| tableName | Naam van de tabel. | Nee (als 'query' in de activiteitbron is opgegeven) |

**Voorbeeld**

```json
{
    "name": "SalesforceMarketingCloudDataset",
    "properties": {
        "type": "SalesforceMarketingCloudObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SalesforceMarketingCloud linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie voor een volledige lijst van de secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten, de [pijplijnen](concepts-pipelines-activities.md) artikel. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door Salesforce Marketing Cloud-bron.

### <a name="salesforce-marketing-cloud-as-source"></a>SalesForce Marketing Cloud als bron

Als u wilt kopiëren van gegevens uit Salesforce Marketing Cloud, stelt u het brontype in de kopieeractiviteit naar **SalesforceMarketingCloudSource**. De volgende eigenschappen worden ondersteund in de kopieeractiviteit **source** sectie:

| Eigenschap | Beschrijving | Verplicht |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron voor kopiëren-activiteit moet worden ingesteld op: **SalesforceMarketingCloudSource** | Ja |
| query | Gebruik de aangepaste SQL-query om gegevens te lezen. Bijvoorbeeld: `"SELECT * FROM MyTable"`. | Nee (als de 'tableName' in de gegevensset is opgegeven) |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromSalesforceMarketingCloud",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SalesforceMarketingCloud input dataset name>",
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
                "type": "SalesforceMarketingCloudSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.

## <a name="next-steps"></a>Volgende stappen
Zie voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Azure Data Factory, [ondersteunde gegevensarchieven](copy-activity-overview.md#supported-data-stores-and-formats).
