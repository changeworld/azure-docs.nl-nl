---
title: Scenario's identificeren en het analyse proces plannen-team data Science process | Azure Machine Learning
description: Identificeer scenario's en plan voor geavanceerde analytische gegevensverwerking op basis van een reeks van belangrijke vragen.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: b0b811a2b7ed432b7fc5015886b28337ca33424e
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76710323"
---
# <a name="how-to-identify-scenarios-and-plan-for-advanced-analytics-data-processing"></a>Scenario's en plannen identificeren voor geavanceerde analytische gegevensverwerking

Welke resources zijn er nodig om een omgeving te maken die geavanceerde analyse verwerking op een gegevensset kan uitvoeren? In dit artikel wordt een reeks vragen gesteld die u kan helpen bij het identificeren van taken en resources die relevant zijn voor uw scenario.

Zie [Wat is het team data Science process (TDSP)](overview.md)voor meer informatie over de volg orde van de stappen op hoog niveau voor Predictive Analytics. Elke stap vereist specifieke resources voor de taken die relevant zijn voor uw specifieke scenario.

Vraag de belangrijkste vragen op de volgende gebieden om uw scenario te identificeren:

* gegevens logistiek
* gegevens kenmerken
* kwaliteit van gegevensset
* Aanbevolen hulpprogram ma's en talen

## <a name="logistic-questions-data-locations-and-movement"></a>Logistieke vragen: gegevenslocaties en -verplaatsing

De logistiek vragen hebben betrekking op de volgende items:

* gegevens bron locatie
* doel bestemming in azure
* vereisten voor het verplaatsen van de gegevens, met inbegrip van de planning, het bedrag en de betrokken resources

Mogelijk moet u de gegevens tijdens het analyse proces meerdere keren verplaatsen. Een gebruikelijk scenario is het lokale gegevens verplaatsen naar een vorm van opslag van Azure en vervolgens in Machine Learning Studio.

### <a name="what-is-your-data-source"></a>Wat is uw gegevens bron?

Bevindt uw gegevens zich lokaal of in de Cloud? Mogelijke locaties zijn:

* een openbaar beschikbaar HTTP-adres
* de locatie van een lokaal of netwerk bestand
* een SQL Server-Data Base
* een Azure Storage-container

### <a name="what-is-the-azure-destination"></a>Wat is de Azure-bestemming?

Waar moeten uw gegevens worden verwerkt of gemodelleerd? 

* Azure Blob Storage
* SQL Azure-databases
* SQL Server op virtuele Azure-machine
* HDInsight (Hadoop op Azure) of Hive-tabellen
* Azure Machine Learning
* Koppel bare virtuele harde schijven van Azure

### <a name="how-are-you-going-to-move-the-data"></a>Hoe gaat u de gegevens verplaatsen?

Zie voor procedures en bronnen voor het opnemen of laden van gegevens in een groot aantal verschillende opslag-en verwerkings omgevingen:

* [Gegevens laden in opslag omgevingen voor analyse](ingest-data.md)
* [Importeer uw trainings gegevens in Azure Machine Learning Studio (klassiek) van verschillende gegevens bronnen](../studio/import-data.md)

### <a name="does-the-data-need-to-be-moved-on-a-regular-schedule-or-modified-during-migration"></a>Moeten de gegevens worden verplaatst volgens een regel matig schema of worden gewijzigd tijdens de migratie?

Overweeg het gebruik van Azure Data Factory (ADF) wanneer gegevens voortdurend moeten worden gemigreerd. ADF kan handig zijn voor:

* een hybride scenario waarbij zowel on-premises als in de cloud resources worden betrokken
* een scenario waarin de gegevens worden verwerkt, gewijzigd of gewijzigd door bedrijfs logica tijdens het migreren

Zie [gegevens verplaatsen van een on-premises SQL-Server naar SQL Azure met Azure Data Factory](move-sql-azure-adf.md)voor meer informatie.

### <a name="how-much-of-the-data-is-to-be-moved-to-azure"></a>Hoeveel gegevens worden verplaatst naar Azure?

Grote gegevens sets kunnen de opslag capaciteit van bepaalde omgevingen overschrijden. Zie voor een voor beeld de bespreking van grootte limieten voor Machine Learning Studio (klassiek) in de volgende sectie. In dergelijke gevallen kunt u een voor beeld van de gegevens tijdens de analyse gebruiken. Zie [voorbeeld gegevens in het team data Science process](sample-data.md)voor meer informatie over het omlaag bemonsteren van een gegevensset in verschillende Azure-omgevingen.

## <a name="data-characteristics-questions-type-format-and-size"></a>Vragen over de kenmerken van gegevens: type, de indeling en grootte

Deze vragen zijn essentieel om uw opslag-en verwerkings omgevingen te plannen. Ze kunnen u helpen bij het kiezen van het juiste scenario voor uw gegevens type en het begrijpen van alle beperkingen.

### <a name="what-are-the-data-types"></a>Wat zijn de gegevens typen?

* Numerieke
* Categorische gegevens
* Tekenreeksen
* Binair bestand

### <a name="how-is-your-data-formatted"></a>Hoe worden uw gegevens ingedeeld?

* Door komma's gescheiden (CSV) of platte bestanden (TSV) door tabs gescheiden
* Wel of niet gecomprimeerd
* Azure-blobs
* Hadoop Hive-tabellen
* SQL Server-tabellen

### <a name="how-large-is-your-data"></a>Hoe groot zijn uw gegevens?

* Kleine: minder dan 2 GB
* Gemiddeld: Groter is dan 2 GB en kleiner dan 10 GB
* Grote: Groter is dan 10 GB

Maak de Azure Machine Learning Studio-omgeving (klassiek) bijvoorbeeld:

* Zie de sectie [gegevens indelingen en gegevens typen](../studio/import-data.md#supported-data-formats-and-data-types) die worden ondersteund voor een lijst met de gegevens indelingen en typen die door Azure machine learning Studio worden ondersteund.
* Zie [Azure-abonnement en service limieten, quota's en beperkingen](../../azure-resource-manager/management/azure-subscription-service-limits.md)voor meer informatie over de beperkingen van andere Azure-Services die worden gebruikt in het analyse proces.

## <a name="data-quality-questions-exploration-and-pre-processing"></a>Vragen over de kwaliteit van gegevens: verkennen en de voorverwerking

### <a name="what-do-you-know-about-your-data"></a>Wat weet u over uw gegevens?

Meer informatie over de basis kenmerken van uw gegevens:

* De patronen of trends die het vertoont
* De uitschieters
* Hoeveel waarden ontbreken

Deze stap is belang rijk om u te helpen:

* Bepalen hoeveel vooraf-verwerking nodig is
* Formuleer hypo Thesen die de meest geschikte functies of type analyse Voorst Ellen
* Plannen formuleren voor extra gegevens verzameling

Handige technieken voor gegevens inspectie zijn onder andere het berekenen van de berekenings statistieken en visualisaties. Voor meer informatie over hoe u een gegevensset in verschillende Azure-omgevingen kunt verkennen, raadpleegt u [gegevens in het team data Science proces verkennen](explore-data.md).

### <a name="does-the-data-require-preprocessing-or-cleaning"></a>Is voor de gegevens voor verwerking of reiniging vereist?

Mogelijk moet u uw gegevens voorverwerken en opschonen voordat u de gegevensset effectief kunt gebruiken voor machine learning. Onbewerkte gegevens zijn vaak ruis en onbetrouwbaar. Er ontbreken mogelijk waarden. Met behulp van dergelijke gegevens voor modellen kan misleidend resultaten opleveren. Zie [taken om gegevens voor te bereiden voor verbeterde machine learning](prepare-data.md)voor een beschrijving.

## <a name="tools-and-languages-questions"></a>Vragen over hulpprogramma's en talen

Er zijn veel opties voor talen, ontwikkel omgevingen en hulpprogram ma's. Houd rekening met uw behoeften en voor keuren.

### <a name="what-languages-do-you-prefer-to-use-for-analysis"></a>Welke talen wilt u liever voor analyse gebruiken?

* R
* Python
* SQL

### <a name="what-tools-should-you-use-for-data-analysis"></a>Welke hulpprogram ma's moet u voor gegevens analyse gebruiken?

* [Microsoft Azure Power shell](/powershell/azure/overview) : een script taal die wordt gebruikt voor het beheren van uw Azure-resources in een script taal
* [Azure Machine Learning Studio](../studio/what-is-ml-studio.md)
* [Revolutie Analytics](https://www.microsoft.com/sql-server/machinelearningserver)
* [RStudio](https://www.rstudio.com)
* [Python Tools for Visual Studio](https://aka.ms/ptvsdocs)
* [Anaconda](https://www.continuum.io/why-anaconda)
* [Jupyter-notebooks](https://jupyter.org/)
* [Micro soft Power BI](https://powerbi.microsoft.com)

## <a name="identify-your-advanced-analytics-scenario"></a>Identificeren van uw scenario voor geavanceerde analyses

Nadat u de vragen in de vorige sectie hebt beantwoord, kunt u bepalen welk scenario het meest geschikt is voor uw situatie. De voorbeeld scenario's worden beschreven in [scenario's voor geavanceerde analyses in azure machine learning](plan-sample-scenarios.md).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Wat is het team data Science process (TDSP)?](overview.md)
