---
title: Gebruikers beheren in azure Block Chain workbench
description: Hoe u gebruikers beheert in azure Block Chain Workbench.
ms.date: 05/09/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 573ec477a3e75beb91f90da0545fb7d4c0f9bf39
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79252179"
---
# <a name="manage-users-in-azure-blockchain-workbench"></a>Gebruikers beheren in azure Block Chain workbench

Azure Block Chain Workbench bevat gebruikers beheer voor personen en organisaties die deel uitmaken van uw consortium.

## <a name="prerequisites"></a>Vereisten

Er is een implementatie van Block Chain Workbench vereist. Zie [Azure Block Chain Workbench-implementatie](deploy.md) voor meer informatie over de implementatie.

## <a name="add-azure-ad-users"></a>Azure AD-gebruikers toevoegen

Azure Block Chain Workbench maakt gebruik van Azure Active Directory (Azure AD) voor verificatie, Toegangs beheer en rollen. Gebruikers in de Azure AD-Tenant van Block Chain Workbench kunnen Block Chain Workbench verifiëren en gebruiken. Gebruikers toevoegen aan de toepassingsrol van de beheerder om te communiceren en acties uit te voeren.

Block Chain Workbench-gebruikers moeten zich in de Azure AD-Tenant bevinden voordat u ze aan toepassingen en rollen kunt toewijzen. Gebruik de volgende stappen om gebruikers toe te voegen aan Azure AD:

1.  Meld u aan bij de [Azure-portal](https://portal.azure.com).
2.  Selecteer uw account in de rechter bovenhoek en schakel over naar de Azure AD-Tenant die is gekoppeld aan block Chain Workbench.
3.  Selecteer **Azure Active Directory > gebruikers**. U ziet een lijst met gebruikers in uw Directory.
4.  Selecteer **nieuwe gebruiker**om gebruikers toe te voegen aan de Directory. Voor externe gebruikers selecteert u **nieuwe gast gebruiker**.

    ![Nieuwe gebruiker](./media/manage-users/add-ad-user.png)

5.  Vul de vereiste velden in voor de nieuwe gebruiker. Selecteer **Maken**.

Raadpleeg de documentatie van [Azure AD](../../active-directory/fundamentals/add-users-azure-active-directory.md) voor meer informatie over het beheren van gebruikers binnen Azure AD.

## <a name="manage-blockchain-workbench-administrators"></a>Beheerders van Block Chain Workbench beheren

Wanneer gebruikers zijn toegevoegd aan de Directory, is de volgende stap het kiezen van welke gebruikers Block Chain Workbench-beheerders zijn. Gebruikers in de groep **Administrators** zijn gekoppeld aan de toepassingsrol van de **beheerder** in Block Chain Workbench. Beheerders kunnen gebruikers toevoegen of verwijderen, gebruikers toewijzen aan specifieke scenario's en nieuwe toepassingen maken.

Gebruikers toevoegen aan de groep **Administrators** in de Azure AD-adres lijst:

1.  Meld u aan bij de [Azure-portal](https://portal.azure.com).
2.  Controleer of u zich in de Azure AD-Tenant bevindt die is gekoppeld aan block Chain Workbench door uw account in de rechter bovenhoek te selecteren.
3.  Selecteer **Azure Active Directory > bedrijfs toepassingen**.
4.  Selecteer de Azure AD-client toepassing voor Block Chain workbench
    
    ![Alle bedrijfs toepassings registraties](./media/manage-users/select-blockchain-client-app.png)

5.  Selecteer **gebruikers en groepen > gebruiker toevoegen**.
6.  Selecteer in **toewijzing toevoegen**de optie **gebruikers**. Kies of zoek naar de gebruiker die u wilt toevoegen als beheerder. Klik op **selecteren wanneer u** klaar bent met kiezen.

    ![Toewijzing toevoegen](./media/manage-users/add-user-assignment.png)

9.  Controleer of de **rol** is ingesteld op **Administrator**
10. Selecteer **Toewijzen**. De toegevoegde gebruikers worden weer gegeven in de lijst waaraan de beheerdersrol is toegewezen.

    ![Gebruikers van client-app Block Chain](./media/manage-users/blockchain-admin-list.png)

## <a name="managing-blockchain-workbench-members"></a>Leden van Block Chain Workbench beheren

Gebruik de toepassing Block Chain Workbench voor het beheren van gebruikers en organisaties die deel uitmaken van uw consortium. U kunt gebruikers toevoegen aan of verwijderen uit toepassingen en rollen.

1. [Open de Block Chain Workbench](deploy.md#blockchain-workbench-web-url) in uw browser en meld u aan als beheerder.

    ![Blockchain Workbench](./media/manage-users/blockchain-workbench-applications.png)

    Leden worden toegevoegd aan elke toepassing. Leden kunnen een of meer toepassings rollen hebben om contracten te initiëren of acties uit te voeren.

2. Als u leden voor een toepassing wilt beheren, selecteert u een toepassings tegel in het deel venster **toepassingen** .

    Het aantal leden dat aan de geselecteerde toepassing is gekoppeld, wordt weer gegeven in de leden tegel.

    ![Toepassing selecteren](./media/manage-users/blockchain-workbench-select-application.png)


#### <a name="add-member-to-application"></a>Lid toevoegen aan toepassing

1. Selecteer de tegel member om een lijst met de huidige leden weer te geven.
2. Selecteer **leden toevoegen**.

    ![Leden toevoegen](./media/manage-users/application-add-members.png)

3. Zoek de naam van de gebruiker.  Alleen Azure AD-gebruikers die voorkomen in de Blockchain Workbench-tenant staan vermeld. Als de gebruiker niet wordt gevonden, moet u [Azure AD-gebruikers toevoegen](#add-azure-ad-users).

    ![Leden toevoegen](./media/manage-users/find-user.png)

4. Selecteer een **rol** in de vervolg keuzelijst.

    ![Leden van rol selecteren](./media/manage-users/application-select-role.png)

5. Selecteer **Toevoegen** om het lid met de gekoppelde rol aan de toepassing toe te voegen.

#### <a name="remove-member-from-application"></a>Lid uit toepassing verwijderen

1. Selecteer de tegel member om een lijst met de huidige leden weer te geven.
2. Voor de gebruiker die u wilt verwijderen, kiest u **verwijderen** in de vervolg keuzelijst rol.

    ![Lid verwijderen](./media/manage-users/application-remove-member.png)

#### <a name="change-or-add-role"></a>Rol wijzigen of toevoegen

1. Selecteer de tegel member om een lijst met de huidige leden weer te geven.
2. Voor de gebruiker die u wilt wijzigen, klikt u op de vervolg keuzelijst en selecteert u de nieuwe rol.

    ![Rol wijzigen](./media/manage-users/application-change-role.png)

## <a name="next-steps"></a>Volgende stappen

In dit procedure-artikel hebt u geleerd hoe u gebruikers beheert voor Azure Block Chain Workbench. Ga door naar het volgende artikel voor meer informatie over het maken van een blockchain-toepassing.

> [!div class="nextstepaction"]
> [Een Block Chain-toepassing maken in azure Block Chain workbench](create-app.md)
