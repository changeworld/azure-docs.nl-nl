---
title: Zelfstudie om Azure Data Box | Microsoft Docs
description: Ontdek wat de implementatievereisten zijn en hoe u een Azure Data Box kunt bestellen
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 04/23/2019
ms.author: alkohli
ms.openlocfilehash: b0204673c0706403c8c5a7367be19e590d9cb134
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/14/2019
ms.locfileid: "65604084"
---
# <a name="tutorial-order-azure-data-box"></a>Zelfstudie: Azure Data Box bestellen

Azure Data Box is een hybride oplossing waarmee u uw on-premises gegevens snel, gemakkelijk en betrouwbaar in Azure kunt importeren. U zet uw gegevens op een opslagapparaat van 80 TB (bruikbare capaciteit) dat Microsoft naar u heeft verstuurd, en u stuurt het apparaat vervolgens terug. Deze gegevens worden vervolgens geüpload in Azure.

In deze zelfstudie wordt beschreven hoe u een Azure Data Box bestelt. In deze zelfstudie komen deze onderwerpen aan bod:

> [!div class="checklist"]
> * Vereisten om Data Box te implementeren
> * Een Data Box bestellen
> * De bestelling volgen
> * De bestelling annuleren

## <a name="prerequisites"></a>Vereisten

Zorg dat u aan de volgende configuratievereisten voor de Data Box-service en het apparaat voldoet voordat u het apparaat implementeert.

### <a name="for-service"></a>Voor de service

Zorg voordat u begint voor het volgende:
- U hebt een Microsoft Azure Storage-account met toegangsreferenties.
- Zorg ervoor dat het abonnement dat u voor de Data Box-service gebruikt, een van de volgende typen is:
    - Microsoft Enterprise Agreement (EA). Meer informatie over [EA-abonnementen](https://azure.microsoft.com/pricing/enterprise-agreement/).
    - Cloud Solution Provider (CSP). Meer informatie over het [Azure CSP-programma](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview).
    - Microsoft Azure Sponsorship. Meer informatie over het [Azure Sponsorship-programma](https://azure.microsoft.com/offers/ms-azr-0036p/).

- U hebt eigenaars- of inzenderstoegang tot het abonnement nodig om een Data Box-order te kunnen maken.

### <a name="for-device"></a>Voor het apparaat

Zorg voordat u begint voor het volgende:
- Er is een hostcomputer verbonden met het datacenternetwerk. Data Box kopieert de gegevens vanaf deze computer. Uw hostcomputer moet een ondersteund besturingssysteem hebben, zoals beschreven in [Systeemvereisten voor Azure Data Box](data-box-system-requirements.md).
- Uw datacenter moet een netwerk met hoge snelheid hebben. Het wordt aangeraden dat u ten minste één 10 GbE-verbinding hebt. Als er geen 10 GbE-verbinding beschikbaar is, kan een 1 GbE-gegevenskoppeling worden gebruikt. Dit heeft echter wel invloed op de kopieersnelheid.


## <a name="order-data-box"></a>Data Box bestellen

Voer de volgende stappen uit in de Azure-portal om een apparaat te bestellen.

1. Gebruik uw Microsoft Azure-referenties om u aan te melden op deze URL: [https://portal.azure.com](https://portal.azure.com).
2. Klik op **+ Een resource maken** en zoek naar *Azure Data Box*. Klik op **Azure Data Box**.
    
   [![Zoek naar Azure Data Box 1](media/data-box-deploy-ordered/search-azure-data-box1.png)](media/data-box-deploy-ordered/search-azure-data-box1.png#lightbox)

3. Klik op **Create**.

4. Controleer of de Data Box-service beschikbaar is in uw regio. Voer de volgende gegevens in of selecteer deze en klik op **Toepassen**. 

    |Instelling  |Value  |
    |---------|---------|
    |Abonnement     | Selecteer een EA-, CSP- of Azure Sponsorship-abonnement voor de Data Box-service. <br> Het abonnement is gekoppeld aan uw factureringsrekening.       |
    |Type overdracht     | Selecteer **Importeren in Azure**.        |
    |Bronland     |   Selecteer het land/de regio waarin uw gegevens zich momenteel bevindt.         |
    |Doel-Azure-regio     |     Selecteer de Azure-regio waarnaar u uw gegevens wilt overdragen.        |

5. Selecteer **Data Box**. De maximale aan bruikbare capaciteit voor één order is 80 TB. U kunt meerdere bestellingen doen voor grotere gegevensgrootten.

      [![Selecteer Data Box optie 1](media/data-box-deploy-ordered/select-data-box-option1.png)](media/data-box-deploy-ordered/select-data-box-option1.png#lightbox)

6. Voer in **Bestellen** de **Orderdetails** in. Voer de volgende gegevens in of selecteer deze en klik op **Volgende**.
    
    |Instelling  |Waarde  |
    |---------|---------|
    |Name     |  Geef een beschrijvende naam op om de bestelling te volgen. <br> De naam kan tussen 3 en 24 tekens bevatten (letters, cijfers en afbreekstreepjes). <br> De naam moet beginnen en eindigen met een letter of cijfer.      |
    |Resourcegroep     |   Gebruik een bestaande of maak een nieuwe. <br> Een resourcegroep is een logische container voor resources die samen kunnen worden beheerd of geïmplementeerd.         |
    |Doel-Azure-regio     | Selecteer een regio voor uw opslagaccount. <br> Ga naar [Beschikbaarheid in de regio](data-box-overview.md#region-availability) voor meer informatie.        |
    |Opslaglocatie     | Kies in de storage-account of beheerde schijven of beide. <br> Selecteer een of meer opslagaccounts in de gefilterde lijst van een bestaand opslagaccount, gebaseerd op de opgegeven Azure-regio. Data Box kan worden gekoppeld aan maximaal 10 opslagaccounts. <br> U kunt ook maken een nieuwe **voor algemeen gebruik v1**, **voor algemeen gebruik v2**, of **Blob storage-account**. <br>Storage-accounts met virtuele netwerken worden ondersteund. Schakelt de vertrouwde services binnen de firewall-instellingen van storage account network zodat Data Box-service om te werken met beveiligde storage-accounts. Zie voor meer informatie over het [Azure Data Box toevoegen als een vertrouwde service](../storage/common/storage-network-security.md#exceptions).|

    Als u de storage-account als de opslaglocatie, ziet u de volgende schermafbeelding:

    ![Data Box-order voor storage-account](media/data-box-deploy-ordered/order-storage-account.png)

    Als u Data Box voor het maken van beheerde schijven van de on-premises VHD's, moet u ook de volgende informatie:

    |Instelling  |Value  |
    |---------|---------|
    |Resourcegroepen     | Maak nieuwe resourcegroepen als u van plan bent voor het maken van beheerde schijven vanaf on-premises VHD's. U kunt een bestaande resourcegroep gebruiken alleen als de resourcegroep is eerder hebt gemaakt bij het maken van een Data Box-order voor beheerde schijf door Data Box-service. <br> Geef meerdere resourcegroepen, gescheiden door puntkomma's. Maximaal 10 resourcegroepen worden ondersteund.|

    ![Data Box-order voor beheerde schijf](media/data-box-deploy-ordered/order-managed-disks.png)

    Het opslagaccount dat is opgegeven voor beheerde schijven wordt als een tijdelijke opslagaccount dat gebruikt. De uploads van de Data Box-service is de VHD's als pagina-blobs naar het tijdelijke opslagaccount voordat u deze converteren naar beheerde schijven en verplaatsen naar de resourcegroepen. Zie voor meer informatie, [controleren of gegevens uploaden naar Azure](data-box-deploy-picked-up.md#verify-data-upload-to-azure).

7. Bij **Verzendadres** geeft u uw voor- en achternaam, de naam en het postadres van het bedrijf en een geldig telefoonnummer op. Klik op **Adres valideren**. De service controleert of de service beschikbaar is voor de regio van het verzendadres. Als de service beschikbaar is voor het opgegeven verzendadres, ontvangt u daarover een melding. Klik op **volgende**.

8. In de **Meldingsdetails** geeft u een e-mailadres op. De service stuurt e-mailmeldingen naar het opgegeven e-mailadres over updates van de bestelstatus.

    We raden u aan een e-mailadres van een groep te gebruiken, zodat u meldingen blijft ontvangen als een beheerder de groep verlaat.

9. Bekijk de gegevens met betrekking tot de bestelling, het contact, de meldingen en de privacyvoorwaarden in **Samenvatting**. Vink het selectievakje aan waarmee u akkoord gaat met de privacyvoorwaarden.

10. Klik op **Bestellen**. Het duurt een paar minuten voordat de bestelling is gemaakt.


## <a name="track-the-order"></a>De bestelling volgen

Nadat u uw bestelling hebt geplaatst, kunt u de status van de bestelling volgen via de Azure-portal. Ga naar uw Data Box-bestelling en vervolgens naar **Overzicht** om de status te bekijken. In de portal ziet u dat de bestelling de status **Besteld** heeft.

Als het apparaat niet beschikbaar is, ontvangt u een melding. Als het apparaat wel beschikbaar is, identificeert Microsoft het apparaat dat moet worden verzonden en bereidt Microsoft de verzending voor. Tijdens de voorbereiding van het apparaat vinden de volgende acties plaats:

- Voor elk opslagaccount dat aan het apparaat is gekoppeld, worden SMB-shares gemaakt.
- Voor elke share worden toegangsreferenties zoals een gebruikersnaam en wachtwoord gegenereerd.
- Er wordt ook een apparaatwachtwoord gegenereerd om het apparaat mee te ontgrendelen.
- De Data Box wordt vergrendeld om onbevoegde toegang tot het apparaat te voorkomen.

Wanneer de apparaatvoorbereiding is voltooid, wordt de bestelling in de portal weergegeven met de status **Verwerkt**.

![Data Box-bestelling verwerkt](media/data-box-overview/data-box-order-status-processed.png)

Microsoft bereidt de verzending vervolgens voor en verzendt het apparaat met een regionale vervoerder. U ontvangt uw volgnummer zodra het apparaat is verzonden. In de portal wordt bestelling weergegeven met de status **Verzonden**.

![Data Box-bestelling verzonden](media/data-box-overview/data-box-order-status-dispatched.png)

## <a name="cancel-the-order"></a>De bestelling annuleren

Als u deze bestelling wilt annuleren, gaat u in de Azure-portal naar **Overzicht** en klikt u in de opdrachtbalk op **Annuleren**.

Nadat u een bestelling hebt geplaatst, kunt u deze op elk moment annuleren voordat de status op Verwerkt is ingesteld.
 
Als u een geannuleerde bestelling wilt verwijderen, gaat u naar **Overzicht** en klikt u in de opdrachtbalk op **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie bent u meer te weten gekomen over verschillende onderwerpen met betrekking tot Azure Data Box, zoals:

> [!div class="checklist"]
> * Vereisten om Data Box te implementeren
> * Data Box bestellen
> * De bestelling volgen
> * De bestelling annuleren

Ga naar de volgende zelfstudie om te lezen hoe u uw Data Box instelt.

> [!div class="nextstepaction"]
> [Uw Azure Data Box instellen](./data-box-deploy-set-up.md)


