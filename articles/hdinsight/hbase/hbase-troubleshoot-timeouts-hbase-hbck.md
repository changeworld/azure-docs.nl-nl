---
title: Time-outs met de opdracht ' hbase hbck ' in azure HDInsight
description: Er is een time-outprobleem opgetreden met de opdracht ' hbase hbck ' bij het herstellen van regio toewijzingen
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 5604b42e1611830f3aaea9ae180cdb8142ab0942
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75887186"
---
# <a name="scenario-timeouts-with-hbase-hbck-command-in-azure-hdinsight"></a>Scenario: time-outs met de opdracht ' hbase hbck ' in azure HDInsight

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Er worden time-outs gevonden met `hbase hbck` opdracht bij het herstellen van regio toewijzingen.

## <a name="cause"></a>Oorzaak

Een mogelijke oorzaak van problemen met de time-out wanneer u de `hbck`-opdracht gebruikt, kan het zijn dat verschillende regio's in de status ' in overgang ' gedurende een lange periode zijn. U kunt deze regio's als offline weer geven in de gebruikers interface van HBase Master. Omdat er een groot aantal regio's probeert te overstappen, HBase Master mogelijk een time-out opgetreden en kan deze regio's niet weer online worden gezet.

## <a name="resolution"></a>Resolutie

1. Meld u met SSH aan bij het HDInsight HBase-cluster.

1. Voer `hbase zkcli` opdracht uit om verbinding te maken met Apache ZooKeeper shell.

1. Voer `rmr /hbase/regions-in-transition` of `rmr /hbase-unsecure/regions-in-transition` opdracht uit.

1. Sluit uit `hbase zkcli` shell met behulp van `exit` opdracht.

1. Start de Active HBase Master-service opnieuw vanuit de Apache Ambari-gebruikers interface.

1. Voer de opdracht `hbase hbck -fixAssignments` uit.

1. Bewaak de HBase Master gebruikers interface ' regio in overgang ' die sectie om ervoor te zorgen dat er geen regio achterblijft.

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

- Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

- Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) -het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring. Verbinding maken met de Azure-community met de juiste resources: antwoorden, ondersteuning en experts.

- Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees [hoe u een ondersteunings aanvraag voor Azure kunt maken](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)voor meer informatie. De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).
