---
title: Ondersteunde gegevens archieven in azure data share
description: Meer informatie over de gegevens archieven die worden ondersteund voor het gebruik van Azure data share.
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 11c759dc8865da9de63e3acbfa1d4e26836d010a
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77622446"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Ondersteunde gegevens archieven in azure data share

Azure data share biedt open en flexibel delen van gegevens, met inbegrip van de mogelijkheid om te delen vanuit en naar verschillende gegevens archieven. Gegevens providers kunnen gegevens delen van het ene type gegevens archief en hun gegevens gebruikers kunnen kiezen met welke gegevens opslag gegevens moeten worden ontvangen. 

In dit artikel vindt u meer informatie over de uitgebreide set met Azure-gegevens archieven die worden ondersteund in azure data share. U kunt ook informatie vinden over de combi Naties van gegevens archieven die kunnen worden gebruikt door gegevens providers en gegevens gebruikers. 

## <a name="what-data-stores-are-supported-in-azure-data-share"></a>Welke gegevens archieven worden ondersteund in azure data share? 

De onderstaande tabel bevat een overzicht van de ondersteunde gegevens bronnen voor Azure data share. 

| Gegevensarchief | Op moment opnamen gebaseerd delen | In-place delen 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage |✓ | |
| Azure Data Lake Storage Gen1 |✓ | |
| Azure Data Lake Storage Gen2 |✓ ||
| Azure SQL Database |Openbare Preview | |
| Azure Synapse Analytics (voorheen Azure SQL DW) |Openbare Preview | |
| Azure Data Explorer | |Openbare Preview |

## <a name="data-store-support-matrix"></a>Ondersteunings matrix voor gegevens opslag

Azure-gegevens share biedt gegevens gebruikers flexibiliteit bij het bepalen van een gegevens archief om gegevens te accepteren in. Gegevens die vanuit Azure SQL Database worden gedeeld, kunnen bijvoorbeeld worden ontvangen in Azure Data Lake Store Gen2, Azure SQL Database of Azure Synapse Analytics. Klanten kunnen kiezen in welke indeling gegevens moeten worden ontvangen bij het configureren van een ontvangen gegevens share. 

De onderstaande tabel bevat informatie over verschillende combi Naties en keuzen die gebruikers van gegevens hebben wanneer ze hun gegevens delen accepteren en configureren. Zie [toewijzing van gegevensset configureren](how-to-configure-mapping.md)voor meer informatie over het configureren van gegevensset-toewijzingen.

|  | Azure Blob Storage | Azure Data Lake Storage Gen1 | Azure Data Lake Storage Gen2 | Azure SQL Database | Azure Synapse Analytics 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage | ✓ || ✓|
| Azure Data Lake Storage Gen1 | ✓ | | ✓|
| Azure Data Lake Storage Gen2 | ✓ | | ✓|
| Azure SQL Database | ✓ | | ✓| ✓| ✓|
| Azure Synapse Analytics (voorheen Azure SQL DW) | ✓ | | ✓| ✓| ✓|

## <a name="share-from-a-storage-account"></a>Delen vanuit een opslag account
De Azure-gegevens share ondersteunt het delen van bestanden, mappen en bestands systemen van Azure Data Lake gen1 en Azure Data Lake Gen2. Het biedt ook ondersteuning voor het delen van blobs, mappen en containers vanuit Azure Blob Storage. Momenteel wordt alleen blok-BLOB ondersteund. Wanneer mappen worden gedeeld in delen op basis van moment opnamen, kan de gegevens gebruiker ervoor kiezen om een volledige kopie van de share gegevens te maken of om alleen nieuwe of bijgewerkte bestanden te kopiëren. Bestaande bestanden met dezelfde naam worden overschreven.

## <a name="share-from-a-sql-based-source"></a>Delen vanuit een bron op basis van SQL
Azure data share ondersteunt het delen van tabellen of weer gaven van Azure SQL Database en Azure Synapse Analytics (voorheen Azure SQL DW). Gegevens verbruiker kan ervoor kiezen om de gegevens in Azure Data Lake Storage Gen2 of Azure Blob Storage te accepteren als CSV-of Parquet-bestand. Met volledige moment opname wordt de inhoud van het doel bestand overschreven. De gegevens consument kan de gegevens ook in een SQL-tabel accepteren. Als de doel-SQL-tabel niet beschikbaar is op de gegevens verbruiker, maakt Azure data share de SQL-tabel met het bron schema. Met volledige moment opname wordt inhoud van de bron tabel toegevoegd aan de SQL-doel tabel. Incrementele moment opname wordt momenteel niet ondersteund.

## <a name="share-from-azure-data-explorer"></a>Delen vanuit Azure Data Explorer
Azure data share ondersteunt de mogelijkheid om data bases in-place te delen vanuit Azure Data Explorer-clusters. Gegevens provider kan delen op Data Base of cluster niveau. Wanneer gegevens worden gedeeld op database niveau, hebben gebruikers alleen toegang tot de specifieke data base (s) die worden gedeeld door de gegevens provider. Wanneer gegevens worden gedeeld op cluster niveau, heeft de gebruiker toegang tot alle data bases van het cluster van de provider, met inbegrip van toekomstige data bases die zijn gemaakt door de gegevens provider.

Voor toegang tot gedeelde data bases moet de gegevens consument zijn eigen Azure Data Explorer-cluster hebben. Azure Data Explorer-cluster van de gegevens verbruiker moet in hetzelfde Azure-Data Center vinden als het Azure Data Explorer-cluster van de gegevens provider. Wanneer het delen van een relatie tot stand is gebracht, maakt Azure data share een symbolische koppeling tussen de Azure Data Explorer-clusters van de provider en de consumer.

Azure Data Explorer ondersteunt twee modi voor gegevens opname: batch en streaming. De gegevens die worden ontvangen van de batch in de gedeelde data base, worden weer gegeven tussen enkele seconden en een paar minuten aan de kant van de gegevens gebruiker. De gegevens die van streaming worden ontvangen, kunnen tot wel 24 uur duren aan de kant van de gegevens gebruiker. 

## <a name="next-steps"></a>Volgende stappen

Ga door naar de zelf studie [uw gegevens delen](share-your-data.md) voor meer informatie over het delen van gegevens.
