---
title: De service-principal van een beheerde identiteit weer geven in de Azure Portal-Azure AD
description: Stapsgewijze instructies voor het weer geven van de service-principal van een beheerde identiteit in de Azure Portal.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: c12f15cc79d5329d028239ade4e18a853000bf01
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79298594"
---
# <a name="view-the-service-principal-of-a-managed-identity-in-the-azure-portal"></a>De service-principal van een beheerde identiteit weer geven in de Azure Portal

Beheerde identiteiten voor Azure-resources bieden Azure-Services met een automatisch beheerde identiteit in Azure Active Directory. U kunt deze identiteit gebruiken voor verificatie bij elke service die ondersteuning biedt voor Azure AD-verificatie, zonder dat u referenties hebt in uw code. 

In dit artikel leert u hoe u de service-principal van een beheerde identiteit kunt weer geven met behulp van de Azure Portal.

 > [!NOTE] 
 > Service-principals zijn bedrijfs toepassingen. 

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met beheerde identiteiten voor Azure-resources, raadpleegt u de [sectie Overzicht](overview.md).
- Als u nog geen Azure-account hebt, [meldt u zich aan voor een gratis account](https://azure.microsoft.com/free/).
- [Systeem toegewezen identiteit inschakelen op een virtuele machine](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) of [toepassing](/azure/app-service/overview-managed-identity#add-a-system-assigned-identity).

## <a name="view-the-service-principal"></a>De Service-Principal weer geven

In deze procedure ziet u hoe u de service-principal van een virtuele machine kunt weer geven waarvoor door het systeem toegewezen identiteit is ingeschakeld (dezelfde stappen zijn van toepassing op een toepassing).

1. Klik op **Azure Active Directory** en klik vervolgens op **bedrijfs toepassingen**.
2. Kies onder **toepassings type** **alle toepassingen** en klik vervolgens op **Toep assen**.
3. In het vak Zoek filter typt u de naam van de virtuele machine of toepassing waarvoor beheerde identiteit is ingeschakeld of kiest u deze in de weer gegeven lijst.

   ![Service-Principal beheerde identiteit weer geven in de portal](./media/how-to-view-managed-identity-service-principal-portal/view-managed-identity-service-principal-portal.png)

## <a name="next-steps"></a>Volgende stappen

[Beheerde identiteiten voor Azure-resources](/azure/active-directory/managed-identities-azure-resources/overview)

