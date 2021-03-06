---
title: Uw investeringen in de cloud optimaliseren met Azure Cost Management | Microsoft Docs
description: Dit artikel helpt u de meeste waarde uit uw investeringen in de cloud te halen, uw kosten te verlagen en te evalueren waar uw geld wordt uitgegeven.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 02/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: adwise
ms.custom: seodec18
ms.openlocfilehash: 6d13bf4b2a3abd3a1552ac656a6a15fed8c7cb60
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78383530"
---
# <a name="how-to-optimize-your-cloud-investment-with-azure-cost-management"></a>Uw investeringen in de cloud optimaliseren met Azure Cost Management

Azure Cost Management biedt u de hulpmiddelen om uw uitgaven te plannen, te analyseren en te verlagen om uw investering in de cloud optimaal te benutten. In dit document vindt u een methodische aanpak voor het beheren van kosten en highlights van de hulpprogramma's die beschikbaar zijn om de uitdagingen voor de kosten van uw organisatie aan te spreken. Met Azure kunt u eenvoudig cloudoplossingen bouwen en implementeren. Het is echter belangrijk dat deze oplossingen zijn geoptimaliseerd om de kosten voor uw organisatie tot een minimum te beperken. Als u de principes volgt die in dit document worden beschreven en onze hulpmiddelen gebruikt, kunt u ervoor zorgen dat uw organisatie is voorbereid op succes.

## <a name="methodology"></a>Methodologie

Kostenbeheer is een organisatorisch probleem en moet een doorlopende procedure zijn die begint voordat u geld besteedt aan cloud-resources. Uw organisatie moet het volgende doen om kostenbeheer te implementeren en de kosten te optimaliseren:

- Bereid u voor met de juiste hulpprogramma's voor succes
- Wees verantwoordelijk voor kosten
- Onderneem de juiste actie om de uitgaven te optimaliseren

Er moeten drie sleutelgroepen worden uitgelijnd in uw organisatie, zodat u zeker weet dat u de kosten met succes hebt beheerd.

- **Financiën** - mensen die verantwoordelijk zijn voor het goedkeuren van budgetaanvragen voor de hele organisatie op basis van cloud-bestedingsprognoses. Ze betalen de bijbehorende factuur en wijzen kosten aan verschillende teams toe om verantwoordelijkheid te stimuleren.
- **Managers** - zakelijke besluitvormers in een organisatie die inzicht moeten hebben in de cloudbestedingen om de beste bestedingsresultaten te vinden.
- **App-teams** - engineers die op dagelijkse basis cloudresources beheren, ontwikkelen services om te voldoen aan de behoeften van de organisatie. Deze teams hebben de flexibiliteit nodig om de meeste waarde in hun gedefinieerde budgetten te leveren.

### <a name="key-principles"></a>Belangrijkste principes

Gebruik de onderstaande principes om uw organisatie te positioneren voor succes in het beheer van cloudkosten.

#### <a name="planning"></a>Planning

Uitgebreide, vooraf gemaakte planning biedt u de mogelijkheid om cloudgebruik aan uw specifieke bedrijfsvereisten aan te passen. Vraag uzelf:

- Welk zakelijk probleem moet ik oplossen?
- Welke gebruikspatronen worden verwacht van mijn resources?

Met uw antwoorden kunt u de aanbiedingen selecteren die voor u geschikt zijn. Ze bepalen welke infrastructuur moet worden gebruikt en hoe deze wordt gebruikt om de efficiëntie van Azure te maximaliseren.

#### <a name="visibility"></a>Zichtbaarheid

Bij een goed gestructureerd kostenbeheer helpt u mensen te informeren over de kosten van Azure waarvoor ze verantwoordelijk zijn of voor het geld dat ze uitgeven. Azure heeft services die zijn ontworpen om u inzicht te geven in *waar* uw geld wordt uitgegeven. Profiteer van deze hulpprogramma's. Ze kunnen u helpen bij het vinden van resources die worden gebruikt, het verwijderen van afval en het maximaliseren van de kostenbesparingen.

#### <a name="accountability"></a>Verantwoordelijkheid

Ken kosten in uw organisatie toe om ervoor te zorgen dat mensen verantwoordelijk zijn voor de uitgaven van hun team. Als u de Azure-uitgaven van uw organisatie volledig wilt begrijpen, moet u uw resources ordenen om inzicht te krijgen in de kostentoewijzing. Een goede organisatie helpt bij het beheren en verlagen van kosten en houdt mensen verantwoordelijk voor efficiënte uitgaven in uw organisatie.

#### <a name="optimization"></a>Optimalisatie

Onderneem actie om uw uitgaven te verminderen. Profiteer er maximaal van op basis van de bevindingen die zijn verzameld via planning en vergroot de zichtbaarheid van de kosten. U kunt bijvoorbeeld optimalisaties voor aankoop en licenties overwegen, samen met wijzigingen in de infrastructuurimplementatie die verderop in dit document worden beschreven.

#### <a name="iteration"></a>Iteratie

Iedereen in uw organisatie moet rekening houden met de levenscyclus van kostenbeheer. Ze moeten voortdurend betrokken blijven om de kosten te optimaliseren. Wees strikt over dit iteratieve proces en maak het een belangrijk grondbeginsel van verantwoordelijke cloud-governance in uw organisatie.

![Diagram van de belangrijkste principes met zichtbaarheid, verantwoording en optimalisatie](./media/cost-mgt-best-practices/principles.png)

## <a name="plan-with-cost-in-mind"></a>Plan met kosten in gedachte

Voordat u cloudresources implementeert, moet u de volgende items beoordelen:

- De Azure-aanbieding die het best voldoet aan uw behoeften
- Welke resources u wilt gebruiken
- Hoeveel ze mogelijk kosten

Azure biedt hulpmiddelen die u kunnen helpen bij het beoordelingsproces. De hulpprogramma's kunnen u een goed idee geven van de investeringen die nodig zijn om uw workloads in te schakelen. Vervolgens kunt u de beste configuratie voor uw situatie selecteren.

### <a name="azure-onboarding-options"></a>Opties voor het voorbereiden van Azure

De eerste stap bij het maximaliseren van uw ervaring binnen Cost Management is om te onderzoeken en te beslissen welke Azure-aanbieding voor u het meest geschikt is. Denk na over uw planning voor het gebruik van Azure in de toekomst. Bedenk ook hoe uw factureringsmodel geconfigureerd moet worden. Houd rekening met de volgende vragen wanneer u uw besluit neemt:

- Hoe lang moet ik het gebruik van Azure plannen? Moet ik testen of moet ik een infrastructuur voor langere termijn bouwen?
- Hoe kan ik voor Azure betalen? Moet ik een gereduceerde prijs vooruit betalen of aan het einde van de maand gefactureerd worden?

Ga voor meer informatie over de verschillende opties naar [Hoe u Azure kunt kopen](https://azure.microsoft.com/pricing/purchase-options/). Hieronder vindt u enkele van de meest voorkomende factureringsmodellen.

#### <a name="free"></a>[Gratis](https://azure.microsoft.com/free/)

- 12 maanden aan populaire gratis services
- $200 aan tegoed om services gedurende 30 dagen te verkennen
- 25+ services zijn altijd gratis

#### <a name="pay-as-you-go"></a>[Betalen per gebruik](https://azure.microsoft.com/offers/ms-azr-0003p)

- Geen minimum bedragen of toezeggingen
- Concurrerende prijzen
- Alleen betalen voor wat u gebruikt
- Op elk moment annuleren

#### <a name="enterprise-agreement"></a>[Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/)

- Opties voor monetaire toezeggingen vooraf
- Toegang tot gereduceerde Azure-prijzen

## <a name="estimate-the-cost-of-your-solution"></a>De kosten van uw oplossing schatten

Voordat u een infrastructuur implementeert, moet u bepalen hoeveel uw oplossing gaat kosten. De evaluatie helpt u bij het maken van een budget voor uw organisatie voor de workload, vooraf. Vervolgens kunt u een budget over tijd gebruiken om de geldigheid van de eerste schatting te bepalen. En u kunt deze vergelijken met de werkelijke kosten van uw geïmplementeerde oplossing.

### <a name="azure-pricing-calculator"></a>Azure-prijscalculator

Met de Azure-prijscalculator kunt u verschillende combinaties van Azure-Services combineren en vergelijken om een schatting te krijgen van de kosten. U kunt uw oplossing op verschillende manieren in Azure implementeren. Dit kan van invloed zijn op uw algemene uitgaven. U kunt het hulpprogramma het meest effectief gebruiken om u te bedenken over alle vereisten voor de infrastructuur van uw cloudimplementatie. Het kan u helpen een solide schatting te krijgen van uw geschatte bestedingen in Azure.

Zie de [Azure-prijscalculator](https://azure.microsoft.com/pricing/calculator) voor meer informatie.

### <a name="azure-migrate"></a>Azure Migrate

Azure Migrate is een service die de huidige workloads van uw organisatie in on-premises datacenters evalueert. Het biedt u inzicht in wat u mogelijk nodig hebt van een Azure-vervangingsoplossing. Eerst analyseert Migrate uw on-premises machines om te bepalen of migratie haalbaar is. Vervolgens wordt de VM-grootte in Azure aanbevolen om de prestaties te maximaliseren. Ten slotte maakt het een schatting van de kosten voor een oplossing op basis van Azure.

Zie [Azure Migrate](../../site-recovery/migrate-overview.md) voor meer informatie.

## <a name="analyze-and-manage-your-costs"></a>Uw kosten analyseren en beheren

Blijf op de hoogte van de ontwikkeling van de kosten voor uw organisatie in de loop van de tijd. Gebruik de volgende technieken om uw uitgaven goed te begrijpen en te beheren.

### <a name="organize-and-tag-your-resources"></a>Uw resources ordenen en labelen

Organiseer uw resources met de kosten in gedachte. Wanneer u abonnementen en resourcegroepen maakt, moet u nadenken over de teams die verantwoordelijk zijn voor de gekoppelde kosten. Zorg ervoor dat uw bij uw rapportage uw organisatie in het oog houdt. Abonnementen en resourcegroepen bieden goede buckets om de uitgaven voor uw organisatie in te delen en te kenmerken. Tags bieden een goede manier om de kosten te berekenen. U kunt tags gebruiken als filter. En u kunt ze groeperen op het analyseren van gegevens en het onderzoeken van de kosten. Enterprise Agreement-klanten kunnen ook afdelingen maken en hier abonnementen onder plaatsen. Op kosten gebaseerde organisatie in Azure zorgt ervoor dat de relevante personen in uw organisatie het account kunnen blijven gebruiken om de uitgaven van hun team te verminderen.

### <a name="use-cost-analysis"></a>Kostenanalyse gebruiken

Met kostenanalyse kunt u de kosten van uw organisatie dieper analyseren door uw kosten te segmenteren en delen met behulp van standaard resource-eigenschappen. Houd rekening met de volgende veelgestelde vragen als richtlijn voor de analyse. Als u deze vragen regelmatig beantwoordt, kunt u beter op de hoogte blijven en meer kostenbewuste beslissingen nemen.

- **Geschatte kosten voor de huidige maand** – Hoeveel heb ik deze maand tot nu toe gehad? Blijf ik binnen mijn budget?
- **Anomalieën onderzoeken** – Voer routinecontroles uit om ervoor te zorgen dat de kosten binnen een redelijk bereik van normaal gebruik blijven. Wat zijn de trends? Zijn er uitschieters?
- **Factuurafstemming** – Zijn mijn laatste gefactureerde kosten meer dan de vorige maand? Hoe zijn gewoonten wat betreft uitgaven gewijzigd over de maanden?
- **Interne terugstorting** – Nu ik weet hoeveel er in rekening wordt gebracht, hoe moeten die kosten dan worden uitgesplitst voor mijn organisatie?

Ga voor meer informatie naar [Kostenanalyse](quick-acm-cost-analysis.md).

### <a name="export-billing-data-on-a-schedule"></a>Factureringsgegevens exporteren volgens een planning

Moet u uw facturerings gegevens importeren in een extern systeem, zoals een dashboard of financieel systeem? Automatische export naar Azure Storage instellen en voorkomen dat bestanden elke maand handmatig moeten worden gedownload. U kunt vervolgens gemakkelijk automatische integratie met andere systemen instellen om uw factureringsgegevens gesynchroniseerd te laten worden.

Zie voor meer informatie over het exporteren van factureringsgegevens [Geëxporteerde gegevens maken en beheren](tutorial-export-acm-data.md).

### <a name="create-budgets"></a>Budgetten maken

Nadat u uw bestedingspatronen hebt geïdentificeerd en geanalyseerd, is het belangrijk om te beginnen met het instellen van limieten voor uzelf en uw teams. Azure-budgetten bieden u de mogelijkheid om een kosten- of op gebruik gebaseerd budget met veel drempels en waarschuwingen in te stellen. Controleer de budgetten die u rege matig maakt om de voortgang van uw budget te bekijken en zo nodig wijzigingen aan te brengen. Met Azure budgetten kunt u ook een automatiseringstrigger configureren wanneer een bepaalde budgetdrempel wordt bereikt. U kunt uw service bijvoorbeeld configureren om VM’s af te sluiten. U kunt uw infrastructuur ook verplaatsen naar een andere prijscategorie als reactie op een budgettrigger.

Zie [Azure Budgets](tutorial-acm-create-budgets.md) voor meer informatie.

Zie [Automatisering op basis van budget](../manage/cost-management-budget-scenario.md) voor meer informatie.

## <a name="act-to-optimize"></a>Actie om te optimaliseren
Gebruik de volgende manieren om de uitgaven te optimaliseren.

### <a name="cut-out-waste"></a>Verspilling vermijden

Nadat u uw infrastructuur in Azure hebt geïmplementeerd, is het belangrijk ervoor te zorgen dat deze wordt gebruikt. De eenvoudigste manier om onmiddellijk met besparen te beginnen, is om uw resources te controleren en degene die niet worden gebruikt te verwijderen. Van daaruit moet u bepalen of uw resources zo efficiënt mogelijk worden gebruikt.

#### <a name="azure-advisor"></a>Azure Advisor

Azure Advisor is een service die onder andere virtuele machines identificeert met een laag gebruik van een bepaald CPU- of netwerkgebruik. Hier kunt u besluiten om de computer af te sluiten of het formaat ervan te wijzigen op basis van de geschatte kosten om de computers draaiende te houden. Advisor biedt ook aanbevelingen voor aankopen van gereserveerde instanties. De aanbevelingen zijn gebaseerd op uw laatste 30 dagen van het gebruik van virtuele machines. Wanneer u deze actie hebt uitgevoerd, kunnen de aanbevelingen u helpen om uw uitgaven te verminderen.

Zie [Azure Advisor](../../advisor/advisor-overview.md) voor meer informatie.

### <a name="size-your-vms-properly"></a>De grootte van uw virtuele machines correct aanpassen

VM-grootte heeft een grote invloed op de totale kosten van Azure. Het aantal Vm's dat nodig is in Azure, is mogelijk niet gelijk aan wat u momenteel hebt geïmplementeerd in een on-premises datacentrum. Zorg ervoor dat u de juiste grootte kiest voor de werkbelastingen die u wilt uitvoeren.

Zie [Azure IaaS: juiste grootte en kosten](https://azure.microsoft.com/resources/videos/azurecon-2015-azure-iaas-proper-sizing-and-cost/) voor meer informatie.

### <a name="use-purchase-discounts"></a>Inkoopkortingen gebruiken

Azure heeft veel kortingen die uw organisatie kan gebruiken om geld te besparen.

#### <a name="azure-reservations"></a>Azure-reserveringen

Met Azure Reservations kunt u vooraf betalen voor een abonnement voor één of drie jaar op een virtuele machine of rekencapaciteit van SQL Database. Door vooraf te betalen krijgt u korting op de resources die u gebruikt. Met Azure-reserveringen kunt u de kosten voor uw virtuele machine of de rekencapaciteit van een SQL-database aanzienlijk verlagen — namelijk tot 72 procent op de betalen-per-gebruik-prijzen bij vooraf betalen voor één jaar of drie jaar. Reserveringen voorzien in een korting op de factuur en zijn niet van invloed op de runtime-status van uw virtuele machines of SQL-databases.

Zie [Wat zijn Azure Reservations](../reservations/save-compute-costs-reservations.md) voor meer informatie.

#### <a name="use-azure-hybrid-benefit"></a>Azure Hybrid Benefit gebruiken

Als u al Windows Server- of SQL Server-licenties in uw on-premises implementaties hebt, kunt u het programma Azure Hybrid Benefit gebruiken om op Azure te besparen. Met het voordeel van Windows Server dekt elke licentie de kosten van het besturingssysteem (maximaal twee virtuele machines) en betaalt u alleen voor de basisrekenkosten. U kunt bestaande SQL Server-licenties gebruiken om maximaal 55 procent te besparen op op vCore gebaseerde SQL Database-opties. De opties omvatten SQL Server in Azure Virtual Machines en SQL Server Integration Services.

Zie [Azure Hybrid Benefit-spaarcalculator](https://azure.microsoft.com/pricing/hybrid-benefit/)voor meer informatie.

### <a name="other-resources"></a>Meer informatie

Azure heeft ook een service waarmee u tegen een gereduceerd tarief services kunt bouwen die gebruikmaken van de overschotcapaciteit in Azure. Zie voor meer informatie [VM's met lage prioriteit gebruiken met Batch](../../batch/batch-low-pri-vms.md).

## <a name="next-steps"></a>Volgende stappen
- Als u nog geen ervaring hebt met Cost Management, lees dan [Wat is Azure Cost Management?](../cost-management-billing-overview.md) om te leren hoe het helpt bij het bewaken en beheren van Azure-uitgaven en het optimaliseren van resourcegebruik.
