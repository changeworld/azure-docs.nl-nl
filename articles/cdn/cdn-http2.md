---
title: HTTP/2-ondersteuning in Azure CDN | Microsoft Docs
description: Meer informatie over HTTP/2-en CDN-ondersteuning.
services: cdn
documentationcenter: ''
author: lichard
manager: erikre
editor: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/04/2017
ms.author: ril
ms.openlocfilehash: 703623e3f7f314d87417458f3f9a218dfdf45427
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849976"
---
# <a name="http2-support-in-azure-cdn"></a>HTTP/2-ondersteuning in Azure CDN

HTTP/2 is een belang rijke revisie van HTTP/1.1 \. Het biedt snellere webprestaties, gereduceerde reactie tijd en verbeterde gebruikers ervaring, terwijl de vertrouwde HTTP-methoden, status codes en semantiek behouden blijven. HTTP/2 is ontworpen voor gebruik met HTTP en HTTPS, maar veel client webbrowsers ondersteunen alleen HTTP/2 via TLS.

### <a name="http2-benefits"></a>HTTP/2-voor delen

De voor delen van HTTP/2 zijn onder andere:

*   **Multiplexing en gelijktijdigheid**

    Als u HTTP 1,1 gebruikt, zijn er meerdere TCP-verbindingen vereist voor het maken van meerdere bron aanvragen. Met HTTP/2 kunnen meerdere resources worden aangevraagd op één TCP-verbinding.

*   **Header compressie**

    Door de HTTP-headers voor de geleverde resources te comprimeren, wordt de tijd op de kabel aanzienlijk verminderd.

*   **Stroom afhankelijkheden**

    Met stroom afhankelijkheden kan de client aangeven dat de server een prioriteit heeft.


## <a name="http2-browser-support"></a>Ondersteuning voor HTTP/2-browsers

Alle belang rijke browsers hebben ondersteuning voor HTTP/2 geïmplementeerd in hun huidige versies. Niet-ondersteunde browsers worden automatisch teruggevallen op HTTP/1.1.

|Browser|Minimale versie|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

## <a name="enabling-http2-support-in-azure-cdn"></a>Ondersteuning voor HTTP/2 inschakelen in Azure CDN

Momenteel is HTTP/2-ondersteuning actief voor alle Azure CDN-profielen. Er is geen verdere actie vereist van klanten.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de voor delen van HTTP/2 in actie [deze demo van Akamai](https://http2.akamai.com/demo).

Raadpleeg de volgende bronnen voor meer informatie over HTTP/2:

*   [Start pagina voor HTTP/2-specificaties](https://http2.github.io/)
*   [Officiële Veelgestelde vragen over HTTP/2](https://http2.github.io/faq/)
*   [Akamai HTTP/2-informatie](https://http2.akamai.com/)

Zie [Azure CDN-overzicht](https://azure.microsoft.com/documentation/articles/cdn-overview/)voor meer informatie over de beschik bare functies van Azure CDN.