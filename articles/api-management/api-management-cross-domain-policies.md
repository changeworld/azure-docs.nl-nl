---
title: Azure API Management-beleid voor meerdere domeinen | Microsoft Docs
description: Meer informatie over het interdomeinbeveiligingsmodel dat beschikbaar is voor gebruik in azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 7689d277-8abe-472a-a78c-e6d4bd43455d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: b72abf4e208c57987375a105865046f194460058
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79265985"
---
# <a name="api-management-cross-domain-policies"></a>API Management-beleid voor meerdere domeinen
In dit onderwerp vindt u een verwijzing naar de volgende API Management-beleids regels. Zie [beleid in API Management](https://go.microsoft.com/fwlink/?LinkID=398186)voor meer informatie over het toevoegen en configureren van beleid.

## <a name="CrossDomainPolicies"></a>Beleid voor meerdere domeinen

- [Interdomein-aanroepen toestaan](api-management-cross-domain-policies.md#AllowCrossDomainCalls) : maakt de API toegankelijk vanuit Adobe Flash en micro soft Silverlight-clients op basis van een browser.
- [Cors](api-management-cross-domain-policies.md#CORS) -voegt cross-Origin resource SHARING (CORS)-ondersteuning toe aan een bewerking of een API voor het toestaan van interdomein-aanroepen vanuit clients op basis van de browser.
- [Jsonp](api-management-cross-domain-policies.md#JSONP) : voegt JSON met opvulling (Jsonp)-ondersteuning toe aan een bewerking of een API voor het toestaan van cross-domein aanroepen vanuit Java script-clients op basis van een browser.

## <a name="AllowCrossDomainCalls"></a>Interdomein-aanroepen toestaan
Gebruik het `cross-domain`-beleid om de API toegankelijk te maken vanuit Adobe Flash en micro soft Silverlight browser-gebaseerde clients.

### <a name="policy-statement"></a>Beleids verklaring

```xml
<cross-domain>
    <!-Policy configuration is in the Adobe cross-domain policy file format,
        see https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html-->
</cross-domain>
```

### <a name="example"></a>Voorbeeld

```xml
<cross-domain>
    <cross-domain>
        <allow-http-request-headers-from domain='*' headers='*' />
    </cross-domain>
</cross-domain>
```

### <a name="elements"></a>Elementen

|Naam|Beschrijving|Vereist|
|----------|-----------------|--------------|
|Kruis domein|Hoofd element. Onderliggende elementen moeten voldoen aan de [Adobe Cross-Domain policy file-specificatie](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html).|Ja|

### <a name="usage"></a>Gebruik
Dit beleid kan worden gebruikt in de volgende beleids [secties](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [bereiken](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

- **Beleids secties:** binnenkomend
- **Beleids bereik:** alle bereiken

## <a name="CORS"></a>VOORBEREIDENDE
Het `cors` beleid voegt ondersteuning voor het door voeren van cross-Origin Resource Sharing (CORS) toe aan een bewerking of een API voor het toestaan van interdomein-aanroepen vanuit clients op basis van een browser.

Met CORS kunnen een browser en een server communiceren en bepalen of specifieke cross-Origin-aanvragen (XMLHttpRequests-aanroepen van Java script op een webpagina naar andere domeinen) al dan niet mogen worden uitgevoerd. Dit biedt meer flexibiliteit dan alleen het toestaan van niet-oorspronkelijke aanvragen, maar is veiliger dan het toestaan van alle cross-Origin-aanvragen.

### <a name="policy-statement"></a>Beleids verklaring

```xml
<cors allow-credentials="false|true">
    <allowed-origins>
        <origin>origin uri</origin>
    </allowed-origins>
    <allowed-methods preflight-result-max-age="number of seconds">
        <method>http verb</method>
    </allowed-methods>
    <allowed-headers>
        <header>header name</header>
    </allowed-headers>
    <expose-headers>
        <header>header name</header>
    </expose-headers>
</cors>
```

### <a name="example"></a>Voorbeeld
In dit voor beeld wordt gedemonstreerd hoe aanvragen voorafgaand aan de vlucht worden ondersteund, zoals gebruikers met aangepaste kopteksten of andere methoden dan GET en POST. Gebruik de secties `allowed-methods` en `allowed-headers`, zoals wordt weer gegeven in het volgende voor beeld, ter ondersteuning van aangepaste headers en extra HTTP-termen.

```xml
<cors allow-credentials="true">
    <allowed-origins>
        <!-- Localhost useful for development -->
        <origin>http://localhost:8080/</origin>
        <origin>http://example.com/</origin>
    </allowed-origins>
    <allowed-methods preflight-result-max-age="300">
        <method>GET</method>
        <method>POST</method>
        <method>PATCH</method>
        <method>DELETE</method>
    </allowed-methods>
    <allowed-headers>
        <!-- Examples below show Azure Mobile Services headers -->
        <header>x-zumo-installation-id</header>
        <header>x-zumo-application</header>
        <header>x-zumo-version</header>
        <header>x-zumo-auth</header>
        <header>content-type</header>
        <header>accept</header>
    </allowed-headers>
    <expose-headers>
        <!-- Examples below show Azure Mobile Services headers -->
        <header>x-zumo-installation-id</header>
        <header>x-zumo-application</header>
    </expose-headers>
</cors>
```

### <a name="elements"></a>Elementen

|Naam|Beschrijving|Vereist|Standaard|
|----------|-----------------|--------------|-------------|
|voorbereidende|Hoofd element.|Ja|N.v.t.|
|toegestaan-oorsprong|Bevat `origin` elementen die de toegestane oorsprongen voor meerdere domein aanvragen beschrijven. `allowed-origins` kunnen een enkel `origin`-element bevatten waarin wordt aangegeven dat `*` een wille keurige oorsprong of een of meer `origin`-elementen met een URI mag bevatten.|Ja|N.v.t.|
|Oorsprong|De waarde kan `*` zijn om alle oorsprongen toe te staan, of een URI die één oorsprong opgeeft. De URI moet een schema, host en poort bevatten.|Ja|Als de poort wordt wegge laten in een URI, wordt poort 80 gebruikt voor HTTP en poort 443 wordt gebruikt voor HTTPS.|
|toegestane methoden|Dit element is vereist als andere methoden dan GET of POST zijn toegestaan. Bevat `method` elementen waarmee de ondersteunde HTTP-woorden worden opgegeven. De waarde `*` geeft aan dat alle methoden zijn.|Nee|Als deze sectie niet aanwezig is, worden GET en POST ondersteund.|
|method|Hiermee geeft u een HTTP-woord op.|Er is ten minste één `method` element vereist als de sectie `allowed-methods` aanwezig is.|N.v.t.|
|toegestaan-headers|Dit element bevat `header` elementen die namen van de headers opgeven die in de aanvraag kunnen worden opgenomen.|Nee|N.v.t.|
|weer geven-headers|Dit element bevat `header` elementen die namen van de headers opgeven die toegankelijk zijn voor de client.|Nee|N.v.t.|
|koptekst|Hiermee geeft u de naam van een header.|Er is ten minste één `header` element vereist in `allowed-headers` of `expose-headers` als de sectie aanwezig is.|N.v.t.|

### <a name="attributes"></a>Kenmerken

|Naam|Beschrijving|Vereist|Standaard|
|----------|-----------------|--------------|-------------|
|allow-credentials|De `Access-Control-Allow-Credentials` header in het Preflight-antwoord wordt ingesteld op de waarde van dit kenmerk en is van invloed op de mogelijkheid van de client om referenties in meerdere domein aanvragen in te dienen.|Nee|onwaar|
|preflight-result-max-age|De `Access-Control-Max-Age` header in het Preflight-antwoord wordt ingesteld op de waarde van dit kenmerk en is van invloed op de mogelijkheid van de gebruikers agent om een reactie in de cache op te slaan.|Nee|0|

### <a name="usage"></a>Gebruik
Dit beleid kan worden gebruikt in de volgende beleids [secties](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [bereiken](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

- **Beleids secties:** binnenkomend
- **Beleids bereik:** alle bereiken

## <a name="JSONP"></a>JSONP
Het `jsonp`-beleid voegt JSON met opvulling (JSONP)-ondersteuning toe aan een bewerking of een API voor het toestaan van cross-domein aanroepen vanuit Java script-clients op basis van een browser. JSONP is een methode die wordt gebruikt in Java script-Program ma's om gegevens op te vragen van een server in een ander domein. JSONP omzeilt de beperking die wordt afgedwongen door de meeste webbrowsers, waarbij toegang tot webpagina's moet zich in hetzelfde domein bevallen.

### <a name="policy-statement"></a>Beleids verklaring

```xml
<jsonp callback-parameter-name="callback function name" />
```

### <a name="example"></a>Voorbeeld

```xml
<jsonp callback-parameter-name="cb" />
```

Als u de methode aanroept zonder de para meter call back? CB = XXX, wordt een ongewone JSON geretourneerd (zonder een functie aanroep wrapper).

Als u de call back-para meter toevoegt `?cb=XXX` wordt een JSONP resultaat geretourneerd, waarbij de oorspronkelijke JSON-resultaten rond de call back-functie, zoals `XYZ('<json result goes here>');`

### <a name="elements"></a>Elementen

|Naam|Beschrijving|Vereist|
|----------|-----------------|--------------|
|jsonp|Hoofd element.|Ja|

### <a name="attributes"></a>Kenmerken

|Naam|Beschrijving|Vereist|Standaard|
|----------|-----------------|--------------|-------------|
|call back-para meter-name|De Java script-functie aanroep van meerdere domeinen, voorafgegaan door de Fully Qualified Domain Name waarbij de functie zich bevindt.|Ja|N.v.t.|

### <a name="usage"></a>Gebruik
Dit beleid kan worden gebruikt in de volgende beleids [secties](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [bereiken](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

- **Beleids secties:** uitgaand
- **Beleids bereik:** alle bereiken

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het gebruik van beleid:

+ [Beleid in API Management](api-management-howto-policies.md)
+ [Api's transformeren](transform-api.md)
+ [Beleids verwijzing](api-management-policy-reference.md) voor een volledige lijst met beleids instructies en hun instellingen
+ [Voor beelden van beleid](policy-samples.md)
