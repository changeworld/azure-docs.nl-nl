---
title: Azure Monitor voor VM's kaart integreren met Operations Manager | Microsoft Docs
description: Azure Monitor voor VM's detecteert automatisch toepassings onderdelen op Windows-en Linux-systemen en wijst de communicatie tussen services toe. In dit artikel wordt beschreven hoe u met de kaart functie automatisch gedistribueerde toepassings diagrammen maakt in Operations Manager.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/12/2019
ms.openlocfilehash: 112413720f969474369555a74bc89846666e2ef9
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77663450"
---
# <a name="integrate-system-center-operations-manager-with-azure-monitor-for-vms-map-feature"></a>System Center Operations Manager integreren met Azure Monitor voor VM's kaart functie

In Azure Monitor voor VM's kunt u gedetecteerde toepassings onderdelen weer geven op virtuele Windows-en Linux-machines (Vm's) die worden uitgevoerd in azure of in uw omgeving. Als deze integratie tussen de kaart functie en System Center Operations Manager, kunt u automatisch gedistribueerde toepassings diagrammen maken in Operations Manager die zijn gebaseerd op de dynamische afhankelijkheids toewijzingen in Azure Monitor voor VM's. In dit artikel wordt beschreven hoe u uw System Center Operations Manager-beheer groep configureert ter ondersteuning van deze functie.

>[!NOTE]
>Als u Servicetoewijzing al hebt geïmplementeerd, kunt u uw kaarten weer geven in Azure Monitor voor VM's, met daarin extra functies voor het controleren van de status en prestaties van de virtuele machine. De kaart functie van Azure Monitor voor VM's is bedoeld om de zelfstandige Servicetoewijzing oplossing te vervangen. Zie [Azure monitor voor VM's-overzicht](vminsights-overview.md)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

* Een System Center Operations Manager-beheer groep (2012 R2 of hoger).
* Een Log Analytics-werk ruimte die is geconfigureerd voor de ondersteuning van Azure Monitor voor VM's.
* Een of meer virtuele Windows-en Linux-machines of fysieke computers die worden bewaakt door Operations Manager en het verzenden van gegevens naar uw Log Analytics-werk ruimte. Linux-servers die rapporteren aan een Operations Manager-beheer groep moeten worden geconfigureerd om rechtstreeks verbinding te maken met Azure Monitor. Raadpleeg het overzicht in [logboek gegevens verzamelen met de log Analytics-agent](../platform/log-analytics-agent.md)voor meer informatie.
* Een service-principal met toegang tot het Azure-abonnement dat is gekoppeld aan de Log Analytics-werk ruimte. Ga voor meer informatie naar [een service-principal maken](#create-a-service-principal).

## <a name="install-the-service-map-management-pack"></a>Installeer de Servicetoewijzing management pack

U schakelt de integratie tussen Operations Manager en de kaart functie in door het importeren van micro soft. System Center. ServiceMap management pack bundel (micro soft. System Center. ServiceMap. MPB). U kunt de management pack bundel downloaden van het [micro soft Download centrum](https://www.microsoft.com/download/details.aspx?id=55763). De bundel bevat de volgende Management Packs:

* Micro soft Servicetoewijzing-toepassings weergaven
* Micro soft System Center Servicetoewijzing intern
* Micro soft System Center Servicetoewijzing onderdrukkingen
* Micro soft System Center Servicetoewijzing

## <a name="configure-integration"></a>Integratie configureren

Nadat u de Servicetoewijzing management pack hebt geïnstalleerd, wordt er een nieuw knoop punt **servicetoewijzing**weer gegeven onder **Operations Management Suite** in het deel venster **beheer** van uw Operations Manager Operations-console.

>[!NOTE]
>[Operations Management Suite is een verzameling van services](../terminology.md#april-2018---retirement-of-operations-management-suite-brand) die zijn opgenomen log Analytics, maakt nu deel uit van [Azure monitor](../overview.md).

Ga als volgt te werk om Azure Monitor voor VM's kaart integratie te configureren:

1. Als u de configuratie wizard wilt openen, klikt u in het deel venster **servicetoewijzing overzicht** op **werk ruimte toevoegen**.  

    ![Servicetoewijzing deel venster Overzicht](media/service-map-scom/scom-configuration.png)

2. Voer in het venster configuratie van de **verbinding** de naam van de TENANT of id, toepassings-id (ook wel bekend als de gebruikers naam of clientID) en het wacht woord van de Service-Principal in en klik vervolgens op **volgende**. Ga voor meer informatie naar een service-principal maken.

    ![Het venster verbindings configuratie](media/service-map-scom/scom-config-spn.png)

3. Selecteer in het venster voor het selecteren van het **abonnement** het Azure-abonnement, de Azure-resource groep (de naam die de log Analytics-werk ruimte bevat) en log Analytics werk ruimte en klik vervolgens op **volgende**.

    ![De werk ruimte Operations Manager configuratie](media/service-map-scom/scom-config-workspace.png)

4. In het venster **computer groep selecteren** kiest u de servicetoewijzing machine groepen die u wilt synchroniseren met Operations Manager. Klik op **computer groepen toevoegen/verwijderen**, kies groepen in de lijst met **beschik bare computer groepen**en klik op **toevoegen**.  Wanneer u klaar bent met het selecteren van groepen, klikt u op **OK** om te volt ooien.

    ![De Operations Manager configuratie machine groepen](media/service-map-scom/scom-config-machine-groups.png)

5. In het venster **server selectie** configureert u de servicetoewijzing servers groep met de servers die u wilt synchroniseren tussen Operations Manager en de kaart functie. Klik op **servers toevoegen/verwijderen**.

    Voor de integratie van het bouwen van een gedistribueerd toepassings diagram voor een-server moet de-server:

   * Bewaakt door Operations Manager
   * Geconfigureerd om te rapporteren aan de Log Analytics werk ruimte die is geconfigureerd met Azure Monitor voor VM's
   * Vermeld in de groep Servicetoewijzing servers

     ![De configuratie groep Operations Manager](media/service-map-scom/scom-config-group.png)

6. Optioneel: Selecteer de resource groep alle beheerser vers om te communiceren met Log Analytics en klik vervolgens op **werk ruimte toevoegen**.

    ![De resource groep voor de Operations Manager configuratie](media/service-map-scom/scom-config-pool.png)

    Het kan een minuut duren voordat de Log Analytics-werk ruimte is geconfigureerd en geregistreerd. Nadat de configuratie is geconfigureerd, start Operations Manager de eerste synchronisatie van de kaart.

    ![De resource groep voor de Operations Manager configuratie](media/service-map-scom/scom-config-success.png)

## <a name="monitor-integration"></a>Integratie controleren

Nadat de Log Analytics werk ruimte is verbonden, wordt een nieuwe map, Servicetoewijzing, weer gegeven in het deel venster **bewaking** van de Operations Manager Operations-console.

![Het deel venster Operations Manager bewaking](media/service-map-scom/scom-monitoring.png)

De map Servicetoewijzing heeft vier knoop punten:

* **Actieve waarschuwingen**: een lijst met alle actieve waarschuwingen over de communicatie tussen Operations Manager en Azure monitor.  

  >[!NOTE]
  >Deze waarschuwingen worden niet Log Analytics waarschuwingen die zijn gesynchroniseerd met Operations Manager en ze worden gegenereerd in de beheer groep op basis van werk stromen die zijn gedefinieerd in de Servicetoewijzing management pack.

* **Servers**: geeft een lijst van de bewaakte servers die zijn geconfigureerd om te synchroniseren van Azure monitor voor VM's kaart functie.

    ![Het deel venster Operations Manager monitoring servers](media/service-map-scom/scom-monitoring-servers.png)

* **Afhankelijkheids weergaven van computer groepen**: een lijst met alle computer groepen die zijn gesynchroniseerd vanuit de kaart functie. U kunt op een wille keurige groep klikken om het gedistribueerde toepassings diagram weer te geven.

    ![Het diagram voor gedistribueerde toepassingen Operations Manager](media/service-map-scom/scom-group-dad.png)

* **Server afhankelijkheids weergaven**: een lijst met alle servers die zijn gesynchroniseerd vanuit de kaart functie. U kunt klikken op een wille keurige server om het gedistribueerde toepassings diagram weer te geven.

    ![Het diagram voor gedistribueerde toepassingen Operations Manager](media/service-map-scom/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>De werk ruimte bewerken of verwijderen

U kunt de geconfigureerde werk ruimte bewerken of verwijderen via het Servicetoewijzing deel venster **overzicht** (**beheer** Venster > **Operations Management Suite** > **servicetoewijzing**).

>[!NOTE]
>[Operations Management Suite is een verzameling services](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/azure-monitor-rebrand.md#retirement-of-operations-management-suite-brand) die log Analytics bevat, die nu deel uitmaakt van [Azure monitor](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/overview.md).

U kunt slechts één Log Analytics werkruimte configureren in deze huidige versie.

![Het deel venster Operations Manager werk ruimte bewerken](media/service-map-scom/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>Regels en onderdrukkingen configureren

Met een regel, *micro soft. System Center. ServiceMapImport. rule*, wordt regel matig gegevens opgehaald van Azure monitor voor VM's kaart functie. Als u het synchronisatie-interval wilt wijzigen, kunt u de regel overschrijven en de waarde voor de para meter **IntervalMinutes**wijzigen.

![Het venster Eigenschappen van Operations Manager onderdrukkingen](media/service-map-scom/scom-overrides.png)

* **Ingeschakeld**: automatische updates in-of uitschakelen.
* **IntervalMinutes**: Hiermee geeft u de tijd tussen updates op. Het standaard interval is een uur. Als u toewijzingen vaker wilt synchroniseren, kunt u de waarde wijzigen.
* **TimeoutSeconds**: Hiermee geeft u de tijds duur vóór de time-out van de aanvraag op.
* **TimeWindowMinutes**: Hiermee geeft u het tijd venster op voor het opvragen van gegevens. De standaard waarde is 60 minuten, het Maxi maal toegestane interval.

## <a name="known-issues-and-limitations"></a>Bekende problemen en beperkingen

Het huidige ontwerp bevat de volgende problemen en beperkingen:

* U kunt alleen verbinding maken met een enkele Log Analytics-werk ruimte.
* Hoewel u servers hand matig aan de groep Servicetoewijzing servers kunt toevoegen via het deel venster **ontwerpen** , worden de kaarten voor die servers niet direct gesynchroniseerd. Ze worden tijdens de volgende synchronisatie cyclus gesynchroniseerd vanaf Azure Monitor voor VM's toewijzings functie.
* Als u wijzigingen aanbrengt in de diagrammen voor gedistribueerde toepassingen die zijn gemaakt door de management pack, worden deze wijzigingen waarschijnlijk overschreven bij de volgende synchronisatie met Azure Monitor voor VM's.

## <a name="create-a-service-principal"></a>Een service-principal maken

Zie voor officiële Azure-documentatie over het maken van een Service-Principal:

* [Een service-principal maken met behulp van Power shell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Een service-principal maken met behulp van Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)
* [Een service-principal maken met behulp van de Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)

### <a name="feedback"></a>Feedback
Hebt u feedback voor ons over de integratie met Azure Monitor voor VM's kaart functie of deze documentatie? Ga naar onze [pagina met gebruikers spraak](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), waar u functies kunt suggereren of stem op bestaande suggesties.
