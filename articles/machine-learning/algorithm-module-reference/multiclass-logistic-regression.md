---
title: 'Multi klasse logistiek regressie: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module logistiek-regressie voor meerdere klassen in Azure Machine Learning voor het maken van een logistiek regressie model dat kan worden gebruikt voor het voors pellen van verschillende waarden.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 40193a2547959b44c5753cfcc6ccad9344ab9486
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920430"
---
# <a name="multiclass-logistic-regression-module"></a>Module logistiek-regressie voor multi klasse

In dit artikel wordt een module in Azure Machine Learning Designer (preview) beschreven.

Met deze module kunt u een logistiek regressie model maken dat kan worden gebruikt om meerdere waarden te voors pellen.

Classificatie met behulp van logistiek regressie is een geclassificeerde leer methode en vereist daarom een gegevensset met een label. U traint het model door het model en de gelabelde gegevensset op te geven als invoer voor een module, zoals [Train model](./train-model.md). Het getrainde model kan vervolgens worden gebruikt om waarden voor nieuwe invoer voorbeelden te voors pellen.

Azure Machine Learning biedt ook een [logistiek regressie module van twee klassen](./two-class-logistic-regression.md) , die geschikt is voor de classificatie van binaire of dichotomous variabelen.

## <a name="about-multiclass-logistic-regression"></a>Over een logistiek regressie voor multi klasse

Logistiek regressie is een bekende methode in statistieken die wordt gebruikt om de kans op een resultaat te voors pellen en populair is voor classificatie taken. Het algoritme voor spelt de kans op een gebeurtenis door gegevens aan een logistiek functie toe te passen. 

In een logistiek-regressie met meerdere klassen kan de classificatie worden gebruikt voor het voors pellen van meervoudige resultaten.

## <a name="configure-a-multiclass-logistic-regression"></a>Een logistiek-regressie met meer klasse configureren

1. Voeg de module **logistiek-regressie** (Multi Class) toe aan de pijp lijn.

2. Geef op hoe u wilt dat het model wordt getraind door de optie **trainer modus maken** in te stellen.

    + **Eén para meter**: gebruik deze optie als u weet hoe u het model wilt configureren en geef een specifieke set waarden als argumenten op.

    + **Parameter bereik**: Selecteer deze optie als u niet zeker weet wat de beste para meters zijn en u een parameter sweep wilt uitvoeren. Selecteer een bereik met waarden om over te sporen en het [model Hyper parameters](tune-model-hyperparameters.md) wordt herhaald op alle mogelijke combi Naties van de instellingen die u hebt verstrekt om de Hyper parameters te bepalen die de optimale resultaten opleveren.  

3. **Optimalisatie tolerantie**, geeft u de drempel waarde voor optimalisatie convergentie op. Als de verbetering tussen herhalingen kleiner is dan de drempel waarde, wordt het algoritme gestopt en wordt het huidige model geretourneerd.

4. **L1 regularisatie Weight**, **L2-regularisatie gewicht**: Typ een waarde die moet worden gebruikt voor de regularisatie-para meters L1 en L2. Een andere waarde dan nul wordt aanbevolen voor beide.

    Regularisatie is een methode voor het voor komen van overmontage door het bestraffen van modellen met extreme coëfficiënt waarden. Regularisatie werkt door de sanctie toe te voegen die is gekoppeld aan coëfficiënt waarden voor de fout van de hypo these. Een nauw keurig model met extreme coëfficiënt waarden wordt meer bestraft, maar een minder nauw keurig model met meer conservatieve waarden zou minder worden bestraft.

     L1 en L2-regularisatie hebben verschillende effecten en worden gebruikt. L1 kan worden toegepast op sparse modellen, wat handig is bij het werken met zeer dimensionale gegevens. In tegens telling tot L2-regularisatie is de voor keur voor gegevens die niet sparse zijn.  Dit algoritme ondersteunt een lineaire combi natie van L1-en L2 regularisatie-waarden: dat wil zeggen, als `x = L1` en `y = L2`, `ax + by = c` de lineaire lijn van de regularisatie-voor waarden definieert.

     Er zijn verschillende lineaire combi Naties van L1-en L2-termen ontwikkeld voor logistieke regressie modellen, zoals [elastische net regularisatie](https://wikipedia.org/wiki/Elastic_net_regularization).

6. **Wille keurig getal Seed**: Typ een geheel getal dat moet worden gebruikt als seed voor het algoritme als u wilt dat de resultaten herhaaldelijk worden uitgevoerd. Anders wordt een waarde van de systeem klok gebruikt als seed, waardoor er iets andere resultaten kunnen worden geproduceerd in uitvoeringen van dezelfde pijp lijn.

8. Verbind een gegevensset met een label en een van de trein modules:

    + Als u de **modus trainer maken** instelt op **één para meter**, gebruikt u de module [Train model](./train-model.md) .

9. Voer de pijplijn uit.



## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 