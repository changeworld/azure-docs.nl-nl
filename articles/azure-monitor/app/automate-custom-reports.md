---
title: Aangepaste rapporten automatiseren met Azure-toepassing Insights-gegevens
description: Aangepaste dagelijkse/wekelijkse/maandelijkse rapporten automatiseren met Azure-toepassing Insights-gegevens
ms.topic: conceptual
ms.date: 05/20/2019
ms.reviewer: sdash
ms.openlocfilehash: d91595a863901fcc420611ac644c7856e74320dd
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77655120"
---
# <a name="automate-custom-reports-with-azure-application-insights-data"></a>Aangepaste rapporten automatiseren met Azure-toepassing Insights-gegevens

Periodieke rapporten helpen een team op de hoogte te houden van hoe hun bedrijfs kritieke services doen. Ontwikkel aars, DevOps/SRE teams en hun managers kunnen productief zijn met geautomatiseerde rapporten die op betrouw bare wijze inzichten leveren zonder dat iedereen zich hoeft aan te melden bij de portal. Dergelijke rapporten kunnen ook leiden tot het identificeren van geleidelijke toename van latentie, laad-of fout percentages waarmee geen waarschuwings regels kunnen worden geactiveerd.

Elke onderneming heeft zijn unieke rapportage behoeften, zoals: 

* Specifieke percentiel aggregaties van metrische gegevens of aangepaste metrische gegevens in een rapport.
* Hebben verschillende rapporten voor dagelijkse, wekelijkse en maandelijkse samenbrengen van gegevens voor verschillende doel groepen.
* Segmenteren op aangepaste kenmerken, zoals regio, of omgeving. 
* Groepeer enkele AI-resources samen in één rapport, zelfs als ze zich in verschillende abonnementen of resource groepen bevinden, enzovoort.
* Afzonderlijke rapporten met gevoelige metrische gegevens die worden verzonden naar selectief publiek.
* Rapporten aan belanghebbenden die mogelijk geen toegang tot de portal resources hebben.

> [!NOTE] 
> De wekelijkse Application Insights Digest-e-mail heeft geen aanpassing toegestaan en wordt niet meer in aanmerking genomen voor de hieronder vermelde aangepaste opties. De laatste wekelijkse overzichts-e-mail wordt verzonden op 11 juni 2018. Configureer een van de volgende opties om Vergelijk bare aangepaste rapporten te verkrijgen (gebruik de query die hieronder wordt voorgesteld).

## <a name="to-automate-custom-report-emails"></a>Aangepaste rapport e-mails automatiseren

U kunt [programmatisch query's uitvoeren op Application Insights](https://dev.applicationinsights.io/) gegevens om aangepaste rapporten te genereren op schema. U kunt de volgende opties gebruiken om snel aan de slag te gaan:

* [Rapporten automatiseren met Microsoft Flow](automate-with-flow.md)
* [Rapporten automatiseren met Logic Apps](automate-with-logic-apps.md)
* Gebruik de [Azure function](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function) -sjabloon ' Application Insights gepland overzicht ' in het bewakings scenario. Deze functie maakt gebruik van SendGrid om het e-mail bericht te leveren. 

    ![Azure-functie sjabloon](./media/automate-custom-reports/azure-function-template.png)

## <a name="sample-query-for-a-weekly-digest-email"></a>Voorbeeld query voor een wekelijks overzicht van e-mail
De volgende query geeft een samen voeging tussen meerdere gegevens sets voor een wekelijks overzicht per e-mail, zoals rapport. Pas deze indien nodig aan en gebruik dit met een van de hierboven genoemde opties om een wekelijks rapport te automatiseren.   

```AIQL
let period=7d;
requests
| where timestamp > ago(period)
| summarize Row = 1, TotalRequests = sum(itemCount), FailedRequests = sum(toint(success == 'False')),
    RequestsDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
| join (
dependencies
| where timestamp > ago(period)
| summarize Row = 1, TotalDependencies = sum(itemCount), FailedDependencies = sum(success == 'False'),
    DependenciesDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row | join (
pageViews
| where timestamp > ago(period)
| summarize Row = 1, TotalViews = sum(itemCount)
) on Row | join (
exceptions
| where timestamp > ago(period)
| summarize Row = 1, TotalExceptions = sum(itemCount)
) on Row | join (
availabilityResults
| where timestamp > ago(period)
| summarize Row = 1, OverallAvailability = iff(isnan(avg(toint(success))), '------', tostring(toint(avg(toint(success)) * 10000) / 100.0)),
    AvailabilityDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row
| project TotalRequests, FailedRequests, RequestsDuration, TotalDependencies, FailedDependencies, DependenciesDuration, TotalViews, TotalExceptions, OverallAvailability, AvailabilityDuration
```

## <a name="application-insights-scheduled-digest-report"></a>Application Insights gepland samenvattings rapport

1. Selecteer in de Azure Portal **een resource maken** > **compute** > **functie-app**.

   ![Een functie-app scherm opname van een Azure-resource maken](./media/automate-custom-reports/function-app-01.png)

2. Voer de juiste gegevens in voor uw app en selecteer _maken_. (Application Insights _is_ alleen vereist als u de nieuwe functie-app wilt controleren met Application Insights)

   ![Een scherm opname van de Azure-resource functie-app instellingen maken](./media/automate-custom-reports/function-app-02.png)

3. Wanneer de implementatie van uw nieuwe functie-app is voltooid, selecteert **u naar resource**.

4. Selecteer **nieuwe functie**.

   ![Een nieuwe functie scherm afbeelding maken](./media/automate-custom-reports/function-app-03.png)

5. Selecteer de **_sjabloon geplande samen vatting Application Insights_** .

     > [!NOTE]
     > Functie-apps worden standaard gemaakt met runtime versie 2. x. U moet [Azure functions runtime versie](https://docs.microsoft.com/azure/azure-functions/set-runtime-version) **1. x** instellen voor het gebruik van de sjabloon Application Insights geplande samen vatting.  scherm opname van ![-runtime](./../../../includes/media/functions-view-update-version-portal/function-app-view-version.png)



   ![Scherm afbeelding nieuwe functie Application Insights sjabloon](./media/automate-custom-reports/function-app-04.png)

6. Voer het juiste e-mail adres van de ontvanger in voor uw rapport en selecteer **maken**.

   ![Scherm opname van functie-instellingen](./media/automate-custom-reports/function-app-05.png)

7. Selecteer uw **functie-app** > **platform functies** > **Toepassings instellingen**.

    ![Scherm opname van de Azure function-toepassings instellingen](./media/automate-custom-reports/function-app-07.png)

8. Maak drie nieuwe toepassings instellingen met de juiste overeenkomende waarden ``AI_APP_ID``, ``AI_APP_KEY``en ``SendGridAPI``. Selecteer **Opslaan**.

     ![Scherm opname van de functie integratie interface](./media/automate-custom-reports/function-app-08.png)
    
    (De AI_ waarden vindt u onder API-toegang voor de Application Insights resource waarover u een rapport wilt maken. Als u geen Application Insights-API-sleutel hebt, kunt u een **API-sleutel maken**.)
    
   * AI_APP_ID = toepassings-ID
   * AI_APP_KEY = API-sleutel
   * SendGridAPI = SendGrid-API-sleutel

     > [!NOTE]
     > Als u geen SendGrid-account hebt, kunt u er een maken. De documentatie van SendGrid voor Azure Functions is [hier](https://docs.microsoft.com/azure/azure-functions/functions-bindings-sendgrid)beschikbaar. Als u alleen een minimale uitleg wilt over het instellen van SendGrid en het genereren van een API-sleutel, wordt aan het einde van dit artikel vermeld. 

9. Selecteer **integreren** en klik onder uitvoer op **SendGrid ($Return)** .

     ![Scherm opname van uitvoer](./media/automate-custom-reports/function-app-09.png)

10. Selecteer de zojuist gemaakte app-instelling voor **SendGridAPI**onder de **instelling app SendGridAPI-sleutel**.

     ![Scherm afbeelding van functie-app uitvoeren](./media/automate-custom-reports/function-app-010.png)

11. Voer uw functie-app uit en test deze.

     ![Scherm opname testen](./media/automate-custom-reports/function-app-11.png)

12. Controleer uw e-mail om te bevestigen dat het bericht is verzonden/ontvangen.

     ![Scherm afbeelding van onderwerpregel van E-mail](./media/automate-custom-reports/function-app-12.png)

## <a name="sendgrid-with-azure"></a>SendGrid met Azure

Deze stappen zijn alleen van toepassing als u nog geen SendGrid-account hebt geconfigureerd.

1. Ga naar het Azure Portal Selecteer **een resource maken** zoek naar **SendGrid-e-mail bezorging** > Klik op > **maken** en vul de specifieke instructies voor het maken van de SendGrid in. 

     ![Scherm opname van SendGrid-resource maken](./media/automate-custom-reports/function-app-13.png)

2. Wanneer u hebt gemaakt onder SendGrid-accounts, selecteert u **beheren**.

     ![Scherm opname van API-sleutel instellingen](./media/automate-custom-reports/function-app-14.png)

3. Hiermee wordt de site van SendGrid gestart. Selecteer **instellingen** > **API-sleutels**.

     ![Scherm opname van API-sleutel toepassing maken en weer geven](./media/automate-custom-reports/function-app-15.png)

4. Een API-sleutel maken > Kies **& weer gave maken** (Raadpleeg de documentatie van SendGrid over beperkte toegang om te bepalen welk niveau van machtigingen het meest geschikt is voor uw API-sleutel. Volledige toegang is hier alleen voor beeld van toepassing geselecteerd.)

   ![Scherm opname van volledige toegang](./media/automate-custom-reports/function-app-16.png)

5. Kopieer de volledige sleutel. deze waarde is wat u nodig hebt in uw functie-app-instellingen als waarde voor SendGridAPI

   ![Scherm opname van API-sleutel kopiëren](./media/automate-custom-reports/function-app-17.png)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het maken van [Analytics-query's](../../azure-monitor/log-query/get-started-queries.md).
* Meer informatie over het [programmatisch opvragen van Application Insights gegevens](https://dev.applicationinsights.io/)
* Meer informatie over [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).
* Meer informatie over [Microsoft flow](https://ms.flow.microsoft.com).
