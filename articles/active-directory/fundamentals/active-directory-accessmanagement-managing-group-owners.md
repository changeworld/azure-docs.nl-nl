---
title: Toevoegen of verwijderen van groepseigenaren - Azure Active Directory | Microsoft Docs
description: Groepseigenaren van instructies over het toevoegen of verwijderen met behulp van Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f83a48ad13fe4ae217319b2aa85adf976aa6a7ae
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77149844"
---
# <a name="add-or-remove-group-owners-in-azure-active-directory"></a>Toevoegen of verwijderen van eigenaren van groepen in Azure Active Directory
Azure Active Directory (Azure AD)-beveiligingsgroepen zijn eigendom van en beheerd door eigenaren van groepen. Groeps eigenaren kunnen gebruikers of service-principals zijn en kunnen de groep met inbegrip van lidmaatschap beheren. Alleen bestaande groeps eigenaren of groep-Beheerders kunnen groeps eigenaren toewijzen. Groepseigenaren hoeven te worden leden van de groep.

Wanneer een groep geen eigenaar heeft, kunnen beheerders van groeps beheer de groep blijven beheren. U kunt het beste elke groep ten minste één eigenaar hebben. Zodra de eigen aren zijn toegewezen aan een groep, kan de laatste eigenaar van de groep niet worden verwijderd. Zorg ervoor dat u een andere eigenaar selecteert voordat u de laatste eigenaar uit de groep verwijdert.

## <a name="add-an-owner-to-a-group"></a>Eigenaar toevoegen aan een groep
Hieronder vindt u instructies voor het toevoegen van een gebruiker als een eigenaar aan een groep met behulp van de Azure AD-Portal. Als u een Service-Principal als eigenaar van een groep wilt toevoegen, volgt u de instructies om dit te doen met behulp van [Power shell](https://docs.microsoft.com/powershell/module/Azuread/Add-AzureADGroupOwner?view=azureadps-2.0).

### <a name="to-add-a-group-owner"></a>De Groepseigenaar van een toevoegen
1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met het account van een globale administrator voor de map.

2. Selecteer **Azure Active Directory**, selecteer **groepen**en selecteer vervolgens de groep waarvoor u een eigenaar wilt toevoegen (voor dit voor beeld *MDM-beleid-West*).

3. Selecteer op de pagina **MDM-beleid-West-overzicht** de optie **eigen aren**.

    ![Beleid voor MDM - West-overzichtspagina met eigenaren-optie is gemarkeerd](media/active-directory-accessmanagement-managing-group-owners/add-owners-option-overview-blade.png)

4. Selecteer op de pagina **MDM-beleid-West-eigen** aars de optie **eigen aren toevoegen**, zoek naar en selecteer de gebruiker die de nieuwe groeps eigenaar wordt, en kies vervolgens **selecteren**.

    ![MDM - West - beleid eigenaars-pagina met de optie eigenaren toevoegen gemarkeerd](media/active-directory-accessmanagement-managing-group-owners/add-owners-owners-blade.png)

    Nadat u de nieuwe eigenaar hebt geselecteerd, kunt u de pagina **eigen aars** vernieuwen en de naam zien die is toegevoegd aan de lijst met eigen aren.

## <a name="remove-an-owner-from-a-group"></a>Een eigenaar verwijderen uit een groep
Een eigenaar verwijderen uit een groep met behulp van Azure AD.

### <a name="to-remove-an-owner"></a>Een eigenaar verwijderen
1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met het account van een globale administrator voor de map.

2. Selecteer **Azure Active Directory**, selecteer **groepen**en selecteer vervolgens de groep waarvoor u een eigenaar wilt verwijderen (voor dit voor beeld *MDM-beleid-West*).

3. Selecteer op de pagina **MDM-beleid-West-overzicht** de optie **eigen aren**.

    ![Beleid voor MDM - West-overzichtspagina met eigenaren-optie is gemarkeerd](media/active-directory-accessmanagement-managing-group-owners/remove-owners-option-overview-blade.png)

4. Selecteer op de pagina **MDM-beleid-West-eigen aars** de gebruiker die u wilt verwijderen als groeps eigenaar, kies **verwijderen** op de pagina gegevens van de gebruiker en selecteer **Ja** om uw beslissing te bevestigen.

    ![Informatie-pagina van de gebruiker met de optie verwijderen gemarkeerd](media/active-directory-accessmanagement-managing-group-owners/remove-owner-info-blade.png)

    Nadat u de eigenaar hebt verwijderd, kunt u teruggaan naar de pagina **eigen aars** en de naam weer geven is verwijderd uit de lijst met eigen aren.

## <a name="next-steps"></a>Volgende stappen
- [Managing access to resources with Azure Active Directory groups](active-directory-manage-groups.md) (Toegang tot resources beheren met Azure Active Directory-groepen)

- [Azure Active Directory cmdlets for configuring group settings](../users-groups-roles/groups-settings-cmdlets.md) (Azure Active Directory-cmdlets voor het configureren van groepsinstellingen)

- [Groepen gebruiken om toegang toe te wijzen aan een geïntegreerde SaaS-app](../users-groups-roles/groups-saasapps.md)

- [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](../hybrid/whatis-hybrid-identity.md)

- [Azure Active Directory cmdlets for configuring group settings](../users-groups-roles/groups-settings-v2-cmdlets.md) (Azure Active Directory-cmdlets voor het configureren van groepsinstellingen)
