---
title: Een lab maken in Azure DevTest Labs | Microsoft Docs
description: In dit artikel wordt stapsgewijs uitgelegd hoe u een Lab maakt met behulp van de Azure Portal en Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 8b6d3e70-6528-42a4-a2ef-449575d0f928
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 5cd675823b85e975dcb8dfe152c27b2d30463c1c
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759734"
---
# <a name="create-a-lab-in-azure-devtest-labs"></a>Een lab maken in Azure DevTest Labs
Een lab in Azure DevTest Labs is de infrastructuur die een groep resources omvat, zoals VM's (virtuele machines), waarmee u deze resources beter kunt beheren door limieten en quota op te geven. In dit artikel wordt uitgelegd hoe u een lab maakt met behulp van Azure Portal.

## <a name="prerequisites"></a>Vereisten
Als u een lab wilt maken, hebt u het volgende nodig:

* Een Azure-abonnement. Zie [Azure aanschaffen](https://azure.microsoft.com/pricing/purchase-options/) of [Gratis proefversie van één maand](https://azure.microsoft.com/pricing/free-trial/) voor meer informatie over Azure-aankoopopties. U moet de eigenaar van het abonnement zijn om het lab te maken.

## <a name="steps-to-create-a-lab-in-azure-devtest-labs"></a>Stappen voor het maken van een lab in Azure DevTest Labs
In de volgende stappen ziet u hoe u Azure Portal kunt gebruiken om een lab te maken in Azure DevTest Labs. 

1. Meld u aan bij de [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecteer in het hoofdmenu aan de linkerkant **Alle services** (boven aan de lijst). Selecteer * (Star) naast **DevTest Labs** in de sectie **DEVOPS** . Met deze actie worden **DevTest Labs** toegevoegd aan het navigatie menu aan de linkerkant, zodat u de volgende keer snel toegang kunt krijgen. 

    ![Alle services: Selecteer DevTest Labs](./media/devtest-lab-create-lab/all-services-select.png)
2. Selecteer nu **DevTest Labs** in het navigatie menu links. Selecteer **toevoegen** op de werk balk. 
   
    ![Een lab toevoegen](./media/devtest-lab-create-lab/add-lab-button.png)
1. Voer op de pagina **een DevTest-Lab maken** de volgende acties uit: 
    1. Voer een **naam** in voor het lab.
    2. Selecteer het **abonnement** dat u wilt koppelen aan het lab.
    3. Voer een **naam in voor de resource groep** voor het lab. 
    4. Selecteer een **locatie** waar u het Lab wilt opslaan.
    4. Selecteer **Auto-shutdown** om op te geven of u het automatisch afsluiten van alle virtuele machines van het lab wilt inschakelen en de parameters voor deze machines wilt definiëren. De functie Auto-shutdown is voornamelijk een kostenbesparende functie waar u kunt opgeven wanneer de VM automatisch moet worden afgesloten. Nadat u een lab hebt gemaakt, kunt u de Auto-shutdown-instellingen wijzigen door de stappen te volgen in het artikel [Alle beleidsregels beheren voor een lab in Azure DevTest Labs](./devtest-lab-set-lab-policy.md#set-auto-shutdown).
    1. Voer informatie over **Naam** en **Waarde** in voor **Tags** als u aangepaste tags wilt maken die worden toegevoegd aan elke resource die u in het lab maakt. Tags zijn handig om labresources te beheren en te organiseren per categorie. Zie [Tags toevoegen aan een lab](devtest-lab-add-tag.md) voor meer informatie over tags, inclusief het toevoegen van tags na het maken van het lab.
    6. Selecteer **Opties voor Automation** als u Azure Resource Manager-sjablonen wilt ophalen voor automatisering van de configuratie. 
    7. Selecteer **Maken**. U kunt de vorderingen bij het maken van het lab bijhouden in de sectie **Meldingen**. 
    
        ![De sectie Een lab maken van DevTest Labs](./media/devtest-lab-create-lab/create-devtestlab-blade.png)
    8. Als u klaar bent, selecteert u in de melding **naar resource** . U kunt ook de pagina **DevTest Labs** vernieuwen om de zojuist gemaakte Lab weer te geven in de lijst met Labs.  Selecteer de test omgeving in de lijst. U ziet de start pagina voor uw Lab. 

        ![Start pagina voor het lab](./media/devtest-lab-create-lab/lab-home-page.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Volgende stappen
Wanneer u uw lab hebt gemaakt, kunt u onder andere de volgende stappen uitvoeren:

* [Toegang tot een lab beveiligen](devtest-lab-add-devtest-user.md)
* [Labbeleidsregels instellen](devtest-lab-set-lab-policy.md)
* [Een labsjabloon maken](devtest-lab-create-template.md)
* [Aangepaste artefacten maken voor uw virtuele machines](devtest-lab-artifact-author.md)
* [Een virtuele machine aan een lab toevoegen](devtest-lab-add-vm.md)

