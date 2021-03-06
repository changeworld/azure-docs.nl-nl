---
title: 'Snelstartgids: een C# ASP.net core-app maken'
description: Meer informatie over het uitvoeren van web-apps in Azure App Service door C# de standaard sjabloon voor ASP.net core web-apps te implementeren vanuit Visual Studio.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 08/30/2019
ms.custom: seodec18
ms.openlocfilehash: 285e4cc1f38dd2adb5934e49d87b43e09d74ce11
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79241477"
---
# <a name="create-an-aspnet-core-web-app-in-azure"></a>Een ASP.NET Core-web-app maken in Azure

> [!NOTE]
> In dit artikel gaat u een app implementeren in App Service onder Windows. Zie _Een .NET Core-web-app maken en implementeren in App Service onder Linux_  om een app te implementeren in App Service onder [Linux](./containers/quickstart-dotnetcore.md).
>

[Azure App Service](overview.md) biedt een uiterst schaalbare webhostingservice met self-patchfunctie.

In deze snelstart ziet u hoe u uw eerste ASP.NET Core-web-app implementeert naar Azure App Service. Als u klaar bent, hebt u een resourcegroep die bestaat uit een App Service-plan en een Azure Service-app met een geïmplementeerde webtoepassing.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Als u deze zelf studie wilt volt ooien, installeert u <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> met de **ASP.net-en Web Development** -werk belasting.

Als u Visual Studio 2019 al hebt geïnstalleerd:

- Installeer de meest recente updates in Visual Studio door **Help** te selecteren > **op updates te controleren**.
- Voeg de werk belasting toe door **extra** > **Hulpprogram Ma's en functies ophalen**te selecteren.

## <a name="create-an-aspnet-core-web-app"></a>Een ASP.NET Core-web-app maken

Maak een ASP.NET Core-web-app door de volgende stappen uit te voeren:

1. Open Visual Studio en selecteer vervolgens **een nieuw project maken**.

1. Zoek en kies **ASP.net core webtoepassing** voor C#in **een nieuw project maken**en selecteer **volgende**.

1. Geef in **uw nieuwe project een**naam voor de toepassing _myFirstAzureWebApp_en selecteer vervolgens **maken**.

   ![Uw web-app-project configureren](./media/app-service-web-get-started-dotnet/configure-web-app-project.png)

1. Kies voor deze Quick Start de sjabloon **Webtoepassing** . Zorg ervoor dat verificatie is ingesteld op **geen verificatie** en dat er geen andere optie is geselecteerd. Selecteer **Maken**.

   ![Selecteer ASP.NET Core Razor Pages voor deze zelf studie](./media/app-service-web-get-started-dotnet/aspnet-razor-pages-app.png)

    U kunt elk type ASP.NET Core-web-app implementeren in Azure.

1. Selecteer in het menu van Visual Studio **fout opsporing** > **zonder fout opsporing starten** om de web-app lokaal uit te voeren.

   ![De app lokaal uitvoeren](./media/app-service-web-get-started-dotnet/razor-web-app-running-locally.png)

## <a name="publish-your-web-app"></a>Uw web-app publiceren

1. Klik in **Solution Explorer**met de rechter muisknop op het project **MyFirstAzureWebApp** en selecteer **publiceren**.

1. Kies **app service** en selecteer vervolgens **publiceren**.

   ![Publiceren vanaf de projectoverzichtspagina](./media/app-service-web-get-started-dotnet/publish-app-vs2019.png)

1. In **app service nieuwe maken**, zijn uw opties afhankelijk van of u al bent aangemeld bij Azure en of u een Visual Studio-account hebt gekoppeld aan een Azure-account. Selecteer **een account toevoegen** of **Aanmelden** om u aan te melden bij uw Azure-abonnement. Als u al bent aangemeld, selecteert u het gewenste account.

   > [!NOTE]
   > Als u al bent aangemeld, selecteert u **Maken** nog niet.
   >

   ![Aanmelden bij Azure](./media/app-service-web-get-started-dotnet/sign-in-azure-vs2019.png)

   [!INCLUDE [resource group intro text](../../includes/resource-group.md)]

1. Selecteer voor **resource groep**de optie **Nieuw**.

1. In **naam van nieuwe resource groep**voert u *myResourceGroup* in en selecteert u **OK**.

   [!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

1. Selecteer **Nieuw**voor het **hosting abonnement**.

1. Voer in het dialoog venster **hosting plan configureren** de waarden in van de volgende tabel en selecteer vervolgens **OK**.

   | Instelling | Voorgestelde waarde | Beschrijving |
   |-|-|-|
   |App Service-plan| myAppServicePlan | De naam van het App Service-plan. |
   | Locatie | Europa -west | Het datacenter waar de web-app wordt gehost. |
   | Grootte | Gratis | De [prijscategorie](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) bepaalt de hosting-functies. |

   ![Een App Service-plan maken](./media/app-service-web-get-started-dotnet/app-service-plan-vs2019.png)

1. Voer bij **naam**een unieke app-naam in die alleen de geldige tekens bevat `a-z`, `A-Z`, `0-9`en `-`. U kunt de automatisch gegenereerde unieke naam accepteren. De URL van de web-app is `http://<app_name>.azurewebsites.net`, waarbij `<app_name>` de naam van uw app is.

   ![Uw app-naam configureren](./media/app-service-web-get-started-dotnet/web-app-name-vs2019.png)

1. Selecteer **Maken** om de Azure-resources te gaan maken.

Zodra de wizard is voltooid, wordt de ASP.NET Core-web-app naar Azure gepubliceerd. Daarna wordt de app gestart in de standaardbrowser.

![Gepubliceerde ASP.NET-web-app in Azure](./media/app-service-web-get-started-dotnet/web-app-running-live.png)

De naam van de app die is opgegeven in de **app service nieuwe pagina maken** , wordt gebruikt als het URL-voor voegsel in de notatie `http://<app_name>.azurewebsites.net`.

**Gefeliciteerd!** Uw ASP.NET Core web-app wordt live uitgevoerd in Azure App Service.

## <a name="update-the-app-and-redeploy"></a>De app bijwerken en opnieuw implementeren

1. Ga in **Solution Explorer**naar het project en open **pagina's** > **index. cshtml**.

1. Vervang de twee `<div>`-tags door de volgende code:

   ```HTML
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we’ve built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Als u opnieuw wilt implementeren naar Azure, klikt u in **Solution Explorer** met de rechtermuisknop op het project **myFirstAzureWebApp** en selecteert u **Publiceren**.

1. Selecteer op de pagina samen vatting **publiceren** de optie **publiceren**.

   ![Pagina overzicht van Visual Studio-publicatie](./media/app-service-web-get-started-dotnet/publish-summary-page-vs2019.png)

Als het publiceren is voltooid, start Visual Studio een browser waarin de URL van de web-app wordt geladen.

![Bijgewerkte ASP.NET-web-app in Azure](./media/app-service-web-get-started-dotnet/web-app-running-live-updated.png)

## <a name="manage-the-azure-app"></a>De Azure-app beheren

Als u de Web-App wilt beheren, gaat u naar de [Azure Portal](https://portal.azure.com)en zoekt en selecteert u **app Services**.

![App Services selecteren](./media/app-service-web-get-started-dotnet/app-services.png)

Selecteer op de pagina **app Services** de naam van uw web-app.

![Navigatie naar Azure-app in de portal](./media/app-service-web-get-started-dotnet/access-portal-vs2019.png)

De pagina Overzicht van uw web-app wordt weergegeven. Hier kunt u het basis beheer, zoals bladeren, stoppen, starten, opnieuw starten en verwijderen.

![App Service in Azure Portal](./media/app-service-web-get-started-dotnet/web-app-general-vs2019.png)

Het linkermenu bevat een aantal pagina's voor het configureren van uw app.

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [ASP.NET Core met SQL Database](app-service-web-tutorial-dotnetcore-sqldb.md)
