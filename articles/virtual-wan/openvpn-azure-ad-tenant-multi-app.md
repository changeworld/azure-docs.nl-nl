---
title: 'Virtuele WAN: Azure AD-Tenant voor verschillende gebruikers groepen: Azure AD-verificatie'
description: U kunt P2S VPN gebruiken om verbinding te maken met uw VNet met behulp van Azure AD-verificatie
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: alzam
ms.openlocfilehash: 4e667dcccd612873e8633c375bc3ce611e11c962
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77485911"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Een Azure Active Directory-Tenant maken voor P2S OpenVPN-protocol verbindingen

Wanneer u verbinding maakt met uw VNet, kunt u verificatie op basis van certificaten of RADIUS-verificatie gebruiken. Wanneer u echter het open VPN-protocol gebruikt, kunt u ook Azure Active Directory-verificatie gebruiken. Als u wilt dat een andere set gebruikers verbinding kan maken met verschillende VPN-gateways, kunt u meerdere apps in AD registreren en deze koppelen aan verschillende VPN-gateways.

Dit artikel helpt u bij het instellen van een Azure AD-Tenant voor P2S OpenVPN-verificatie, en het maken en registreren van meerdere apps in azure AD om verschillende gebruikers en groepen toegang te geven.

> [!NOTE]
> Azure AD-verificatie wordt alleen ondersteund voor OpenVPN®-protocol verbindingen.
>

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="site"></a>6. een nieuwe P2S-configuratie maken

Een P2S-configuratie definieert de parameters om verbinding te maken met externe clients.

1. Stel de volgende variabelen in en vervang waarden naar behoefte voor uw omgeving.

   ```azurepowershell-interactive
   $aadAudience = "00000000-abcd-abcd-abcd-999999999999"
   $aadIssuer = "https://sts.windows.net/00000000-abcd-abcd-abcd-999999999999/"
   $aadTenant = "https://login.microsoftonline.com/00000000-abcd-abcd-abcd-999999999999"    
   ```

2. Voer de volgende opdrachten uit om de configuratie te maken:

   ```azurepowershell-interactive
   $aadConfig = New-AzVpnServerConfiguration -ResourceGroupName <ResourceGroup> -Name newAADConfig -VpnProtocol OpenVPN -VpnAuthenticationType AAD -AadTenant $aadTenant -AadIssuer $aadIssuer -AadAudience $aadAudience -Location westcentralus
   ```

   > [!NOTE]
   > Gebruik de toepassings-ID van de Azure VPN-client niet in de bovenstaande opdrachten: alle gebruikers krijgen toegang tot de VPN-gateway. Gebruik de ID van de toepassing (en) die u hebt geregistreerd.

## <a name="hub"></a>7. hub-toewijzing bewerken

1. Navigeer naar de Blade **hubs** onder het virtuele WAN.

2. Selecteer de hub waaraan u de VPN-server configuratie wilt koppelen en klik op het weglatings teken (...).

    ![Nieuwe site](media/openvpn-azure-ad-tenant-multi-app/p2s4.jpg)

3. Klik op **virtuele hub bewerken**.

4. Schakel het selectie vakje een **punt-naar-site-gateway insluiten** in en selecteer de gewenste **Gateway-schaal eenheid** .

    ![Nieuwe site](media/openvpn-azure-ad-tenant-multi-app/p2s2.jpg)

5. Voer de **adres groep** in van waaruit de VPN-clients IP-adressen worden toegewezen.

6. Klik op **Bevestigen**.

7. De bewerking kan dertig minuten duren.

## <a name="device"></a>8. VPN-profiel downloaden

Gebruik het VPN-profiel om uw clients te configureren.

1. Klik op de pagina voor uw virtuele WAN op **VPN-configuraties voor gebruikers**.

2. Klik boven aan de pagina op **VPN-configuratie voor gebruiker downloaden**.

3. Wanneer het bestand gereed is, klikt u op de koppeling om het te downloaden.

4. Gebruik het profiel bestand voor het configureren van de VPN-clients.

5. Pak het gedownloade zip-bestand uit.

6. Blader naar de map ungezipte ' AzureVPN '.

7. Noteer de locatie van het bestand azurevpnconfig. XML. Azurevpnconfig. XML bevat de instelling voor de VPN-verbinding en kan rechtstreeks in de Azure VPN-client toepassing worden geïmporteerd. U kunt dit bestand ook distribueren naar alle gebruikers die verbinding moeten maken via e-mail of een andere manier. De gebruiker heeft geldige Azure AD-referenties nodig om verbinding te kunnen maken.

## <a name="9-configure-user-vpn-clients"></a>9. VPN-clients voor gebruikers configureren

Als u verbinding wilt maken, moet u de Azure VPN-client downloaden en het VPN-client profiel importeren dat in de vorige stappen is gedownload op elke computer die verbinding wil maken met het VNet.

> [!NOTE]
> Azure AD-verificatie wordt alleen ondersteund voor OpenVPN®-protocol verbindingen.
>

#### <a name="to-download-the-azure-vpn-client"></a>De Azure VPN-client downloaden

Gebruik deze [koppeling](https://go.microsoft.com/fwlink/?linkid=2117554) om de Azure VPN-client te downloaden.

#### <a name="import"></a>Een client profiel importeren

1. Selecteer op de pagina **importeren**.

    ![wederinvoer](./media/openvpn-azure-ad-tenant-multi-app/import/import1.jpg)

2. Blader naar het profiel XML-bestand en selecteer het. Selecteer **openen**terwijl het bestand is geselecteerd.

    ![wederinvoer](./media/openvpn-azure-ad-tenant-multi-app/import/import2.jpg)

3. Geef de naam van het profiel op en selecteer **Opslaan**.

    ![wederinvoer](./media/openvpn-azure-ad-tenant-multi-app/import/import3.jpg)

4. Selecteer **verbinding maken** om verbinding te maken met het VPN.

    ![wederinvoer](./media/openvpn-azure-ad-tenant-multi-app/import/import4.jpg)

5. Zodra de verbinding is gemaakt, wordt het pictogram groen en vervolgens **verbonden**.

    ![wederinvoer](./media/openvpn-azure-ad-tenant-multi-app/import/import5.jpg)

#### <a name="delete"></a>Een client profiel verwijderen

1. Selecteer het beletsel teken (...) naast het client profiel dat u wilt verwijderen. Selecteer vervolgens **verwijderen**.

    ![delete](./media/openvpn-azure-ad-tenant-multi-app/delete/delete1.jpg)

2. Selecteer **verwijderen** om te verwijderen.

    ![delete](./media/openvpn-azure-ad-tenant-multi-app/delete/delete2.jpg)

#### <a name="diagnose"></a>Verbindings problemen vaststellen

1. Als u verbindings problemen wilt vaststellen, kunt u het hulp programma voor **diagnose** gebruiken. Selecteer het beletsel teken (...) naast de VPN-verbinding die u wilt diagnosticeren om het menu weer te geven. Selecteer vervolgens **diagnose**.

    ![vaststellen](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose1.jpg)

2. Selecteer op de pagina **Eigenschappen van verbinding** de optie **diagnose uitvoeren**.

    ![vaststellen](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose2.jpg)

3. Meld u aan met uw referenties.

    ![vaststellen](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose3.jpg)

4. Bekijk de resultaten van de diagnose.

    ![vaststellen](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose4.jpg)

## <a name="viewwan"></a>10. uw virtuele WAN weer geven

1. Navigeer naar uw virtuele WAN.

2. Op de pagina Overzicht vertegenwoordigt elk punt op de kaart een hub. Houd de muisaanwijzer boven een punt om de statussamenvatting voor de hub weer te geven.

3. In de sectie Hubs en verbindingen vindt u informatie over de hubstatus, site, regio, VPN-verbindingsstatus en verzonden en ontvangen bytes.

## <a name="viewhealth"></a>11. de status van uw resource weer geven

1. Navigeer naar uw WAN.

2. Klik op de pagina van uw WAN, in de sectie **Ondersteuning en probleemoplossing**, op **Status** en bekijk de status van uw resource.

## <a name="cleanup"></a>Resources opschonen

U kunt de opdracht [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) gebruiken om de resourcegroep en alle resources die deze bevat te verwijderen, wanneer u deze niet meer nodig hebt. Vervangen 'myResourceGroup' door de naam van uw resourcegroep en voer de volgende PowerShell-opdracht uit:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Virtual WAN de pagina [Overzicht van Virtual WAN](virtual-wan-about.md).
