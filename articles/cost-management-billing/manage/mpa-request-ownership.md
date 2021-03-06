---
title: Eigendom aanvragen van facturering van Azure-abonnementen voor Microsoft Partner-overeenkomst (MPA)
description: Meer informatie over het aanvragen van eigendom van facturering van Azure-abonnementen van andere gebruikers.
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: f8f2db3e81c498757bfc39bf70999ce1e70c09da
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79117176"
---
# <a name="get-billing-ownership-of-azure-subscriptions-to-your-mpa-account"></a>Eigendom aanvragen van facturering van Azure-abonnementen voor uw MPA-account

Als u één geconsolideerde factuur wilt bieden voor beheerde services en Azure-verbruik, kan de Cloud Solution Provider (CSP) het eigendom van de facturering van Azure-abonnementen overnemen van hun klanten met een Direct Enterprise Agreement (EA).

Deze functie is alleen beschikbaar voor CSP-partners voor directe facturering die zijn gecertificeerd als [Azure Expert MSP](https://partner.microsoft.com/membership/azure-expert-msp). Het valt onder governance en beleid van Microsoft en voor bepaalde klanten is mogelijk een beoordeling en goedkeuring vereist.

Als u het eigendom van de facturering wilt aanvragen, moet u beschikken over de rol **Globale beheerder** of **Beheerderagent**. Zie [Partner Center - Assign users roles and permissions](https://docs.microsoft.com/partner-center/permissions-overview) (Engelstalig) voor meer informatie.

Dit artikel is van toepassing op factureringsrekeningen voor Microsoft Partner-overeenkomsten. Deze rekeningen worden gemaakt voor Cloud Solution Providers (CSP's) voor het beheren van de facturering voor hun klanten in de nieuwe commerce-ervaring. De nieuwe ervaring is alleen beschikbaar voor partners, die ten minste één klant hebben die een Microsoft-klantovereenkomst (MCA) heeft geaccepteerd en een Azure-plan heeft. [Controleer of u toegang hebt tot een Microsoft Partner-overeenkomst](#check-access-to-a-microsoft-partner-agreement).

## <a name="prerequisites"></a>Vereisten

1. Een [resellerrelatie ](https://docs.microsoft.com/partner-center/request-a-relationship-with-a-customer) met de klant tot stand brengen. Controleer in het [overzicht van geautoriseerde CSP's per regio](https://docs.microsoft.com/partner-center/regional-authorization-overview) of de tenant van de klant en de tenant van de partner zich in dezelfde regio bevinden.  

2. [Controleer of de klant de Microsoft-klantovereenkomst heeft geaccepteerd](https://docs.microsoft.com/partner-center/confirm-customer-agreement).

3. Stel het [Azure-plan](https://docs.microsoft.com/partner-center/purchase-azure-plan) in voor de klant. Als de klant aankopen doet via meerdere resellers, moet u een Azure-plan instellen voor elke combinatie van een klant en een wederverkoper.

## <a name="request-billing-ownership"></a>Eigendom van facturering aanvragen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met behulp van de referenties van de CSP-beheeragent.

2. Zoek naar **Kostenbeheer en facturering**.

   ![Schermopname van zoekopdracht in de Azure-portal naar kostenbeheer en facturering](./media/mpa-request-ownership/search-cmb.png)

3. Selecteer **Klanten** aan de linkerkant en selecteer vervolgens een klant in de lijst.

   ![Schermopname van het selecteren van klanten](./media/mpa-request-ownership/mpa-select-customers.png)        

4. Selecteer **Overdrachtsaanvragen** aan de linkerkant en selecteer vervolgens **Een nieuwe aanvraag toevoegen**.

   ![Schermopname van de selectie van overdrachtsaanvragen](./media/mpa-request-ownership/mpa-select-transfer-requests.png)

5. Voer het e-mailadres in van de gebruiker in de klantorganisatie die de overdrachtsaanvraag moet accepteren. De gebruiker moet een accounteigenaar voor een Enterprise Agreement zijn. Selecteer **Overdrachtsaanvraag verzenden**.

   ![Schermopname van de verzending van een overdrachtsaanvraag](./media/mpa-request-ownership/mpa-send-transfer-requests.png)

6. De gebruiker ontvangt een e-mail met instructies voor het beoordelen van uw overdrachtsaanvraag.

   ![Schermopname van de e-mail over de beoordeling van een overdrachtsaanvraag](./media/mpa-request-ownership/mpa-review-transfer-request-email.png)

7. De gebruiker kan de overdrachtsaanvraag goedkeuren door de koppeling in de e-mail te selecteren en de instructies te volgen.

    ![Schermopname van de e-mail over de beoordeling van een overdrachtsaanvraag](./media/mpa-request-ownership/mpa-review-transfer-request.png)

## <a name="check-the-transfer-request-status"></a>De status van de overdrachtsaanvraag controleren

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

2. Zoek naar **Kostenbeheer en facturering**.

   ![Schermopname van zoekopdracht in de Azure-portal naar kostenbeheer en facturering](./media/mpa-request-ownership/billing-search-cost-management-billing.png)

3. Selecteer **Klanten** aan de linkerkant.

   ![Schermopname van het selecteren van klanten](./media/mpa-request-ownership/mpa-select-customers.png)

4. Selecteer de klant in de lijst waarvoor u de overdrachtsaanvraag hebt verzonden.

5. Selecteer **Overdrachtsaanvragen** aan de linkerkant. Op de pagina Overdrachtsaanvragen wordt de volgende informatie weergegeven:

    ![Schermopname van de lijst met overdrachtsaanvragen](./media/mpa-request-ownership/mpa-select-transfer-requests-for-status.png)

   |Kolom|Definitie|
   |---------|---------|
   |Aanvraagdatum|De datum waarop de overdrachtsaanvraag is verzonden|
   |Ontvanger|Het e-mailadres van de gebruiker aan wie u de aanvraag voor het overdragen van het eigendom van facturering hebt verzonden|
   |Vervaldatum|De datum waarop de aanvraag verloopt|
   |Status|De status van de overdrachtsaanvraag|

    De overdrachtsaanvraag kan een van de volgende statussen hebben:

   |Status|Definitie|
   |---------|---------|
   |Wordt uitgevoerd|De gebruiker heeft de overdrachtsaanvraag niet geaccepteerd|
   |Wordt verwerkt|De gebruiker heeft de overdrachtsaanvraag geaccepteerd. Facturering voor abonnementen die de gebruiker heeft geselecteerd, wordt overgebracht naar uw account|
   |Voltooid| De facturering voor abonnementen die de gebruiker heeft geselecteerd, wordt overgebracht naar uw account|
   |Voltooid met fouten|De aanvraag is voltooid, maar de facturering voor sommige abonnementen die de gebruiker heeft geselecteerd, kan niet worden overgedragen|
   |Verlopen|De gebruiker heeft de aanvraag niet op tijd geaccepteerd en de aanvraag is verlopen|
   |Geannuleerd|Iemand met toegang tot de overdrachtsaanvraag heeft de aanvraag geannuleerd|
   |Geweigerd|De gebruiker heeft de overdrachtsaanvraag geweigerd|

6. Selecteer een overdrachtsaanvraag om details weer te geven. Op de pagina met overdrachtsdetails wordt de volgende informatie weergegeven: ![Schermopname van de lijst van overgedragen abonnementen](./media/mpa-request-ownership/mpa-transfer-completed.png)

   |Kolom  |Definitie|
   |---------|---------|
   |Id van overdrachtsaanvraag|De unieke id voor uw overdrachtsaanvraag. Als u een ondersteuningsaanvraag indient, deelt u de id met de ondersteuning van Azure om uw ondersteuningsaanvraag te versnellen|
   |Overdracht aangevraagd op|De datum waarop de overdrachtsaanvraag is verzonden|
   |Overdracht aangevraagd door|Het e-mailadres van de gebruiker die de overdrachtsaanvraag heeft verzonden|
   |Overdrachtsaanvraag verloopt op| De datum waarop de overdrachtsaanvraag verloopt|
   |E-mailadres van ontvanger|Het e-mailadres van de gebruiker aan wie u de aanvraag voor het overdragen van het eigendom van facturering hebt verzonden|
   |Overdrachtskoppeling verzonden naar ontvanger|De url die naar de gebruiker is verzonden om de overdrachtsaanvraag te controleren|

## <a name="supported-subscription-types"></a>Ondersteunde abonnementstypen

U kunt het eigendom van facturering aanvragen van de hieronder vermelde typen abonnementen.

- [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)\*
- [Microsoft Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/)

\* Een Dev/Test-abonnement moet eerst worden omgezet naar een EA Enterprise-aanbieding via een ondersteuningsticket. Een Enterprise Dev/Test-abonnement wordt gefactureerd met een tarief op basis van betalen per gebruik nadat het is overgezet. Kortingen die via de Enterprise Dev/Test-aanbieding worden aangeboden met de EA van de klant, zijn niet beschikbaar voor de CSP-partner.

## <a name="additional-information"></a>Aanvullende informatie

Het volgende gedeelte bevat aanvullende informatie over het overdragen van abonnementen.

### <a name="no-service-downtime"></a>Geen servicedowntime

Azure-services in het abonnement worden zonder onderbreking uitgevoerd. We maken de overgang alleen voor de factureringsrelatie voor de Azure-abonnementen die de gebruiker selecteert om over te dragen.

### <a name="disabled-subscriptions"></a>Uitgeschakelde abonnementen

Uitgeschakelde abonnementen kunnen niet worden overgedragen. Abonnementen moeten de status actief hebben om hun eigendom van facturering te kunnen overdragen.

### <a name="azure-resources-transfer"></a>Overdracht van Azure-bronnen

Alle resources van de abonnementen, zoals VM's, schijven en websites, worden overgebracht.

### <a name="azure-marketplace-products-transfer"></a>Overdracht van Azure Marketplace-producten

Azure Marketplace-producten die beschikbaar zijn voor abonnementen die worden beheerd door Cloud Solution Providers (CSP's), worden samen met hun respectieve abonnementen overgedragen. Abonnementen met Azure Marketplace-producten die niet zijn ingeschakeld voor CSP's, kunnen niet worden overgedragen.

### <a name="azure-reservations-transfer"></a>Overdracht van Azure-reserveringen

Azure-reserveringen worden niet automatisch verplaatst met abonnementen. U kunt de reservering behouden of [de reservering annuleren en opnieuw aanschaffen](https://docs.microsoft.com/azure/cost-management-billing/reservations/exchange-and-refund-azure-reservations) in CSP. 

### <a name="access-to-azure-services"></a>Toegang tot Azure-services

Toegang voor bestaande gebruikers, groepen of service-principals die is toegewezen met behulp van [Azure RBAC (op rollen gebaseerd toegangsbeheer)](../../role-based-access-control/overview.md) wordt niet beïnvloed tijdens de overgang. De partner krijgt geen nieuwe RBAC-toegang tot de abonnementen.  

De partners moeten samenwerken met de klant om toegang te krijgen tot abonnementen.  De partners moeten [Beheer namens - AOBO](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) of [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/concepts/cloud-solution-provider)-toegang krijgen om ondersteuningstickets te openen.

### <a name="azure-support-plan"></a>Azure-ondersteuningsplan

Ondersteuning voor Azure wordt niet overgedragen met de abonnementen. Als de gebruiker alle Azure-abonnementen overdraagt, vraagt u deze gebruiker om het ondersteuningsplan te annuleren. Na de overdracht is de CSP-partner verantwoordelijk voor de ondersteuning. De klant moet samenwerken met de CSP-partner voor elke ondersteuningsaanvraag.  

### <a name="charges-for-transferred-subscription"></a>Kosten voor overgedragen abonnement

De oorspronkelijke factureringseigenaar van de abonnementen is verantwoordelijk voor alle kosten die zijn gerapporteerd tot het moment waarop de overdracht is voltooid. U bent verantwoordelijk voor de kosten die worden gerapporteerd vanaf het tijdstip van de overdracht. Er kunnen kosten zijn die plaatsvonden vóór de overdracht, maar die daarna werden gerapporteerd. Deze kosten worden weergegeven op uw factuur.

### <a name="cancel-a-transfer-request"></a>Een overdrachtsaanvraag annuleren

U kunt de overdrachtsaanvraag annuleren totdat de aanvraag is goedgekeurd of geweigerd. Als u de overdrachtsaanvraag wilt annuleren, gaat u naar de [pagina met overdrachtsgegevens](#check-the-transfer-request-status) en selecteert u Annuleren onderaan de pagina.

### <a name="software-as-a-service-saas-transfer"></a>Overdracht van Software als een dienst (SaaS)

SaaS-producten worden niet overgedragen met de abonnementen. Vraag de gebruiker om [Contact op te nemen met de ondersteuning voor Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om eigendom van SaaS-producten over te dragen. Samen met het eigendom van facturering kan de gebruiker ook het eigendom van resources overdragen. Met eigendom van resources kunt u beheerbewerkingen uitvoeren, zoals het verwijderen en weergeven van de details van het product. De gebruiker moet een resource-eigenaar zijn van het SaaS-product om eigendom van de resource over te dragen.

### <a name="additional-approval-for-certain-customers"></a>Aanvullende goedkeuring voor bepaalde klanten

Voor bepaalde overdrachtsaanvragen kan een extra beoordelingsproces van Microsoft vereist zijn vanwege de aard van de huidige Enterprise Enrollment-structuur van de klant. De partner wordt op de hoogte gesteld van dergelijke vereisten wanneer zij proberen een uitnodiging te verzenden naar klanten. Partners dienen samen te werken met hun Partner Development Manager en het accountteam van de klant om dit beoordelingsproces te voltooien.

### <a name="azure-subscription-directory"></a>Map van Azure-abonnement
De map van de Azure-abonnementen die worden overgedragen, moet overeenkomen met de map van de klant die is geselecteerd tijdens het tot stand brengen van de CSP-relatie.

Als deze twee mappen niet overeenkomen, kunnen de abonnementen niet worden overgedragen. U moet een nieuwe CSP-resellerrelatie met de klant tot stand brengen door de map van de Azure-abonnementen te selecteren of de map van Azure-abonnementen zo te wijzigen dat deze overeenkomt met de map van de CSP-relatie van de klant. Zie [Een bestaand abonnement koppelen aan uw Azure AD Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory#to-associate-an-existing-subscription-to-your-azure-ad-directory).


## <a name="check-access-to-a-microsoft-partner-agreement"></a>Toegang tot een Microsoft Partner-overeenkomst controleren
[!INCLUDE [billing-check-mpa](../../../includes/billing-check-mpa.md)]

## <a name="need-help-contact-support"></a>Hebt u hulp nodig? Contact opnemen met ondersteuning

Als u hulp nodig hebt, kunt u [contact opnemen met ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om uw probleem snel op te lossen.

## <a name="next-steps"></a>Volgende stappen

- Het eigendom van facturering van de Azure-abonnementen wordt overgezet naar u. Houd de kosten voor deze abonnementen bij in [Azure Portal.](https://portal.azure.com)

- Werk samen met de klant om toegang te krijgen tot de overgedragen Azure-abonnementen. [Toegang tot Azure-resources beheren met behulp van RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).
