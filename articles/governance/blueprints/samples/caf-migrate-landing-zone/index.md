---
title: Overzicht van het CAF-blauwdrukvoorbeeld voor de migratielandingszone
description: Overzicht en architectuur van het Cloud Adoption Framework (CAF) voor blauwdrukvoorbeeld van Azure Migration-landingszone.
ms.date: 08/20/2019
ms.topic: sample
ms.openlocfilehash: 08171a39c83ea6822243d7239882a19b053f213e
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74545499"
---
# <a name="overview-of-the-microsoft-cloud-adoption-framework-for-azure-migration-landing-zone-blueprint-sample"></a>Overzicht van het Microsoft Cloud Adoption Framework-blauwdrukvoorbeeld voor de Azure-migratielandingszone

De Microsoft CAF-blauwdruk (Cloud Adoption Framework) voor de Azure-migratielandingszone is een infrastructuur waarmee u uw eerste workload kunt migreren en uw cloudomgeving kunt beheren in overeenstemming met CAF.

Het [CAF Foundation](../caf-foundation/index.md)-blauwdrukvoorbeeld is een uitbreiding op dit voorbeeld.

## <a name="architecture"></a>Architectuur

Het CAF-blauwdrukvoorbeeld voor de migratielandingszone implementeert fundamentele infrastructuurresources in Azure, die door organisaties kunnen worden gebruikt om hun abonnement voor te bereiden op de migratie van virtuele machines. Het biedt ook ondersteuning bij het instellen van governance-controles die noodzakelijk zijn voor het beheren van de cloudresources. Met dit voorbeeld worden resources, beleid en sjablonen geïmplementeerd en afgedwongen die een organisatie helpen om zorgeloos aan de slag te gaan met Azure.

![De afbeelding van de CAF-migratielandingszone laat zien wat er wordt geïnstalleerd als onderdeel van CAF-richtlijnen voor de initiële landingszone ](../../media/caf-blueprints/caf-migration-landing-zone-architecture.png)

Deze omgeving bestaat uit diverse Azure-services die een veilige, volledig bewaakte en direct inzetbare governance bieden. De omgeving bestaat uit de volgende elementen:

- Een [Azure Key Vault](../../../../key-vault/key-vault-overview.md)-exemplaar om de geheimen te bewaren voor de certificaten, sleutels en geheimen die in de omgeving met gedeelde services worden geïmplementeerd
- [Log Analytics](../../../../azure-monitor/overview.md) wordt geïmplementeerd om te garanderen dat op een centrale locatie logboeken worden bijgehouden voor alle acties en services zodra u met de migratie begint
- Implementeer [Azure Security Center](../../../../security-center/security-center-intro.md) (standaardversie). Dit biedt beveiliging tegen bedreigingen voor uw gemigreerde workloads.
- Implementeer [Azure Virtual Network](../../../../virtual-network/virtual-networks-overview.md). Dit biedt een geïsoleerd netwerk en subnetten voor uw virtuele machine.
- Implementeer het [Azure Migrate-project](../../../..//migrate/migrate-overview.md) voor detectie en evaluatie. Er worden hulpprogramma's toegevoegd voor serverevaluatie, servermigratie, database-evaluatie en databasemigratie.  


Al deze elementen voldoen aan de aanbevolen procedures zoals gepubliceerd in [Azure Architecture Center - Referentiearchitecturen](/azure/architecture/reference-architectures/).

> [!NOTE]
> Met de CAF-migratieblauwdruk wordt een landingszone voor uw workloads ingericht. Naast deze fundamentele architectuur dient u nog wel de evaluatie en migratie van uw virtuele machines/databases uit te voeren.

Zie [Microsoft Cloud Adoption Framework voor Azure: migreren](/azure/architecture/cloud-adoption/migrate/) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

U hebt het overzicht en de architectuur van het CAF-blauwdrukvoorbeeld voor de migratielandingszone doorgenomen.

> [!div class="nextstepaction"]
> [CAF-blauwdrukvoorbeeld voor de migratielandingszone - Implementatiestappen](./deploy.md)

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van een blauwdruk](../../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../../concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](../../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../../concepts/resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).