---
title: De instellingen voor een Azure-SSIS Integration Runtime aanpassen
description: In dit artikel wordt beschreven hoe u de aangepaste configuratie-interface gebruikt voor een Azure-SSIS Integration Runtime om extra onderdelen te installeren of instellingen te wijzigen
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 02/14/2020
ms.openlocfilehash: 9c084564fec3faf59317fe9e05f3e850a38454d6
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77251971"
---
# <a name="customize-the-setup-for-an-azure-ssis-integration-runtime"></a>De instellingen voor een Azure-SSIS Integration Runtime aanpassen

De aangepaste installatie voor een Azure-SQL Server Integration Services Integration Runtime (Azure-SSIS IR) biedt een interface voor het toevoegen van uw eigen stappen tijdens het instellen of opnieuw configureren van uw Azure-SSIS IR. 

Door de aangepaste installatie te gebruiken, kunt u de standaard configuratie van het besturings systeem of de omgeving wijzigen in, bijvoorbeeld door extra Windows-Services te starten of toegangs referenties voor bestands shares op te slaan. U kunt ook extra onderdelen, zoals assembly's, stuur Programma's of uitbrei dingen, op elk knoop punt van uw Azure-SSIS IR installeren.

U kunt op twee manieren aangepaste installatie op uw Azure-SSIS IR uitvoeren: 
* **Snelle aangepaste installatie zonder script**: Voer enkele algemene systeem configuraties en Windows-opdrachten uit of installeer populaire of aanbevolen extra onderdelen zonder scripts te gebruiken.
* **Aangepaste standaard installatie met een script**: bereid een script en de bijbehorende bestanden voor en upload deze allemaal samen naar een BLOB-container in uw Azure Storage-account. Vervolgens geeft u een Shared Access Signature (SAS) Uniform Resource Identifier (URI) voor uw container op wanneer u uw Azure-SSIS IR instelt of opnieuw configureert. Elk knoop punt van uw Azure-SSIS IR downloadt vervolgens het script en de bijbehorende bestanden uit uw container en voert uw aangepaste installatie uit met verhoogde machtigingen. Wanneer uw aangepaste installatie is voltooid, uploadt elk knoop punt de standaard uitvoer van de uitvoering en andere logboeken naar uw container.

U kunt zowel gratis als niet-gelicentieerde onderdelen en betaalde, gelicentieerde onderdelen installeren met snelle en standaard aangepaste installatie. Als u een onafhankelijke software leverancier (ISV) bent, raadpleegt u [betaalde of gelicentieerde onderdelen voor een Azure-SSIS IR ontwikkelen](how-to-develop-azure-ssis-ir-licensed-components.md).

> [!IMPORTANT]
> Omdat v2-serie knooppunten van een Azure-SSIS IR niet geschikt zijn voor aangepaste installatie, gebruikt u in plaats daarvan de V3-serie knooppunten. Als u al knoop punten van de v2-serie gebruikt, schakelt u zo snel mogelijk over naar de V3-serie knooppunten.

## <a name="current-limitations"></a>Huidige beperkingen

De volgende beperkingen gelden alleen voor standaard aangepaste Setup:

- Als u *Gacutil. exe* in uw script wilt gebruiken voor het installeren van assembly's in de Global assembly cache (GAC), moet u *Gacutil. exe* opgeven als onderdeel van de aangepaste installatie. U kunt ook gebruikmaken van de kopie die is opgenomen in de *open bare preview* -container, die later wordt beschreven in de sectie ' instructies '.

- Als u wilt verwijzen naar een submap in uw script, ondersteunt *Msiexec. exe* de `.\`-notatie niet om te verwijzen naar de hoofdmap. Gebruik een opdracht als `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` in plaats van `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`.

- Beheerders shares of verborgen netwerk shares die automatisch door Windows worden gemaakt, worden momenteel niet ondersteund op de Azure-SSIS IR.

- Het ODBC-stuur programma voor de IBM iSeries-toegang wordt niet ondersteund op de Azure-SSIS IR. Tijdens uw aangepaste installatie worden mogelijk installatie fouten weer geven. Neem contact op met IBM Support voor hulp.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Als u uw Azure-SSIS IR wilt aanpassen, hebt u de volgende items nodig:

- [Een Azure-abonnement](https://azure.microsoft.com/)

- [Uw Azure-SSIS IR inrichten](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

- [Een Azure-opslag account](https://azure.microsoft.com/services/storage/). Niet vereist voor snelle aangepaste installatie. Voor aangepaste standaard instellingen uploadt en slaat u uw aangepaste installatie script en de bijbehorende bestanden op in een BLOB-container. Het aangepaste installatie proces uploadt ook de uitvoerings logboeken naar dezelfde BLOB-container.

## <a name="instructions"></a>Instructies

1. Als u uw Azure-SSIS IR wilt instellen of opnieuw wilt configureren met Power shell, downloadt en installeert u [Azure PowerShell](/powershell/azure/install-az-ps). Ga door naar stap 4 voor snelle aangepaste installatie.

1. Bereid uw aangepaste installatie script en de bijbehorende bestanden voor (bijvoorbeeld. bat,. cmd,. exe,. dll,. msi of. ps1-bestanden).

   * U moet een script bestand met de naam *Main. cmd*hebben. Dit is het toegangs punt van uw aangepaste installatie.  
   * Om ervoor te zorgen dat het script op de achtergrond kan worden uitgevoerd, raden we u aan het eerst op uw lokale machine te testen.  
   * Als u wilt dat extra logboeken die worden gegenereerd door andere hulpprogram ma's (bijvoorbeeld *Msiexec. exe*), worden geüpload naar de container, geeft u de vooraf gedefinieerde omgevings variabele `CUSTOM_SETUP_SCRIPT_LOG_DIR`, als de logboekmap in uw scripts (bijvoorbeeld *msiexec/i xxx. msi/quiet/LV% CUSTOM_SETUP_SCRIPT_LOG_DIR% \ install. log*).

1. Down load, installeer en open [Azure Storage Explorer](https://storageexplorer.com/). Dit doet u als volgt:

   a. Onder **(lokaal en gekoppeld)** klikt u met de rechter muisknop op **opslag accounts**en selecteert **u verbinding maken met Azure Storage**.

      ![Verbinding maken met Azure Storage](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   b. Selecteer **een opslag accountnaam en-sleutel gebruiken**en selecteer **volgende**.

      ![De naam en sleutel van een opslagaccount gebruiken](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   c. Voer de naam en sleutel van uw Azure Storage-account in, selecteer **volgende**en selecteer vervolgens **verbinding maken**.

      ![Naam en sleutel van opslag account opgeven](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   d. Klik onder uw verbonden Azure Storage-account met de rechter muisknop op **BLOB-containers**, selecteer **BLOB-container maken**en geef de nieuwe container een naam.

      ![Een blob-container maken](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   e. Selecteer de nieuwe container en upload uw aangepaste installatie script en de bijbehorende bestanden. Zorg ervoor dat u *Main. cmd* op het hoogste niveau van uw container uploadt, niet in een map. Zorg er ook voor dat de container alleen de benodigde aangepaste installatie bestanden bevat, zodat deze niet langer lang worden gedownload naar uw Azure-SSIS IR. De maximale duur van een aangepaste installatie is momenteel ingesteld op 45 minuten voordat er een time-out optreedt. Dit omvat de tijd voor het downloaden van alle bestanden uit uw container en het installeren ervan op het Azure-SSIS IR. Als er meer tijd nodig is, kunt u een ondersteunings ticket genereren.

      ![Bestanden uploaden naar de BLOB-container](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   f. Klik met de rechter muisknop op de container en selecteer vervolgens **Shared Access Signature ophalen**.

      ![De Shared Access Signature voor de container ophalen](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   g. Maak de SAS-URI voor uw container met een voldoende lange verloop tijd en met de machtiging lezen/schrijven/lijst. U hebt de SAS-URI nodig om uw aangepaste installatie script en de bijbehorende bestanden te downloaden en uit te voeren wanneer een wille keurig knoop punt van uw Azure-SSIS IR wordt geimageeerd of opnieuw wordt gestart. U hebt schrijf machtigingen nodig voor het uploaden van de installatie Logboeken.

      > [!IMPORTANT]
      > Zorg ervoor dat de SAS-URI niet verlopen en dat de aangepaste installatie resources altijd beschikbaar zijn tijdens de hele levens cyclus van uw Azure-SSIS IR, van het maken tot verwijderen, met name als u uw Azure-SSIS IR tijdens deze periode regel matig stopt en start.

      ![De Shared Access Signature voor de container genereren](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   h. Kopieer de SAS-URI van uw container en sla deze op.

      ![De Shared Access Signature kopiëren en opslaan](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. Wanneer u uw Azure-SSIS IR instelt of opnieuw configureert met een data factory gebruikers interface, kunt u aangepaste Setup toevoegen of verwijderen door het selectie vakje **uw Azure-SSIS Integration runtime met extra systeem configuraties/onderdelen installeren** in te scha kelen in het gedeelte **Geavanceerde instellingen** van het deel venster **Integration runtime Setup** . 

   Als u aangepaste standaard instellingen wilt toevoegen, voert u de SAS-URI van uw container in het vak **aangepaste installatie container SAS URI** in. 
   
   Als u snelle aangepaste installatie wilt toevoegen, selecteert u **Nieuw** om het deel venster **snelle aangepaste installatie toevoegen** te openen en selecteert u vervolgens een type in de vervolg keuzelijst **snelle aangepaste installatie typen** :

   * Als u het **opdracht cmdkey uitvoeren** selecteert, kunt u toegangs referenties voor uw bestands shares of Azure files shares op Azure-SSIS IR bewaren door de naam van uw doel computer of de domein naam, de account naam of de gebruikers naam en de account sleutel of het wacht woord in de vakken **/add**, **/User**en **/Pass** op te geven. Dit is vergelijkbaar met het uitvoeren van de Windows [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) -opdracht op uw lokale computer.
   
   * Als u het type **omgevings variabele toevoegen** selecteert, kunt u Windows-omgevings variabelen toevoegen voor gebruik in uw pakketten die worden uitgevoerd op de Azure-SSIS IR door de naam en waarde van de omgevings variabele in te voeren in de vakken **naam** en **variabele waarde** . Dit is vergelijkbaar met het uitvoeren van de Windows-opdracht [set](https://docs.microsoft.com/windows-server/administration/windows-commands/set_1) op uw lokale computer.

   * Als u het **onderdeel type licentie installeren** selecteert, kunt u een geïntegreerd onderdeel selecteren in onze ISV-partners in de vervolg keuzelijst **onderdeel naam** :

     * Als u het onderdeel **taak-Factory van SentryOne** selecteert, kunt u de [taken fabrieks](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) suite van onderdelen van SentryOne op uw Azure-SSIS IR installeren door de product licentie code in te voeren die u hebt aangeschaft in het vak **licentie code** . De huidige geïntegreerde versie is **2019.4.3**.

     * Als u de **oh22's-HEDDA selecteert. IO** -onderdeel kunt u de [HEDDA installeren. ](https://hedda.io/ssis-component/)Het onderdeel io-gegevens kwaliteit/opschonen van oh22 op uw Azure-SSIS IR na het aanschaffen van de service. De huidige geïntegreerde versie is **1.0.13**.

     * Als u het **oh22's SQLPhonetics.net** -onderdeel selecteert, kunt u de [SQLPhonetics.net](https://sqlphonetics.oh22.is/sqlphonetics-net-for-microsoft-ssis/) -gegevens kwaliteit/het overeenkomende onderdeel van oh22 op uw Azure-SSIS IR installeren door de product licentie code in te voeren die u hebt aangeschaft in het vak **licentie code** . De huidige geïntegreerde versie is **1.0.43**.

     * Als u het **SSIS Integration Toolkit** -onderdeel van de KingswaySoft selecteert, kunt u het pakket met de [SSIS Integration Toolkit](https://www.kingswaysoft.com/products/ssis-integration-toolkit-for-microsoft-dynamics-365) van connectors voor CRM/ERP/marketing/samenwerkings-apps installeren, zoals micro soft Dynamics/share point/Project Server, Oracle/sales force marketing Cloud, Azure-SSIS IR enzovoort door de product licentie code in te voeren die u hebt aangeschaft in het vak **licentie code** . De huidige geïntegreerde versie is **2019,2**.

     * Als u het **SSIS Productivity Pack** -onderdeel van de KingswaySoft selecteert, kunt u de [SSIS-productiviteits pakket](https://www.kingswaysoft.com/products/ssis-productivity-pack) onderdelen van KingswaySoft op uw Azure-SSIS IR installeren door de product licentie code in te voeren die u hebt aangeschaft in het vak **licentie code** . De huidige geïntegreerde versie is **10,0**.

   Uw toegevoegde snelle aangepaste Setup wordt weer gegeven in de sectie **Geavanceerde instellingen** . Als u deze wilt verwijderen, schakelt u de selectie vakjes in en selecteert u vervolgens **verwijderen**.

   ![Geavanceerde instellingen met aangepaste installatie](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

1. Wanneer u de Azure-SSIS IR met Power shell instelt of opnieuw configureert, kunt u de aangepaste Setup toevoegen of verwijderen door de `Set-AzDataFactoryV2IntegrationRuntime` cmdlet uit te voeren voordat u de Azure-SSIS IR start.
   
   ```powershell
   $ResourceGroupName = "[your Azure resource group name]"
   $DataFactoryName = "[your data factory name]"
   $AzureSSISName = "[your Azure-SSIS IR name]"
   # Custom setup info: Standard/express custom setups
   $SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
   $ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO or leave it empty]" # OPTIONAL to configure an express custom setup without script

   # Add custom setup parameters if you use standard/express custom setups
   if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
   {
       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -SetupScriptContainerSasUri $SetupScriptContainerSasUri
   }
   if(![string]::IsNullOrEmpty($ExpressCustomSetup))
   {
       if($ExpressCustomSetup -eq "RunCmdkey")
       {
           $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
           $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
           $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
       }
       if($ExpressCustomSetup -eq "SetEnvironmentVariable")
       {
           $variableName = "YourVariableName"
           $variableValue = "YourVariableValue"
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
       }
       if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
       {
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
       }
       # Create an array of one or more express custom setups
       $setups = New-Object System.Collections.ArrayList
       $setups.Add($setup)

       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -ExpressCustomSetup $setups
   }
   Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
       -DataFactoryName $DataFactoryName `
       -Name $AzureSSISName `
       -Force
   ```
   
   Nadat de aangepaste standaard installatie is voltooid en uw Azure-SSIS IR wordt gestart, kunt u de standaard uitvoer van *Main. cmd* en andere uitvoerings logboeken vinden in de map *Main. cmd. log* van uw opslag container.

1. Als u enkele voor beelden van aangepaste standaard instellingen wilt weer geven, maakt u verbinding met onze open bare preview-container met behulp van Azure Storage Explorer.

   a. Onder **(lokaal en gekoppeld)** klikt u met de rechter muisknop op **opslag accounts**, selecteert **u verbinding maken met Azure Storage**, selecteert **u een Connection String of een URI voor de hand tekening voor gedeelde toegang gebruiken**en selecteert u vervolgens **volgende**.

      ![Verbinding maken met Azure Storage met de Shared Access Signature](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b. Selecteer **een SAS-URI gebruiken** en voer vervolgens in het vak **URI** de volgende SAS-URI in:

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

      ![De Shared Access Signature voor de container opgeven](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. Selecteer **volgende**en selecteer vervolgens **verbinding maken**.

   d. Selecteer in het linkerdeel venster de container Connected **publicpreview** en dubbel klik vervolgens op de map *CustomSetupScript* . In deze map zijn de volgende items:

      * Een voor *beeld* van een map, die een aangepaste installatie bevat voor het installeren van een basis taak op elk knoop punt van uw Azure-SSIS IR. De taak heeft niets maar een paar seconden in de slaap stand. De map bevat ook een map *gacutil* , waarvan de volledige inhoud (*Gacutil. exe*, *Gacutil. exe. config*en *1033 \ gacutlrc. dll*) naar uw container kan worden gekopieerd.

      * Een *UserScenarios* -map, die verschillende aangepaste installatie voorbeelden bevat uit echte gebruikers scenario's.

        ![Inhoud van de open bare preview-container](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   e. Dubbel klik op de map *UserScenarios* om de volgende items te zoeken:

      * Een *.NET FRAMEWORK 3,5* -map die een aangepaste installatie bevat voor het installeren van een eerdere versie van de .NET Framework die mogelijk vereist is voor aangepaste onderdelen op elk knoop punt van uw Azure-SSIS IR.

      * Een *bcp* -map die een aangepaste installatie bevat voor het installeren van SQL Server opdracht regel programma's (*MsSqlCmdLnUtils. msi*), met inbegrip van het pakket voor bulksgewijs kopiëren (*bcp*), op elk knoop punt van uw Azure-SSIS IR.

      * Een *Excel* -map, die een aangepast installatie script (*Main. cmd*) bevat voor C# het installeren van assembly's en bibliotheken die u in script taken kunt gebruiken om Excel-bestanden dynamisch te lezen en schrijven op elk knoop punt van uw Azure-SSIS IR. 
      
        Down load eerst [*ExcelDataReader. dll*](https://www.nuget.org/packages/ExcelDataReader/) en [*DocumentFormat. OpenXml. dll*](https://www.nuget.org/packages/DocumentFormat.OpenXml/)en upload deze allemaal samen met *Main. cmd* naar uw container. Als u alleen de standaard Excel-verbindings beheer, de Excel-bron en de Excel-bestemming wilt gebruiken, is het vereiste toegangs herdistribueersysteem al vooraf geïnstalleerd op uw Azure-SSIS IR, dus hebt u geen aangepaste installatie nodig.
      
      * Een *MySQL ODBC-* map, die een aangepast installatie script (*Main. cmd*) bevat voor het installeren van de MySQL ODBC-stuur Programma's op elk knoop punt van uw Azure-SSIS IR. Met deze installatie kunt u de ODBC-verbindings beheer, bron en bestemming gebruiken om verbinding te maken met de MySQL-server. 
     
        [Down load eerst de nieuwste 64-bits en 32-bits versies van de MySQL ODBC-stuur Programma's](https://dev.mysql.com/downloads/connector/odbc/) (bijvoorbeeld *mysql-connector-odbc-8.0.13-winx64. msi* en *mysql-connector-odbc-8.0.13-Win32. msi*) en upload deze allemaal samen met *Main. cmd* naar uw container.

      * Een *Oracle Enter prise* -map, die een aangepast installatie script (*Main. cmd*) en een installatie configuratie bestand (*client. RSP*) bevat waarmee de Oracle-connectors en het OCI-stuur programma kunnen worden geïnstalleerd op elk knoop punt van uw Azure-SSIS IR Enter prise-editie. Met deze installatie kunt u de Oracle-verbindings beheer,-bron en-bestemming gebruiken om verbinding te maken met de Oracle-server. 
      
        Down load eerst micro soft-connectors v 5.0 voor Oracle (*AttunitySSISOraAdaptersSetup. msi* en *AttunitySSISOraAdaptersSetup64. msi*) van het [micro soft Download centrum](https://www.microsoft.com/en-us/download/details.aspx?id=55179) en de nieuwste Oracle-client (bijvoorbeeld *winx64_12102_client. zip*) van [Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html)en upload deze allemaal samen met *Main. cmd* en *client. RSP* naar uw container. Als u TNS gebruikt om verbinding te maken met Oracle, moet u ook *bestand Tnsnames. ora*downloaden, bewerken en uploaden naar uw container, zodat deze kan worden gekopieerd naar de map Oracle-installatie tijdens de installatie.

      * Een *Oracle STANDARD ADO.net* -map met een aangepast installatie script (*Main. cmd*) om het Oracle ODP.net-stuur programma te installeren op elk knoop punt van uw Azure-SSIS IR. Met deze installatie kunt u de ADO.NET-verbindings beheer,-bron en-bestemming gebruiken om verbinding te maken met de Oracle-server. 
      
        [Down load eerst het meest recente Oracle ODP.net-stuur programma](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html) (bijvoorbeeld *ODP. NET_Managed_ODAC122cR1. zip*) en upload het vervolgens samen met *Main. cmd* naar uw container.
       
      * Een *ODBC-standaard-* map met een aangepaste installatie script (*Main. cmd*) om het Oracle ODBC-stuur programma te installeren en de naam van de gegevens bron (DSN) op elk knoop punt van uw Azure-SSIS IR te configureren. Met deze installatie kunt u de ODBC-verbindings beheer, de bron, de doel-of Power Query verbindings beheer en de bron met het ODBC-gegevens bron type gebruiken om verbinding te maken met de Oracle-server. 
      
        Down load eerst de nieuwste Oracle Instant-client (Basic package of Basic Lite package) en het ODBC-pakket en upload deze allemaal samen met *Main. cmd* naar uw container:
        * [64-bits pakketten downloaden](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (Basic-pakket: *instantclient-Basic-Windows. x64-18.3.0.0.0 dbru. zip*; Basic Lite-pakket: *instantclient-basiclite-Windows. x64-18.3.0.0.0 dbru. zip*; ODBC-pakket: *instantclient-ODBC-Windows. x64-18.3.0.0.0 dbru. zip*) 
        * [32-bits pakketten downloaden](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (Basic-pakket: *instantclient-Basic-NT-18.3.0.0.0 dbru. zip*; Basic Lite-pakket: *instantclient-basiclite-NT-18.3.0.0.0 dbru. zip*; ODBC-pakket: *instantclient-ODBC-NT-18.3.0.0.0 dbru. zip*)

      * Een *Oracle Standard OLEDB* -map, die een aangepast installatie script (*Main. cmd*) bevat om het Oracle OLEDB-stuur programma te installeren op elk knoop punt van uw Azure-SSIS IR. Met deze installatie kunt u de OLEDB-verbindings beheer, de bron en de bestemming gebruiken om verbinding te maken met de Oracle-server. 
     
        [Down load eerst het meest recente Oracle OLEDB-stuur programma](https://www.oracle.com/partners/campaign/index-090165.html) (bijvoorbeeld *ODAC122010Xcopy_x64. zip*) en upload het vervolgens samen met *Main. cmd* naar uw container.

      * Een *POSTGRESQL ODBC* -map, die een aangepast installatie script (*Main. cmd*) bevat om de POSTGRESQL ODBC-stuur Programma's te installeren op elk knoop punt van uw Azure-SSIS IR. Met deze installatie kunt u de ODBC-verbindings beheer, bron en bestemming gebruiken om verbinding te maken met de PostgreSQL-server. 
     
        [Down load eerst de nieuwste 64-bits en 32-bits versies van postgresql ODBC-stuur programma-installatie Programma's](https://www.postgresql.org/ftp/odbc/versions/msi/) (bijvoorbeeld *psqlodbc_x64. msi* en *psqlodbc_x86. msi*) en upload deze allemaal samen met *Main. cmd* naar uw container.

      * Een *SAP BW* map, die een aangepast installatie script (*Main. cmd*) bevat voor het installeren van de SAP .net-connector-Assembly (*librfc32. dll*) op elk knoop punt van uw Azure-SSIS IR Enter prise-editie. Met deze installatie kunt u de SAP Business Warehouse (BW)-verbindings beheer, bron en bestemming gebruiken om verbinding te maken met de SAP BW-server. 
      
        Upload eerst de 64-bits of de 32-bits versie van *librfc32. dll* uit de SAP-installatiemap in combi natie met *Main. cmd* naar uw container. Het script kopieert vervolgens de SAP-assembly naar de map *%windir%\syswow64* of *%windir%\System32* tijdens de installatie.

      * Een *opslagmap* , die een aangepaste installatie bevat om Azure PowerShell te installeren op elk knoop punt van uw Azure-SSIS IR. Met deze installatie kunt u SSIS-pakketten implementeren en uitvoeren die [Power shell-scripts uitvoeren voor het bewerken van uw Azure Storage-account](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell). 
      
        Kopieer *Main. cmd*, een voor beeld van een *AzurePowerShell. msi* (of gebruik de meest recente versie) en *Storage. ps1* naar uw container. Gebruik *Power shell. dtsx* als een sjabloon voor uw pakketten. De pakket sjabloon bevat een combi natie van een [Azure Blob-Download taak](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task), waarmee *Storage. ps1* als een gewijzigd Power shell-script wordt gedownload en een taak voor het uitvoeren van een [proces](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/), waarmee het script wordt uitgevoerd op elk knoop punt.

      * Een *TERADATA* -map die een aangepast installatie script (*Main. cmd*) bevat, het bijbehorende bestand (*install. cmd*) en de installatie pakketten ( *. msi*). Met deze bestanden worden de Teradata-connectors, de TPT-API (parallel trans Porter) en het ODBC-stuur programma geïnstalleerd op elk knoop punt van uw Azure-SSIS IR Enter prise-editie. Met deze installatie kunt u de Teradata-verbindings beheer, bron en bestemming gebruiken om verbinding te maken met de Teradata-server. 
      
        [Down load eerst de Teradata-Hulpprogram ma's en Hulpprogram ma's 15. x zip-bestand](http://partnerintelligence.teradata.com) (bijvoorbeeld *TeradataToolsAndUtilitiesBase__windows_indep. 15.10.22.00. zip*) en upload het vervolgens samen met de eerder genoemde *. cmd* -en *MSI* -bestanden naar uw container.

      * Een *ZULU OPENJDK* -map die een aangepast installatie script (*Main. cmd*) en Power shell-bestand (*install_openjdk. ps1*) bevat voor het installeren van ZULU OPENJDK op elk knoop punt van uw Azure-SSIS IR. Met deze installatie kunt u Azure Data Lake Store en flexibele bestands connectors gebruiken voor het verwerken van ORC-en Parquet-bestanden. Zie [Azure Feature Pack voor integratie Services](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-ver15#dependency-on-java)voor meer informatie. 
      
        [Down load eerst de meest recente Zulu-openjdk](https://www.azul.com/downloads/zulu/zulu-windows/) (bijvoorbeeld *Zulu 8.33.0.1-jdk 8.0.192-win_x64. zip*) en upload deze vervolgens samen met *Main. cmd* en *install_openjdk. ps1* naar uw container.

        ![Mappen in de map gebruikers scenario's](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   f. Kopieer de inhoud van de geselecteerde map naar uw container om deze aangepaste installatie voorbeelden uit te proberen.
   
      Wanneer u de Azure-SSIS IR instelt of opnieuw configureert met behulp van de Data Factory-gebruikers interface, schakelt u het selectie vakje **Azure-SSIS Integration runtime met aanvullende systeem configuraties/onderdelen installeren** in het gedeelte **Geavanceerde instellingen** in en voert u de SAS-URI van uw container in het vak **aangepaste installatie container SAS URI** in.
   
      Wanneer u uw Azure-SSIS IR instelt of opnieuw configureert met Power shell, voert u de `Set-AzDataFactoryV2IntegrationRuntime` cmdlet uit met de SAS-URI van uw container als waarde voor de `SetupScriptContainerSasUri`-para meter.

## <a name="next-steps"></a>Volgende stappen

- [De Enter prise-editie van de Azure-SSIS Integration Runtime instellen](how-to-configure-azure-ssis-ir-enterprise-edition.md)
- [Betaalde of gelicentieerde aangepaste onderdelen ontwikkelen voor de Azure-SSIS Integration Runtime](how-to-develop-azure-ssis-ir-licensed-components.md)
