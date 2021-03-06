---
title: Linux-cluster gebeurtenissen bewaken in azure Service Fabric
description: Meer informatie over het bewaken van een Service Fabric Linux-cluster gebeurtenissen door Service Fabric platform gebeurtenissen te schrijven naar syslog.
author: srrengar
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: srrengar
ms.openlocfilehash: 5bd3bda71943b2ba8a34cd4fbd0b20917b875670
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645749"
---
# <a name="service-fabric-linux-cluster-events-in-syslog"></a>Linux-cluster gebeurtenissen Service Fabric in syslog

Service Fabric beschrijft een reeks platform gebeurtenissen om u te informeren over belang rijke activiteiten in uw cluster. De volledige lijst met gebeurtenissen die worden weer gegeven, is [hier](service-fabric-diagnostics-event-generation-operational.md)beschikbaar. Er zijn verschillende manieren om deze gebeurtenissen te gebruiken. In dit artikel wordt beschreven hoe u Service Fabric configureert om deze gebeurtenissen te schrijven naar syslog.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="introduction"></a>Inleiding

In de 6,4-release is de SyslogConsumer geïntroduceerd om de Service Fabric-platform gebeurtenissen te verzenden naar syslog voor Linux-clusters. Wanneer deze functie is ingeschakeld, worden gebeurtenissen automatisch gestroomd naar syslog dat kan worden verzameld en verzonden door de Log Analytics-agent.

Elke syslog-gebeurtenis heeft 4 onderdelen
* Opslagruimte
* Identity
* Bericht
* Ernst

De SyslogConsumer schrijft alle platform gebeurtenissen met behulp van faciliteit `Local0`. U kunt bijwerken naar een wille keurige geldige faciliteit door de configuratie configuratie te wijzigen. De gebruikte identiteit is `ServiceFabric`. Het veld bericht bevat de gehele gebeurtenis geserialiseerd in JSON, zodat deze kan worden opgevraagd en gebruikt door diverse hulpprogram ma's. 

## <a name="enable-syslogconsumer"></a>SyslogConsumer inschakelen

Als u de SyslogConsumer wilt inschakelen, moet u een upgrade van het cluster uitvoeren. De sectie `fabricSettings` moet worden bijgewerkt met de volgende code. Opmerking Deze code bevat alleen secties die betrekking hebben op SyslogConsumer

```json
    "fabricSettings": [
        {
            "name": "Diagnostics",
            "parameters": [
            {
                "name": "ConsumerInstances",
                "value": "AzureWinFabCsv, AzureWinFabCrashDump, AzureTableWinFabEtwQueryable, SyslogConsumer"
            }
            ]
        },
        {
            "name": "SyslogConsumer",
            "parameters": [
            {
                "name": "ProducerInstance",
                "value": "WinFabLttProducer"
            },
            {
            "name": "ConsumerType",
            "value": "SyslogConsumer"
            },
            {
                "name": "IsEnabled",
                "value": "true"
            }
            ]
        },
        {
            "name": "Common",
            "parameters": [
            {
                "name": "LinuxStructuredTracesEnabled",
                "value": "true"
            }
            ]
        }
    ],
```

Dit zijn de wijzigingen die u moet aanroepen
1. In de sectie algemeen is er een nieuwe para meter met de naam `LinuxStructuredTracesEnabled`. **Dit is vereist om Linux-gebeurtenissen gestructureerd en geserialiseerd te hebben wanneer ze worden verzonden naar syslog.**
2. In de sectie diagnostische gegevens is een nieuwe ConsumerInstance: SyslogConsumer toegevoegd. Dit geeft aan dat het platform een andere gebruiker van de gebeurtenissen is. 
3. De nieuwe sectie SyslogConsumer moet `IsEnabled` hebben als `true`. Het is geconfigureerd voor het automatisch gebruiken van de Local0-faciliteit. U kunt dit overschrijven door een andere para meter toe te voegen.

```json
    {
        "name": "New LogFacility",
        "value": "<Valid Syslog Facility>"
    }
```

## <a name="azure-monitor-logs-integration"></a>Integratie van Azure Monitor-logboeken
U kunt deze syslog-gebeurtenissen lezen in een bewakings programma, zoals Azure Monitor Logboeken. U kunt met behulp van de volgende [instructies] een Log Analytics-werk ruimte maken met behulp van de Azure Marketplace. (.. /Azure-monitor/Learn/Quick-Create-Workspace.MD) u moet ook de Log Analytics agent toevoegen aan uw cluster om deze gegevens te verzamelen en naar de werk ruimte te verzenden. Dit is dezelfde agent die wordt gebruikt voor het verzamelen van prestatie meter items. 

1. Ga naar de Blade `Advanced Settings`

    ![Werkruimte-instellingen](media/service-fabric-diagnostics-oms-syslog/workspace-settings.png)

2. Klik op `Data`
3. Klik op `Syslog`
4. Local0 configureren als de faciliteit die u wilt volgen. U kunt een andere faciliteit toevoegen als u deze hebt gewijzigd in fabricSettings

    ![Syslog configureren](media/service-fabric-diagnostics-oms-syslog/syslog-configure.png)
5. Ga naar de query Verkenner door te klikken op `Logs` in het menu van de werkruimte resource om query's te starten

    ![Werkruimte logboeken](media/service-fabric-diagnostics-oms-syslog/workspace-logs.png)
6. U kunt een query uitvoeren op de `Syslog` tabel die naar `ServiceFabric` zoekt als de proces naam. De onderstaande query is een voor beeld van het parseren van de JSON in de gebeurtenis en het weer geven van de inhoud ervan.

```kusto
    Syslog | where ProcessName == "ServiceFabric" | extend $payload = parse_json(SyslogMessage) | project $payload
```

![Syslog-query](media/service-fabric-diagnostics-oms-syslog/syslog-query.png)

Het bovenstaande voor beeld is van een NodeDown-gebeurtenis. U kunt [hier](service-fabric-diagnostics-event-generation-operational.md)de volledige lijst met gebeurtenissen bekijken.

## <a name="next-steps"></a>Volgende stappen
* [De Log Analytics-agent implementeren](service-fabric-diagnostics-oms-agent.md) naar uw knooppunten om te verzamelen prestatiemeteritems en verzamelen van Logboeken voor uw containers en docker-statistieken
* Krijg vertrouwd met de functies voor [Zoeken in Logboeken en query's](../log-analytics/log-analytics-log-searches.md) die worden aangeboden als onderdeel van Azure monitor logboeken
* [De weer gave Designer gebruiken om aangepaste weer gaven te maken in Azure Monitor-logboeken](../log-analytics/log-analytics-view-designer.md)
* Naslag informatie over het [Azure monitor van logboek integratie met syslog](../log-analytics/log-analytics-data-sources-syslog.md).
