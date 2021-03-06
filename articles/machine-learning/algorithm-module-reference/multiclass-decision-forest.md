---
title: 'Multi Class-besluitvormings forest: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module voor het maken van een machine learning model op basis van *het algoritme van de beslissings* forest in azure machine learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: b1f7aaeaf5d1537e0e5a427ad994f7e31e43fee3
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77921365"
---
# <a name="multiclass-decision-forest-module"></a>Module voor de beslissings forest voor multi klassen

In dit artikel wordt een module in Azure Machine Learning Designer (preview) beschreven.

Gebruik deze module om een machine learning model te maken op basis van het algoritme van het *beslissings forest* . Een besluitvormings forest is een ensemble model dat snel een reeks beslissings structuren bouwt, terwijl u leert van gelabelde gegevens.

## <a name="more-about-decision-forests"></a>Meer informatie over besluitvormings forests

Het algoritme van het beslissings forest is een ensemble leer methode voor classificatie. Het algoritme werkt door meerdere beslissings structuren te bouwen en vervolgens te *stemmen* op de meest populaire uitvoer klasse. Stemmen is een vorm van aggregatie, waarbij elke structuur in een forest met classificatie beslissingen een niet-genormaliseerd frequentie histogram van labels uitvoert. Het aggregatie proces berekent deze histogrammen en normaliseert het resultaat om de ' kansen ' voor elk label op te halen. De structuren met een hoog Voorspellings betrouwbaarheids niveau hebben een groter gewicht in de definitieve beslissing van de ensemble.

Beslissings structuren in het algemeen zijn niet-parametrische modellen, wat betekent dat ze gegevens ondersteunen met verschillende distributies. In elke structuur wordt een reeks eenvoudige tests uitgevoerd voor elke klasse, waardoor de niveaus van een boom structuur worden verhoogd totdat een Leaf-knoop punt (beslissing) is bereikt.

Beslissings structuren hebben veel voor delen:

+ Ze kunnen niet-lineaire beslissings grenzen vertegenwoordigen.
+ Ze zijn efficiënt in reken-en geheugen gebruik tijdens de training en voor spellingen.
+ Ze voeren geïntegreerde functie selectie en-classificatie uit.
+ Ze zijn robuust in de aanwezigheid van ruis functies.

De classificatie van het beslissings forest in Azure Machine Learning bestaat uit een ensemble van beslissings structuren. In het algemeen bieden ensemble-modellen betere dekking en nauw keurigheid dan enkelvoudige beslissings structuren. Zie [beslissings structuren](https://go.microsoft.com/fwlink/?LinkId=403677)voor meer informatie.

## <a name="how-to-configure-multiclass-decision-forest"></a>Het configureren van een forest met een klassen beslissing

1. Voeg de module voor het **beslissings forest** met een klassemodule toe aan uw pijp lijn in de ontwerp functie. U kunt deze module vinden onder **machine learning**, **model initialiseren**en **classificatie**.

2. Dubbel klik op de module om het deel venster **Eigenschappen** te openen.

3. Kies voor **resampling methode**de methode die is gebruikt voor het maken van de afzonderlijke structuren.  U kunt kiezen uit overbagging of replicatie.

    + **Overbaggen**: de functie voor het *verzamelen van de Boots trap* In deze methode wordt elke structuur op een nieuw voor beeld gekweekt, gemaakt door de oorspronkelijke gegevensset wille keurig te bemonsteren, totdat u een gegevensset hebt die de oorspronkelijke grootte heeft. De uitvoer van de modellen worden gecombineerd door middel van *stemmen*. Dit is een vorm van aggregatie. Zie de Wikipedia-vermelding voor Boots trap aggregatie voor meer informatie.

    + **Repliceren**: in replicatie wordt elke structuur getraind op exact dezelfde invoer gegevens. De bepaling van welk gesplitst predicaat voor elk structuur knooppunt wordt gebruikt, blijft wille keurig en maakt diverse structuren.

   

4. Geef op hoe u wilt dat het model wordt getraind door de optie **trainer modus maken** in te stellen.

    + **Eén para meter**: Selecteer deze optie als u weet hoe u het model wilt configureren en geef een set waarden als argumenten op.

    + **Parameter bereik**: Selecteer deze optie als u niet zeker weet wat de beste para meters zijn en u een parameter sweep wilt uitvoeren. Selecteer een bereik met waarden om over te sporen en het [model Hyper parameters](tune-model-hyperparameters.md) wordt herhaald op alle mogelijke combi Naties van de instellingen die u hebt verstrekt om de Hyper parameters te bepalen die de optimale resultaten opleveren.   

5. **Aantal beslissings structuren**: Typ het maximum aantal beslissings structuren dat in de ensemble kan worden gemaakt. Door meer beslissings structuren te maken, kunt u een betere dekking krijgen, maar kan de tijd van de training toenemen.

    Deze waarde bepaalt ook het aantal structuren dat wordt weer gegeven in de resultaten, wanneer het getrainde model wordt gevisualiseerd. Als u één boom structuur wilt zien of afdrukken, kunt u de waarde instellen op 1. Dit betekent echter wel dat er slechts één structuur kan worden geproduceerd (de boom structuur met de initiële set para meters), en er geen verdere herhalingen worden uitgevoerd.

6. **Maximale diepte van de beslissings structuren**: Typ een getal om de maximale diepte van een beslissings structuur te beperken. Het verg Roten van de diepte van de structuur kan nauw keuriger zijn, met het risico van een aantal verdeelde en verhoogde opleidings tijd.

7. **Aantal wille keurige splitsingen per knoop punt**: Typ het aantal splitpen dat moet worden gebruikt bij het maken van elk knoop punt van de structuur. Een *splitsing* houdt in dat onderdelen in elk niveau van de structuur (knoop punt) wille keurig worden gedeeld.

8. **Minimum aantal steek proeven per blad knooppunt**: geeft het minimum aantal cases aan dat nodig is voor het maken van een Terminal knooppunt (Leaf) in een boom structuur. Door deze waarde te verhogen, verhoogt u de drempel voor het maken van nieuwe regels.

    Met de standaard waarde 1 kan zelfs een enkele case ertoe leiden dat een nieuwe regel wordt gemaakt. Als u de waarde op 5 verhoogt, moeten de trainings gegevens ten minste vijf gevallen bevatten die voldoen aan dezelfde voor waarden.



10. Verbind een gegevensset met een label en een van de trainings modules:

    + Als u de **modus trainer maken** instelt op **één para meter**, gebruikt u de module [Train model](./train-model.md) .

11. Voer de pijplijn uit.



## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 