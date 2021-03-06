---
title: Koppeling delen om een toegangs pakket aan te vragen in het beheer van rechten van Azure AD-Azure Active Directory
description: Meer informatie over het delen van een koppeling voor het aanvragen van een toegangs pakket in Azure Active Directory rechten beheer.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea90032b1f0cfe598ffdb3d35448a996f3111036
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2020
ms.locfileid: "78968765"
---
# <a name="share-link-to-request-an-access-package-in-azure-ad-entitlement-management"></a>Koppeling delen om een toegangs pakket aan te vragen in het beheer van rechten van Azure AD

De meeste gebruikers in uw Directory kunnen zich aanmelden bij de portal van mijn toegang en er wordt automatisch een lijst met toegangs pakketten weer geven die ze kunnen aanvragen. Voor gebruikers van externe zakelijke partners die nog niet in uw Directory zijn, moet u deze een koppeling sturen die ze kunnen gebruiken om een toegangs pakket aan te vragen. 

Zolang de catalogus voor het toegangs pakket is [ingeschakeld voor externe gebruikers](entitlement-management-catalog-create.md) en u een [beleid hebt voor de map van de externe gebruiker](entitlement-management-access-package-request-policy.md), kan de externe gebruiker de koppeling naar mijn Access-Portal gebruiken om het toegangs pakket aan te vragen.

## <a name="share-link-to-request-an-access-package"></a>Koppeling delen om een toegangs pakket aan te vragen

**Vereiste rol:** Globale beheerder, gebruikers beheerder, catalogus eigenaar of toegangs pakket beheer

1. Klik in de Azure Portal op **Azure Active Directory** en klik vervolgens op **Identity governance**.

1. Klik in het menu links op **toegangs pakketten** en open vervolgens het toegangs pakket.

1. Op de pagina overzicht kopieert u de **koppeling naar de portal van mijn toegang**.

    ![Overzicht van toegangs pakketten-koppeling naar mijn Access-Portal](./media/entitlement-management-shared/my-access-portal-link.png)

    Het is belang rijk dat u de volledige koppeling naar de portal van mijn Access kopieert wanneer u deze naar een interne zakelijke partner verzendt. Dit zorgt ervoor dat de partner toegang krijgt tot de portal van uw Directory om de aanvraag te doen. De koppeling begint met `myaccess`, bevat een directory-hint en eindigt met een toegangs pakket-ID.  (Voor de Amerikaanse overheid wordt het domein in de koppeling naar de portal van mijn toegang `myaccess.microsoft.us`.)

    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. E-mail of verzend de koppeling naar uw externe zakelijke partner. Ze kunnen de koppeling delen met hun gebruikers om het toegangs pakket aan te vragen.

## <a name="next-steps"></a>Volgende stappen

- [Toegang aanvragen tot een toegangs pakket](entitlement-management-request-access.md)
- [Toegangs aanvragen goed keuren of weigeren](entitlement-management-request-approve.md)