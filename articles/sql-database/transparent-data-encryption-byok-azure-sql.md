---
title: Door de klant beheerde transparante gegevens versleuteling (TDE)
description: Bring Your Own Key-ondersteuning (BYOK) voor Transparent Data Encryption (TDE) met Azure Key Vault voor SQL Database en Azure Synapse. TDE met BYOK overview, voor delen, hoe het werkt, overwegingen en aanbevelingen.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: azure-synapse
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 02/12/2020
ms.openlocfilehash: a29466ad5b261e1e2ce818d7b4a18260e35caaec
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79255650"
---
# <a name="azure-sql-transparent-data-encryption-with-customer-managed-key"></a>Azure SQL Transparent Data Encryption met door de klant beheerde sleutel

Azure SQL [transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) met door de klant beheerde sleutel maakt het Bring your own Key-scenario (BYOK) mogelijk voor gegevens bescherming in rust en stelt organisaties in staat om schei ding van taken in het beheer van sleutels en gegevens te implementeren. Met door de klant beheerde transparante gegevens versleuteling is de klant verantwoordelijk voor en in een volledig beheer van een belang rijke levenscyclus beheer (het maken van sleutels, uploaden, draaien, verwijderen), machtigingen voor sleutel gebruik en het controleren van bewerkingen op sleutels.

In dit scenario is de sleutel die wordt gebruikt voor het versleutelen van de database versleutelings sleutel (DEK), de zogeheten TDE-Protector, een door de klant beheerde asymmetrische sleutel die is opgeslagen in een door de klant en door de klant beheerde [Azure Key Vault (Azure)](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault), een op de cloud gebaseerd extern sleutel beheersysteem. Key Vault is een Maxi maal beschik bare en schaal bare beveiligde opslag voor RSA cryptografische sleutels, optioneel ondersteund door FIPS 140-2 level 2 gevalideerde hardware security modules (Hsm's). Het biedt geen directe toegang tot een opgeslagen sleutel, maar voorziet in Services voor het versleutelen/ontsleutelen met behulp van de sleutel voor de gemachtigde entiteiten. De sleutel kan worden gegenereerd door de sleutel kluis, geïmporteerd of [overgedragen naar de sleutel kluis van een on-premises HSM-apparaat](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys).

Voor Azure SQL Database en Azure Synapse wordt de TDE-Protector ingesteld op het niveau van de logische server en overgenomen door alle versleutelde data bases die aan die server zijn gekoppeld. Voor Azure SQL Managed instance wordt de TDE-Protector ingesteld op het niveau van de instantie en overgenomen door alle versleutelde data bases op die instantie. De term *Server* verwijst naar SQL database logische server en beheerde instantie in dit document, tenzij anders aangegeven.

> [!IMPORTANT]
> Voor degenen die gebruikmaken van service-beheerde TDE die willen beginnen met door de klant beheerde TDE, blijft de gegevens versleuteld tijdens het proces van overschakeling en wordt er geen downtime of hercodering van de database bestanden weer gegeven. Wanneer u overschakelt van een door een service beheerde sleutel naar een door de klant beheerde sleutel, hoeft u de DEK alleen opnieuw te versleutelen, wat een snelle en online bewerking is.

## <a name="benefits-of-the-customer-managed-tde"></a>Voor delen van de door de klant beheerde TDE

Door de klant beheerde TDE biedt de volgende voor delen voor de klant:

- Volledige en nauw keurige controle over het gebruik en het beheer van de TDE-Protector;

- Transparantie van het gebruik van TDE-Protector;

- Mogelijkheid om schei ding van taken in het beheer van sleutels en gegevens binnen de organisatie te implementeren;

- Key Vault beheerder kan belang rijke toegangs machtigingen intrekken om de versleutelde data base ontoegankelijk te maken.

- Centraal beheer van sleutels in azure;

- Meer vertrouwen van uw eind klanten, omdat Azure is ontworpen zodat micro soft geen versleutelings sleutels kan zien of extra heren.

## <a name="how-customer-managed-tde-works"></a>Hoe door de klant beheerde TDE werkt

![Instellen en werken van de door de klant beheerde TDE](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-roles.PNG)

Om de server in staat te stellen TDE-beveiliging te gebruiken die is opgeslagen in azure voor versleuteling van de DEK, moet de sleutel kluis beheerder de volgende toegangs rechten verlenen aan de server met behulp van de unieke AAD-identiteit:

- **Get** -voor het ophalen van het open bare onderdeel en de eigenschappen van de sleutel in de Key Vault

- **wrapKey** : om te kunnen beveiligen (versleutelen) dek

- **sleutel uitpakken** : voor het opheffen van de beveiliging (ontsleutelen) dek

De sleutel kluis beheerder kan ook [logboek registratie inschakelen voor gebeurtenissen van sleutel kluis controle](https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault), zodat ze later kunnen worden gecontroleerd.

Wanneer de server is geconfigureerd voor het gebruik van een TDE-Protector van Azure, stuurt de server de DEK van elke TDE-Data Base naar de sleutel kluis voor versleuteling. Sleutel kluis retourneert de versleutelde DEK, die vervolgens wordt opgeslagen in de gebruikers database.

Wanneer dit nodig is, verzendt de server beveiligde DEK naar de sleutel kluis voor ontsleuteling.

Audi tors kunnen Azure Monitor gebruiken om de sleutel kluis audit event-logboeken te bekijken als logboek registratie is ingeschakeld.

[!INCLUDE [sql-database-akv-permission-delay](includes/sql-database-akv-permission-delay.md)]

## <a name="requirements-for-configuring-customer-managed-tde"></a>Vereisten voor het configureren van door de klant beheerde TDE

### <a name="requirements-for-configuring-akv"></a>Vereisten voor het configureren van Azure

- De sleutel kluis en het SQL Database/beheerde exemplaar moeten deel uitmaken van dezelfde Azure Active Directory Tenant. De cross-Tenant sleutel kluis en server interacties worden niet ondersteund. Als u later resources wilt verplaatsen, moet TDE met Azure opnieuw worden geconfigureerd. Meer informatie over het [verplaatsen van resources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).

- De functie voor [voorlopig verwijderen](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) moet zijn ingeschakeld op de sleutel kluis om te beschermen tegen onbedoelde sleutel (of sleutel kluis) voor het verwijderen van gegevens verlies. Voorlopig verwijderde bronnen worden 90 dagen bewaard, tenzij de klant in de tussen tijd is hersteld of verwijderd. De *herstel* -en *opschoon* acties hebben hun eigen machtigingen die zijn gekoppeld aan een sleutel kluis toegangs beleid. De functie voor voorlopig verwijderen is standaard uitgeschakeld en kan worden ingeschakeld via [Power shell](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell#enabling-soft-delete) of [cli](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli#enabling-soft-delete). Deze kan niet worden ingeschakeld via Azure Portal.  

- Verleen de SQL Database Server of het beheerde exemplaar toegang tot de sleutel kluis (Get, wrapKey, sleutel uitpakken) met behulp van de Azure Active Directory identiteit. Wanneer u Azure Portal gebruikt, wordt de Azure AD-identiteit automatisch gemaakt. Wanneer u Power shell of CLI gebruikt, moet de Azure AD-identiteit expliciet worden gemaakt en moet de voltooiing ervan worden gecontroleerd. Zie [Configure TDe with BYOK](transparent-data-encryption-byok-azure-sql-configure.md) en [Configure TDe with BYOK for Managed instance](https://aka.ms/sqlmibyoktdepowershell) voor gedetailleerde stapsgewijze instructies voor het gebruik van Power shell.

- Wanneer u Firewall met Azure gebruikt, moet u *de optie vertrouwde micro soft-Services toestaan om de firewall te omzeilen*, in te scha kelen.

### <a name="requirements-for-configuring-tde-protector"></a>Vereisten voor het configureren van TDE-beveiliging

- TDE-Protector kan alleen asymmetrische, RSA 2048-of RSA HSM 2048-sleutel zijn.

- De datum en tijd waarop de sleutel wordt geactiveerd, moeten in het verleden liggen. De verval datum (indien ingesteld) moet een datum en tijd in de toekomst zijn.

- De sleutel moet de *ingeschakelde* status hebben.

- Als u een bestaande sleutel in de sleutel kluis importeert, moet u deze opgeven in de ondersteunde bestands indelingen (. pfx,. byok of. back-up).

## <a name="recommendations-when-configuring-customer-managed-tde"></a>Aanbevelingen voor het configureren van door de klant beheerde TDE

### <a name="recommendations-when-configuring-akv"></a>Aanbevelingen voor het configureren van Azure

- U kunt in totaal Maxi maal 500 Algemeen of 200 Bedrijfskritiek data bases koppelen aan een sleutel kluis in één abonnement om hoge Beschik baarheid te garanderen wanneer de server toegang heeft tot de TDE-beveiliging in de sleutel kluis. Deze cijfers zijn gebaseerd op de ervaring en gedocumenteerd in de [sleutel kluis-service limieten](https://docs.microsoft.com/azure/key-vault/key-vault-service-limits). Dit is om te voor komen dat u problemen ondervindt na een failover van de server, omdat deze wordt geactiveerd als veel belang rijke bewerkingen voor de kluis als er data bases op die server zijn.

- Stel een resource vergrendeling in voor de sleutel kluis om te bepalen wie deze kritieke resource kan verwijderen en om onbedoelde of ongeoorloofde verwijdering te voor komen. Meer informatie over [resource vergrendelingen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources).

- Controle en rapportage inschakelen voor alle versleutelings sleutels: sleutel kluis biedt logboeken die eenvoudig kunnen worden geïnjecteerd in andere hulpprogram ma's voor beveiligings informatie en beheer van gebeurtenissen. Operations Management Suite [log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault) is een voor beeld van een service die al is geïntegreerd.

- Koppel elke server aan twee sleutel kluizen die zich in verschillende regio's bevinden, en bewaar hetzelfde sleutel materiaal om hoge Beschik baarheid van versleutelde data bases te garanderen. Markeer alleen de sleutel van de sleutel kluis in dezelfde regio als een TDE-Protector. Systeem zal gebruiken

### <a name="recommendations-when-configuring-tde-protector"></a>Aanbevelingen voor het configureren van TDE-beveiliging
- Bewaar een kopie van de TDE-protector op een veilige plaats of borg aan de borg service.

- Als de sleutel wordt gegenereerd in de sleutel kluis, maakt u een sleutel back-up voordat u de sleutel voor de eerste keer gebruikt in Azure. U kunt back-ups alleen herstellen naar een Azure Key Vault. Meer informatie over de [back-up-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey) -opdracht.

- Maak een nieuwe back-up wanneer er wijzigingen worden aangebracht in de sleutel (bijvoorbeeld sleutel kenmerken, tags, Acl's).

- **Bewaar vorige versies** van de sleutel in de sleutel kluis bij het draaien van sleutels, zodat oudere database back-ups kunnen worden hersteld. Wanneer de TDE-Protector voor een Data Base wordt gewijzigd, worden de oude back-ups van de Data Base **niet bijgewerkt** voor gebruik van de meest recente TDe-Protector. Op het moment van terugzetten heeft elke back-up de TDE-Protector nodig die is versleuteld met tijdens het maken. U kunt de volgende stappen uitvoeren om de [transparent Data Encryption beveiliging te draaien met behulp van Power shell](transparent-data-encryption-byok-azure-sql-key-rotation.md).

- Bewaar alle eerder gebruikte sleutels in azure, zelfs na het overschakelen naar door service beheerde sleutels. Het zorgt ervoor dat database back-ups kunnen worden hersteld met de TDE-beveiligingen die zijn opgeslagen in Azure.  TDE-beveiligingen die zijn gemaakt met Azure Key Vault moeten worden behouden totdat alle resterende opgeslagen back-ups zijn gemaakt met door service beheerde sleutels. Herstel bare back-ups van deze sleutels maken met [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey).

- Als u een mogelijk gemanipuleerde sleutel tijdens een beveiligings incident wilt verwijderen zonder het risico van gegevens verlies, volgt u de stappen in de [sleutel remove a potentieel aangetast](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md).

## <a name="inaccessible-tde-protector"></a>Ontoegankelijke TDE-beveiliging

Wanneer transparante gegevens versleuteling is geconfigureerd voor het gebruik van een door de klant beheerde sleutel, is voortdurende toegang tot de TDE-Protector vereist voor de data base om online te blijven. Als de server geen toegang meer heeft tot de door de klant beheerde TDE-Protector in azure, wordt in Maxi maal tien minuten een Data Base gestart met het weigeren van alle verbindingen met het bijbehorende fout bericht en wordt de status gewijzigd in niet *toegankelijk*. De enige actie die is toegestaan voor een Data Base met de status unaccessible, wordt deze verwijderd.

> [!NOTE]
> Als de data base niet toegankelijk is vanwege een onregelmatige netwerk storing, is er geen actie vereist en worden de data bases automatisch weer online gezet.

Nadat de toegang tot de sleutel is hersteld, is voor de Data Base weer online extra tijd en stappen vereist, afhankelijk van de tijd die is verstreken zonder toegang tot de sleutel en de grootte van de gegevens in de Data Base:

- Als de sleutel toegang binnen acht uur wordt hersteld, wordt de data base binnen het volgende uur automatisch opnieuw geactiveerd.

- Als de sleutel toegang na meer dan acht uur wordt hersteld, is het automatisch herstellen niet mogelijk en hebt u voor de data base extra stappen nodig op de portal. Dit kan een aanzienlijke hoeveelheid tijd duren, afhankelijk van de grootte van de data base. Zodra de Data Base weer online is, eerder geconfigureerde instellingen op server niveau, zoals [failover-groeps](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group) configuratie, punt-in-time-herstel geschiedenis en tags **gaan verloren**. Daarom is het raadzaam om een meldings systeem te implementeren waarmee u de onderliggende sleutel toegangs problemen binnen acht uur kunt identificeren en oplossen.

### <a name="accidental-tde-protector-access-revocation"></a>Onopzettelijke toegang tot TDE-Protector

Het kan gebeuren dat iemand met voldoende toegangs rechten voor de sleutel kluis per ongeluk server toegang tot de sleutel uitschakelt door:

- de *Get*-, *wrapKey*-, *sleutel uitpakken* -machtigingen van de sleutel kluis intrekken van de server

- de sleutel verwijderen

- de sleutel kluis verwijderen

- de firewall regels van de sleutel kluis wijzigen

- de beheerde identiteit van de server in Azure Active Directory verwijderen

Meer informatie over [de algemene oorzaken voor het ontoegankelijk maken van de data base](https://docs.microsoft.com/sql/relational-databases/security/encryption/troubleshoot-tde?view=azuresqldb-current#common-errors-causing-databases-to-become-inaccessible).

## <a name="monitoring-of-the-customer-managed-tde"></a>Bewaking van de door de klant beheerde TDE

Als u de status van de Data Base wilt bewaken en waarschuwingen wilt inschakelen voor het verlies van toegang tot TDE-Protector, configureert u de volgende Azure-functies:
- [Azure resource Health](https://docs.microsoft.com/azure/service-health/resource-health-overview). Een ontoegankelijke data base die de toegang tot de TDE-Protector heeft verloren, wordt weer gegeven als ' niet beschikbaar ' nadat de eerste verbinding met de data base is geweigerd.
- [Activiteiten logboek](https://docs.microsoft.com/azure/service-health/alerts-activity-log-service-notifications) wanneer toegang tot de TDe-Protector in de door de klant beheerde sleutel kluis mislukt, worden de vermeldingen toegevoegd aan het activiteiten logboek.  Als u waarschuwingen voor deze gebeurtenissen maakt, kunt u zo snel mogelijk toegang herstellen.
- [Actie groepen](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) kunnen worden gedefinieerd om u meldingen en waarschuwingen te sturen op basis van uw voor keuren, bijvoorbeeld E-mail/SMS/push/Voice, Logic app, webhook, ITSM of Automation Runbook.

## <a name="database-backup-and-restore-with-customer-managed-tde"></a>Back-up en herstel van de data base met door de klant beheerde TDE

Wanneer een Data Base is versleuteld met TDE met behulp van een sleutel uit Key Vault, worden alle nieuw gegenereerde back-ups ook versleuteld met dezelfde TDE-Protector. Wanneer de TDE-beveiliging wordt gewijzigd, worden oude back-ups van de Data Base **niet bijgewerkt** voor gebruik van de meest recente TDe-Protector.

Als u een back-up wilt herstellen die is versleuteld met een TDE-Protector van Key Vault, moet u ervoor zorgen dat het sleutel materiaal beschikbaar is voor de doel server. Daarom is het raadzaam dat u alle oude versies van de TDE-Protector in de sleutel kluis bewaart, zodat database back-ups kunnen worden hersteld.

> [!IMPORTANT]
> Er kan op elk moment niet meer dan één TDE-protector worden ingesteld voor een server. De sleutel is gemarkeerd met de sleutel de standaard TDE-Protector maken op de Blade Azure Portal. Meerdere extra sleutels kunnen echter aan een server worden gekoppeld zonder dat ze als een TDE-protector worden gemarkeerd. Deze sleutels worden niet gebruikt voor het beveiligen van DEK, maar kunnen worden gebruikt tijdens het terugzetten vanuit een back-up, als het back-upbestand is versleuteld met de sleutel met de bijbehorende vinger afdruk.

Als de sleutel die nodig is voor het herstellen van een back-up niet langer beschikbaar is voor de doel server, wordt het volgende fout bericht weer gegeven in de Restore try: ' target server `<Servername>` heeft geen toegang tot alle Azure-Uri's die zijn gemaakt tussen \<Time Stamp #1 > en \<Time Stamp #2 >. Voer de bewerking opnieuw uit nadat alle Azure-Uri's zijn teruggezet.

Als u dit wilt beperken, voert u de cmdlet [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) uit voor de doel-SQL database logische server of [Get-AzSqlInstanceKeyVaultKey](/powershell/module/az.sql/get-azsqlinstancekeyvaultkey) voor het beheerde exemplaar van het doel om de lijst met beschik bare sleutels te retour neren en de ontbrekende waarden te identificeren. Om ervoor te zorgen dat alle back-ups kunnen worden hersteld, moet u ervoor zorgen dat de doel server voor het terugzetten toegang heeft tot alle benodigde sleutels. Deze sleutels hoeven niet als TDE-Protector te worden gemarkeerd.

Zie [een Azure SQL database herstellen](sql-database-recovery-using-backups.md)voor meer informatie over herstel van back-ups voor SQL database. Zie [een SQL-groep herstellen](../sql-data-warehouse/backup-and-restore.md)voor meer informatie over het herstel van back-ups voor SQL-groepen. Zie [Snelstartgids: een Data Base herstellen voor een beheerd exemplaar](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) voor het systeem eigen back-up/herstel van de SQL Server met een beheerd exemplaar.

Aanvullende overwegingen voor logboek bestanden: back-ups van logboek bestanden blijven versleuteld met de oorspronkelijke TDE-Protector, zelfs als deze is gedraaid en de data base nu een nieuwe TDE-Protector gebruikt.  Op het moment van terugzetten zijn beide sleutels nodig om de data base te herstellen.  Als het logboek bestand gebruikmaakt van een TDE-Protector die is opgeslagen in Azure Key Vault, is deze sleutel vereist tijdens de herstel tijd, zelfs als de data base is gewijzigd voor het gebruik van door de service beheerde TDE.

## <a name="high-availability-with-customer-managed-tde"></a>Hoge Beschik baarheid met door de klant beheerde TDE

Zelfs als er geen geo-redundantie is geconfigureerd voor de server, wordt het ten zeerste aanbevolen om de server te configureren voor het gebruik van twee verschillende sleutel kluizen in twee verschillende regio's met hetzelfde sleutel materiaal. U kunt dit doen door een TDE-Protector te maken met behulp van de primaire sleutel kluis die zich in dezelfde regio bevindt als de server en de sleutel te klonen in een sleutel kluis in een andere Azure-regio, zodat de server toegang heeft tot een tweede sleutel kluis als de primaire sleutel kluis exper Ience een storing tijdens het uitvoeren van de data base.

Gebruik de cmdlet backup-AzKeyVaultKey om de sleutel in een versleutelde indeling op te halen uit de primaire sleutel kluis en gebruik vervolgens de cmdlet Restore-AzKeyVaultKey en geef in de tweede regio een sleutel kluis op om de sleutel te klonen. U kunt ook Azure Portal gebruiken om een back-up-en herstel sleutel te maken. De sleutel in de secundaire sleutel kluis in een andere regio in de SE mag niet worden gemarkeerd als TDE-Protector en is niet zelfs toegestaan.

 Als er een storing optreedt in de primaire sleutel kluis en alleen dan wordt het systeem automatisch overgeschakeld naar de andere gekoppelde sleutel met dezelfde vinger afdruk in de secundaire sleutel kluis als deze bestaat. Houd er rekening mee dat de switch niet wordt uitgevoerd als TDE-Protector niet toegankelijk is vanwege ingetrokken toegangs rechten, of omdat de sleutel of sleutel kluis is verwijderd, omdat dit kan erop wijzen dat de klant opzettelijk de toegang tot de sleutel wil beperken.

![HA met één server](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-ha.png)

## <a name="geo-dr-and-customer-managed-tde"></a>Geo-DR en door de klant beheerde TDE

In de scenario's voor zowel [actieve geo-replicatie](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication) als voor [failover-groepen](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group) is voor elke server die betrokken is een afzonderlijke sleutel kluis vereist. deze moet zich op dezelfde locatie bevinden als de server in dezelfde Azure-regio. De klant is verantwoordelijk voor het bewaren van het belangrijkste materiaal in de sleutel kluizen, zodat geo-secundair synchroon is en dezelfde sleutel kan overnemen uit de lokale sleutel kluis als de primaire ontoegankelijk is vanwege een storing in de regio en een failover wordt geactiveerd. . Er kunnen Maxi maal vier secundairen worden geconfigureerd en koppelen (secundaire zones van de secundaire zones) wordt niet ondersteund.

Als u problemen wilt voor komen tijdens het maken of tijdens het tot stand brengen van geo-replicatie vanwege onvolledige sleutel materiaal, is het belang rijk om deze regels te volgen bij het configureren van door de klant beheerde TDE:

- Alle betrokken sleutel kluizen moeten dezelfde eigenschappen hebben en dezelfde toegangs rechten voor de betreffende servers.

- Alle betrokken sleutel kluizen moeten identieke sleutel materialen bevatten. Het is niet alleen van toepassing op de huidige TDE-Protector, maar op alle vorige TDE-beveiligingen die kunnen worden gebruikt in de back-upbestanden.

- De initiële installatie en rotatie van de TDE-Protector moeten eerst worden uitgevoerd op het secundaire en vervolgens op de primaire.

![Failover-groepen en geo-Dr](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-bcdr.png)

Als u een failover wilt testen, volgt u de stappen in het [overzicht van actieve geo-replicatie](sql-database-geo-replication-overview.md). Deze moet regel matig worden uitgevoerd om te bevestigen dat de toegangs machtigingen voor SQL op beide sleutel kluizen behouden blijven.

## <a name="next-steps"></a>Volgende stappen

U kunt ook de volgende Power shell-voorbeeld scripts controleren voor algemene bewerkingen met door de klant beheerde TDE:

- [De Transparent Data Encryption-Protector voor SQL Database draaien met behulp van Power shell](transparent-data-encryption-byok-azure-sql-key-rotation.md)

- [Een Transparent Data Encryption-Protector (TDE) voor SQL Database verwijderen met behulp van Power shell](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-remove-tde-protector)

- [Transparent Data Encryption beheren in een beheerd exemplaar met uw eigen sleutel met behulp van Power shell](https://docs.microsoft.com/azure/sql-database/scripts/transparent-data-encryption-byok-sql-managed-instance-powershell?toc=%2fpowershell%2fmodule%2ftoc.json)
