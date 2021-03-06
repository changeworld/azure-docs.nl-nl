---
title: Machine learning en data science-hulpprogramma 's
titleSuffix: Azure Data Science Virtual Machine
description: Meer informatie over de hulpprogram ma's voor machine learning en frameworks die vooraf zijn geïnstalleerd op de Data Science Virtual Machine.
keywords: hulpprogramma's voor datatechnologie, virtuele machine voor datatechnologie, hulpprogramma voor datatechnologie, linux-datatechnologie
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: c6b7e3da6cb6fd87e3b43d6f310e3b76f8fc4d30
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79283834"
---
# <a name="machine-learning-and-data-science-tools-on-azure-data-science-virtual-machines"></a>Machine learning-en data Science-hulpprogram ma's op Azure data Science Virtual Machines
Azure data Science Virtual Machines (Dsvm) beschikt over een uitgebreide set hulpprogram ma's en bibliotheken voor machine learning die beschikbaar zijn in populaire talen, zoals python, R en Julia.

Hier volgen enkele van de machine learning-hulpprogram ma's en-bibliotheken op Dsvm.

## <a name="azure-machine-learning-sdk-for-python"></a>Azure Machine Learning-SDK voor Python

Zie de volledige referentie voor de [Azure machine learning SDK voor python](https://docs.microsoft.com/azure/machine-learning/overview-what-is-azure-ml).

|    |           |
| ------------- | ------------- |
| Wat is het?   |   Azure Machine Learning is een Cloud service die u kunt gebruiken om machine learning-modellen te ontwikkelen en te implementeren. Als u bouwen, trainen, schalen en beheren met behulp van de Python-SDK, kunt u uw modellen bijhouden. Modellen als containers implementeren en deze in de cloud, on-premises of in Azure IoT Edge uitvoeren.   |
| Ondersteunde versies     | Windows (conda-omgeving: AzureML), Linux (conda-omgeving: py36)    |
| Wordt doorgaans gebruikt      | Algemeen machine learning-platform      |
| Hoe wordt deze geconfigureerd of geïnstalleerd?      |  Geïnstalleerd met GPU-ondersteuning   |
| Over het gebruik of uit te voeren      | Als een python-SDK en in de Azure CLI. Activeer de Conda-omgeving `AzureML` op de Windows-editie *of* op `py36` in Linux Edition.      |
| Koppeling naar voorbeelden      | Voor beelden van Jupyter-notebooks zijn opgenomen in de `AzureML`-map onder notebooks.  |
| Meer hulpprogramma 's      | Visual Studio Code, Jupyter   |

## <a name="h2o"></a>H2O

|    |           |
| ------------- | ------------- |
| Wat is het?   | Een open-source AI-platform dat ondersteuning biedt voor in-Memory, gedistribueerde, snelle en schaal bare machine learning.  |
| Ondersteunde versies      | Linux   |
| Wordt doorgaans gebruikt      | Gedistribueerde, schaal bare machine learning voor algemeen gebruik   |
| Hoe wordt deze geconfigureerd of geïnstalleerd?      | H2O is geïnstalleerd in `/dsvm/tools/h2o`.      |
| Over het gebruik of uit te voeren      | Verbinding maken met de virtuele machine met behulp van X2Go. Start een nieuwe terminal en voer `java -jar /dsvm/tools/h2o/current/h2o.jar`uit. Start vervolgens een webbrowser en maak verbinding met `http://localhost:54321`.      |
| Koppeling naar voorbeelden      | Voor beelden zijn beschikbaar op de virtuele machine in Jupyter in de map `h2o`.      |
| Meer hulpprogramma 's      | Apache Spark, MXNet, XGBoost, mousserende Water, Deep Water    |

Er zijn verschillende andere machine learning-bibliotheken op Dsvm, zoals het populaire `scikit-learn`-pakket dat deel uitmaakt van de Anaconda python-distributie voor Dsvm. Om te zien wat de lijst met pakketten die beschikbaar zijn in Python, voert R en Julia, u de respectieve Pakketbeheer.

## <a name="lightgbm"></a>LightGBM

|    |           |
| ------------- | ------------- |
| Wat is het?   | Een snel, gedistribueerd verloop van de kleur overgang-Boosting (GBDT, GBRT, GBM of MART Framework) op basis van de beslissings structuur-algoritmen. Dit wordt gebruikt voor classificatie, classificatie en veel andere taken voor machine learning.    |
| Ondersteunde versies      | Windows, Linux    |
| Wordt doorgaans gebruikt      | Algemeen gebruik van kleur overgang-Boosting Framework      |
| Hoe wordt deze geconfigureerd of geïnstalleerd?      | LightGBM is geïnstalleerd op Windows, als een Python-pakket. Op Linux bevindt het opdracht regel programma zich in `/opt/LightGBM/lightgbm`, wordt het R-pakket geïnstalleerd en worden Python-pakketten geïnstalleerd.     |
| Koppeling naar voorbeelden      | [LightGBM-hand leiding](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| Meer hulpprogramma 's      | MXNet, XgBoost  |

## <a name="rattle"></a>Rammelaar
|    |           |
| ------------- | ------------- |
| Wat is het?   |   Een Graphical User Interface voor gegevens analyse met behulp van R.   |
| Ondersteunde versies     | Windows, Linux     |
| Wordt doorgaans gebruikt      | Algemeen UI-hulp programma voor gegevens analyse voor R    |
| Over het gebruik of uit te voeren      | Als een hulp programma voor de gebruikers interface. Start in Windows een opdracht prompt, voer R uit en voer vervolgens in R `rattle()`uit. Op Linux maakt u verbinding met X2Go, start u een Terminal, voert u R uit en voert u vervolgens in R `rattle()`uit. |
| Koppeling naar voorbeelden      | [Rattle](https://togaware.com/onepager/) |
| Meer hulpprogramma 's      |LightGBM, Weka, XGBoost   |

## <a name="vowpal-wabbit"></a>Vowpal Wabbit
|    |           |
| ------------- | ------------- |
| Wat is het?   |   Een snelle, open-source, out-of-Core-learning systeem bibliotheek    |
| Ondersteunde versies     | Windows, Linux     |
| Wordt doorgaans gebruikt      | Algemene machine-learning-bibliotheek      |
| Hoe wordt deze geconfigureerd of geïnstalleerd?      |  Windows: MSI-installatie programma<br/>Linux: apt-get |
| Over het gebruik of uit te voeren      | Als een on-path opdracht regel programma (`C:\Program Files\VowpalWabbit\vw.exe` in Windows `/usr/bin/vw` in Linux)    |
| Koppeling naar voorbeelden      | [VowPal Wabbit-voor beelden](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| Meer hulpprogramma 's      |LightGBM, MXNet, XGBoost   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Wat is het?   |  Een verzameling machine learning-algoritmen voor gegevens analyse taken. De algoritmen kunnen worden toegepast op een set gegevens rechtstreeks of met de naam van uw eigen Java-code. Weka bevat hulpprogramma's voor het vooraf verwerken van gegevens, classificatie, regressie, clustering, koppelingsregels voor en visualisatie. |
| Ondersteunde versies     | Windows, Linux     |
| Wordt doorgaans gebruikt      | Algemeen hulp programma voor machine learning     |
| Over het gebruik of uit te voeren      | Zoek in Windows naar Fridge in het menu **Start** . Meld u aan bij Linux met X2Go en ga vervolgens naar **toepassingen** > **Development** > **Fridge**. |
| Koppeling naar voorbeelden      | [Fridge-voor beelden](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Meer hulpprogramma 's      |LightGBM, ratel, XGBoost   |

## <a name="xgboost"></a>XGBoost 
|    |           |
| ------------- | ------------- |
| Wat is het?   |   Een snelle, draag bare en gedistribueerde GBDT-, GBRT-of GBM-bibliotheek voor python, R, Java, scala, C++enzovoort. Deze wordt uitgevoerd op één computer en op Apache Hadoop en Spark.    |
| Ondersteunde versies     | Windows, Linux     |
| Wordt doorgaans gebruikt      | Algemene machine-learning-bibliotheek      |
| Hoe wordt deze geconfigureerd of geïnstalleerd?      |  Geïnstalleerd met GPU-ondersteuning   |
| Over het gebruik of uit te voeren      | Als Python-bibliotheek (2,7 en 3,5), R-pakket en on-path opdracht regel programma (`C:\dsvm\tools\xgboost\bin\xgboost.exe` voor Windows en `/dsvm/tools/xgboost/xgboost` voor Linux)    |
| Koppelingen naar voorbeelden      | Voor beelden zijn beschikbaar op de virtuele machine, in `/dsvm/tools/xgboost/demo` in Linux en `C:\dsvm\tools\xgboost\demo` in Windows.   |
| Meer hulpprogramma 's      | LightGBM, MXNet   |

## <a name="apache-drill"></a>Apache Drill
|    |           |
| ------------- | ------------- |
| Wat is het?   | Open-Source SQL-query-engine op big data    |
| Ondersteunde DSVM-versies      | Windows 2019 (preview), Linux  |
| Hoe wordt het geconfigureerd en geïnstalleerd op de DSVM?      |  In `/dsvm/tools/drill*` alleen in de Inge sloten modus geïnstalleerd   |
| Wordt doorgaans gebruikt      |  Voor in-place gegevens onderzoek zonder extra heren, transformeren, laden (ETL). Query's uitvoeren op verschillende gegevens bronnen en indelingen, waaronder CSV, JSON, relationele tabellen en Hadoop.     |
| Hoe gebruiken en uitvoeren      | Snelkoppeling bureau blad  <br/> [Aan de slag met boren in 10 minuten](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| Gerelateerde hulpprogram ma's op de DSVM      |   Rattle, Fridge, SQL Server Management Studio      |


