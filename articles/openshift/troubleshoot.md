---
title: Problemen met Azure Red Hat OpenShift oplossen
description: Problemen oplossen en veelvoorkomende problemen oplossen met Azure Red Hat open Shift
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: troubleshooting
ms.date: 05/08/2019
ms.openlocfilehash: ee032cdf4a3f72b2cd2e7da0658effe75b6fb1fa
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76274932"
---
# <a name="troubleshooting-for-azure-red-hat-openshift"></a>Probleem oplossing voor Azure Red Hat open Shift

In dit artikel worden enkele veelvoorkomende problemen beschreven die optreden bij het maken of beheren van Microsoft Azure Red Hat open Shift-clusters.

## <a name="retrying-the-creation-of-a-failed-cluster"></a>Het maken van een mislukt cluster opnieuw proberen

Als u een Azure Red Hat open Shift-cluster maakt met behulp van de `az` CLI-opdracht, mislukt het opnieuw proberen van het maken.
Gebruik `az openshift delete` om het mislukte cluster te verwijderen en maak vervolgens een volledig nieuw cluster.

## <a name="hidden-azure-red-hat-openshift-cluster-resource-group"></a>Verborgen Azure Red Hat open Shift-cluster resource groep

Op dit moment wordt de `Microsoft.ContainerService/openShiftManagedClusters` resource die automatisch door de Azure CLI (`az openshift create` opdracht) wordt gemaakt, verborgen in de Azure Portal. Controleer in de weer gave **resource groep** de optie **verborgen typen weer** geven om de resource groep weer te geven.

![Scherm afbeelding van het selectie vakje verborgen type in de portal](./media/aro-portal-hidden-type.png)

## <a name="creating-a-cluster-results-in-error-that-no-registered-resource-provider-found"></a>Als een cluster wordt gemaakt, treedt er een fout op die geen geregistreerde resource provider heeft gevonden

Als het maken van een cluster resulteert in een fout die `No registered resource provider found for location '<location>' and API version '2019-04-30' for type 'openShiftManagedClusters'. The supported api-versions are '2018-09-30-preview`, maakt u deel uit van het voor beeld en moet u de [gereserveerde instanties van Azure virtual machines aanschaffen](https://aka.ms/openshift/buy) om het algemeen beschik bare product te gebruiken. Een reserve Ring vermindert uw uitgaven door vooraf te betalen voor volledig beheerde Azure-Services. Raadpleeg [*Wat is Azure Reservations*](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations) voor meer informatie over reserve ringen en hoe u geld bespaart.

## <a name="next-steps"></a>Volgende stappen

- Probeer het [Help Center voor Red Hat open Shift](https://help.openshift.com/) voor meer informatie over het oplossen van openstaande ploegen.

- Vind antwoorden op [Veelgestelde vragen over Azure Red Hat open Shift](openshift-faq.md).
