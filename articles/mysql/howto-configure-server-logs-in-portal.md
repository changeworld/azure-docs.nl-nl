---
title: Toegang tot langzame query logboeken-Azure Portal-Azure Database for MySQL
description: In dit artikel wordt beschreven hoe u de langzame Logboeken in Azure Database for MySQL kunt configureren en openen vanuit de Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 52d219a94676e0f1ce4ceac31b0b50c9d36665ba
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770574"
---
# <a name="configure-and-access-slow-query-logs-from-the-azure-portal"></a>Langzame query logboeken van de Azure Portal configureren en openen

U kunt de [Azure database for MySQL langzame query logboeken](concepts-server-logs.md) configureren, weer geven en downloaden vanuit de Azure Portal.

## <a name="prerequisites"></a>Vereisten
Voor de stappen in dit artikel moet [Azure database for mysql server](quickstart-create-mysql-server-database-using-azure-portal.md)zijn geïnstalleerd.

## <a name="configure-logging"></a>Logboek registratie configureren
Configureer de toegang tot het MySQL-logboek voor langzame query's. 

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

2. Selecteer uw Azure Database for MySQL-server.

3. Selecteer in de sectie **bewaking** in de zijbalk **Server logboeken**. 
   ![scherm opname van opties voor Server logboeken](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Selecteer om de server parameters weer te geven de optie **Klik hier om Logboeken in te scha kelen en logboek parameters te configureren**.

5. Wijzig de para meters die u wilt aanpassen. Alle wijzigingen die u aanbrengt in deze sessie, worden in paars gemarkeerd. 

   Nadat u de para meters hebt gewijzigd, selecteert u **Opslaan**. Of u kunt uw wijzigingen negeren.

   ![Scherm opname van opties voor server parameters](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

Op de pagina **server parameters** kunt u teruggaan naar de lijst met Logboeken door de pagina te sluiten.

## <a name="view-list-and-download-logs"></a>Lijst weer geven en logboeken downloaden
Nadat de logboek registratie is gestart, kunt u een lijst met beschik bare langzame query logboeken bekijken en afzonderlijke logboek bestanden downloaden.

1. Open Azure Portal.

2. Selecteer uw Azure Database for MySQL-server.

3. Selecteer in de sectie **bewaking** in de zijbalk **Server logboeken**. Op de pagina wordt een lijst met uw logboek bestanden weer gegeven.

   ![Scherm opname van de pagina Server logboeken, met de lijst met gemarkeerde logboeken](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > De naam Conventie van het logboek is **mysql-slow < uw server name >-yyyymmddhh. log**. De datum en tijd die in de bestands naam wordt gebruikt, is het tijdstip waarop het logboek is uitgegeven. Logboek bestanden worden elke 24 uur of 7,5 GB gedraaid, afhankelijk van wat het eerste komt. 

4. Gebruik, indien nodig, het zoekvak om snel naar een specifiek logboek te beperken, op basis van datum en tijd. De zoek opdracht bevindt zich op de naam van het logboek.

5. Als u afzonderlijke logboek bestanden wilt downloaden, selecteert u het pictogram met de pijl-omlaag naast elk logboek bestand in de tabelrij.

   ![Scherm opname van de pagina Server logboeken, met pictogram met de pijl-omlaag gemarkeerd](./media/howto-configure-server-logs-in-portal/5-download.png)

## <a name="set-up-diagnostic-logs"></a>Diagnostische logboeken instellen

1. Selecteer in de sectie **bewaking** van de zijbalk **diagnostische instellingen** > **Diagnostische instellingen toevoegen**.

   ![Scherm opname van opties voor Diagnostische instellingen](./media/howto-configure-server-logs-in-portal/add-diagnostic-setting.png)

1. Geef een naam op voor de diagnostische instelling.

1. Geef op welke gegevens worden gesinkt om de langzame query Logboeken (opslag account, Event Hub of Log Analytics-werk ruimte) te verzenden.

1. Selecteer **MySqlSlowLogs** als het logboek type.
![scherm opname van configuratie opties voor Diagnostische instellingen](./media/howto-configure-server-logs-in-portal/configure-diagnostic-setting.png)

1. Nadat u de gegevens sinks hebt geconfigureerd om de langzame query logboeken te pipeen, selecteert u **Opslaan**.
![scherm opname van configuratie opties voor Diagnostische instellingen, met gemarkeerde](./media/howto-configure-server-logs-in-portal/save-diagnostic-setting.png)

1. Open de logboeken voor trage query's door ze te verkennen in de gegevens-sinks die u hebt geconfigureerd. Het kan tot tien minuten duren voordat de logboeken worden weer gegeven.

## <a name="next-steps"></a>Volgende stappen
- Zie [langzame query Logboeken openen in cli](howto-configure-server-logs-in-cli.md) voor meer informatie over het downloaden van langzame query logboeken via een programma.
- Meer informatie over [trage query-logboeken](concepts-server-logs.md) in azure database for MySQL.
- Zie de MySQL-documentatie over [Logboeken](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)voor meer informatie over de parameter definities en MySQL-logboek registratie.