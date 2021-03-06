---
title: Fout C# opsporingsgegevens voor Azure data Lake U-SQL-taken
description: In dit artikel wordt beschreven hoe u fouten opspoort voor een U-SQL-vertex met Azure Data Lake-Hulpprogram Ma's voor Visual Studio.
services: data-lake-analytics
ms.service: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: bcd0b01e-1755-4112-8e8a-a5cabdca4df2
ms.topic: conceptual
ms.date: 11/30/2017
ms.openlocfilehash: 72239fc1679d2ebbfd9c9b5be6b79b58efb760cb
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71315802"
---
# <a name="debug-user-defined-c-code-for-failed-u-sql-jobs"></a>Fout opsporing door gebruiker C# gedefinieerde code voor mislukte U-SQL-taken

U-SQL biedt een uitbreidings model dat C#wordt gebruikt. In U-SQL-scripts is het eenvoudig om functies C# aan te roepen en analyse functies uit te voeren die niet worden ondersteund door SQL, zoals verklarende taal. Zie voor meer informatie over de U-SQL-uitbrei ding [u-SQL-programmeer gids](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-u-sql-programmability-guide#use-user-defined-functions-udf). 

In de praktijk is het mogelijk dat alle code fouten opsporen, maar het is lastig om een gedistribueerde taak met aangepaste code in de cloud te debuggen met beperkte logboek bestanden. [Azure data Lake-Hulpprogram ma's voor Visual Studio](https://aka.ms/adltoolsvs) biedt een functie met de naam fout: **vertex debug**, waarmee u de fouten die zich in uw aangepaste code kunnen voordoen, gemakkelijker kunt opsporen. Wanneer U-SQL-taak mislukt, wordt de fout status door de service gehandhaafd en helpt u bij het downloaden van de Cloud fout omgeving naar de lokale computer voor fout opsporing. De lokale down load legt de gehele cloud omgeving vast, inclusief alle invoer gegevens en gebruikers code.

In de volgende video ziet u mislukte vertex fout opsporing in Azure Data Lake-Hulpprogram Ma's voor Visual Studio.

> [!VIDEO https://www.youtube.com/embed/3enkNvprfm4]
>

> [!IMPORTANT]
> Voor Visual Studio zijn de volgende twee updates vereist voor het gebruik van deze functie: [Micro soft C++ Visual 2015 Redistributable update 3](https://www.microsoft.com/en-us/download/details.aspx?id=53840) en de [Universal C runtime voor Windows](https://www.microsoft.com/download/details.aspx?id=50410).
>

## <a name="download-failed-vertex-to-local-machine"></a>Defecte vertex naar lokale machine downloaden

Wanneer u een mislukte taak in Azure Data Lake-Hulpprogram Ma's voor Visual Studio opent, ziet u een gele waarschuwings balk met gedetailleerde fout berichten op het tabblad fout.

1. Klik op **downloaden** om alle vereiste resources en invoer stromen te downloaden. Als het downloaden niet is voltooid, klikt u op **opnieuw**.

2. Klik op **openen** nadat het downloaden is voltooid om een lokale debugging omgeving te genereren. Er wordt een nieuwe oplossing voor fout opsporing geopend, en als u een bestaande oplossing hebt geopend in Visual Studio, moet u ervoor zorgen dat u deze opslaat en sluit voordat u fouten opspoort.

![Down load hoekpunt van Visual Studio Azure Data Lake Analytics U-SQL-fout opsporing](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

## <a name="configure-the-debugging-environment"></a>De omgeving voor fout opsporing configureren

> [!NOTE]
> Controleer voordat u fouten opspoort of de **runtime-uitzonde ringen common language** in het venster uitzonderings instellingen (**CTRL + ALT + E**).

![Visual Studio-instelling voor het opsporen van fouten in Azure Data Lake Analytics U-SQL](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

In het nieuwe gestarte exemplaar van Visual Studio is het mogelijk dat u de door de C# gebruiker gedefinieerde bron code niet kunt vinden:

1. [Ik vind mijn bron code in de oplossing](#source-code-is-included-in-debugging-solution)

2. [Ik kan mijn bron code niet vinden in de oplossing](#source-code-is-not-included-in-debugging-solution)

### <a name="source-code-is-included-in-debugging-solution"></a>De bron code is opgenomen in de oplossing voor fout opsporing

Er zijn twee gevallen waarin de C# bron code is vastgelegd:

1. De gebruikers code wordt gedefinieerd in het code-behind-bestand ( `Script.usql.cs` meestal genoemd in een U-SQL-project).

2. De gebruikers code is gedefinieerd in C# het klassen bibliotheek project voor de U-SQL-toepassing en is geregistreerd als assembly met **fout opsporingsgegevens**.

Als de bron code naar de oplossing wordt geïmporteerd, kunt u de Visual Studio-probleemoplossings programma's (Watch, variabelen, enzovoort) gebruiken om het probleem op te lossen:

1. Druk op **F5** om de foutopsporing te starten. De code wordt uitgevoerd totdat deze door een uitzonde ring wordt gestopt.

2. Open het bron code bestand en stel onderbrekings punten in en druk vervolgens op **F5** om de code stap voor stap fouten op te sporen.

    ![Uitzonde ring bij Azure Data Lake Analytics U-SQL-fout opsporing](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-exception.png)

### <a name="source-code-is-not-included-in-debugging-solution"></a>De bron code is niet opgenomen in de oplossing voor fout opsporing

Als de gebruikers code niet is opgenomen in het code-behind-bestand of als u de assembly niet hebt geregistreerd met **fout opsporingsgegevens**, wordt de bron code niet automatisch opgenomen in de oplossing voor fout opsporing. In dit geval hebt u extra stappen nodig om uw bron code toe te voegen:

1. Klik met de rechter muisknop op **oplossing ' VertexDebug ' > voeg > bestaande project toe...** om de assembly bron code te vinden en het project toe te voegen aan de oplossing voor fout opsporing.

    ![Azure Data Lake Analytics U-SQL-fout opsporing project toevoegen](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-add-project-to-debug-solution.png)

2. Haal het pad naar de projectmap op voor **FailedVertexDebugHost** -project. 

3. Klik met de rechter muisknop op **de toegevoegde assemblage broncode project > eigenschappen**, selecteer het tabblad **samen stellen** links en plak het gekopieerde pad dat eindigt op \Bin\debug als **uitvoer > uitvoer traject**. Het laatste uitvoer traject is als `<DataLakeTemp path>\fd91dd21-776e-4729-a78b-81ad85a4fba6\loiu0t1y.mfo\FailedVertexDebug\FailedVertexDebugHost\bin\Debug\`volgt.

    ![Azure Data Lake Analytics U-SQL debug set PDB-pad instellen](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-set-pdb-path.png)

Na deze instellingen start u fout opsporing met **F5** en onderbrekings punten. U kunt ook de Visual Studio-hulpprogram ma's voor fout opsporing (Watch, variabelen, enzovoort) gebruiken om het probleem op te lossen.

> [!NOTE]
> Bouw het project van de assembly-bron code opnieuw op telkens nadat u de code hebt gewijzigd om bijgewerkte. PDB-bestanden te genereren.

## <a name="resubmit-the-job"></a>De taak opnieuw verzenden

Wanneer de fout opsporing is voltooid, wordt het volgende bericht weer gegeven in het uitvoer venster:

    The Program 'LocalVertexHost.exe' has exited with code 0 (0x0).

![Azure Data Lake Analytics U-SQL-fout opsporing geslaagd](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-succeed.png)

De mislukte taak opnieuw verzenden:

1. Voor taken met code-behind oplossingen kopieert u de C# code naar het bron bestand van de code-behind `Script.usql.cs`(meestal).

2. Voor taken met assembly's klikt u met de rechter muisknop op het assemblage broncode project in de oplossing voor fout opsporing en registreert u de bijgewerkte dll-assembly's in uw Azure Data Lake catalogus.

3. Verzend de U-SQL-taak opnieuw.

## <a name="next-steps"></a>Volgende stappen

- [Programmeer handleiding voor U-SQL](data-lake-analytics-u-sql-programmability-guide.md)
- [Door de gebruiker gedefinieerde U-SQL-Opera tors ontwikkelen voor Azure Data Lake Analytics-taken](data-lake-analytics-u-sql-develop-user-defined-operators.md)
- [U-SQL-taken testen en controleren op fouten met behulp van lokale uitvoering en de Azure Data Lake U-SQL-SDK](data-lake-analytics-data-lake-tools-local-run.md)
- [Problemen oplossen met een abnormale terugkerende taak](data-lake-analytics-data-lake-tools-debug-recurring-job.md)
