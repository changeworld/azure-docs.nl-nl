---
title: Problemen met Apache Spark in azure HDInsight oplossen
description: Vind antwoorden op veelgestelde vragen over het werken met Apache Spark en Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: troubleshooting
ms.date: 08/22/2019
ms.custom: seodec18
ms.openlocfilehash: 80bca2dab1d07d9b99e75e283068bff99335fa18
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79271939"
---
# <a name="troubleshoot-apache-spark-by-using-azure-hdinsight"></a>Apache Spark oplossen met behulp van Azure HDInsight

Meer informatie over de belangrijkste problemen en hun oplossingen bij het werken met Apache Spark Payloads in [Apache Ambari](https://ambari.apache.org/).

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters"></a>Hoe configureer ik een Apache Spark-toepassing met behulp van Apache Ambari op clusters?

Spark-configuratie waarden kunnen worden afgestemd om te voor komen dat een Apache Spark toepassing `OutofMemoryError` uitzonde ring. In de volgende stappen worden standaard waarden voor Spark-configuratie in azure HDInsight weer gegeven:

1. Meld u bij `https://CLUSTERNAME.azurehdidnsight.net` aan bij Ambari met uw cluster referenties. In het eerste scherm wordt een overzichts dashboard weer gegeven. Er zijn geringe cosmetische verschillen tussen HDInsight 3,6 en 4,0.

1. Navigeer naar **Spark2** - > **configs**.

    ![Selecteer het tabblad configuraties](./media/apache-troubleshoot-spark/apache-spark-ambari-config2.png)

1. Selecteer in de lijst met configuraties de **Opties Custom-spark2-defaults**.

1. Zoek naar de waarde-instelling die u moet aanpassen, zoals Spark. uitvoerder **. geheugen**. In dit geval is de waarde van **9728m** te hoog.

    ![Selecteer de aangepaste-spark-standaardinstellingen](./media/apache-troubleshoot-spark/apache-spark-ambari-config4.png)

1. Stel de waarde in op de aanbevolen instelling. De waarde **2048m** wordt aanbevolen voor deze instelling.

1. Sla de waarde en sla vervolgens de configuratie. Selecteer **Opslaan**.

    ![Waarde wijzigen naar 2048 min.](./media/apache-troubleshoot-spark/apache-spark-ambari-config6a.png)

    Schrijf een opmerking over de configuratie wijzigingen en selecteer vervolgens **Opslaan**.

    ![Voer een opmerking over de wijzigingen die u hebt gemaakt](./media/apache-troubleshoot-spark/apache-spark-ambari-config6c.png)

    U wordt gewaarschuwd als de configuraties vereisen aandacht. Noteer de items en selecteer vervolgens **door gaan**.

    ![Selecteer Doorgaan toch](./media/apache-troubleshoot-spark/apache-spark-ambari-config6b.png)

1. Wanneer een configuratie is opgeslagen, wordt u gevraagd de service opnieuw te starten. Selecteer **opnieuw opstarten**.

    ![Opnieuw opstarten selecteren](./media/apache-troubleshoot-spark/apache-spark-ambari-config7a.png)

    Controleer of het opnieuw opstarten.

    ![Bevestigen selecteren Alles opnieuw starten](./media/apache-troubleshoot-spark/apache-spark-ambari-config7b.png)

    De processen die worden uitgevoerd, kunt u bekijken.

    ![Actieve processen controleren](./media/apache-troubleshoot-spark/apache-spark-ambari-config7c.png)

1. U kunt configuraties toevoegen. Selecteer in de lijst met configuraties de **Opties aangepast-spark2-standaard**en selecteer vervolgens **eigenschap toevoegen**.

    ![Selecteer toevoegen de eigenschap](./media/apache-troubleshoot-spark/apache-spark-ambari-config8.png)

1. Een nieuwe eigenschap definiëren. U kunt één eigenschap definiëren met behulp van een dialoogvenster voor specifieke instellingen, zoals het gegevenstype. Of u kunt meerdere eigenschappen definiëren met behulp van één definitie per regel.

    In dit voor beeld is de eigenschap **Spark. drivers. Memory** gedefinieerd met de waarde **4G**.

    ![Nieuwe eigenschap definiëren](./media/apache-troubleshoot-spark/apache-spark-ambari-config9.png)

1. De configuratie op te slaan en start de service opnieuw zoals beschreven in stap 6 en 7.

Deze wijzigingen zijn brede, door het cluster, maar kunnen worden overschreven wanneer u de Spark-taak verzendt.

## <a name="how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>Hoe configureer ik een Apache Spark-toepassing met behulp van een Jupyter-notebook op clusters?

In de eerste cel van de Jupyter-notebook, na de instructie **%% Configure** , geeft u de Spark-configuraties in een geldige JSON-indeling op. De werkelijke waarden zo nodig wijzigen:

![Een configuratie toevoegen](./media/apache-troubleshoot-spark/add-configuration-cell.png)

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters"></a>Hoe configureer ik een Apache Spark-toepassing met behulp van Apache Livy op clusters?

De Spark-toepassing naar Livy indienen met behulp van een REST-client, zoals cURL. Een vergelijkbaar met de volgende opdracht gebruiken. De werkelijke waarden zo nodig wijzigen:

```apache
curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
```

## <a name="how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters"></a>Hoe configureer ik een Apache Spark-toepassing met behulp van spark-submit op clusters?

Spark-shell met behulp van een vergelijkbaar met de volgende opdracht start. De werkelijke waarde van de configuraties zo nodig wijzigen:

```apache
spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
```

### <a name="additional-reading"></a>Meer lezen

[Verzen ding van taken Apache Spark op HDInsight-clusters](https://web.archive.org/web/20190112152841/https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* [Overzicht van Spark-geheugen beheer](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview).

* [Fout opsporing van Spark-toepassing op HDInsight-clusters](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/).

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) -het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring. Verbinding maken met de Azure-community met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees [hoe u een ondersteunings aanvraag voor Azure kunt maken](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)voor meer informatie. De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).
