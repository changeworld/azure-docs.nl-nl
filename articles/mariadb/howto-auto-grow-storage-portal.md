---
title: Opslag Azure Portal automatisch verg Roten-Azure Database for MariaDB
description: In dit artikel wordt beschreven hoe u automatische groei opslag voor Azure Database for MariaDB kunt inschakelen met behulp van Azure Portal
author: ambhatna
ms.author: ambhatna
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 5c5e9154260a255d9e9b8bc5775a479df7e41522
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74767497"
---
# <a name="auto-grow-storage-in-azure-database-for-mariadb-using-the-azure-portal"></a>Opslag in Azure Database for MariaDB automatisch verg Roten met behulp van de Azure Portal
In dit artikel wordt beschreven hoe u een Azure Database for MariaDB Server-opslag kunt configureren om te groeien zonder dat dit van invloed is op de werk belasting.

Wanneer een server de toegewezen opslag limiet bereikt, is de server gemarkeerd als alleen-lezen. Als u automatisch verg Roten van opslag inschakelt, neemt de server opslag echter toe om de groeiende gegevens te verwerken. Voor servers met een ingerichte opslag van minder dan 100 GB wordt de ingerichte opslag grootte met 5 GB verhoogd zodra de beschik bare opslag onder het hoogste van 1 GB of 10% van de ingerichte opslag ligt. Voor servers met meer dan 100 GB ingerichte opslag wordt de ingerichte opslag grootte verhoogd met 5% wanneer de beschik bare opslag ruimte lager is dan 5% van de ingerichte opslag grootte. De maximale opslag limieten die [hier](https://docs.microsoft.com/azure/mariadb/concepts-pricing-tiers#storage) zijn opgegeven, zijn van toepassing.

## <a name="prerequisites"></a>Vereisten
U hebt het volgende nodig om deze hand leiding te volt ooien:
- Een [Azure database for MariaDB server](./quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>Opslag automatisch verg Roten inschakelen 

Voer de volgende stappen uit om MariaDB-Server opslag automatisch uitbreiden in te stellen:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)uw bestaande Azure database for MariaDB-server.

2. Klik op de pagina MariaDB-server onder **instellingen** kop op **prijs categorie** om de pagina prijs categorie te openen.

3. In de sectie automatische groei selecteert u **Ja** om automatische groei van opslag in te scha kelen.

    ![Azure Database for MariaDB-Settings_Pricing_tier-automatische groei](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Klik op **OK** om de wijzigingen op te slaan.

5. Bij een melding wordt bevestigd dat automatisch uitbreiden is ingeschakeld.

    ![Azure Database for MariaDB-automatisch groei geslaagd](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het maken van waarschuwingen over metrische gegevens](howto-alert-metric.md).
