---
title: Beleid voor Azure API Management-trans formatie | Microsoft Docs
description: Meer informatie over de transformatie beleidsregels die beschikbaar zijn voor gebruik in azure API Management.
services: api-management
documentationcenter: ''
author: miaojiang
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/11/2019
ms.author: apimpm
ms.openlocfilehash: c26cca40b0bf6d02bcec09945043f4ba854fa8e9
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79249462"
---
# <a name="api-management-transformation-policies"></a>API Management transformatie beleid
In dit onderwerp vindt u een verwijzing naar de volgende API Management-beleids regels. Zie [beleid in API Management](https://go.microsoft.com/fwlink/?LinkID=398186)voor meer informatie over het toevoegen en configureren van beleid.

##  <a name="TransformationPolicies"></a>Transformatie beleid

-   [JSON converteren naar XML](api-management-transformation-policies.md#ConvertJSONtoXML) : Hiermee wordt de aanvraag of antwoord tekst van JSON naar XML geconverteerd.

-   [CONVERTEER XML naar JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) -de aanvraag of antwoord tekst van XML naar JSON wordt geconverteerd.

-   [Teken reeks in hoofd tekst zoeken en vervangen](api-management-transformation-policies.md#Findandreplacestringinbody) : zoekt naar een subtekenreeks voor aanvragen of antwoorden en vervangt deze door een andere subtekenreeks.

-   [Masker url's in content](api-management-transformation-policies.md#MaskURLSContent) -rewrites (maskers) in de hoofd tekst van het antwoord, zodat ze verwijzen naar de equivalente koppeling via de gateway.

-   [Backend-service instellen](api-management-transformation-policies.md#SetBackendService) : Hiermee wordt de back-end-service voor een binnenkomende aanvraag gewijzigd.

-   [Hoofd tekst instellen](api-management-transformation-policies.md#SetBody) : Hiermee stelt u de bericht tekst voor binnenkomende en uitgaande aanvragen.

-   [Set http header](api-management-transformation-policies.md#SetHTTPheader) : wijst een waarde toe aan een bestaande reactie en/of aanvraag header of voegt een nieuwe reactie en/of een aanvraag header toe.

-   [Query teken reeks parameter instellen](api-management-transformation-policies.md#SetQueryStringParameter) : toevoegen, vervangt waarde van of verwijdert query teken reeks parameter.

-   [URL herschrijven](api-management-transformation-policies.md#RewriteURL) : converteert een aanvraag-URL uit de open bare vorm naar het formulier dat wordt verwacht door de webservice.

-   [XML transformeren met behulp van een XSLT](api-management-transformation-policies.md#XSLTransform) : past een XSL-trans formatie toe op XML in de hoofd tekst van de aanvraag of het antwoord.

##  <a name="ConvertJSONtoXML"></a>JSON naar XML converteren
 Het `json-to-xml`-beleid converteert een aanvraag of antwoord tekst van JSON naar XML.

### <a name="policy-statement"></a>Beleids verklaring

```xml
<json-to-xml apply="always | content-type-json" consider-accept-header="true | false" parse-date="true | false"/>
```

### <a name="example"></a>Voorbeeld

```xml
<policies>
    <inbound>
        <base />
    </inbound>
    <outbound>
        <base />
        <json-to-xml apply="always" consider-accept-header="false" parse-date="false"/>
    </outbound>
</policies>
```

### <a name="elements"></a>Elementen

|Naam|Beschrijving|Vereist|
|----------|-----------------|--------------|
|json-to-xml|Hoofd element.|Ja|

### <a name="attributes"></a>Kenmerken

|Naam|Beschrijving|Vereist|Standaard|
|----------|-----------------|--------------|-------------|
|toepassen|Het kenmerk moet worden ingesteld op een van de volgende waarden.<br /><br /> -altijd-altijd conversie Toep assen.<br />-content-type-JSON-Convert alleen als de content-type-header van het antwoord duidt op de aanwezigheid van JSON.|Ja|N.v.t.|
|Overweeg-Accept-header|Het kenmerk moet worden ingesteld op een van de volgende waarden.<br /><br /> -True-conversie Toep assen als JSON is aangevraagd in de koptekst van de Accept-aanvraag.<br />-ONWAAR-altijd conversie Toep assen.|Nee|true|
|parseren datum|Wanneer ingesteld op `false` datum waarden worden alleen tijdens de trans formatie gekopieerd|Nee|true|

### <a name="usage"></a>Gebruik
 Dit beleid kan worden gebruikt in de volgende beleids [secties](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [bereiken](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Beleids secties:** inkomend, uitgaand, op fouten

-   **Beleids bereik:** alle bereiken

##  <a name="ConvertXMLtoJSON"></a>XML converteren naar JSON
 Het `xml-to-json` beleid converteert een aanvraag of antwoord tekst van XML naar JSON. Dit beleid kan worden gebruikt om Api's te moderniseren op basis van webservices met alleen XML-back-end.

### <a name="policy-statement"></a>Beleids verklaring

```xml
<xml-to-json kind="javascript-friendly | direct" apply="always | content-type-xml" consider-accept-header="true | false"/>
```

### <a name="example"></a>Voorbeeld

```xml
<policies>
    <inbound>
        <base />
    </inbound>
    <outbound>
        <base />
        <xml-to-json kind="direct" apply="always" consider-accept-header="false" />
    </outbound>
</policies>
```

### <a name="elements"></a>Elementen

|Naam|Beschrijving|Vereist|
|----------|-----------------|--------------|
|xml-to-json|Hoofd element.|Ja|

### <a name="attributes"></a>Kenmerken

|Naam|Beschrijving|Vereist|Standaard|
|----------|-----------------|--------------|-------------|
|type|Het kenmerk moet worden ingesteld op een van de volgende waarden.<br /><br /> -Java script-vriendelijk: de geconverteerde JSON heeft een formulier vriendelijk voor Java script-ontwikkel aars.<br />-direct-de geconverteerde JSON weerspiegelt de oorspronkelijke XML-document structuur.|Ja|N.v.t.|
|toepassen|Het kenmerk moet worden ingesteld op een van de volgende waarden.<br /><br /> -altijd-Convert altijd.<br />-content-type-XML: alleen converteren als de content-type-header van het antwoord de aanwezigheid van XML aangeeft.|Ja|N.v.t.|
|Overweeg-Accept-header|Het kenmerk moet worden ingesteld op een van de volgende waarden.<br /><br /> -True-de conversie Toep assen als XML is aangevraagd in de header geaccepteerd van aanvraag.<br />-ONWAAR-altijd conversie Toep assen.|Nee|true|

### <a name="usage"></a>Gebruik
 Dit beleid kan worden gebruikt in de volgende beleids [secties](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [bereiken](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Beleids secties:** inkomend, uitgaand, op fouten

-   **Beleids bereik:** alle bereiken

##  <a name="Findandreplacestringinbody"></a>Teken reeks in hoofd tekst zoeken en vervangen
 Het `find-and-replace`-beleid vindt een subtekenreeks voor aanvragen of antwoorden en vervangt deze door een andere subtekenreeks.

### <a name="policy-statement"></a>Beleids verklaring

```xml
<find-and-replace from="what to replace" to="replacement" />
```

### <a name="example"></a>Voorbeeld

```xml
<find-and-replace from="notebook" to="laptop" />
```

### <a name="elements"></a>Elementen

|Naam|Beschrijving|Vereist|
|----------|-----------------|--------------|
|zoeken en vervangen|Hoofd element.|Ja|

### <a name="attributes"></a>Kenmerken

|Naam|Beschrijving|Vereist|Standaard|
|----------|-----------------|--------------|-------------|
|from|De teken reeks waarnaar moet worden gezocht.|Ja|N.v.t.|
|tot|De vervangende teken reeks. Een teken reeks met een lengte van nul opgeven om de zoek teken reeks te verwijderen.|Ja|N.v.t.|

### <a name="usage"></a>Gebruik
 Dit beleid kan worden gebruikt in de volgende beleids [secties](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [bereiken](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Beleids secties:** inkomend, uitgaand, back-end, op fout

-   **Beleids bereik:** alle bereiken

##  <a name="MaskURLSContent"></a>Url's in inhoud maskeren
 De koppelingen in de hoofd tekst van het `redirect-content-urls` beleid worden opnieuw geschreven (maskers), zodat ze verwijzen naar de equivalente koppeling via de gateway. Gebruik in de sectie uitgaand om de koppelingen naar de tekst van een antwoord opnieuw te schrijven om ze te laten verwijzen naar de gateway. Gebruik in de sectie binnenkomend voor een tegenovergesteld effect.

> [!NOTE]
>  Dit beleid wijzigt geen header waarden, zoals `Location` kopteksten. Gebruik het [set-header](api-management-transformation-policies.md#SetHTTPheader) beleid om de waarden van de header te wijzigen.

### <a name="policy-statement"></a>Beleids verklaring

```xml
<redirect-content-urls />
```

### <a name="example"></a>Voorbeeld

```xml
<redirect-content-urls />
```

### <a name="elements"></a>Elementen

|Naam|Beschrijving|Vereist|
|----------|-----------------|--------------|
|redirect-content-urls|Hoofd element.|Ja|

### <a name="usage"></a>Gebruik
 Dit beleid kan worden gebruikt in de volgende beleids [secties](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [bereiken](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Beleids secties:** inkomend, uitgaand

-   **Beleids bereik:** alle bereiken

##  <a name="SetBackendService"></a>Backend-service instellen
 Gebruik het `set-backend-service`-beleid om een binnenkomende aanvraag om te leiden naar een andere backend dan de back-end die is opgegeven in de API-instellingen voor die bewerking. Dit beleid wijzigt de back-end-service basis-URL van de inkomende aanvraag naar de waarde die is opgegeven in het beleid.

### <a name="policy-statement"></a>Beleids verklaring

```xml
<set-backend-service base-url="base URL of the backend service" />
```

of

```xml
<set-backend-service backend-id="identifier of the backend entity specifying base URL of the backend service" />
```

> [!NOTE]
> Back-end-entiteiten kunnen worden beheerd via beheer- [API](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/backend) en [Power shell](https://www.powershellgallery.com/packages?q=apimanagement).

### <a name="example"></a>Voorbeeld

```xml
<policies>
    <inbound>
        <choose>
            <when condition="@(context.Request.Url.Query.GetValueOrDefault("version") == "2013-05")">
                <set-backend-service base-url="http://contoso.com/api/8.2/" />
            </when>
            <when condition="@(context.Request.Url.Query.GetValueOrDefault("version") == "2014-03")">
                <set-backend-service base-url="http://contoso.com/api/9.1/" />
            </when>
        </choose>
        <base />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```
In dit voor beeld routeren het beleid voor het instellen van de back-service routeert aanvragen op basis van de versie waarde die in de query reeks is door gegeven aan een andere back-end-service dan die in de API is opgegeven.

De basis-URL van de back-end-service wordt aanvankelijk afgeleid van de API-instellingen. De aanvraag-URL `https://contoso.azure-api.net/api/partners/15?version=2013-05&subscription-key=abcdef` wordt dan `http://contoso.com/api/10.4/partners/15?version=2013-05&subscription-key=abcdef` waarbij `http://contoso.com/api/10.4/` de back-end-service-URL is die is opgegeven in de API-instellingen.

Wanneer de [<\>](api-management-advanced-policies.md#choose) beleids verklaring kiezen wordt toegepast, kan de basis-URL van de back-end opnieuw worden gewijzigd in `http://contoso.com/api/8.2` of `http://contoso.com/api/9.1`, afhankelijk van de waarde van de query parameter voor versie aanvragen. Als de waarde bijvoorbeeld `"2013-15"`, wordt de uiteindelijke aanvraag-URL `http://contoso.com/api/8.2/partners/15?version=2013-05&subscription-key=abcdef`.

Als verdere trans formatie van de aanvraag gewenst is, kan er een ander [transformatie beleid](api-management-transformation-policies.md#TransformationPolicies) worden gebruikt. Als u bijvoorbeeld de para meter voor de versie query wilt verwijderen en de aanvraag wordt doorgestuurd naar een specifieke versie van de back-end, kunt u het beleid voor het instellen van de [query reeks parameter](api-management-transformation-policies.md#SetQueryStringParameter) gebruiken om het kenmerk voor de nu redundante versie te verwijderen.

### <a name="example"></a>Voorbeeld

```xml
<policies>
    <inbound>
        <set-backend-service backend-id="my-sf-service" sf-partition-key="@(context.Request.Url.Query.GetValueOrDefault("userId","")" sf-replica-type="primary" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```
In dit voor beeld stuurt het beleid de aanvraag naar een service Fabric-back-end met behulp van de query-teken reeks van de gebruikers-id als partitie sleutel en gebruikt de primaire replica van de partitie.

### <a name="elements"></a>Elementen

|Naam|Beschrijving|Vereist|
|----------|-----------------|--------------|
|set-backend-service|Hoofd element.|Ja|

### <a name="attributes"></a>Kenmerken

|Naam|Beschrijving|Vereist|Standaard|
|----------|-----------------|--------------|-------------|
|basis-URL|Basis-URL van nieuwe back-end-service.|Er moet een van `base-url` of `backend-id` aanwezig zijn.|N.v.t.|
|back-end-id|De id van de back-end waarnaar moet worden doorgestuurd. (Back-upentiteiten worden beheerd via [API](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/backend) en [Power shell](https://www.powershellgallery.com/packages?q=apimanagement).)|Er moet een van `base-url` of `backend-id` aanwezig zijn.|N.v.t.|
|SF-partitie-Key|Alleen van toepassing wanneer de back-end een Service Fabric-service is en is opgegeven met behulp van back-end-id. Wordt gebruikt voor het omzetten van een specifieke partitie van de service voor naam omzetting.|Nee|N.v.t.|
|sf-replica-type|Alleen van toepassing wanneer de back-end een Service Fabric-service is en is opgegeven met behulp van back-end-id. Hiermee wordt bepaald of de aanvraag naar de primaire of secundaire replica van een partitie moet gaan. |Nee|N.v.t.|
|EB-oplossen-voor waarde|Alleen van toepassing wanneer de back-end een Service Fabric-service is. Voor waarde waarmee wordt aangegeven of de aanroep van Service Fabric back-end moet worden herhaald met de nieuwe oplossing.|Nee|N.v.t.|
|SF-service-instance-name|Alleen van toepassing wanneer de back-end een Service Fabric-service is. Hiermee kunnen service-exemplaren tijdens runtime worden gewijzigd. |Nee|N.v.t.|
|SF-listener-naam|Alleen van toepassing wanneer de back-end een Service Fabric-service is en is opgegeven met behulp van back-end-id. Met Service Fabric Reliable Services kunt u meerdere listeners in een service maken. Dit kenmerk wordt gebruikt om een specifieke listener te selecteren wanneer een backend-betrouw bare service meer dan één listener heeft. Als dit kenmerk niet is opgegeven, probeert API Management een listener zonder naam te gebruiken. Een listener zonder naam is gebruikelijk voor Reliable Services die slechts één listener hebben. |Nee|N.v.t.|

### <a name="usage"></a>Gebruik
 Dit beleid kan worden gebruikt in de volgende beleids [secties](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [bereiken](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Beleids secties:** inkomend, back-end

-   **Beleids bereik:** alle bereiken

##  <a name="SetBody"></a>Hoofd tekst instellen
 Gebruik het `set-body`-beleid om de bericht tekst voor binnenkomende en uitgaande aanvragen in te stellen. Voor toegang tot de bericht tekst kunt u de eigenschap `context.Request.Body` of de `context.Response.Body`gebruiken, afhankelijk van het feit of het beleid zich in de sectie binnenkomend of uitgaand bevindt.

> [!IMPORTANT]
>  Houd er rekening mee dat wanneer u de bericht tekst met `context.Request.Body` of `context.Response.Body`opent, de oorspronkelijke bericht tekst verloren gaat en moet worden ingesteld door de hoofd tekst terug in de expressie te retour neren. Als u de inhoud van de hoofd tekst wilt behouden, stelt u de para meter `preserveContent` in op `true` bij het openen van het bericht. Als `preserveContent` is ingesteld op `true` en er een andere hoofd tekst wordt geretourneerd door de expressie, wordt de geretourneerde tekst gebruikt.
>
>  Let op de volgende overwegingen wanneer u het `set-body`-beleid gebruikt.
>
> - Als u het `set-body`-beleid gebruikt om een nieuwe of bijgewerkte hoofd tekst te retour neren, hoeft u `preserveContent` niet in te stellen op `true` omdat u de nieuwe inhoud van de hoofd tekst expliciet opgeeft.
>   -   Het behouden van de inhoud van een antwoord in de inkomende pijp lijn is niet logisch omdat er nog geen antwoord is.
>   -   Het behouden van de inhoud van een aanvraag in de uitgaande pijp lijn is niet zinvol omdat de aanvraag op dit moment al naar de back-end is verzonden.
>   -   Als dit beleid wordt gebruikt wanneer er geen bericht tekst is, bijvoorbeeld in een binnenkomende GET, wordt er een uitzonde ring gegenereerd.

 Zie de secties `context.Request.Body`, `context.Response.Body`en `IMessage` in de tabel [context variabele](api-management-policy-expressions.md#ContextVariables) voor meer informatie.

### <a name="policy-statement"></a>Beleids verklaring

```xml
<set-body>new body value as text</set-body>
```

### <a name="examples"></a>Voorbeelden

#### <a name="literal-text-example"></a>Voor beeld van letterlijke tekst

```xml
<set-body>Hello world!</set-body>
```

#### <a name="example-accessing-the-body-as-a-string-note-that-we-are-preserving-the-original-request-body-so-that-we-can-access-it-later-in-the-pipeline"></a>Voor beeld van toegang tot de hoofd tekst als een teken reeks. Houd er rekening mee dat we de oorspronkelijke hoofd tekst van de aanvraag behouden, zodat we deze later in de pijp lijn kunnen openen.

```xml
<set-body>
@{ 
    string inBody = context.Request.Body.As<string>(preserveContent: true); 
    if (inBody[0] =='c') { 
        inBody[0] = 'm'; 
    } 
    return inBody; 
}
</set-body>
```

#### <a name="example-accessing-the-body-as-a-jobject-note-that-since-we-are-not-reserving-the-original-request-body-accessing-it-later-in-the-pipeline-will-result-in-an-exception"></a>Voor beeld van toegang tot de hoofd tekst als een JObject. Houd er rekening mee dat omdat de oorspronkelijke hoofd tekst van de aanvraag niet wordt gereserveerd en er later in de pijp lijn toegang toe wordt verkregen.

```xml
<set-body> 
@{ 
    JObject inBody = context.Request.Body.As<JObject>(); 
    if (inBody.attribute == <tag>) { 
        inBody[0] = 'm'; 
    } 
    return inBody.ToString(); 
} 
</set-body>

```

#### <a name="filter-response-based-on-product"></a>Antwoord filteren op basis van product
 In dit voor beeld ziet u hoe u het filteren van inhoud uitvoert door gegevens elementen te verwijderen uit het antwoord dat is ontvangen van de back-end-service wanneer u het `Starter` product gebruikt. Zie voor een demonstratie van het configureren en gebruiken van dit beleid [Cloud cover aflevering 177: meer API management functies met Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) en Fast-forward to 34:30. Begin om 31:50 om een overzicht te zien van [de donkerly Forecast API](https://developer.forecast.io/) die wordt gebruikt voor deze demo.

```xml
<!-- Copy this snippet into the outbound section to remove a number of data elements from the response received from the backend service based on the name of the api product -->
<choose>
  <when condition="@(context.Response.StatusCode == 200 && context.Product.Name.Equals("Starter"))">
    <set-body>@{
        var response = context.Response.Body.As<JObject>();
        foreach (var key in new [] {"minutely", "hourly", "daily", "flags"}) {
          response.Property (key).Remove ();
        }
        return response.ToString();
      }
    </set-body>
  </when>
</choose>
```

### <a name="using-liquid-templates-with-set-body"></a>Liquide sjablonen gebruiken met de set Body
Het `set-body`-beleid kan worden geconfigureerd voor het gebruik van de taal [Liquid](https://shopify.github.io/liquid/basics/introduction/) sjabloon om de hoofd tekst van een aanvraag of antwoord te transformeren. Dit kan zeer effectief zijn als u de indeling van uw bericht volledig wilt wijzigen.

> [!IMPORTANT]
> De implementatie van liquide middelen die worden gebruikt in het `set-body` beleidC# , wordt geconfigureerd in de modus ' mode '. Dit is met name belang rijk bij het uitvoeren van dingen als filteren. Een voor beeld: als u een datum filter gebruikt, is het gebruik van C# de behuizing en datum notatie van Pascal bijvoorbeeld vereist.
>
> {{body.foo.startDateTime| Date:"yyyyMMddTHH:mm:ddZ"}}

> [!IMPORTANT]
> Als u een juiste binding wilt maken met een XML-hoofd tekst met behulp van de vloei bare sjabloon, gebruikt u een `set-header`-beleid om het inhouds type in te stellen op Application/XML, Text/XML (of een type dat eindigt op + XML). voor een JSON-hoofd tekst moet de toepassing/JSON, Text/JSON (of een type dat eindigt op + JSON) zijn.

#### <a name="convert-json-to-soap-using-a-liquid-template"></a>JSON naar SOAP converteren met behulp van een vloeistof sjabloon
```xml
<set-body template="liquid">
    <soap:Envelope xmlns="http://tempuri.org/" xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
        <soap:Body>
            <GetOpenOrders>
                <cust>{{body.getOpenOrders.cust}}</cust>
            </GetOpenOrders>
        </soap:Body>
    </soap:Envelope>
</set-body>
```

#### <a name="transform-json-using-a-liquid-template"></a>JSON transformeren met een vloeistof sjabloon
```xml
{
"order": {
    "id": "{{body.customer.purchase.identifier}}",
    "summary": "{{body.customer.purchase.orderShortDesc}}"
    }
}
```

### <a name="elements"></a>Elementen

|Naam|Beschrijving|Vereist|
|----------|-----------------|--------------|
|set-Body|Hoofd element. Bevat de hoofd tekst of een expressie die een tekst als resultaat retourneert.|Ja|

### <a name="properties"></a>Eigenschappen

|Naam|Beschrijving|Vereist|Standaard|
|----------|-----------------|--------------|-------------|
|sjabloon|Hiermee wordt de sjabloon-modus voor het instellen van het hoofd beleid voor regels gewijzigd in. Momenteel is de enige ondersteunde waarde:<br /><br />-liquide-het hoofd beleid instellen gebruikt de vloeistof sjabloon-engine |Nee||

Voor toegang tot informatie over de aanvraag en het antwoord kan de vloeistof sjabloon worden gebonden aan een context object met de volgende eigenschappen: <br />
<pre>context.
    Request.
        Url
        Method
        OriginalMethod
        OriginalUrl
        IpAddress
        MatchedParameters
        HasBody
        ClientCertificates
        Headers

    Response.
        StatusCode
        Method
        Headers
Url.
    Scheme
    Host
    Port
    Path
    Query
    QueryString
    ToUri
    ToString

OriginalUrl.
    Scheme
    Host
    Port
    Path
    Query
    QueryString
    ToUri
    ToString
</pre>



### <a name="usage"></a>Gebruik
 Dit beleid kan worden gebruikt in de volgende beleids [secties](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [bereiken](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Beleids secties:** inkomend, uitgaand, back-end

-   **Beleids bereik:** alle bereiken

##  <a name="SetHTTPheader"></a>HTTP-header instellen
 Het `set-header` beleid wijst een waarde toe aan een bestaande reactie en/of aanvraag header of voegt een nieuwe reactie en/of een aanvraag header toe.

 Hiermee wordt een lijst met HTTP-headers in een HTTP-bericht ingevoegd. Wanneer dit beleid in een inkomende pijp lijn wordt geplaatst, worden de HTTP-headers ingesteld voor de aanvraag die wordt door gegeven aan de doel service. Wanneer dit beleid in een uitgaande pijp lijn wordt geplaatst, worden de HTTP-headers voor het antwoord dat wordt verzonden naar de client van de gateway ingesteld.

### <a name="policy-statement"></a>Beleids verklaring

```xml
<set-header name="header name" exists-action="override | skip | append | delete">
    <value>value</value> <!--for multiple headers with the same name add additional value elements-->
</set-header>
```

### <a name="examples"></a>Voorbeelden

#### <a name="example---adding-header-override-existing"></a>Voor beeld: header toevoegen, bestaande overschrijven

```xml
<set-header name="some header name" exists-action="override">
    <value>20</value>
</set-header>
```
#### <a name="example---removing-header"></a>Voor beeld: koptekst verwijderen

```xml
 <set-header name="some header name" exists-action="delete" />
```



#### <a name="forward-context-information-to-the-backend-service"></a>Context informatie door sturen naar de back-end-service
 In dit voor beeld ziet u hoe u beleid toepast op het API-niveau voor het leveren van context informatie aan de back-end-service. Zie voor een demonstratie van het configureren en gebruiken van dit beleid [Cloud cover aflevering 177: meer API management functies met Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) en Fast-forward to 10:30. Bij 12:10 is er een demo van het aanroepen van een bewerking in de ontwikkelaars Portal, waar u het beleid op het werk kunt zien.

```xml
<!-- Copy this snippet into the inbound element to forward some context information, user id and the region the gateway is hosted in, to the backend service for logging or evaluation -->
<set-header name="x-request-context-data" exists-action="override">
  <value>@(context.User.Id)</value>
  <value>@(context.Deployment.Region)</value>
</set-header>
```

 Zie [beleids expressies](api-management-policy-expressions.md) en [context variabelen](api-management-policy-expressions.md#ContextVariables)voor meer informatie.

> [!NOTE]
> Meerdere waarden van een header worden samengevoegd met een CSV-teken reeks, bijvoorbeeld: `headerName: value1,value2,value3`
>
> Uitzonde ringen bevatten gestandaardiseerde headers, die waarden:
> - kan komma's bevatten (`User-Agent`, `WWW-Authenticate`, `Proxy-Authenticate`),
> - kan date bevatten (`Cookie`, `Set-Cookie`, `Warning`),
> - contains date (`Date`, `Expires`, `If-Modified-Since`, `If-Unmodified-Since`, `Last-Modified`, `Retry-After`).
>
> In het geval van deze uitzonde ringen worden meerdere header-waarden niet samengevoegd tot één teken reeks en worden ze door gegeven als afzonderlijke headers, bijvoorbeeld: `User-Agent: value1`
>`User-Agent: value2`
>`User-Agent: value3`

### <a name="elements"></a>Elementen

|Naam|Beschrijving|Vereist|
|----------|-----------------|--------------|
|set-header|Hoofd element.|Ja|
|waarde|Hiermee geeft u de waarde van de in te stellen header op. Voeg aanvullende `value` elementen toe voor meerdere headers met dezelfde naam.|Nee|

### <a name="properties"></a>Eigenschappen

|Naam|Beschrijving|Vereist|Standaard|
|----------|-----------------|--------------|-------------|
|exists-actie|Hiermee geeft u op welke actie moet worden ondernomen wanneer de header al is opgegeven. Dit kenmerk moet een van de volgende waarden hebben.<br /><br /> -Override: vervangt de waarde van de bestaande header.<br />-Skip-vervangt niet de bestaande waarde van de header.<br />-append-de waarde wordt toegevoegd aan de bestaande waarde van de header.<br />-delete: verwijdert de header uit de aanvraag.<br /><br /> Wanneer deze instelling is ingesteld op `override` het aanmelden van meerdere vermeldingen met dezelfde naam resulteert in de header die wordt ingesteld op basis van alle vermeldingen (die meerdere keren worden weer gegeven). alleen waarden die worden weer gegeven, worden ingesteld in het resultaat.|Nee|onderdrukking|
|naam|Hiermee geeft u de naam op van de header die moet worden ingesteld.|Ja|N.v.t.|

### <a name="usage"></a>Gebruik
 Dit beleid kan worden gebruikt in de volgende beleids [secties](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [bereiken](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Beleids secties:** inkomend, uitgaand, back-end, op fout

-   **Beleids bereik:** alle bereiken

##  <a name="SetQueryStringParameter"></a>Query teken reeks parameter instellen
 Het `set-query-parameter` beleid voegt, vervangt waarde van of verwijdert een query teken reeks parameter van een aanvraag. Kan worden gebruikt om query parameters door te geven die worden verwacht door de back-end-service, die optioneel zijn of nooit aanwezig zijn in de aanvraag.

### <a name="policy-statement"></a>Beleids verklaring

```xml
<set-query-parameter name="param name" exists-action="override | skip | append | delete">
    <value>value</value> <!--for multiple parameters with the same name add additional value elements-->
</set-query-parameter>
```

### <a name="examples"></a>Voorbeelden

#### <a name="example"></a>Voorbeeld

```xml

<set-query-parameter>
  <parameter name="api-key" exists-action="skip">
    <value>12345678901</value>
  </parameter>
  <!-- for multiple parameters with the same name add additional value elements -->
</set-query-parameter>

```

#### <a name="forward-context-information-to-the-backend-service"></a>Context informatie door sturen naar de back-end-service
 In dit voor beeld ziet u hoe u beleid toepast op het API-niveau voor het leveren van context informatie aan de back-end-service. Zie voor een demonstratie van het configureren en gebruiken van dit beleid [Cloud cover aflevering 177: meer API management functies met Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) en Fast-forward to 10:30. Bij 12:10 is er een demo van het aanroepen van een bewerking in de ontwikkelaars Portal, waar u het beleid op het werk kunt zien.

```xml
<!-- Copy this snippet into the inbound element to forward a piece of context, product name in this example, to the backend service for logging or evaluation -->
<set-query-parameter name="x-product-name" exists-action="override">
  <value>@(context.Product.Name)</value>
</set-query-parameter>

```

 Zie [beleids expressies](api-management-policy-expressions.md) en [context variabelen](api-management-policy-expressions.md#ContextVariables)voor meer informatie.

### <a name="elements"></a>Elementen

|Naam|Beschrijving|Vereist|
|----------|-----------------|--------------|
|set-query-parameter|Hoofd element.|Ja|
|waarde|Hiermee geeft u de waarde van de in te stellen queryparameter op. Voeg aanvullende `value` elementen toe voor meerdere query parameters met dezelfde naam.|Ja|

### <a name="properties"></a>Eigenschappen

|Naam|Beschrijving|Vereist|Standaard|
|----------|-----------------|--------------|-------------|
|exists-actie|Hiermee geeft u op welke actie moet worden uitgevoerd wanneer de querytekenreeks al is opgegeven. Dit kenmerk moet een van de volgende waarden hebben.<br /><br /> -Override: vervangt de waarde van de bestaande para meter.<br />-Skip-vervangt niet de bestaande waarde van de query parameter.<br />-append-de waarde wordt toegevoegd aan de bestaande waarde van de query parameter.<br />-DELETE-verwijdert de query parameter uit de aanvraag.<br /><br /> Wanneer u instelt op `override` het opnemen van meerdere vermeldingen met dezelfde naam resulteert in de query parameter die wordt ingesteld op basis van alle vermeldingen (die meerdere keren worden weer gegeven). alleen waarden die worden weer gegeven, worden ingesteld in het resultaat.|Nee|onderdrukking|
|naam|Hiermee geeft u de naam op van de query parameter die moet worden ingesteld.|Ja|N.v.t.|

### <a name="usage"></a>Gebruik
 Dit beleid kan worden gebruikt in de volgende beleids [secties](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [bereiken](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Beleids secties:** inkomend, back-end

-   **Beleids bereik:** alle bereiken

##  <a name="RewriteURL"></a>URL opnieuw schrijven
 Het `rewrite-uri`-beleid converteert een aanvraag-URL uit de open bare vorm naar het formulier dat wordt verwacht door de webservice, zoals wordt weer gegeven in het volgende voor beeld.

- Open bare URL-`http://api.example.com/storenumber/ordernumber`

- Aanvraag-URL-`http://api.example.com/v2/US/hardware/storenumber&ordernumber?City&State`

  Dit beleid kan worden gebruikt wanneer een menselijke en/of browser vriendelijke URL moet worden omgezet in de URL-indeling die wordt verwacht door de webservice. Dit beleid hoeft alleen te worden toegepast wanneer een alternatieve URL-indeling wordt weer gegeven, zoals schone Url's, doorlopende url's, gebruikers vriendelijke Url's of SEO-vriendelijke Url's die louter structurele Url's zijn die geen query teken reeks bevatten en in plaats daarvan alleen het pad van de resource bevatten ( na het schema en de instantie). Dit wordt vaak gedaan voor vormgeving, bruikbaarheid of optimalisatie van zoek machines (SEO).

> [!NOTE]
>  U kunt alleen query reeks parameters toevoegen met behulp van het beleid. U kunt geen extra para meters voor het pad naar een sjabloon toevoegen in de herschrijf-URL.

### <a name="policy-statement"></a>Beleids verklaring

```xml
<rewrite-uri template="uri template" copy-unmatched-params="true | false" />
```

### <a name="example"></a>Voorbeeld

```xml
<policies>
    <inbound>
        <base />
        <rewrite-uri template="/v2/US/hardware/{storenumber}&{ordernumber}?City=city&State=state" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```
```xml
<!-- Assuming incoming request is /get?a=b&c=d and operation template is set to /get?a={b} -->
<policies>
    <inbound>
        <base />
        <rewrite-uri template="/put" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
<!-- Resulting URL will be /put?c=d -->
```
```xml
<!-- Assuming incoming request is /get?a=b&c=d and operation template is set to /get?a={b} -->
<policies>
    <inbound>
        <base />
        <rewrite-uri template="/put" copy-unmatched-params="false" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
<!-- Resulting URL will be /put -->
```

### <a name="elements"></a>Elementen

|Naam|Beschrijving|Vereist|
|----------|-----------------|--------------|
|Herschrijf-URI|Hoofd element.|Ja|

### <a name="attributes"></a>Kenmerken

|Kenmerk|Beschrijving|Vereist|Standaard|
|---------------|-----------------|--------------|-------------|
|sjabloon|De daad werkelijke webservice-URL met alle query teken reeks parameters. Bij het gebruik van expressies moet de gehele waarde een expressie zijn.|Ja|N.v.t.|
|kopiëren-niet-overeenkomende para meters|Hiermee wordt aangegeven of query parameters in de binnenkomende aanvraag die niet aanwezig zijn in de oorspronkelijke URL-sjabloon, worden toegevoegd aan de URL die is gedefinieerd door de sjabloon voor opnieuw schrijven|Nee|true|

### <a name="usage"></a>Gebruik
 Dit beleid kan worden gebruikt in de volgende beleids [secties](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [bereiken](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Beleids secties:** binnenkomend

-   **Beleids bereik:** alle bereiken

##  <a name="XSLTransform"></a>XML transformeren met behulp van een XSLT
 Het `Transform XML using an XSLT`-beleid past een XSL-trans formatie toe op XML in de aanvraag of antwoord tekst.

### <a name="policy-statement"></a>Beleids verklaring

```xml
<xsl-transform>
    <parameter name="User-Agent">@(context.Request.Headers.GetValueOrDefault("User-Agent","non-specified"))</parameter>
    <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
        <xsl:output method="xml" indent="yes" />
        <xsl:param name="User-Agent" />
        <xsl:template match="* | @* | node()">
            <xsl:copy>
                <xsl:if test="self::* and not(parent::*)">
                    <xsl:attribute name="User-Agent">
                        <xsl:value-of select="$User-Agent" />
                    </xsl:attribute>
                </xsl:if>
                <xsl:apply-templates select="* | @* | node()" />
            </xsl:copy>
        </xsl:template>
    </xsl:stylesheet>
  </xsl-transform>
```

### <a name="example"></a>Voorbeeld

```xml
<policies>
  <inbound>
      <base />
  </inbound>
  <outbound>
      <base />
      <xsl-transform>
        <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
            <xsl:output omit-xml-declaration="yes" method="xml" indent="yes" />
            <!-- Copy all nodes directly-->
            <xsl:template match="node()| @*|*">
                <xsl:copy>
                    <xsl:apply-templates select="@* | node()|*" />
                </xsl:copy>
            </xsl:template>
        </xsl:stylesheet>
    </xsl-transform>
  </outbound>
</policies>
```

### <a name="elements"></a>Elementen

|Naam|Beschrijving|Vereist|
|----------|-----------------|--------------|
|XSL-trans formatie|Hoofd element.|Ja|
|parameter|Gebruikt voor het definiëren van variabelen die in de trans formatie worden gebruikt|Nee|
|XSL: Style Sheet|Hoofd element voor opmaak modellen. Alle elementen en kenmerken die zijn gedefinieerd binnen de standaard [XSLT-specificatie](https://www.w3.org/TR/xslt)|Ja|

### <a name="usage"></a>Gebruik
 Dit beleid kan worden gebruikt in de volgende beleids [secties](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [bereiken](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Beleids secties:** inkomend, uitgaand

-   **Beleids bereik:** alle bereiken

## <a name="next-steps"></a>Volgende stappen

Zie de volgende onderwerpen voor meer informatie:

+ [Beleid in API Management](api-management-howto-policies.md)
+ [Beleids verwijzing](api-management-policy-reference.md) voor een volledige lijst met beleids instructies en hun instellingen
+ [Voor beelden van beleid](policy-samples.md)
