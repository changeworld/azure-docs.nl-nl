---
author: yashar
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 02-18-2020
ms.openlocfilehash: aebe8f05bd1a3607da6d1153c87490891fc6b299
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471449"
---
Wanneer u een gereserveerde VM-instantie koopt, kunt u ervoor kiezen om te optimaliseren voor de flexibiliteit van de instantie grootte of de prioriteit van de capaciteit. Zie voor meer informatie over het instellen of wijzigen van de instelling Optimize voor gereserveerde VM-instanties [de instelling optimaliseren wijzigen voor gereserveerde](../articles/cost-management-billing/reservations/manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances)VM-instanties.

Met een gereserveerde virtuele machine-instantie die is geoptimaliseerd voor flexibiliteit van de instantie grootte, kan de door u gekochte reserve ring gelden voor de grootte van virtuele machines (Vm's) in dezelfde groep voor de flexibiliteit van de instantie grootte. Als u bijvoorbeeld een reserve ring aanschaft voor een VM-grootte die wordt vermeld in de DSv2-serie, zoals Standard_DS5_v2, kan de reserverings korting van toepassing zijn op de andere vier grootten die worden vermeld in die zelfde groep voor de grotere instantie grootte:

- Standard_DS1_v2
- Standard_DS2_v2
- Standard_DS3_v2
- Standard_DS4_v2

Maar dat de reserverings korting niet van toepassing is op Vm's die worden vermeld in verschillende flexibiliteits groepen voor de instantie grootte, zoals Sku's in DSv2 Series High Memory: Standard_DS11_v2, Standard_DS12_v2, enzovoort.

Binnen de groep voor de flexibiliteit van de instantie grootte is het aantal Vm's waarop de reserverings korting van toepassing is afhankelijk van de grootte van de virtuele machine die u kiest wanneer u een reserve ring koopt. Het is ook afhankelijk van de grootte van de virtuele machines die u uitvoert. De verhouding kolom vergelijkt de relatieve footprint voor elke VM-grootte in de flexibiliteits groep voor instantie grootte. Gebruik de verhouding waarde om te berekenen hoe de reserverings korting van toepassing is op de virtuele machines die u uitvoert.

## <a name="examples"></a>Voorbeelden

De volgende voor beelden gebruiken de grootten en verhoudingen in de tabel DSv2.

U koopt een gereserveerde VM-instantie met de grootte Standard_DS4_v2 waarbij de verhouding of de relatieve footprint in vergelijking met de andere grootten in die reeks 8 is.

- Scenario 1: Voer acht Standard_DS1_v2 grote Vm's met een ratio van 1 uit. Uw reserverings korting is van toepassing op alle acht Vm's.
- Scenario 2: Voer twee Standard_DS2_v2 grootte-Vm's uit met een ratio van 2 elk. Voer ook een Standard_DS3_v2 VM-grootte uit met een ratio van 4. De totale footprint is 2 + 2 + 4 = 8. Daarom is uw reserverings korting van toepassing op alle drie de virtuele machines.
- Scenario 3: een Standard_DS5_v2 met een ratio van 16 uitvoeren. Uw reserverings korting is van toepassing op de helft van de reken kosten van de virtuele machine.

In de volgende secties ziet u welke grootten zich in dezelfde reeks groep bevinden wanneer u een gereserveerde VM-instantie koopt die is geoptimaliseerd voor de flexibiliteit van de instantie grootte.

## <a name="instance-size-flexibility-ratio-for-vms"></a>Flexibiliteits verhouding van instantie grootte voor Vm's 

Hieronder ziet u de flexibiliteits groepen, ArmSkuName en de verhoudingen van de instantie grootte.  

[Flexibiliteits verhouding van instantie grootte](https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv)

De bestands-URL en het schema worden hersteld, zodat u dit bestand programmatisch kunt gebruiken. De gegevens zijn binnenkort ook beschikbaar via API.
