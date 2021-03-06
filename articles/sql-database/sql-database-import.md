---
title: Een BACPAC-bestand importeren om een Data Base te maken
description: Maak een newAzure SQL-database door een BACPAC-bestand te importeren.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/20/2019
ms.openlocfilehash: 05698596f966f879da1affc58af0122d08d519ff
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79256235"
---
# <a name="quickstart-import-a-bacpac-file-to-a-database-in-azure-sql-database"></a>Quick Start: een BACPAC-bestand importeren in een data base in Azure SQL Database

U kunt een SQL Server Data Base importeren in een data base in Azure SQL Database met behulp van een [BACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) -bestand. U kunt de gegevens importeren uit een `BACPAC`-bestand dat is opgeslagen in Azure Blob Storage (alleen standaard opslag) of vanuit lokale opslag op een on-premises locatie. Als u de snelheid van het importeren wilt maximaliseren door meer en snellere resources te bieden, schaalt u uw Data Base naar een hogere servicelaag en de grootte van de berekening tijdens het import proces. U kunt vervolgens omlaag schalen nadat het importeren is voltooid.

> [!NOTE]
> Compatibiliteitsniveau van de geïmporteerde database is gebaseerd op het compatibiliteitsniveau van de brondatabase.

> [!IMPORTANT]
> Na het importeren van uw database, kunt u de database op het huidige compatibiliteitsniveau (niveau 100 voor de database AdventureWorks2008R2) of op een hoger niveau. Zie [ALTER DATABASE Compatibility Level](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) (Compatibiliteitsniveau ALTER DATABASE) voor meer informatie over de implicaties en opties bij het hanteren van een database op een bepaald compatibiliteitsniveau. Zie ook [ALTER DATABASE SCOPED CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) voor informatie over aanvullende instellingen op databaseniveau in verband met compatibiliteitsniveaus.

## <a name="using-azure-portal"></a>Azure Portal gebruiken

Bekijk deze video om te zien hoe u kunt importeren uit een BACPAC-bestand in de Azure Portal of door gaan met lezen:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Its-just-SQL-Restoring-a-database-to-Azure-SQL-DB-from-backup/player?WT.mc_id=dataexposed-c9-niner]

De [Azure Portal](https://portal.azure.com) biedt *alleen* ondersteuning voor het maken van één data base in Azure SQL database en *alleen* vanuit een BACPAC-bestand dat is opgeslagen in Azure Blob Storage.

Het is momenteel niet mogelijk om een Data Base te migreren naar een [beheerd exemplaar](sql-database-managed-instance.md) vanuit een BACPAC-bestand met behulp van Azure PowerShell. Gebruik in plaats daarvan SQL Server Management Studio of SQLPackage.

> [!NOTE]
> Machines voor het verwerken van import/export-aanvragen die zijn ingediend via de Azure Portal of Power shell moeten het BACPAC-bestand opslaan, evenals tijdelijke bestanden die worden gegenereerd door het Framework van de Gegevenslaagtoepassing (DacFX). De benodigde schijf ruimte varieert aanzienlijk tussen data bases met dezelfde grootte en kan Maxi maal drie keer zoveel schijf ruimte vereisen als de data base. Machines met het verzoek om te importeren/exporteren hebben 450GB lokale schijf ruimte. Als gevolg hiervan kunnen sommige aanvragen mislukken met de fout `There is not enough space on the disk`. In dit geval is de tijdelijke oplossing om sqlpackage. exe uit te voeren op een computer met voldoende lokale schijf ruimte. We raden u aan SqlPackage-data bases die groter zijn dan 150 GB te importeren/exporteren om dit probleem te voor komen.

1. Als u vanuit een BACPAC-bestand wilt importeren in een nieuwe Data Base met behulp van de Azure Portal, opent u de juiste database server pagina en selecteert u op de werk balk de optie **Data Base importeren**.  

   ![Data base-Import1](./media/sql-database-import/sql-server-import-database.png)

1. Selecteer het opslag account en de container voor het BACPAC-bestand en selecteer vervolgens het BACPAC-bestand waaruit u wilt importeren.

1. Geef de nieuwe grootte van de database (meestal hetzelfde als oorsprong) en de bestemming van SQL Server-referenties opgeven. Zie [Create Data Base](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current)(Engelstalig) voor een lijst met mogelijke waarden voor een nieuwe Azure-SQL database.

   ![Data base-import2](./media/sql-database-import/sql-server-import-database-settings.png)

1. Klik op **OK**.

1. Als u de voortgang van een import bewerking wilt controleren, opent u de server pagina van de data base en selecteert u de **geschiedenis importeren/exporteren**onder **instellingen**. Wanneer het importeren is gelukt, is de status **voltooid** .

   ![Status van data base importeren](./media/sql-database-import/sql-server-import-database-history.png)

1. Als u wilt controleren of de data base Live is op de database server, selecteert u **SQL-data bases** en controleert u of de nieuwe Data Base **online**is.

## <a name="using-sqlpackage"></a>SqlPackage gebruiken

Zie [para meters en eigenschappen importeren](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties)om een SQL Server-Data Base te importeren met behulp van het opdracht regel hulpprogramma [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) . SqlPackage heeft de nieuwste [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) en [SQL Server Data tools voor Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). U kunt de meest recente [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) ook downloaden via het micro soft Download centrum.

Voor schaal baarheid en prestaties raden we u aan SqlPackage te gebruiken in de meeste productie omgevingen, in plaats van de Azure Portal te gebruiken. Zie voor een SQL Server klant advies team blog over het migreren met behulp van `BACPAC`-bestanden [migreren van SQL Server naar Azure SQL database met behulp van BACPAC-bestanden](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Met de volgende SqlPackage-opdracht wordt de **AdventureWorks2008R2** -data base uit de lokale opslag geïmporteerd naar een Azure SQL database-server met de naam **mynewserver20170403**. Er wordt een nieuwe data base gemaakt met de naam **myMigratedDatabase** met een **Premium** -servicelaag en een **P6** -service doelstelling. Deze waarden als geschikt is voor uw omgeving te wijzigen.

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=<migratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Als u verbinding wilt maken met een SQL Database Server die één data base van achter een bedrijfs firewall beheert, moet de firewall poort 1433 open hebben. U moet een [punt-naar-site-verbinding](sql-database-managed-instance-configure-p2s.md) of een snelle route verbinding hebben om verbinding te maken met een beheerd exemplaar.

In dit voorbeeld laat zien hoe een database met behulp van SqlPackage met Universal verificatie van Active Directory importeren.

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="using-powershell"></a>PowerShell gebruiken

> [!NOTE]
> [Een beheerd exemplaar](sql-database-managed-instance.md) biedt momenteel geen ondersteuning voor het migreren van een Data Base naar een exemplaar database vanuit een BACPAC-bestand met behulp van Azure PowerShell. Gebruik SQL Server Management Studio of SQLPackage om in een beheerd exemplaar te importeren.

> [!NOTE]
> Op de machines voor het verwerken van import/export-aanvragen die zijn ingediend via de portal of Power shell moet het Bacpac-bestand worden opgeslagen, evenals tijdelijke bestanden die worden gegenereerd door Application Framework (DacFX). De benodigde schijf ruimte varieert aanzienlijk van Db's met dezelfde grootte en kan Maxi maal drie keer van de grootte van de data base duren. Machines met het verzoek om te importeren/exporteren hebben 450GB lokale schijf ruimte. Als gevolg hiervan kunnen sommige aanvragen mislukken met de fout ' er is onvoldoende ruimte op de schijf '. In dit geval is de tijdelijke oplossing om sqlpackage. exe uit te voeren op een computer met voldoende lokale schijf ruimte. Bij het importeren/exporteren van data bases die groter zijn dan 150 GB, gebruikt u SqlPackage om dit probleem te voor komen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> De module Power shell Azure Resource Manager (RM) wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkeling is voor de module AZ. SQL. De AzureRM-module blijft oplossingen ontvangen tot ten minste december 2020.  De argumenten voor de opdrachten in de module AZ en in de AzureRm-modules zijn aanzienlijk identiek. Zie [Inleiding tot de nieuwe Azure PowerShell AZ-module](/powershell/azure/new-azureps-module-az)voor meer informatie over de compatibiliteit.

Gebruik de cmdlet [New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport) om een aanvraag voor het importeren van een Data Base naar de Azure SQL database-service te verzenden. Het importeren kan enige tijd duren, afhankelijk van de grootte van de database.

```powershell
$importRequest = New-AzSqlDatabaseImport -ResourceGroupName "<resourceGroupName>" `
    -ServerName "<serverName>" -DatabaseName "<databaseName>" `
    -DatabaseMaxSizeBytes "<databaseSizeInBytes>" -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzStorageAccountKey `
        -ResourceGroupName "<resourceGroupName>" -StorageAccountName "<storageAccountName>").Value[0] `
        -StorageUri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
        -Edition "Standard" -ServiceObjectiveName "P6" `
        -AdministratorLogin "<userId>" `
        -AdministratorLoginPassword $(ConvertTo-SecureString -String "<password>" -AsPlainText -Force)
```

U kunt de cmdlet [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) gebruiken om de voortgang van het importeren te controleren. Als de cmdlet direct na de aanvraag wordt uitgevoerd, wordt er meestal `Status: InProgress`geretourneerd. Het importeren is voltooid wanneer u `Status: Succeeded`ziet.

```powershell
$importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink

[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress") {
    $importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}

[Console]::WriteLine("")
$importStatus
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Gebruik de opdracht [AZ-SQL-DB-import](/cli/azure/sql/db#az-sql-db-import) om een aanvraag voor het importeren van een Data Base naar de Azure SQL database-service te verzenden. Het importeren kan enige tijd duren, afhankelijk van de grootte van de database.

```azurecli
# get the storage account key
az storage account keys list --resource-group "<resourceGroup>" --account-name "<storageAccount>"

az sql db import --resource-group "<resourceGroup>" --server "<server>" --name "<database>" `
    --storage-key-type "StorageAccessKey" --storage-key "<storageAccountKey>" `
    --storage-uri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
    -u "<userId>" -p "<password>"
```

* * *

> [!TIP]
> Zie [een Data Base importeren uit een BACPAC-bestand](scripts/sql-database-import-from-bacpac-powershell.md)voor een ander voor beeld van een script.

## <a name="limitations"></a>Beperkingen

Importeren naar een database in een elastische pool wordt niet ondersteund. U kunt gegevens importeren in één data base en vervolgens de data base verplaatsen naar een elastische pool.

## <a name="import-using-wizards"></a>Importeren met behulp van de wizards

Ook kunt u deze wizards.

- [Wizard data-tier Application importeren in SQL Server Management Studio](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard).
- [SQL Server wizard Importeren en exporteren](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard).

## <a name="next-steps"></a>Volgende stappen

- Voor informatie over het maken van verbinding met en het uitvoeren van een query op een geïmporteerde SQL Database raadpleegt u [Quick Start: Azure SQL database: gebruik SQL Server Management Studio om verbinding te maken en gegevens op te vragen](sql-database-connect-query-ssms.md).
- Raadpleeg dit blogartikel van het SQL Server-klantadviesteam over migratie met behulp van BACPAC-bestanden: [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407) (Migreren van SQL Server naar Azure SQL Database met BACPAC-bestanden).
- Zie [SQL Server Data Base Migration to Azure SQL database](sql-database-single-database-migrate.md)voor een bespreking van het volledige SQL Server database migratie proces, met inbegrip van de aanbevelingen voor de prestaties.
- Zie [Azure Storage Security Guide (Engelstalig](https://docs.microsoft.com/azure/storage/common/storage-security-guide)) voor meer informatie over het veilig beheren en delen van opslag sleutels en gedeelde toegangs handtekeningen.
