---
title: Een fout in het geheugen van een Hive oplossen in azure HDInsight
description: Fout bij onvoldoende geheugen in HDInsight oplossen. Het scenario van de klant is een query in veel grote tabellen.
keywords: fout vanwege onvoldoende geheugen, OOM, Hive-instellingen
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: hdinsightactive
ms.date: 11/28/2019
ms.openlocfilehash: add55c29bb93d8dce9ad69bd9850a1db02ea5afe
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74687774"
---
# <a name="fix-an-apache-hive-out-of-memory-error-in-azure-hdinsight"></a>De fout Apache Hive onvoldoende geheugen in azure HDInsight oplossen

Meer informatie over het oplossen van een Apache Hive OOM-fout (out of Memory) bij het verwerken van grote tabellen door het configureren van Hive-geheugen instellingen.

## <a name="run-apache-hive-query-against-large-tables"></a>Apache Hive query uitvoeren op grote tabellen

Een klant heeft een Hive-query uitgevoerd:

```sql
SELECT
    COUNT (T1.COLUMN1) as DisplayColumn1,
    …
    …
    ….
FROM
    TABLE1 T1,
    TABLE2 T2,
    TABLE3 T3,
    TABLE5 T4,
    TABLE6 T5,
    TABLE7 T6
where (T1.KEY1 = T2.KEY1….
    …
    …
```

Enkele nuances van deze query:

* T1 is een alias voor een grote tabel, TABEL1, die veel van de kolom typen van de teken reeks bevat.
* Andere tabellen zijn niet groot, maar hebben wel veel kolommen.
* Alle tabellen worden samengevoegd met elkaar, in sommige gevallen met meerdere kolommen in TABEL1 en anderen.

Het volt ooien van de Hive-query duurde 26 minuten in een HDInsight-cluster van 24 knoop punt a3. De klant heeft de volgende waarschuwings berichten gemerkt:

    Warning: Map Join MAPJOIN[428][bigTable=?] in task 'Stage-21:MAPRED' is a cross product
    Warning: Shuffle Join JOIN[8][tables = [t1933775, t1932766]] in Stage 'Stage-4:MAPRED' is a cross product

Door gebruik te maken van de Apache TEZ Execution Engine. Dezelfde query is 15 minuten uitgevoerd en vervolgens de volgende fout gegenereerd:

    Status: Failed
    Vertex failed, vertexName=Map 5, vertexId=vertex_1443634917922_0008_1_05, diagnostics=[Task failed, taskId=task_1443634917922_0008_1_05_000006, diagnostics=[TaskAttempt 0 failed, info=[Error: Failure while running task:java.lang.RuntimeException: java.lang.OutOfMemoryError: Java heap space
        at
    org.apache.hadoop.hive.ql.exec.tez.TezProcessor.initializeAndRunProcessor(TezProcessor.java:172)
        at org.apache.hadoop.hive.ql.exec.tez.TezProcessor.run(TezProcessor.java:138)
        at
    org.apache.tez.runtime.LogicalIOProcessorRuntimeTask.run(LogicalIOProcessorRuntimeTask.java:324)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:176)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:168)
        at java.security.AccessController.doPrivileged(Native Method)
        at javax.security.auth.Subject.doAs(Subject.java:415)
        at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1628)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:168)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:163)
        at java.util.concurrent.FutureTask.run(FutureTask.java:262)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
        at java.lang.Thread.run(Thread.java:745)
    Caused by: java.lang.OutOfMemoryError: Java heap space

De fout blijft actief wanneer u een grotere virtuele machine gebruikt (bijvoorbeeld D12).

## <a name="debug-the-out-of-memory-error"></a>Fout bericht over onvoldoende geheugen

Onze ondersteunings-en technische teams hebben een van de problemen gevonden waardoor de fout onvoldoende geheugen wordt veroorzaakt. Dit is een [bekend probleem dat wordt beschreven in Apache Jira](https://issues.apache.org/jira/browse/HIVE-8306):

"When component. auto. Convert. Total. noconditionaltask = True Controleer noconditionaltask. size en als de som van de tabel grootten in de kaart koppeling kleiner is dan noconditionaltask. grootte van het plan genereert een toewijzings koppeling, het probleem met deze waarde is dat de berekening niet wordt uitgevoerd Als u rekening moet houden met de overhead die wordt geïntroduceerd door de verschillende implementatie van de hashtabel als resultaat als de som van de invoer groottes kleiner is dan de grootte van de noconditionaltask met een kleine marge, wordt OOM. "

De **component. auto. Convert. joinexpressie. noconditionaltask** in het bestand Hive-site. XML is ingesteld op **True**:

```xml
<property>
    <name>hive.auto.convert.join.noconditionaltask</name>
    <value>true</value>
    <description>
            Whether Hive enables the optimization about converting common join into mapjoin based on the input file size.
            If this parameter is on, and the sum of size for n-1 of the tables/partitions for a n-way join is smaller than the
            specified size, the join is directly converted to a mapjoin (there is no conditional task).
    </description>
</property>
```

Het is waarschijnlijk dat de toewijzing van de Java-heap onvoldoende geheugen is vanwege de fout melding. Zoals uitgelegd in de blog post [Hadoop-geheugen instellingen in HDInsight](https://blogs.msdn.com/b/shanyu/archive/2014/07/31/hadoop-yarn-memory-settings-in-hdinsigh.aspx), wanneer TEZ Execution Engine wordt gebruikt, wordt de gebruikte heap-ruimte van de TEZ-container. Bekijk de volgende afbeelding met een beschrijving van het geheugen van de TEZ-container.

![TEZ container-geheugen diagram: fout in geheugen van Hive](./media/hdinsight-hadoop-hive-out-of-memory-error-oom/hive-out-of-memory-error-oom-tez-container-memory.png)

Als het blog bericht wordt voorgesteld, definieert de volgende twee geheugen instellingen het container geheugen voor de heap: **Hive. TEZ. container. size** en **Hive. TEZ. java. kiest**. Vanuit onze ervaring betekent de uitzonde ring voor onvoldoende geheugen niet dat de container grootte te klein is. Dit betekent dat de grootte van de Java-heap (Hive. TEZ. java. kiest) te klein is. Als er onvoldoende geheugen wordt weer geven, kunt u proberen om **Hive. TEZ. java. kiest**. Als dat nodig is, moet u wellicht **component. TEZ. container. size**verhogen. De instelling **Java. kiest** zich ongeveer 80% van **container. size**.

> [!NOTE]  
> De **component setting. TEZ. java. kiest** altijd kleiner dan **Hive. TEZ. container. size**.

Omdat een D12 machine 28 GB geheugen heeft, hebben we besloten een container grootte van 10 GB (10240 MB) te gebruiken en 80% toe te wijzen aan java. kiest voor:

    SET hive.tez.container.size=10240
    SET hive.tez.java.opts=-Xmx8192m

Met de nieuwe instellingen is de query binnen tien minuten uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

Het ophalen van een OOM-fout betekent niet noodzakelijkerwijs dat de container grootte te klein is. In plaats daarvan moet u de geheugen instellingen configureren zodat de heap groter wordt en ten minste 80% van de grootte van het container geheugen. Zie [Apache Hive Query's optimaliseren voor Apache Hadoop in HDInsight](hdinsight-hadoop-optimize-hive-query.md)voor het optimaliseren van Hive-query's.
