---
title: Nauw keurigheid van patronen toevoegen-LUIS
titleSuffix: Azure Cognitive Services
description: Voeg patroon sjablonen toe om de nauw keurigheid van de voor spellingen te verbeteren in Language Understanding-toepassingen (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: diberry
ms.openlocfilehash: 21afb12bf2464218119ebf52ebd980745e3d731d
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76311713"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Patronen toevoegen om de nauw keurigheid van de voor spelling te verbeteren
Nadat een LUIS-app eind punt uitingen ontvangt, gebruikt u een [patroon](luis-concept-patterns.md) om de nauw keurigheid van de voor spelling te verbeteren voor uitingen die een patroon onthult in de woord volgorde en woord keuze. Patronen gebruiken specifieke [syntaxis](luis-concept-patterns.md#pattern-syntax) om de locatie van: [entiteiten](luis-concept-entity-types.md), entiteits [rollen](luis-concept-roles.md)en optionele tekst aan te geven.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

> [!CAUTION]
> Patronen bestaan alleen uit door machines geleerde entiteits-Parents, geen subonderdelen.

## <a name="adding-example-utterances-as-pattern"></a>Voor beeld van uitingen als patroon toevoegen

Als u een patroon wilt toevoegen voor een entiteit, is de _eenvoudigste_ manier om het patroon te maken op de pagina Details van intentie. Dit zorgt ervoor dat uw syntaxis overeenkomt met het voor beeld utterance.

1. Selecteer de app op de pagina **mijn apps** in de [Preview-portal van Luis](https://preview.luis.ai).
1. Selecteer op de pagina lijst met **doel** stellingen de doel naam van het voor beeld utterance u een sjabloon utterance wilt maken.
1. Selecteer op de pagina Details van intentie de rij voor het voor beeld-utterance dat u wilt gebruiken als de sjabloon utterance en selecteer **+ toevoegen als patroon** in de context werkbalk.

    > [!div class="mx-imgBorder"]
    > ![scherm opname van het selecteren van voor beeld utterance als sjabloon patroon op de detail pagina Details.](./media/luis-how-to-model-intent-pattern/add-example-utterances-as-pattern-template-utterance-from-intent-detail-page.png)

1. Selecteer in het pop-upvenster **gereed** op de pagina **patronen bevestigen** . U hoeft de onderdelen, beperkingen of descriptors van de entiteiten niet te definiëren. U hoeft alleen de door de machine geleerde entiteit weer te geven.

    > [!div class="mx-imgBorder"]
    > ![scherm afbeelding van het bevestigen van voor beeld utterance als sjabloon patroon op de detail pagina Details.](./media/luis-how-to-model-intent-pattern/confirm-patterns-from-example-utterance-intent-detail-page.png)

1. Als u de sjabloon wilt bewerken, zoals het selecteren van tekst als optioneel, met behulp van de vier Kante haken `[]`, moet u deze bewerking op de pagina **patronen** maken.

1. Selecteer in de navigatie balk **Train** om de app te trainen met het nieuwe patroon.

## <a name="add-template-utterance-using-correct-syntax"></a>Sjabloon utterance toevoegen met de juiste syntaxis

1. Open uw app door het selecteren van de naam ervan op **mijn Apps** pagina en selecteer vervolgens **patronen** in het linkerdeelvenster onder **verbeterde app-prestaties**.

    > [!div class="mx-imgBorder"]
    > Scherm afbeelding van de lijst met patronen ![](./media/luis-how-to-model-intent-pattern/patterns-1.png)

1. Selecteer het juiste type voor het patroon.

1. In het tekstvak sjabloon, typt u de sjabloon utterance en selecteer Enter. Als u de naam van de entiteit opgeven wilt, gebruikt u de juiste patroon entiteit-syntaxis. Beginnen met de syntaxis van de entiteit met `{`. De lijst met entiteiten weergegeven. Selecteer de juiste entiteit.

    > [!div class="mx-imgBorder"]
    > Scherm afbeelding van de entiteit voor het patroon](./media/luis-how-to-model-intent-pattern/patterns-3.png) ![

    Als uw entiteit een [rol](luis-concept-roles.md)bevat, geeft u de rol op met één dubbele punt, `:`na de naam van de entiteit, zoals `{Location:Origin}`. De lijst met functies voor de entiteiten in een lijst weergegeven. Selecteer de rol en selecteer vervolgens Enter.

    > [!div class="mx-imgBorder"]
    > Scherm opname van entiteit ![met rol](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    Nadat u de juiste entiteit hebt geselecteerd, klaar bent met het invoeren van het patroon en selecteer vervolgens Enter. Wanneer u klaar bent u patronen [trainen](luis-how-to-train.md) uw app.

    > [!div class="mx-imgBorder"]
    > ![scherm opname van het ingevoerde patroon met beide typen entiteiten](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="train-your-app-after-changing-model-with-patterns"></a>Uw app trainen na het wijzigen van model met patronen
Nadat u toevoegen, bewerken, verwijderen of opnieuw toewijzen van een patroon [trainen](luis-how-to-train.md) en [publiceren](luis-how-to-publish-app.md) uw app voor uw wijzigingen zijn doorgevoerd in eindpunt query's.

<a name="search-patterns"></a>
<a name="edit-a-pattern"></a>
<a name="reassign-individual-pattern-to-different-intent"></a>
<a name="reassign-several-patterns-to-different-intent"></a>
<a name="delete-a-single-pattern"></a>
<a name="delete-several-patterns"></a>
<a name="filter-pattern-list-by-entity"></a>
<a name="filter-pattern-list-by-intent"></a>
<a name="remove-entity-or-intent-filter"></a>
<a name="add-pattern-from-existing-utterance-on-intent-or-entity-page"></a>

## <a name="use-contextual-toolbar"></a>Contextuele werk balk gebruiken

Met de contextuele werk balk boven de lijst met patronen kunt u het volgende doen:

* Patronen zoeken
* Een patroon bewerken
* Opnieuw toewijzen van afzonderlijke patroon naar een ander doel
* Opnieuw toewijzen van verschillende patronen naar een ander doel
* Delete-a-enkel patroon
* Verschillende patronen verwijderen
* Patroon lijst filteren door entiteit
* Filter-pattern-list-by-intentie
* Entiteit of intentie filter verwijderen
* Patroon van bestaande utterance op de pagina kunt u lezen wat of entiteit toevoegen

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [bouwen van een patroon](luis-tutorial-pattern.md) met een patroon. alle en rollen met een zelf studie.
* Meer informatie over het [trainen](luis-how-to-train.md) uw app.
