---
title: "Zelf studie: gegevens laden & query's uitvoeren met Apache Spark-Azure HDInsight"
description: "Zelf studie: informatie over het laden van gegevens en het uitvoeren van interactieve query's op Spark-clusters in azure HDInsight."
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc
ms.date: 02/12/2020
ms.openlocfilehash: 5eb6788a558e4429296731f1693edd18bf92f98f
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198885"
---
# <a name="tutorial-load-data-and-run-queries-on-an-apache-spark-cluster-in-azure-hdinsight"></a>Zelfstudie: Gegevens laden en query's uitvoeren in een Apache Spark-cluster in Azure HDInsight

In deze zelfstudie leert u hoe u een gegevensframe maakt van een CSV-bestand en hoe u interactieve Spark SQL-query's uitvoert op een [Apache Spark](https://spark.apache.org/)-cluster in Azure HDInsight. In Spark is een gegevensframe een gedistribueerde verzameling gegevens die zijn geordend in benoemde kolommen. Er ligt hetzelfde concept aan ten grondslag als aan een tabel in een relationele database of aan een gegevensframe in R/Python.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Een gegevensframe maken van een CSV-bestand
> * Query's uitvoeren op het gegevensframe

## <a name="prerequisites"></a>Vereisten

Een Apache Spark-cluster in HDInsight. Zie [een Apache Spark-cluster maken](./apache-spark-jupyter-spark-sql-use-portal.md).

## <a name="create-a-jupyter-notebook"></a>Een Jupyter-notebook maken

Jupyter Notebook is een interactieve notitieblokomgeving die ondersteuning biedt voor verschillende programmeertalen. Via het notitieblok kunt u interactie hebben met uw gegevens, code combineren met markdown-tekst en eenvoudige visualisaties uitvoeren.

1. Bewerk de URL `https://SPARKCLUSTER.azurehdinsight.net/jupyter` door `SPARKCLUSTER` te vervangen door de naam van uw Spark-cluster. Voer vervolgens de bewerkte URL in een webbrowser in. Voer de aanmeldingsreferenties voor het cluster in als u daarom wordt gevraagd.

2. Selecteer op de webpagina Jupyter **nieuwe** > **PySpark** om een notitie blok te maken.

   ![Een Jupyter Notebook maken om de interactieve Spark SQL-query uit te voeren](./media/apache-spark-load-data-run-query/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Een Jupyter Notebook maken om de interactieve Spark SQL-query uit te voeren")

   Er wordt een nieuw notitie blok gemaakt en geopend met de naam Naamloos (`Untitled.ipynb`).

    > [!NOTE]  
    > Door de PySpark-kernel te gebruiken voor het maken van een notebook, wordt de `spark`-sessie automatisch voor u gemaakt wanneer u de eerste cel met code uitvoert. U hoeft de sessie dus niet expliciet te maken.

## <a name="create-a-dataframe-from-a-csv-file"></a>Een gegevensframe maken van een CSV-bestand

Toepassingen kunnen dataframes rechtstreeks maken op basis van bestanden of mappen in de externe opslag, zoals Azure Storage of Azure Data Lake Storage; uit een Hive-tabel; of van andere gegevens bronnen die door Spark worden ondersteund, zoals Cosmos DB, Azure SQL DB, DW, enzovoort. In de volgende schermafbeelding ziet u een momentopname van het bestand HVAC.csv dat wordt gebruikt in deze zelfstudie. Het CSV-bestand wordt geleverd met alle HDInsight Spark-clusters. De gegevens hebben betrekking op de schommelingen in temperatuur van sommige gebouwen.

![Moment opname van gegevens voor de interactieve Spark SQL-query](./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "Moment opname van gegevens voor de interactieve Spark SQL-query")

1. Plak de volgende code in een lege cel van het Jupyter-notitie blok en druk op **SHIFT + ENTER** om de code uit te voeren. Met de code importeert u de typen die voor dit scenario zijn vereist:

    ```python
    from pyspark.sql import *
    from pyspark.sql.types import *
    ```

    Wanneer u een interactieve query uitvoert in Jupyter, ziet u in het venster van de webbrowser of de titelbalk van het tabblad de status **(Busy)** , samen met de titel van het notebook. Ook ziet u een gevulde cirkel naast de **PySpark**-tekst in de rechterbovenhoek. Wanneer de taak is voltooid, verandert deze in een lege cirkel.

    ![Status van interactieve Spark SQL-query](./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "Status van interactieve Spark SQL-query")

1. Noteer de sessie-id die wordt geretourneerd. In de bovenstaande afbeelding is de sessie-id 0. Desgewenst kunt u de sessie Details ophalen door te navigeren naar `https://CLUSTERNAME.azurehdinsight.net/livy/sessions/ID/statements` waarbij CLUSTERNAME de naam van uw Spark-cluster is en ID uw sessie-id-nummer is.

1. Voer de volgende code uit om een gegevensframe en een tijdelijke tabel (**hvac**) te maken.

    ```python
    # Create a dataframe and table from sample data
    csvFile = spark.read.csv('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv', header=True, inferSchema=True)
    csvFile.write.saveAsTable("hvac")
    ```

## <a name="run-queries-on-the-dataframe"></a>Query's uitvoeren op het gegevensframe

Zodra de tabel is gemaakt, kunt u een interactieve query uitvoeren op de gegevens.

1. Voer de volgende code uit in een lege cel van de notebook:

    ```sql
    %%sql
    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```

   De uitvoer wordt weergegeven in een tabel.

     ![Tabel uitvoer van resultaat van interactieve Spark-query](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "Tabel uitvoer van resultaat van interactieve Spark-query")

2. U kunt de resultaten ook in andere visualisaties bekijken. Als u een vlakdiagram wilt zien voor dezelfde uitvoer, selecteert u **Area** en stelt u de andere waarden in zoals deze worden weergegeven.

    ![Gebieds grafiek van resultaat van interactieve Spark-query](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "Gebieds grafiek van resultaat van interactieve Spark-query")

3. Navigeer in de menu balk van de notitie blok naar **bestand** > **opslaan en controle punt**.

4. Als u direct verdergaat met de [volgende zelfstudie](apache-spark-use-bi-tools.md), laat u het notebook open. Als dat niet het geval is, sluit u het notitie blok om de cluster bronnen vrij te geven: Navigeer in de menu balk van het notitie blok naar **bestand** >  **sluiten en stoppen**.

## <a name="clean-up-resources"></a>Resources opschonen

Met HDInsight worden uw gegevens-en Jupyter-notebooks opgeslagen in Azure Storage of Azure Data Lake Storage, zodat u een cluster veilig kunt verwijderen wanneer deze niet in gebruik is. U betaalt ook voor een HDInsight-cluster, zelfs wanneer het niet in gebruik is. Omdat de kosten voor het cluster veel keren meer zijn dan de kosten voor opslag, is het economisch zinvol om clusters te verwijderen wanneer ze niet worden gebruikt. Als u direct verder wilt met de zelfstudie, is het beter om het cluster te behouden.

Open het cluster in Azure Portal en selecteer **Verwijderen**.

![HDInsight-cluster verwijderen](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "HDInsight-cluster verwijderen")

U kunt ook de naam van de resourcegroep selecteren om de pagina van de resourcegroep te openen en vervolgens **Resourcegroep verwijderen** selecteren. Als u de resourcegroep verwijdert, verwijdert u zowel het HDInsight Spark-cluster als het standaardopslagaccount.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u een data frame maakt op basis van een CSV-bestand en hoe u interactieve Spark SQL-query's uitvoert op een Apache Spark cluster in azure HDInsight. Ga naar het volgende artikel om te zien hoe de gegevens die u hebt geregistreerd in Apache Spark kunnen worden overgebracht naar een BI-hulpprogramma voor analyse zoals Power BI.

> [!div class="nextstepaction"]
> [Gegevens analyseren met BI-tools](apache-spark-use-bi-tools.md)
