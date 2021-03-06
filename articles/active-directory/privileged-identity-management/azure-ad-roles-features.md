---
title: Azure AD-rollen beheren in Privileged Identity Management (PIM) | Microsoft Docs
description: Azure AD-rollen beheren voor toewijzings Privileged Identity Management (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5d63577cbdbdf18cb17618bdb5c9e3b5de0e44a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79245978"
---
# <a name="management-capabilities-for-azure-ad-roles-in-privileged-identity-management"></a>Beheer mogelijkheden voor Azure AD-rollen in Privileged Identity Management

De beheer ervaring voor Azure AD-rollen in Privileged Identity Management is bijgewerkt om samen te komen hoe Azure AD-rollen en Azure-resource rollen worden beheerd. Voorheen hadden Privileged Identity Management voor Azure-resource rollen een aantal belang rijke functies die niet beschikbaar waren voor Azure AD-rollen.

Wanneer de update momenteel is geïmplementeerd, worden de twee in één beheer ervaring samengevoegd en krijgt u dezelfde functionaliteit voor Azure AD-rollen als voor Azure-resource rollen. In dit artikel wordt u geïnformeerd over de bijgewerkte functies en eventuele vereisten.


## <a name="time-bound-assignments"></a>Tijdgebonden toewijzingen

Voorheen werd Privileged Identity Management voor Azure AD-rollen u vertrouwd met roltoewijzingen met twee mogelijke statussen: *in aanmerking komend* en *permanent*. U kunt nu een begin-en eind tijd instellen voor elk type toewijzing. Deze toevoeging geeft u vier mogelijke statussen waarin u een toewijzing kunt plaatsen:

- Permanent geschikt
- Permanent actief
- In aanmerking komen, met de opgegeven begin-en eind datum voor de toewijzing
- Actief, met de opgegeven begin-en eind datum voor de toewijzing

In veel gevallen, zelfs als u niet wilt dat gebruikers een geschikte toewijzing en rollen activeren, kunt u uw Azure AD-organisatie nog steeds beveiligen door een verloop tijd voor toewijzingen in te stellen. Als u bijvoorbeeld enkele tijdelijke gebruikers hebt die in aanmerking komen, kunt u een verloop instellen zodat deze automatisch worden verwijderd uit de roltoewijzing wanneer het werk is voltooid.

## <a name="new-role-settings"></a>Instellingen voor nieuwe rol

We voegen ook nieuwe instellingen voor Azure AD-rollen toe. Voorheen kon u activerings instellingen alleen per rol configureren. Dat wil zeggen dat activerings instellingen, zoals vereisten voor multi-factor Authentication en vereisten voor incidenten/aanvraag ticket, worden toegepast op alle gebruikers die in aanmerking komen voor een opgegeven rol. U kunt nu configureren of een afzonderlijke gebruiker multi-factor Authentication moet uitvoeren voordat ze een rol kunnen activeren. U kunt ook geavanceerde controle hebben over uw Privileged Identity Management-e-mails die betrekking hebben op specifieke rollen.

## <a name="extend-and-renew-assignments"></a>Toewijzingen uitbreiden en vernieuwen

Zodra u een tijd afhankelijke toewijzing hebt, kan de eerste vraag worden gevraagd wat er gebeurt als een rol is verlopen? In deze nieuwe versie bieden we twee opties voor dit scenario:

- Uitbreiden: wanneer een roltoewijzing bijna is verlopen, kan de gebruiker Privileged Identity Management gebruiken om een uitbrei ding aan te vragen voor die roltoewijzing
- Verlengen: wanneer een roltoewijzing is verlopen, kan de gebruiker Privileged Identity Management gebruiken om een verlenging aan te vragen voor die roltoewijzing

Voor zowel door de gebruiker geïnitieerde acties is een goed keuring van een globale beheerder of een beheerder met een bevoegde rol vereist. Beheerders hoeven deze verloop tijd niet langer te beheersen. Ze hoeven alleen te wachten op de verlenging of de vernieuwings aanvragen en ze worden goedgekeurd als de aanvraag geldig is.

## <a name="api-changes"></a>API-wijzigingen

Wanneer klanten de bijgewerkte versie hebben geïmplementeerd naar hun Azure AD-organisatie, werkt de bestaande Graph-API niet meer. U moet overstappen om de [Graph API voor Azure-resource rollen](https://docs.microsoft.com/graph/api/resources/privilegedidentitymanagement-resources?view=graph-rest-beta)te gebruiken. Als u Azure AD-rollen wilt beheren met die API, swapt u `/azureResources` door `/aadroles` in de hand tekening en gebruikt u de map-ID voor de `resourceId`.

We hebben ons het beste getracht te bereiken voor alle klanten die de vorige API gebruiken om ze van tevoren op de hoogte te stellen. Als uw Azure AD-organisatie is verplaatst naar de nieuwe versie en u nog steeds afhankelijk bent van de oude API, kunt u aan het team bellen op pim_preview@microsoft.com.

## <a name="powershell-change"></a>Power shell-wijziging

Voor klanten die de Privileged Identity Management Power shell-module voor Azure AD-rollen gebruiken, zal de Power shell niet meer werken met de update. In plaats van de vorige cmdlets moet u de Privileged Identity Management-cmdlets in de Azure AD preview Power shell-module gebruiken. Installeer de Azure AD Power shell-module vanuit het [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). U kunt nu [de documentatie en voor beelden voor PIM-bewerkingen in deze Power shell-module lezen](powershell-for-azure-ad-roles.md).

## <a name="next-steps"></a>Volgende stappen

- [Een aangepaste Azure AD-rol toewijzen](azure-ad-custom-roles-assign.md)
- [Een aangepaste gebruikersrol toewijzing van Azure AD verwijderen of bijwerken](azure-ad-custom-roles-update-remove.md)
- [Een aangepaste functie toewijzing voor Azure AD configureren](azure-ad-custom-roles-configure.md)
- [Roldefinities in azure AD](../users-groups-roles/directory-assign-admin-roles.md)
