---
title: bestand opnemen
description: bestand opnemen
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 01/15/2020
ms.custom: include file
ms.openlocfilehash: cb43c8b8c952d8db6cf450a7015c22c85e7fe4b5
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76268281"
---
Het `objectIdType` (of **object-ID-type**) verwijst naar het type identiteit dat wordt gegeven aan een rol. Naast de typen `DeviceId` en `UserDefinedFunctionId` komen object-id-typen overeen met de eigenschappen van Azure Active Directory-objecten.

De volgende tabel bevat de ondersteunde object-id-typen in azure Digital Apparaatdubbels:

| Type | Beschrijving |
| --- | --- |
| UserID | Hiermee wijst u een rol toe aan een gebruiker. |
| DeviceId | Hiermee wijst u een rol toe aan een apparaat. |
| DomainName | Hiermee wijst u een rol toe aan een domein naam. Elke gebruiker met de opgegeven domein naam heeft de toegangs rechten van de bijbehorende rol. |
| TenantId | Hiermee wijst u een rol toe aan een Tenant. Elke gebruiker die lid is van de opgegeven Azure AD-Tenant-ID heeft de toegangs rechten van de bijbehorende rol. |
| ServicePrincipalId | Hiermee wijst u een rol toe aan een Service-Principal object-ID. |
| UserDefinedFunctionId | Hiermee wijst u een rol toe aan een door de gebruiker gedefinieerde functie (UDF). |