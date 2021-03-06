---
title: Beheer eindpunt van beheerd exemplaar detecteren
description: Meer informatie over het verkrijgen van een openbaar IP-adres voor Azure SQL Database Managed instance Management-eind punt en het controleren van de ingebouwde firewall beveiliging
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
ms.date: 12/04/2018
ms.openlocfilehash: 03cd89084c2bae3339311f2f684a0d5e7bac1f68
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825723"
---
# <a name="determine-the-management-endpoint-ip-address"></a>Het IP-adres van het beheer eindpunt bepalen

Het virtuele cluster Azure SQL Database Managed instance bevat een beheer eindpunt dat door micro soft wordt gebruikt voor beheer bewerkingen. Het beheer eindpunt wordt beveiligd met een ingebouwde firewall op het netwerk niveau en de verificatie op basis van wederzijdse certificaten op toepassings niveau. U kunt het IP-adres van het beheer eindpunt bepalen, maar u hebt geen toegang tot dit eind punt.

Als u het IP-adres van het beheer wilt bepalen, moet u een DNS-zoek opdracht uitvoeren op de FQDN van uw beheerde exemplaar: `mi-name.zone_id.database.windows.net`. Hiermee wordt een DNS-vermelding geretourneerd die lijkt `trx.region-a.worker.vnet.database.windows.net`. U kunt vervolgens een DNS-zoek opdracht uitvoeren op deze FQDN met ". vnet" verwijderd. Hiermee wordt het IP-adres van het beheer geretourneerd. 

Deze Power shell doet dit allemaal voor u als u \<MI FQDN-\> vervangt door de DNS-vermelding van uw beheerde exemplaar: `mi-name.zone_id.database.windows.net`:
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

Zie voor meer informatie over beheerde exemplaren en connectiviteit de [Azure SQL database Managed instance connectivity-architectuur](sql-database-managed-instance-connectivity-architecture.md).
