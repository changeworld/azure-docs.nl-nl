---
title: Apache Hive gebruiken met Power shell in HDInsight-Azure
description: Power shell gebruiken voor het uitvoeren van Apache Hive query's in Apache Hadoop in azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/24/2019
ms.openlocfilehash: deaa934b257fab74830d75e308a283e7608dc590
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552590"
---
# <a name="run-apache-hive-queries-using-powershell"></a>Apache Hive query's uitvoeren met Power shell

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Dit document bevat een voor beeld van het gebruik van Azure PowerShell om Apache Hive query's uit te voeren in een Apache Hadoop in HDInsight-cluster.

> [!NOTE]  
> Dit document biedt geen gedetailleerde beschrijving van de HiveQL-instructies die worden gebruikt in de voor beelden. Zie [Apache Hive gebruiken met Apache Hadoop op HDInsight](hdinsight-use-hive.md)voor meer informatie over de HiveQL die in dit voor beeld wordt gebruikt.

## <a name="prerequisites"></a>Vereisten

* Een Apache Hadoop cluster in HDInsight. Zie aan de [slag met HDInsight op Linux](./apache-hadoop-linux-tutorial-get-started.md).

* De Power shell [AZ-module](https://docs.microsoft.com/powershell/azure/overview) is geïnstalleerd.

## <a name="run-a-hive-query"></a>Een Hive-query uitvoeren

Azure PowerShell biedt *cmdlets* waarmee u Hive-Query's op HDInsight op afstand kunt uitvoeren. Intern maken de cmdlets REST-aanroepen naar [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) op het HDInsight-cluster.

De volgende cmdlets worden gebruikt bij het uitvoeren van Hive-query's in een extern HDInsight-cluster:

* `Connect-AzAccount`: verifieert Azure PowerShell voor uw Azure-abonnement.
* `New-AzHDInsightHiveJobDefinition`: Hiermee wordt een *taak definitie* gemaakt met behulp van de opgegeven HiveQL-instructies.
* `Start-AzHDInsightJob`: de taak definitie wordt naar HDInsight verzonden en de taak wordt gestart. Er wordt een *taak* object geretourneerd.
* `Wait-AzHDInsightJob`: gebruikt het taak object om de status van de taak te controleren. Er wordt gewacht tot de taak is voltooid of de wacht tijd is overschreden.
* `Get-AzHDInsightJobOutput`: wordt gebruikt om de uitvoer van de taak op te halen.
* `Invoke-AzHDInsightHiveJob`: wordt gebruikt om HiveQL-instructies uit te voeren. Deze cmdlet blokkeert de query en retourneert vervolgens de resultaten.
* `Use-AzHDInsightCluster`: stelt het huidige cluster in dat moet worden gebruikt voor de `Invoke-AzHDInsightHiveJob` opdracht.

De volgende stappen laten zien hoe u deze cmdlets kunt gebruiken om een taak uit te voeren in uw HDInsight-cluster:

1. Gebruik een editor om de volgende code op te slaan als `hivejob.ps1`.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=5-42)]

2. Open een nieuwe **Azure PowerShell** opdracht prompt. Wijzig de mappen in de locatie van het `hivejob.ps1` bestand en gebruik vervolgens de volgende opdracht om het script uit te voeren:

        .\hivejob.ps1

    Wanneer het script wordt uitgevoerd, wordt u gevraagd om de cluster naam en de referenties van het HTTPS/Cluster-beheer account in te voeren. U wordt mogelijk ook gevraagd om u aan te melden bij uw Azure-abonnement.

3. Wanneer de taak is voltooid, wordt er informatie weer gegeven die vergelijkbaar is met de volgende tekst:

        Display the standard output...
        2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
        2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
        2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id

4. Zoals eerder vermeld, kunt u `Invoke-Hive` gebruiken om een query uit te voeren en te wachten op de reactie. Gebruik het volgende script om te zien hoe invoke-Hive werkt:

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=50-71)]

    De uitvoer ziet er als volgt uit:

        2012-02-03    18:35:34    SampleClass0    [ERROR]    incorrect    id
        2012-02-03    18:55:54    SampleClass1    [ERROR]    incorrect    id
        2012-02-03    19:25:27    SampleClass4    [ERROR]    incorrect    id

   > [!NOTE]  
   > Voor langere HiveQL query's kunt u de script bestanden Azure PowerShell **hier-strings** of HiveQL gebruiken. Het volgende code fragment laat zien hoe u de `Invoke-Hive` cmdlet kunt gebruiken om een HiveQL-script bestand uit te voeren. Het HiveQL-script bestand moet worden geüpload naar wasbs://.
   >
   > `Invoke-AzHDInsightHiveJob -File "wasbs://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
   >
   > Zie <a href="https://technet.microsoft.com/library/ee692792.aspx" target="_blank">hier Windows Power shell gebruiken-teken reeksen</a>voor meer informatie over **hier-teken reeksen**.

## <a name="troubleshooting"></a>Problemen oplossen

Als er geen informatie wordt geretourneerd wanneer de taak is voltooid, raadpleegt u de fouten Logboeken. Als u de fout gegevens voor deze taak wilt weer geven, voegt u het volgende toe aan het einde van het `hivejob.ps1` bestand, slaat u het op en voert u het vervolgens opnieuw uit.

```powershell
# Print the output of the Hive job.
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Met deze cmdlet wordt de informatie geretourneerd die wordt geschreven naar STDERR tijdens taak verwerking.

## <a name="summary"></a>Samenvatting

Zoals u kunt zien, biedt Azure PowerShell een eenvoudige manier om Hive-query's uit te voeren in een HDInsight-cluster, de taak status te controleren en de uitvoer op te halen.

## <a name="next-steps"></a>Volgende stappen

Voor algemene informatie over Hive in HDInsight:

* [Apache Hive gebruiken met Apache Hadoop op HDInsight](hdinsight-use-hive.md)

Voor informatie over andere manieren om met Hadoop in HDInsight te werken:

* [MapReduce gebruiken met Apache Hadoop op HDInsight](hdinsight-use-mapreduce.md)
