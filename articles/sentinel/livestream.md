---
title: Gebruik jacht Livestream in azure Sentinel om bedreigingen te detecteren | Microsoft Docs
description: In dit artikel wordt beschreven hoe u met behulp van jacht Livestream in azure Sentinel gegevens bijhoudt.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2019
ms.author: yelevin
ms.openlocfilehash: b392644e504fa8187e637278bef8718c9c2caa3f
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77582123"
---
# <a name="use-hunting-livestream-in-azure-sentinel-to-detect-threats"></a>Jacht Livestream in azure Sentinel gebruiken om bedreigingen te detecteren

> [!IMPORTANT]
> Jacht Livestream in azure Sentinel is momenteel beschikbaar als open bare preview-versie en wordt geleidelijk geïmplementeerd naar tenants.
> Deze functie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.


Gebruik jacht Livestream om interactieve sessies te maken waarmee u nieuw gemaakte query's kunt testen wanneer er gebeurtenissen optreden, meldingen van de sessies ontvangen wanneer een overeenkomst wordt gevonden en indien nodig onderzoek starten. U kunt snel een livestream-sessie maken met behulp van een Log Analytics-query.

- **Nieuw gemaakte query's testen wanneer er gebeurtenissen optreden**
    
    U kunt query's testen en aanpassen zonder conflicten met de huidige regels die actief worden toegepast op gebeurtenissen. Nadat u hebt gecontroleerd of deze nieuwe query's werken zoals verwacht, kunt u ze eenvoudig promo veren tot aangepaste waarschuwings regels door een optie te selecteren waarmee de sessie wordt uitgebreid naar een waarschuwing.

- **Ontvang een melding wanneer er bedreigingen optreden**
    
    U kunt bedreigingen gegevensfeeds vergelijken met geaggregeerde logboek gegevens en een melding ontvangen wanneer er een overeenkomst plaatsvindt. Bedreigings gegevensfeeds zijn actieve streams van gegevens die gerelateerd zijn aan potentiële of huidige bedreigingen, zodat de melding kan wijzen op een mogelijke bedreiging voor uw organisatie. Maak in plaats van een aangepaste waarschuwings regel een livestream-sessie wanneer u op de hoogte wilt worden gesteld van een mogelijk probleem zonder de overhead van het onderhouden van een aangepaste waarschuwings regel.

- **Onderzoek starten**
    
    Als er sprake is van een actief onderzoek waarbij een activum zoals een host of gebruiker is betrokken, kunt u specifieke (of alle) activiteiten in de logboek gegevens weer geven als deze op die Asset plaatsvinden. U kunt een melding ontvangen wanneer deze activiteit optreedt.


## <a name="create-a-livestream-session"></a>Een livestream-sessie maken

U kunt een livestream-sessie maken op basis van een bestaande zoek opdracht of een volledig nieuwe sessie maken.

1. Ga in het Azure Portal naar **Sentinel** > **Threat Management** > **jacht**.

2. Een livestream-sessie maken op basis van een zoek opdracht:
    
    1. Ga op het tabblad **query's** naar de zoek opdracht die u wilt gebruiken.
    2. Klik met de rechter muisknop op de query en selecteer **toevoegen aan Livestream**. Bijvoorbeeld:
    
    > [!div class="mx-imgBorder"]
    > ![een livestream-sessie maken op basis van de query voor de Sentinel-jacht van Azure](./media/livestream/livestream-from-query.png)

3. Een volledig nieuwe Livestream-sessie maken: 
    
    1. Selecteer het tabblad **Livestream**
    2. Selecteer **Ga naar livestream**.
    
4. In het deel venster **Livestream** :
    
    - Als u Livestream van een query hebt gestart, controleert u de query en brengt u de gewenste wijzigingen aan.
    - Als u Livestream helemaal niet hebt gestart, maakt u de query. 

5. Selecteer **afspelen** in de opdracht balk.
    
    De status balk onder de opdracht balk geeft aan of uw Livestream-sessie actief of onderbroken is. In het volgende voor beeld wordt de sessie uitgevoerd:
    
    > [!div class="mx-imgBorder"]
    > ![Livestream-sessie maken op basis van een Sentinel-jacht van Azure](./media/livestream/livestream-session.png)

6. Selecteer **Opslaan** vanaf de opdracht balk.
    
    Tenzij u **onderbreken**selecteert, blijft de sessie actief totdat u bent afgemeld bij de Azure Portal.

## <a name="view-your-livestream-sessions"></a>Uw Livestream-sessies weer geven

1. Ga in het Azure Portal naar **Sentinel** > **Threat management** > **jacht** > tabblad **Livestream** .

2. Selecteer de Livestream-sessie die u wilt weer geven of bewerken. Bijvoorbeeld:
    
    > [!div class="mx-imgBorder"]
    > ![een livestream-sessie maken op basis van de query voor de Sentinel-jacht van Azure](./media/livestream/livestream-tab.png)
    
    De geselecteerde Livestream-sessie wordt geopend, zodat u deze kunt afspelen, onderbreken, bewerken, enzovoort.

## <a name="receive-notifications-when-new-events-occur"></a>Meldingen ontvangen wanneer er nieuwe gebeurtenissen optreden

Omdat Livestream-meldingen voor nieuwe gebeurtenissen gebruikmaken van Azure Portal meldingen, worden deze meldingen weer geven wanneer u de Azure Portal gebruikt. Bijvoorbeeld:

![Azure Portal-melding voor LiveStream](./media/livestream/notification.png)

Selecteer de melding om het deel venster **Livestream** te openen.
 
## <a name="elevate-a-livestream-session-to-an-alert"></a>Een livestream-sessie verhogen naar een waarschuwing

U kunt een livestream-sessie promo veren naar een nieuwe waarschuwing door **uitbrei ding op waarschuwing te** selecteren in de opdracht balk van de relevante Livestream-sessie:

> [!div class="mx-imgBorder"]
> ![Livestream-sessie verhogen naar een waarschuwing](./media/livestream/elevate-to-alert.png)

Met deze actie wordt de wizard voor het maken van regels geopend. deze wordt vooraf ingevuld met de query die is gekoppeld aan de Livestream-sessie.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u de jacht Livestream in azure Sentinel kunt gebruiken. Raadpleeg de volgende artikelen voor meer informatie over Azure Sentinel:

- [Proactief zoeken naar bedreigingen](hunting.md)
- [Notitie blokken gebruiken voor het uitvoeren van geautomatiseerde jacht-campagnes](notebooks.md)
