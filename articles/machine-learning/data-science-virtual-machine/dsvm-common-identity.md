---
title: Een algemene identiteit instellen
titleSuffix: Azure Data Science Virtual Machine
description: Informatie over het maken van algemene gebruikersaccounts die kunnen worden gebruikt voor meerdere virtuele Machines voor Datatechnologie. U kunt Azure Active Directory of een on-premises Active Directory gebruiken voor verificatie van gebruikers aan de Data Science Virtual Machine.
keywords: deep learning, AI, hulpprogramma's voor data science, virtuele machine voor datatechnologie, georuimtelijke analyses, team data science process
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 05/08/2018
ms.openlocfilehash: 44f1f7ae3b290e1dbf01877f3881e1d95a238446
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/01/2019
ms.locfileid: "70208144"
---
# <a name="set-up-a-common-identity-on-a-data-science-virtual-machine"></a>Stel een gemeen schappelijke identiteit in op een Data Science Virtual Machine

Op een Microsoft Azure virtuele machine (VM), met inbegrip van een Data Science Virtual Machine (DSVM), maakt u lokale gebruikers accounts tijdens het inrichten van de VM. Gebruikers worden vervolgens geverifieerd aan de virtuele machine met behulp van deze referenties. Als u meerdere virtuele machines hebt waartoe uw gebruikers toegang moeten hebben, kan het beheren van referenties zeer onlastig zijn. Een uitstekende oplossing is het implementeren van algemene gebruikers accounts en-beheer via een op standaarden gebaseerde ID-provider. Met deze aanpak kunt u één set met referenties gebruiken om toegang te krijgen tot meerdere resources op Azure, met inbegrip van meerdere Dsvm.

Active Directory is een populaire ID-provider en wordt ondersteund in azure als een Cloud service en als een on-premises Directory. U kunt Azure Active Directory (Azure AD) of het on-premises Active Directory om gebruikers op een zelfstandige DSVM of een Datatechnologie-cluster in een schaalset voor virtuele machine van Azure te verifiëren. U doen dit door de DSVM-exemplaren toevoegen aan een Active Directory-domein.

Als u Active Directory al hebt, kunt u deze gebruiken als uw algemene ID-provider. Als u niet beschikt over Active Directory, kunt u een beheerd Active Directory exemplaar uitvoeren op Azure via [Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/) (Azure AD DS).

De documentatie voor [Azure AD](https://docs.microsoft.com/azure/active-directory/) bevat gedetailleerde [beheer instructies](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution), inclusief informatie over het verbinden van Azure AD met uw on-premises Directory als u er een hebt.

In dit artikel wordt beschreven hoe u een volledig beheerde Active Directory domein service in azure instelt met behulp van Azure AD DS. U kunt vervolgens uw Dsvm toevoegen aan het beheerde Active Directory domein. Deze benadering stelt gebruikers in staat om toegang te krijgen tot een groep Dsvm (en andere Azure-bronnen) via een gemeen schappelijk gebruikers account en referenties.

## <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Een volledig beheerde Active Directory-domein in Azure instellen

Azure AD DS, kunt u eenvoudig uw identiteiten beheren door te voorzien in een volledig beheerde service in Azure. Op deze Active Directory-domein beheert u gebruikers en groepen. Voer de volgende stappen uit om een door Azure gehoste Active Directory domein en gebruikers accounts in uw directory in te stellen:

1. In de Azure-portal, moet u de gebruiker toevoegen aan Active Directory: 

   1. Meld u aan bij het [Azure Active Directory-beheer centrum](https://aad.portal.azure.com) met behulp van een account dat een globale beheerder voor de Directory is.
    
   1. Selecteer **Azure Active Directory** en vervolgens **Gebruikers en groepen**.
    
   1. Selecteer in **gebruikers en groepen** **alle gebruikers**en selecteer vervolgens **nieuwe gebruiker**.
   
           The **User** pane opens:
      
      ![Het deelvenster 'Gebruiker'](./media/add-user.png)
    
   1. Voer de details voor de gebruiker in, zoals **Naam** en **Gebruikersnaam**. Het domeingedeelte van de gebruikersnaam moet de eerste instantie domein naam '[naam].onmicrosoft.com' of een geverifieerde, niet-gefedereerde [aangepaste domeinnaam](../../active-directory/add-custom-domain.md) zoals 'contoso.com'.
    
   1. Kopieer of noteer het gegenereerde gebruikerswachtwoord zodat u dit aan de gebruiker kunt doorgeven nadat dit proces is voltooid.
    
   1. U kunt er ook voor kiezen om de informatie bij **Profiel**, **Groepen** of **Directory-rol** in te vullen voor de gebruiker. 
    
   1. Onder **gebruiker**, selecteert u **maken**.
    
   1. Het gegenereerde wacht woord veilig distribueren naar de nieuwe gebruiker zodat deze zich kan aanmelden.

1. Maak een Azure AD DS-exemplaar. Volg de instructies in [enable Azure Active Directory Domain Services using the Azure Portal](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started) (de sectie ' een instantie maken en basis instellingen configureren '). Het is belangrijk om bij te werken van de bestaande gebruikerswachtwoorden in Active Directory, zodat het wachtwoord in Azure AD DS is gesynchroniseerd. Het is ook belang rijk om DNS toe te voegen aan Azure AD DS, zoals wordt beschreven onder ' Vul de velden in het venster basis principes van de Azure Portal om een Azure AD DS-exemplaar te maken ' in die sectie.

1. Maak een afzonderlijk DSVM-subnet in het virtuele netwerk dat u hebt gemaakt in de sectie ' het virtuele netwerk maken en configureren ' van de voor gaande stap.
1. Maak een of meer DSVM-instanties in het DSVM-subnet.
1. Volg de [instructies](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-join-ubuntu-linux-vm ) om de DSVM toe te voegen aan Active Directory. 
1. Koppel een Azure Files-share om uw thuis-of notitieblokmap te hosten, zodat uw werk ruimte op elke machine kan worden gekoppeld. (Als u nauw keurig machtigingen op bestands niveau nodig hebt, moet NFS (Network File System) worden uitgevoerd op een of meer virtuele machines.)

   1. [Maak een Azure Files-share](../../storage/files/storage-how-to-create-file-share.md).
    
   2.  Koppel deze share aan de Linux-DSVM. Wanneer u **verbinding maken** selecteert voor de Azure Files share in uw opslag account in de Azure Portal, wordt de opdracht uitgevoerd in de bash-shell op de Linux DSVM wordt weer gegeven. De opdracht ziet er als volgt:
   
   ```
   sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
   ```
1. Stel dat u uw Azure Files-share in/data/Workspace. hebt gekoppeld Maak nu mappen voor elk van uw gebruikers in de share:/data/Workspace/user1,/data/Workspace/user2, enzovoort. Maak een `notebooks` map in de werkruimte van elke gebruiker. 
1. Symbolische koppelingen maken voor `notebooks` in `$HOME/userx/notebooks/remote`.   

U hebt nu de gebruikers in uw Active Directory-exemplaar dat wordt gehost in Azure. Door Active Directory referenties te gebruiken, kunnen gebruikers zich aanmelden bij elk DSVM (SSH of JupyterHub) dat is gekoppeld aan Azure AD DS. Omdat de werkruimte voor gebruikers op een Azure-bestandsshare is, hebben gebruikers toegang tot hun laptops en andere werkzaamheden van een DSVM wanneer ze JupyterHub.

Voor automatisch schalen, kunt u een virtuele-machineschaalset maken van een pool van virtuele machines die zijn gekoppeld aan het domein op deze manier en met de gedeelde schijf die is gekoppeld. Gebruikers kunnen zich aanmelden bij elke beschik bare computer in de schaalset voor virtuele machines en toegang hebben tot de gedeelde schijf waarop hun notitie blokken zijn opgeslagen. 

## <a name="next-steps"></a>Volgende stappen

* [Veilig opslaan van referenties voor toegang tot cloud-bronnen](dsvm-secure-access-keys.md)



