---
title: Overzicht van open Shift in azure
description: Een overzicht van open Shift in Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/7/2019
ms.author: haroldw
ms.openlocfilehash: 021ebe010a27fa155de861121e1972466c800f4a
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035414"
---
# <a name="openshift-in-azure"></a>OpenShift in Azure

Openshift is een open en uitbreidbaar container toepassings platform dat docker en Kubernetes naar de onderneming brengt.  

Open Shift bevat Kubernetes voor container indeling en-beheer. Hiermee worden hulpprogram ma's voor ontwikkel aars en bewerkingen toegevoegd waarmee het volgende kan worden uitgevoerd:

- Snelle ontwikkeling van toepassingen.
- Eenvoudig implementeren en schalen.
- Onderhoud van de levens cyclus op lange termijn voor teams en toepassingen.

Er zijn meerdere versies van open Shift beschikbaar.  Van deze versies zijn er slechts twee beschikbaar voor klanten om te implementeren in Azure: open Shift container platform en OKD (voorheen open Shift-oorsprong).

## <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Microsoft Azure Red Hat open Shift is een volledig beheerd aanbod van open Shift dat wordt uitgevoerd in Azure. Deze service wordt gezamenlijk beheerd en ondersteund door micro soft en Red Hat. Zie de documentatie van [Azure Red Hat open Shift service](https://docs.microsoft.com/azure/openshift/) voor meer informatie.

## <a name="openshift-container-platform"></a>OpenShift Container Platform

Container platform is een [commerciële versie](https://www.openshift.com) van bedrijfs eigendom van en wordt ondersteund door Red Hat. Met deze versie kopen klanten de benodigde rechten voor open Shift container platform en zijn ze verantwoordelijk voor de installatie en het beheer van de volledige infra structuur.

Omdat klanten ' eigenaar ' van het hele platform zijn, kunnen ze het installeren in hun on-premises Data Center of in een open bare Cloud (zoals Azure).

## <a name="okd"></a>OKD

OKD is een [open-source](https://www.okd.io/) upstream-project van openshift dat wordt ondersteund door de community. OKD kan worden geïnstalleerd op CentOS of Red Hat Enterprise Linux (RHEL).

## <a name="next-steps"></a>Volgende stappen

- [Algemene vereisten voor open Shift configureren in azure](./openshift-container-platform-3x-prerequisites.md)
- [Open Shift container platform implementeren in azure](./openshift-container-platform-3x.md)
- [Zelf beheerde Marketplace-aanbieding voor open Shift container platform implementeren](./openshift-container-platform-3x-marketplace-self-managed.md)
- [Open Shift in Azure Stack implementeren](./openshift-azure-stack.md)
- [Taken na de implementatie](./openshift-container-platform-3x-post-deployment.md)
- [Problemen met de openshift-implementatie oplossen](./openshift-container-platform-3x-troubleshooting.md)
