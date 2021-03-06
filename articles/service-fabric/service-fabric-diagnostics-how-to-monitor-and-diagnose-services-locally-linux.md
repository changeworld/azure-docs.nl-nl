---
title: Fouten opsporen in azure Service Fabric-apps in Linux
description: Meer informatie over het bewaken en diagnosticeren van uw Service Fabric Services op een lokale Linux-ontwikkel machine.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: d8b5ec2f2190586f5eced5eee112b190a82504c3
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/28/2019
ms.locfileid: "75526291"
---
# <a name="monitor-and-diagnose-services-in-a-local-linux-machine-development-setup"></a>Services controleren en diagnoses uitvoeren in een lokale installatie van Linux machine Development


> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
>
>

Voor het bewaken, detecteren, diagnosticeren en oplossen van problemen kunnen services worden voortgezet met een minimale onderbreking van de gebruikers ervaring. Bewaking en diagnose zijn essentieel in een werkelijke geïmplementeerde productie omgeving. Wanneer u een soortgelijk model tijdens de ontwikkeling van services aanneemt, zorgt u ervoor dat de diagnostische pijp lijn werkt wanneer u overstapt naar een productie omgeving. Service Fabric maakt het eenvoudig voor service ontwikkelaars om diagnostische gegevens te implementeren die naadloos kunnen worden gebruikt voor de installatie van lokale en productie clusters met één computer.


## <a name="debugging-service-fabric-java-applications"></a>Fout opsporing Service Fabric Java-toepassingen

Voor Java-toepassingen zijn [meerdere logboek registratie raamwerken](https://en.wikipedia.org/wiki/Java_logging_framework) beschikbaar. Omdat `java.util.logging` is de standaard optie met de JRE, wordt deze ook gebruikt voor de [code voorbeelden in github](https://github.com/Azure-Samples/service-fabric-java-getting-started). In de volgende bespreking wordt uitgelegd hoe u het `java.util.logging`-Framework kunt configureren.

Met Java. util. logging kunt u uw toepassings logboeken omleiden naar geheugen, uitvoer stromen, console bestanden of sockets. Voor elk van deze opties zijn er standaard-handlers die al in het Framework zijn opgenomen. U kunt een `app.properties`-bestand maken om de bestandshandler voor uw toepassing te configureren om alle logboeken door te sturen naar een lokaal bestand.

Het volgende code fragment bevat een voorbeeld configuratie:

```java
handlers = java.util.logging.FileHandler

java.util.logging.FileHandler.level = ALL
java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.FileHandler.limit = 1024000
java.util.logging.FileHandler.count = 10
java.util.logging.FileHandler.pattern = /tmp/servicefabric/logs/mysfapp%u.%g.log
```

De map waarnaar het `app.properties` bestand verwijst, moet bestaan. Nadat het `app.properties` bestand is gemaakt, moet u ook het script voor het toegangs punt wijzigen, `entrypoint.sh` in de map `<applicationfolder>/<servicePkg>/Code/` om de eigenschap `java.util.logging.config.file` in te stellen op `app.properties` bestand. De vermelding moet er ongeveer uitzien als in het volgende code fragment:

```sh
java -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=<path to app.properties> -jar <service name>.jar
```


Deze configuratie resulteert in het verzamelen van Logboeken in een roteering op `/tmp/servicefabric/logs/`. Het logboek bestand in dit geval heet mysfapp% u .% g. log, waarbij:
* **% u** is een uniek nummer voor het oplossen van conflicten tussen gelijktijdige Java-processen.
* **% g** is het generatie nummer om onderscheid te maken tussen het draaiende Logboeken.

Als er geen handler expliciet is geconfigureerd, wordt de-console-handler standaard geregistreerd. U kunt de logboeken weer geven in syslog onder/var/log/syslog.

Zie de [code voorbeelden in github](https://github.com/Azure-Samples/service-fabric-java-getting-started)voor meer informatie.


## <a name="debugging-service-fabric-c-applications"></a>Fout opsporing C# service Fabric toepassingen


Er zijn meerdere frameworks beschikbaar voor het traceren van CoreCLR-toepassingen in Linux. Zie [github: Logging](http:/github.com/aspnet/logging)(Engelstalig) voor meer informatie.  Omdat Event source vertrouwd is C# met ontwikkel aars, wordt in dit artikel Event Source gebruikt voor het traceren van CoreCLR-voor beelden in Linux.

De eerste stap is het toevoegen van System. Diagnostics. tracering, zodat u uw logboeken kunt schrijven naar geheugen, uitvoer stromen of console bestanden.  Voor logboek registratie met Event source voegt u het volgende project toe aan uw project. json:

```json
    "System.Diagnostics.StackTrace": "4.0.1"
```

U kunt een aangepaste EventListener gebruiken om te Luis teren naar de service gebeurtenis en ze vervolgens op de juiste wijze omleiden naar traceer bestanden. Het volgende code fragment toont een voor beeld van de implementatie van logboek registratie met behulp van Event source en een aangepaste EventListener:


```csharp

public class ServiceEventSource : EventSource
{
        public static ServiceEventSource Current = new ServiceEventSource();

        [NonEvent]
        public void Message(string message, params object[] args)
        {
            if (this.IsEnabled())
            {
                var finalMessage = string.Format(message, args);
                this.Message(finalMessage);
            }
        }

        // TBD: Need to add method for sample event.

}

```


```csharp
internal class ServiceEventListener : EventListener
{

        protected override void OnEventSourceCreated(EventSource eventSource)
        {
            EnableEvents(eventSource, EventLevel.LogAlways, EventKeywords.All);
        }
        protected override void OnEventWritten(EventWrittenEventArgs eventData)
        {
                using (StreamWriter Out = new StreamWriter( new FileStream("/tmp/MyServiceLog.txt", FileMode.Append)))
                {
                        // report all event information
                        Out.Write(" {0} ", Write(eventData.Task.ToString(), eventData.EventName, eventData.EventId.ToString(), eventData.Level,""));
                        if (eventData.Message != null)
                                Out.WriteLine(eventData.Message, eventData.Payload.ToArray());
                        else
                        {
                                string[] sargs = eventData.Payload != null ? eventData.Payload.Select(o => o.ToString()).ToArray() : null; 
                                Out.WriteLine("({0}).", sargs != null ? string.Join(", ", sargs) : "");
                        }
                }
        }
}
```


Het voor gaande fragment voert de logboeken uit naar een bestand in `/tmp/MyServiceLog.txt`. Deze bestands naam moet op de juiste manier worden bijgewerkt. Als u de logboeken wilt omleiden naar de console, gebruikt u het volgende code fragment in uw aangepaste EventListener-klasse:

```csharp
public static TextWriter Out = Console.Out;
```

De voor beelden [ C# ](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started) van voor beelden gebruiken Event source en een aangepaste EventListener om gebeurtenissen in een bestand te registreren.



## <a name="next-steps"></a>Volgende stappen
Dezelfde tracerings code die aan uw toepassing wordt toegevoegd, werkt ook met de diagnostische gegevens van uw toepassing op een Azure-cluster. Bekijk deze artikelen over de verschillende opties voor de hulpprogram ma's en beschrijf hoe u deze kunt instellen.
* [Logboeken verzamelen met Azure Diagnostics](service-fabric-diagnostics-how-to-setup-lad.md)
