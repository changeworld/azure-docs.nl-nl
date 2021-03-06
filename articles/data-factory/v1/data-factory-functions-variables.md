---
title: Data Factory functies en systeem variabelen
description: Geeft een lijst van Azure Data Factory functies en systeem variabelen
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 9acc369e24d1bac92dea3fb6ae391a410e5f6c3d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73667653"
---
# <a name="azure-data-factory---functions-and-system-variables"></a>Azure Data Factory-functies en systeem variabelen
> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [systeem variabelen in Data Factory](../control-flow-system-variables.md).

Dit artikel bevat informatie over functies en variabelen die door Azure Data Factory worden ondersteund.

## <a name="data-factory-system-variables"></a>Data Factory systeem variabelen

| Naam variabele | Beschrijving | Object bereik | JSON-bereik en use cases |
| --- | --- | --- | --- |
| WindowStart |Begin van tijds interval voor het venster huidige activiteit uitvoeren |faxgebeurtenissen |<ol><li>Geef query's voor gegevens selectie op. Zie connector artikelen waarnaar wordt verwezen in het artikel [activiteiten voor gegevens verplaatsing](data-factory-data-movement-activities.md) .</li> |
| WindowEnd |Einde van het tijds interval voor het venster huidige activiteit uitvoeren |faxgebeurtenissen |hetzelfde als WindowStart. |
| Slice start |Begin van het tijds interval voor het gegevens segment dat wordt geproduceerd |faxgebeurtenissen<br/>Sets |<ol><li>Paden en bestands namen voor dynamische mappen opgeven tijdens het werken met [Azure Blob](data-factory-azure-blob-connector.md) en [File System-gegevens sets](data-factory-onprem-file-system-connector.md).</li><li>Geef invoer afhankelijkheden op met data factory functies in de verzameling activiteits ingangen.</li></ol> |
| SliceEnd |Het einde van het tijds interval voor het huidige gegevens segment. |faxgebeurtenissen<br/>Sets |hetzelfde als slice start. |

> [!NOTE]
> Het is momenteel data factory vereist dat het schema dat is opgegeven in de activiteit exact overeenkomt met het schema dat is opgegeven in de beschik baarheid van de uitvoer gegevensset. Daarom worden WindowStart, WindowEnd en slice start en SliceEnd altijd toegewezen aan dezelfde tijds periode en één uitvoer segment.
> 

### <a name="example-for-using-a-system-variable"></a>Voor beeld van het gebruik van een systeem variabele
In het volgende voor beeld worden jaar, maand, dag en tijd van **slice start** geëxtraheerd in afzonderlijke variabelen die worden gebruikt door **FolderPath** en **filename** -eigenschappen.

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```

## <a name="data-factory-functions"></a>Data Factory functies
U kunt functies in data factory samen met systeem variabelen gebruiken voor de volgende doel einden:

1. Het opgeven van query's voor gegevens selectie (Zie connector artikelen waarnaar wordt verwezen door het artikel [activiteiten voor gegevens verplaatsing](data-factory-data-movement-activities.md) .
   
   De syntaxis voor het aanroepen van een data factory functie is: **$$\<functie >** voor gegevens selectie query's en andere eigenschappen in de activiteit en gegevens sets.  
2. Het opgeven van invoer afhankelijkheden met data factory functies in de verzameling activiteiten ingangen.
   
    $ $ is niet nodig voor het opgeven van invoer afhankelijke expressies.     

In het volgende voor beeld wordt de eigenschap **sqlReaderQuery** in een JSON-bestand toegewezen aan een waarde die wordt geretourneerd door de functie `Text.Format`. In dit voor beeld wordt ook een systeem variabele met de naam **WindowStart**gebruikt. Dit is de start tijd van het venster voor het uitvoeren van de activiteit.

```json
{
    "Type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
}
```

Zie het onderwerp [aangepaste datum-en tijd notatie reeksen](https://msdn.microsoft.com/library/8kb3ddd4.aspx) waarin de verschillende opmaak opties worden beschreven die u kunt gebruiken (bijvoorbeeld: ay vs. jjjj). 

### <a name="functions"></a>Functions
De volgende tabellen geven een lijst van alle functies in Azure Data Factory:

| Category | Functie | Parameters | Beschrijving |
| --- | --- | --- | --- |
| Time |AddHours (X, Y) |X: DateTime <br/><br/>Y: int |Voegt Y-uren toe aan de opgegeven tijd X. <br/><br/>Voorbeeld: `9/5/2013 12:00:00 PM + 2 hours = 9/5/2013 2:00:00 PM` |
| Time |AddMinutes (X, Y) |X: DateTime <br/><br/>Y: int |Voegt Y minuten toe aan X.<br/><br/>Voorbeeld: `9/15/2013 12: 00:00 PM + 15 minutes = 9/15/2013 12: 15:00 PM` |
| Time |StartOfHour (X) |X: datetime |Hiermee wordt de begin tijd voor het uur opgehaald dat wordt vertegenwoordigd door de uur component van X. <br/><br/>Voorbeeld: `StartOfHour of 9/15/2013 05: 10:23 PM is 9/15/2013 05: 00:00 PM` |
| Date |AddDays (X, Y) |X: DateTime<br/><br/>Y: int |Voegt Y-dagen toe aan X. <br/><br/>Voor beeld: 9/15/2013 12:00:00 uur + 2 dagen = 9/17/2013 12:00:00 PM.<br/><br/>U kunt de dagen ook aftrekken door Y als een negatief getal op te geven.<br/><br/>Voor beeld: `9/15/2013 12:00:00 PM - 2 days = 9/13/2013 12:00:00 PM`. |
| Date |AddMonths (X, Y) |X: DateTime<br/><br/>Y: int |Voegt Y-maanden toe aan X.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 month = 10/15/2013 12:00:00 PM`.<br/><br/>U kunt ook maanden aftrekken door Y als een negatief getal op te geven.<br/><br/>Voor beeld: `9/15/2013 12:00:00 PM - 1 month = 8/15/2013 12:00:00 PM`.|
| Date |AddQuarters (X, Y) |X: DateTime <br/><br/>Y: int |Voegt Y * 3 maanden toe aan X.<br/><br/>Voorbeeld: `9/15/2013 12:00:00 PM + 1 quarter = 12/15/2013 12:00:00 PM` |
| Date |AddWeeks (X, Y) |X: DateTime<br/><br/>Y: int |Voegt Y * 7 dagen toe aan X<br/><br/>Voor beeld: 9/15/2013 12:00:00 uur + 1 week = 9/22/2013 12:00:00 uur<br/><br/>U kunt ook weken aftrekken door Y als een negatief getal op te geven.<br/><br/>Voor beeld: `9/15/2013 12:00:00 PM - 1 week = 9/7/2013 12:00:00 PM`. |
| Date |AddYears (X, Y) |X: DateTime<br/><br/>Y: int |Voegt Y-jaren toe aan X.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 year = 9/15/2014 12:00:00 PM`<br/><br/>U kunt ook jaren aftrekken door Y als een negatief getal op te geven.<br/><br/>Voor beeld: `9/15/2013 12:00:00 PM - 1 year = 9/15/2012 12:00:00 PM`. |
| Date |Dag (X) |X: DateTime |Hiermee wordt het dag gedeelte van X opgehaald.<br/><br/>Voor beeld: `Day of 9/15/2013 12:00:00 PM is 9`. |
| Date |DayOfWeek (X) |X: DateTime |Hiermee wordt het onderdeel dag van de week van X opgehaald.<br/><br/>Voor beeld: `DayOfWeek of 9/15/2013 12:00:00 PM is Sunday`. |
| Date |DayOfYear (X) |X: DateTime |Hiermee wordt de dag in het jaar opgehaald die wordt vertegenwoordigd door het jaar gedeelte van X.<br/><br/>Voorbeelden:<br/>`12/1/2015: day 335 of 2015`<br/>`12/31/2015: day 365 of 2015`<br/>`12/31/2016: day 366 of 2016 (Leap Year)` |
| Date |DaysInMonth (X) |X: DateTime |Hiermee worden de dagen in de maand opgehaald die worden vertegenwoordigd door het onderdeel month van para meter X.<br/><br/>Voor beeld: `DaysInMonth of 9/15/2013 are 30 since there are 30 days in the September month`. |
| Date |EndOfDay (X) |X: DateTime |Hiermee wordt de datum/tijd opgehaald die het einde van de dag vertegenwoordigt (dag van het onderdeel) van X.<br/><br/>Voor beeld: `EndOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 11:59:59 PM`. |
| Date |EndOfMonth (X) |X: DateTime |Hiermee wordt het einde van de maand opgehaald die wordt vertegenwoordigd door het onderdeel maand van para meter X. <br/><br/>Voor beeld: `EndOfMonth of 9/15/2013 05:10:23 PM is 9/30/2013 11:59:59 PM` (datum/tijd die het einde van de maand september aangeeft) |
| Date |StartOfDay (X) |X: DateTime |Hiermee wordt het begin van de dag opgehaald die wordt vertegenwoordigd door het onderdeel dag van para meter X.<br/><br/>Voor beeld: `StartOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 12:00:00 AM`. |
| DateTime |Van (X) |X: teken reeks |Teken reeks X naar een datum tijd parseren. |
| DateTime |Maten (X) |X: DateTime |Hiermee wordt de eigenschap Ticks van de para meter X opgehaald. Eén streepje is gelijk aan 100 nano seconden. De waarde van deze eigenschap vertegenwoordigt het aantal maat streepjes dat sinds 12:00:00 middernacht, 1 januari 0001 is verstreken. |
| Tekst |Indeling (X) |X: teken reeks variabele |Hiermee wordt de tekst opgemaakt (`\\'` combi natie gebruiken om `'` teken te escapen).|

> [!IMPORTANT]
> Wanneer u een functie binnen een andere functie gebruikt, hoeft u geen **$$** voor voegsel voor de binnenste functie te gebruiken. Bijvoorbeeld: $ $Text. Format (' PartitionKey EQ \\' my_pkey_filter_value\\' en RowKey ge \\' {0: JJJJ-MM-DD uu: mm: SS}\\' ', time. AddHours (slice start,-6)). In dit voor beeld ziet u dat **$$** voor voegsel niet wordt gebruikt voor de functie **time. AddHours** . 

#### <a name="example"></a>Voorbeeld
In het volgende voor beeld worden de para meters voor invoer en uitvoer voor de Hive-activiteit bepaald met behulp van de `Text.Format`-functie en de systeem variabele slice start. 

```json  
{
    "name": "HiveActivitySamplePipeline",
        "properties": {
    "activities": [
            {
            "name": "HiveActivitySample",
            "type": "HDInsightHive",
            "inputs": [
                    {
                    "name": "HiveSampleIn"
                    }
            ],
            "outputs": [
                    {
                    "name": "HiveSampleOut"
                }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
                    "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                    "scriptLinkedService": "StorageLinkedService",
                    "defines": {
                        "Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)",
                        "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                }
            }
            }
    ]
    }
}
```

### <a name="example-2"></a>Voor beeld 2

In het volgende voor beeld wordt de DateTime-para meter voor de opgeslagen procedure activiteit bepaald met behulp van de tekst. De functie Format en de variabele slice start. 

```json
{
    "name": "SprocActivitySamplePipeline",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "usp_sample",
                    "storedProcedureParameters": {
                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                    }
                },
                "outputs": [
                    {
                        "name": "sprocsampleout"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SprocActivitySample"
            }
        ],
            "start": "2016-08-02T00:00:00Z",
            "end": "2016-08-02T05:00:00Z",
        "isPaused": false
    }
}
```

### <a name="example-3"></a>Voor beeld 3
Als u gegevens wilt lezen van de vorige dag in plaats van de dag die wordt vertegenwoordigd door de slice start, gebruikt u de functie AddDays zoals weer gegeven in het volgende voor beeld: 

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-01-01T08:00:00",
        "end": "2017-01-01T11:00:00",
        "description": "hive activity",
        "activities": [
            {
                "name": "SampleHiveActivity",
                "inputs": [
                    {
                        "name": "MyAzureBlobInput",
                        "startTime": "Date.AddDays(SliceStart, -1)",
                        "endTime": "Date.AddDays(SliceEnd, -1)"
                    }
                ],
                "outputs": [
                    {
                        "name": "MyAzureBlobOutput"
                    }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adftutorial\\hivequery.hql",
                    "scriptLinkedService": "StorageLinkedService",
                    "defines": {
                        "Year": "$$Text.Format('{0:yyyy}',WindowsStart)",
                        "Month": "$$Text.Format('{0:MM}',WindowStart)",
                        "Day": "$$Text.Format('{0:dd}',WindowStart)"
                    }
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 2,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

Zie het onderwerp [aangepaste datum-en tijd notatie reeksen](https://msdn.microsoft.com/library/8kb3ddd4.aspx) waarin de verschillende opmaak opties worden beschreven die u kunt gebruiken (bijvoorbeeld: JJ versus. jjjj). 

