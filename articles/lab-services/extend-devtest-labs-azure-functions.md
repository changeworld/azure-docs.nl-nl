---
title: Azure DevTest Labs uitbreiden met behulp van Azure Functions | Microsoft Docs
description: Meer informatie over het uitbreiden van Azure DevTest Labs met behulp van Azure Functions.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: dd1fc4c1076d89c12b25837db9fa6a0ac3e1f3a5
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/23/2019
ms.locfileid: "70014358"
---
# <a name="use-azure-functions-to-extend-devtest-labs"></a>Azure Functions gebruiken om DevTest Labs uit te breiden
U kunt Azure Functions gebruiken ter ondersteuning van andere scenario's dan die die al door DevTest Labs worden ondersteund. Azure Functions kan worden gebruikt om de ingebouwde functionaliteit van de service uit te breiden om te voldoen aan uw bedrijfsspecifieke behoeften. De volgende lijst bevat enkele van de mogelijke scenario's. In dit artikel wordt beschreven hoe u een van deze voorbeeld scenario's implementeert.

- Een samen vatting van virtuele machines (Vm's) op het hoogste niveau in het lab opgeven
- [Een lab configureren voor het gebruik van een extern bureau blad-gateway](configure-lab-remote-desktop-gateway.md)
- Nalevings rapportage op de pagina interne ondersteuning
- Gebruikers in staat stellen om bewerkingen te volt ooien waarvoor uitgebreide machtigingen nodig zijn in het abonnement
- [Werk stromen starten op basis van DevTest Labs-gebeurtenissen](https://github.com/RogerBestMsft/DTL-SecureArtifactData)

## <a name="overview"></a>Overzicht
[Azure functions](../azure-functions/functions-overview.md) is een serverloze computing platform in Azure. Door gebruik te maken van Azure Functions in een oplossing met DevTest Labs kunnen we de bestaande functies uitbreiden met onze eigen aangepaste code. Zie [Azure functions-documentatie](../azure-functions/functions-overview.md)voor meer informatie over Azure functions. In dit artikel wordt gebruikgemaakt van een voor beeld van een samen vatting van de virtuele machines in het lab op het hoogste niveau, zoals in de volgende gevallen: Azure Functions

**Voor beeld van vereiste/scenario**: Gebruikers kunnen details weer geven over alle virtuele machines in een lab, met inbegrip van het besturings systeem, de eigenaar en eventuele toegepaste artefacten.  Als het item Windows- **updates Toep assen** niet recent is toegepast, kunt u dit ook op een eenvoudige manier Toep assen.

Als u het scenario wilt volt ooien, gebruikt u twee functies zoals beschreven in het volgende diagram:  

![Algehele stroom](./media/extend-devtest-labs-azure-functions/flow.png)

De bron code voor deze voorbeeld functies bevindt zich in de [github-opslag plaats van DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions) (zowel C# als Power shell-implementaties zijn beschikbaar).

- **UpdateInternalSupportPage**: Met deze functie wordt een query uitgevoerd op DevTest Labs en wordt de interne ondersteunings pagina rechtstreeks bijgewerkt met details over de virtuele machines.
- **ApplyWindowsUpdateArtifact**: Voor een virtuele machine in een Lab past deze functie het **Windows Update** -artefact toe.

## <a name="how-it-works"></a>Hoe werkt het?
Wanneer gebruikers de **interne** ondersteunings pagina in DevTest Labs selecteren, hebben ze een vooraf gevulde pagina met informatie over vm's, Lab-eigen aars en ondersteunings contacten.  

Wanneer u de knop **selecteren om te vernieuwen** selecteert, wordt de eerste Azure-functie aangeroepen door de pagina: **UpdateInternalSupportPage**. De functie vraagt DevTest Labs naar informatie en schrijft vervolgens de **interne** ondersteunings pagina met de nieuwe informatie.

Er is een extra actie die kan worden uitgevoerd voor alle virtuele machines waarop de Windows Update-artefacten niet recent zijn toegepast, is er een knop voor het Toep assen van Windows-updates op de VM. Wanneer u de knop ***Windows Update uitvoeren** voor een VM selecteert, wordt de tweede Azure-functie aangeroepen door de pagina: **ApplyWindowsUpdateArtifact**. Met deze functie wordt gecontroleerd of de virtuele machine wordt uitgevoerd. als dit het geval is, past het [Windows Update](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-install-windows-updates) artefact rechtstreeks toe.

## <a name="step-by-step-walkthrough"></a>Stapsgewijze instructies
In deze sectie vindt u stapsgewijze instructies voor het instellen van Azure-resources die nodig zijn om de **interne** ondersteunings pagina bij te werken. In dit overzicht vindt u een voor beeld van het uitbreiden van DevTest Labs. U kunt dit patroon gebruiken voor andere scenario's.

### <a name="step-1-create-a-service-principal"></a>Stap 1: Een service-principal maken 
De eerste stap bestaat uit het ophalen van een service-principal met machtigingen voor het abonnement dat het Lab bevat. De Service-Principal moet de verificatie op basis van wacht woorden gebruiken. U kunt dit doen met [Azure cli](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest), [Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps?view=azps-2.5.0)of de [Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md). Als u al een Service-Principal hebt om te gebruiken, kunt u deze stap overs Laan.

Noteer de **toepassings-id**, **sleutel**en **Tenant-id** voor de Service-Principal. U hebt deze later nodig in dit overzicht. 

### <a name="step-2-download-the-sample-and-open-in-visual-studio-2019"></a>Stap 2: Het voor beeld downloaden en openen in Visual Studio 2019
Down load lokaal een kopie van het [ C# Azure functions](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions/CSharp) -voor beeld (door de opslag plaats te klonen of [](https://github.com/Azure/azure-devtestlab/archive/master.zip)door de opslag plaats te downloaden).  

1. Open de voorbeeld oplossing met Visual Studio 2019.  
1. Installeer de **Azure Development** -workload voor Visual Studio als u deze nog niet hebt geïnstalleerd. Het kan worden geïnstalleerd via **hulpprogram ma's** -> en het menu-item**hulpprogram ma's ophalen** .

    ![Azure Development-workload](./media/extend-devtest-labs-azure-functions/azure-development-workload-vs.png)
1. Bouw de oplossing. Selecteer **Build** en bouw vervolgens het menu-item van de **oplossing** .

### <a name="step-3-deploy-the-sample-to-azure"></a>Stap 3: Het voor beeld implementeren in azure
Klik in Visual Studio in het venster **Solution Explorer** met de rechter muisknop op het project **AzureFunctions** en selecteer vervolgens **publiceren**. Volg de wizard om het publiceren naar een nieuwe of een bestaand Azure-functie-app te volt ooien. Zie voor gedetailleerde informatie over het ontwikkelen en implementeren van Azure functions met behulp van Visual Studio [Azure functions ontwikkelen met Visual Studio](../azure-functions/functions-develop-vs.md).

![Dialoog venster publiceren](./media/extend-devtest-labs-azure-functions/publish-dialog.png)


### <a name="step-4--gather-application-settings"></a>Stap 4:  Toepassings instellingen verzamelen
Zodra de functies zijn gepubliceerd, moet u de Url's voor deze functies ophalen van de Azure Portal. 

1. Navigeer naar [Azure Portal](https://portal.azure.com). 
1. Zoek de functie-app.
1. Selecteer de functie op de pagina **functie-apps** . 
1. Selecteer **functie-URL ophalen** , zoals wordt weer gegeven in de volgende afbeelding. 

    ![Url's voor Azure functions](./media/extend-devtest-labs-azure-functions/function-url.png)
4. Kopieer en bewaar de URL. Herhaal deze stappen voor de andere Azure-functie. 

Daarnaast hebt u aanvullende informatie nodig over de Service-Principal, zoals de toepassings-ID, sleutel en Tenant-ID.


### <a name="step-5--update-application-settings"></a>Stap 5:  Toepassings instellingen bijwerken
Selecteer in Visual Studio, nadat u de Azure-functie hebt gepubliceerd, de **Azure app service instellingen bewerken** onder **acties**. De volgende toepassings instellingen bijwerken (extern):

- AzureFunctionUrl_ApplyUpdates
- AzureFunctionUrl_UpdateSupportPage
- WindowsUpdateAllowedDays (standaard ingesteld op 7)
- ServicePrincipal_AppId
- ServicePrincipal_Key
- ServicePrincipal_Tenant

    ![Toepassingsinstellingen](./media/extend-devtest-labs-azure-functions/application-settings.png)

### <a name="step-6-test-the-azure-function"></a>Stap 6: De Azure-functie testen
De laatste stap in dit scenario is het testen van de Azure-functie.  

1. Ga naar de functie **UpdateInternalSupportPage** in de functie-app die u in stap 3 hebt gemaakt. 
1. Selecteer **test** aan de rechter kant van de pagina. 
1. Voer in de route-eigenschappen (LABNAME, RESOURCEGROUPNAME en SUBSCRIPTIONID) in.
1. Selecteer **uitvoeren** om de functie uit te voeren.  

    Met deze functie wordt de interne ondersteunings pagina van het opgegeven Lab bijgewerkt. Het bevat ook een knop waarmee gebruikers de functie de volgende keer rechtstreeks kunnen aanroepen

    ![Test functie](./media/extend-devtest-labs-azure-functions/test-function.png)

## <a name="next-steps"></a>Volgende stappen
Met Azure Functions kunt u de functionaliteit van DevTest Labs uitbreiden naast wat er al is ingebouwd en Help klanten te voldoen aan hun unieke vereisten voor hun teams. Dit patroon kan worden uitgebreid & uitgebreidere, nog veel meer.  Raadpleeg de volgende artikelen voor meer informatie over DevTest Labs: 

- [DevTest Labs Enter prise-referentie architectuur](devtest-lab-reference-architecture.md)
- [Veelgestelde vragen](devtest-lab-faq.md)
- [DevTest Labs omhoog schalen](devtest-lab-guidance-scale.md)
- [DevTest Labs automatiseren met Power shell](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Modules/Library/Tests)








