---
title: Taal analyse functies toevoegen aan teken reeks velden
titleSuffix: Azure Cognitive Search
description: Meertalige tekst analyse in meerdere talen voor niet-Engelse query's en indexen in azure Cognitive Search.
manager: nitinme
author: Yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/10/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: a97bee27b74aa211b4d4d56547726555edefa87a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79283145"
---
# <a name="add-language-analyzers-to-string-fields-in-an-azure-cognitive-search-index"></a>Taal analyse functies toevoegen aan teken reeks velden in een Azure Cognitive Search-index

Een *taal analyse* is een specifiek type [tekst analyse](search-analyzers.md) dat lexicale analyses uitvoert met behulp van de taal kundige regels van de doel taal. Elk doorzoekbaar veld heeft een eigenschap **Analyzer** . Als uw index vertaalde teken reeksen bevat, zoals afzonderlijke velden voor Engelse en Chinese tekst, kunt u taal analysen opgeven voor elk veld om toegang te krijgen tot de uitgebreide taal mogelijkheden van die analyse functies.  

Azure Cognitive Search ondersteunt 35-analyse functies die worden ondersteund door Lucene, en 50-analyse functies die worden ondersteund door een eigen micro soft-technologie voor natuurlijke taal verwerking die wordt gebruikt in Office en Bing.

## <a name="comparing-analyzers"></a>Analyse functies vergelijken

Sommige ontwikkel aars hebben mogelijk de voor keur aan een bekendere, eenvoudige, open-source oplossing van Lucene. De taal analysen van Lucene zijn sneller, maar de micro soft-analyse functies hebben geavanceerde mogelijkheden, zoals lemmatisering, het afmaken van woorden (in talen als Duits, Deens, Nederlands, Zweeds, Noors, Estland, finish, Hong aars, Slowaaks) en entiteit herkenning (Url's, e-mails, datums, cijfers). Voer, indien mogelijk, vergelijkingen uit van de micro soft-en lucene-analyse functies om te bepalen welke een beter aansluit. 

Indexeren met micro soft-analyse functies is gemiddeld twee tot drie keer langzamer dan hun bijbehorende Lucene, afhankelijk van de taal. Zoek prestaties moeten niet significant worden beïnvloed voor de gemiddelde grootte van query's. 

### <a name="english-analyzers"></a>Engelse analyse functies

De standaard-Analyzer is standaard-Lucene, die goed werkt voor het Engels, maar mogelijk niet ook en de Engelse analyse functie van Lucene of het Engelse analyse beleid van micro soft. 
 
+ De Engelse Analyzer van Lucene breidt de Standard Analyzer uit. Hiermee verwijdert u de possessives (van het begin) van woorden, wordt de functie voor het verbreken van de splitsing van het schema en de Engelse stop woorden verwijderd.  

+ De Engelse Analyzer van micro soft voert lemmatisering uit in plaats van de ontleding. Dit betekent dat het verbogen en onregelmatige woord vormen aanzienlijk beter kan worden verwerkt, wat resulteert in meer relevante zoek resultaten 

## <a name="configuring-analyzers"></a>Analyse functies configureren

Taal analysen worden gebruikt als-is. Voor elk veld in de index definitie kunt u de eigenschap **Analyzer** instellen op een analyse naam die de taal en talen stack (micro soft of lucene) aangeeft. Dezelfde analyse wordt toegepast bij het indexeren en zoeken naar dat veld. U kunt bijvoorbeeld afzonderlijke velden voor Engelse, Franse en Spaanse Hotel beschrijvingen hebben die naast elkaar in dezelfde index bestaan.

> [!NOTE]
> Het is niet mogelijk om een andere taal analyse te gebruiken tijdens de indexerings tijd dan bij het opvragen van een veld. Deze mogelijkheid is gereserveerd voor [aangepaste analyse](index-add-custom-analyzers.md)functies. Als u daarom probeert de eigenschappen **searchAnalyzer** of **indexAnalyzer** in te stellen op de naam van een taal analyse, retourneert de rest API een fout bericht. U moet in plaats daarvan de eigenschap **Analyzer** gebruiken.

Gebruik de para meter **searchFields** om op te geven in welke taal specifiek veld moet worden gezocht in uw query's. U kunt query voorbeelden bekijken die de eigenschap Analyzer in [Zoek documenten](https://docs.microsoft.com/rest/api/searchservice/search-documents)bevatten. 

Zie [Create index &#40;Azure Cognitive Search rest API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)voor meer informatie over eigenschappen van de index. Zie voor meer informatie over het analyseren van Azure Cognitive Search analyse functies [in azure Cognitive Search](https://docs.microsoft.com/azure/search/search-analyzers).

<a name="language-analyzer-list"></a>

## <a name="language-analyzer-list"></a>Language Analyzer-lijst 
 Hieronder ziet u de lijst met ondersteunde talen in combi natie met de namen van Lucene en micro soft Analyzer.  

|Taal|Naam van micro soft Analyzer|Naam van Lucene Analyzer|  
|--------------|-----------------------------|--------------------------|  
|Arabisch|ar.microsoft|AR. lucene|  
|Armeens||HY. lucene|  
|Bengalees|bn.microsoft||  
|Baskisch||eu.lucene|  
|Bulgaars|bg.microsoft|bg.lucene|  
|Catalaans|ca.microsoft|ca.lucene|  
|Vereenvoudigd Chinees|zh-Hans.microsoft|zh-Hans.lucene|  
|Traditioneel Chinees|zh-Hant.microsoft|zh-Hant.lucene|  
|Kroatisch|hr.microsoft||  
|Tsjechisch|cs.microsoft|cs.lucene|  
|Deens|da.microsoft|da.lucene|  
|Dutch|nl.microsoft|nl.lucene|  
|Nederlands|en.microsoft|en. lucene|  
|Estlands|et.microsoft||  
|Fins|fi.microsoft|fi.lucene|  
|Frans|fr.microsoft|fr. lucene|  
|Galicisch||gl.lucene|  
|Duits|de.microsoft|de.lucene|  
|Grieks|el.microsoft|el.lucene|  
|Gujarati|gu.microsoft||  
|Hebreeuws|he.microsoft||  
|Hindi|hi.microsoft|hi.lucene|  
|Hongaars|hu.microsoft|hu.lucene|  
|IJslands|is.microsoft||  
|Indonesisch (Bahasa)|id.microsoft|id.lucene|  
|Iers||ga.lucene|  
|Italiaans|it.microsoft|it.lucene|  
|Japans|ja.microsoft|ja.lucene|  
|Kanarees|kn.microsoft||  
|Koreaans|ko.microsoft|ko.lucene|  
|Lets|lv.microsoft|lv.lucene|  
|Litouws|lt.microsoft||  
|Malayalam|ml.microsoft||  
|Maleis (Latijns)|ms.microsoft||  
|Marathi|mr.microsoft||  
|Noors|nb.microsoft|no.lucene|  
|Perzisch||fa.lucene|  
|Pools|pl.microsoft|pl.lucene|  
|Portugees (Brazilië)|pt-Br.microsoft|pt-br. lucene|  
|Portugees (Portugal)|pt-Pt.microsoft|pt-Pt.lucene|  
|Punjabi|pa.microsoft||  
|Roemeens|ro.microsoft|ro.lucene|  
|Russisch|ru.microsoft|ru.lucene|  
|Servisch (Cyrillisch)|SR-Cyrillisch. micro soft||  
|Servisch (Latijns)|sr-latin.microsoft||  
|Slowaaks|sk.microsoft||  
|Sloveens|sl.microsoft||  
|Spaans|es.microsoft|es. lucene|  
|Zweeds|sv.microsoft|sv.lucene|  
|Tamil|ta.microsoft||  
|Telugu|te.microsoft||  
|Thais|th. micro soft|th. lucene|  
|Turks|tr.microsoft|tr. lucene|  
|Oekraïens|uk.microsoft||  
|Urdu|ur.microsoft||  
|Vietnamees|vi.microsoft||  

 Alle analyse functies met namen die zijn gekoppeld aan **lucene** , worden aangedreven door [de taal Analyseers van Apache Lucene](https://lucene.apache.org/core/6_6_1/core/overview-summary.html ).

## <a name="see-also"></a>Zie ook  

+ [Index &#40;van Azure Cognitive Search rest API maken&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  

+ [Klasse Analyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername)  

