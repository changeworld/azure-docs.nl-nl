---
title: Inhouds typen-QnA Maker
description: Inhouds typen bevatten veel standaard gestructureerde documenten, zoals PDF, DOC en TXT.
services: cognitive-services
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: 7c78f9ea261fa636cce50b69524802d0900e9d7b
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77650194"
---
# <a name="content-types-of-documents-you-can-add-to-a-knowledge-base"></a>Inhouds typen van documenten die u aan een kennis database kunt toevoegen
Inhouds typen bevatten veel standaard gestructureerde documenten, zoals PDF, DOC en TXT.

## <a name="file-and-url-data-types"></a>Bestands-en URL-gegevens typen

De onderstaande tabel bevat een overzicht van de typen inhoud en bestandsindelingen die worden ondersteund door de QnA Maker.

|Brontype|Type inhoud| Voorbeelden|
|--|--|--|
|URL|Veelgestelde vragen<br> (Plat, met secties of met een start pagina voor onderwerpen)<br>Ondersteunings pagina's <br> (Procedures voor één pagina, artikelen, problemen oplossen, enz.)|[Veelgestelde vragen](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs), <br>[Veelgestelde vragen met koppelingen](https://www.microsoft.com/en-us/software-download/faq),<br> [Veelgestelde vragen met de introductie pagina van de onderwerpen](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[Ondersteunings artikel](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF / DOC-BESTAND|Faq's<br> Product handleiding,<br> Brochures,<br> Waarop<br> Flyer-beleid,<br> Ondersteunings handleiding,<br> Gestructureerde QnA,<br> Heap.|**Zonder meerdere zetten**<br>[Structured QnA. doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx),<br> Voor [beeld van product manual. PDF](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> Voor [beeld van semi-Structured. doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> Voor [beeld van technisch document. PDF](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf),<br><br>**Meerdere zetten**:<br>[Surface Pro (DOCX)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Contoso-voor delen (DOCX)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Voor delen van Contoso (PDF)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)|
|\* Excel|Gestructureerd QnA-bestand<br> (inclusief RTF-, HTML-ondersteuning)|[Voor beeld van QnA FAQ. xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|\* TXT/TSV|Gestructureerd QnA-bestand|[Voor beeld van Chit-Chat. TSV](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

Als u verificatie voor uw gegevens bron nodig hebt, kunt u de volgende methoden voor het ophalen van die inhoud in QnA Maker:

* Down load het bestand hand matig en importeer het in QnA Maker
* Bestand toevoegen vanaf de geauthenticeerde [share point-locatie](../how-to/add-sharepoint-datasources.md)

## <a name="url-content"></a>URL-inhoud

Er kunnen twee typen documenten worden geïmporteerd via **URL** in QnA Maker:

* Veelgestelde vragen over URL 's
* Url's voor ondersteuning

Elk type geeft een verwachte indeling aan.

## <a name="file-based-content"></a>Op bestanden gebaseerde inhoud

U kunt bestanden toevoegen aan een Knowledge Base van een open bare bron of uw lokale bestands systeem in de [QnA Maker Portal](https://www.qnamaker.ai).

## <a name="content-format-guidelines"></a>Richt lijnen voor inhouds indeling

Meer informatie over de [richt lijnen](../reference-document-format-guidelines.md) voor de indeling van de verschillende bestanden.

## <a name="next-steps"></a>Volgende stappen

Begrijpen welke informatie wordt opgeslagen in een [set met vraag en antwoord (QnA)](question-answer-set.md).