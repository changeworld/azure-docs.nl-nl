---
title: Koppelingen voor beheerde toepassing implementeren met behulp van beleid
description: Meer informatie over het implementeren van koppelingen voor een beheerde toepassing met behulp van Azure Policy-service.
author: msHich
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: hich
ms.openlocfilehash: ec0fe8f66ef2ad2458b4ffad0e848591793e5b05
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75650940"
---
# <a name="deploy-associations-for-a-managed-application-using-azure-policy"></a>Koppelingen voor een beheerde toepassing implementeren met behulp van Azure Policy

Azure-beleid kan worden gebruikt voor het implementeren van koppelingen om resources te koppelen aan een beheerde toepassing. In dit artikel wordt een ingebouwd beleid beschreven waarmee koppelingen worden geïmplementeerd en hoe u dit beleid kunt gebruiken.

## <a name="built-in-policy-to-deploy-associations"></a>Ingebouwd beleid voor het implementeren van koppelingen

Het implementeren van koppelingen voor een beheerde toepassing is een ingebouwd beleid dat kan worden gebruikt voor het implementeren van een koppeling om een resource te koppelen aan een beheerde toepassing. Het beleid accepteert drie para meters:

- ID van de beheerde toepassing: deze ID is de resource-ID van de beheerde toepassing waaraan de resources moeten worden gekoppeld.
- Resource typen die u wilt koppelen: deze resource typen zijn de lijst met resource typen die aan de beheerde toepassing moeten worden gekoppeld. U kunt meerdere resource typen aan een beheerde toepassing koppelen met hetzelfde beleid.
- Voor voegsel van koppelings naam: deze teken reeks is het voor voegsel dat moet worden toegevoegd aan de naam van de koppelings bron die wordt gemaakt. De standaard waarde is ' DeployedByPolicy '.

Het beleid maakt gebruik van DeployIfNotExists-evaluatie. Het wordt uitgevoerd nadat een resource provider een aanvraag voor het maken of bijwerken van een resource van de geselecteerde resource type (n) heeft afgehandeld en de evaluatie de status code geslaagd heeft geretourneerd. Daarna wordt de koppelings bron geïmplementeerd met een sjabloon implementatie.
Zie voor meer informatie over koppelingen [Azure Custom providers resource onboarding](../custom-providers/concepts-resource-onboarding.md)

## <a name="how-to-use-the-deploy-associations-built-in-policy"></a>Het ingebouwde beleid voor het implementeren van koppelingen gebruiken 

### <a name="prerequisites"></a>Vereisten
Als de beheerde toepassing machtigingen nodig heeft voor het abonnement om een actie uit te voeren, werkt de beleids implementatie van koppelings bron niet zonder de machtigingen toe te kennen.

### <a name="policy-assignment"></a>Beleidstoewijzing
Als u het ingebouwde beleid wilt gebruiken, maakt u een beleids toewijzing en wijst u de Deploy-koppelingen toe voor een beleid voor beheerde toepassingen. Zodra het beleid is toegewezen, identificeert het beleid niet-compatibele resources en implementeert de koppeling voor deze resources.

![Ingebouwd beleid toewijzen](media/concepts-built-in-policy/assign-builtin-policy-managedapp.png)

## <a name="getting-help"></a>Ondersteuning vragen

Als u vragen hebt over het ontwikkelen van aangepaste Azure-resource providers, vraagt u deze op [stack overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). Er is mogelijk al een vergelijk bare vraag beantwoord, dus controleer eerst vóór het boeken. Voeg de tag ```azure-custom-providers``` toe om snel antwoord te krijgen.

## <a name="next-steps"></a>Volgende stappen

In dit artikel vindt u informatie over het gebruik van ingebouwd beleid voor het implementeren van koppelingen. Raadpleeg de volgende artikelen voor meer informatie:

- [Concepten: voor bereiding van resources in azure Custom providers](../custom-providers/concepts-resource-onboarding.md)
- [Zelf studie: onboarding van resources met aangepaste providers](../custom-providers/tutorial-resource-onboarding.md)
- [Zelf studie: aangepaste acties en resources maken in azure](../custom-providers/tutorial-get-started-with-custom-providers.md)
- [Snelstartgids: een aangepaste resource provider maken en aangepaste resources implementeren](../custom-providers/create-custom-provider.md)
- [Procedure: aangepaste acties toevoegen aan een Azure-REST API](../custom-providers/custom-providers-action-endpoint-how-to.md)
- [Procedure: aangepaste resources toevoegen aan een Azure-REST API](../custom-providers/custom-providers-resources-endpoint-how-to.md)
