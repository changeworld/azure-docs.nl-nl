---
title: Live streaming encoders die worden aanbevolen door Media Services-Azure | Microsoft Docs
description: Meer informatie over on-premises coderings Programma's voor live streamen aanbevolen door Media Services
services: media-services
keywords: code ring; encoders; media
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 02/10/2020
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 3b7a75ac1c0876d562dc49e9253fe734475a551a
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79298951"
---
# <a name="tested-on-premises-live-streaming-encoders"></a>Geteste on-premises live streaming encoders

In Azure Media Services vertegenwoordigt een [live gebeurtenis](https://docs.microsoft.com/rest/api/media/liveevents) (kanaal) een pijp lijn voor het verwerken van live-streaming-inhoud. De live-gebeurtenis ontvangt Live invoer stromen op een van de volgende twee manieren.

* Een on-premises Live Encoder verzendt een gegevensgestuurde multi-bitrate RTMP-of Smooth Streaming (gefragmenteerde MP4)-stream naar de live gebeurtenis die niet is ingeschakeld voor het uitvoeren van Live code ring met Media Services. De opgenomen streams passeren Live gebeurtenissen zonder verdere verwerking. Deze methode wordt **Pass-Through**genoemd. We raden u aan om met het Live coderings programma multi-bitrate streams te verzenden in plaats van een single-bitrate stream naar een Pass-through live-gebeurtenis om Adaptive Bitrate Streaming toe te staan aan de client. 

    Als u multi-bitrate streams gebruikt voor de Pass-through live-gebeurtenis, moeten de grootte van de video-GOP terug en de video fragmenten op verschillende bitsnelheden worden gesynchroniseerd om onverwacht gedrag bij het afspelen te voor komen.

  > [!TIP]
  > Het gebruik van een Pass-Through-methode is de voordeligste manier om live streamen uit te voeren.
 
* Een on-premises Live Encoder verzendt een stream met één bitsnelheid naar de live gebeurtenis die is ingeschakeld voor het uitvoeren van Live code ring met Media Services in een van de volgende indelingen: RTMP of Smooth Streaming (gefragmenteerde MP4). De live-gebeurtenis voert vervolgens Live encoding uit van de inkomende single-bitrate stream naar een multi-bitrate-video stroom (adaptief).

In dit artikel worden de geteste on-premises live streaming encoders besproken. Zie [uw on-premises encoder controleren](become-on-premises-encoder-partner.md) voor instructies voor het controleren van uw on-premises Live coderings programma

Zie voor gedetailleerde informatie over Live encoding met Media Services [live streamen met Media Services v3](live-streaming-overview.md).

## <a name="encoder-requirements"></a>Encoder vereisten

Encoders moeten TLS 1,2 ondersteunen bij het gebruik van HTTPS-of RTMP-protocollen.

## <a name="live-encoders-that-output-rtmp"></a>Live coderings Programma's die RTMP uitvoeren

Media Services raadt het gebruik aan van een van de volgende live-encoders met RTMP als uitvoer. De ondersteunde URL-schema's zijn `rtmp://` of `rtmps://`.

Bij het streamen via RTMP controleert u de instellingen voor de firewall en/of proxy om te zien of de uitgaande TCP-poorten 1935 en 1936 open zijn.<br/><br/>
Bij het streamen via RTMPS controleert u de instellingen voor de firewall en/of proxy om te zien of de uitgaande TCP-poorten 2935 en 2936 open zijn.

> [!NOTE]
> Encoders moeten TLS 1,2 ondersteunen bij het gebruik van RTMP-protocollen.

- Adobe Flash Media Live Encoder 3.2
- [Cambria Live 4,3](https://www.capellasystems.net/products/cambria-live/)
- Elementair Live (versie 2.14.15 en hoger)
- Haivision KB
- Haivision Makito X HEVC
- OBS Studio
- Switcher Studio (iOS)
- Telestream-Wirecast (versie 13.0.2 of hoger vanwege de TLS 1,2-vereiste)
- Telestream-Wirecast S (alleen RTMP wordt ondersteund)
- Teradek Slice 756
- TriCaster 8000
- Tricaster Mini HD-4
- VMIX
- xStream
- [Ffmpeg](https://www.ffmpeg.org)
- [GoPro](https://gopro.com/help/articles/block/getting-started-with-live-streaming) Helden 7 en held 8
- [Restream.io](https://restream.io/)

## <a name="live-encoders-that-output-fragmented-mp4"></a>Live coderings Programma's die gefragmenteerde MP4 uitvoeren

Media Services raadt u aan om een van de volgende Live coderings Programma's te gebruiken met multi-bitrate Smooth Streaming (gefragmenteerde MP4) als uitvoer. De ondersteunde URL-schema's zijn `http://` of `https://`.

> [!NOTE]
> Encoders moeten TLS 1,2 ondersteunen bij het gebruik van HTTPS-protocollen.

- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elementaire Live (versie 2.14.15 en hoger vanwege de TLS 1,2-vereiste)
- Envivio 4Caster C4 Gen III 
- Denk aan de communicatie selenio MCP3
- Media Excel Hero Live en Hero 4K (UHD/HEVC)
- [Ffmpeg](https://www.ffmpeg.org)

> [!TIP]
>  Als u live-gebeurtenissen in meerdere talen wilt streamen (bijvoorbeeld een Engels audio nummer en één Spaans audio spoor), kunt u dit doen met de media Excel live encoder die is geconfigureerd voor het verzenden van live feeds naar een Pass-through live-gebeurtenis.

## <a name="configuring-on-premises-live-encoder-settings"></a>Instellingen voor on-premises Live coderings Programma's configureren

Zie voor meer informatie over welke instellingen geldig zijn voor uw live-gebeurtenis type [vergelijking van live gebeurtenis typen](live-event-types-comparison.md).

### <a name="playback-requirements"></a>Vereisten voor afspelen

Als u de inhoud wilt afspelen, moet zowel een audio-als een video stroom aanwezig zijn. Afspelen van de stream alleen video wordt niet ondersteund.

### <a name="configuration-tips"></a>Configuratie tips

- Gebruik indien mogelijk een internetverbinding ' hardwired '.
- Wanneer u de vereisten voor de band breedte bepaalt, verdubbelt u de streaming bitrates. Hoewel het niet verplicht is, kunt u met deze eenvoudige regel de impact van de netwerk congestie verminderen.
- Wanneer u coderingsprogramma's op basis van software, sluit u alle onnodige programma's.
- Het wijzigen van de encoder configuratie nadat deze is gestart, heeft negatieve gevolgen voor de gebeurtenis. Configuratie wijzigingen kunnen ertoe leiden dat de gebeurtenis Insta Biel wordt. 
- Zorg ervoor dat u uw gebeurtenis in ruime tijd kunt instellen. Voor grootschalige gebeurtenissen raden we u aan om de installatie een uur voor uw gebeurtenis te starten.

## <a name="see-also"></a>Zie ook

[Live streamen met Media Services v3](live-streaming-overview.md)

## <a name="next-steps"></a>Volgende stappen

[Uw coderings programma verifiëren](become-on-premises-encoder-partner.md)
