---
title: Gegevens transformeren met Databricks notebook
description: Meer informatie over het verwerken of transformeren van gegevens door een Databricks-notebook uit te voeren.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: nabhishek
ms.author: abnarain
manager: shwang
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: c7a2aec35511ef066033c3d6462143ac31660e76
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74923061"
---
# <a name="transform-data-by-running-a-databricks-notebook"></a>Gegevens transformeren door een Databricks-notebook uit te voeren

Met de activiteit Azure Databricks notebook in een [Data Factory pijp lijn](concepts-pipelines-activities.md) wordt een Databricks-notebook in uw Azure Databricks-werk ruimte uitgevoerd. In dit artikel vindt u een overzicht van de [activiteiten voor gegevens transformatie](transform-data.md) , waarmee u een algemene weer gave van gegevens transformatie en ondersteunde transformatie activiteiten krijgt. Azure Databricks is een beheerd platform voor het uitvoeren van Apache Spark.

## <a name="databricks-notebook-activity-definition"></a>Definitie van Databricks-notebook-activiteit

Hier volgt een voor beeld van de JSON-definitie van een Databricks-notebook activiteit:

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksNotebook",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "notebookPath": "/Users/user@example.com/ScalaExampleNotebook",
            "baseParameters": {
                "inputpath": "input/folder1/",
                "outputpath": "output/"
            },
            "libraries": [
                {
                "jar": "dbfs:/docs/library.jar"
                }
            ]
        }
    }
}
```

## <a name="databricks-notebook-activity-properties"></a>Eigenschappen van Databricks notebook-activiteit

In de volgende tabel worden de JSON-eigenschappen beschreven die in de JSON-definitie worden gebruikt:

|Eigenschap|Beschrijving|Verplicht|
|---|---|---|
|name|De naam van de activiteit in de pijp lijn.|Ja|
|description|Tekst die beschrijft wat de activiteit doet.|Nee|
|type|Voor Databricks notebook-activiteit is het type activiteit DatabricksNotebook.|Ja|
|linkedServiceName|De naam van de gekoppelde Databricks-service waarop de Databricks-notebook wordt uitgevoerd. Zie voor meer informatie over deze gekoppelde service artikel [Compute linked Services](compute-linked-services.md) (Engelstalig).|Ja|
|notebookPath|Het absolute pad van de notebook dat moet worden uitgevoerd in de Databricks-werk ruimte. Dit pad moet beginnen met een slash.|Ja|
|baseParameters|Een matrix met sleutel-waardeparen. Basis parameters kunnen worden gebruikt voor elke uitvoering van de activiteit. Als het notitie blok een para meter accepteert die niet is opgegeven, wordt de standaard waarde van het notitie blok gebruikt. Meer informatie over para meters in [Databricks-notebooks](https://docs.databricks.com/api/latest/jobs.html#jobsparampair).|Nee|
|bibliotheken|Een lijst met bibliotheken die op het cluster moeten worden geïnstalleerd waarmee de taak wordt uitgevoerd. Dit kan een matrix zijn met \<teken reeks, object >.|Nee|


## <a name="supported-libraries-for-databricks-activities"></a>Ondersteunde bibliotheken voor Databricks-activiteiten

In de bovenstaande definitie van de Databricks-activiteit geeft u deze typen tape wisselaars op: *jar*, *ei*, *WHL*, *maven*, *pypi*, *krans*.

```json
{
    "libraries": [
        {
            "jar": "dbfs:/mnt/libraries/library.jar"
        },
        {
            "egg": "dbfs:/mnt/libraries/library.egg"
        },
    {
            "whl": "dbfs:/mnt/libraries/mlflow-0.0.1.dev0-py2-none-any.whl"
        },
        {
            "whl": "dbfs:/mnt/libraries/wheel-libraries.wheelhouse.zip"
        },
        {
            "maven": {
                "coordinates": "org.jsoup:jsoup:1.7.2",
                "exclusions": [ "slf4j:slf4j" ]
            }
        },
        {
            "pypi": {
                "package": "simplejson",
                "repo": "http://my-pypi-mirror.com"
            }
        },
        {
            "cran": {
                "package": "ada",
                "repo": "https://cran.us.r-project.org"
            }
        }
    ]
}

```

Zie de [Databricks-documentatie](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary) voor bibliotheek typen voor meer informatie.

## <a name="passing-parameters-between-notebooks-and-data-factory"></a>Para meters door geven tussen notitie blokken en Data Factory

U kunt data factory-para meters door geven aan notebooks met behulp van de eigenschap *baseParameters* in databricks-activiteit. 

In bepaalde gevallen kan het nodig zijn om bepaalde waarden van het notitie blok terug te geven aan data factory, dat kan worden gebruikt voor controle stroom (voorwaardelijke controles) in data factory of worden verbruikt door downstream-activiteiten (grootte limiet is 2 MB). 

1. In uw notitie blok kunt u [dbutils. notebook. Exit ("returnValue")](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-workflows.html#notebook-workflows-exit) aanroepen en de bijbehorende ' returnValue ' wordt geretourneerd aan Data Factory.

2. U kunt de uitvoer in data factory gebruiken door gebruik te maken van een expressie zoals `'@activity('databricks notebook activity name').output.runOutput'`. 

   > [!IMPORTANT]
   > Als u een JSON-object doorgeeft, kunt u waarden ophalen door de namen van eigenschappen toe te voegen. Voorbeeld: `'@activity('databricks notebook activity name').output.runOutput.PropertyName'`

## <a name="how-to-upload-a-library-in-databricks"></a>Een bibliotheek uploaden in Databricks

#### <a name="using-databricks-workspace-uihttpsdocsazuredatabricksnetuser-guidelibrarieshtmlcreate-a-library"></a>[De gebruikers interface van Databricks werk ruimte gebruiken](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

U kunt de [DATABRICKS cli (installatie)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli)gebruiken om het dbfs-pad op te halen van de bibliotheek die wordt toegevoegd met behulp van de gebruikers interface. 

De jar-bibliotheken worden normaal gesp roken opgeslagen onder dbfs:/File Store/potten tijdens het gebruik van de gebruikers interface. U kunt alle weer geven via de CLI: *databricks FS ls dbfs:/File Store/potten*.



#### <a name="copy-library-using-databricks-clihttpsdocsazuredatabricksnetuser-guidedev-toolsdatabricks-clihtmlcopy-a-file-to-dbfs"></a>[Bibliotheek kopiëren met behulp van Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

Voor beeld: *databricks FS CP sparkpi-assembly-0.1. jar dbfs:/File Store/potten*
