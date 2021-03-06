---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 11/05/2019
ms.author: alkohli
ms.openlocfilehash: 16647b6a13e64073ab570d36a8a380d0e36bd855
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73799776"
---
- Kopieer bestanden niet rechtstreeks naar een van de voorgemaakte shares. U moet een map maken onder de share en vervolgens bestanden naar die map kopiëren.
- Een map onder de *StorageAccount_BlockBlob* en *StorageAccount_PageBlob* is een container. Zo worden bijvoorbeeld containers gemaakt als *StorageAccount_BlockBlob/container* en *StorageAccount_PageBlob/container*.
- Elke map die direct onder *StorageAccount_AzureFiles* is gemaakt, wordt vertaald naar een Azure-bestands share.
- Als u een bestaand Azure-object (zoals een BLOB of een bestand) in de Cloud hebt die dezelfde naam heeft als het object dat wordt gekopieerd, wordt het bestand in de Cloud overschreven door Data Box.
- Elk bestand dat in *StorageAccount_BlockBlob* -en *StorageAccount_PageBlob* -shares wordt geschreven, wordt respectievelijk geüpload als een blok-Blob en een pagina-blob.
- Azure Blob-opslag biedt geen ondersteuning voor directory's. Als u een map in de map *StorageAccount_BlockBlob* maakt, worden virtuele mappen in de BLOB-naam gemaakt. Voor Azure Files wordt de daad werkelijke mapstructuur behouden.
- Een lege Directory-hiërarchie (zonder bestanden) die is gemaakt onder *StorageAccount_BlockBlob* -en *StorageAccount_PageBlob* -mappen, wordt niet geüpload.
- Als er fouten zijn bij het uploaden van gegevens naar Azure, wordt er een fouten logboek gemaakt in het doel-opslag account. Het pad naar dit fouten logboek is beschikbaar wanneer het uploaden is voltooid en u kunt het logboek controleren om de corrigerende actie te ondernemen. Verwijder geen gegevens uit de bron zonder de geüploade gegevens te verifiëren.
- Meta gegevens en NTFS-machtigingen voor bestanden blijven niet behouden wanneer de gegevens naar Azure Files worden geüpload. Het *kenmerk laatst gewijzigd* van de bestanden wordt bijvoorbeeld niet bewaard wanneer de gegevens worden gekopieerd.
