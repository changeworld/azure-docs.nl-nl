---
title: Voor beeld van API management-beleid-aanvraag toestaan met externe goed keurder
titleSuffix: Azure API Management
description: 'Voor beeld van Azure API management-beleid: demonstreert hoe aanvragen worden geautoriseerd met een externe autorisatie die een aangepaste of verouderde logica voor verificatie/autorisatie inkapselt.'
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/06/2018
ms.author: apimpm
ms.openlocfilehash: 99bf1068042eb7ab0c43e2a683ca7116d2e426f3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75442502"
---
# <a name="authorize-requests-using-external-authorizer"></a>Aanvragen met externe autorisatie toestaan

In dit artikel wordt een voor beeld gegeven van een Azure API management-beleid dat laat zien hoe u de toegang tot de API kunt beveiligen met behulp van een externe autoriseer die aangepaste verificatie/autorisatie logica inkapselt. Volg de stappen die worden beschreven in [een beleid instellen of bewerken](../set-edit-policies.md)om een beleids code in te stellen of te bewerken. Zie voor andere voor beelden [beleids voorbeelden](../policy-samples.md).

## <a name="policy"></a>Beleid

Plak de code in het **binnenkomende** blok.

[!code-xml[Main](../../../api-management-policy-samples/examples/Authorize requests using external authorizer.policy.xml)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over APIM-beleid:

+ [Beleid voor toegangs beperkingen](../api-management-access-restriction-policies.md)
+ [Voor beelden van beleid](../policy-samples.md)
