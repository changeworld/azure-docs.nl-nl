---
title: Live Azure Service Fabric-apps met Application Insights profileren
description: Profiler inschakelen voor een Service Fabric toepassing
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 664d6eb377185613a1a5670daf6747b482c79d9d
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671610"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>Live Azure Service Fabric-toepassingen met Application Insights profileren

U kunt ook Application Insights Profiler op deze services implementeren:
* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="set-up-the-environment-deployment-definition"></a>De definitie van de implementatie van de omgeving instellen

Application Insights Profiler is opgenomen in Azure Diagnostics. U kunt de Azure Diagnostics-extensie installeren met behulp van een Azure Resource Manager sjabloon voor uw Service Fabric cluster. Een [sjabloon ophalen waarmee Azure Diagnostics op een service Fabric cluster wordt geïnstalleerd](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json).

Voer de volgende acties uit om uw omgeving in te stellen:

1. Profiler ondersteunt .NET Framework en .net core. Als u .NET Framework gebruikt, zorg er dan voor dat u [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) of hoger gebruikt. Het is voldoende om te bevestigen dat het geïmplementeerde besturings systeem `Windows Server 2012 R2` of hoger is. Profiler ondersteunt .NET Core 2,1 en nieuwere toepassingen.

1. Zoek naar de [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) extensie in het bestand met de implementatie sjabloon.

1. Voeg de volgende `SinksConfig` sectie toe als onderliggend element van `WadCfg`. Vervang de waarde van de eigenschap `ApplicationInsightsProfiler` door uw eigen Application Insights instrumentatie sleutel:  

      ```json
      "SinksConfig": {
        "Sink": [
          {
            "name": "MyApplicationInsightsProfilerSink",
            "ApplicationInsightsProfiler": "00000000-0000-0000-0000-000000000000"
          }
        ]
      }
      ```

      Zie [controle en diagnose gebruiken met een Windows-VM en Azure Resource Manager sjablonen](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)voor informatie over het toevoegen van de diagnostische extensie aan uw implementatie sjabloon.

1. Implementeer uw Service Fabric-cluster met behulp van uw Azure Resource Manager sjabloon.  
  Als uw instellingen juist zijn, wordt Application Insights Profiler geïnstalleerd en ingeschakeld wanneer de uitbrei ding Azure Diagnostics wordt geïnstalleerd. 

1. Voeg Application Insights toe aan uw Service Fabric-toepassing.  
  Voor Profiler voor het verzamelen van profielen voor uw aanvragen moet uw toepassing tracking bewerkingen met Application Insights. Voor stateless Api's kunt u verwijzen naar instructies voor het [bijhouden van aanvragen voor profile ring](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json). Zie [aangepaste bewerkingen bijhouden met Application Insights .NET SDK](custom-operations-tracking.md?toc=/azure/azure-monitor/toc.json)voor meer informatie over het bijhouden van aangepaste bewerkingen in andere soorten apps.

1. Implementeer uw toepassing opnieuw.


## <a name="next-steps"></a>Volgende stappen

* Verkeer genereren voor uw toepassing (bijvoorbeeld een [beschikbaarheids test](monitor-web-app-availability.md)starten). Wacht vervolgens tien tot vijf tien minuten voordat traceringen worden verzonden naar het Application Insights-exemplaar.
* Zie [Profiler traceringen](profiler-overview.md?toc=/azure/azure-monitor/toc.json) in de Azure Portal.
* Zie [problemen oplossen met Profiler](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)voor hulp bij het oplossen van problemen met profileringen.
