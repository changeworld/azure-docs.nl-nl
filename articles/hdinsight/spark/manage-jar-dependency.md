---
title: JAR-afhankelijkheden beheren-Azure HDInsight
description: In dit artikel worden aanbevolen procedures beschreven voor het beheren van afhankelijkheden van Java-archief (JAR) voor HDInsight-toepassingen.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: da3387dd9846847f7643ded43c8cbff8ed8b166e
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77135731"
---
# <a name="jar-dependency-management-best-practices"></a>Best practices voor het beheer van JAR-afhankelijkheden

Onderdelen die zijn geïnstalleerd op HDInsight-clusters, hebben afhankelijkheden van bibliotheken van derden. Normaal gesp roken wordt in een specifieke versie van algemene modules zoals guava naar deze ingebouwde onderdelen verwezen. Wanneer u een toepassing met de bijbehorende afhankelijkheden verzendt, kan dit een conflict veroorzaken tussen verschillende versies van dezelfde module. Als de versie van het onderdeel waarnaar u in het klassenpad verwijst, het eerste is, kunnen ingebouwde onderdelen uitzonde ringen genereren vanwege incompatibiliteit van de versie. Als ingebouwde onderdelen echter hun afhankelijkheden van het klassenpad injecteren, kan uw toepassing fouten als `NoSuchMethod`genereren.

Als u versie conflicten wilt voor komen, kunt u overwegen om uw toepassings afhankelijkheden te arceren.

## <a name="what-does-package-shading-mean"></a>Wat betekent pakket arcering?
Arcering biedt een manier om afhankelijkheden in te voegen en de naam ervan te wijzigen. De klassen worden opnieuw gelokaliseerd en de byte code en bronnen voor de bewerking worden opnieuw geschreven om een persoonlijke kopie van uw afhankelijkheden te maken.

## <a name="how-to-shade-a-package"></a>Hoe kan ik een pakket arceren?

### <a name="use-uber-jar"></a>Gebruik uber-jar
Uber-jar is een enkel jar-bestand dat zowel de toepassing jar als de bijbehorende afhankelijkheden bevat. De afhankelijkheden in uber-jar zijn standaard niet gearceerd. In sommige gevallen kan dit leiden tot een versie conflict als andere onderdelen of toepassingen verwijzen naar een andere versie van die bibliotheken. Om dit te voor komen, kunt u een uber-bestand maken met enkele (of alle) van de gearceerde afhankelijkheden.

### <a name="shade-package-using-maven"></a>Schaduw pakket met behulp van Maven
Maven kan toepassingen bouwen die zijn geschreven in Java en scala. Met maven-Shade-invoeg toepassing kunt u eenvoudig een gekleurd uber-jar maken.

In het onderstaande voor beeld ziet u een bestand `pom.xml` dat is bijgewerkt met een schaduw van een pakket met behulp van de Maven-Shade-invoeg toepassing.  De sectie XML `<relocation>…</relocation>` verplaatst klassen van package `com.google.guava` naar package `com.google.shaded.guava` door de overeenkomstige bestands vermeldingen van het JAR-bestand te verplaatsen en de betreffende byte code te herschrijven.

Nadat `pom.xml`is gewijzigd, kunt u `mvn package` uitvoeren om het gearceerde uber te maken.

```xml
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>3.2.1</version>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>shade</goal>
            </goals>
            <configuration>
              <relocations>
                <relocation>
                  <pattern>com.google.guava</pattern>
                  <shadedPattern>com.google.shaded.guava</shadedPattern>
                </relocation>
              </relocations>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
```

### <a name="shade-package-using-sbt"></a>Schaduw pakket met behulp van SBT
SBT is ook een hulp programma voor het bouwen van scala en Java. SBT heeft geen Shade-invoeg toepassing zoals Maven-Shad-plugin. U kunt `build.sbt` bestand wijzigen in schaduw pakketten. 

Als u bijvoorbeeld wilt scha duwen `com.google.guava`, kunt u de onderstaande opdracht toevoegen aan het `build.sbt` bestand:

```scala
assemblyShadeRules in assembly := Seq(
  ShadeRule.rename("com.google.guava" -> "com.google.shaded.guava.@1").inAll
)
```

Vervolgens kunt u `sbt clean` en `sbt assembly` uitvoeren om het geschakeerde jar-bestand te maken. 

## <a name="next-steps"></a>Volgende stappen

* [HDInsight IntelliJ-Hulpprogram Ma's gebruiken](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox)

* [Een scala maven-toepassing maken voor Spark in IntelliJ](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-create-standalone-application)
