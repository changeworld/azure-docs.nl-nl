---
title: Verzameling van Azure-toepassing Insights-IP-adressen | Microsoft Docs
description: Meer informatie over hoe IP-adressen en geolocatie worden verwerkt met Azure-toepassing Insights
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 969061ec89ddd0f13caa675bc324207c6c5d8843
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77656514"
---
# <a name="geolocation-and-ip-address-handling"></a>Verwerking van geolocatie en IP-adres

In dit artikel wordt uitgelegd hoe geolocatie lookup en IP-adres afhandeling plaatsvindt in Application Insights en hoe u het standaard gedrag wijzigt.

## <a name="default-behavior"></a>Standaardgedrag

IP-adressen worden standaard tijdelijk verzameld, maar niet opgeslagen in Application Insights. Het basisproces verloopt als volgt:

IP-adressen worden verzonden naar Application Insights als onderdeel van telemetriegegevens. Bij het bereiken van het opname-eind punt in azure wordt het IP-adres gebruikt voor het uitvoeren van een geolocatie lookup met [GeoLite2 van Maxmind](https://dev.maxmind.com/geoip/geoip2/geolite2/). De resultaten van deze zoek actie worden gebruikt om de volgende velden in te vullen `client_City`, `client_StateOrProvince``client_CountryOrRegion`. Op dit moment wordt het IP-adres verwijderd en worden `0.0.0.0` naar het veld `client_IP` geschreven.

* Browser-telemetrie: het IP-adres van de afzender wordt tijdelijk verzameld. Het IP-adres wordt berekend door het opname-eind punt.
* Server-telemetrie: de Application Insights module verzamelt tijdelijk het client-IP-adres. Het wordt niet verzameld als `X-Forwarded-For` is ingesteld.

Dit gedrag is inherent aan het ontwerp om onnodig verzamelen van persoons gegevens te voor komen. Als dat mogelijk is, kunt u het verzamelen van persoons gegevens het beste vermijden. 

## <a name="overriding-default-behavior"></a>Standaard gedrag negeren

Hoewel het standaard gedrag is om het verzamelen van persoons gegevens te minimaliseren, bieden we nog steeds de flexibiliteit om IP-adres gegevens te verzamelen en op te slaan. Voordat u ervoor kiest om persoonlijke gegevens op te slaan, zoals IP-adressen, wordt u ten zeerste aangeraden te controleren of dit geen nalevings vereisten of lokale voor Schriften bevat waarvan u mogelijk afhankelijk bent. Raadpleeg de [richt lijnen voor persoonlijke gegevens](https://docs.microsoft.com/azure/azure-monitor/platform/personal-data-mgmt)voor meer informatie over het afhandelen van persoonlijke gegevens in Application Insights.

## <a name="storing-ip-address-data"></a>IP-adres gegevens opslaan

Als u de IP-verzameling en-opslag wilt inschakelen, moet u de eigenschap `DisableIpMasking` van het onderdeel Application Insights instellen op `true`. Deze eigenschap kan worden ingesteld via Azure Resource Manager sjablonen of door de REST API aan te roepen. 

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-sjabloon

```json
{
       "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<resource-name>",
       "name": "<resource-name>",
       "type": "microsoft.insights/components",
       "location": "westcentralus",
       "tags": {
              
       },
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "Flow_Type": "Redfield",
              "Request_Source": "IbizaAIExtension",
              // ...
              "DisableIpMasking": true
       }
}
```

### <a name="portal"></a>Portal 

Als u het gedrag voor een enkele Application Insights resource alleen hoeft te wijzigen, kunt u dit het beste doen via de Azure Portal.  

1. Ga naar de Application Insights Bron > **instellingen** > **sjabloon exporteren** 

    ![Sjabloon exporteren](media/ip-collection/export-template.png)

2. **Implementatie** selecteren

    ![Implementatie knop gemarkeerd in rood](media/ip-collection/deploy.png)

3. Selecteer **sjabloon bewerken**. (Als uw sjabloon extra eigenschappen of resources heeft die niet in deze voorbeeld sjabloon worden weer gegeven, gaat u voorzichtig te werk om er zeker van te zijn dat alle resources de sjabloon implementatie accepteren als een incrementele wijziging/update.)

    ![Sjabloon bewerken](media/ip-collection/edit-template.png)

4. Breng de volgende wijzigingen aan in de JSON voor uw resource en klik vervolgens op **Opslaan**:

    ![In de scherm afbeelding wordt een komma toegevoegd na ' IbizaAIExtension ' en een nieuwe regel toegevoegd onder ' DisableIpMasking ': True](media/ip-collection/save.png)

    > [!WARNING]
    > Als er een fout optreedt met de melding:  **_de resource groep bevindt zich op een locatie die niet wordt ondersteund door een of meer resources in de sjabloon. Kies een andere resource groep._** Selecteer tijdelijk een andere resource groep in de vervolg keuzelijst en selecteer vervolgens de oorspronkelijke resource groep om de fout op te lossen.

5. Selecteer **Ik ga akkoord** > **kopen**. 

    ![Sjabloon bewerken](media/ip-collection/purchase.png)

    In dit geval wordt er niets nieuw gekocht, maar wordt alleen de configuratie van de bestaande Application Insights resource bijgewerkt.

6. Zodra de implementatie is voltooid, worden nieuwe telemetriegegevens vastgelegd.

    Als u een sjabloon opnieuw wilt selecteren en bewerken, zou u alleen de standaard sjabloon zien en de zojuist toegevoegde eigenschap en de bijbehorende waarde niet zien. Als u geen IP-adres gegevens ziet en wilt bevestigen dat `"DisableIpMasking": true` is ingesteld. Voer de volgende Power shell uit: (Vervang `Fabrikam-dev` door de naam van de juiste resource en resource groep.)
    
    ```powershell
    # If you aren't using the cloud shell you will need to connect to your Azure account
    # Connect-AzAccount 
    $AppInsights = Get-AzResource -Name 'Fabrikam-dev' -ResourceType 'microsoft.insights/components' -ResourceGroupName 'Fabrikam-dev'
    $AppInsights.Properties
    ```
    
    Er wordt een lijst met eigenschappen geretourneerd als resultaat. Een van de eigenschappen moet `DisableIpMasking: true`lezen. Als u de Power shell uitvoert voordat u de nieuwe eigenschap met Azure Resource Manager implementeert, bestaat de eigenschap niet.

### <a name="rest-api"></a>Rest-API

De nettolading van de [rest-API](https://docs.microsoft.com/rest/api/azure/) voor het maken van dezelfde wijzigingen is als volgt:

```
PATCH https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/microsoft.insights/components/<resource-name>?api-version=2018-05-01-preview HTTP/1.1
Host: management.azure.com
Authorization: AUTH_TOKEN
Content-Type: application/json
Content-Length: 54

{
       "location": "<resource location>",
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "DisableIpMasking": true
       }
}
```

## <a name="telemetry-initializer"></a>Initialisatie functie voor telemetrie

Als u een flexibeler alternatief nodig hebt dan `DisableIpMasking` om alle of een deel van de IP-adressen vast te leggen, kunt u een [initialisatie functie voor telemetrie](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer) gebruiken om alle of een deel van het IP-adres naar een aangepast veld te kopiëren. 

### <a name="aspnet--aspnet-core"></a>ASP.NET/ASP.NET Core

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MyWebApp
{
    public class CloneIPAddress : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            ISupportProperties propTelemetry = telemetry as ISupportProperties;

            if (propTelemetry !=null && !propTelemetry.Properties.ContainsKey("client-ip"))
            {
                string clientIPValue = telemetry.Context.Location.Ip;
                propTelemetry.Properties.Add("client-ip", clientIPValue);
            }
        }
    } 
}
```

> [!NOTE]
> Als u geen toegang hebt tot `ISupportProperties`, controleert u of u de laatste stabiele versie van de Application Insights SDK uitvoert. `ISupportProperties` zijn bedoeld voor hoge kardinaliteit waarden, terwijl `GlobalProperties` geschikter zijn voor waarden met weinig kardinaliteit, zoals regio naam, omgevings naam, enzovoort. 

### <a name="enable-telemetry-initializer-for-aspnet"></a>De initialisatie functie voor telemetrie voor ASP.NET inschakelen

```csharp
using Microsoft.ApplicationInsights.Extensibility;


namespace MyWebApp
{
    public class MvcApplication : System.Web.HttpApplication
    {
        protected void Application_Start()
        {
              //Enable your telemetry initializer:
              TelemetryConfiguration.Active.TelemetryInitializers.Add(new CloneIPAddress());
        }
    }
}

```

### <a name="enable-telemetry-initializer-for-aspnet-core"></a>Initialisatie functie voor telemetrie inschakelen voor ASP.NET Core

U kunt de initialisatie functie ASP.NET Core voor telemetrie op dezelfde manier maken als ASP.NET, maar om de initialisatie functie in te scha kelen, gebruikt u het volgende voor beeld om te verwijzen:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, CloneIPAddress>();
}
```

### <a name="nodejs"></a>Node.js

```javascript
appInsights.defaultClient.addTelemetryProcessor((envelope) => {
    const baseData = envelope.data.baseData;
    if (appInsights.Contracts.domainSupportsProperties(baseData)) {
        const ipAddress = envelope.tags[appInsights.defaultClient.context.keys.locationIp];
        if (ipAddress) {
            baseData.properties["client-ip"] = ipAddress;
        }
    }
});
```

### <a name="client-side-javascript"></a>JavaScript aan de clientzijde

In tegens telling tot de Sdk's aan de server zijde berekent de Java script SDK aan de client zijde geen IP-adres. Standaard wordt de berekening van IP-adressen voor telemetrie aan de client zijde uitgevoerd op het opname-eind punt in azure bij de ontvangst van telemetrie. Dit betekent dat als u client gegevens naar een proxy verzendt en vervolgens doorstuurt naar het opname-eind punt, het IP-adres van de proxy wordt weer gegeven in de berekening van het IP-adres en niet de client. Als er geen proxy wordt gebruikt, mag dit geen probleem zijn.

Als u het IP-adres rechtstreeks aan de client zijde wilt berekenen, moet u uw eigen aangepaste logica toevoegen om deze berekening uit te voeren en het resultaat gebruiken om de `ai.location.ip`-tag in te stellen. Als `ai.location.ip` is ingesteld, wordt de berekening van het IP-adres niet uitgevoerd door het opname-eind punt en wordt het opgegeven IP-adres geaccepteerd en gebruikt voor het uitvoeren van de geo-zoek opdracht. In dit scenario wordt het IP-adres standaard nog steeds nul. 

Als u het volledige IP-adres wilt behouden dat is berekend op basis van uw aangepaste logica, kunt u een telemetrie-initialisatie functie gebruiken waarmee de IP-adres gegevens die u in `ai.location.ip` hebt ingevoerd, naar een afzonderlijk aangepast veld worden gekopieerd. Maar in tegens telling tot de Sdk's aan de server zijde, zonder afhankelijk te zijn van bibliotheken van derden of uw eigen aangepaste IP-verzamelings logica op de client, wordt het IP-adres voor u niet door de SDK aan de client zijde berekend.    


```javascript
appInsights.addTelemetryInitializer((item) => {
    const ipAddress = item.tags && item.tags["ai.location.ip"];
    if (ipAddress) {
        item.baseData.properties = {
            ...item.baseData.properties,
            "client-ip": ipAddress
        };
    }
});

```  

### <a name="view-the-results-of-your-telemetry-initializer"></a>De resultaten van de initialisatie functie voor telemetrie weer geven

Als u vervolgens nieuw verkeer voor uw site inschakelt en ongeveer 2-5 minuten moet wachten om er zeker van te zijn dat er tijd is opgenomen, kunt u een Kusto-query uitvoeren om te zien of de IP-adres verzameling werkt:

```kusto
requests
| where timestamp > ago(1h) 
| project appName, operation_Name, url, resultCode, client_IP, customDimensions.["client-ip"]
```

Nieuwe IP-adressen moeten worden weer gegeven in de kolom `customDimensions_client-ip`. De standaard `client-ip` kolom heeft nog steeds een waarde van 4 octetten of de eerste drie octetten worden alleen weer gegeven, afhankelijk van hoe u de IP-adres verzameling op onderdeel niveau hebt geconfigureerd. Als u lokaal test na de implementatie van de telemetrie-initialisatie functie en de waarde die u ziet voor `customDimensions_client-ip` is `::1` dit is het verwachte gedrag. `::1` vertegenwoordigt het loop back-adres in IPv6. Het is gelijk aan `127.0.01` in IPv4. het resultaat is dat u tijdens het testen van localhost te zien krijgt.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [verzamelen van persoonlijke gegevens](https://docs.microsoft.com/azure/azure-monitor/platform/personal-data-mgmt) in Application Insights.

* Meer informatie over hoe [IP-adres verzameling](https://apmtips.com/blog/2016/07/05/client-ip-address/) in Application Insights werkt. (Dit is een oudere externe blog post, geschreven door een van onze technici. Het huidige standaard gedrag waarbij het IP-adres wordt vastgelegd als `0.0.0.0`, wordt voorgezet, maar het wordt uitgebreid naar de mechanismen van de ingebouwde `ClientIpHeaderTelemetryInitializer`.)
