---
title: Een connection string configureren
titleSuffix: Azure Storage
description: Een connection string configureren voor een Azure-opslag account. Een connection string bevat de informatie die nodig is om tijdens runtime toegang te verlenen tot een opslag account vanuit uw toepassing met behulp van gedeelde sleutel verificatie.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 12/20/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f617beec8a53570ede7755040cfbb92a7d1712b7
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79268351"
---
# <a name="configure-azure-storage-connection-strings"></a>Azure Storage-verbindingsreeksen configureren

Een connection string bevat de autorisatie-informatie die is vereist voor uw toepassing om toegang te krijgen tot gegevens in een Azure Storage account tijdens runtime met behulp van gedeelde sleutel verificatie. U kunt verbindings reeksen configureren voor het volgende:

* Maak verbinding met de Azure-opslag emulator.
* Toegang tot een opslag account in Azure.
* Toegang krijgen tot opgegeven resources in azure via een Shared Access Signature (SAS).

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-and-copy-a-connection-string"></a>Een connection string weer geven en kopiëren

[!INCLUDE [storage-view-keys-include](../../../includes/storage-view-keys-include.md)]

## <a name="store-a-connection-string"></a>Een connection string opslaan

Uw toepassing moet tijdens runtime toegang hebben tot de connection string om aanvragen voor Azure Storage te autoriseren. U hebt verschillende mogelijkheden om uw connection string op te slaan:

* U kunt uw connection string opslaan in een omgevings variabele.
* Een toepassing die wordt uitgevoerd op het bureau blad of op een apparaat kan de connection string opslaan in een **app. config** -of **Web. config** -bestand. Voeg de connection string toe aan de sectie **AppSettings** in deze bestanden.
* Een toepassing die wordt uitgevoerd in een Azure-Cloud service kan de connection string opslaan in het [Azure service configuration schema-bestand (. cscfg)](https://msdn.microsoft.com/library/ee758710.aspx). Voeg de connection string toe aan de sectie **ConfigurationSettings** van het service configuratie bestand.

Het opslaan van uw connection string in een configuratie bestand maakt het eenvoudig om de connection string bij te werken om te scha kelen tussen de opslag emulator en een Azure Storage-account in de Cloud. U hoeft alleen de connection string te bewerken om naar uw doel omgeving te verwijzen.

U kunt de [Microsoft Azure Configuration Manager](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) gebruiken om toegang te krijgen tot uw Connection String in runtime, ongeacht waar de toepassing wordt uitgevoerd.

## <a name="configure-a-connection-string-for-the-storage-emulator"></a>Een connection string configureren voor de opslag emulator

[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Zie [de Azure-opslag emulator gebruiken voor ontwikkeling en testen](storage-use-emulator.md)voor meer informatie over de opslag emulator.

## <a name="configure-a-connection-string-for-an-azure-storage-account"></a>Een connection string configureren voor een Azure-opslag account

Als u een connection string voor uw Azure-opslag account wilt maken, gebruikt u de volgende indeling. Geef aan of u verbinding wilt maken met het opslag account met behulp van HTTPS (aanbevolen) of HTTP, vervang `myAccountName` door de naam van uw opslag account en vervang `myAccountKey` door de toegangs sleutel van uw account:

`DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey`

Uw connection string kan er bijvoorbeeld ongeveer als volgt uitzien:

`DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>`

Hoewel Azure Storage zowel HTTP als HTTPS in een connection string ondersteunt, *wordt https ten zeerste aanbevolen*.

> [!TIP]
> U kunt de verbindings reeksen van uw opslag account vinden in de [Azure Portal](https://portal.azure.com). Navigeer naar **instellingen** > **toegangs sleutels** in de menu Blade van uw opslag account om verbindings reeksen voor de primaire en secundaire toegangs sleutel weer te geven.
>

## <a name="create-a-connection-string-using-a-shared-access-signature"></a>Een connection string maken met behulp van een hand tekening voor gedeelde toegang

[!INCLUDE [storage-use-sas-in-connection-string-include](../../../includes/storage-use-sas-in-connection-string-include.md)]

## <a name="create-a-connection-string-for-an-explicit-storage-endpoint"></a>Een connection string maken voor een expliciet opslag eindpunt

U kunt in uw connection string expliciete service-eind punten opgeven in plaats van de standaard eindpunten te gebruiken. Als u een connection string wilt maken waarmee een expliciet eind punt wordt opgegeven, geeft u het volledige service-eind punt voor elke service op, met inbegrip van de protocol specificatie (HTTPS (aanbevolen) of HTTP), in de volgende indeling:

```
DefaultEndpointsProtocol=[http|https];
BlobEndpoint=myBlobEndpoint;
FileEndpoint=myFileEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
AccountName=myAccountName;
AccountKey=myAccountKey
```

Een scenario waarin u mogelijk een expliciet eind punt wilt opgeven, is wanneer u uw Blob Storage-eind punt hebt toegewezen aan een [aangepast domein](../blobs/storage-custom-domain-name.md). In dat geval kunt u uw aangepaste eind punt voor Blob Storage in uw connection string opgeven. U kunt desgewenst de standaard eindpunten voor de andere services opgeven als uw toepassing deze gebruikt.

Hier volgt een voor beeld van een connection string waarmee een expliciet eind punt voor de Blob service wordt opgegeven:

```
# Blob endpoint only
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
AccountName=storagesample;
AccountKey=<account-key>
```

In dit voor beeld worden expliciete eind punten voor alle services opgegeven, met inbegrip van een aangepast domein voor de Blob service:

```
# All service endpoints
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
FileEndpoint=https://myaccount.file.core.windows.net;
QueueEndpoint=https://myaccount.queue.core.windows.net;
TableEndpoint=https://myaccount.table.core.windows.net;
AccountName=storagesample;
AccountKey=<account-key>
```

De eind punt waarden in een connection string worden gebruikt voor het maken van de aanvraag-Uri's voor de opslag Services en het bepalen van de vorm van Uri's die naar uw code worden geretourneerd.

Als u een opslag eindpunt hebt toegewezen aan een aangepast domein en dat eind punt weglaat van een connection string, kunt u die connection string niet gebruiken voor toegang tot gegevens in die service vanuit uw code.

> [!IMPORTANT]
> De waarden van de service-eind punten in de verbindings reeksen moeten een juist opgemaakte Uri's zijn, waaronder `https://` (aanbevolen) of `http://`. Omdat Azure Storage geen HTTPS ondersteunt voor aangepaste domeinen, *moet* u `http://` opgeven voor elke eind punt-URI die verwijst naar een aangepast domein.
>

### <a name="create-a-connection-string-with-an-endpoint-suffix"></a>Een connection string met een eind punt achtervoegsel maken

Als u een connection string wilt maken voor een opslag service in regio's of exemplaren met verschillende eindpunt achtervoegsels, zoals voor Azure-China 21Vianet of Azure Government, gebruikt u de volgende connection string-indeling. Geef aan of u verbinding wilt maken met het opslag account met behulp van HTTPS (aanbevolen) of HTTP, vervang `myAccountName` door de naam van uw opslag account, vervang `myAccountKey` door de toegangs sleutel van uw account en vervang `mySuffix` door het URI-achtervoegsel:

```
DefaultEndpointsProtocol=[http|https];
AccountName=myAccountName;
AccountKey=myAccountKey;
EndpointSuffix=mySuffix;
```

Hier volgt een voor beeld connection string voor Storage services in azure China 21Vianet:

```
DefaultEndpointsProtocol=https;
AccountName=storagesample;
AccountKey=<account-key>;
EndpointSuffix=core.chinacloudapi.cn;
```

## <a name="parsing-a-connection-string"></a>Een connection string parseren

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="next-steps"></a>Volgende stappen

* [De Azure-opslag emulator gebruiken voor ontwikkelen en testen](storage-use-emulator.md)
* [Azure Storage Explorers](storage-explorers.md)
* [Shared Access signatures (SAS) gebruiken](storage-sas-overview.md)
