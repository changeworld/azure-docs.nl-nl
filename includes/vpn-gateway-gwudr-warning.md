---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/28/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a852807ab685e85b76d26e5b39c99a32f645bbd7
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838165"
---
Door de gebruiker gedefinieerde routes met de bestemming 0.0.0.0/0 en Nsg's op de GatewaySubnet **worden niet ondersteund**. Gateways die met deze configuratie zijn gemaakt, worden geblokkeerd en kunnen niet worden gemaakt. Gateways hebben toegang tot de beheer controllers nodig om goed te kunnen functioneren.
