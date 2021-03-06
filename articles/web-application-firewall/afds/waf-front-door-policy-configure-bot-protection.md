---
title: Bot-beveiliging voor Web Application firewall configureren met Azure front-deur (preview-versie)
description: Meer informatie over Web Application firewall (WAF).
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: f48b683044bc727cda461fb64a743c055188962a
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76934657"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>Bot-beveiliging voor Web Application firewall configureren (preview-versie)
In dit artikel wordt beschreven hoe u de bot-beveiligings regel configureert in azure Web Application firewall (WAF) voor de voor deur met behulp van Azure Portal. De bot-beveiligings regel kan ook worden geconfigureerd met een CLI-, Azure PowerShell-of Azure Resource Manager-sjabloon.

> [!IMPORTANT]
> De set bot Protection-regelset bevindt zich momenteel in een open bare preview en wordt weer gegeven met een preview-service level agreement. De reden hiervoor is dat bepaalde functies mogelijk niet worden ondersteund of beperkte mogelijkheden hebben.  Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Vereisten

Maak een basis WAF-beleid voor de voor deur door de instructies te volgen die worden beschreven in [een WAF-beleid maken voor Azure front deur met behulp van de Azure Portal](waf-front-door-create-portal.md).

## <a name="enable-bot-protection-rule-set"></a>Regel set voor bot-beveiliging inschakelen

Selecteer op de pagina **beheerde regels** bij het maken van een firewall beleid voor webtoepassingen eerst de sectie **beheerde regel sets** zoeken, schakel het selectie vakje in vóór de regel **Microsoft_BotManager_1 dpm\dpm\protectionagents\ra\3.0.** in de vervolg keuzelijst en selecteer vervolgens **controleren + maken**.

   ![Bot-beveiligings regel](.././media/waf-front-door-configure-bot-protection/botmanager112019.png)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [bewaken van WAF](waf-front-door-monitor.md).
