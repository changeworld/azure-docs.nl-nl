---
title: CreateUiDefinition. JSON-bestand voor het deel venster Portal
description: Hierin wordt beschreven hoe u gebruikers interface definities maakt voor de Azure Portal. Wordt gebruikt bij het definiëren van Azure Managed Applications.
author: tfitzmac
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: tomfitz
ms.openlocfilehash: 5fcc87e894cfab0075b33835a0673b133c629b73
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75650875"
---
# <a name="createuidefinitionjson-for-azure-managed-applications-create-experience"></a>CreateUiDefinition. json voor het maken van ervaring met Azure Managed Application

In dit document worden de belangrijkste concepten van het bestand **createUiDefinition. json** geïntroduceerd dat Azure Portal gebruikt om de gebruikers interface te definiëren bij het maken van een beheerde toepassing.

De sjabloon is als volgt

```json
{
   "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
   "handler": "Microsoft.Azure.CreateUIDef",
   "version": "0.1.2-preview",
   "parameters": {
      "basics": [ ],
      "steps": [ ],
      "outputs": { },
      "resourceTypes": [ ]
   }
}
```

Een CreateUiDefinition bevat altijd drie eigenschappen: 

* afhandelingsprocedure
* versie
* parameters

De handler moet altijd worden `Microsoft.Azure.CreateUIDef`en de meest recente ondersteunde versie is `0.1.2-preview`.

Het schema van de eigenschap para meters is afhankelijk van de combi natie van de opgegeven handler en versie. Voor beheerde toepassingen zijn de ondersteunde eigenschappen `basics`, `steps`en `outputs`. De eigenschappen van de basis beginselen en de stappen bevatten de [elementen](create-uidefinition-elements.md) , zoals tekst vakken en vervolg keuzelijsten, die moeten worden weer gegeven in de Azure Portal. De eigenschap outputs wordt gebruikt om de uitvoer waarden van de opgegeven elementen toe te wijzen aan de para meters van de Azure Resource Manager-implementatie sjabloon.

Inclusief `$schema` wordt aanbevolen, maar is optioneel. Indien opgegeven, moet de waarde voor `version` overeenkomen met de versie binnen de `$schema` URI.

U kunt een JSON-editor gebruiken om uw createUiDefinition te maken en deze vervolgens te testen in de [createUiDefinition-sandbox](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade) om deze te bekijken. Zie [uw portal-interface testen voor Azure Managed Applications](test-createuidefinition.md)voor meer informatie over de sandbox.

## <a name="basics"></a>Basisbeginselen

Basis beginselen is de eerste stap die wordt gegenereerd wanneer de Azure Portal het bestand parseert. Naast het weer geven van de elementen die zijn opgegeven in `basics`, injecteert de portal elementen voor gebruikers om het abonnement, de resource groep en de locatie voor de implementatie te kiezen. Als dat mogelijk is, moeten elementen die para meters voor implementatie query's uitvoeren, zoals de naam van een cluster of beheerders referenties, in deze stap gaan.

## <a name="steps"></a>Stappen

De eigenschap Steps kan nul of meer extra stappen bevatten om weer te geven na basis beginselen, die elk een of meer elementen bevatten. U kunt stappen toevoegen per rol of laag van de toepassing die wordt geïmplementeerd. Voeg bijvoorbeeld een stap toe voor invoer van hoofd knooppunten en een stap voor de worker-knoop punten in een cluster.

## <a name="outputs"></a>Uitvoer

De Azure Portal gebruikt de eigenschap `outputs` om elementen van `basics` en `steps` te koppelen aan de para meters van de Azure Resource Manager-implementatie sjabloon. De sleutels van deze woorden lijst zijn de namen van de sjabloon parameters en de waarden zijn eigenschappen van de uitvoer objecten van de elementen waarnaar wordt verwezen.

Als u de resource naam voor een beheerde toepassing wilt instellen, moet u een waarde met de naam `applicationResourceName` in de eigenschap outputs toevoegen. Als u deze waarde niet instelt, wijst de toepassing een GUID toe voor de naam. U kunt een tekstvak in de gebruikers interface toevoegen dat een naam aanvraagt bij de gebruiker.

```json
"outputs": {
    "vmName": "[steps('appSettings').vmName]",
    "trialOrProduction": "[steps('appSettings').trialOrProd]",
    "userName": "[steps('vmCredentials').adminUsername]",
    "pwd": "[steps('vmCredentials').vmPwd.password]",
    "applicationResourceName": "[steps('appSettings').vmName]"
}
```

## <a name="resource-types"></a>Resourcetypen

Als u de beschik bare locaties wilt filteren op alleen de locaties die ondersteuning bieden voor de resource typen die moeten worden geïmplementeerd, geeft u een matrix van de resource typen op. Als u meer dan één resource type opgeeft, worden alleen de locaties geretourneerd die alle resource typen ondersteunen. Deze eigenschap is optioneel.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Azure.CreateUIDef",
    "version": "0.1.2-preview",
    "parameters": {
      "resourceTypes": ["Microsoft.Compute/disks"],
      "basics": [
        ...
```  

## <a name="functions"></a>Functions

CreateUiDefinition biedt [functies](create-uidefinition-functions.md) voor het werken met de invoer en uitvoer van elementen en functies, zoals voor waarden. Deze functies zijn vergelijkbaar in zowel de syntaxis als de functionaliteit voor het Azure Resource Manager van sjabloon functies.

## <a name="next-steps"></a>Volgende stappen

Het bestand createUiDefinition. json heeft zelf een eenvoudig schema. De reële diepte van het is afkomstig van alle ondersteunde elementen en functies. Deze items worden in meer detail beschreven op:

- [Opties](create-uidefinition-elements.md)
- [Functies](create-uidefinition-functions.md)

Een huidig JSON-schema voor createUiDefinition is hier beschikbaar: https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json.

Zie [createUiDefinition. json](https://github.com/Azure/azure-managedapp-samples/blob/master/Managed%20Application%20Sample%20Packages/201-managed-app-using-existing-vnet/createUiDefinition.json)voor een voor beeld van een gebruikers interface bestand.
