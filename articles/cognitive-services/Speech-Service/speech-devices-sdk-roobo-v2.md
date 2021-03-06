---
title: Speech-apparaten SDK roobo Smart audio dev kit v2-Speech Service
titleSuffix: Azure Cognitive Services
description: Vereisten en instructies voor het aan de slag gaan met de SDK voor spraak apparaten, roobo Smart audio dev kit v2.
services: cognitive-services
author: anushapatnala
manager: wellsi
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: v-anusp
ms.openlocfilehash: 5cf851bc9333004c0e14713cde44f470fb8c0c02
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78304284"
---
# <a name="device-roobo-smart-audio-dev-kit-v2"></a>Apparaat: roobo Smart audio dev kit v2

Dit artikel bevat apparaatspecifieke informatie voor de roobo Smart audio dev Kit2.

## <a name="set-up-the-development-kit"></a>Instellen van de development kit

1. De Development Kit heeft twee micro USB-connectors. De linker connector is de Power Development Kit om te zetten en is gemarkeerd als stroom in de onderstaande afbeelding. De juiste is om deze te beheren en is gemarkeerd als debug in de installatie kopie. 
    ![verbinding maken met de dev kit](media/speech-devices-sdk/roobo-v2-connections.png)
1. Schakel de Development Kit uit met behulp van een micro USB-kabel om de voedings poort te verbinden met een PC of een stroom adapter. Een groene stroom indicator is afhankelijk van het bovenste bord.
1. Als u de Development Kit wilt beheren, verbindt u de poort voor fout opsporing met een computer met een tweede micro USB-kabel. Het is essentieel dat u een kabel van hoge kwaliteit gebruikt om betrouw bare communicatie te garanderen.
1. Uw Development Kit rond rechtop plaatsen, met microfoons die zijn gericht op het plafond zoals hierboven wordt weer gegeven


## <a name="development-information"></a>Ontwikkelings informatie

Zie de [roobo Development Guide (Engelstalig](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf)) voor meer informatie over ontwikkel aars.

## <a name="audio-recordplay"></a>Audio Record/Play

DDK2-audio bewerkingen kunnen op de volgende manieren worden uitgevoerd:
* Gebruik ALSA open-source-bibliotheken en hun toepassingen.
* Gebruik de appmainprog-interface voor het ontwikkelen van toepassingen. DDK2 audio-gerelateerde software Framework gebruikt het standaard ALSA-Framework, dat u kunt gebruiken libasound. Om software direct te ontwikkelen. U kunt ALSA arecord en aplay rechtstreeks gebruiken om audio te registreren en af te spelen.
