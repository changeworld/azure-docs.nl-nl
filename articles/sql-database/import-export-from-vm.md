---
title: Een SQL database importeren of exporteren
description: Een Azure-SQL database importeren of exporteren zonder dat Azure-Services toegang hebben tot de server.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/08/2020
ms.openlocfilehash: 9f694f3f0ec740d0a4e8dc4e6bf8845c408802c8
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75897843"
---
# <a name="import-or-export-an-azure-sql-database-without-allowing-azure-services-to-access-the-server"></a>Een Azure-SQL database importeren of exporteren zonder dat Azure-Services toegang hebben tot de server

In dit artikel leest u hoe u een Azure-SQL database importeert of exporteert wanneer *Azure-Services* op de Azure SQL-Server is ingesteld op *uitgeschakeld* . De werk stroom gebruikt een virtuele machine van Azure om SqlPackage uit te voeren om de import-of export bewerking uit te voeren.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure Portal](https://portal.azure.com/).

## <a name="create-the-azure-virtual-machine"></a>De virtuele Azure-machine maken

Maak een virtuele machine van Azure door de knop **implementeren in azure** te selecteren.

Met deze sjabloon kunt u een eenvoudige virtuele Windows-machine implementeren met een aantal verschillende opties voor de Windows-versie, met behulp van de meest recente versie van patches. Hiermee wordt een VM met a2-grootte in de locatie van de resource groep geïmplementeerd en wordt de Fully Qualified Domain Name van de virtuele machine geretourneerd.
<br><br>

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-simple-windows%2Fazuredeploy.json" target="_blank">
    <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

Zie [een zeer eenvoudige implementatie van een virtuele Windows-machine](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)voor meer informatie.


## <a name="connect-to-the-virtual-machine"></a>Verbinding maken met de virtuele machine

De volgende stappen laten zien hoe u verbinding kunt maken met uw virtuele machine via een verbinding met een extern bureau blad.

1. Nadat de implementatie is voltooid, gaat u naar de virtuele machine-resource.

    ![VM](./media/import-export-from-vm/vm.png)  

2. Selecteer **Verbinden**.

   Er wordt een Remote Desktop Protocol (RDP-bestand)-formulier weergegeven met het openbare IP-adres en poort nummer voor de virtuele machine.

   ![RDP-formulier](./media/import-export-from-vm/rdp.png)  

3. Selecteer **RDP-bestand downloaden**.

   > [!NOTE]
   > U kunt ook SSH verbinding maken met uw virtuele machine.

4. Sluit de **verbinding maken met virtuele machine** formulier.
5. Open het gedownloade RDP-bestand om verbinding met de VM te maken.
6. Wanneer u hierom wordt gevraagd, selecteert u **Connect**. Op een Mac hebt u een RDP-client nodig, zoals deze [Extern-bureaubladclient](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) uit de Mac App Store.

7. Voer de gebruikersnaam en wachtwoord die u hebt opgegeven bij het maken van de virtuele machine en kies vervolgens **OK**.

8. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Kies **Ja** of **doorgaan** om door te gaan met de verbinding.



## <a name="install-sqlpackage"></a>SqlPackage installeren

[Down load en installeer de meest recente versie van SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage-download).




Zie [SqlPackage. exe](https://docs.microsoft.com/sql/tools/sqlpackage)(Engelstalig) voor meer informatie.

## <a name="create-a-firewall-rule-to-allow-the-vm-access-to-the-database"></a>Een firewall regel maken om de VM toegang tot de data base te geven

Voeg het open bare IP-adres van de virtuele machine toe aan de firewall van de SQL Database-Server.

Met de volgende stappen maakt u een IP-firewall regel op server niveau voor het open bare IP-adres van uw virtuele machine en maakt u connectiviteit vanaf de virtuele machine mogelijk.

1. Selecteer **SQL-data bases** in het menu aan de linkerkant en selecteer vervolgens uw Data Base op de pagina **SQL-data bases** . De overzichts pagina voor de data base wordt geopend, met de volledig gekwalificeerde server naam (zoals **servername.database.Windows.net**) en biedt opties voor verdere configuratie.

2. Kopieer deze volledig gekwalificeerde server naam om te gebruiken bij het maken van verbinding met uw server en de bijbehorende data bases.

   ![servernaam](./media/sql-database-get-started-portal/server-name.png)

3. Selecteer **Serverfirewall instellen** op de werkbalk. De pagina **Firewallinstellingen** voor de databaseserver wordt geopend.

   ![IP-firewallregel op serverniveau](./media/sql-database-get-started-portal/server-firewall-rule.png)

4. Kies **client-IP toevoegen** op de werk balk om het open bare IP-adres van uw virtuele machine toe te voegen aan een nieuwe IP-firewall regel op server niveau. Een IP-firewallregel op serverniveau kan poort 1433 openen voor een individueel IP-adres of voor een bereik van IP-adressen.

5. Selecteer **Opslaan**. Er wordt een IP-firewall regel op server niveau gemaakt voor het open bare IP-adres van uw virtuele machine, waarmee poort 1433 wordt geopend op de SQL Database Server.

6. Sluit de pagina **Firewallinstellingen**.



## <a name="export-a-database-using-sqlpackage"></a>Een Data Base exporteren met behulp van SqlPackage

Zie [para meters en eigenschappen exporteren](https://docs.microsoft.com/sql/tools/sqlpackage#export-parameters-and-properties)als u een SQL database wilt exporteren met behulp van het [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) -opdracht regel programma. Het hulp programma SqlPackage wordt geleverd met de nieuwste versies van [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) en [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt), of u kunt de meest recente versie van [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage-download)downloaden.

We raden u aan het SqlPackage-hulp programma te gebruiken voor schaal en prestaties in de meeste productie omgevingen. Raadpleeg dit blogartikel van het SQL Server-klantadviesteam over migratie met behulp van BACPAC-bestanden: [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migreren van SQL Server naar Azure SQL Database met BACPAC-bestanden).

In dit voor beeld ziet u hoe u een Data Base exporteert met SqlPackage. exe met Active Directory universele verificatie. Vervang door waarden die specifiek zijn voor uw omgeving.

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=<servername>.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```




## <a name="import-a-database-using-sqlpackage"></a>Een Data Base importeren met behulp van SqlPackage

Zie [para meters en eigenschappen importeren](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties)om een SQL Server-Data Base te importeren met behulp van het opdracht regel hulpprogramma [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) . SqlPackage heeft de nieuwste [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) en [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt). U kunt ook de meest recente versie van [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage-download)downloaden.

Voor schaal baarheid en prestaties raden we u aan SqlPackage te gebruiken in de meeste productie omgevingen, in plaats van de Azure Portal te gebruiken. Zie voor een SQL Server klant advies team blog over het migreren met behulp van `BACPAC`-bestanden [migreren van SQL Server naar Azure SQL database met behulp van BACPAC-bestanden](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Met de volgende SqlPackage-opdracht wordt de **AdventureWorks2017** -data base uit de lokale opslag naar een Azure SQL database-server geïmporteerd. Deze maakt u een nieuwe database met de naam **myMigratedDatabase** met een **Premium** servicelaag en een **P6** Servicedoelstelling. Deze waarden als geschikt is voor uw omgeving te wijzigen.

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=myMigratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2017.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Als u verbinding wilt maken met een SQL Database Server die één data base van achter een bedrijfs firewall beheert, moet de firewall poort 1433 open hebben. U moet een [punt-naar-site-verbinding](sql-database-managed-instance-configure-p2s.md) of een snelle route verbinding hebben om verbinding te maken met een beheerd exemplaar.

In dit voorbeeld laat zien hoe een database met behulp van SqlPackage met Universal verificatie van Active Directory importeren.

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="performance-considerations"></a>Prestatieoverwegingen

De export snelheid verschilt als gevolg van een groot aantal factoren (bijvoorbeeld een gegevensshape), zodat het onmogelijk is om te voors pellen welke snelheid moet worden verwacht. SqlPackage kan veel tijd in beslag nemen, vooral voor grote data bases.

Om de beste prestaties te krijgen, kunt u de volgende strategieën proberen:

1. Zorg ervoor dat er geen andere werk belasting wordt uitgevoerd op de data base. Het maken van een kopie vóór het exporteren is mogelijk de beste oplossing om ervoor te zorgen dat er geen andere werk belastingen worden uitgevoerd.
2. Verhoog de database serviceniveau doelstelling (SLO) voor een betere verwerking van de werk belasting voor exporteren (voornamelijk I/O-lees bewerkingen). Als de data base momenteel wordt GP_Gen5_4, zou een Bedrijfskritiek-laag bij het lezen van de werk belasting kunnen helpen.
3. Zorg ervoor dat geclusterde indexen met name voor grote tabellen zijn. 
4. Virtuele machines (Vm's) moeten zich in dezelfde regio bevinden als de data base om netwerk beperkingen te helpen voor komen.
5. Vm's moeten SSD hebben met voldoende grootte voor het genereren van tijdelijke artefacten voordat ze naar de Blob-opslag worden geüpload.
6. Vm's moeten voldoende kern-en geheugen configuratie hebben voor de specifieke data base.

## <a name="store-the-imported-or-exported-bacpac-file"></a>Sla het geïmporteerde of geëxporteerde op. BACPAC-bestand

Dit. Het BACPAC-bestand kan worden opgeslagen in [Azure-blobs](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)of [Azure files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction). 

Gebruik Azure Files om de beste prestaties te krijgen. SqlPackage werkt met het bestands systeem zodat het rechtstreeks toegang heeft tot Azure Files.

Gebruik Azure blobs, wat lager is dan een Premium Azure-bestands share, om de kosten te verlagen. Het is echter wel nodig om de te kopiëren [. BACPAC-bestand](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) tussen de BLOB en het lokale bestands systeem vóór de import-of export bewerking. Als gevolg hiervan zal het proces langer duren.

Uploaden of downloaden. BACPAC-bestanden, Zie [gegevens overdragen met AzCopy en Blob Storage](../storage/common/storage-use-azcopy-blobs.md)en [gegevens overdragen met AzCopy en File Storage](../storage/common/storage-use-azcopy-files.md).

Afhankelijk van uw omgeving moet u mogelijk [Azure Storage firewalls en virtuele netwerken configureren](../storage/common/storage-network-security.md).

## <a name="next-steps"></a>Volgende stappen

- Zie voor meer informatie over verbinding maken met en query uitvoeren op een geïmporteerde SQL-Database, [Quick Start: Azure SQL Database: gebruikt SQL Server Management Studio om verbinding maken met en gegevens op te vragen](sql-database-connect-query-ssms.md).
- Raadpleeg dit blogartikel van het SQL Server-klantadviesteam over migratie met behulp van BACPAC-bestanden: [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407) (Migreren van SQL Server naar Azure SQL Database met BACPAC-bestanden).
- Zie voor een discussie over de hele SQL Server-database migreren proces, inclusief aanbevelingen voor prestaties, [SQL Server-databasemigratie naar Azure SQL Database](sql-database-single-database-migrate.md).
- Voor informatie over het beheren en te delen opslagsleutels en gedeelde toegang handtekeningen veilig, Zie [Azure Storage-beveiligingshandleiding](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
