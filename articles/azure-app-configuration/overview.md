---
title: Wat is Azure App Configuration?
description: Een overzicht van de service Azure App Configuration.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: overview
ms.date: 02/19/2020
ms.openlocfilehash: 1f1cec68813d33e7fa19a414a30adfc9a41df91f
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523472"
---
# <a name="what-is-azure-app-configuration"></a>Wat is Azure App Configuration?

Azure-app-configuratie biedt een service voor het centraal beheren van toepassings instellingen en functie vlaggen. Moderne Program ma's, met name Program ma's die worden uitgevoerd in een Cloud, hebben doorgaans veel onderdelen die worden gedistribueerd. Het verspreiden van configuratie-instellingen over deze onderdelen kan leiden tot moeilijk oplosbare fouten tijdens de implementatie van een toepassing. Gebruik app-configuratie om alle instellingen voor uw toepassing op te slaan en de toegang op één locatie te beveiligen.

## <a name="why-use-app-configuration"></a>Waarom app-configuratie gebruiken?

Cloudgebaseerde toepassingen worden vaak uitgevoerd op meerdere virtuele machines of containers in meerdere regio's en gebruiken meerdere externe services. Het maken van een robuuste en schaal bare toepassing in een gedistribueerde omgeving vormt een aanzienlijke uitdaging.

Verschillende programmeer methodieken helpen ontwikkel aars bij het verg Roten van de complexiteit van het bouwen van toepassingen. In de app met [twaalf factoren](https://12factor.net/) worden bijvoorbeeld veel goed geteste architectuur patronen en best practices voor gebruik met Cloud toepassingen beschreven. Een belangrijke aanbeveling in deze handleiding is om de configuratie van de code te scheiden. De configuratie-instellingen van een toepassing moeten extern worden opgeslagen in het uitvoer bare bestand en worden gelezen vanuit de runtime-omgeving of een externe bron.

Hoewel een toepassing gebruik kan maken van app-configuratie, zijn de volgende voor beelden de typen toepassing die het gebruik ervan kunnen doen:

* Micro Services op basis van de Azure Kubernetes-service, Azure Service Fabric of andere apps die zijn geïmplementeerd in een of meer geographs
* Serverloze apps, die Azure Functions of andere op gebeurtenissen gebaseerde stateless Compute-apps bevatten
* Pijp lijn voor continue implementatie

App Configuration biedt de volgende voordelen:

* Een volledig beheerde service die binnen enkele minuten kan worden ingesteld
* Flexibele sleutel representaties en toewijzingen
* Labelen met labels
* Herhaling van instellingen naar een bepaald tijdstip
* Exclusieve gebruikers interface voor functie vlaggen beheer
* Vergelijking van twee sets configuraties op aangepaste dimensies
* Verbeterde beveiliging via door Azure beheerde identiteiten
* Versleuteling van gevoelige informatie in rust en onderweg
* Systeem eigen integratie met populaire Frameworks

App-configuratie complementen [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), die wordt gebruikt voor het opslaan van toepassings geheimen. Met app-configuratie kunt u de volgende scenario's eenvoudiger implementeren:

* Beheer en distributie van hiërarchische configuratie gegevens voor verschillende omgevingen en geographs centraliseren
* Toepassings instellingen dynamisch wijzigen zonder de nood zaak om een toepassing opnieuw te implementeren of opnieuw op te starten
* Beschik baarheid van functies in realtime best uren

## <a name="use-app-configuration"></a>App-configuratie gebruiken

De eenvoudigste manier om een app-configuratie archief toe te voegen aan uw toepassing, is via een client bibliotheek van micro soft. De volgende methoden zijn beschikbaar om verbinding te maken met uw toepassing, afhankelijk van uw gekozen taal en Framework

| Computertaal en framework | Verbinding maken |
|---|---|
| .NET core en ASP.NET Core | App-configuratie provider voor .NET core |
| .NET Framework en ASP.NET | App Configuration Builder voor .NET |
| Java Spring | App-configuratie client voor lente-Cloud |
| Andere | REST API app-configuratie |

## <a name="next-steps"></a>Volgende stappen

* [Snelstartgids ASP.NET Core](./quickstart-aspnet-core-app.md)
* [Snelstartgids voor .NET core](./quickstart-dotnet-core-app.md)
* [Snelstartgids .NET Framework](./quickstart-dotnet-app.md)
* [Snelstartgids Azure Functions](./quickstart-azure-functions-csharp.md)
* [Snelstartgids voor Java Spring](./quickstart-java-spring-app.md)
* [Quick start voor functie vlag ASP.NET Core](./quickstart-feature-flag-aspnet-core.md)
* [Snelstartgids voor Spring boot-functie](./quickstart-feature-flag-spring-boot.md)
