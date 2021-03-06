---
title: 'Azure Databricks: algemene vragen en Help'
description: Krijg antwoorden op veelgestelde vragen en informatie over het oplossen van problemen met Azure Databricks.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/25/2018
ms.openlocfilehash: 8d7aab43641c6c594ff60368ccb3810e0c060dd7
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671567"
---
# <a name="frequently-asked-questions-about-azure-databricks"></a>Veelgestelde vragen over Azure Databricks

In dit artikel vindt u een overzicht van de belangrijkste vragen die u mogelijk hebt gerelateerd aan Azure Databricks. Ook vindt u hier enkele veelvoorkomende problemen die u mogelijk hebt tijdens het gebruik van Databricks. Zie [Wat is Azure Databricks](what-is-azure-databricks.md)voor meer informatie. 

## <a name="can-i-use-azure-key-vault-to-store-keyssecrets-to-be-used-in-azure-databricks"></a>Kan ik Azure Key Vault gebruiken om sleutels/geheimen op te slaan die moeten worden gebruikt in Azure Databricks?
Ja. U kunt Azure Key Vault gebruiken om sleutels/geheimen op te slaan voor gebruik met Azure Databricks. Zie [Azure Key Vault-ondersteunde bereiken](/azure/databricks/security/secrets/secret-scopes)voor meer informatie.


## <a name="can-i-use-azure-virtual-networks-with-databricks"></a>Kan ik virtuele netwerken van Azure gebruiken met Databricks?
Ja. U kunt een Azure Virtual Network (VNET) gebruiken met Azure Databricks. Zie [deploying Azure Databricks in uw Azure Virtual Network](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)voor meer informatie.

## <a name="how-do-i-access-azure-data-lake-storage-from-a-notebook"></a>Hoe kan ik toegang tot Azure Data Lake Storage vanuit een notebook? 

Volg deze stappen:
1. In Azure Active Directory (Azure AD) richt u een Service-Principal in en noteert u de bijbehorende sleutel.
1. Wijs de benodigde machtigingen toe aan de Service-Principal in Data Lake Storage.
1. Gebruik de referenties van de Service-Principal in het notitie blok om toegang te krijgen tot een bestand in Data Lake Storage.

Zie [Azure data Lake Storage gebruiken met Azure Databricks](/azure/databricks/data/data-sources/azure/azure-datalake)voor meer informatie.

## <a name="fix-common-problems"></a>Veelvoorkomende problemen oplossen

Hier volgen enkele problemen die kunnen optreden met Databricks.

### <a name="issue-this-subscription-is-not-registered-to-use-the-namespace-microsoftdatabricks"></a>Probleem: dit abonnement is niet geregistreerd voor gebruik van de naam ruimte ' micro soft. Databricks '

#### <a name="error-message"></a>Foutbericht

' Dit abonnement is niet geregistreerd voor gebruik van de naam ruimte ' micro soft. Databricks '. Zie https://aka.ms/rps-not-found voor het registreren van abonnementen. (Code: MissingSubscriptionRegistration) "

#### <a name="solution"></a>Oplossing

1. Ga naar de [Azure Portal](https://portal.azure.com).
1. Selecteer **abonnementen**, het abonnement dat u gebruikt en vervolgens **resource providers**. 
1. Selecteer in de lijst met resource providers bij **micro soft. Databricks**de optie **registreren**. U moet de rol Inzender of eigenaar hebben voor het abonnement om de resource provider te registreren.


### <a name="issue-your-account-email-does-not-have-the-owner-or-contributor-role-on-the-databricks-workspace-resource-in-the-azure-portal"></a>Probleem: uw account {email} heeft niet de rol owner of Inzender in de Databricks werkruimte resource in de Azure Portal

#### <a name="error-message"></a>Foutbericht

"Uw account {email} heeft geen rol voor eigenaar of bijdrager in de Databricks werkruimte resource in de Azure Portal. Deze fout kan ook optreden als u een gast gebruiker bent in de Tenant. Vraag uw beheerder u toegang te verlenen of als gebruiker rechtstreeks toe te voegen aan de Databricks-werk ruimte. 

#### <a name="solution"></a>Oplossing

Hier volgen enkele oplossingen voor dit probleem:

* Als u de Tenant wilt initialiseren, moet u zijn aangemeld als een gewone gebruiker van de Tenant, niet als een gast gebruiker. U moet ook een rol Inzender hebben op de Databricks werkruimte resource. U kunt een gebruiker toegang verlenen vanaf het tabblad **toegangs beheer (IAM)** in uw Databricks-werk ruimte in de Azure Portal.

* Deze fout kan ook optreden als de naam van uw e-mail domein is toegewezen aan meerdere directory's in azure AD. U kunt dit probleem omzeilen door een nieuwe gebruiker te maken in de map met het abonnement met uw Databricks-werk ruimte.

    a. Ga in de Azure Portal naar Azure AD. Selecteer **gebruikers en groepen** > **een gebruiker toe te voegen**.

    b. Voeg een gebruiker toe met een `@<tenant_name>.onmicrosoft.com`-e-mail in plaats van `@<your_domain>` e-mail adres. U kunt deze optie vinden in **aangepaste domeinen**onder Azure AD in de Azure Portal.
    
    c. Ken deze nieuwe gebruiker de rol **Inzender** toe aan de Databricks werkruimte resource.
    
    d. Meld u aan bij de Azure Portal met de nieuwe gebruiker en zoek de Databricks-werk ruimte.
    
    e. Start de Databricks-werk ruimte als deze gebruiker.


### <a name="issue-your-account-email-has-not-been-registered-in-databricks"></a>Probleem: uw account {email} is niet geregistreerd in Databricks 

#### <a name="solution"></a>Oplossing

Als u de werk ruimte niet hebt gemaakt en u als gebruiker bent toegevoegd, neemt u contact op met de persoon die de werk ruimte heeft gemaakt. Deze persoon kunt u toevoegen met behulp van de Azure Databricks-beheer console. Zie voor instructies [gebruikers toevoegen en beheren](/azure/databricks/administration-guide/users-groups/users). Als u de werk ruimte hebt gemaakt en deze fout blijft staan, kunt u proberen om de **werk ruimte initialiseren** opnieuw te selecteren in de Azure Portal.

### <a name="issue-cloud-provider-launch-failure-while-setting-up-the-cluster-publicipcountlimitreached"></a>Probleem: het starten van de Cloud provider bij het instellen van het cluster (PublicIPCountLimitReached)

#### <a name="error-message"></a>Foutbericht

Fout bij het starten van de Cloud provider: er is een fout in de Cloud provider opgetreden tijdens het instellen van het cluster. Zie de Databricks-gids voor meer informatie. Azure-fout code: PublicIPCountLimitReached. Azure-fout bericht: kan niet meer dan 10 open bare IP-adressen maken voor dit abonnement in deze regio.

#### <a name="background"></a>Achtergrond

Databricks-clusters gebruiken één openbaar IP-adres per knoop punt (inclusief het knoop punt van het stuur programma). Azure-abonnementen hebben [limieten voor openbaar IP-adres](/azure/azure-resource-manager/management/azure-subscription-service-limits#publicip-address) per regio. Het maken en opschalen van clusters kan dus mislukken als dit ertoe leidt dat het aantal open bare IP-adressen die zijn toegewezen aan het abonnement in die regio de limiet overschrijdt. Deze limiet omvat ook open bare IP-adressen die zijn toegewezen voor niet-Databricks gebruik, zoals aangepaste, door de gebruiker gedefinieerde Vm's.

Over het algemeen gebruiken clusters alleen open bare IP-adressen wanneer ze actief zijn. `PublicIPCountLimitReached` fouten kunnen echter korte tijd blijven optreden, zelfs nadat andere clusters zijn beëindigd. Dit komt doordat Databricks de Azure-resources tijdelijk in de cache opslaat wanneer een cluster wordt beëindigd. Het in de cache opslaan van resources is standaard, omdat de latentie van het opstarten en automatisch schalen van het cluster aanzienlijk wordt gereduceerd in veel gang bare scenario's.

#### <a name="solution"></a>Oplossing

Als uw abonnement de open bare IP-adres limiet voor een bepaalde regio al heeft bereikt, moet u een van de volgende handelingen uitvoeren.

- Nieuwe clusters maken in een andere Databricks-werk ruimte. De andere werk ruimte moet zich bevinden in een regio waarin u de open bare IP-adres limiet van uw abonnement niet hebt bereikt.
- [Vraag om uw open bare IP-adres limiet te verg Roten](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request). Kies **quotum** als het **probleem type**en **netwerk: arm** als het **quotum type**. Vraag in **Details**een toename van een openbaar IP-adres quotum aan. Als uw limiet bijvoorbeeld 60 is, en u een cluster met een 100-knoop punt wilt maken, moet u een limiet voor 160 aanvragen.

### <a name="issue-a-second-type-of-cloud-provider-launch-failure-while-setting-up-the-cluster-missingsubscriptionregistration"></a>Probleem: een tweede type fout bij het starten van een Cloud provider tijdens het instellen van het cluster (MissingSubscriptionRegistration)

#### <a name="error-message"></a>Foutbericht

Fout bij het starten van de Cloud provider: er is een fout in de Cloud provider opgetreden tijdens het instellen van het cluster. Zie de Databricks-gids voor meer informatie.
Azure-fout code: MissingSubscriptionRegistration Azure-fout bericht: het abonnement is niet geregistreerd voor het gebruik van de naam ruimte ' micro soft. Compute '. Zie https://aka.ms/rps-not-found voor het registreren van abonnementen.

#### <a name="solution"></a>Oplossing

1. Ga naar de [Azure Portal](https://portal.azure.com).
1. Selecteer **abonnementen**, het abonnement dat u gebruikt en vervolgens **resource providers**. 
1. Selecteer in de lijst met resource providers bij **micro soft. Compute**de optie **registreren**. U moet de rol Inzender of eigenaar hebben voor het abonnement om de resource provider te registreren.

Zie [resource providers en-typen](../azure-resource-manager/management/resource-providers-and-types.md)voor meer gedetailleerde instructies.

### <a name="issue-azure-databricks-needs-permissions-to-access-resources-in-your-organization-that-only-an-admin-can-grant"></a>Probleem: Azure Databricks moet machtigingen hebben voor toegang tot resources in uw organisatie die alleen door een beheerder kunnen worden verleend.

#### <a name="background"></a>Achtergrond

Azure Databricks is geïntegreerd met Azure Active Directory. U kunt machtigingen instellen in Azure Databricks (bijvoorbeeld op notitie blokken of clusters) door gebruikers op te geven uit Azure AD. Azure Databricks om de namen van de gebruikers uit uw Azure AD weer te geven, is de machtiging lezen vereist voor die informatie en wordt toestemming gegeven. Als de toestemming nog niet beschikbaar is, ziet u de fout.

#### <a name="solution"></a>Oplossing

Meld u aan als globale beheerder voor de Azure Portal. Ga voor Azure Active Directory naar het tabblad **gebruikers instellingen** en zorg ervoor dat **gebruikers toestemming kunnen geven voor apps die namens hen toegang hebben tot Bedrijfs gegevens** is ingesteld op **Ja**.

## <a name="next-steps"></a>Volgende stappen

- [Snelstartgids: aan de slag met Azure Databricks](quickstart-create-databricks-workspace-portal.md)
- [Wat is Azure Databricks?](what-is-azure-databricks.md)
