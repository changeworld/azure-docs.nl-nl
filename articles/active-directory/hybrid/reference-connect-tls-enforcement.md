---
title: 'Azure AD Connect: TLS 1,2 afdwinging voor Azure Active Directory Connect | Microsoft Docs'
description: In dit artikel vindt u een overzicht van alle releases van Azure AD Connect en Azure AD Sync
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ff5c75785622b43e66b808009c4674d4b2f2b50
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78300845"
---
# <a name="tls-12-enforcement-for-azure-ad-connect"></a>TLS 1,2 afdwingen voor Azure AD Connect

Transport Layer Security (TLS) Protocol versie 1,2 is een crypto grafie-protocol dat is ontworpen om beveiligde communicatie mogelijk te maken.  Het TLS-protocol is voornamelijk gericht op privacy-en gegevens integriteit.  TLS heeft een groot aantal iteraties door lopen met versie 1,2 die is gedefinieerd in [RFC 5246](https://tools.ietf.org/html/rfc5246).  Azure Active Directory Connect versie 1.2.65.0 en hoger nu volledige ondersteuning voor het gebruik van alleen TLS 1,2 voor communicatie met Azure.  Dit document bevat informatie over het afdwingen van de Azure AD Connect-server om alleen TLS 1,2 te gebruiken.

## <a name="update-the-registry"></a>Het REGI ster bijwerken
Om ervoor te zorgen dat de Azure AD Connect-server alleen TLS 1,2 gebruikt, moet het REGI ster van de Windows-Server worden bijgewerkt.  Stel de volgende register sleutels op de Azure AD Connect-server in.

>[!IMPORTANT]
>Nadat u het REGI ster hebt bijgewerkt, moet u de Windows-Server opnieuw opstarten om de wijzigingen van kracht te laten worden.


### <a name="enable-tls-12"></a>TLS 1,2 inschakelen
- [HKEY_LOCAL_MACHINE \SOFTWARE\WOW6432Node\Microsoft\\. NETFramework\v4.0.30319]
  - "SystemDefaultTlsVersions" = dword: 00000001
  - "Schusestrongcrypto toe" = dword: 0000001
- [HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\\. NETFramework\v4.0.30319]
  - "SystemDefaultTlsVersions" = dword: 00000001
  - "SchUseStrongCrypto"=dword:00000001
- [HKEY_LOCAL_MACHINE \SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ server]
  - "Ingeschakeld" = dword: 00000001
- [HKEY_LOCAL_MACHINE \SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ server]
  - "DisabledByDefault" = dword: 00000000 
- [HKEY_LOCAL_MACHINE \SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ client]
  - "Ingeschakeld" = dword: 00000001
- [HKEY_LOCAL_MACHINE \SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ client]
  - "DisabledByDefault" = dword: 00000000

### <a name="powershell-script-to-enable-tls-12"></a>Power shell-script voor het inschakelen van TLS 1,2
U kunt het volgende Power shell-script gebruiken om TLS 1,2 op uw Azure AD Connect-server in te scha kelen.

```powershell
    New-Item 'HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319' -Force | Out-Null

    New-ItemProperty -path 'HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319' -name 'SystemDefaultTlsVersions' -value '1' -PropertyType 'DWord' -Force | Out-Null

    New-ItemProperty -path 'HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319' -name 'SchUseStrongCrypto' -value '1' -PropertyType 'DWord' -Force | Out-Null

    New-Item 'HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319' -Force | Out-Null

    New-ItemProperty -path 'HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319' -name 'SystemDefaultTlsVersions' -value '1' -PropertyType 'DWord' -Force | Out-Null

    New-ItemProperty -path 'HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319' -name 'SchUseStrongCrypto' -value '1' -PropertyType 'DWord' -Force | Out-Null

    New-Item 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server' -Force | Out-Null
    
    New-ItemProperty -path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server' -name 'Enabled' -value '1' -PropertyType 'DWord' -Force | Out-Null
    
    New-ItemProperty -path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server' -name 'DisabledByDefault' -value 0 -PropertyType 'DWord' -Force | Out-Null
    
    New-Item 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client' -Force | Out-Null
    
    New-ItemProperty -path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client' -name 'Enabled' -value '1' -PropertyType 'DWord' -Force | Out-Null
    
    New-ItemProperty -path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client' -name 'DisabledByDefault' -value 0 -PropertyType 'DWord' -Force | Out-Null
    Write-Host 'TLS 1.2 has been enabled.'
```

### <a name="disable-tls-12"></a>TLS 1,2 uitschakelen
- [HKEY_LOCAL_MACHINE \SOFTWARE\WOW6432Node\Microsoft\\. NETFramework\v4.0.30319]
  - "SystemDefaultTlsVersions" = dword: 00000000
  - "Schusestrongcrypto toe" = dword: 0000000
- [HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\\. NETFramework\v4.0.30319]
  - "SystemDefaultTlsVersions" = dword: 00000000
  - "Schusestrongcrypto toe" = dword: 00000000
- [HKEY_LOCAL_MACHINE \SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ server]
  - "Ingeschakeld" = dword: 00000000
- [HKEY_LOCAL_MACHINE \SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ server]
  - "DisabledByDefault" = dword: 00000001
- [HKEY_LOCAL_MACHINE \SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ client]
  - "Ingeschakeld" = dword: 00000000
- [HKEY_LOCAL_MACHINE \SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ client]
  - "DisabledByDefault" = dword: 00000001 

### <a name="powershell-script-to-disable-tls-12"></a>Power shell-script voor het uitschakelen van TLS 1,2
U kunt het volgende Power shell-script gebruiken om TLS 1,2 op uw Azure AD Connect-server uit te scha kelen. \

```powershell
    New-Item 'HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319' -Force | Out-Null

    New-ItemProperty -path 'HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319' -name 'SystemDefaultTlsVersions' -value '0' -PropertyType 'DWord' -Force | Out-Null

    New-ItemProperty -path 'HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319' -name 'SchUseStrongCrypto' -value '0' -PropertyType 'DWord' -Force | Out-Null

    New-Item 'HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319' -Force | Out-Null

    New-ItemProperty -path 'HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319' -name 'SystemDefaultTlsVersions' -value '0' -PropertyType 'DWord' -Force | Out-Null

    New-ItemProperty -path 'HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319' -name 'SchUseStrongCrypto' -value '0' -PropertyType 'DWord' -Force | Out-Null

    New-Item 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server' -Force | Out-Null
    
    New-ItemProperty -path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server' -name 'Enabled' -value '0' -PropertyType 'DWord' -Force | Out-Null
    
    New-ItemProperty -path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server' -name 'DisabledByDefault' -value 1 -PropertyType 'DWord' -Force | Out-Null
    
    New-Item 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client' -Force | Out-Null
    
    New-ItemProperty -path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client' -name 'Enabled' -value '0' -PropertyType 'DWord' -Force | Out-Null
    
    New-ItemProperty -path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client' -name 'DisabledByDefault' -value 1 -PropertyType 'DWord' -Force | Out-Null
    Write-Host 'TLS 1.2 has been disabled.'
```

## <a name="next-steps"></a>Volgende stappen
* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](whatis-hybrid-identity.md)
