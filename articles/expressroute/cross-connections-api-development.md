---
title: Ontwikkeling en integratie van Azure ExpressRoute CrossConnnections API
description: Dit artikel bevat een gedetailleerd overzicht van ExpressRoute-partners over het resource type expressRouteCrossConnections.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: mialdrid
ms.openlocfilehash: b4a83013d2cbeb2871a3963ec0c95144c02f4d66
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77187011"
---
# <a name="expressroute-crossconnnections-api-development-and-integration"></a>ExpressRoute CrossConnnections API-ontwikkeling en-integratie

Met de ExpressRoute partner Resource Manager-API kunnen ExpressRoute-partners de laag-2-en laag-3-configuratie van ExpressRoute-circuits van klanten beheren. De ExpressRoute partner Resource Manager-API introduceert een nieuw resource type, **expressRouteCrossConnections**. Partners gebruiken deze resource om ExpressRoute-circuits van klanten te beheren.

## <a name="workflow"></a>Werkstroom

De expressRouteCrossConnections-resource is een schaduw bron voor het ExpressRoute-circuit. Wanneer een Azure-klant een ExpressRoute-circuit maakt en een specifieke ExpressRoute-partner selecteert, maakt micro soft een expressRouteCrossConnections-resource in het Azure ExpressRoute-beheer abonnement van de partner. In dat geval definieert micro soft een resource groep voor het maken van de expressRouteCrossConnections-resource in. De naamgevings standaard voor de resource groep is **CrossConnection-* PeeringLocation * * *; waarbij PeeringLocation = de locatie van de ExpressRoute. Als een klant bijvoorbeeld een ExpressRoute-circuit in Denver maakt, wordt de CrossConnection gemaakt in het Azure-abonnement van de partner in de volgende resource groep: **CrossConnnection-Denver**.

ExpressRoute-partners beheren laag-2-en laag-3-configuratie door REST-bewerkingen uit te geven voor de expressRouteCrossConnections-resource.

## <a name="benefits"></a>Voordelen

Voor delen van verplaatsen naar de expressRouteCrossConnections-resource:

* Toekomstige verbeteringen voor ExpressRoute-partners worden beschikbaar gesteld op de ExpressRouteCrossConnection-resource.

* Partners kunnen op [rollen gebaseerde Access Control](https://docs.microsoft.com/azure/role-based-access-control/overview) Toep assen op de expressRouteCrossConnection-resource. Met deze besturings elementen kunt u machtigingen definiëren waarvoor gebruikers accounts de expressRouteCrossConnection-resource kunnen wijzigen en peering-configuraties toevoegen/bijwerken/verwijderen.

* De expressRouteCrossConnection-resource beschrijft Api's die handig kunnen zijn bij het oplossen van problemen met ExpressRoute-verbindingen. Dit omvat de ARP-tabel, het samen vatting BGP-route tabel en de details van BGP-route tabellen. Deze functie wordt niet ondersteund door de klassieke implementatie-Api's.

* Partners kunnen ook de geadverteerde community's op micro soft-peering opzoeken door gebruik te maken van de *RouteFilter* -resource.

## <a name="api-development-and-integration-steps"></a>API-ontwikkeling en integratie stappen

Voor het ontwikkelen van de partner-API gebruiken ExpressRoute-partners een test voor het instellen van de klant en het testen van de partner. De instelling van de test gebruiker wordt gebruikt om ExpressRoute-circuits te maken in peering-locaties die zijn gekoppeld aan dummy-apparaten en poorten. De test partner Setup wordt gebruikt voor het beheren van de ExpressRoute-circuits die zijn gemaakt op de locatie van de test peering.

### <a name="1-enlist-subscriptions"></a>1. abonnementen aanmelden

Als u de test partner wilt aanvragen en de installatie van de klant wilt testen, meldt u zich aan met twee betalen per gebruik Azure-abonnementen voor uw technische contact persoon van ExpressRoute:
* **ExpressRoute_API_Dev_Provider_Sub:** Dit abonnement wordt gebruikt voor het beheren van ExpressRoute-circuits die zijn gemaakt in peering-sites testen op dummy-apparaten en poorten.

* **ExpressRoute_API_Dev_Customer_Sub:** Dit abonnement wordt gebruikt om ExpressRoute-circuits te maken in peering-locaties die zijn gekoppeld aan dummy apparaten en poorten.

De test peering locaties: Dummy apparaten en poorten worden standaard niet aan productie klanten blootgesteld. Als u ExpressRoute-circuits wilt maken die zijn gekoppeld aan de test installatie, moet een functie vlag voor het abonnement worden ingeschakeld.

### <a name="2-register-the-dev_provider-subscription-to-access-the-expressroutecrossconnections-api"></a>2. Registreer het Dev_Provider-abonnement om toegang te krijgen tot de expressRouteCrossConnections-API

Voor toegang tot de expressRouteCrossConnections-API moet het partner abonnement worden inge schreven bij de **resource provider micro soft. Network**. Volg de stappen in het artikel [Azure-resource providers en-typen](/azure/azure-resource-manager/management/resource-providers-and-types#azure-portal) om het registratie proces te volt ooien.

### <a name="3-set-up-authentication-for-azure-resource-manager-rest-api-calls"></a>3. authenticatie voor Azure Resource Manager-REST API-aanroepen instellen

De meeste Azure-Services vereisen client code voor verificatie bij Resource Manager, met geldige referenties, vóór het aanroepen van service-Api's. Verificatie wordt gecoördineerd tussen de verschillende actoren van Azure AD en biedt de client een toegangs token als bewijs van authenticatie.

Het verificatie proces bestaat uit twee belang rijke stappen:

1. [Registreer de client](https://docs.microsoft.com/rest/api/azure/#register-your-client-application-with-azure-ad).
2. [Maak de toegangs aanvraag](https://docs.microsoft.com/rest/api/azure/#create-the-request).

### <a name="4-provide-network-contributor-permission-to-the-client-application"></a>4. de machtiging netwerk Inzender opgeven voor de client toepassing

Zodra de verificatie is geconfigureerd, moet u de netwerkinzender toegang verlenen tot uw client toepassing, onder de Dev_Provider_Sub. Als u toestemming wilt geven, meldt u zich aan bij de [Azure Portal](https://ms.portal.azure.com/#home) en voert u de volgende stappen uit:

1. Navigeer naar abonnementen en selecteer de Dev_Provider_Sub
2. Ga naar Access Control (IAM)
3. Roltoewijzing toevoegen
4. Selecteer de rol netwerk bijdrager
5. Toegang tot de Azure AD-gebruiker,-groep of Service-Principal toewijzen
6. Selecteer uw client toepassing
7. Wijzigingen opslaan

### <a name="5-develop"></a>5. Ontwikkel

Ontwikkel de [expressRouteCrossConnections-API](https://docs.microsoft.com/rest/api/expressroute/expressroutecrossconnections).

## <a name="rest-api"></a>REST-API

Zie [ExpressRoute CrossConnections rest API](https://docs.microsoft.com/rest/api/expressroute/expressroutecrossconnections) voor rest API-documentatie.

## <a name="next-steps"></a>Volgende stappen

Zie [EXPRESSROUTE rest api's](https://docs.microsoft.com/rest/api/expressroute/)(Engelstalig) voor meer informatie over alle ExpressRoute rest-api's.