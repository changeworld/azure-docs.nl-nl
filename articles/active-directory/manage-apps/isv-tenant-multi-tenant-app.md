---
title: Een Azure-Tenant maken voor een toepassing met meerdere tenants
description: Richt lijnen voor onafhankelijke software leveranciers op het integreren met Azure Active Directory
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 637adba89445e6974e83486f0641576225ccd268
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/09/2019
ms.locfileid: "70812608"
---
# <a name="create-an-azure-tenant-for-a-multi-tenant-application"></a>Een Azure-Tenant maken voor een toepassing met meerdere tenants  

Als u toegang tot uw multi tenant-toepassing wilt verlenen, moet u een Azure Active Directory-Tenant maken om de toepassing te registreren en de Federatie van de identiteit van uw klant in te scha kelen. Zie [het juiste Federatie protocol kiezen voor uw multi tenant-toepassing](isv-choose-multi-tenant-federation.md). Met deze Tenant kunt u uw toepassing en de Federatie testen in een omgeving die vergelijkbaar is met de Azure AD-omgevingen van uw klanten.

## <a name="costs-of-hosting-a-multi-tenant-application"></a>Kosten voor het hosten van een multi tenant-toepassing

Azure Active Directory is beschikbaar in meerdere edities. [Bekijk de gedetailleerde functie vergelijking](https://azure.microsoft.com/pricing/details/active-directory/).

U kunt uw Azure-abonnement en Azure Active Directory gratis maken en basis functies gebruiken.

## <a name="create-your-tenant"></a>Uw Tenant maken

1. Maak uw Tenant. Zie [een ontwikkel omgeving instellen](../develop/quickstart-create-new-tenant.md).

2. Toegang tot uw toepassing met eenmalige aanmelding inschakelen en testen

   a. **Voor OIDC-of Oath-toepassingen** [registreert u uw toepassing](../develop/quickstart-register-app.md) als multi tenant-toepassing. Selecteer de optie accounts in een organisatorische map en persoonlijke micro soft-accounts in ondersteunde account typen

   b. **Voor SAML-en WS-based-toepassingen**kunt u [eenmalige aanmelding op basis van SAML configureren](configure-single-sign-on-non-gallery-applications.md) met behulp van een algemene SAML-sjabloon in azure AD.

U kunt indien nodig ook [een toepassing met één Tenant converteren naar meerdere tenants](../develop/howto-convert-app-to-be-multi-tenant.md) .

## <a name="next-steps"></a>Volgende stappen

[SSO in uw toepassing integreren](isv-sso-content.md)
