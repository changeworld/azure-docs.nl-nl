---
title: Een machine learning algoritme selecteren
titleSuffix: Azure Machine Learning
description: Azure Machine Learning-algoritmen selecteren voor onder Super visie en zonder super visie geleerde lessen in Clustering, classificatie of regressie experimenten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: FrancescaLazzeri
ms.author: lazzeri
ms.reviewer: cgronlun
ms.date: 03/05/2020
ms.openlocfilehash: e0482bac9569a834adf3e1cdef2b3f702980eac0
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78328660"
---
# <a name="how-to-select-algorithms-for-azure-machine-learning"></a>Algoritmen voor Azure Machine Learning selecteren

Een veelvoorkomende vraag is ' welk machine learning-algoritme moet ik gebruiken? ' Het algoritme dat u selecteert, is afhankelijk van twee verschillende aspecten van het data Science-scenario:

 - **Wat wilt u met uw gegevens doen?** Wat is met name de zakelijke vraag die u wilt beantwoorden door meer te weten te komen over uw vroegere gegevens?

 - **Wat zijn de vereisten van uw data Science-scenario?** Wat is met name de nauw keurigheid, de trainings tijd, de lineariteit, het aantal para meters en het aantal functies dat door uw oplossing wordt ondersteund?

 ![Overwegingen voor het kiezen van algoritmen: wat wilt u weten? Wat zijn de scenario vereisten?](./media/how-to-select-algorithms/how-to-select-algorithms.png)

## <a name="business-scenarios-and-the-machine-learning-algorithm-cheat-sheet"></a>Bedrijfs scenario's en het Cheat-werk blad van Machine Learning algoritme

Het [Cheat-venster van Azure machine learning Algorithm](https://docs.microsoft.com/azure/machine-learning/algorithm-cheat-sheet?WT.mc_id=docs-article-lazzeri) helpt u bij de eerste overweging: **wat u met uw gegevens wilt doen**? Zoek op het blad Machine Learning Algorithm Cheat naar een taak die u wilt uitvoeren en zoek vervolgens een [Azure machine learning design](https://docs.microsoft.com/azure/machine-learning/concept-designer?WT.mc_id=docs-article-lazzeri) -algoritme voor de Predictive Analytics oplossing. 

Machine Learning Designer voorziet in een uitgebreide Port Folio met algoritmen, zoals het forest voor het [nemen](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-decision-forest?WT.mc_id=docs-article-lazzeri)van een omgeving, [aanbevelings systemen](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-recommender?WT.mc_id=docs-article-lazzeri), [Neural netwerk regressie](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/neural-network-regression?WT.mc_id=docs-article-lazzeri), [Multiklasse Neural netwerk](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-neural-network?WT.mc_id=docs-article-lazzeri)en [K-betekent clustering](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/k-means-clustering?WT.mc_id=docs-article-lazzeri). Elk algoritme is ontworpen om een ander type machine learning probleem op te lossen. Zie de [Naslag informatie over machine learning Designer-algoritme en de module](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/module-reference?WT.mc_id=docs-article-lazzeri) voor een volledige lijst samen met documentatie over de werking van elk algoritme en het afstemmen van de para meters om de algoritme te optimaliseren.

> [!NOTE]
> Als u het Cheat-venster van machine learning Algorithm wilt downloaden, gaat u naar het Cheat-blad van het [Azure machine learning-algoritme](https://docs.microsoft.com/azure/machine-learning/algorithm-cheat-sheet?WT.mc_id=docs-article-lazzeri).
> 
> 

Naast de richt lijnen in het Cheat-venster van de Azure Machine Learning-algoritme moet u andere vereisten overwegen wanneer u een machine learning algoritme voor uw oplossing kiest. Hieronder vindt u aanvullende factoren om rekening mee te houden, zoals de nauw keurigheid, de trainings tijd, de lineariteit, het aantal para meters en het aantal functies.

## <a name="additional-requirements-for-a-data-science-scenario"></a>Aanvullende vereisten voor een Data Science-scenario

Wanneer u weet wat u met uw gegevens wilt doen, moet u aanvullende vereisten voor uw oplossing bepalen. 

Maak keuzen en mogelijke trans acties voor de volgende vereisten:

- Nauw keurigheid
- Trainings tijd
- Lineariteit
- Aantal para meters
- Aantal functies

## <a name="accuracy"></a>Nauw keurigheid

Nauw keurigheid van machine learning meet de effectiviteit van een model als het aandeel van de werkelijke resultaten tot het totale aantal cases. In Machine Learning Designer berekent de [module evaluate model](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-model?WT.mc_id=docs-article-lazzeri) een set metrische standaard waarden voor de evaluatie van de industrie. U kunt deze module gebruiken om de nauw keurigheid van een getraind model te meten.

Het is niet altijd nodig om het meest nauw keurige antwoord optimaal te benutten. Soms is een benadering voldoende, afhankelijk van wat u wilt gebruiken voor. Als dat het geval is, kunt u uw verwerkings tijd aanzienlijk verkorten door de methoden te benaderen. Methoden die bij benadering zijn, zijn ook vanzelfsprekend om overmontage te voor komen.

Er zijn drie manieren om de module evaluate model te gebruiken:

- Scores genereren voor uw trainings gegevens om het model te evalueren
- Scores op het model genereren, maar deze scores vergelijken met scores op een gereserveerde testset
- Vergelijk de scores voor twee verschillende, gerelateerde modellen, met dezelfde set gegevens

Zie [Evaluate model module](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-model?WT.mc_id=docs-article-lazzeri)voor een volledige lijst met metrische gegevens en benaderingen die u kunt gebruiken om de nauw keurigheid van machine learning modellen te evalueren.

## <a name="training-time"></a>Trainings tijd

Bij het leren van een super visie betekent de training dat historische gegevens worden gebruikt voor het bouwen van een machine learning model dat fouten minimaliseert. Het aantal minuten of uren dat nodig is voor het trainen van een model, is afhankelijk van de verschillende algoritmen. De trainings tijd is vaak nauw verbonden met nauw keurigheid. een voor de andere. 

Bovendien zijn sommige algoritmen gevoeliger voor het aantal gegevens punten dan andere. U kunt een specifiek algoritme kiezen, omdat u een tijds beperking hebt, vooral wanneer de gegevensset groot is.

In Machine Learning Designer is het maken en gebruiken van een machine learning model doorgaans een proces met drie stappen:

1.  Configureer een model door een bepaald type algoritme te kiezen en vervolgens de para meters of Hyper parameters te definiëren. 

2.  Geef een gegevensset op die is gelabeld en die gegevens bevat die compatibel zijn met het algoritme. Verbind zowel de gegevens als het model om de [model module te trainen](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/train-model?WT.mc_id=docs-article-lazzeri).

3.  Nadat de training is voltooid, kunt u het getrainde model met een van de [Score modules](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/score-model?WT.mc_id=docs-article-lazzeri) gebruiken om voor spellingen te doen op nieuwe gegevens.

## <a name="linearity"></a>Lineariteit

Lineariteit in statistieken en machine learning betekent dat er een lineaire relatie is tussen een variabele en een constante in uw gegevensset. Voor beelden van lineaire classificatie-algoritmen gaan ervan uit dat klassen kunnen worden gescheiden door een rechte lijn (of het hogere analoge).

Veel machine learning-algoritmen maken gebruik van lineariteit. In Azure Machine Learning Designer omvatten ze: 

- [Multiklasse-logistieke regressie](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-logistic-regression?WT.mc_id=docs-article-lazzeri)
- [Logistiek regressie met twee klassen](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-logistic-regression?WT.mc_id=docs-article-lazzeri)
- [Vector machines ondersteunen](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-support-vector-machine?WT.mc_id=docs-article-lazzeri)  

Bij lineaire regressie algoritmen wordt ervan uitgegaan dat gegevens trends een rechte lijn volgen. Deze veronderstelling is niet slecht voor bepaalde problemen, maar voor anderen vermindert de nauw keurigheid. Ondanks hun nadelen zijn lineaire algoritmen populair als de eerste strategie. Ze zijn vaak algorithmically eenvoudig en snel te trainen.

![Grens van niet-lineaire klasse](./media/how-to-select-algorithms/nonlinear-class-boundary.png)

Grens van niet- ***lineaire klasse***: *afhankelijk van een lineaire classificatie algoritme zou een lage nauw keurigheid opleveren.*

![Gegevens met een niet-lineaire trend](./media/how-to-select-algorithms/nonlinear-trend.png)

***Gegevens met een niet-lineaire trend***: als *u een lineaire regressie methode gebruikt, worden er veel grotere fouten gegenereerd dan nodig zijn.*

## <a name="number-of-parameters"></a>Aantal para meters

Para meters zijn de knopen die een gegevens wetenschapper krijgt om in te scha kelen bij het instellen van een algoritme. Ze zijn getallen die van invloed zijn op het gedrag van de algoritme, zoals fout tolerantie of het aantal iteraties of opties tussen varianten van de werking van het algoritme. De trainings tijd en nauw keurigheid van het algoritme kunnen soms gevoelig zijn om alleen de juiste instellingen te verkrijgen. Normaal gesp roken vereisen algoritmen met een groot aantal para meters de meeste proef versie en fout om een goede combi natie te vinden.

U kunt ook de [module model Hyper parameters afstemmen](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/tune-model-hyperparameters?WT.mc_id=docs-article-lazzeri) in machine learning Designer: het doel van deze module is het bepalen van de optimale Hyper parameters voor een machine learning model. De module bouwt en test meerdere modellen met behulp van verschillende combi Naties van instellingen. Hiermee worden metrische gegevens van alle modellen vergeleken om de combi Naties van instellingen te verkrijgen. 

Dit is een goede manier om ervoor te zorgen dat u de parameter ruimte hebt gemaakt. de tijd die nodig is om een model te trainen, neemt exponentieel toe met het aantal para meters. Aan de kant van een groot aantal para meters wordt doorgaans aangegeven dat een algoritme meer flexibiliteit heeft. Het kan vaak zeer goed nauw keurig zijn, op voor waarde dat u de juiste combi natie van parameter instellingen kunt vinden.

## <a name="number-of-features"></a>Aantal functies

In machine learning is een functie een meet bare variabele van het verschijnsel dat u probeert te analyseren. Voor bepaalde typen gegevens kan het aantal functies erg groot zijn vergeleken met het aantal gegevens punten. Dit is vaak het geval met genetischer of tekstuele gegevens. 

Een groot aantal functies kan enkele leer algoritmen verwarrenden, waardoor de trainings tijd unfeasibly lang wordt. [Support Vector machines](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-support-vector-machine?WT.mc_id=docs-article-lazzeri) zijn bijzonder goed geschikt voor scenario's met een groot aantal functies. Daarom zijn ze in veel toepassingen gebruikt om gegevens op te halen uit de tekst-en afbeeldings classificatie. Ondersteuning voor vector machines kan worden gebruikt voor zowel classificatie-als regressie taken.

De functie selectie verwijst naar het proces van het Toep assen van statistische tests op invoer, op basis van een opgegeven uitvoer. Het doel is om te bepalen welke kolommen meer voor spelling van de uitvoer zijn. De [module](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/filter-based-feature-selection?WT.mc_id=docs-article-lazzeri) voor het selecteren van functies op basis van filters in machine learning Designer bevat meerdere algoritmen voor het selecteren van onderdelen. De module bevat correlatie methoden, zoals Pearson-correlatie en Chi-kwadraat waarden.

U kunt ook de [permutatie functie urgentie module](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/permutation-feature-importance?WT.mc_id=docs-article-lazzeri) gebruiken om een aantal scores voor de functie prioriteit voor uw gegevensset te berekenen. Vervolgens kunt u deze scores gebruiken om te bepalen welke functies het beste in een model moeten worden gebruikt.


## <a name="next-steps"></a>Volgende stappen

 - [Meer informatie over Azure Machine Learning Designer](https://docs.microsoft.com/azure/machine-learning/service/concept-designer?WT.mc_id=docs-article-lazzeri)
 - Zie [machine learning design-algoritme en module referentie](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/module-reference?WT.mc_id=docs-article-lazzeri) voor beschrijvingen van alle machine learning-algoritmen die beschikbaar zijn in azure machine learning Designer.
 - Als u de relatie tussen diep leren, machine learning en AI wilt verkennen, raadpleegt u [uitgebreide informatie versus machine learning](https://docs.microsoft.com/azure/machine-learning/service/concept-deep-learning-vs-machine-learning?WT.mc_id=docs-article-lazzeri)
