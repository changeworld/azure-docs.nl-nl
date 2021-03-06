---
title: Een Video Indexer account beheren
titleSuffix: Azure Media Services
description: In dit artikel wordt beschreven hoe u een Video Indexer account beheert dat is verbonden met Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 12/16/2019
ms.author: juliako
ms.openlocfilehash: f3825f6c9186c5e04807dd3890a14fcc6d370989
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454664"
---
# <a name="manage-a-video-indexer-account-connected-to-azure"></a>Een Video Indexer account beheren dat is verbonden met Azure

In dit artikel wordt beschreven hoe u een Video Indexer-account kunt beheren dat is verbonden met uw Azure-abonnement en een Azure Media Services-account.

> [!NOTE]
> U moet de eigenaar van het Video Indexer account zijn voor het uitvoeren van aanpassingen van de account configuratie die in dit onderwerp worden beschreven.

## <a name="prerequisites"></a>Vereisten

Verbind uw Video Indexer-account met Azure, zoals beschreven in [verbonden met Azure](connect-to-azure.md). 

Zorg ervoor dat u voldoet aan de [vereisten](connect-to-azure.md#prerequisites) en Lees [overwegingen](connect-to-azure.md#considerations) in het artikel.

## <a name="examine-account-settings"></a>Account instellingen controleren

In deze sectie worden de instellingen van uw Video Indexer-account onderzocht.

Instellingen weer geven:

1. Klik in de rechter bovenhoek op het pictogram van de gebruiker en selecteer **instellingen**.

    ![Instellingen](./media/manage-account-connected-to-azure/select-settings.png)

2. Selecteer op de pagina **instellingen** het tabblad **account** .

Als uw video indexer-account is verbonden met Azure, ziet u het volgende:

* De naam van het onderliggende Azure Media Services-account.
* Het aantal uitgevoerde indexerings taken en de wachtrij.
* Het aantal en het type toegewezen gereserveerde eenheden.

Als uw account aanpassingen vereist, worden relevante fouten en waarschuwingen over uw account configuratie weer gegeven op de pagina **instellingen** . De berichten bevatten koppelingen naar exacte locaties in Azure Portal waar u wijzigingen moet aanbrengen. Zie de sectie met [fouten en waarschuwingen](#errors-and-warnings) voor meer informatie.

## <a name="repair-the-connection-to-azure"></a>De verbinding met Azure herstellen

In het dialoog venster **verbinding bijwerken met Azure Media Services** van uw [video indexer](https://www.videoindexer.ai/) pagina, wordt u gevraagd waarden op te geven voor de volgende instellingen: 

|Instelling|Beschrijving|
|---|---|
|Azure-abonnements-ID|De abonnements-ID kan worden opgehaald uit de Azure Portal. Klik op **alle services** in het linkerdeel venster en zoek naar ' Abonnementen '. Selecteer **abonnementen** en kies de gewenste id in de lijst met uw abonnementen.|
|Naam van de resource groep Azure Media Services|De naam voor de resource groep waarin u het Media Services-account hebt gemaakt.|
|Toepassings-id|De Azure AD-toepassings-ID (met machtigingen voor het opgegeven Media Services-account) dat u voor dit Video Indexer account hebt gemaakt. <br/><br/>Als u de App-ID wilt ophalen, gaat u naar Azure Portal. Kies onder het Media Services account uw account en ga naar **API-toegang**. Klik op **verbinding maken met Media Services-API met service-principal** -> **Azure AD-App**. Kopieer de relevante para meters.|
|Toepassingssleutel|De Azure AD-toepassings sleutel die is gekoppeld aan uw Media Services account dat u hierboven hebt opgegeven. <br/><br/>Als u de app-sleutel wilt ophalen, gaat u naar Azure Portal. Kies onder het Media Services account uw account en ga naar **API-toegang**. Klik op **verbinding maken met Media Services-API met service-principal** -> toepassings -> **certificaten & geheimen**te **beheren** . Kopieer de relevante para meters.|

## <a name="auto-scale-reserved-units"></a>Gereserveerde eenheden automatisch schalen

Op de pagina **instellingen** kunt u automatisch schalen instellen voor gereserveerde media-eenheden (ru). Als de optie is **ingeschakeld**, kunt u het maximum aantal aan Rus toewijzen en ervoor zorgen dat de video indexer automatisch Rus wordt gestopt/gestart. Met deze optie betaalt u geen extra geld voor niet-actieve tijd, maar u hoeft niet te wachten op het volt ooien van index taken wanneer de indexerings belasting hoog is.

Automatische schaal aanpassing niet lager dan 1 RU of hoger dan de standaard limiet van het Media Services-account. Als u de limiet wilt verhogen, maakt u een service aanvraag. Zie [quota's en beperkingen](../../media-services/previous/media-services-quotas-and-limitations.md)voor meer informatie over quota en beperkingen en het openen van een ondersteunings ticket.

![Aanmelden](./media/manage-account-connected-to-azure/autoscale-reserved-units.png)

## <a name="errors-and-warnings"></a>Fouten en waarschuwingen

Als uw account aanpassingen vereist, ziet u relevante fouten en waarschuwingen over uw account configuratie op de pagina **instellingen** . De berichten bevatten koppelingen naar exacte locaties in Azure Portal waar u wijzigingen moet aanbrengen. In deze sectie vindt u meer informatie over de fout-en waarschuwings berichten.

* Event Grid

    U moet de EventGrid-resource provider registreren met behulp van de Azure Portal. Ga in het [Azure Portal](https://portal.azure.com/)naar **abonnementen** > [subscription] > **ResourceProviders** > **micro soft. EventGrid**. Als de status niet is **geregistreerd** , klikt u op **registreren**. Het duurt enkele minuten om u te registreren. 

* Streaming-eind punt

    Zorg ervoor dat de onderliggende Media Services account het standaard **streaming-eind punt** met de status gestart heeft. Als dat niet het geval is, kunt u geen Video's bekijken uit dit Media Services-account of Video Indexer.

* Gereserveerde media-eenheden 

    U moet gereserveerde media-eenheden toewijzen aan uw media service bron om Video's te kunnen indexeren. Voor optimale index prestaties is het raadzaam om ten minste 10 S3-gereserveerde eenheden toe te wijzen. Zie de sectie Veelgestelde vragen op de pagina met [prijzen voor Media Services](https://azure.microsoft.com/pricing/details/media-services/) voor prijs informatie.   

## <a name="next-steps"></a>Volgende stappen

U kunt programmatisch communiceren met uw proef account en/of met uw Video Indexer-accounts die zijn verbonden met Azure door de instructies te volgen in: [Api's gebruiken](video-indexer-use-apis.md).

U moet dezelfde Azure AD-gebruiker gebruiken die u hebt gebruikt om verbinding te maken met Azure.
