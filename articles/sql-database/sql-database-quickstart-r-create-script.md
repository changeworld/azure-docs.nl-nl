---
title: Eenvoudige R-scripts maken en uitvoeren
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Voer eenvoudige R-scripts uit in Azure SQL Database Machine Learning Services (preview).
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ms.openlocfilehash: 5b2f8231952d25f5858f8e06a957f1056ecc3651
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768493"
---
# <a name="quickstart-create-and-run-simple-r-scripts-in-azure-sql-database-machine-learning-services-preview"></a>Snelstartgids: eenvoudige R-scripts maken en uitvoeren in Azure SQL Database Machine Learning Services (preview-versie)

In deze Quick Start maakt en voert u een set van R-scripts met behulp van Machine Learning Services (met R) in Azure SQL Database.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Een [Azure-SQL database](sql-database-single-database-get-started.md) met een [firewall regel op server niveau](sql-database-server-level-firewall-rule.md)
- [Machine Learning Services](sql-database-machine-learning-services-overview.md) met R ingeschakeld. [Meld u aan voor de preview-versie](sql-database-machine-learning-services-overview.md#signup).
- [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS)

> [!NOTE]
> Na de onboarding voor de openbare preview wordt Machine Learning voor u ingeschakeld voor uw bestaande of nieuwe database.

In dit voor beeld wordt de opgeslagen procedure [sp_execute_external_script](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) om een goed gevormd R-script op te slaan.

## <a name="run-a-simple-script"></a>Een eenvoudig script uitvoeren

Als u een R-script wilt uitvoeren, geeft u dit als een argument door aan de opgeslagen procedure van het systeem [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).

In de volgende stappen voert u dit voorbeeld R-script uit in uw SQL database:

```r
a <- 1
b <- 2
c <- a/b
d <- a*b
print(c(c, d))
```

1. Open **SQL Server Management Studio** en maak verbinding met de SQL-database.

   Als u hulp nodig hebt bij het verbinding maken, raadpleegt u [Quick Start: SQL Server Management Studio gebruiken om verbinding te maken met een Azure-SQL database](sql-database-connect-query-ssms.md).

1. Geef het volledige R-script door aan de opgeslagen [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) -procedure.

   Het script wordt door gegeven via het argument `@script`. Alles binnen het `@script` argument moet een geldige R-code zijn.

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    a <- 1
    b <- 2
    c <- a/b
    d <- a*b
    print(c(c, d))
    '
    ```

   Als er fouten optreden, kan dit zijn omdat de openbare preview van Machine Learning Services (met R) niet is ingeschakeld voor uw SQL-database. Zie bovenstaande [vereisten](#prerequisites) .

   > [!NOTE]
   > Als u een beheerder bent, kunt u externe code automatisch uitvoeren. U kunt machtigingen verlenen aan andere gebruikers met behulp van de opdracht:
   <br>**Ken een extern script toe aan** *\<gebruikers naam\>* .

2. Het juiste resultaat wordt berekend en de R-`print` functie retourneert het resultaat in het venster **berichten** .

   Dit ziet er ongeveer als volgt uit.

    **Results**

    ```text
    STDOUT message(s) from external script:
    0.5 2
    ```

## <a name="run-a-hello-world-script"></a>Een Hallo wereld script uitvoeren

Een typisch voor beeld is een script dat alleen de teken reeks ' Hallo wereld ' uitvoert. Voer de volgende opdracht uit.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'OutputDataSet<-InputDataSet'
    , @input_data_1 = N'SELECT 1 AS hello'
WITH RESULT SETS(([Hello World] INT));
GO
```

De invoer van deze opgeslagen procedure omvat:

| | |
|-|-|
| @language | Hiermee definieert u de taal extensie die moet worden aangeroepen, in dit geval R |
| @script | Hiermee worden de opdrachten gedefinieerd die worden door gegeven aan de R-runtime. Het hele R-script moet in dit argument worden inge sloten als Unicode-tekst. U kunt ook de tekst toevoegen aan een variabele van het type **nvarchar** en vervolgens de variabele aanroepen |
| @input_data_1 | gegevens die door de query zijn geretourneerd, worden door gegeven aan de R-runtime, waarmee de gegevens worden geretourneerd die moeten worden SQL Server als een gegevens frame |
|MET RESULTATEN SETS | -component definieert het schema van de geretourneerde gegevens tabel voor SQL Server en voegt "Hallo wereld" toe als kolom naam, **int** voor het gegevens type |

De opdracht voert de volgende tekst uit:

| Hallo wereld |
|-------------|
| 1 |

## <a name="use-inputs-and-outputs"></a>Invoer en uitvoer gebruiken

Standaard accepteert [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) één gegevensset als invoer, die doorgaans in de vorm van een geldige SQL-query wordt opgegeven. Vervolgens wordt er één R-gegevens frame als uitvoer geretourneerd.

We gebruiken nu de standaard invoer-en uitvoer variabelen van [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql): **input dataset** en **output dataset**.

1. Maak een kleine tabel met test gegevens.

    ```sql
    CREATE TABLE RTestData (col1 INT NOT NULL)
    
    INSERT INTO RTestData
    VALUES (1);
    
    INSERT INTO RTestData
    VALUES (10);
    
    INSERT INTO RTestData
    VALUES (100);
    GO
    ```

1. Gebruik de instructie `SELECT` om de tabel op te vragen.
  
    ```sql
    SELECT *
    FROM RTestData
    ```

    **Results**

    ![Inhoud van de RTestData-tabel](./media/sql-database-quickstart-r-create-script/select-rtestdata.png)

1. Voer het volgende R-script uit. De gegevens worden opgehaald uit de tabel met behulp van de instructie `SELECT`, door gegeven door de R runtime en de gegevens worden als een gegevens frame geretourneerd. De component `WITH RESULT SETS` definieert het schema van de geretourneerde gegevens tabel voor SQL Database, waarbij de kolom naam *NewColName*wordt toegevoegd.

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'OutputDataSet <- InputDataSet;'
        , @input_data_1 = N'SELECT * FROM RTestData;'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    **Results**

    ![Uitvoer van R-script waarmee gegevens uit een tabel worden geretourneerd](./media/sql-database-quickstart-r-create-script/r-output-rtestdata.png)

1. Nu gaan we de namen van de invoer-en uitvoer variabelen wijzigen. De standaard namen voor invoer en uitvoer variabelen zijn **input dataset** en **output dataset**, met dit script worden de namen gewijzigd in **SQL_in** en **SQL_out**:

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N' SQL_out <- SQL_in;'
        , @input_data_1 = N' SELECT 12 as Col;'
        , @input_data_1_name = N'SQL_in'
        , @output_data_1_name = N'SQL_out'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    Houd er rekening mee dat R hoofdletter gevoelig is. De invoer-en uitvoer variabelen in het R-script (**SQL_out**, **SQL_in**) moeten overeenkomen met de waarden die zijn gedefinieerd met `@input_data_1_name` en `@output_data_1_name`, inclusief het geval.

   > [!TIP]
   > Er kan maar één invoergegevensset worden doorgegeven als parameter, en u kunt slechts één gegevensset retourneren. U kunt vanuit de R-code wel andere gegevenssets aanroepen en u kunt, naast de gegevensset, ook andere typen uitvoer retourneren. U kunt ook het uitvoertrefwoord toevoegen aan elke willekeurige parameter om deze te laten retourneren met de resultaten.

1. U kunt ook alleen waarden genereren met behulp van het R-script zonder invoer gegevens (`@input_data_1` is ingesteld op leeg).

   Met het volgende script wordt de tekst "Hallo" en "wereld" uitgevoerd.

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    mytextvariable <- c("hello", " ", "world");
    OutputDataSet <- as.data.frame(mytextvariable);
    '
        , @input_data_1 = N''
    WITH RESULT SETS(([Col1] CHAR(20) NOT NULL));
    ```

    **Results**

    ![Resultaten doorzoeken met @script als invoer](./media/sql-database-quickstart-r-create-script/r-data-generated-output.png)

## <a name="check-r-version"></a>R-versie controleren

Als u wilt zien welke versie van R is geïnstalleerd in uw SQL database, voert u het volgende script uit.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'print(version)';
GO
```

Met de R-functie `print` wordt de versie geretourneerd in het **Berichtvenster**. In het onderstaande voor beeld ziet u dat SQL Database in dit geval R versie 3.4.4 is geïnstalleerd.

**Results**

```text
STDOUT message(s) from external script:
                   _
platform       x86_64-w64-mingw32
arch           x86_64
os             mingw32
system         x86_64, mingw32
status
major          3
minor          4.4
year           2018
month          03
day            15
svn rev        74408
language       R
version.string R version 3.4.4 (2018-03-15)
nickname       Someone to Lean On
```

## <a name="list-r-packages"></a>R-pakketten vermelden

Microsoft biedt een aantal R-pakketten waarin Machine Learning Services vooraf zijn geïnstalleerd in de SQL-database.

Voer het volgende script uit om een lijst weer te geven van welke R-pakketten zijn geïnstalleerd, inclusief versie, afhankelijkheden, licentie en bibliotheekpad.

```SQL
EXEC sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(installed.packages()[,c("Package", "Version", "Depends", "License", "LibPath")]);'
WITH result sets((
            Package NVARCHAR(255)
            , Version NVARCHAR(100)
            , Depends NVARCHAR(4000)
            , License NVARCHAR(1000)
            , LibPath NVARCHAR(2000)
            ));
```

De uitvoer is van `installed.packages()` in R en wordt geretourneerd als een resultaatset.

**Results**

![Geïnstalleerde pakketten in R](./media/sql-database-quickstart-r-create-script/r-installed-packages.png)

## <a name="next-steps"></a>Volgende stappen

Als u een machine learning model met behulp van R in SQL Database wilt maken, volgt u deze Snelstartgids:

> [!div class="nextstepaction"]
> [Een voorspellend model maken en trainen in R met Azure SQL Database Machine Learning Services (preview)](sql-database-quickstart-r-train-score-model.md)

Zie de volgende artikelen voor meer informatie over Azure SQL Database Machine Learning Services met R (preview).

- [Azure SQL Database Machine Learning Services met R (preview-versie)](sql-database-machine-learning-services-overview.md)
- [Geavanceerde R-functies schrijven in Azure SQL Database met behulp van Machine Learning Services (preview)](sql-database-machine-learning-services-functions.md)
- [Werken met R-en SQL-gegevens in Azure SQL Database Machine Learning Services (preview-versie)](sql-database-machine-learning-services-data-issues.md)
