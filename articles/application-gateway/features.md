---
title: Azure-toepassing gateway functies
description: Meer informatie over Azure-toepassing gateway functies
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: victorh
ms.openlocfilehash: 550d9f4f5396b2165260e39cd28222b083dd6756
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79279986"
---
# <a name="azure-application-gateway-features"></a>Azure-toepassing gateway functies

[Azure Application Gateway](overview.md) is een load balancer voor webverkeer waarmee u het verkeer naar uw webapps kunt beheren.

![Application Gateway conceptual](media/overview/figure1-720.png)

Application Gateway bevat de volgende functies:

- [Beëindiging van Secure Sockets Layer (SSL/TLS)](#secure-sockets-layer-ssltls-termination)
- [Automatisch schalen](#autoscaling)
- [Zone redundantie](#zone-redundancy)
- [Statisch VIP](#static-vip)
- [Web Application firewall](#web-application-firewall)
- [Ingangs controller voor AKS](#ingress-controller-for-aks)
- [Route ring op basis van URL](#url-based-routing)
- [Hosting op meerdere sites](#multiple-site-hosting)
- [Omleiding](#redirection)
- [Sessie affiniteit](#session-affinity)
- [WebSocket en HTTP/2 verkeer](#websocket-and-http2-traffic)
- [Verbinding verbreken](#connection-draining)
- [Aangepaste fout pagina's](#custom-error-pages)
- [HTTP-headers opnieuw schrijven](#rewrite-http-headers)
- [Grootte aanpassen](#sizing)

## <a name="secure-sockets-layer-ssltls-termination"></a>Beëindiging van Secure Sockets Layer (SSL/TLS)

Application Gateway ondersteunt het beëindigen van SSL/TLS op de gateway, waarna verkeer doorgaans niet-versleuteld naar de back-endservers stromen. Met deze functie voorkomt u prijzige overhead voor het versleutelen en ontsleutelen voor uw webservers. Maar soms is niet-versleutelde communicatie met de servers een acceptabele optie. Dit kan worden veroorzaakt door beveiligings vereisten, nalevings vereisten of door de toepassing alleen een beveiligde verbinding te accepteren. Voor deze toepassingen ondersteunt Application Gateway end-to-end SSL/TLS-versleuteling.

Zie [overzicht van SSL-beëindiging en end-to-end SSL met Application Gateway](ssl-overview.md) voor meer informatie

## <a name="autoscaling"></a>Automatisch schalen

Application Gateway Standard_v2 ondersteunt automatisch schalen en kan omhoog of omlaag worden geschaald op basis van het wijzigen van de verkeers laad patronen. Automatisch schalen heft ook de vereiste op om tijdens het inrichten een implementatiegrootte of het aantal instanties te kiezen. 

Zie [v2 SKU](application-gateway-autoscaling-zone-redundant.md)voor automatisch schalen voor meer informatie over de functies van Application Gateway Standard_v2.

## <a name="zone-redundancy"></a>Zone redundantie

Een Standard_v2 Application Gateway kan meerdere Beschikbaarheidszones omvatten, waardoor er betere fout tolerantie wordt geboden en de nood zaak voor het inrichten van afzonderlijke toepassings gateways in elke zone wordt verwijderd.

## <a name="static-vip"></a>Statisch VIP

De Standard_v2 SKU van Application Gateway ondersteunt alleen statisch VIP-type. Dit zorgt ervoor dat het VIP dat is gekoppeld aan de toepassings gateway niet wordt gewijzigd, zelfs gedurende de levens duur van de Application Gateway.

## <a name="web-application-firewall"></a>Web Application Firewall

Web Application firewall (WAF) is een service waarmee uw webtoepassingen gecentraliseerd worden beschermd tegen veelvoorkomende aanvallen en beveiligings problemen. WAF is gebaseerd op regels van de [OWASP (open Web Application Security project) kern regel sets](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3,1 (alleen WAF_v2), 3,0 en 2.2.9. 

Webtoepassingen zijn in toenemende mate het doel van aanvallen die gebruikmaken van veelvoorkomende bekende beveiligingsproblemen. Veelvoorkomende aanvallen zijn hierbij onder andere aanvallen met SQL-injecties en aanvallen via scripting op meerdere sites. Het kan een hele uitdaging zijn om dergelijke aanvallen in toepassingscode te voorkomen en dit kan tevens veel onderhoud, patching en controle vereisen op meerdere lagen van de toepassingstopologie. Een gecentraliseerde firewall voor webtoepassingen maakt het beveiligingsbeheer veel eenvoudiger en biedt toepassingsbeheerders meer veiligheid tegen bedreigingen of aanvallen. Een WAF-oplossing kan ook sneller reageren op een beveiligingsrisico door een patch voor een bekend beveiligingsprobleem toe te passen op een centrale locatie in plaats van elke afzonderlijke webtoepassing te beveiligen. Bestaande toepassings gateways kunnen eenvoudig worden geconverteerd naar een toepassings gateway met Web Application firewall.

Zie [Wat is Azure Web Application firewall?](../web-application-firewall/overview.md)voor meer informatie.

## <a name="ingress-controller-for-aks"></a>Controller van inkomend verkeer voor AKS
Met Application Gateway ingangs controller (AGIC) kunt u Application Gateway gebruiken als binnenkomend verkeer voor een [Azure Kubernetes service-cluster (AKS)](https://azure.microsoft.com/services/kubernetes-service/) . 

De ingangs controller wordt uitgevoerd als een pod in het AKS-cluster en maakt gebruik van Kubernetes inkomende [bronnen](https://kubernetes.io/docs/concepts/services-networking/ingress/) en converteert deze naar een Application Gateway configuratie, waardoor de gateway verkeer naar de Kubernetes van de peulen kan verdelen. De ingangs controller ondersteunt alleen Application Gateway Standard_v2 en WAF_v2 Sku's. 

Zie [Application Gateway ingress controller (AGIC) (Engelstalig)](ingress-controller-overview.md)voor meer informatie.

## <a name="url-based-routing"></a>URL-gebaseerde routering

Met op URL-pad gebaseerde routering kunt u verkeer routeren naar back-endserverpools die zijn gebaseerd op de URL-paden van de aanvraag. Een van de scenario's is het routeren van aanvragen voor verschillende inhoudstypen naar een andere pool.

Aanvragen voor `http://contoso.com/video/*` worden bijvoorbeeld doorgestuurd naar VideoServerPool en aanvragen voor `http://contoso.com/images/*` worden doorgestuurd naar ImageServerPool. Als geen van de padpatronen overeenkomen, wordt DefaultServerPool geselecteerd.

Zie [URL-pad op basis van route ring-overzicht](url-route-overview.md)voor meer informatie.

## <a name="multiple-site-hosting"></a>Hosting van meerdere sites

Door meerdere sites te hosten, kunt u meer dan een website configureren op dezelfde instantie van de toepassingsgateway. Met deze functie kunt u een efficiëntere topologie voor uw implementaties configureren door Maxi maal 100 websites aan één Application Gateway toe te voegen (voor optimale prestaties). Elke website kan worden omgeleid naar een eigen pool. Application Gateway kan bijvoorbeeld verkeer regelen voor `contoso.com` en `fabrikam.com` vanaf twee servergroepen genaamd ContosoServerPool en FabrikamServerPool.

Aanvragen voor `http://contoso.com` worden gerouteerd naar ContoServerPool en aanvragen voor `http://fabrikam.com` worden gerouteerd naar FabrikamServerPool.

Op dezelfde manier kunnen twee subdomeinen van hetzelfde bovenliggende domein worden gehost op dezelfde implementatie van een toepassingsgateway. Voorbeelden van subdomeinen die worden gehost op één toepassingsgateway-implementatie, zijn `http://blog.contoso.com` en `http://app.contoso.com`.

Zie [Application Gateway multiple site hosting](multiple-site-overview.md)(Engelstalig) voor meer informatie.

## <a name="redirection"></a>Omleiding

Een veelvoorkomend scenario voor veel webtoepassingen is de ondersteuning van automatische HTTP-naar-HTTPS-omleiding zodat alle communicatie tussen een toepassing en gebruikers plaatsvindt via een versleuteld pad.

In het verleden hebt u mogelijk technieken gebruikt, zoals het maken van een exclusieve pool waarvan het enige doel is om aanvragen om te leiden die worden ontvangen op HTTP-HTTPS. Application Gateway ondersteunt het omleiden van verkeer op de Application Gateway. Dit vereenvoudigt de configuratie van toepassingen, optimaliseert het resourcegebruik en biedt ondersteuning voor nieuwe omleidingsscenario's, waaronder de globale en op pad gebaseerde omleidingen. Application Gateway omleidings ondersteuning is niet beperkt tot HTTP-naar-HTTPS-omleiding. Dit is een algemeen omleidingsmechanisme, zodat u op basis van regels kunt omleiden van en naar elke poort die u gebruikt. Ook omleiding naar een externe site wordt ondersteund.

Ondersteuning voor Application Gateway-omleiding biedt de volgende mogelijkheden:

- Globale omleiding van de ene poort naar de andere poort op de Gateway. Hierdoor is HTTP-naar-HTTPS-omleiding op een site mogelijk.
- Padgebaseerde omleiding. Dit type omleiding maakt HTTP-naar-HTTPS-omleiding alleen mogelijk op een specifiek sitegebied, bijvoorbeeld een winkelwagengebied aangegeven door `/cart/*`.
- Omleiden naar een externe site.

Zie [Application Gateway omleidings overzicht](redirect-overview.md)voor meer informatie.

## <a name="session-affinity"></a>Sessieaffiniteit

De functie Sessieaffiniteit op basis van cookies is handig als u een gebruikerssessie op dezelfde server wilt behouden. Met behulp van de gatewaybeheerde cookies kan de Application Gateway het daarop volgende verkeer van een gebruikerssessie naar dezelfde server leiden voor verwerking. Dit is belangrijk wanneer de sessiestatus lokaal wordt opgeslagen op de server voor een gebruikerssessie.

Zie [hoe een toepassings gateway werkt](how-application-gateway-works.md#modifications-to-the-request)voor meer informatie.

## <a name="websocket-and-http2-traffic"></a>Websocket- en HTTP-/2-verkeer

Application Gateway biedt systeemeigen ondersteuning voor de WebSocket- en HTTP-/2-protocollen. Er is geen door de gebruiker configureerbare instelling om selectief WebSocket-ondersteuning in of uit te schakelen.

De WebSocket- en HTTP-/2-protocollen maken full-duplex-communicatie tussen een server en een client mogelijk via een langdurige TCP-verbinding. Dit maakt een meer interactieve communicatie mogelijk tussen de webserver en de client, die bidirectioneel kan zijn zonder dat hiervoor polling nodig is, zoals vereist in implementaties op basis van HTTP. Deze protocollen hebben een lage overhead, in tegens telling tot HTTP, en kunnen dezelfde TCP-verbinding opnieuw gebruiken voor meerdere aanvragen en antwoorden, wat resulteert in een efficiëntere bron gebruik. Deze protocollen zijn ontworpen om te werken via de traditionele HTTP-poorten: 80 en 443.

Zie ondersteuning voor [Websockets](application-gateway-websocket.md) en [http/2-ondersteuning](configuration-overview.md#http2-support)voor meer informatie.

## <a name="connection-draining"></a>Verwerkingsstop voor verbindingen

Verwerkingsstop voor verbindingen helpt u om back-endgroepsleden zonder problemen te verwijderen tijdens geplande service-updates. Deze instelling wordt ingeschakeld via de HTTP-instelling van de back-end en kan tijdens het maken van de regel worden toegepast op alle leden van een back-endgroep. Wanneer deze functie is ingeschakeld, wordt door Application Gateway ervoor gezorgd dat alle ongedaan maken van de registratie van exemplaren van een back-end-groep geen nieuwe aanvraag ontvangen en dat bestaande aanvragen binnen een geconfigureerde tijds limiet kunnen worden voltooid. Dit geldt voor beide back-endservers die expliciet worden verwijderd uit de back-end-pool door een wijziging van de gebruikers configuratie en back-end-exemplaren die als slecht zijn gerapporteerd zoals bepaald door de status controles. De enige uitzonde ring hierop zijn aanvragen die zijn gebonden voor het deregistreren van instanties, die expliciet zijn geregistreerd vanwege gateway beheer sessie-affiniteit, en blijven worden gefactureerd voor het ongedaan maken van de registratie van instanties.

Zie [Application Gateway configuratie-overzicht](configuration-overview.md#connection-draining)voor meer informatie.

## <a name="custom-error-pages"></a>Aangepaste foutpagina's

Met Application Gateway kunt u aangepaste foutpagina's maken in plaats van standaardfoutpagina's weer te geven. U kunt uw eigen huisstijl en lay-out hanteren door een aangepaste foutpagina te gebruiken.

Zie [aangepaste fouten](custom-error.md)voor meer informatie.

## <a name="rewrite-http-headers"></a>HTTP-headers opnieuw genereren

Met HTTP-headers kunnen de client en server aanvullende informatie door geven met de aanvraag of het antwoord. Het herschrijven van deze HTTP-headers helpt u bij het uitvoeren van verschillende belang rijke scenario's, zoals:

- Toevoegen van aan beveiliging gerelateerde header velden zoals HSTS/X-XSS-beveiliging.
- De velden van de antwoord header worden verwijderd die gevoelige informatie kunnen onthullen.
- Poort gegevens van X-doorgestuurd-voor kopteksten

Application Gateway ondersteunt de mogelijkheid om HTTP-aanvragen en-antwoord headers toe te voegen, te verwijderen of bij te werken, terwijl de aanvraag-en antwoord pakketten tussen de client en de back-end-pool worden verplaatst. Het biedt ook de mogelijkheid om voor waarden toe te voegen om ervoor te zorgen dat de opgegeven headers alleen worden herschreven wanneer aan bepaalde voor waarden wordt voldaan.

Zie [HTTP-headers herschrijven](rewrite-http-headers.md)voor meer informatie.

## <a name="sizing"></a>Grootte aanpassen

Application Gateway Standard_v2 kan worden geconfigureerd voor implementaties met automatisch schalen of vaste grootte. Deze SKU biedt geen verschillende grootte van het exemplaar. Zie [v2 SKU](application-gateway-autoscaling-zone-redundant.md#pricing)voor automatisch schalen voor meer informatie over de prestaties en prijzen van v2.

De Application Gateway standaard wordt aangeboden in drie grootten: **klein**, **gemiddeld**en **groot**. Kleine exemplaargrootten zijn bedoeld voor het ontwikkelen en testen van scenario's.

Zie [Servicelimieten voor Application Gateway](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#application-gateway-limits) voor een volledige lijst van toepassingsgateway-limieten.

De volgende tabel toont een gemiddelde prestaties doorvoer voor elke Application Gateway v1-instantie waarvoor SSL-offload is ingeschakeld:

| Gemiddelde grootte van een antwoord van de back-endpagina | Klein | Middelgroot | Groot |
| --- | --- | --- | --- |
| 6 kB |7,5 Mbps |13 Mbps |50 Mbps |
| 100 kB |35 Mbps |100 Mbps |200 Mbps |

> [!NOTE]
> Deze waarden zijn geschatte waarden voor de doorvoer van een toepassingsgateway. De werkelijke doorvoer hangt af van verschillende details van de omgeving, zoals de gemiddelde paginagrootte, locatie van back-endexemplaren en de verwerkingstijd voor een pagina. Voor nauwkeurige prestatiecijfers moet u uw eigen tests uitvoeren. Deze waarden worden alleen geboden als richtlijn voor de capaciteitsplanning.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over hoe Application Gateway werkt: [hoe een toepassings gateway werkt](how-application-gateway-works.md)