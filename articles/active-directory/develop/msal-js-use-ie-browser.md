---
title: Problemen met Internet Explorer (MSAL. js) | Azure
titleSuffix: Microsoft identity platform
description: Gebruik de micro soft-verificatie bibliotheek voor Java script (MSAL. js) met de Internet Explorer-browser.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: c4f3c4153e1404a5576427be7ef218f5a669387e
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76695854"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-msaljs"></a>Bekende problemen met Internet Explorer en micro soft Edge-browsers (MSAL. js)

Micro soft-verificatie bibliotheek voor Java script (MSAL. js) wordt gegenereerd voor [Java script-ES5](https://fr.wikipedia.org/wiki/ECMAScript#ECMAScript_Edition_5_.28ES5.29) , zodat deze kan worden uitgevoerd in Internet Explorer. Er zijn echter enkele dingen die u moet weten.

## <a name="run-an-app-in-internet-explorer"></a>Een app uitvoeren in Internet Explorer
Als u van plan bent MSAL. js te gebruiken in toepassingen die kunnen worden uitgevoerd in Internet Explorer, moet u een verwijzing naar een poly-toevoegen voordat u verwijst naar het script MSAL. js.

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js" class="pre"></script>
```

Dit komt omdat Internet Explorer geen systeem eigen ondersteuning biedt voor Java script-beloftes.

## <a name="debugging-an-application-running-in-internet-explorer"></a>Fout opsporing in een toepassing die wordt uitgevoerd in Internet Explorer

### <a name="running-in-production"></a>Uitvoeren in productie
Het implementeren van uw toepassing naar productie (bijvoorbeeld in azure web apps) werkt normaal, vooropgesteld dat de eind gebruiker pop-ups heeft geaccepteerd. We hebben het getest met Internet Explorer 11.

### <a name="running-locally"></a>Lokaal uitvoeren
Als u lokaal wilt uitvoeren en fouten wilt opsporen in uw toepassing die in Internet Explorer wordt uitgevoerd, moet u rekening houden met de volgende overwegingen (Stel dat u uw toepassing wilt uitvoeren als *http://localhost:1234* ):

- Internet Explorer heeft een beveiligings mechanisme met de naam ' beveiligde modus ', waarmee wordt voor komen dat MSAL. js goed werkt. Nadat u zich hebt aangemeld, kunt u onder de symptomen de pagina omleiden naar http://localhost:1234/null.

- Als u uw toepassing lokaal wilt uitvoeren en fouten wilt opsporen, moet u deze beveiligde modus uitschakelen. Voor dit:

    1. Klik op **Hulpprogram ma's** voor Internet Explorer (het tandwiel pictogram).
    1. Selecteer **Internet opties** en klik vervolgens op het tabblad **beveiliging** .
    1. Klik op de zone **Internet** en Schakel **beveiligde modus inschakelen uit (hiervoor moet Internet Explorer opnieuw worden gestart)** . Internet Explorer waarschuwt dat uw computer niet meer is beveiligd. Klik op **OK**.
    1. Start Internet Explorer opnieuw.
    1. Uw toepassing uitvoeren en fouten opsporen.

Wanneer u klaar bent, herstelt u de beveiligings instellingen van Internet Explorer.  Selecteer **instellingen** -> **Internet opties** -> **beveiliging** -> **alle zones op standaard niveau instellen**.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over [bekende problemen bij het gebruik van MSAL. js in Internet Explorer](msal-js-use-ie-browser.md).