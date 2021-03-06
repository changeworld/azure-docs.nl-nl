---
title: Containers configureren-Computer Vision
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt beschreven hoe u de vereiste en optionele instellingen voor Tekst herkennen containers in Computer Vision kunt configureren.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: ddbee3695c2a7ef7cb63c48cccacbd2d53a8c1a9
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718985"
---
# <a name="configure-computer-vision-docker-containers"></a>Computer Vision docker-containers configureren

U configureert de runtime-omgeving van de Computer Vision-container met behulp van de `docker run` opdracht argumenten. Deze container heeft verschillende vereiste instellingen, samen met enkele optionele instellingen. Er zijn verschillende [voor beelden](#example-docker-run-commands) van de opdracht beschikbaar. De container-specifieke instellingen zijn de facturerings instellingen. 

## <a name="configuration-settings"></a>Configuratie-instellingen

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> De instellingen [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting)en [`Eula`](#eula-setting) worden samen gebruikt en u moet geldige waarden opgeven voor alle drie deze. anders kan de container niet worden gestart. Zie [facturering](computer-vision-how-to-install-containers.md)voor meer informatie over het gebruik van deze configuratie-instellingen voor het instantiëren van een container.

## <a name="apikey-configuration-setting"></a>Configuratie-instelling ApiKey

Met de instelling `ApiKey` geeft u de resource sleutel Azure `Cognitive Services` op die wordt gebruikt om de facturerings gegevens voor de container bij te houden. U moet een waarde opgeven voor de ApiKey en de waarde moet een geldige sleutel zijn voor de _Cognitive Services_ bron die is opgegeven voor de configuratie-instelling [`Billing`](#billing-configuration-setting) .

Deze instelling bevindt zich op de volgende locatie:

* Azure Portal: **Cognitive Services** Resource Management onder **sleutels**

## <a name="applicationinsights-setting"></a>ApplicationInsights-instelling

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Instelling facturerings configuratie

Met de instelling `Billing` geeft u de eindpunt-URI op van de _Cognitive Services_ resource op Azure die wordt gebruikt om de facturerings gegevens voor de container te meten. U moet een waarde opgeven voor deze configuratie-instelling en de waarde moet een geldige eindpunt-URI zijn voor een _Cognitive Services_ resource in Azure. De container rapporteert het gebruik ongeveer elke 10 tot 15 minuten.

Deze instelling bevindt zich op de volgende locatie:

* Azure Portal: overzicht van **Cognitive Services** , gelabelde `Endpoint`

Vergeet niet om `vision/v1.0` route ring toe te voegen aan de URI van het eind punt, zoals wordt weer gegeven in de volgende tabel. 

|Vereist| Naam | Gegevenstype | Beschrijving |
|--|------|-----------|-------------|
|Ja| `Billing` | Tekenreeks | URL van het facturerings eindpunt<br><br>Voorbeeld:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0` |

## <a name="eula-setting"></a>Gebruiksrecht overeenkomst instellen

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Gefluente instellingen

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Instellingen voor HTTP-proxy referenties

[!INCLUDE [Container shared configuration HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Instellingen voor logboek registratie
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Koppelings instellingen

Gebruik bindings koppelingen om gegevens van en naar de container te lezen en te schrijven. U kunt een invoer koppeling of uitvoer koppeling opgeven door de optie `--mount` op te geven in de opdracht [docker run](https://docs.docker.com/engine/reference/commandline/run/) .

De Computer Vision-containers gebruiken geen invoer-of uitvoer koppelingen om training of service gegevens op te slaan. 

De exacte syntaxis van de locatie voor het koppelen van de host varieert, afhankelijk van het besturings systeem van de host. Daarnaast is de koppel locatie van de [hostcomputer](computer-vision-how-to-install-containers.md#the-host-computer)mogelijk niet toegankelijk als gevolg van een conflict tussen de machtigingen die worden gebruikt door het docker-service account en de machtigingen voor het koppelen van de host-locatie. 

|Optioneel| Naam | Gegevenstype | Beschrijving |
|-------|------|-----------|-------------|
|Niet toegestaan| `Input` | Tekenreeks | Computer Vision containers gebruiken deze niet.|
|Optioneel| `Output` | Tekenreeks | Het doel van de uitvoer koppeling. De standaard waarde is `/output`. Dit is de locatie van de logboeken. Dit omvat container Logboeken. <br><br>Voorbeeld:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Voor beeld van docker-opdrachten uitvoeren

De volgende voor beelden gebruiken de configuratie-instellingen om te laten zien hoe u `docker run`-opdrachten schrijft en gebruikt.  Als de container eenmaal wordt uitgevoerd, blijft deze actief totdat u deze [stopt](computer-vision-how-to-install-containers.md#stop-the-container) .

* **Regel voortzettings teken**: de docker-opdrachten in de volgende secties gebruiken de back slash, `\`, als een regel voortzettings teken. Vervang of verwijder dit op basis van de vereisten van uw host-besturings systeem. 
* **Argument volgorde**: Wijzig de volg orde van de argumenten niet, tenzij u bekend bent met docker-containers.

Vervang {_argument_name_} door uw eigen waarden:

| Tijdelijke aanduiding | Waarde | Notatie of voor beeld |
|-------------|-------|---|
| **{API_KEY}** | De eindpunt sleutel van de `Computer Vision` resource op de pagina Azure `Computer Vision`-sleutels. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | De waarde van het facturerings eindpunt is beschikbaar op de pagina overzicht van Azure `Computer Vision`.| Zie [vereiste para meters](computer-vision-how-to-install-containers.md#gathering-required-parameters) voor expliciete voor beelden verzamelen. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> De opties `Eula`, `Billing`en `ApiKey` moeten worden opgegeven om de container uit te voeren. anders wordt de container niet gestart.  Zie [facturering](computer-vision-how-to-install-containers.md#billing)voor meer informatie.
> De ApiKey-waarde is de **sleutel** van de pagina Azure `Cognitive Services` resource sleutels.

## <a name="container-docker-examples"></a>Voor beelden van container docker

De volgende docker-voor beelden zijn voor de Lees-container.

### <a name="basic-example"></a>Basis voorbeeld

  ```docker
  docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
  containerpreview.azurecr.io/microsoft/cognitive-services-read \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>Voor beeld van logboek registratie 

  ```docker
  docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
  containerpreview.azurecr.io/microsoft/cognitive-services-read \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Volgende stappen

* Lees [hoe u containers installeert en uitvoert](computer-vision-how-to-install-containers.md).
