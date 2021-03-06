---
title: Profileer productie-apps in azure met Application Insights Profiler
description: Identificeer het warme pad in uw webserver code met een profilerings versie met weinig footprint.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: ce952bd248640d03fcff43284707614577df8469
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671644"
---
# <a name="profile-production-applications-in-azure-with-application-insights"></a>Profileer productie toepassingen in azure met Application Insights
## <a name="enable-application-insights-profiler-for-your-application"></a>Application Insights Profiler inschakelen voor uw toepassing

Azure-toepassing Insights Profiler biedt prestatie traceringen voor toepassingen die worden uitgevoerd in de productie omgeving in Azure. Profiler legt de gegevens automatisch op schaal vast zonder dat dit een negatieve invloed heeft op uw gebruikers. Profiler helpt u bij het identificeren van het ' hot ' codepad dat het langst duurt wanneer een bepaalde webaanvraag wordt verwerkt. 

Profiler werkt met .NET-toepassingen die zijn geïmplementeerd op de volgende Azure-Services. Specifieke instructies voor het inschakelen van Profiler voor elk Service type vindt u op de onderstaande koppelingen.

* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines-en virtuele-machine schaal sets](profiler-vm.md?toc=/azure/azure-monitor/toc.json)
* [**Preview** ASP.NET Core Azure Linux Web Apps](profiler-aspnetcore-linux.md?toc=/azure/azure-monitor/toc.json) 

Raadpleeg de [hand leiding](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)voor het oplossen van problemen als u Profiler hebt ingeschakeld, maar geen traceringen ziet.

## <a name="view-profiler-data"></a>Profiler gegevens weer geven

Voor Profiler voor het uploaden van traceringen moet uw toepassing actief aanvragen verwerken. Als u een experiment uitvoert, kunt u aanvragen genereren voor uw web-app met behulp van [Application Insights prestatie testen](https://docs.microsoft.com/vsts/load-test/app-service-web-app-performance-test). Als u Profiler hebt ingeschakeld, kunt u een korte belasting test uitvoeren. Terwijl de belasting test wordt uitgevoerd, selecteert u de knop **profiel nu** in het [deel venster **instellingen van Profiler** ](profiler-settings.md). Wanneer Profiler wordt uitgevoerd, worden de profielen op wille keurige tijdstippen per uur en gedurende een periode van twee minuten. Als uw toepassing een stabiele stroom van aanvragen afhandelt, wordt elk uur traceringen door Profiler geüpload.

Nadat uw toepassing een aantal verkeer heeft ontvangen en de Profiler tijd heeft gehad om de traceringen te uploaden, moet u traceringen hebben om weer te geven. Dit proces kan 5 tot 10 minuten duren. Als u traceringen wilt weer geven, selecteert u in het deel venster **prestaties** de optie **acties ondernemen**en selecteert u de knop **traceringen Profiler** .

![Voor beeld van profilerings logboeken van Application Insights performance ster][performance-blade]

Selecteer een voor beeld om een code verdeling weer te geven van de tijd die is besteed aan het uitvoeren van de aanvraag.

![Application Insights Trace Explorer][trace-explorer]

In Trace Explorer wordt de volgende informatie weer gegeven:

* **Hete pad weer geven**: Hiermee opent u het grootste Leaf-knoop punt of ten minste iets wat er is gesloten. In de meeste gevallen is dit knoop punt bijna een prestatie knelpunt.
* **Label**: de naam van de functie of gebeurtenis. De boom structuur bevat een combi natie van code en gebeurtenissen, zoals SQL-en HTTP-gebeurtenissen. De eerste gebeurtenis vertegenwoordigt de totale duur van de aanvraag.
* **Verstreken**: het tijds interval tussen het begin van de bewerking en het einde van de bewerking.
* **Wanneer**: de tijd waarop de functie of gebeurtenis werd uitgevoerd ten opzichte van andere functies.

## <a name="how-to-read-performance-data"></a>Prestatie gegevens lezen

Micro soft service Profiler maakt gebruik van een combi natie van bemonsterings methoden en instrumentatie voor het analyseren van de prestaties van uw toepassing. Wanneer de gedetailleerde verzameling wordt uitgevoerd, wordt de instructie verwijzing van elke computer-CPU elke milliseconde door de service-Profiler voor beelden van de opdracht wijzer. Elk voor beeld legt de volledige aanroep stack vast van de thread die momenteel wordt uitgevoerd. Het bevat gedetailleerde informatie over de werking van de thread, op zowel een hoog niveau als een laag abstractie niveau. De service Profiler verzamelt ook andere gebeurtenissen om de activiteit correlatie en causality bij te houden, inclusief context wisselings gebeurtenissen, TPL-gebeurtenissen (Task Parallel Library) en gebeurtenissen van thread groepen.

De aanroep stack die wordt weer gegeven in de tijdlijn weergave, is het resultaat van de steek proef en instrumentatie. Omdat elk voor beeld de volledige aanroep stack van de thread vastlegt, bevat het code van Microsoft .NET Framework en van andere frameworks waarnaar u verwijst.

### <a id="jitnewobj"></a>Object toewijzing (CLR) JIT\_nieuwe of CLR! JIT\_Newarr1)

**CLR! JIT-\_nieuw** en **CLR! JIT\_Newarr1** zijn hulp functies in .NET Framework die geheugen van een beheerde heap toewijzen. **CLR! JIT-\_nieuw** wordt aangeroepen wanneer een object wordt toegewezen. **CLR! JIT\_Newarr1** wordt aangeroepen wanneer een object matrix wordt toegewezen. Deze twee functies zijn doorgaans snel en nemen relatief weinig tijd in beslag. Als **CLR! JIT\_nieuwe** of **CLR! JIT\_Newarr1** in uw tijd lijn veel tijd in beslag neemt, kan de code veel objecten toewijzen en aanzienlijke hoeveel heden geheugen gebruiken.

### <a id="theprestub"></a>De code wordt geladen (CLR! ThePreStub)

**CLR! ThePreStub** is een hulp functie in .NET Framework waarmee de code wordt voor bereid voor de eerste keer wordt uitgevoerd. Deze uitvoering omvat gewoonlijk, maar is niet beperkt tot JIT-compilatie (just-in-time). Voor elke C# methode **CLR! ThePreStub** moet Maxi maal één keer worden aangeroepen tijdens een proces.

Als **CLR! ThePreStub** neemt een lange tijd in beslag voor een aanvraag, de aanvraag is de eerste om die methode uit te voeren. De tijd voor het laden van .NET Framework de eerste methode is aanzienlijk. U kunt overwegen een opwarm-proces te gebruiken dat het gedeelte van de code uitvoert voordat uw gebruikers er toegang tot hebben, of u kunt overwegen om de systeem eigen image generator (Ngen. exe) uit te voeren in uw assembly's.

### <a id="lockcontention"></a>Conflicten vergren delen (CLR! JITutil\_MonContention of CLR! JITutil\_MonEnterWorker)

**CLR! JITutil\_MonContention** of **CLR! JITutil\_MonEnterWorker** geeft aan dat de huidige thread wacht totdat een vergren deling wordt vrijgegeven. Deze tekst wordt vaak weer gegeven wanneer u een C# **Lock** -instructie uitvoert, de monitor aanroept **. Voer** de methode in of roep een methode aan met het kenmerk **MethodImplOptions. Synchronized** . Vergrendelings conflicten treedt doorgaans op wanneer thread _a_ een vergren deling ophaalt en thread _B_ probeert dezelfde vergren deling te verkrijgen voordat thread _a_ deze uitgeeft.

### <a id="ngencold"></a>Code laden ([koude])

Als de naam van de methode **[koude]** bevat, zoals **mscorlib.ni! [ KOUD] System. reflectie. CustomAttribute. IsDefined**, .NET Framework runtime voert code voor de eerste keer uit die niet is geoptimaliseerd door de [profiel optimalisatie](/cpp/build/profile-guided-optimizations). Voor elke methode moet deze Maxi maal eenmaal worden weer gegeven tijdens het proces.

Als het laden van code een aanzienlijke hoeveelheid tijd in beslag neemt voor een aanvraag, is de aanvraag de eerste om het niet-geoptimaliseerde gedeelte van de methode uit te voeren. Overweeg het gebruik van een opwarm-proces dat dat gedeelte van de code uitvoert voordat uw gebruikers er toegang tot hebben.

### <a id="httpclientsend"></a>HTTP-aanvraag verzenden

Methoden zoals **httpclient maakt. Send** geven aan dat de code wacht tot een HTTP-aanvraag is voltooid.

### <a id="sqlcommand"></a>Database bewerking

Methoden zoals **SqlCommand. Execute** geven aan dat de code wacht tot de database bewerking is voltooid.

### <a id="await"></a>Wachten (nog\_tijd)

In **Afwachting\_tijd** geeft aan dat de code wacht tot een andere taak is voltooid. Deze vertraging treedt meestal op met C# de instructie **AWAIT** . Wanneer de C# **code een ogen**blik geduld, wordt de thread ontslagen en wordt de besturing teruggegeven aan de thread groep. er is geen thread **die wacht totdat de bewerking** is voltooid. De thread die de **wacht** tijd ' geblokkeerd ' had, is echter in afwachting van het volt ooien van de bewerking. De instructie **AWAIT\_time** geeft de geblokkeerde tijd aan die wacht totdat de taak is voltooid.

### <a id="block"></a>Geblokkeerde tijd

**BLOCKED_TIME** geeft aan dat de code wacht totdat een andere resource beschikbaar is. Het kan bijvoorbeeld wachten op een synchronisatie object, voor een thread die beschikbaar is of voor het volt ooien van een aanvraag.

### <a name="unmanaged-async"></a>Niet-beheerde async

.NET Framework verzendt ETW-gebeurtenissen en geeft activiteit-id's door aan tussen threads zodat asynchrone aanroepen kunnen worden getraceerd tussen threads. Niet-beheerde code (systeem eigen code) en sommige oudere stijlen van asynchrone code ontbreken deze gebeurtenissen en activiteit-id's, waardoor de Profiler niet kan zien welke thread en welke functies op de thread worden uitgevoerd. Dit is het label ' niet-beheerde async ' in de aanroep stack. Als u het ETW-bestand downloadt, kunt u [PerfView](https://github.com/Microsoft/perfview/blob/master/documentation/Downloading.md) gebruiken om meer inzicht te krijgen in wat er gebeurt.

### <a id="cpu"></a>CPU-tijd

De CPU is bezig met het uitvoeren van de instructies.

### <a id="disk"></a>Schijf tijd

De toepassing voert schijf bewerkingen uit.

### <a id="network"></a>Netwerk tijd

De toepassing voert netwerk bewerkingen uit.

### <a id="when"></a>Wanneer kolom

De kolom **Wanneer** is een visualisatie van de manier waarop de inclusieve steek proeven voor een knoop punt in de loop van de tijd variëren. Het totale bereik van de aanvraag is onderverdeeld in 32 tijd buckets. De inclusieve voor beelden voor dat knoop punt worden gecumuleerd in deze 32 buckets. Elke Bucket wordt weer gegeven als een balk. De hoogte van de balk vertegenwoordigt een geschaalde waarde. Voor knoop punten die zijn gemarkeerd **CPU_TIME** of **BLOCKED_TIME**, of waarbij een duidelijke relatie is voor het gebruik van een resource (bijvoorbeeld een CPU, schijf of thread), vertegenwoordigt de balk het verbruik van een van de resources tijdens de Bucket. Voor deze metrische gegevens is het mogelijk om een waarde van meer dan 100 procent op te halen door meerdere resources te gebruiken. Als u bijvoorbeeld gemiddeld twee Cpu's gebruikt tijdens een interval, krijgt u 200 procent.

## <a name="limitations"></a>Beperkingen

De standaard Bewaar periode voor gegevens is vijf dagen. De maximale hoeveelheid gegevens die per dag wordt opgenomen, is 10 GB.

Er worden geen kosten in rekening gebracht voor het gebruik van de profilerings service. Als u deze wilt gebruiken, moet uw web-app worden gehost in ten minste de Basic-laag van de functie Web Apps van Azure App Service.

## <a name="overhead-and-sampling-algorithm"></a>Algoritme voor overhead en sampling

Profiler wordt wille keurig twee minuten elk uur uitgevoerd op elke virtuele machine die als host fungeert voor de toepassing die Profiler heeft ingeschakeld voor het vastleggen van traceringen. Wanneer Profiler wordt uitgevoerd, wordt er een CPU-overhead van 5 tot 15 procent toegevoegd aan de server.

## <a name="next-steps"></a>Volgende stappen
Schakel Application Insights Profiler in voor uw Azure-toepassing. Zie ook:
* [App Services](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines-en virtuele-machine schaal sets](profiler-vm.md?toc=/azure/azure-monitor/toc.json)


[performance-blade]: ./media/profiler-overview/performance-blade-v2-examples.png
[trace-explorer]: ./media/profiler-overview/trace-explorer.png
