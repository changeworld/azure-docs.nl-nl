---
title: Beveiligings aanbevelingen voor installatie kopieën van Azure Marketplace | Microsoft Docs
description: In dit artikel worden aanbevelingen gegeven voor installatie kopieën die zijn opgenomen in de markt plaats
services: security
documentationcenter: na
author: barclayn
manager: barbkess
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: barclayn
ms.openlocfilehash: b82cf957f4bd74cb2c63bfd5a7fe73899b395df6
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795816"
---
# <a name="security-recommendations-for-azure-marketplace-images"></a>Beveiligings aanbevelingen voor installatie kopieën van Azure Marketplace

Uw installatie kopie moet voldoen aan deze beveiligings configuratie aanbevelingen. Zo kunt u een hoog beveiligings niveau voor installatie kopieën van partner oplossingen in azure Marketplace onderhouden.

Voer altijd een detectie van beveiligings problemen op uw installatie kopie uit voordat u deze verzendt. Als u een beveiligings probleem detecteert in uw eigen gepubliceerde installatie kopie, moet u uw klanten tijdig op de hoogte stellen van het beveiligings probleem en hoe dit kan worden opgelost.

## <a name="open-source-based-images"></a>Open-source installatie kopieën

|||
|--------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Categorie**                                                 | **Kijk**                                                                                                                                                                                                                                                                              |
| Beveiliging                                                     | Installeer de meest recente beveiligings patches voor de Linux-distributie.                                                                                                                                                                                                              |
| Beveiliging                                                     | Volg de richt lijnen voor de branche om de VM-installatie kopie te beveiligen voor de specifieke Linux-distributie.                                                                                                                                                                                     |
| Beveiliging                                                     | Beperk de kwets baarheid door minimale footprint te behouden met alleen de benodigde Windows Server-functies,-onderdelen,-services en-netwerk poorten.                                                                                                                                               |
| Beveiliging                                                     | Scan de bron code en de resulterende VM-installatie kopie voor malware.                                                                                                                                                                                                                                   |
| Beveiliging                                                     | De VHD-installatie kopie bevat alleen de benodigde vergrendelde accounts die geen standaard wachtwoorden hebben waarmee interactieve aanmelding zou worden toegestaan. geen deuren terug.                                                                                                                                           |
| Beveiliging                                                     | Schakel Firewall regels uit, tenzij toepassing functioneel afhankelijk is, zoals een firewall apparaat.                                                                                                                                                                             |
| Beveiliging                                                     | Verwijder alle gevoelige informatie uit de VHD-installatie kopie, zoals SSH-sleutels, bekende hosts-bestand, logboek bestanden en overbodige certificaten.                                                                                                                                       |
| Beveiliging                                                     | Vermijd het gebruik van LVM.                                                                                                                                                                                                                                            |
| Beveiliging                                                     | De nieuwste versies van de vereiste bibliotheken bevatten: </br> -OpenSSL v 1.0 of hoger </br> -Python 2,5 of hoger (python 2.6 + wordt sterk aanbevolen) </br> -Python pyasn1-pakket als dit nog niet is geïnstalleerd </br> -d. OpenSSL v 1,0 of hoger                                                                |
| Beveiliging                                                     | Wis de bash/shell-geschiedenis vermeldingen.                                                                                                                                                                                                                                             |
| Netwerken                                                   | Neem standaard de SSH-server op. Stel SSH in op sshd config met de volgende optie: ' ClientAliveInterval 180.                                                                                                                                                        |
| Netwerken                                                   | Verwijder een aangepaste netwerk configuratie uit de installatie kopie. Verwijder het `rm /etc/resolv.conf`.                                                                                                                                                                                |
| Implementatie                                                   | Installeer de meest recente Azure Linux-agent.</br> -Installeer met het pakket RPM of het deb.  </br> -U kunt ook het hand matige installatie proces gebruiken, maar de installatie pakketten worden aanbevolen en voor keur. </br> -Als u de agent hand matig installeert vanuit de GitHub-opslag plaats, kopieert u eerst het `waagent` bestand naar `/usr/sbin` en voert u uit (als basis): </br>`# chmod 755 /usr/sbin/waagent` </br>`# /usr/sbin/waagent -install` </br>Het configuratie bestand van de agent wordt geplaatst op `/etc/waagent.conf`. |
| Implementatie                                                   | Zorg ervoor dat de ondersteuning van Azure onze partners kan voorzien van een seriële console-uitvoer wanneer dat nodig is en een voldoende time-out voor het koppelen van de besturingssysteem schijf van Cloud opslag biedt. Voeg de volgende para meters toe aan de opstart regel voor de installatie kopie-kernel: `console=ttyS0 earlyprintk=ttyS0 rootdelay=300`. |
| Implementatie                                                   | Geen wissel partitie op de besturingssysteem schijf. De Linux-agent kan worden gevraagd om te maken op de lokale bron schijf.         |
| Implementatie                                                   | Maak een enkele hoofdpartitie voor de schijf met het besturingssysteem.      |
| Implementatie                                                   | 64-bits besturings systeem.                                                                                                                                                                                                                                                          |

## <a name="windows-server-based-images"></a>Installatie kopieën op basis van Windows Server

|||
|-------------| -------------------------|
| **Categorie**                                                     | **Kijk**                                                                                                                                                                |
| Beveiliging                                                         | Gebruik een veilige basis installatie kopie van het besturings systeem. De VHD die wordt gebruikt voor de bron van een installatie kopie op basis van Windows Server moet afkomstig zijn uit de installatie kopieën van Windows Server-besturings systemen die via Microsoft Azure worden meegeleverd. |
| Beveiliging                                                         | Installeer alle meest recente beveiligings updates.                                                                                                                                     |
| Beveiliging                                                         | Toepassingen mogen niet afhankelijk zijn van beperkte gebruikers namen, zoals Administrator, root of admin.                                                                |
| Beveiliging                                                         | Schakel BitLocker-stationsversleuteling in voor zowel harde schijven van het besturings systeem als voor gegevens harde schijven.                                                             |
| Beveiliging                                                         | Beperk de kwets baarheid door minimale footprint te behouden met alleen de benodigde Windows Server-functies,-onderdelen,-services en-netwerk poorten ingeschakeld.                         |
| Beveiliging                                                         | Scan de bron code en de resulterende VM-installatie kopie voor malware.                                                                                                                     |
| Beveiliging                                                         | Stel de beveiligings update voor Windows Server-installatie kopieën in op automatisch bijwerken.                                                                                                                |
| Beveiliging                                                         | De VHD-installatie kopie bevat alleen de benodigde vergrendelde accounts die geen standaard wachtwoorden hebben waarmee interactieve aanmelding zou worden toegestaan. geen deuren terug.                             |
| Beveiliging                                                         | Schakel Firewall regels uit, tenzij toepassing functioneel afhankelijk is, zoals een firewall apparaat.                                                               |
| Beveiliging                                                         | Verwijder alle gevoelige informatie uit de VHD-installatie kopie, inclusief HOSTS-bestanden, logboek bestanden en overbodige certificaten.                                              |
| Implementatie                                                       | 64-bits besturings systeem.                            |

Zelfs als uw organisatie geen installatie kopieën heeft in de Azure Marketplace, kunt u de installatie kopieën van Windows en Linux controleren op basis van deze aanbevelingen.

## <a name="contacting-customers"></a>Contact opnemen met klanten

Klanten en hun e-mail adres identificeren:

1.  In Cloud Partner-portal selecteert u op de rails de optie **inzichten**.
2.  Gebruik op het tabblad **Orders en gebruik** de velden **begin datum** en **eind datum** om het gebruik van query's binnen het vereiste datum bereik te doorzoeken. Hier ziet u de Azure-abonnementen die dagelijks zijn gebruikt voor de aanbieding. Deze gegevens exporteren. 
3.  Op dezelfde manier kunt u op het tabblad **klant** een query uitvoeren op uw klanten.
4.  Zoek de abonnements-ID van stap 2 in de abonnements-ID uit stap 3 om de benodigde klant gegevens te vinden.
