---
title: Virtuele Windows-bureau blad-taak verdeling configureren-Azure
description: De taakverdelings methode configureren voor een virtuele Windows-desktop omgeving.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5d8670994791e360f5e3b30b90b4bea5d55464b5
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128305"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>De taakverdelingsmethode voor Windows Virtual Desktop configureren

Als u de taakverdelings methode voor een hostgroep configureert, kunt u de virtuele Windows-bureaublad omgeving aanpassen aan uw behoeften.

>[!NOTE]
> Dit is niet van toepassing op een permanente bureau blad-hostgroep, omdat gebruikers altijd een 1:1-koppeling hebben met een sessie-host binnen de hostgroep.

## <a name="configure-breadth-first-load-balancing"></a>Gelijkmatige taak verdeling configureren

Breedte-eerste taak verdeling is de standaard configuratie voor nieuwe niet-permanente hostgroepen. Breed-First Load Balancing distribueert nieuwe gebruikers sessies over alle beschik bare sessie-hosts in de hostgroep. Bij het configureren van gelijkmatige taak verdeling kunt u een maximum aantal sessies per sessiehost instellen in de hostgroep.

[Down load en Importeer eerst de Windows Virtual Desktop Power shell-module](/powershell/windows-virtual-desktop/overview/) voor gebruik in uw Power shell-sessie als u dat nog niet hebt gedaan. Daarna voert u de volgende cmdlet uit om u aan te melden bij uw account:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Voer de volgende Power shell-cmdlet uit om een hostgroep zodanig te configureren dat de taak verdeling breed is zonder de maximale sessie limiet aan te passen:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer
```

Voer de volgende Power shell-cmdlet uit om een hostgroep zodanig te configureren dat de taak verdeling breed is en een nieuwe maximale sessie limiet te gebruiken:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>Diepte configureren-eerste taak verdeling

Diepte: voor de taak verdeling worden nieuwe gebruikers sessies gedistribueerd naar een beschik bare sessiehost met het hoogste aantal verbindingen, maar de drempel waarde voor de maximale sessie limiet is niet bereikt. Bij het configureren van diepte-eerste taak verdeling **moet** u een maximum aantal sessies per sessiehost instellen in de hostgroep.

Voer de volgende Power shell-cmdlet uit om een hostgroep te configureren voor het uitvoeren van diepte-eerste taak verdeling:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -DepthFirstLoadBalancer -MaxSessionLimit ###
```
