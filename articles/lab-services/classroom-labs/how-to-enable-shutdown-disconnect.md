---
title: Het afsluiten van Vm's inschakelen bij het verbreken van Azure Lab Services | Microsoft Docs
description: Meer informatie over het in-of uitschakelen van automatisch afsluiten van Vm's wanneer een verbinding met een extern bureau blad wordt verbroken.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/13/2019
ms.author: spelluru
ms.openlocfilehash: 68a27a325a0ef02c6eeea9867a21ba0e24ab5321
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77117128"
---
# <a name="enable-automatic-shutdown-of-vms-on-disconnect"></a>Automatisch afsluiten van Vm's inschakelen bij het verbreken van de verbinding
In dit artikel wordt beschreven hoe u automatisch afsluiten van **Windows 10** Lab-vm's (sjabloon of student) kunt in-of uitschakelen nadat een verbinding met een extern bureau blad is verbroken. U kunt ook opgeven hoe lang de Vm's moeten wachten totdat de gebruiker opnieuw verbinding maakt voordat deze automatisch wordt afgesloten.

Een Lab-account beheerder kan deze instelling configureren voor het lab-account waarin u Labs maakt. Zie voor meer informatie [automatisch afsluiten van Vm's configureren bij het verbreken van de verbinding met een Lab-account](how-to-configure-lab-accounts.md#automatic-shutdown-of-vms-on-disconnect). Als eigenaar van het lab kunt u de instelling onderdrukken wanneer u een Lab maakt of nadat het lab is gemaakt. 

## <a name="configure-when-creating-a-lab"></a>Configureren bij het maken van een Lab
Op de pagina stap 3 van de wizard Lab maken kunt u deze functie in-of uitschakelen en opgeven hoe lang de virtuele machine moet wachten totdat de gebruiker opnieuw verbinding maakt voordat deze automatisch wordt afgesloten. 

![Configureren op het moment dat het lab wordt gemaakt](../media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

## <a name="configure-after-the-lab-is-created"></a>Configureren nadat het lab is gemaakt
U kunt deze instelling configureren op de pagina **instellingen** , zoals wordt weer gegeven in de volgende afbeelding: 

![Configureren nadat het lab is gemaakt](../media/how-to-enable-shutdown-disconnect/configure-lab-automatic-shutdown.png)

> [!WARNING]
> Als u het Windows-besturings systeem (OS) op een virtuele machine afsluit voordat u een RDP-sessie met de virtuele machine verbreekt, werkt de functie automatisch afsluiten niet goed.  

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- [Dash board voor klassikale Labs](use-dashboard.md)