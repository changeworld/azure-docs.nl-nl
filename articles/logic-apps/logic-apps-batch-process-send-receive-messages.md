---
title: Berichten in batch verwerken als een groep
description: Berichten in groepen tussen uw werk stromen verzenden en ontvangen met batch verwerking in Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: e48d2bb2ffce0dd4f9293417534165165d426784
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666751"
---
# <a name="send-receive-and-batch-process-messages-in-azure-logic-apps"></a>Berichten verzenden, ontvangen en verwerken in Azure Logic Apps

Als u berichten wilt verzenden en verwerken op een specifieke manier als groepen, kunt u een batch-oplossing maken waarmee berichten worden verzameld in een *batch* totdat aan de opgegeven criteria wordt voldaan voor het vrijgeven en verwerken van de batch berichten. Batch verwerking kan verminderen hoe vaak de logische app berichten verwerkt. In dit artikel wordt beschreven hoe u een batch oplossing bouwt door twee Logic apps te maken binnen hetzelfde Azure-abonnement, een Azure-regio en deze specifieke volg orde te volgen: 

* De logische [receiver](#batch-receiver) -Logic-app, waarmee berichten in een batch worden geaccepteerd en verzameld, totdat aan de opgegeven criteria wordt voldaan voor het vrijgeven en verwerken van die berichten.

  Zorg ervoor dat u eerst de batch-ontvanger maakt, zodat u later de batch bestemming kunt selecteren wanneer u de batch-afzender maakt.

* Een of meer Logic [' batch Sender '](#batch-sender) logische apps, die de berichten verzenden naar de eerder gemaakte batch-ontvanger. 

   U kunt ook een unieke sleutel opgeven, zoals een klant nummer, waarmee de doel batch wordt *gepartitioneerd* of onderverdeeld in logische subsets op basis van die sleutel. Op die manier kan de receiver-app alle items met dezelfde sleutel verzamelen en ze samen verwerken.

Zorg ervoor dat uw batch-ontvanger en batch-afzender hetzelfde Azure-abonnement *en* de Azure-regio delen. Als dat niet het geval is, kunt u de batch-ontvanger niet selecteren wanneer u de batch-afzender maakt, omdat deze niet zichtbaar zijn voor elkaar.

## <a name="prerequisites"></a>Vereisten

Als u dit voor beeld wilt volgen, hebt u de volgende items nodig:

* Een Azure-abonnement. Als u geen abonnement hebt, kunt u [beginnen met een gratis Azure-account](https://azure.microsoft.com/free/). U kunt [zich ook registreren voor een abonnement met betalen per gebruik](https://azure.microsoft.com/pricing/purchase-options/).

* Een e-mail account met een [e-mail provider die door Azure Logic apps wordt ondersteund](../connectors/apis-list.md)

* Basis kennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md) 

* Als u Visual Studio wilt gebruiken in plaats van de Azure Portal, moet u [Visual Studio instellen voor het werken met Logic apps](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="batch-receiver"></a>

## <a name="create-batch-receiver"></a>Batch-ontvanger maken

Voordat u berichten naar een batch kunt verzenden, moet deze batch eerst bestaan als de bestemming waar u deze berichten verzendt. Eerst moet u de Logic-app ' batch-ontvanger ' maken, die begint met de **batch** trigger. Op die manier kunt u tijdens het maken van de logische app Sender de logische app voor de batch-ontvanger selecteren. De batch-ontvanger gaat verder met het verzamelen van berichten totdat aan de opgegeven criteria wordt voldaan om deze berichten te vrijgeven en te verwerken. Terwijl batch-ontvangers niets hoeven te weten over batch afzenders, moeten batch afzenders de bestemming weten waar ze de berichten verzenden. 

1. Maak in de [Azure Portal](https://portal.azure.com) of Visual Studio een logische app met deze naam: "BatchReceiver" 

2. Voeg in Logic Apps Designer de **batch** trigger toe, waarmee de werk stroom van de logische app wordt gestart. Voer in het zoekvak ' batch ' in als uw filter. Selecteer deze trigger: **batch-berichten**

   ![Trigger voor batch berichten toevoegen](./media/logic-apps-batch-process-send-receive-messages/add-batch-receiver-trigger.png)

3. Stel deze eigenschappen in voor de batch-ontvanger: 

   | Eigenschap | Beschrijving | 
   |----------|-------------|
   | **Batch modus** | **inline**- : voor het definiëren van release criteria binnen de batch trigger <br>- **integratie account**: voor het definiëren van configuraties met meerdere release criteria via een [integratie account](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md). Met een integratie account kunt u deze configuraties op één plek behouden in plaats van in afzonderlijke logische apps. | 
   | **Batch naam** | De naam voor uw batch, die in dit voor beeld ' TestBatch ' is, en geldt alleen voor de **inline** batch modus |  
   | **Release criteria** | Is alleen van toepassing op de **inline** batch modus en selecteert de criteria om te voldoen aan de verwerking van elke batch: <p>- **telling op basis van berichten**: de batch vrijgeven op basis van het aantal berichten dat door de batch is verzameld. <br>**op basis**van - grootte: de batch vrijgeven op basis van de totale grootte in bytes voor alle berichten die door die batch worden verzameld. <br>- **schema**: de batch vrijgeven op basis van een terugkeer schema, waarmee een interval en frequentie worden opgegeven. In de geavanceerde opties kunt u ook een tijd zone selecteren en een begin datum en-tijd opgeven. <br>- **Alles selecteren**: gebruik alle opgegeven criteria. | 
   | **Aantal berichten** | Het aantal berichten dat in de batch moet worden verzameld, bijvoorbeeld 10 berichten. De limiet voor een batch is 8.000 berichten. | 
   | **Batch grootte** | De totale grootte in bytes die in de batch moet worden verzameld, bijvoorbeeld 10 MB. De maximale grootte van een batch is 80 MB. | 
   | **Planning** | Het interval en de frequentie tussen batch releases, bijvoorbeeld 10 minuten. De minimale terugkeer patroon is 60 seconden of 1 minuut. Gedeeltelijke minuten worden effectief afgerond tot 1 minuut. Kies **Geavanceerde opties weer geven**om een tijd zone of een start datum en-tijd op te geven. | 
   ||| 

   > [!NOTE]
   > 
   > Als u de release criteria wijzigt terwijl de trigger nog steeds een batch heeft, maar niet-verzonden berichten, gebruikt de trigger de bijgewerkte release criteria voor het verwerken van de niet-verzonden berichten. 

   In dit voor beeld worden alle criteria weer gegeven, maar voor uw eigen testen voert u slechts één criterium uit:

   ![Details van batch trigger opgeven](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-criteria.png)

4. Voeg nu een of meer acties toe die elke batch verwerken. 

   Voor dit voor beeld voegt u een actie toe waarmee een e-mail bericht wordt verzonden wanneer de batch trigger wordt geactiveerd. 
   De trigger wordt uitgevoerd en verzendt een e-mail bericht wanneer de batch 10 berichten bevat, 10 MB of een Pass-Through 10 minuten duurt.

   1. Kies **nieuwe stap**onder de batch trigger.

   2. Voer 'e-mail verzenden' als filter in het zoekvak in.
   Selecteer een e-mail connector op basis van uw e-mail provider.

      Als u bijvoorbeeld een persoonlijk account hebt, zoals @outlook.com of @hotmail.com, selecteert u de Outlook.com-connector. 
      Als u een Gmail-account hebt, selecteert u de Gmail-connector. 
      In dit voor beeld wordt Office 365 Outlook gebruikt. 

   3. Selecteer deze actie: e-mail ***provider* voor e-mail < verzenden>**

      Bijvoorbeeld:

      ![Selecteer een actie voor het verzenden van een e-mail voor uw e-mail provider](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-send-email-action.png)

5. Meld u aan bij uw e-mailaccount als dat wordt gevraagd. 

6. Stel de eigenschappen in voor de actie die u hebt toegevoegd.

   * Voer het e-mailadres van de ontvanger in het vak **Aan** in. 
   Voor testdoeleinden kunt u uw eigen e-mailadres gebruiken.

   * Selecteer in het vak **onderwerp** het veld **partitie naam** wanneer de lijst met dynamische inhoud wordt weer gegeven.

     ![Selecteer in de lijst met dynamische inhoud ' partitie naam '](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details.png)

     U kunt later in de batch-verzender een unieke partitie sleutel opgeven waarmee de doel batch wordt onderverdeeld in logische subsets waar u berichten kunt verzenden. 
     Elke set heeft een uniek nummer dat wordt gegenereerd door de logische app voor het verzenden van batch-verwerkingen. 
     Met deze mogelijkheid kunt u één batch met meerdere subsets gebruiken en elke subset definiëren met de naam die u opgeeft.

     > [!IMPORTANT]
     > Een partitie heeft een limiet van 5.000 berichten of 80 MB. Als aan een van beide voor waarden wordt voldaan, wordt de batch door Logic Apps mogelijk vrijgegeven, zelfs als niet aan uw gedefinieerde release voorwaarde wordt voldaan.

   * Selecteer in het vak **hoofd tekst** het veld **bericht-id** wanneer de lijst met dynamische inhoud wordt weer gegeven. 

     De Logic Apps Designer voegt automatisch een lus ' voor elke ' toe om de actie e-mail verzenden uit te voeren, omdat die actie de uitvoer van de vorige actie als een verzameling behandelt, in plaats van een batch. 

     ![Selecteer bericht-id bij ' hoofd tekst '](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details-for-each.png)

7.  Sla uw logische app op. U hebt nu een batch-ontvanger gemaakt.

    ![Uw logische app opslaan](./media/logic-apps-batch-process-send-receive-messages/save-batch-receiver-logic-app.png)

8. Als u Visual Studio gebruikt, zorg er dan voor dat u [uw logische app-ontvanger logica implementeert in azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure). Anders kunt u de batch-ontvanger niet selecteren wanneer u de batch-afzender maakt.

<a name="batch-sender"></a>

## <a name="create-batch-sender"></a>Batch-afzender maken

Maak nu een of meer logische apps voor batch Sender die berichten verzenden naar de logische app-ontvanger. In elke batch-verzender geeft u de batch-ontvanger en de batch naam, de bericht inhoud en andere instellingen op. U kunt eventueel een unieke partitie sleutel opgeven om de batch te verdelen in logische subsets voor het verzamelen van berichten met die sleutel. 

* Zorg ervoor dat u [uw batch-ontvanger](#batch-receiver) al hebt gemaakt, dus wanneer u uw batch-afzender maakt, kunt u de bestaande batch-ontvanger als de doel batch selecteren. Terwijl batch-ontvangers niets hoeven te weten over batch afzenders, moeten batch afzenders weten waar ze berichten moeten verzenden. 

* Zorg ervoor dat uw batch-ontvanger en batch-Sender dezelfde Azure-regio *en* hetzelfde Azure-abonnement delen. Als dat niet het geval is, kunt u de batch-ontvanger niet selecteren wanneer u de batch-afzender maakt, omdat deze niet zichtbaar zijn voor elkaar.

1. Maak een andere logische app met deze naam: "BatchSender"

   1. Voer in het zoekvak ' recurrence ' in als uw filter. 
   Selecteer deze trigger: **recurrence-schema**

      ![De trigger ' recurrence-Schedule ' toevoegen](./media/logic-apps-batch-process-send-receive-messages/add-schedule-trigger-batch-sender.png)

   2. Stel de frequentie en het interval in om elke minuut de logische app van de afzender uit te voeren.

      ![Stel de frequentie en het interval voor de trigger voor terugkeer patroon in](./media/logic-apps-batch-process-send-receive-messages/recurrence-trigger-batch-sender-details.png)

2. Voeg een nieuwe actie toe voor het verzenden van berichten naar een batch.

   1. Kies **nieuwe stap**onder de trigger voor terugkeer patroon.

   2. Voer in het zoekvak ' batch ' in als uw filter. 
   Selecteer de lijst **acties** en selecteer deze actie: **een Logic apps werk stroom kiezen met batch trigger: berichten verzenden naar batch**

      ![Selecteer een Logic Apps werk stroom kiezen met batch trigger](./media/logic-apps-batch-process-send-receive-messages/send-messages-batch-action.png)

   3. Selecteer de logische app-ontvanger die u eerder hebt gemaakt.

      ![De logische app voor de batch-ontvanger selecteren](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch-receiver.png)

      > [!NOTE]
      > De lijst bevat ook alle andere logische apps die batch triggers hebben. 
      > 
      > Als u Visual Studio gebruikt en er geen batch-ontvangers worden weer gegeven, controleert u of u uw batch-ontvanger hebt geïmplementeerd in Azure. Als u dat nog niet hebt gedaan, kunt u leren hoe u [de logische app voor uw batch-ontvanger naar Azure implementeert](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure). 

   4. Selecteer deze actie: **Batch_messages-<*uw-batch-ontvanger*>**

      ![Selecteer deze actie: ' Batch_messages-< uw-Logic-app > '](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch.png)

3. De eigenschappen van de batch Sender instellen:

   | Eigenschap | Beschrijving | 
   |----------|-------------| 
   | **Batch naam** | De batch naam die is gedefinieerd door de logische app voor de ontvanger (TestBatch) in dit voor beeld <p>**Belang rijk**: de batch naam wordt gevalideerd tijdens runtime en moet overeenkomen met de naam die is opgegeven door de logische app van de ontvanger. Als u de batch naam wijzigt, mislukt de batch Sender. | 
   | **Bericht inhoud** | De inhoud van het bericht dat u wilt verzenden | 
   ||| 

   Voor dit voor beeld voegt u deze expressie toe, waarmee de huidige datum en tijd worden ingevoegd in de bericht inhoud die u naar de batch verzendt:

   1. Klik in het vak **bericht inhoud** . 

   2. Wanneer de lijst met dynamische inhoud wordt weer gegeven, kiest u **expressie**. 

   3. Voer de expressie in `utcnow()`en kies vervolgens **OK**. 

      ![Kies in ' bericht inhoud ' expressie ', voer ' utcnow () ' in en kies OK.](./media/logic-apps-batch-process-send-receive-messages/batch-sender-details.png)

4. Stel nu een partitie in voor de batch. Kies in de actie BatchReceiver de optie **Geavanceerde opties weer geven** en stel deze eigenschappen in:

   | Eigenschap | Beschrijving | 
   |----------|-------------| 
   | **Partitie naam** | Een optionele, unieke partitie sleutel die moet worden gebruikt voor het delen van de doel batch in logische subsets en het verzamelen van berichten op basis van die sleutel | 
   | **Bericht-id** | Een optionele bericht-id die een gegenereerde Globally Unique Identifier (GUID) is wanneer deze leeg is | 
   ||| 

   Voor dit voor beeld voegt u in het vak **partitie naam** een expressie toe waarmee een wille keurig getal tussen een en vijf wordt gegenereerd. Laat het vak **bericht-id** leeg.
   
   1. Klik in het vak **partitie naam** zodat de lijst met dynamische inhoud wordt weer gegeven. 

   2. Kies in de lijst met dynamische inhoud voor **Expressie**.
   
   3. Voer de expressie in `rand(1,6)`en kies vervolgens **OK**.

      ![Een partitie voor de doel batch instellen](./media/logic-apps-batch-process-send-receive-messages/batch-sender-partition-advanced-options.png)

      Met deze functie **ASELECT** wordt een getal tussen een en vijf gegenereerd. 
      U splitst deze batch dus in vijf genummerde partities, die met deze expressie dynamisch worden ingesteld.

5. Sla uw logische app op. De logische app voor de afzender ziet er nu uit als in dit voor beeld:

   ![De logische app voor uw afzender opslaan](./media/logic-apps-batch-process-send-receive-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>Uw logische Apps testen

Als u de oplossing voor batch verwerking wilt testen, moet u de logische apps enkele minuten actief blijven. Binnenkort begint u met het ophalen van e-mail berichten in groepen van vijf, allemaal met dezelfde partitie sleutel.

Uw batch Sender Logic app wordt elke minuut uitgevoerd, genereert een wille keurig getal tussen één en vijf, en gebruikt dit gegenereerde getal als de partitie sleutel voor de doel batch waarnaar berichten worden verzonden. Telkens wanneer de batch vijf items met dezelfde partitie sleutel bevat, wordt de logische app-ontvanger geactiveerd en wordt e-mail verzonden voor elk bericht.

> [!IMPORTANT]
> Wanneer u klaar bent met testen, moet u ervoor zorgen dat u de BatchSender Logic-app uitschakelt om het verzenden van berichten te stoppen en te voor komen dat uw postvak in overbelasting.

## <a name="next-steps"></a>Volgende stappen

* [Batch en EDI-berichten verzenden](../logic-apps/logic-apps-scenario-edi-send-batch-messages.md)
* [Op logische app-definities bouwen met behulp van JSON](../logic-apps/logic-apps-author-definitions.md)
* [Een serverloze app maken in Visual Studio met Azure Logic Apps en functies](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [Afhandeling van uitzonde ringen en fouten logboek registratie voor Logic apps](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
