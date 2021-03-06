---
title: Trans acties en vergrendelings modi in betrouw bare verzamelingen
description: Azure Service Fabric betrouw bare status Manager en betrouw bare incasso transacties en vergren deling.
ms.topic: conceptual
ms.date: 5/1/2017
ms.custom: sfrev
ms.openlocfilehash: 5f7b3a4d43d35f0d2965dd33c8f69143f4b3a8f7
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76938914"
---
# <a name="transactions-and-lock-modes-in-azure-service-fabric-reliable-collections"></a>Trans acties en vergrendelings modi in azure Service Fabric reliable-verzamelingen

## <a name="transaction"></a>Transactie

Een trans actie is een opeenvolging van bewerkingen die worden uitgevoerd als één logische werk eenheid. Het vertoont de eigenschappen van het gemeen schappelijke [zuur](https://en.wikipedia.org/wiki/ACID) (*atomiciteit*, *consistentie*, *isolatie*, *duurzaamheid*) van database transacties:

* **Atomiciteit**: een trans actie moet een atoom werk eenheid zijn. Met andere woorden, alle wijzigingen in de gegevens worden uitgevoerd, of ze worden niet uitgevoerd.
* **Consistentie**: wanneer u klaar bent, moet een trans actie alle gegevens in een consistente status laten staan. Alle interne gegevens structuren moeten aan het einde van de trans actie juist zijn.
* **Isolatie**: wijzigingen aangebracht door gelijktijdige trans acties moeten worden geïsoleerd van de wijzigingen die zijn aangebracht door andere gelijktijdige trans acties. Het isolatie niveau dat voor een bewerking binnen een [ITransaction](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.itransaction?view=azure-dotnet) wordt gebruikt, wordt bepaald door de [IReliableState](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.ireliablestate?view=azure-dotnet) die de bewerking uitvoert.
* **Duurzaamheid**: nadat een trans actie is voltooid, zijn de gevolgen ervan permanent aanwezig in het systeem. De wijzigingen blijven behouden, zelfs in het geval van een systeem fout.

### <a name="isolation-levels"></a>Isolatieniveaus

Het isolatie niveau bepaalt de mate waarin de trans actie moet worden geïsoleerd van wijzigingen die zijn aangebracht door andere trans acties.
Er zijn twee isolatie niveaus die in betrouw bare verzamelingen worden ondersteund:

* **Herhaal bare Lees bewerking**: Hiermee geeft u op dat instructies geen gegevens kunnen lezen die zijn gewijzigd, maar nog niet zijn doorgevoerd door andere trans acties en dat geen andere trans acties gegevens kunnen wijzigen die door de huidige trans actie zijn gelezen totdat de huidige trans actie is voltooid.
* **Moment opname**: de gegevens die worden gelezen door een instructie in een trans actie, zijn de transactionele, consistente versie van de gegevens die bestonden aan het begin van de trans actie.
  De trans actie kan alleen gegevens wijzigingen herkennen die zijn vastgelegd vóór het begin van de trans actie.
  Gegevens wijzigingen die zijn aangebracht door andere trans acties na het begin van de huidige trans actie, zijn niet zichtbaar voor instructies die in de huidige trans actie worden uitgevoerd.
  Het effect is alsof de instructies in een trans actie een moment opname van de doorgevoerde gegevens ophalen die aan het begin van de trans actie bestonden.
  Moment opnamen zijn consistent in betrouw bare verzamelingen.

Betrouw bare verzamelingen kiezen automatisch het isolatie niveau dat moet worden gebruikt voor een bepaalde Lees bewerking, afhankelijk van de bewerking en de rol van de replica op het moment dat de trans actie wordt gemaakt.
Hieronder ziet u de tabel waarin de standaard waarden voor het isolatie niveau worden weer gegeven voor betrouw bare dictionary-en wachtrij bewerkingen.

| Bewerking \ rol | Primair | Secundair |
| --- |:--- |:--- |
| Enkele entiteit gelezen |Herhaal bare Lees bewerking |Momentopname |
| Opsomming, aantal |Momentopname |Momentopname |

> [!NOTE]
> Algemene voor beelden voor bewerkingen met één entiteit zijn `IReliableDictionary.TryGetValueAsync`, `IReliableQueue.TryPeekAsync`.
> 

Zowel de betrouw bare woorden lijst als de betrouw bare wachtrij ondersteunen *uw schrijf bewerkingen*.
Met andere woorden, elke schrijf bewerking binnen een trans actie is zichtbaar voor een lees bewerking die tot dezelfde trans actie behoort.

## <a name="locks"></a>Vergrendelingen

In betrouw bare verzamelingen implementeren alle trans acties strikte twee fase vergrendeling: een trans actie geeft de vergrendelde vergren deling niet vrij totdat de trans actie wordt beëindigd met een afbreken of door voeren.

Een betrouw bare woorden lijst maakt gebruik van vergren deling op rijniveau voor alle bewerkingen van één entiteit.
Betrouw bare wachtrij verhoudingen voor een strikte transactionele FIFO-eigenschap.
Betrouw bare wachtrij maakt gebruik van vergren delingen op bewerking niveau, waardoor één trans actie met `TryPeekAsync` en/of `TryDequeueAsync` en één trans actie met `EnqueueAsync` tegelijk kan worden uitgevoerd.
Houd er rekening mee dat u de FIFO wilt behouden als er in een `TryPeekAsync` of `TryDequeueAsync` ooit wordt geobserveerd dat de betrouw bare wachtrij leeg is, wordt `EnqueueAsync`ook vergrendeld.

Schrijf bewerkingen maken altijd exclusieve vergren delingen.
Voor lees bewerkingen is de vergren deling afhankelijk van een aantal factoren:

- Een lees bewerking die is uitgevoerd met snap shot-isolatie, is vergrendeld.
- Elke Herhaal bare Lees bewerking maakt standaard gebruik van gedeelde vergren delingen.
- Voor elke Lees bewerking die Herhaal bare Lees bewerkingen ondersteunt, kan de gebruiker echter om een update vergrendeling vragen in plaats van de gedeelde vergren deling.
Een update vergrendeling is een asymmetrische vergren deling die wordt gebruikt om te voor komen dat een gemeen schappelijke vorm van deadlock optreedt wanneer meerdere trans acties op een later tijdstip worden vergren delen voor mogelijke updates.

De vergrendelings compatibiliteits matrix vindt u in de volgende tabel:

| Aanvraag-\ verleend | Geen | Gedeeld | Update | Exclusive |
| --- |:--- |:--- |:--- |:--- |
| Gedeeld |Geen conflict |Geen conflict |conflicteren |conflicteren |
| Update |Geen conflict |Geen conflict |conflicteren |conflicteren |
| Exclusive |Geen conflict |conflicteren |conflicteren |conflicteren |

Het time-outargument in reliable Collection-Api's wordt gebruikt voor het detecteren van deadlocks.
Bijvoorbeeld, twee trans acties (T1 en T2) proberen K1 te lezen en bij te werken.
Het is mogelijk om deadlock, omdat deze beide uiteindelijk de gedeelde vergren deling hebben.
In dit geval is er een time-out opgestaan voor een of beide bewerkingen. Ik heb dit scenario, een update vergrendeling kan een dergelijke deadlock verhinderen.

## <a name="next-steps"></a>Volgende stappen

* [Werken met betrouwbare verzamelingen](service-fabric-work-with-reliable-collections.md)
* [Reliable Services meldingen](service-fabric-reliable-services-notifications.md)
* [Back-up en herstel (nood herstel) Reliable Services](service-fabric-reliable-services-backup-restore.md)
* [Configuratie van betrouw bare status Manager](service-fabric-reliable-services-configuration.md)
* [Naslag informatie voor ontwikkel aars voor betrouw bare verzamelingen](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
