---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 852a3b00e8513f9ce68c5aae54c974505d0c9af6
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67176533"
---
1. Start op uw Mac **toegang tot sleutelhanger**. Op de linkernavigatiebalk onder **categorie**Open **mijn certificaten**. Zoek het SSL-certificaat dat u hebt gedownload in de vorige sectie en vervolgens de inhoud ervan te vermelden. Selecteer alleen het certificaat (niet de persoonlijke sleutel selecteren). Vervolgens [exporteren](https://support.apple.com/kb/PH20122?locale=en_US).
2. In de [Azure-portal](https://portal.azure.com/), selecteer **door alles bladeren** > **App Services**. Vervolgens Selecteer uw Mobile Apps-back-end. 
3. Onder **instellingen**, selecteer **App Service-Push**. Selecteer vervolgens de naam van uw notification hub. 
4. Ga naar **Apple Push Notification Services** >  **-certificaat uploaden**. Upload het .p12-bestand selecteren van de juiste **modus** (afhankelijk van of uw SSL-clientcertificaat uit eerder is productie- of sandbox). Wijzigingen opslaan.

Uw service is nu geconfigureerd om te werken met pushmeldingen in iOS.

[1]: ./media/app-service-mobile-apns-configure-push/mobile-push-notification-hub.png
