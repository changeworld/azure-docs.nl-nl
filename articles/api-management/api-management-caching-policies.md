---
title: Cache beleid voor Azure API Management | Microsoft Docs
description: Meer informatie over de cache beleidsregels die beschikbaar zijn voor gebruik in azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 8147199c-24d8-439f-b2a9-da28a70a890c
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/27/2018
ms.author: apimpm
ms.openlocfilehash: 06c4ede12f939e48973d3e0b502d90b848d199bb
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79280298"
---
# <a name="api-management-caching-policies"></a>Beleid voor API Management cache
In dit onderwerp vindt u een verwijzing naar de volgende API Management-beleids regels. Zie [beleid in API Management](https://go.microsoft.com/fwlink/?LinkID=398186)voor meer informatie over het toevoegen en configureren van beleid.

## <a name="CachingPolicies"></a>Cache beleidsregels

- Beleid voor antwoord caching
    - [Ophalen uit cache](api-management-caching-policies.md#GetFromCache) -cache uitvoeren en een geldig antwoord in de cache retour neren wanneer deze beschikbaar zijn.
    - [Opslaan in cache](api-management-caching-policies.md#StoreToCache) -caches op basis van de opgegeven cache beheer configuratie.
- Beleid voor waarde in cache opslaan
    - [Waarde ophalen uit cache](#GetFromCacheByKey) -een item in de cache ophalen met behulp van sleutel.
    - [Waarde voor opslaan in cache](#StoreToCacheByKey) : een item in de cache opslaan per sleutel.
    - [Waarde uit cache verwijderen](#RemoveCacheByKey) : een item in de cache verwijderen per sleutel.

## <a name="GetFromCache"></a>Ophalen uit cache
Gebruik het `cache-lookup`-beleid om de cache op te zoeken en een geldig antwoord in de cache te retour neren wanneer deze beschikbaar zijn. Dit beleid kan worden toegepast in gevallen waarin de inhoud van de reactie in een bepaalde periode statisch blijft. Antwoord caching vermindert de band breedte en de verwerkings vereisten op de back-end-webserver en verlaagt de latentie die wordt waargenomen door API-consumers.

> [!NOTE]
> Dit beleid moet een bijbehorend [Archief voor cache](api-management-caching-policies.md#StoreToCache) beleid hebben.

### <a name="policy-statement"></a>Beleids verklaring

```xml
<cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" caching-type="prefer-external | external | internal" downstream-caching-type="none | private | public" must-revalidate="true | false" allow-private-response-caching="@(expression to evaluate)">
  <vary-by-header>Accept</vary-by-header>
  <!-- should be present in most cases -->
  <vary-by-header>Accept-Charset</vary-by-header>
  <!-- should be present in most cases -->
  <vary-by-header>Authorization</vary-by-header>
  <!-- should be present when allow-private-response-caching is "true"-->
  <vary-by-header>header name</vary-by-header>
  <!-- optional, can repeated several times -->
  <vary-by-query-parameter>parameter name</vary-by-query-parameter>
  <!-- optional, can repeated several times -->
</cache-lookup>
```

### <a name="examples"></a>Voorbeelden

#### <a name="example"></a>Voorbeeld

```xml
<policies>
    <inbound>
        <base />
        <cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="none" must-revalidate="true" caching-type="internal" >
            <vary-by-query-parameter>version</vary-by-query-parameter>
        </cache-lookup>
    </inbound>
    <outbound>
        <cache-store duration="seconds" />
        <base />
    </outbound>
</policies>
```

#### <a name="example-using-policy-expressions"></a>Voor beeld van beleids expressies
In dit voor beeld ziet u hoe u de duur van het opslaan van antwoorden in het cache geheugen van de back-end-service kunt API Management configureren, zoals opgegeven door de `Cache-Control`-instructie van de back-upservice. Zie voor een demonstratie van het configureren en gebruiken van dit beleid [Cloud cover aflevering 177: meer API management functies met Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) en Fast-forward to 25:25.

```xml
<!-- The following cache policy snippets demonstrate how to control API Management response cache duration with Cache-Control headers sent by the backend service. -->

<!-- Copy this snippet into the inbound section -->
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >
  <vary-by-header>Accept</vary-by-header>
  <vary-by-header>Accept-Charset</vary-by-header>
</cache-lookup>

<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the default value of 5 min if none is found  -->
<cache-store duration="@{
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;
  }"
 />
```

Zie [beleids expressies](api-management-policy-expressions.md) en [context variabelen](api-management-policy-expressions.md#ContextVariables)voor meer informatie.

### <a name="elements"></a>Elementen

|Naam|Beschrijving|Vereist|
|----------|-----------------|--------------|
|cache-lookup|Hoofd element.|Ja|
|variëren per koptekst|Begin de caching van antwoorden per waarde van de opgegeven header, zoals accepteren, accepteren-charset, accepteren-coderen, accepteren-taal, autorisatie, verwachten van, host, if-match.|Nee|
|vary-by-query-parameter|Begin de caching van antwoorden per waarde van de opgegeven query parameters. Voer één of meerdere para meters in. Gebruik een punt komma als scheidings teken. Als er geen is opgegeven, worden alle query parameters gebruikt.|Nee|

### <a name="attributes"></a>Kenmerken

| Naam                           | Beschrijving                                                                                                                                                                                                                                                                                                                                                 | Vereist | Standaard           |
|--------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| toestaan: private-Response-caching | Als deze instelling is ingesteld op `true`, kan het opslaan van aanvragen met een autorisatie-header in de cache worden geplaatst.                                                                                                                                                                                                                                                                        | Nee       | onwaar             |
| cache-type               | U kunt kiezen uit de volgende waarden van het kenmerk:<br />- `internal` voor het gebruik van de ingebouwde API Management cache,<br />- `external` om de externe cache te gebruiken zoals wordt beschreven in [een externe Azure-cache gebruiken voor redis in Azure API Management](api-management-howto-cache-external.md),<br />- `prefer-external` om externe cache te gebruiken als u een andere configuratie of een interne cache gebruikt. | Nee       | `prefer-external` |
| downstream-caching-type        | Dit kenmerk moet worden ingesteld op een van de volgende waarden.<br /><br /> -geen-downstream-caching is niet toegestaan.<br />-Private-downstream-caching is toegestaan.<br />-openbaar-privé en gedeeld downstream-caching is toegestaan.                                                                                                          | Nee       | geen              |
| moet opnieuw valideren                | Wanneer downstream-caching is ingeschakeld, schakelt dit kenmerk de `must-revalidate` Cache Control-instructie in de gateway-antwoorden in of uit.                                                                                                                                                                                                                      | Nee       | true              |
| variëren per ontwikkelaar              | Stel deze in op `true` om antwoorden per [abonnements sleutel](https://docs.microsoft.com/azure/api-management/api-management-subscriptions)op te slaan in de cache.                                                                                                                                                                                                                                                                                                         | Ja      |         False          |
| variëren per ontwikkelaar-groepen       | Stel deze in op `true` om antwoorden per [gebruikers groep](https://docs.microsoft.com/azure/api-management/api-management-howto-create-groups)in de cache op te slaan.                                                                                                                                                                                                                                                                                                             | Ja      |       False            |

### <a name="usage"></a>Gebruik
Dit beleid kan worden gebruikt in de volgende beleids [secties](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [bereiken](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

- **Beleids secties:** binnenkomend
- **Beleids bereik:** alle bereiken

## <a name="StoreToCache"></a>Opslaan in cache
In het `cache-store` beleid worden antwoorden in de cache opgeslagen volgens de opgegeven cache-instellingen. Dit beleid kan worden toegepast in gevallen waarin de inhoud van de reactie in een bepaalde periode statisch blijft. Antwoord caching vermindert de band breedte en de verwerkings vereisten op de back-end-webserver en verlaagt de latentie die wordt waargenomen door API-consumers.

> [!NOTE]
> Dit beleid moet een bijbehorende [Get van cache](api-management-caching-policies.md#GetFromCache) -beleid hebben.

### <a name="policy-statement"></a>Beleids verklaring

```xml
<cache-store duration="seconds" />
```

### <a name="examples"></a>Voorbeelden

#### <a name="example"></a>Voorbeeld

```xml
<policies>
    <inbound>
        <base />
        <cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" downstream-caching-type="none | private | public" must-revalidate="true | false">
            <vary-by-query-parameter>parameter name</vary-by-query-parameter> <!-- optional, can repeated several times -->
        </cache-lookup>
    </inbound>
    <outbound>
        <base />
        <cache-store duration="3600" />
    </outbound>
</policies>
```

#### <a name="example-using-policy-expressions"></a>Voor beeld van beleids expressies
In dit voor beeld ziet u hoe u de duur van het opslaan van antwoorden in het cache geheugen van de back-end-service kunt API Management configureren, zoals opgegeven door de `Cache-Control`-instructie van de back-upservice. Zie voor een demonstratie van het configureren en gebruiken van dit beleid [Cloud cover aflevering 177: meer API management functies met Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) en Fast-forward to 25:25.

```xml
<!-- The following cache policy snippets demonstrate how to control API Management response cache duration with Cache-Control headers sent by the backend service. -->

<!-- Copy this snippet into the inbound section -->
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >
  <vary-by-header>Accept</vary-by-header>
  <vary-by-header>Accept-Charset</vary-by-header>
</cache-lookup>

<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the default value of 5 min if none is found  -->
<cache-store duration="@{
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;
  }"
 />
```

Zie [beleids expressies](api-management-policy-expressions.md) en [context variabelen](api-management-policy-expressions.md#ContextVariables)voor meer informatie.

### <a name="elements"></a>Elementen

|Naam|Beschrijving|Vereist|
|----------|-----------------|--------------|
|cache-Store|Hoofd element.|Ja|

### <a name="attributes"></a>Kenmerken

| Naam             | Beschrijving                                                                                                                                                                                                                                                                                                                                                 | Vereist | Standaard           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| duration         | De TTL (time-to-Live) van de in de cache opgeslagen vermeldingen, opgegeven in seconden.                                                                                                                                                                                                                                                                                                   | Ja      | N.v.t.               |

### <a name="usage"></a>Gebruik
Dit beleid kan worden gebruikt in de volgende beleids [secties](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [bereiken](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

- **Beleids secties:** uitgaand
- **Beleids bereik:** alle bereiken

## <a name="GetFromCacheByKey"></a>Waarde uit cache ophalen
Gebruik het `cache-lookup-value`-beleid om de cache zoekopdracht op sleutel uit te voeren en een waarde in de cache te retour neren. De sleutel kan een wille keurige teken reeks waarde hebben en wordt doorgaans verschaft met een beleids expressie.

> [!NOTE]
> Dit beleid moet een overeenkomende [Store-waarde in het cache](#StoreToCacheByKey) beleid hebben.

### <a name="policy-statement"></a>Beleids verklaring

```xml
<cache-lookup-value key="cache key value"
    default-value="value to use if cache lookup resulted in a miss"
    variable-name="name of a variable looked up value is assigned to"
    caching-type="prefer-external | external | internal" />
```

### <a name="example"></a>Voorbeeld
Zie [Custom caching in Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-cache-by-key/)voor meer informatie en voor beelden van dit beleid.

```xml
<cache-lookup-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    variable-name="userprofile" />

```

### <a name="elements"></a>Elementen

|Naam|Beschrijving|Vereist|
|----------|-----------------|--------------|
|cache-lookup-waarde|Hoofd element.|Ja|

### <a name="attributes"></a>Kenmerken

| Naam             | Beschrijving                                                                                                                                                                                                                                                                                                                                                 | Vereist | Standaard           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| cache-type | U kunt kiezen uit de volgende waarden van het kenmerk:<br />- `internal` voor het gebruik van de ingebouwde API Management cache,<br />- `external` om de externe cache te gebruiken zoals wordt beschreven in [een externe Azure-cache gebruiken voor redis in Azure API Management](api-management-howto-cache-external.md),<br />- `prefer-external` om externe cache te gebruiken als u een andere configuratie of een interne cache gebruikt. | Nee       | `prefer-external` |
| standaard waarde    | Een waarde die wordt toegewezen aan de variabele als de zoek actie in de cache sleutel een Missing heeft veroorzaakt. Als dit kenmerk niet is opgegeven, wordt `null` toegewezen.                                                                                                                                                                                                           | Nee       | `null`            |
| sleutel              | De cache sleutel waarde die moet worden gebruikt in de zoek opdracht.                                                                                                                                                                                                                                                                                                                       | Ja      | N.v.t.               |
| variabele-naam    | De naam van de [context variabele](api-management-policy-expressions.md#ContextVariables) waaraan de opgezochte waarde wordt toegewezen als de zoek actie is geslaagd. Als de zoek actie resulteert in een Missing, wordt aan de variabele de waarde van het kenmerk `default-value` of `null`toegewezen als het kenmerk `default-value` wordt wegge laten.                                       | Ja      | N.v.t.               |

### <a name="usage"></a>Gebruik
Dit beleid kan worden gebruikt in de volgende beleids [secties](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [bereiken](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

- **Beleids secties:** inkomend, uitgaand, back-end, op fout
- **Beleids bereik:** alle bereiken

## <a name="StoreToCacheByKey"></a>Waarde voor opslaan in cache
De `cache-store-value` voert cache opslag uit op basis van de sleutel. De sleutel kan een wille keurige teken reeks waarde hebben en wordt doorgaans verschaft met een beleids expressie.

> [!NOTE]
> Dit beleid moet een overeenkomende [Get-waarde van het cache](#GetFromCacheByKey) beleid hebben.

### <a name="policy-statement"></a>Beleids verklaring

```xml
<cache-store-value key="cache key value" value="value to cache" duration="seconds" caching-type="prefer-external | external | internal" />
```

### <a name="example"></a>Voorbeeld
Zie [Custom caching in Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-cache-by-key/)voor meer informatie en voor beelden van dit beleid.

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />

```

### <a name="elements"></a>Elementen

|Naam|Beschrijving|Vereist|
|----------|-----------------|--------------|
|cache-Store-waarde|Hoofd element.|Ja|

### <a name="attributes"></a>Kenmerken

| Naam             | Beschrijving                                                                                                                                                                                                                                                                                                                                                 | Vereist | Standaard           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| cache-type | U kunt kiezen uit de volgende waarden van het kenmerk:<br />- `internal` voor het gebruik van de ingebouwde API Management cache,<br />- `external` om de externe cache te gebruiken zoals wordt beschreven in [een externe Azure-cache gebruiken voor redis in Azure API Management](api-management-howto-cache-external.md),<br />- `prefer-external` om externe cache te gebruiken als u een andere configuratie of een interne cache gebruikt. | Nee       | `prefer-external` |
| duration         | De waarde wordt in de cache opgeslagen voor de opgegeven duur waarde, opgegeven in seconden.                                                                                                                                                                                                                                                                                 | Ja      | N.v.t.               |
| sleutel              | Cache sleutel de waarde wordt opgeslagen onder.                                                                                                                                                                                                                                                                                                                   | Ja      | N.v.t.               |
| waarde            | De waarde die in de cache moet worden opgeslagen.                                                                                                                                                                                                                                                                                                                                     | Ja      | N.v.t.               |
### <a name="usage"></a>Gebruik
Dit beleid kan worden gebruikt in de volgende beleids [secties](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [bereiken](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

- **Beleids secties:** inkomend, uitgaand, back-end, op fout
- **Beleids bereik:** alle bereiken

### <a name="RemoveCacheByKey"></a>Waarde uit cache verwijderen
De `cache-remove-value` verwijdert een item in de cache dat wordt geïdentificeerd door de sleutel. De sleutel kan een wille keurige teken reeks waarde hebben en wordt doorgaans verschaft met een beleids expressie.

#### <a name="policy-statement"></a>Beleids verklaring

```xml

<cache-remove-value key="cache key value" caching-type="prefer-external | external | internal"  />

```

#### <a name="example"></a>Voorbeeld

```xml

<cache-remove-value key="@("userprofile-" + context.Variables["enduserid"])"/>

```

#### <a name="elements"></a>Elementen

|Naam|Beschrijving|Vereist|
|----------|-----------------|--------------|
|cache-remove-value|Hoofd element.|Ja|

#### <a name="attributes"></a>Kenmerken

| Naam             | Beschrijving                                                                                                                                                                                                                                                                                                                                                 | Vereist | Standaard           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| cache-type | U kunt kiezen uit de volgende waarden van het kenmerk:<br />- `internal` voor het gebruik van de ingebouwde API Management cache,<br />- `external` om de externe cache te gebruiken zoals wordt beschreven in [een externe Azure-cache gebruiken voor redis in Azure API Management](api-management-howto-cache-external.md),<br />- `prefer-external` om externe cache te gebruiken als u een andere configuratie of een interne cache gebruikt. | Nee       | `prefer-external` |
| sleutel              | De sleutel van de eerder in de cache opgeslagen waarde die uit de cache moet worden verwijderd.                                                                                                                                                                                                                                                                                        | Ja      | N.v.t.               |

#### <a name="usage"></a>Gebruik
Dit beleid kan worden gebruikt in de volgende beleids [secties](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [bereiken](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) .

- **Beleids secties:** inkomend, uitgaand, back-end, op fout
- **Beleids bereik:** alle bereiken

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het gebruik van beleid:

+ [Beleid in API Management](api-management-howto-policies.md)
+ [Api's transformeren](transform-api.md)
+ [Beleids verwijzing](api-management-policy-reference.md) voor een volledige lijst met beleids instructies en hun instellingen
+ [Voor beelden van beleid](policy-samples.md)
