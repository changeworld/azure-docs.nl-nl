---
title: Veelgestelde vragen over het uitvoeren van ingebouwde containers
description: Vind antwoorden op veelgestelde vragen over de ingebouwde Linux-containers in Azure App Service.
keywords: Azure app service, Web-app, veelgestelde vragen, Linux, OSS, Web-app voor containers, multi-container, meerdere containers
author: msangapu-msft
ms.topic: article
ms.date: 10/30/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 2413601db629fda62976b75e349b0340749dc6fa
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2020
ms.locfileid: "78944076"
---
# <a name="azure-app-service-on-linux-faq"></a>Veelgestelde vragen over Azure App Service in Linux

Met de release van App Service in Linux werken we aan het toevoegen van functies en het maken van verbeteringen aan ons platform. In dit artikel vindt u antwoorden op vragen die onze klanten onlangs hebben ingediend.

Als u een vraag hebt, moet u een opmerking over dit artikel toevoegen.

## <a name="built-in-images"></a>Ingebouwde installatie kopieën

**Ik wil de ingebouwde docker-containers die het platform levert splitsen. Waar vind ik die bestanden?**

U kunt alle docker-bestanden vinden op [github](https://github.com/azure-app-service). U kunt alle docker-containers vinden op [docker hub](https://hub.docker.com/u/appsvc/).

<a id="#startup-file"></a>

**Wat zijn de verwachte waarden voor de sectie opstart bestand bij het configureren van de runtime stack?**

| Stack           | Verwachte waarde                                                                         |
|-----------------|----------------------------------------------------------------------------------------|
| Java SE         | de opdracht voor het starten van uw JAR-app (bijvoorbeeld `java -jar /home/site/wwwroot/app.jar --server.port=80`) |
| Tomcat          | de locatie van een script om de benodigde configuraties uit te voeren (bijvoorbeeld `/home/site/deployments/tools/startup_script.sh`)          |
| Node.js         | het PM2-configuratie bestand of het script bestand                                |
| .Net core       | de gecompileerde DLL-naam als `dotnet <myapp>.dll`                                 |
| Ruby            | het ruby-script dat u uw app wilt initialiseren met                     |

Deze opdrachten of scripts worden uitgevoerd nadat de ingebouwde docker-container is gestart, maar voordat de code van de toepassing wordt gestart.

## <a name="management"></a>Beheer

**Wat gebeurt er wanneer ik op de knop opnieuw opstarten Klik in het Azure Portal?**

Deze actie is hetzelfde als het opnieuw opstarten van een docker.

**Kan ik een Secure Shell (SSH) gebruiken om verbinding te maken met de virtuele machine (VM) van de app-container?**

Ja, u kunt dat doen via de SCM-site (broncode Control Management).

> [!NOTE]
> U kunt ook rechtstreeks vanaf uw lokale ontwikkelcomputer verbinding maken met de app-container via SSH, SFTP of Visual Studio Code (voor live foutopsporing gebruikt u Node.js-apps). Zie [Foutopsporing op afstand en SSH in App Service in Linux](https://azure.github.io/AppService/2018/05/07/New-SSH-Experience-and-Remote-Debugging-for-Linux-Web-Apps.html) voor meer informatie.
>

**Hoe kan ik een Linux-App Service plan maken via een SDK of een Azure Resource Manager sjabloon?**

Stel het **gereserveerde** veld van de app-service in op *waar*.

## <a name="continuous-integration-and-deployment"></a>Continue integratie en implementatie

**Mijn web-app maakt nog steeds gebruik van een oude docker-container installatie kopie nadat ik de installatie kopie op docker hub heb bijgewerkt. Biedt ondersteuning voor continue integratie en implementatie van aangepaste containers?**

Ja, om doorlopende integratie/implementatie in te stellen voor Azure Container Registry of DockerHub, door [doorlopende implementatie met Web App for containers](./app-service-linux-ci-cd.md). Voor persoonlijke registers kunt u de container vernieuwen door de web-app te stoppen en vervolgens te starten. U kunt ook een dummy-toepassings instelling wijzigen of toevoegen om het vernieuwen van de container af te dwingen.

**Biedt ondersteuning voor faserings omgevingen?**

Ja.

**Kan ik *webdeploy/MSDeploy* gebruiken om mijn web-app te implementeren?**

Ja, u moet een app-instelling met de naam `WEBSITE_WEBDEPLOY_USE_SCM` instellen op *Onwaar*.

**Git-implementatie van mijn toepassing mislukt wanneer Linux-web-app wordt gebruikt. Hoe kan ik het probleem omzeilen?**

Als de Git-implementatie niet kan worden uitgevoerd in uw Linux-web-app, kiest u een van de volgende opties om de toepassings code te implementeren:

- Gebruik de functie continue levering (preview): u kunt de bron code van uw app opslaan in een Azure DevOps Git opslag plaats of GitHub opslag plaats om Azure Continuous Delivery te gebruiken. Zie [continue levering configureren voor Linux-web-apps](https://blogs.msdn.microsoft.com/devops/2017/05/10/use-azure-portal-to-setup-continuous-delivery-for-web-app-on-linux/)voor meer informatie.

- De [API voor zip-implementatie](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)gebruiken: als u deze API wilt gebruiken, [ssh in uw web-app](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-ssh-support) en gaat u naar de map waar u de code wilt implementeren. Voer de volgende code:

   ```bash
   curl -X POST -u <user> --data-binary @<zipfile> https://{your-sitename}.scm.azurewebsites.net/api/zipdeploy
   ```

   Als er een fout bericht wordt weer gegeven dat de `curl` opdracht niet wordt gevonden, moet u een krul installeren met behulp van `apt-get install curl` voordat u de vorige `curl` opdracht uitvoert.

## <a name="language-support"></a>Taalondersteuning

**Ik wil Web Sockets gebruiken in mijn node. js-toepassing, alle speciale instellingen of configuraties die moeten worden ingesteld?**

Ja, `perMessageDeflate` uitschakelen in uw node. js-code aan de server zijde. Als u bijvoorbeeld socket.io gebruikt, gebruikt u de volgende code:

```nodejs
const io = require('socket.io')(server,{
  perMessageDeflate :false
});
```

**Worden niet-gecompileerde .NET Core-Apps ondersteund?**

Ja.

**Biedt u ondersteuning voor componist als afhankelijkheids Manager voor PHP-apps?**

Ja, tijdens een Git-implementatie moet kudu detecteert dat u een PHP-toepassing implementeert (hartelijk dank voor de aanwezigheid van een Composer-bestand). vervolgens wordt door kudu een Composer installatie geactiveerd.

## <a name="custom-containers"></a>Aangepaste containers

**Ik gebruik mijn eigen aangepaste container. Ik wil dat het platform een SMB-share koppelt aan de `/home/` Directory.**

Als `WEBSITES_ENABLE_APP_SERVICE_STORAGE` instelling niet is **opgegeven** of is ingesteld op *waar*, wordt de `/home/` Directory **gedeeld** over schaal instanties en worden de bestanden die zijn geschreven, **bewaard** tijdens het opnieuw opstarten. Als `WEBSITES_ENABLE_APP_SERVICE_STORAGE` expliciet wordt ingesteld op *False* , wordt de koppeling uitgeschakeld.

**Het starten van mijn aangepaste container duurt lang en het platform start de container opnieuw op voordat deze is gestart.**

U kunt de hoeveelheid tijd configureren die het platform moet wachten voordat de container opnieuw wordt opgestart. Als u dit wilt doen, stelt u de `WEBSITES_CONTAINER_START_TIME_LIMIT` app-instelling in op de gewenste waarde. De standaard waarde is 230 seconden en de maximum waarde is 1800 seconden.

**Wat is de indeling voor de URL van de persoonlijke register server?**

Geef de volledige register-URL op, inclusief `http://` of `https://`.

**Wat is de indeling voor de naam van de installatie kopie in de optie persoonlijk REGI ster?**

Voeg de volledige naam van de installatie kopie toe, met inbegrip van de URL van het persoonlijke REGI ster (bijvoorbeeld myacr.azurecr.io/dotnet:latest). Namen van installatie kopieën die gebruikmaken van een aangepaste poort, [kunnen niet via de portal worden ingevoerd](https://feedback.azure.com/forums/169385-web-apps/suggestions/31304650). Gebruik het [opdracht regel programma`az`](https://docs.microsoft.com/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set)om `docker-custom-image-name`in te stellen.

**Kan ik meer dan één poort op mijn aangepaste container installatie kopie beschikbaar maken?**

Er wordt geen ondersteuning geboden voor het beschikbaar maken van meer dan één poort.

**Kan ik mijn eigen opslag plaatsen?**

Ja, [uw eigen opslag ruimte](https://docs.microsoft.com/azure/app-service/containers/how-to-serve-content-from-azure-storage) maken is beschikbaar als preview-versie.

**Waarom kan ik niet bladeren in het bestands systeem van mijn aangepaste container of processen uitvoeren vanaf de SCM-site?**

De SCM-site wordt uitgevoerd in een afzonderlijke container. U kunt het bestands systeem of het uitvoeren van processen van de app-container niet controleren.

**Mijn aangepaste container luistert naar een andere poort dan poort 80. Hoe kan ik mijn app configureren voor het routeren van aanvragen naar die poort?**

Er is automatische poort detectie. U kunt ook een app-instelling met de naam *WEBSITES_PORT* opgeven en deze de waarde van het verwachte poort nummer geven. Voorheen heeft het platform de instelling van de *poort* -app gebruikt. We zijn van plan deze app-instelling af te nemen en alleen *WEBSITES_PORT* te gebruiken.

**Moet ik HTTPS implementeren in mijn aangepaste container?**

Nee, het platform verwerkt de HTTPS-beëindiging bij de gedeelde front-ends.

## <a name="multi-container-with-docker-compose"></a>Meerdere containers met docker opstellen

**Hoe kan ik Azure Container Registry (ACR) configureren voor gebruik met meerdere containers?**

Als u ACR met meerdere containers wilt gebruiken, moeten **alle container installatie kopieën** worden gehost op dezelfde ACR-register server. Zodra ze zich op dezelfde register server bevinden, moet u toepassings instellingen maken en vervolgens het configuratie bestand van docker opstellen bijwerken met de naam van de ACR-installatie kopie.

Maak de volgende toepassings instellingen:

- DOCKER_REGISTRY_SERVER_USERNAME
- DOCKER_REGISTRY_SERVER_URL (volledige URL, bijvoorbeeld `https://<server-name>.azurecr.io`)
- DOCKER_REGISTRY_SERVER_PASSWORD (beheerders toegang inschakelen in ACR-instellingen)

In het configuratie bestand verwijzen we naar uw ACR-installatie kopie, zoals in het volgende voor beeld:

```yaml
image: <server-name>.azurecr.io/<image-name>:<tag>
```

**Hoe kan ik weet welke container Internet toegankelijk is?**

- Er kan slechts één container geopend zijn voor toegang
- Alleen poort 80 en 8080 zijn toegankelijk (blootgestelde poorten)

Dit zijn de regels voor het bepalen van de toegang tot de container, in volg orde van prioriteit:

- Toepassings instelling `WEBSITES_WEB_CONTAINER_NAME` ingesteld op de container naam
- De eerste container om poort 80 of 8080 te definiëren
- Als geen van de bovenstaande voor waarden waar is, is de eerste container die in het bestand is gedefinieerd toegankelijk (beschikbaar)

## <a name="pricing-and-sla"></a>Prijzen en SLA

**Wat zijn de prijzen, nu de service algemeen beschikbaar is?**

De normale Azure App Service prijzen worden in rekening gebracht voor het aantal uren dat uw app wordt uitgevoerd.

## <a name="other-questions"></a>Andere vragen

**Wat betekent ' aangevraagde functie is niet beschikbaar in de resource groep '?**

Dit bericht kan worden weer gegeven bij het maken van een web-app met behulp van Azure Resource Manager (ARM). Op basis van een huidige beperking voor dezelfde resource groep kunt u geen Windows-en Linux-apps in dezelfde regio combi neren.

**Wat zijn de ondersteunde tekens in de namen van toepassings instellingen?**

U kunt alleen letters (A-Z, a-z), cijfers (0-9) en het onderstrepings teken (_) voor toepassings instellingen gebruiken.

**Waar kan ik nieuwe functies aanvragen?**

U kunt uw idee verzenden via het [Feedback forum van web apps](https://aka.ms/webapps-uservoice). Voeg ' [Linux] ' toe aan de titel van uw idee.

## <a name="next-steps"></a>Volgende stappen

- [Wat is Azure App Service op Linux?](app-service-linux-intro.md)
- [Faseringsomgevingen in Azure App Service instellen](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Doorlopende implementatie met Web App for Containers](./app-service-linux-ci-cd.md)
