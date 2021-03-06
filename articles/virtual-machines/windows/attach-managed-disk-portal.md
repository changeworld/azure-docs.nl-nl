---
title: Een beheerde gegevens schijf koppelen aan een virtuele Windows-machine-Azure
description: Hoe u een beheerde gegevensschijf koppelen aan een virtuele Windows-machine met behulp van de Azure-portal.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 0fe04941821de2ac6e4e873e8d073c3e9b9d9508
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77919376"
---
# <a name="attach-a-managed-data-disk-to-a-windows-vm-by-using-the-azure-portal"></a>Een beheerde gegevensschijf koppelen aan een virtuele Windows-machine met behulp van Azure portal

In dit artikel wordt beschreven hoe u een nieuwe beheerde gegevensschijf koppelen aan een Windows virtuele machine (VM) met behulp van de Azure-portal. De grootte van de virtuele machine bepaalt hoeveel gegevensschijven die u kunt koppelen. Zie [grootten voor virtuele machines](sizes.md)voor meer informatie.


## <a name="add-a-data-disk"></a>Een gegevensschijf toevoegen

1. Ga naar de [Azure Portal](https://portal.azure.com) om een gegevens schijf toe te voegen. Zoek en selecteer **virtuele machines**.
2. Selecteer een virtuele machine in de lijst.
3. Selecteer op de pagina **virtuele machine** de optie **schijven**.
4. Selecteer op de pagina **schijven** de optie **gegevens schijf toevoegen**.
5. Selecteer in de vervolg keuzelijst voor de nieuwe schijf de optie **schijf maken**.
6. Typ op de pagina **Managed Disk maken** een naam voor de schijf en pas de andere instellingen zo nodig aan. Als u gereed bent, selecteert u **Maken**.
7. Selecteer op de pagina **schijven** de optie **Opslaan** om de nieuwe schijf configuratie voor de virtuele machine op te slaan.
8. Nadat Azure de schijf heeft gemaakt en deze aan de virtuele machine is gekoppeld, wordt de nieuwe schijf weer gegeven in de schijf instellingen van de virtuele machine onder **gegevens schijven**.


## <a name="initialize-a-new-data-disk"></a>Een nieuwe gegevensschijf initialiseren

1. Maak verbinding met de VM.
1. Selecteer het menu **Start** van Windows in de actieve VM en voer **diskmgmt. msc** in het zoekvak in. De **schijf beheer** -console wordt geopend.
2. Schijf beheer detecteert dat er een nieuwe, niet-geïnitialiseerde schijf is en het venster **initialiseren van** schijf wordt weer gegeven.
3. Controleer of de nieuwe schijf is geselecteerd en selecteer **OK** om deze te initialiseren.
4. De nieuwe schijf wordt weer gegeven als niet- **toegewezen**. Klik met de rechter muisknop op een wille keurige locatie op de schijf en selecteer **Nieuw eenvoudig volume**. Het venster **Nieuw eenvoudig volume wizard** wordt geopend.
5. Ga door met de wizard, behoud alle standaard waarden en klik op **volt ooien**als u klaar bent.
6. Sluit **schijf beheer**.
7. Een pop-upvenster weergegeven de melding die u nodig hebt om de opmaak van de nieuwe schijf voordat u deze kunt gebruiken. Selecteer **schijf Format teren**.
8. Controleer de instellingen in het venster **nieuwe schijf Format teren** en selecteer vervolgens **starten**.
9. Een waarschuwing weergegeven de melding dat de schijven formatteert, worden alle gegevens. Selecteer **OK**.
10. Wanneer de opmaak is voltooid, selecteert u **OK**.

## <a name="next-steps"></a>Volgende stappen

- U kunt ook [een gegevens schijf koppelen met behulp van Power shell](attach-disk-ps.md).
- Als uw toepassing het station *D:* moet gebruiken om gegevens op te slaan, kunt u [de stationsletter van de tijdelijke Windows-schijf wijzigen](change-drive-letter.md).
