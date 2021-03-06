---
title: Azure lente-Cloud verifiëren met Key Vault in GitHub-acties
description: Sleutel kluis met CI/CD-werk stroom gebruiken voor Azure lente-Cloud met GitHub-acties
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/20/2019
ms.openlocfilehash: 78cd5945e394219be0551bbe97afef07f18b61f7
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2020
ms.locfileid: "78945471"
---
# <a name="authenticate-azure-spring-cloud-with-key-vault-in-github-actions"></a>Azure lente-Cloud verifiëren met Key Vault in GitHub-acties
Sleutel kluis is een veilige plaats voor het opslaan van sleutels. Zakelijke gebruikers moeten referenties opslaan voor CI/CD-omgevingen binnen het bereik dat ze beheren. De sleutel voor het ophalen van referenties in de sleutel kluis moet beperkt zijn tot het bron bereik.  Het heeft alleen toegang tot het sleutel kluis bereik, niet voor het hele Azure-bereik. Het lijkt erop dat een sleutel die alleen een sterk vak kan openen, geen hoofd sleutel is die alle deuren in een gebouw kan openen. Het is een manier om een sleutel te verkrijgen met een andere sleutel. Dit is handig in een CICD-werk stroom. 

## <a name="generate-credential"></a>Referentie genereren
Voor het genereren van een sleutel voor toegang tot de sleutel kluis voert u de volgende opdracht uit op de lokale computer:
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.KeyVault/vaults/<KEY_VAULT> --sdk-auth
```
Met het bereik dat is opgegeven met de para meter `--scopes` wordt de sleutel toegang tot de resource beperkt.  Het kan alleen toegang krijgen tot het sterke vak.

Met resultaten:
```
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
}
```
Sla de resultaten vervolgens op in GitHub **geheimen** zoals beschreven in [uw github-opslag plaats instellen en verifiëren met Azure](./spring-cloud-howto-github-actions.md#set-up-github-repository-and-authenticate).

## <a name="add-access-policies-for-the-credential"></a>Toegangs beleid voor de referentie toevoegen
Met de referentie die u hierboven hebt gemaakt, krijgt u alleen algemene informatie over de Key Vault, niet de inhoud die wordt opgeslagen.  Als u geheimen wilt ophalen die zijn opgeslagen in de Key Vault, moet u toegangs beleid instellen voor de referentie.

Ga naar het **Key Vault** dash board in azure Portal, klik op het **toegangs beheer** menu en open vervolgens het tabblad **roltoewijzingen** . Selecteer **apps** voor **type** en `This resource` voor **bereik**.  De referenties die u in de vorige stap hebt gemaakt, worden weer geven:

 ![Toegangs beleid instellen](./media/github-actions/key-vault1.png)

Kopieer de referentie naam, bijvoorbeeld `azure-cli-2020-01-19-04-39-02`. Open het menu **toegangs beleid** en klik op koppeling naar **toegangs beleid toevoegen** .  Selecteer `Secret Management` voor **sjabloon**en selecteer vervolgens **Principal**. Plak de referentie naam in de **Principal**/**Selecteer** invoervak:

 ![Selecteer](./media/github-actions/key-vault2.png)

 Klik op de knop **toevoegen** in het dialoog venster **toegangs beleid toevoegen** en klik vervolgens op **Opslaan**.

## <a name="generate-full-scope-azure-credential"></a>Azure-referentie met een volledige bereik genereren
Dit is de hoofd sleutel voor het openen van alle deuren in het gebouw. De procedure is vergelijkbaar met de vorige stap, maar hier wijzigen we het bereik voor het genereren van de hoofd sleutel:

```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth
```

Opnieuw, resultaten:
```
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
}
```
Kopieer de volledige JSON-teken reeks.  Bo terug naar **Key Vault** dash board. Open het menu **geheimen** en klik vervolgens op de knop **genereren/importeren** . Voer de geheime naam in, zoals `AZURE-CRENDENTIALS-FOR-SPRING`. Plak de JSON-referentie teken reeks in het invoervak **waarde** . U ziet het invoervak waarde is een tekst veld met één regel in plaats van een tekst gebied met meerdere regels.  U kunt de volledige JSON-teken reeks plakken.

 ![Volledige bereik referentie](./media/github-actions/key-vault3.png)

## <a name="combine-credentials-in-github-actions"></a>Referenties combi neren in GitHub acties
Stel de referenties in die worden gebruikt wanneer de CICD-pijp lijn wordt uitgevoerd:

```
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}           # Strong box key you generated in the first step
    - uses: Azure/get-keyvault-secrets@v1.0
      with:
        keyvault: "zlhe-test"
        secrets: "AZURE-CREDENTIALS-FOR-SPRING"           # Master key to open all doors in the building
      id: keyvaultaction
    - uses: azure/login@v1
      with:
        creds: ${{ steps.keyvaultaction.outputs.AZURE-CREDENTIALS-FOR-SPRING }}
    - name: Azure CLI script
      uses: azure/CLI@v1
      with:
        azcliversion: 2.0.75
        inlineScript: |
          az extension add --name spring-cloud             # Spring CLI commands from here
          az spring-cloud list

```

## <a name="next-steps"></a>Volgende stappen
* [GitHub acties voor de lente in de Cloud](./spring-cloud-howto-github-actions.md)