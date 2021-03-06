---
title: 'Zelf studie: een Playbook uitvoeren in azure Sentinel'
description: 'Zelf studie: in dit artikel wordt beschreven hoe u een Playbook uitvoert in azure Sentinel.'
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: e4afc5c8-ffad-4169-8b73-98d00155fa5a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2019
ms.author: yelevin
ms.openlocfilehash: f6adcb978dbe540d3bdd352089d4dde407d0fb4c
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77585081"
---
# <a name="tutorial-set-up-automated-threat-responses-in-azure-sentinel"></a>Zelf studie: automatische bedreigings reacties instellen in azure Sentinel



Deze zelf studie helpt u bij het gebruik van beveiligings-playbooks in azure Sentinel om automatische bedreigings reacties in te stellen op beveiligings problemen die worden gedetecteerd door Azure Sentinel.


> [!div class="checklist"]
> * Informatie over playbooks
> * Een Playbook maken
> * Een Playbook uitvoeren
> * Bedreigings reacties automatiseren


## <a name="what-is-a-security-playbook-in-azure-sentinel"></a>Wat is een beveiligings Playbook in azure Sentinel?

Een beveiligings Playbook is een verzameling procedures die vanuit Azure Sentinel kan worden uitgevoerd als reactie op een waarschuwing. Een beveiligings Playbook kan u helpen uw antwoord te automatiseren en te organiseren, en kan hand matig worden uitgevoerd of ingesteld om automatisch te worden uitgevoerd wanneer specifieke waarschuwingen worden geactiveerd. Beveiligings playbooks in azure Sentinel zijn gebaseerd op [Azure Logic apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps), wat betekent dat u alle Power, aanpassings mogelijkheden en ingebouwde sjablonen van Logic apps krijgt. Elke Playbook wordt gemaakt voor het specifieke abonnement dat u kiest, maar wanneer u de pagina Playbooks bekijkt, worden alle geselecteerde abonnementen weer geven.

> [!NOTE]
> Playbooks maakt gebruik van Azure Logic Apps, daarom zijn er kosten in rekening gebracht. Ga naar de pagina met prijzen voor [Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) voor meer informatie.

Als u zich bijvoorbeeld zorgen maakt over kwaad aardige aanvallers die toegang hebben tot uw netwerk bronnen, kunt u een waarschuwing instellen die zoekt naar schadelijke IP-adressen die toegang hebben tot uw netwerk. Vervolgens kunt u een Playbook maken die het volgende doet:
1. Wanneer de waarschuwing wordt geactiveerd, opent u een ticket in ServiceNow of een ander IT-ticket systeem.
2. Stuur een bericht naar uw beveiligings kanaal in micro soft teams of een toegestane vertraging om te controleren of uw beveiligings analisten op de hoogte zijn van het incident.
3. Stuur alle informatie in de waarschuwing naar uw senior netwerk beheerder en beveiligings beheerder. Het e-mail bericht bevat ook twee gebruikers keuze rondjes **blok keren** of **negeren**.
4. De Playbook blijft actief nadat een antwoord is ontvangen van de beheerders.
5. Als de beheerders **blok keren**kiezen, wordt het IP-adres in de firewall geblokkeerd en wordt de gebruiker uitgeschakeld in azure AD.
6. Als de beheerders de optie **negeren**kiezen, wordt de waarschuwing gesloten in azure Sentinel en wordt het incident gesloten in ServiceNow.

Beveiligings-playbooks kunnen hand matig of automatisch worden uitgevoerd. Als u deze hand matig uitvoert, kunt u ervoor kiezen om een Playbook op aanvraag uit te voeren als reactie op de geselecteerde waarschuwing. Als u deze uitvoert, betekent dit dat u tijdens het ontwerpen van de correlatie regel automatisch een of meer playbooks wilt uitvoeren wanneer de waarschuwing wordt geactiveerd.


## <a name="create-a-security-playbook"></a>Een beveiligings Playbook maken

Volg deze stappen voor het maken van een nieuwe beveiligings Playbook in azure Sentinel:

1. Open het **Azure Sentinel** -dash board.
2. Onder **beheer**selecteert u **Playbooks**.

   ![Logische apps](./media/tutorial-respond-threats-playbook/playbookimg.png)

3. Klik op de pagina **Sentinel-Playbooks van Azure** op de knop **toevoegen** .

   ![Logische app maken](./media/tutorial-respond-threats-playbook/create-playbook.png) 

4. Typ op de pagina **logische app maken** de aangevraagde informatie voor het maken van uw nieuwe logische app en klik op **maken**. 

5. Selecteer de sjabloon die u wilt gebruiken in de [**ontwerp functie voor logische apps**](../logic-apps/logic-apps-overview.md). Als u een sjabloon selecteert die referenties vereist, moet u deze opgeven. U kunt ook een volledig nieuwe, lege Playbook maken. Selecteer **lege logische app**. 

   ![Ontwerper van logische app](./media/tutorial-respond-threats-playbook/playbook-template.png)

6. U wordt naar de ontwerp functie voor logische apps geleid, waar u een nieuwe sjabloon kunt maken of bewerken. Voor meer informatie over het maken van een Playbook met [Logic apps](../logic-apps/logic-apps-create-logic-apps-from-templates.md).

7. Als u een lege Playbook maakt, typt u in het veld **alle connectors en triggers doorzoeken** de tekst *Azure Sentinel*en selecteert **u wanneer een reactie op een Azure-Sentinel waarschuwing wordt geactiveerd**. <br>Nadat deze is gemaakt, wordt de nieuwe Playbook weer gegeven in de lijst **Playbooks** . Als deze niet wordt weer gegeven, klikt u op **vernieuwen**.

1. Gebruik de functies **Get entities** , waarmee u de relevante entiteiten kunt ophalen in de lijst **entiteiten** , zoals accounts, IP-adressen en hosts. Hiermee kunt u acties uitvoeren voor specifieke entiteiten.

7. Nu kunt u bepalen wat er moet gebeuren wanneer de playbook wordt geactiveerd. U kunt een actie, een logische voor waarde, Case voorwaarden of lussen toevoegen.

   ![Ontwerper van logische app](./media/tutorial-respond-threats-playbook/logic-app.png)

## <a name="how-to-run-a-security-playbook"></a>Een beveiligings Playbook uitvoeren

U kunt op aanvraag een Playbook uitvoeren.

Een Playbook op aanvraag uitvoeren:

1. Selecteer op de pagina **incidenten** een incident en klik op **volledige details weer geven**.

2. Klik op het tabblad **waarschuwingen** op de waarschuwing waarmee u de Playbook wilt uitvoeren en schuif helemaal naar rechts en klik op **playbooks weer geven** en selecteer een Playbook om uit te **voeren** in de lijst met beschik bare playbooks van het abonnement. 



## <a name="automate-threat-responses"></a>Bedreigings reacties automatiseren

SIEM/SOC-teams kunnen regel matig worden overspoeld met beveiligings waarschuwingen. Het gegenereerde volume van waarschuwingen is zo zeer groot dat beschik bare beveiligings beheerders worden overspoeld. Dit resulteert al te vaak in situaties waarin veel waarschuwingen niet kunnen worden onderzocht, waardoor de organisatie kwetsbaar is voor aanvallen die niet meer worden opgemerkt. 

Veel, indien niet de meeste, van deze waarschuwingen voldoen aan terugkerende patronen die kunnen worden aangepakt door specifieke en gedefinieerde herstel acties. Met Azure Sentinel kunt u al uw herbemiddeling definiëren in playbooks. Het is ook mogelijk om realtime-automatisering in te stellen als onderdeel van de Playbook-definitie zodat u een bepaald antwoord op bepaalde beveiligings waarschuwingen volledig kunt automatiseren. Met behulp van realtime-automatisering kunnen antwoord teams hun werk belasting aanzienlijk verminderen door de routine reacties op terugkerende waarschuwingen volledig te automatiseren, waardoor u meer kunt concentreren op unieke waarschuwingen, patronen kunt analyseren, de jacht kunt automatiseren en meer.

Voor het automatiseren van antwoorden:

1. Selecteer de waarschuwing waarvoor u het antwoord wilt automatiseren.
1. Kies op de pagina **waarschuwings regel bewerken** onder **realtime automatisering**de **geactiveerde Playbook** die u wilt uitvoeren wanneer deze waarschuwings regel overeenkomt.
1. Selecteer **Opslaan**.

   ![realtime-automatisering](./media/tutorial-detect-threats/rt-configuration.png)






## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u een Playbook in azure Sentinel uitvoert. Ga door met het [proactief zoeken naar bedreigingen](hunting.md) met Azure Sentinel.


