---
title: Zelf studie-Azure Analysis Services verbinden met Power BI Desktop | Microsoft Docs
author: minewiskan
description: Informatie over het ophalen van een Analysis Services server naam uit de Azure Portal en vervolgens verbinding maken met de server met behulp van Power BI Desktop.
ms.service: azure-analysis-services
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: owend
ms.openlocfilehash: 4d8c753f06e58fd1cce1c55eca213637cb70e436
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73572323"
---
# <a name="tutorial-connect-with-power-bi-desktop"></a>Zelfstudie: Verbinden met Power BI Desktop

In deze zelfstudie gebruikt u Power BI Desktop om verbinding te maken met de voorbeeldmodeldatabase Adventure Works op uw server. De taken die u uitvoert, zijn een simulatie van een normale gebruikersverbinding met het model en het maken van een basisrapport van modelgegevens.

> [!div class="checklist"]
> * Uw servernaam ophalen uit de portal
> * Verbinding maken met behulp van Power BI Desktop
> * Een basisrapport maken

## <a name="prerequisites"></a>Vereisten

- [Voeg de voorbeeldmodeldatabase Adventure Works toe](../analysis-services-create-sample-model.md) aan uw server.
- Zorg dat u [*lees*](../analysis-services-server-admins.md)machtigingen hebt voor de voorbeeldmodeldatabase Adventure Works.
- [Installeer de nieuwste versie van Power BI Desktop](https://powerbi.microsoft.com/desktop).

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal
In deze zelfstudie meldt u zich aan bij de portal om alleen de servernaam op te halen. Normaal gesproken krijgen gebruikers de servernaam van de serverbeheerder.

Meld u aan bij de [portal](https://portal.azure.com/).

## <a name="get-server-name"></a>De servernaam ophalen
Als eerste hebt u de servernaam nodig om verbinding te maken met uw server vanuit Power BI Desktop. U kunt de servernaam ophalen uit de portal.

In **Azure Portal** > server > **Overview** > **Servernaam**,kopieer de servernaam.
   
   ![Servernaam bepalen in Azure](./media/analysis-services-tutorial-pbid/aas-copy-server-name.png)

## <a name="connect-in-power-bi-desktop"></a>Verbinden maken in Power BI Desktop

1. Klik in Power BI Desktop op **Gegevens ophalen** > **Azure** > **Azure Analysis Services-database**.

   ![Verbinding maken in Gegevens ophalen](./media/analysis-services-tutorial-pbid/aas-pbid-connect-aasserver.png)

2. Plak de servernaam in **Server**, geef **adventureworks** op in **Database** en klik vervolgens op **OK**.

   ![De servernaam en modeldatabase opgeven](./media/analysis-services-tutorial-pbid/aas-pbid-connect-aas-servername.png)

3. Voer uw referenties in wanneer dit wordt gevraagd. Het account dat u opgeeft, moet minimaal leesmachtigingen hebben voor de voorbeeldmodeldatabase Adventure Works.

    Het model Adventure Works wordt geopend in Power BI Desktop met een leeg rapport in de rapportweergave. De lijst **Velden** bevat alle niet-verborgen modelobjecten. De verbindingsstatus wordt rechtsonder weergegeven.

4. Selecteer in **VISUALISATIES** de optie **Gegroepeerd staafdiagram**, klik vervolgens op **Opmaak** (verfrollerpictogram) en schakel **Gegevenslabels** in. 

   ![Visualisaties](./media/analysis-services-tutorial-pbid/aas-pbid-visualizations-report.png)

5. Selecteer in de tabel **VELDEN** > **Internetverkoop** de metingen voor **Totaal internetverkoop**  en **Marge**. Selecteer **Productcategorienaam** in de tabel **Productcategorie**.

   ![Rapport voltooien](./media/analysis-services-tutorial-pbid/aas-pbid-complete-report.png)

    Verken het voorbeeldmodel Adventure Works door verschillende visualisaties te maken en gegevens en metrische gegevens te segmenteren. Sla het rapport op wanneer u hiermee tevreden bent.

## <a name="clean-up-resources"></a>Resources opschonen

Als u het rapport niet meer nodig hebt, slaat u het niet op of verwijdert u het bestand als u het rapport wel hebt opgeslagen.

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u geleerd hoe u Power BI Desktop gebruikt om verbinding te maken met een gegevensmodel op een server en een basisrapport te maken. Als u niet bekend bent met het maken van een gegevens model, raadpleegt u de [zelf studie Adventure Works Internet Sales tabellaire Data Modeling](https://docs.microsoft.com/analysis-services/tutorial-tabular-1400/as-adventure-works-tutorial) in de SQL Server Analysis Services docs.