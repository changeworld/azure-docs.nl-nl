---
title: 'Zelfstudie: Azure CLI gebruiken voor implementatie in een bestaand virtueel netwerk - Azure Dedicated HSM | Microsoft Docs'
description: Zelfstudie voor het implementeren van een toegewezen HSM in een bestaand virtueel netwerk met behulp van CLI
services: dedicated-hsm
documentationcenter: na
author: msmbaldwin
manager: rkarlin
editor: ''
ms.service: key-vault
ms.topic: tutorial
ms.custom: mvc, seodec18
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2019
ms.author: mbaldwin
ms.openlocfilehash: 4750673eb60529d812e4df71de9203d4d59a0cc9
ms.sourcegitcommit: 0eb0673e7dd9ca21525001a1cab6ad1c54f2e929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77212257"
---
# <a name="tutorial-deploying-hsms-into-an-existing-virtual-network-using-cli"></a>Zelf studie: Hsm's implementeren in een bestaand virtueel netwerk met behulp van CLI

Azure Toegewezen HSM biedt een fysiek apparaat voor gebruik door één klant, met volledige beheer en verantwoordelijkheid voor volledig beheer. Door het gebruik van fysieke apparaten moet de apparaattoewijzing worden beheerd in Microsoft om ervoor te zorgen dat de capaciteit effectief wordt beheerd. Dit betekent dat de Toegewezen HSM-service in een Azure-abonnement niet gewoon zichtbaar is voor de inrichting van resources. Elke Azure-klant die toegang nodig heeft tot de Toegewezen HSM-service, moet eerst contact opnemen met de beheerder van zijn Microsoft-account om registratie aan te vragen voor de Toegewezen HSM-service. Pas wanneer dit proces is voltooid, is inrichting mogelijk. 

In deze zelfstudie wordt een typisch inrichtingsproces getoond, waarin:

- Een klant al een virtueel netwerk heeft
- De klant een virtuele machine heeft
- De klant HSM-resources moet toevoegen aan de bestaande omgeving.

Een typische implementatiearchitectuur met hoge beschikbaarheid in meerdere regio's kan er als volgt uitzien:

![implementatie in meerdere regio's](media/tutorial-deploy-hsm-cli/high-availability-architecture.png)

Deze zelfstudie richt zich op twee HSM's en de vereiste ExpressRoute-gateway (zie Subnet 1 hierboven), die worden geïntegreerd in een bestaand virtueel netwerk (zie VNET 1 hierboven).  Alle andere resources zijn standaard Azure-resources. Hetzelfde integratieproces kan worden gebruikt voor HSM's in subnet 4 op VNET 3 hierboven.

## <a name="prerequisites"></a>Vereisten

Azure Toegewezen HSM is momenteel niet beschikbaar in de Azure-portal. Alle interactie met de service verloopt via de opdrachtregel of met behulp van PowerShell. In deze zelfstudie wordt gebruikgemaakt van de CLI (opdrachtregelinterface) in Azure Cloud Shell. Volg de instructies in [Aan de slag met Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) als u niet bekend bent met Azure CLI.

Veronderstellingen:

- U hebt het registratieproces van Azure Toegewezen HSM voltooid
- U bent goedgekeurd voor het gebruik van de service. Als dit niet het geval is, neemt u contact op met de vertegenwoordiger van uw Microsoft-account voor meer informatie.
- U hebt een resourcegroep voor deze resources gemaakt en de nieuwe resources die u in deze zelfstudie hebt geïmplementeerd, gaan deel uitmaken van deze groep.
- U hebt het benodigde virtuele netwerk, het subnet en de virtuele machines al gemaakt aan de hand van het diagram hierboven en nu wilt u 2 HSM's integreren in deze implementatie.

In alle onderstaande instructies wordt ervan uitgegaan dat u al naar de Azure-portal bent gegaan en Cloud Shell hebt geopend (selecteer \>\_ ergens in de rechterbovenhoek van de portal).

## <a name="provisioning-a-dedicated-hsm"></a>Een toegewezen HSM inrichten

Het inrichten van HSM's en deze integreren in een bestaand virtueel netwerk via ExpressRoute Gateway wordt gevalideerd met behulp van SSH. Deze validatie zorgt voor bereikbaarheid en basisbeschikbaarheid van het HSM-apparaat bij verdere configuratieactiviteiten. Voor de volgende opdrachten wordt een Azure Resource Manager-sjabloon gebruikt om de HSM-resources en bijbehorende netwerkresources te maken.

### <a name="validating-feature-registration"></a>Functieregistratie valideren

Zoals hierboven is uitgelegd, vereist elke inrichtingsactiviteit dat de Toegewezen HSM-service voor uw abonnement wordt geregistreerd. Om deze te valideren, voert u de volgende opdracht uit in de cloudshell van de Azure-portal.

```azurecli
az feature show \
   --namespace Microsoft.HardwareSecurityModules \
   --name AzureDedicatedHSM
```

Met de volgende opdracht worden de netwerkfuncties voor de Toegewezen HSM-service geverifieerd.

```azurecli
az feature show \
   --namespace Microsoft.Network \
   --name AllowBaremetalServers
```

Beide opdrachten moeten de status Registered retourneren (zoals hieronder wordt weergegeven). Als de opdrachten de status Registered niet retourneren, moet u zich registreren voor deze service. Neem dan contact op met de vertegenwoordiger van het Microsoft-account.

![abonnementsstatus](media/tutorial-deploy-hsm-cli/subscription-status.png)

### <a name="creating-hsm-resources"></a>HSM-resources maken

Er wordt een HSM ingericht in het virtuele netwerk van een klant, daarom zijn een virtueel netwerk en een subnet vereist. Een afhankelijkheid voor de HSM om communicatie tussen het virtuele netwerk en een fysiek apparaat mogelijk te maken, is een ExpressRoute-gateway. En tot slot hebt u een virtuele machine nodig om met behulp van de Gemalto-clientsoftware toegang te krijgen tot het HSM-apparaat. Deze resources zijn voor het gebruiksgemak verzameld in een sjabloonbestand met bijbehorend parameterbestand. Neem rechtstreeks als HSMrequest@Microsoft.com contact op met Microsoft voor de bestanden.

Wanneer u de bestanden hebt, moet u het parameterbestand bewerken om uw voorkeursnamen voor resources in te voegen. Bewerk regels met 'waarde': ''.

- `namingInfix` Voorvoegsel voor namen van HSM-resources
- `ExistingVirtualNetworkName` Naam van het virtuele netwerk dat voor de HSM's wordt gebruikt
- `DedicatedHsmResourceName1` Naam van de HSM-resource in datacenter stempel 1
- `DedicatedHsmResourceName2` Naam van de HSM-resource in datacenter stempel 2
- `hsmSubnetRange` IP-adresbereik van subnet voor HSM's
- `ERSubnetRange` IP-adresbereik van subnet voor VNET-gateway

Hier volgt een voorbeeld van deze wijzigingen:

```json
{
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "namingInfix": {
      "value": "MyHSM"
    },
    "ExistingVirtualNetworkName": {
      "value": "MyHSM-vnet"
    },
    "DedicatedHsmResourceName1": {
      "value": "HSM1"
    },
    "DedicatedHsmResourceName2": {
      "value": "HSM2"
    },
    "hsmSubnetRange": {
      "value": "10.0.2.0/24"
    },
    "ERSubnetRange": {
      "value": "10.0.255.0/26"
    },
  }
}
```

Met het gekoppelde Azure Resource Manager-sjabloonbestand worden 6 resources gemaakt met deze informatie:

- Een subnet voor de HSM's in het opgegeven VNET
- Een subnet voor de gateway van het virtuele netwerk
- Een virtuele-netwerkgateway die het VNET verbindt met de HSM-apparaten
- Een openbaar IP-adres voor de gateway
- Een HSM in stempel 1
- Een HSM in stempel 2

Wanneer de parameterwaarden zijn ingesteld, moeten de bestanden worden geüpload naar de bestandsshare van de cloudshell in de Azure-portal voor gebruik. Klik rechtsboven in de Azure-portal op het symbool van de cloudshell '\>\_' om het onderste gedeelte van het scherm te wijzigen in een opdrachtomgeving. De opties hiervoor zijn BASH en PowerShell, en u moet BASH selecteren als dat nog niet is gebeurd.

De opdrachtshell heeft een optie Uploaden/Downloaden op de werkbalk. Selecteer deze om de sjabloon en parameterbestanden te uploaden naar de bestandsshare:

![bestandsshare](media/tutorial-deploy-hsm-cli/file-share.png)

Wanneer de bestanden zijn geüpload, bent u klaar om resources te maken. Voordat u nieuwe HSM-resources maakt, controleert u eerst of de volgende vereiste resources aanwezig zijn. U moet een virtueel netwerk hebben met subnetbereiken voor compute, HSM's en gateway. De volgende opdrachten zijn een voorbeeld van hoe een dergelijk virtueel netwerk wordt gemaakt.

```azurecli
az network vnet create \
  --name myHSM-vnet \
  --resource-group myRG \
  --address-prefix 10.2.0.0/16
  --subnet-name compute
  --subnet-prefix 10.2.0.0/24
```

```azurecli
--vnet-name myHSM-vnet \
  --resource-group myRG \
  --name hsmsubnet \
  --address-prefixes 10.2.1.0/24 \
  --delegations Microsoft.HardwareSecurityModules/dedicatedHSMs
```

```azurecli
az network vnet subnet create \
  --vnet-name myHSM-vnet \
  --resource-group myRG \
  --name GatewaySubnet \
  --address-prefixes 10.2.255.0/26
```

>[!NOTE]
>De belangrijkste configuratie die u voor het virtuele netwerk moet onthouden, is dat het subnet voor het HSM-apparaat delegaties moet hebben die zijn ingesteld op 'Microsoft.HardwareSecurityModules/dedicatedHSMs'.  De HSM-inrichting werkt alleen als deze optie is ingesteld.

Wanneer aan alle vereisten is voldaan, voert u de volgende opdracht uit om de Azure Resource Manager-sjabloon te gebruiken om te zorgen dat de waarden worden bijgewerkt met uw unieke namen (in ieder geval de naam van de resourcegroep):

```azurecli
az group deployment create \
   --resource-group myRG  \
   --template-file ./Deploy-2HSM-toVNET-Template.json \
   --parameters ./Deploy-2HSM-toVNET-Params.json \
   --name HSMdeploy \
   --verbose
```

Het duurt gewoonlijk maximaal 25 tot 30 minuten totdat deze implementatie is voltooid, waarvan het grootste gedeelte wordt gebruikt voor de HSM-apparaten

![inrichtingsstatus](media/tutorial-deploy-hsm-cli/progress-status.png)

Wanneer de implementatie is geslaagd, wordt er weergegeven: provisioningState: Succeeded. U kunt verbinding maken met de bestaande virtuele machine en SSH gebruiken om de beschikbaarheid van het HSM-apparaat te verzekeren.

## <a name="verifying-the-deployment"></a>De implementatie controleren

Voer de volgende opdrachtenset uit om te controleren of de apparaten zijn ingericht en de apparaatkenmerken weer te geven. Controleer of de resourcegroep op de juiste wijze is ingesteld en de resourcenaam overeenkomt met die in het parameterbestand.

```azurecli
subid=$(az account show --query id --output tsv)
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM1
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM2
```

![uitvoer van inrichting](media/tutorial-deploy-hsm-cli/progress-status2.png)

Nu kunt u ook de resources zien met behulp van de [Azure Resource Explorer](https://resources.azure.com/).   In de Explorer vouwt u aan de linkerkant 'Subscriptions' uit. Vouw achtereenvolgens uw specifieke abonnement voor Toegewezen HSM, 'Resource Groups' en de gebruikte resourcegroep uit. Selecteer tot slot het item 'Resources'.

## <a name="testing-the-deployment"></a>De implementatie testen

Om de implementatie te testen, maakt u verbinding met een virtuele machine die toegang heeft tot de HSM('s) en maakt u vervolgens rechtstreeks verbinding met het HSM-apparaat. Deze acties bevestigen dat de HSM bereikbaar is.
Het SSH-hulpprogramma wordt gebruikt om verbinding te maken met de virtuele machine. De opdracht is vergelijkbaar met de volgende, maar met de beheerdersnaam en DNS-naam die u hebt opgegeven in de parameter.

`ssh adminuser@hsmlinuxvm.westus.cloudapp.azure.com`

Het IP-adres van de VM kan ook worden gebruikt in plaats van de DNS-naam in de bovenstaande opdracht. Als de opdracht is geslaagd, wordt er om een wachtwoord gevraagd en moet u dit invoeren. Zodra u bent aangemeld bij de virtuele machine, kunt u zich aanmelden bij de HSM met behulp van het privé IP-adres dat u kunt vinden in de portal voor de netwerkinterface die is gekoppeld aan de HSM.

![lijst met onderdelen](media/tutorial-deploy-hsm-cli/resources.png)

>[!NOTE]
>U ziet nu het selectievakje Show hidden types. Als dit is ingeschakeld worden HSM-resources weergegeven.

Als u in de bovenstaande schermafbeelding op HSM1_HSMnic of HSM2_HSMnic klikt wordt het juiste privé IP-adres weergegeven. Anders wordt de opdracht `az resource show` hierboven gebruikt als een manier om het juiste IP-adres te identificeren. 

Als u het juiste IP-adres hebt, voert u de volgende opdracht uit, waarbij u dit adres vervangt:

`ssh tenantadmin@10.0.2.4`

Als dit lukt, wordt u om een wachtwoord gevraagd. Het standaardwachtwoord is PASSWORD en de HSM vraagt u om het wachtwoord te wijzigen. Stel een sterk wachtwoord in en gebruik het mechanisme waaraan uw organisatie de voorkeur geeft om het wachtwoord op te slaan en verlies te voorkomen.

>[!IMPORTANT]
>Als u dit wachtwoord kwijtraakt, moet de HSM opnieuw worden ingesteld, wat verlies van uw codes betekent.

Wanneer u met behulp van SSH bent verbonden met de HSM, voert u de volgende opdracht uit om te controleren of de HSM functioneert.

`hsm show`

De uitvoer ziet eruit zoals weergegeven in de onderstaande afbeelding:

![lijst met onderdelen](media/tutorial-deploy-hsm-cli/hsm-show-output.png)

Op dit moment hebt u alle resources toegewezen voor een implementatie van twee HSM's met hoge beschikbaarheid en hebt u de toegang en operationele status gevalideerd. Voor verdere configuratie of tests is meer werk met het HSM-apparaat zelf vereist. Hiervoor volgt u de instructies in hoofdstuk 7 van de beheerdershandleiding van Gemalto Luna Network HSM 7 om de HSM te initialiseren en partities te maken. Alle documentatie en software kunnen rechtstreeks via Gemalto worden gedownload zodra u bent geregistreerd in de Gemalto Customer Support Portal en een klant-id hebt. Download versie 7.2 van de clientsoftware om alle vereiste onderdelen op te halen.

## <a name="delete-or-clean-up-resources"></a>Resources verwijderen of opschonen

Als u klaar bent met het HSM-apparaat, kan het als resource worden verwijderd en worden geretourneerd aan de vrije pool. Uiteraard moet u zorg dragen voor eventuele vertrouwelijke gegevens van klanten die zich op het apparaat bevinden. De beste manier om ' zeroize ' een apparaat te verkrijgen, is het wacht woord voor de HSM-beheerder niet de juiste drie keer op te halen (Opmerking: dit is geen apparaat beheerder, het is de daad werkelijke HSM-beheerder). Als veiligheids maatregel om belang rijk materiaal te beschermen, kan het apparaat niet worden verwijderd als Azure-resource totdat het de status in nul heeft.

> [!NOTE]
> Als u problemen hebt met de configuratie van een Gemalto-apparaat, neemt u contact op met [Gemalto-klantondersteuning](https://safenet.gemalto.com/technical-support/).


Als u alle resources in deze resource groep hebt voltooid, kunt u deze allemaal verwijderen met de volgende opdracht:

```azurecli
az group deployment delete \
   --resource-group myRG \
   --name HSMdeploy \
   --verbose

```

## <a name="next-steps"></a>Volgende stappen

Nadat u de stappen in deze zelfstudie hebt voltooid, zijn de Toegewezen HSM-resources ingericht, beschikt u over een virtueel netwerk met de benodigde HSM's, en kunnen andere netwerkonderdelen communiceren met de HSM.  Nu kunt u deze implementatie aanvullen met meer resources als dit nodig is voor de implementatiearchitectuur waaraan u de voorkeur geeft. Zie de Concepts-documenten voor meer informatie over het plannen van een implementatie.
Een ontwerp met twee HSM's in een primaire regio voor beschikbaarheid op rekniveau, en twee HSM's in een secundaire regio voor regionale beschikbaarheid wordt aanbevolen. Het sjabloonbestand dat in deze zelfstudie wordt gebruikt, kan eenvoudig worden gebruikt als basis voor een implementatie met twee HSM's, maar de parameters ervan moeten worden gewijzigd om te voldoen aan uw vereisten.

* [Hoge beschikbaarheid](high-availability.md)
* [Fysieke beveiliging](physical-security.md)
* [Netwerken](networking.md)
* [Ondersteuning](supportability.md)
* [Controle](monitoring.md)
