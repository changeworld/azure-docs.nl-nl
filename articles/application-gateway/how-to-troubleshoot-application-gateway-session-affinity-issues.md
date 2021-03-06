---
title: Problemen met sessie affiniteit oplossen
titleSuffix: Azure Application Gateway
description: Dit artikel bevat informatie over het oplossen van problemen met sessie affiniteit in Azure-toepassing gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 9f14521c15c3497bed4ffbeba44cb5d78ee4df7b
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74047990"
---
# <a name="troubleshoot-azure-application-gateway-session-affinity-issues"></a>Problemen met de Azure-toepassing gateway sessie affiniteit oplossen

Meer informatie over het vaststellen en oplossen van problemen met sessie affiniteit met Azure-toepassing gateway.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Overzicht

De functie Sessieaffiniteit op basis van cookies is handig als u een gebruikerssessie op dezelfde server wilt behouden. Met behulp van de gatewaybeheerde cookies kan de Application Gateway het daarop volgende verkeer van een gebruikerssessie naar dezelfde server leiden voor verwerking. Dit is belangrijk wanneer de sessiestatus lokaal wordt opgeslagen op de server voor een gebruikerssessie.

## <a name="possible-problem-causes"></a>Mogelijke probleem oorzaken

Het probleem bij het onderhouden van sessie affiniteit op basis van cookies kan optreden als gevolg van de volgende hoofd oorzaken:

- Instelling voor affiniteit op basis van cookies is niet ingeschakeld
- Uw toepassing kan de op cookies gebaseerde affiniteit niet afhandelen
- De toepassing maakt gebruik van affiniteit op basis van cookies, maar aanvragen worden nog steeds stuiteren tussen back-endservers

### <a name="check-whether-the-cookie-based-affinity-setting-is-enabled"></a>Controleer of de instelling voor de affiniteit op basis van cookies is ingeschakeld

Soms kunnen er problemen optreden met de sessie affiniteit wanneer u vergeet de instelling ' op cookie gebaseerd ' in te scha kelen. Volg de instructies om te bepalen of u de instelling ' op cookie gebaseerde affiniteit ' hebt ingeschakeld op het tabblad HTTP-instellingen in de Azure Portal:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

2. Klik in het **linker navigatie** deel venster op **alle resources**. Klik op de naam van de toepassings gateway op de Blade alle resources. Als het abonnement dat u hebt geselecteerd, al verschillende resources heeft, kunt u de naam van de toepassings gateway opgeven in het **filter op naam...** voor eenvoudige toegang tot de toepassingsgateway.

3. Selecteer het tabblad **http-instellingen** onder **instellingen**.

   ![problemen oplossen-sessie affiniteit-problemen-1](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-1.png)

4. Klik op **appGatewayBackendHttpSettings** aan de rechter kant om te controleren of u **ingeschakeld** hebt voor de affiniteit op basis van cookies.

   ![problemen oplossen-sessie-affiniteit-problemen-2](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-2.jpg)



U kunt ook controleren of de waarde '**CookieBasedAffinity**' is ingesteld op *ingeschakeld*onder '**backendHttpSettingsCollection**' door een van de volgende methoden te gebruiken:

- [Get-AzApplicationGatewayBackendHttpSetting](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhttpsetting) uitvoeren in Power shell
- Bekijk het JSON-bestand met behulp van de sjabloon Azure Resource Manager

```
"cookieBasedAffinity": "Enabled", 
```

### <a name="the-application-cannot-handle-cookie-based-affinity"></a>De toepassing kan de op cookies gebaseerde affiniteit niet afhandelen

#### <a name="cause"></a>Oorzaak

De toepassings gateway kan alleen op sessies gebaseerde affiniteit uitvoeren met behulp van een cookie.

#### <a name="workaround"></a>Tijdelijke oplossing

Als de toepassing geen affiniteit op basis van cookies kan verwerken, moet u een externe of interne Azure-load balancer of een andere oplossing van derden gebruiken.

### <a name="application-is-using-cookie-based-affinity-but-requests-still-bouncing-between-back-end-servers"></a>De toepassing maakt gebruik van affiniteit op basis van cookies, maar aanvragen worden nog steeds stuiteren tussen back-endservers

#### <a name="symptom"></a>Symptoom

U hebt de instelling voor affiniteit op basis van cookies ingeschakeld, wanneer u toegang krijgt tot de Application Gateway met behulp van een korte naam-URL in Internet Explorer, bijvoorbeeld: [http://website](http://website/) , de aanvraag is nog steeds stuiteren tussen back-endservers.

Volg de instructies om dit probleem te identificeren:

1. Neem een webdebugger tracering op de client, die verbinding maakt met de toepassing achter de Application Gateway (we gebruiken Fiddler in dit voor beeld).
    **Tip** Als u niet weet hoe u de Fiddler moet gebruiken, schakelt u de optie '**Ik wil netwerk verkeer verzamelen en analyseren met Web debugger**' onderaan.

2. Controleer en analyseer de sessie Logboeken om te bepalen of de cookies van de client de ARRAffinity-details hebben. Als u de details van de ARRAffinity, zoals "**ARRAffinity =** *ARRAffinityValue*" niet in de cookieset vindt, betekent dit dat de client niet antwoordt op de ARRA cookie, die wordt opgegeven door de Application Gateway.
    Bijvoorbeeld:

    ![problemen oplossen-sessie-affiniteit-problemen-3](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-3.png)

    ![problemen oplossen-sessie-affiniteit-problemen-4](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-4.png)

De toepassing blijft proberen de cookie op elke aanvraag in te stellen totdat het antwoord wordt ontvangen.

#### <a name="cause"></a>Oorzaak

Dit probleem doet zich voor omdat Internet Explorer en andere browsers de cookie niet kunnen opslaan of gebruiken met een korte naam-URL.

#### <a name="resolution"></a>Oplossing

U kunt dit probleem oplossen door via een FQDN toegang te zoeken tot Application Gateway. Gebruik bijvoorbeeld [http://website.com](https://website.com/) of [http://appgw.website.com](http://appgw.website.com/) .

## <a name="additional-logs-to-troubleshoot"></a>Aanvullende Logboeken om problemen op te lossen

U kunt aanvullende logboeken verzamelen en deze analyseren om de problemen te verhelpen die betrekking hebben op cookie-gebaseerde sessie affiniteit

### <a name="analyze-application-gateway-logs"></a>Application Gateway-logboeken analyseren

Volg de instructies om de Application Gateway-logboeken te verzamelen:

Logboekregistratie inschakelen via de Azure-portal

1. Zoek in het [Azure Portal](https://portal.azure.com/)uw resource en klik vervolgens op **Diagnostische logboeken**.

   Voor Application Gateway zijn drie logboeken beschikbaar: toegangs logboek, prestatie logboek, firewall logboek

2. Klik op **diagnostiek inschakelen**om gegevens te verzamelen.

   ![problemen oplossen-sessie-affiniteit-problemen-5](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-5.png)

3. De Blade **instellingen voor diagnostische gegevens** bevat de instellingen voor de diagnostische Logboeken. In dit voor beeld slaat Log Analytics de logboeken op. Klik op **configureren** onder **log Analytics** om uw werk ruimte in te stellen. U kunt ook Event Hubs en een opslagaccount gebruiken om de diagnostische logboeken op te slaan.

   ![problemen oplossen-sessie-affiniteit-problemen-6](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-6.png)

4. Bevestig de instellingen en klik vervolgens op **Opslaan**.

   ![problemen oplossen-sessie-affiniteit-problemen-7](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-7.png)

#### <a name="view-and-analyze-the-application-gateway-access-logs"></a>De Application Gateway Access-logboeken weer geven en analyseren

1. Selecteer in de Azure Portal onder de Application Gateway resource weergave **Diagnostische logboeken** in het gedeelte **bewaking** .

   ![problemen oplossen-sessie-affiniteit-problemen-8](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-8.png)

2. Klik aan de rechter kant op '**ApplicationGatewayAccessLog**' in de vervolg keuzelijst onder **logboek categorieën.**  

   ![problemen oplossen-sessie-affiniteit-problemen-9](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-9.png)

3. Klik in de Application Gateway Access-logboek lijst op het logboek dat u wilt analyseren en exporteren, en exporteer vervolgens het JSON-bestand.

4. Converteer het JSON-bestand dat u in stap 3 hebt geëxporteerd naar een CSV-bestand en bekijk ze in Excel, Power BI of een ander hulp programma voor gegevens visualisatie.

5. Controleer de volgende gegevens:

- **Client**: dit is het IP-adres van de client van de client die verbinding maakt.
- **ClientPort** : dit is de bron poort van de client die verbinding maakt voor de aanvraag.
- **RequestQuery** : Dit geeft de doel server aan waarop de aanvraag is ontvangen.
- **Server-gerouteerd**: exemplaar van back-end-pool dat de aanvraag is ontvangen.
- **X-AzureApplicationGateway-log-id**: correlatie-id die voor de aanvraag wordt gebruikt. Het kan worden gebruikt om problemen met verkeer op de back-endservers op te lossen. Bijvoorbeeld: X-AzureApplicationGateway-CACHE-HIT = 0 & SERVER ROUTEed = 10.0.2.4.

  - **Server-status**: http-antwoord code die Application Gateway ontvangen van de back-end.

  ![problemen oplossen-sessie-affiniteit-problemen-11](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-11.png)

Als u ziet dat er twee items afkomstig zijn uit dezelfde client en client poort en worden deze naar dezelfde back-endserver verzonden, betekent dit dat de Application Gateway correct is geconfigureerd.

Als u ziet dat er twee items afkomstig zijn uit dezelfde client-en client poort, en ze worden verzonden naar de verschillende back-endservers, betekent dit dat de aanvraag wordt gestuiterd tussen back-endservers, de optie**toepassing maakt gebruik van op cookies gebaseerde affiniteit, maar aanvragen die nog steeds tussen back-endservers worden gestuiterd**, onderaan om de problemen op te lossen.

### <a name="use-web-debugger-to-capture-and-analyze-the-http-or-https-traffics"></a>Webdebugger gebruiken om de HTTP-of HTTPS-verkeer vast te leggen en te analyseren

Hulpprogram ma's voor fout opsporing, zoals Fiddler, kunnen u helpen bij het opsporen van fouten in webtoepassingen door netwerk verkeer tussen het internet en de test computers vast te leggen. Met deze hulpprogram ma's kunt u inkomende en uitgaande gegevens controleren wanneer de browser deze ontvangt/verzendt. Fiddler, in dit voor beeld, beschikt over de optie HTTP replay die u kan helpen bij het oplossen van problemen aan de client zijde met webtoepassingen, met name voor verificatie soort problemen.

Gebruik de webdebugger van uw keuze. In dit voor beeld gebruiken we Fiddler om http-of HTTPS-verkeer vast te leggen en te analyseren, volgt u de instructies:

1. Down load het Fiddler-hulp programma op <https://www.telerik.com/download/fiddler>.

    > [!NOTE]
    > Kies Fiddler4 als op de opname computer .NET 4 is geïnstalleerd. Kies anders Fiddler2.

2. Klik met de rechter muisknop op het uitvoer bare installatie programma en voer als Administrator uit om de installatie uit te voeren.

    ![problemen oplossen-sessie-affiniteit-problemen-12](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-12.png)

3. Wanneer u Fiddler opent, wordt het vastleggen van verkeer automatisch gestart (Let op de opname in de linkerbenedenhoek). Druk op F12 om het vastleggen van verkeer te starten of te stoppen.

    ![problemen oplossen-sessie-affiniteit-problemen-13](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-13.png)

4. Waarschijnlijk bent u geïnteresseerd in het ontsleutelen van HTTPS-verkeer en kunt u HTTPS-ontsleuteling inschakelen door **extra** > **Opties Fiddler**te selecteren en het selectie vakje **HTTPS-verkeer versleutelen**in te scha kelen.

    ![problemen oplossen-sessie-affiniteit-problemen-14](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-14.png)

5. U kunt vorige niet-gerelateerde sessies verwijderen voordat u het probleem reproduceert door te klikken op **X** (pictogram) > **Alles verwijderen** als scherm afbeelding: 

    ![problemen oplossen-sessie-affiniteit-problemen-15](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-15.png)

6. Wanneer u het probleem hebt opgelost, slaat u het bestand voor controle op door **bestand** te selecteren >  > alle sessies op te **slaan** **..** . 

    ![problemen oplossen-sessie-affiniteit-problemen-16](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-16.png)

7. Controleer en analyseer de sessie Logboeken om te bepalen wat het probleem is.

    Voor voor beelden:

- **Voor beeld A:** U vindt een sessie logboek dat de aanvraag van de client wordt verzonden en gaat naar het open bare IP-adres van de Application Gateway, klikt u op dit logboek om de details weer te geven.  Aan de rechter kant ziet u de gegevens in het onderste vak wat het Application Gateway naar de client retourneert. Selecteer het tabblad RAW en bepaal of de client een '**set-cookie: ARRAffinity =** *ARRAffinityValue*' ontvangt. Als er geen cookie is, wordt sessie affiniteit niet ingesteld, of wordt de Application Gateway geen cookie opnieuw op de client toegepast.

   > [!NOTE]
   > Deze ARRAffinity waarde is de cookie-id, die de Application Gateway stelt dat de client naar een bepaalde back-endserver moet worden verzonden.

   ![problemen oplossen-sessie-affiniteit-problemen-17](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-17.png)

- **Voor beeld B:** Het volgende sessie logboek gevolgd door het vorige is de client reageert terug te vallen op de Application Gateway, die de ARRAAFFINITY heeft ingesteld. Als de ARRAffinity cookie-id overeenkomt, moet het pakket worden verzonden naar dezelfde back-endserver die eerder is gebruikt. Controleer de volgende verschillende regels http-communicatie om te controleren of de ARRAffinity cookie van de client wordt gewijzigd.

   ![problemen oplossen-sessie-affiniteit-problemen-18](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-18.png)

> [!NOTE]
> Voor dezelfde communicatie sessie moet het cookie niet worden gewijzigd. Schakel het bovenste vak aan de rechter kant in, selecteer het tabblad ' cookies ' om te zien of de client de cookie gebruikt en terugstuurt naar de Application Gateway. Als dat niet het geval is, houdt de client browser geen gebruik van de cookie voor conversaties. Soms is de client mogelijk.

 

## <a name="next-steps"></a>Volgende stappen

Als de voor gaande stappen het probleem niet oplossen, opent u een [ondersteunings ticket](https://azure.microsoft.com/support/options/).
