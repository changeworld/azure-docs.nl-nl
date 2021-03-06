---
author: vladvino
ms.service: api-management
ms.topic: include
ms.date: 11/09/2018
ms.author: vlvinogr
ms.openlocfilehash: b9e601c72395b4910850714460321a83a3113e69
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77649535"
---
## <a name="how-apim-proxy-server-responds-with-ssl-certificates-in-the-tls-handshake"></a>Hoe APIM-proxy server reageert met SSL-certificaten in de TLS-Handshake

### <a name="clients-calling-with-sni-header"></a>Clients die met een SNI-header bellen
Als de klant een of meer aangepaste domeinen heeft geconfigureerd voor proxy, kan APIM reageren op HTTPS-aanvragen van het aangepaste domein (bijvoorbeeld contoso.com) en het standaard domein (bijvoorbeeld apim-service-name.azure-api.net). Op basis van de informatie in de Servernaamindicatie (SNI)-header, reageert APIM met het juiste server certificaat.

### <a name="clients-calling-without-sni-header"></a>Clients die worden aangeroepen zonder SNI-header
Als de klant gebruikmaakt van een-client, die de [SNI](https://tools.ietf.org/html/rfc6066#section-3) -header niet verzendt, maakt APIM reacties op basis van de volgende logica:

* Als voor de service slechts één aangepast domein is geconfigureerd voor proxy, is het standaard certificaat het certificaat dat is verleend aan het aangepaste proxy domein.
* Als de service meerdere aangepaste domeinen voor proxy heeft geconfigureerd (ondersteund in de laag **ontwikkelaar** en **Premium** ), kan de klant aangeven welk certificaat het standaard certificaat moet zijn. Als u het standaard certificaat wilt instellen, moet de eigenschap [defaultSslBinding](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/apimanagementservice/createorupdate#hostnameconfiguration) zijn ingesteld op True ("defaultSslBinding": "True"). Als de klant de eigenschap niet instelt, is het standaard certificaat het certificaat dat is verleend aan het standaard proxy domein dat wordt gehost op azure-api.net.

## <a name="support-for-putpost-request-with-large-payload"></a>Ondersteuning voor PUT/POST-aanvragen met grote nettolading

De APIM-proxy server ondersteunt aanvragen met een grote Payload wanneer certificaten aan client zijde worden gebruikt in HTTPS (bijvoorbeeld payload > 40 KB). Om te voor komen dat de aanvraag van de server wordt gevriesd, kunnen klanten de eigenschap ["negotiateClientCertificate": "True](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/ApiManagementService/CreateOrUpdate#hostnameconfiguration) " instellen op de proxy-hostnaam. Als de eigenschap is ingesteld op True, wordt het client certificaat aangevraagd op SSL/TLS-verbindings tijd, vóór een HTTP-aanvraag uitwisseling. Aangezien de instelling van toepassing is op het niveau van de **proxy-hostnaam** , vragen alle verbindings aanvragen naar het client certificaat. Klanten kunnen Maxi maal 20 aangepaste domeinen voor proxy configureren (alleen ondersteund in de **Premium** -laag) en deze beperking omzeilen.

