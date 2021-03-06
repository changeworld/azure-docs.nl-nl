---
title: Machtigingen voor opslag plaatsen in Azure Container Registry
description: Een token maken met machtigingen die zijn gericht op specifieke opslag plaatsen in een REGI ster om installatie kopieën op te halen of te pushen of om andere acties uit te voeren
ms.topic: article
ms.date: 02/13/2020
ms.openlocfilehash: 7d390bf4d97561e374c70f184534ac4f98a40611
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444280"
---
# <a name="create-a-token-with-repository-scoped-permissions"></a>Een token met machtigingen voor een opslag plaats maken

In dit artikel wordt beschreven hoe u tokens en Scope toewijzingen maakt om machtigingen voor opslag in het container register te beheren. Door tokens te maken, kan een register eigenaar gebruikers of Services voorzien van scoped, beperkte toegang tot opslag plaatsen om installatie kopieën te halen of te pushen of andere acties uit te voeren. Een token biedt meer verfijnde machtigingen dan andere opties voor het [instellen](container-registry-authentication.md)van het REGI ster, die de bereik machtigingen voor een volledig REGI ster hebben. 

Scenario's voor het maken van een token zijn onder andere:

* IoT-apparaten met afzonderlijke tokens toestaan een installatie kopie van een opslag plaats te halen
* Een externe organisatie voorzien van machtigingen voor een specifieke opslag plaats 
* Beperk de toegang tot opslag plaatsen tot verschillende gebruikers groepen in uw organisatie. U kunt bijvoorbeeld schrijf-en lees toegang bieden aan ontwikkel aars die installatie kopieën bouwen die gericht zijn op specifieke opslag plaatsen en lees toegang tot teams die vanuit deze opslag plaatsen worden geïmplementeerd.

> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als preview-versie en er [zijn enkele beperkingen van toepassing](#preview-limitations). Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden][terms-of-use]. Sommige aspecten van deze functie worden mogelijk nog gewijzigd voordat de functie algemeen beschikbaar wordt.

## <a name="preview-limitations"></a>Preview-beperkingen

* Deze functie is alleen beschikbaar in een **Premium** container Registry. Zie [Azure container Registry sku's](container-registry-skus.md)voor meer informatie over de service lagen en limieten voor het REGI ster.
* U kunt momenteel geen machtigingen voor opslag plaatsen toewijzen aan een Azure Active Directory identiteit, zoals een service-principal of beheerde identiteit.

## <a name="concepts"></a>Concepten

Als u machtigingen met een opslag plaats met een bereik wilt configureren, maakt u een *token* met een bijbehorende *bereik toewijzing*. 

* Met een **token** in combi natie met een gegenereerd wacht woord kan de gebruiker worden geverifieerd met het REGI ster. U kunt een verval datum instellen voor een token wachtwoord of een token op elk gewenst moment uitschakelen.  

  Na verificatie met een token kan de gebruiker of service een of meer *acties* uitvoeren die zijn gericht op een of meer opslag plaatsen.

  |Bewerking  |Beschrijving  | Voorbeeld |
  |---------|---------|--------|
  |`content/delete`    | Gegevens uit de opslag plaats verwijderen  | Een opslag plaats of een manifest verwijderen |
  |`content/read`     |  Gegevens uit de opslag plaats lezen |  Een artefact ophalen |
  |`content/write`     |  Gegevens naar de opslag plaats schrijven     | Gebruik met `content/read` om een artefact te pushen |
  |`metadata/read`    | Meta gegevens uit de opslag plaats lezen   | Tags of manifesten weer geven |
  |`metadata/write`     |  Meta gegevens naar de opslag plaats schrijven  | Lees-, schrijf-of verwijder bewerkingen in-of uitschakelen |

* Een **Scope toewijzing** groepeert de machtigingen voor de opslag plaats die u toepast op een token en kan opnieuw worden toegepast op andere tokens. Elk token is gekoppeld aan één scope-toewijzing. 

   Met een bereik toewijzing:

    * Meerdere tokens met identieke machtigingen configureren voor een set opslag plaatsen
    * Token machtigingen bijwerken bij het toevoegen of verwijderen van opslagplaats acties in de scope toewijzing of het Toep assen van een andere scope toewijzing 

  Azure Container Registry biedt ook verschillende door het systeem gedefinieerde bereik toewijzingen die u kunt Toep assen, met vaste machtigingen voor alle opslag plaatsen.

In de volgende afbeelding ziet u de relatie tussen tokens en Scope Maps. 

![Register tokens en Scope Maps](media/container-registry-repository-scoped-permissions/token-scope-map-concepts.png)

## <a name="prerequisites"></a>Vereisten

* **Azure cli** -Azure cli-opdrachten voor het maken en beheren van tokens zijn beschikbaar in azure CLI versie 2.0.76 of hoger. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.
* **Docker** : u hebt een lokale docker-installatie nodig om te verifiëren met het REGI ster om installatie kopieën op te halen of te pushen. Docker biedt installatie-instructies voor de systemen [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) en [Linux](https://docs.docker.com/engine/installation/#supported-platforms).
* **Container register** : als u er nog geen hebt, maakt u een Premium container registry in uw Azure-abonnement of werkt u een bestaand REGI ster bij. Gebruik bijvoorbeeld de [Azure Portal](container-registry-get-started-portal.md) of de [Azure cli](container-registry-get-started-azure-cli.md). 

## <a name="create-token---cli"></a>Token maken-CLI

### <a name="create-token-and-specify-repositories"></a>Token maken en opslag plaatsen opgeven

Maak een token met behulp van de opdracht [AZ ACR token Create][az-acr-token-create] . Wanneer u een token maakt, kunt u een of meer opslag plaatsen en gekoppelde acties op elke opslag plaats opgeven. De opslag plaatsen hoeven nog niet in het REGI ster te staan. Zie de volgende sectie voor het maken van een token door een bestaande scope toewijzing op te geven.

In het volgende voor beeld wordt een token gemaakt in het REGI ster *myregistry* met de volgende machtigingen voor de `samples/hello-world` opslag plaats: `content/write` en `content/read`. Standaard wordt met de opdracht de standaard token status ingesteld op `enabled`, maar u kunt de status op elk gewenst moment bijwerken naar `disabled`.

```azurecli
az acr token create --name MyToken --registry myregistry \
  --repository samples/hello-world \
  content/write content/read
```

De uitvoer toont details over het token, met inbegrip van twee gegenereerde wacht woorden. Het is raadzaam om de wacht woorden op een veilige plek op te slaan, zodat u deze later kunt gebruiken voor verificatie. Het wacht woord kan niet opnieuw worden opgehaald, maar er kunnen nieuwe worden gegenereerd.

```console
{
  "creationDate": "2020-01-18T00:15:34.066221+00:00",
  "credentials": {
    "certificates": [],
    "passwords": [
      {
        "creationTime": "2020-01-18T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password1",
        "value": "uH54BxxxxK7KOxxxxRbr26dAs8JXxxxx"
      },
      {
        "creationTime": "2020-01-18T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password2",
        "value": "kPX6Or/xxxxLXpqowxxxxkA0idwLtmxxxx"
      }
    ],
    "username": "MyToken"
  },
  "id": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/tokens/MyToken",
  "name": "MyToken",
  "objectId": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "scopeMapId": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/scopeMaps/MyToken-scope-map",
  "status": "enabled",
  "type": "Microsoft.ContainerRegistry/registries/tokens"
```

De uitvoer bevat details over het bereik dat is gemaakt met de opdracht. U kunt de scope toewijzing, hier met de naam `MyToken-scope-map`, gebruiken om dezelfde opslagplaats acties op andere tokens toe te passen. Of werk de scope toewijzing later bij om de machtigingen van de gekoppelde tokens te wijzigen.

### <a name="create-token-and-specify-scope-map"></a>Token maken en bereik toewijzing opgeven

Een andere manier om een token te maken, is door een bestaande scope toewijzing op te geven. Als u nog geen scope toewijzing hebt, moet u er een maken door opslag plaatsen en gekoppelde acties op te geven. Geef vervolgens de scope toewijzing op bij het maken van een token. 

Als u een scope toewijzing wilt maken, gebruikt u de opdracht [AZ ACR Scope-map Create][az-acr-scope-map-create] . Met de volgende opdracht maakt u een scope toewijzing met dezelfde machtigingen voor de `samples/hello-world`-opslag plaats die u eerder hebt gebruikt. 

```azurecli
az acr scope-map create --name MyScopeMap --registry myregistry \
  --repository samples/hello-world \
  content/write content/read \
  --description "Sample scope map"
```

Voer [AZ ACR token Create][az-acr-token-create] uit om een token te maken, waarbij u de *MyScopeMap* -bereik toewijzing opgeeft. Net als in het vorige voor beeld wordt met de opdracht de standaard token status ingesteld op `enabled`.

```azurecli
az acr token create --name MyToken \
  --registry myregistry \
  --scope-map MyScopeMap
```

De uitvoer toont details over het token, met inbegrip van twee gegenereerde wacht woorden. Het is raadzaam om de wacht woorden op een veilige plek op te slaan, zodat u deze later kunt gebruiken voor verificatie. Het wacht woord kan niet opnieuw worden opgehaald, maar er kunnen nieuwe worden gegenereerd.

## <a name="create-token---portal"></a>Token maken-Portal

U kunt de Azure Portal gebruiken om tokens en Scope Maps te maken. Net als bij de `az acr token create` CLI-opdracht kunt u een bestaande bereik toewijzing Toep assen of een scope toewijzing maken wanneer u een token maakt door een of meer opslag plaatsen en gekoppelde acties op te geven. De opslag plaatsen hoeven nog niet in het REGI ster te staan. 

In het volgende voor beeld wordt een token gemaakt en wordt een scope toewijzing gemaakt met de volgende machtigingen voor de `samples/hello-world` opslagplaats: `content/write` en `content/read`.

1. Navigeer in de portal naar het container register.
1. Onder **Services**selecteert u **tokens (preview) > + toevoegen**.
  token ![maken in de portal](media/container-registry-repository-scoped-permissions/portal-token-add.png)
1. Voer een token naam in.
1. Selecteer onder **Scope toewijzing**de optie **nieuwe maken**.
1. De scope toewijzing configureren:
    1. Voer een naam en beschrijving in voor de bereik toewijzing. 
    1. Onder **opslag**plaatsen voert u `samples/hello-world`in en selecteert u onder **machtigingen**`content/read` en `content/write`. Selecteer vervolgens **+ toevoegen**.  
    ![Scope toewijzing maken in de portal](media/container-registry-repository-scoped-permissions/portal-scope-map-add.png)

    1. Nadat u opslag plaatsen en machtigingen hebt toegevoegd, selecteert u **toevoegen** om de scope toewijzing toe te voegen.
1. Accepteer de standaard token **status** **ingeschakeld** en selecteer vervolgens **maken**.

Nadat het token is gevalideerd en gemaakt, worden de token details weer gegeven in het scherm **tokens** .

### <a name="add-token-password"></a>Token wachtwoord toevoegen

Genereer een wacht woord nadat u een token hebt gemaakt. Het token moet zijn ingeschakeld en een geldig wacht woord hebben om te kunnen worden geverifieerd met het REGI ster.

U kunt één of twee wacht woorden genereren en een verval datum instellen voor elke groep. 

1. Navigeer in de portal naar het container register.
1. Onder **Services**selecteert u **tokens (preview)** en selecteert u een token.
1. Selecteer in de details van het token **Wachtwoord1** of **password2**en selecteer het pictogram genereren.
1. In het scherm wacht woord stelt u desgewenst een verval datum voor het wacht woord in en selecteert u **genereren**.
1. Nadat u een wacht woord hebt gegenereerd, kopieert u het naar een veilige locatie en slaat u deze op. U kunt een gegenereerd wacht woord niet ophalen nadat het scherm is gesloten, maar er kan wel een nieuwe worden gegenereerd.

    ![Token wachtwoord maken in de portal](media/container-registry-repository-scoped-permissions/portal-token-password.png)

## <a name="authenticate-with-token"></a>Verifiëren met token

Wanneer een gebruiker of service een token gebruikt voor verificatie met het doel register, geeft het de token naam als een gebruikers naam en een van de gegenereerde wacht woorden. De verificatie methode is afhankelijk van de geconfigureerde actie of acties die zijn gekoppeld aan het token.

|Bewerking  |Verificatie uitvoeren  |
  |---------|---------|
  |`content/delete`    | `az acr repository delete` in azure CLI |
  |`content/read`     |  `docker login`<br/><br/>`az acr login` in azure CLI  |
  |`content/write`     |  `docker login`<br/><br/>`az acr login` in azure CLI     |
  |`metadata/read`    | `az acr repository show`<br/><br/>`az acr repository show-tags`<br/><br/>`az acr repository show-manifests` in azure CLI   |
  |`metadata/write`     |  `az acr repository untag`<br/><br/>`az acr repository update` in azure CLI |

## <a name="examples-use-token"></a>Voor beelden: token gebruiken

In de volgende voor beelden wordt het token gebruikt dat eerder in dit artikel is gemaakt om algemene bewerkingen uit te voeren op een opslag plaats: push-en pull-installatie kopieën, verwijderen van afbeeldingen en List opslagplaats Tags. Het token is in eerste instantie ingesteld met push-machtigingen (`content/write` en `content/read` acties) in de `samples/hello-world` opslag plaats.

### <a name="pull-and-tag-test-images"></a>Test installatie kopieën voor pull en Tags

Voor de volgende voor beelden haalt u de `hello-world`-en `alpine`-installatie kopieën uit docker hub op en labelt u deze voor uw REGI ster en opslag plaats.

```bash
docker pull hello-world
docker pull alpine
docker tag hello-world myregistry.azurecr.io/samples/hello-world:v1
docker tag hello-world myregistry.azurecr.io/samples/alpine:v1
```

### <a name="authenticate-using-token"></a>Verifiëren met token

Voer `docker login` uit om te verifiëren met het REGI ster, geef de token naam op als gebruikers naam en geef een van de wacht woorden op. Het token moet de `Enabled` status hebben.

Het volgende voor beeld is opgemaakt voor de bash-shell en biedt de waarden met behulp van omgevings variabelen.

```bash
TOKEN_NAME=MyToken
TOKEN_PWD=<token password>

echo $TOKEN_PWD | docker login --username $TOKEN_NAME --password-stdin myregistry.azurecr.io
```

Bij uitvoer moet de verificatie slagen worden weer gegeven:

```console
Login Succeeded
```

### <a name="push-images-to-registry"></a>Installatiekopieën naar het register pushen

Na een geslaagde aanmelding, probeert u de gelabelde installatie kopieën naar het REGI ster te pushen. Omdat het token machtigingen heeft om installatie kopieën naar de `samples/hello-world`-opslag plaats te pushen, lukt de volgende push:

```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

Het token heeft geen machtigingen voor de `samples/alpine` opslag plaats, waardoor de volgende push poging mislukt met een fout die vergelijkbaar is met `requested access to the resource is denied`:

```bash
docker push myregistry.azurecr.io/samples/alpine:v1
```

### <a name="change-pushpull-permissions"></a>Push/pull-machtigingen wijzigen

Als u de machtigingen van een token wilt bijwerken, werkt u de machtigingen bij in de bijbehorende toewijzing van het bereik. De bijgewerkte Scope toewijzing wordt direct toegepast op alle bijbehorende tokens. 

U kunt bijvoorbeeld `MyToken-scope-map` bijwerken met `content/write` en `content/read` acties in de `samples/alpine` opslag plaats en de `content/write` actie verwijderen voor de `samples/hello-world` opslag plaats.  

Als u de Azure CLI wilt gebruiken, voert u [AZ ACR Scope-map Update][az-acr-scope-map-update] uit om de scope toewijzing bij te werken:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/alpine content/write content/read \
  --remove samples/hello-world content/write 
```

In Azure Portal:

1. Navigeer naar het container register.
1. Onder **Services**selecteert u **Scope toewijzingen (preview)** en selecteert u de scope toewijzing die u wilt bijwerken.
1. Onder **opslag**plaatsen voert u `samples/alpine`in en selecteert u onder **machtigingen**`content/read` en `content/write`. Selecteer vervolgens **+ toevoegen**.
1. Onder **opslag**plaatsen selecteert u `samples/hello-world` en selecteert u onder **machtigingen**de optie `content/write`. Selecteer vervolgens **Opslaan**.

Nadat de scope toewijzing is bijgewerkt, is de volgende push geslaagd:

```bash
docker push myregistry.azurecr.io/samples/alpine:v1
```

Omdat de scope toewijzing alleen de machtiging `content/read` heeft voor de `samples/hello-world` opslag plaats, mislukt een push poging naar de `samples/hello-world` opslag plaats nu:
 
```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

Het ophalen van installatie kopieën van beide opslag plaatsen slaagt, omdat de scope toewijzing `content/read` machtigingen biedt voor beide opslag plaatsen:

```bash
docker pull myregistry.azurecr.io/samples/alpine:v1
docker pull myregistry.azurecr.io/samples/hello-world:v1
```
### <a name="delete-images"></a>Installatiekopieën verwijderen

Werk de scope toewijzing bij door de actie `content/delete` toe te voegen aan de `alpine`-opslag plaats. Met deze actie kunnen installatie kopieën in de opslag plaats worden verwijderd of kan de hele opslag plaats worden verwijderd.

Voor een beknopt overzicht worden alleen de opdracht [AZ ACR Scope-map Update][az-acr-scope-map-update] weer gegeven om de scope toewijzing bij te werken:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/alpine content/delete
``` 

Als u de scope toewijzing wilt bijwerken met behulp van de portal, raadpleegt u de voor gaande sectie.

Gebruik de volgende opdracht [AZ ACR repository delete][az-acr-repository-delete] om de `samples/alpine` opslag plaats te verwijderen. Als u installatie kopieën of opslag plaatsen wilt verwijderen, wordt het token niet geverifieerd via `docker login`. Geef in plaats daarvan de naam en het wacht woord van het token door aan de opdracht. In het volgende voor beeld worden de omgevings variabelen gebruikt die eerder in het artikel zijn gemaakt:

```azurecli
az acr repository delete \
  --name myregistry --repository samples/alpine \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

### <a name="show-repo-tags"></a>Opslag plaats-Tags weer geven 

Werk de scope toewijzing bij door de actie `metadata/read` toe te voegen aan de `hello-world`-opslag plaats. Met deze actie kunnen manifest-en label gegevens in de opslag plaats worden gelezen.

Voor een beknopt overzicht worden alleen de opdracht [AZ ACR Scope-map Update][az-acr-scope-map-update] weer gegeven om de scope toewijzing bij te werken:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/hello-world metadata/read 
```  

Als u de scope toewijzing wilt bijwerken met behulp van de portal, raadpleegt u de voor gaande sectie.

Als u meta gegevens in de `samples/hello-world`-opslag plaats wilt lezen, voert u de opdracht [AZ ACR repository show-manifests][az-acr-repository-show-manifests] of [AZ ACR repository show-Tags][az-acr-repository-show-tags] uit. 

Voor het lezen van meta gegevens kan het token niet worden geverifieerd via `docker login`. Geef in plaats daarvan de naam en het wacht woord van het token door aan een van de opdrachten. In het volgende voor beeld worden de omgevings variabelen gebruikt die eerder in het artikel zijn gemaakt:

```azurecli
az acr repository show-tags \
  --name myregistry --repository samples/hello-world \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

Voorbeelduitvoer:

```console
[
  "v1"
]
```
## <a name="manage-tokens-and-scope-maps"></a>Tokens en Scope toewijzingen beheren

### <a name="list-scope-maps"></a>Lijst met bereik toewijzingen

Gebruik de opdracht [AZ ACR Scope-map list][az-acr-scope-map-list] of het scherm **Scope Maps (preview)** in de portal om een lijst weer te geven met alle scope toewijzingen die in een REGI ster zijn geconfigureerd. Bijvoorbeeld:

```azurecli
az acr scope-map list \
  --registry myregistry --output table
```

In de uitvoer ziet u de bereik toewijzingen die u hebt gedefinieerd en enkele door het systeem gedefinieerde bereik toewijzingen die u kunt gebruiken om tokens te configureren:

```
NAME                 TYPE           CREATION DATE         DESCRIPTION
-------------------  -------------  --------------------  ------------------------------------------------------------
_repositories_admin  SystemDefined  2020-01-20T09:44:24Z  Can perform all read, write and delete operations on the ...
_repositories_pull   SystemDefined  2020-01-20T09:44:24Z  Can pull any repository of the registry
_repositories_push   SystemDefined  2020-01-20T09:44:24Z  Can push to any repository of the registry
MyScopeMap           UserDefined    2019-11-15T21:17:34Z  Sample scope map
```

### <a name="show-token-details"></a>Details van token weer geven

Als u de details van een token, zoals de status en de verval datum van het wacht woord, wilt weer geven, voert u de opdracht [AZ ACR token show][az-acr-token-show] uit of selecteert u het token in het scherm **tokens (preview)** in de portal. Bijvoorbeeld:

```azurecli
az acr scope-map show \
  --name MyScopeMap --registry myregistry
```

Gebruik de opdracht [AZ ACR token List][az-acr-token-list] of het scherm **tokens (preview)** in de portal om een lijst weer te geven met alle tokens die in een REGI ster zijn geconfigureerd. Bijvoorbeeld:

```azurecli
az acr token list --registry myregistry --output table
```

### <a name="generate-passwords-for-token"></a>Wacht woorden genereren voor token

Als u geen token wachtwoord hebt of als u nieuwe wacht woorden wilt genereren, voert u de opdracht [AZ ACR token Credential generate][az-acr-token-credential-generate] uit. 

In het volgende voor beeld wordt een nieuwe waarde voor Wachtwoord1 voor het *MyToken* -token gegenereerd, met een verval periode van 30 dagen. Het wacht woord wordt opgeslagen in de omgevings variabele `TOKEN_PWD`. Dit voor beeld is opgemaakt voor de bash-shell.

```azurecli
TOKEN_PWD=$(az acr token credential generate \
  --name MyToken --registry myregistry --days 30 \
  --password1 --query 'passwords[0].value' --output tsv)
```

Als u de Azure Portal wilt gebruiken om een token wachtwoord te genereren, raadpleegt u de stappen in [Create token-Portal](#create-token---portal) eerder in dit artikel.

### <a name="update-token-with-new-scope-map"></a>Token bijwerken met een nieuwe scope toewijzing

Als u een token wilt bijwerken met een andere scope toewijzing, voert u [AZ ACR token update][az-acr-token-update] uit en geeft u de nieuwe scope toewijzing op. Bijvoorbeeld:

```azurecli
az acr token update --name MyToken --registry myregistry \
  --scope-map MyNewScopeMap
```

Selecteer in de portal op het scherm **tokens (voor beeld)** het token en selecteer een andere scope toewijzing onder **Scope toewijzing**.

> [!TIP]
> Nadat u een token met een nieuwe scope toewijzing hebt bijgewerkt, wilt u mogelijk nieuwe token wachtwoorden genereren. Gebruik de opdracht [AZ ACR token Credential generate][az-acr-token-credential-generate] of Genereer een token wachtwoord in de Azure Portal.

## <a name="disable-or-delete-token"></a>Token uitschakelen of verwijderen

Mogelijk moet u het gebruik van de token referenties voor een gebruiker of service tijdelijk uitschakelen. 

Voer de opdracht [AZ ACR token update][az-acr-token-update] uit met behulp van de Azure CLI om de `status` in te stellen op `disabled`:

```azurecli
az acr token update --name MyToken --registry myregistry \
  --status disabled
```

Selecteer in de portal het token in het scherm **tokens (voor beeld)** en selecteer **uitgeschakeld** onder **status**.

Als u een token wilt verwijderen om de toegang door iedereen die de referenties gebruikt permanent ongeldig te maken, voert u de opdracht [AZ ACR token delete][az-acr-token-delete] uit. 

```azurecli
az acr token delete --name MyToken --registry myregistry
```

Selecteer in de portal het token in het scherm **tokens (voor beeld)** en selecteer **verwijderen**.

## <a name="next-steps"></a>Volgende stappen

* Als u Scope toewijzingen en tokens wilt beheren, gebruikt u aanvullende opdrachten in de opdracht [AZ ACR Scope-map][az-acr-scope-map] en [AZ ACR token][az-acr-token] Command groups.
* Zie het [verificatie overzicht](container-registry-authentication.md) voor andere opties voor verificatie met een Azure container Registry, inclusief het gebruik van een Azure Active Directory identiteit, een service-principal of een beheerders account.


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-repository]: /cli/azure/acr/repository/
[az-acr-repository-show-tags]: /cli/azure/acr/repository/#az-acr-repository-show-tags
[az-acr-repository-show-manifests]: /cli/azure/acr/repository/#az-acr-repository-show-manifests
[az-acr-repository-delete]: /cli/azure/acr/repository/#az-acr-repository-delete
[az-acr-scope-map]: /cli/azure/acr/scope-map/
[az-acr-scope-map-create]: /cli/azure/acr/scope-map/#az-acr-scope-map-create
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-show
[az-acr-scope-map-show]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-scope-map-update]: /cli/azure/acr/scope-map/#az-acr-scope-map-update
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-token]: /cli/azure/acr/token/
[az-acr-token-show]: /cli/azure/acr/token/#az-acr-token-show
[az-acr-token-list]: /cli/azure/acr/token/#az-acr-token-list
[az-acr-token-delete]: /cli/azure/acr/token/#az-acr-token-delete
[az-acr-token-create]: /cli/azure/acr/token/#az-acr-token-create
[az-acr-token-update]: /cli/azure/acr/token/#az-acr-token-update
[az-acr-token-credential-generate]: /cli/azure/acr/token/credential/#az-acr-token-credential-generate
