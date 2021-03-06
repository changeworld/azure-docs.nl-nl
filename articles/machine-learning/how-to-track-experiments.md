---
title: Log ML experimenten & metrische gegevens
titleSuffix: Azure Machine Learning
description: Bewaak uw Azure ML experimenten en bewaak metrische uitvoerings gegevens om het proces voor het maken van het model te verbeteren. Voeg logboek registratie toe aan uw trainings script en Bekijk de geregistreerde resultaten van een uitvoering.  Gebruik run. log, run. start_logging of ScriptRunConfig.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/12/2020
ms.custom: seodec18
ms.openlocfilehash: 0c77e9d0aa4f44f33b1345a6021fc0378459ee85
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79296962"
---
# <a name="monitor-azure-ml-experiment-runs-and-metrics"></a>Uitvoeringen en metrische gegevens van Azure ML-experimenten bewaken
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Verbeter het proces voor het maken van het model door de metrische gegevens voor experimenten en bewakings uitvoeringen bij te houden. In dit artikel leert u hoe u logboek registratie code kunt toevoegen aan uw trainings script, een experiment moet verzenden, de uitvoering ervan kunt controleren en de resultaten kunt controleren in Azure Machine Learning.

> [!NOTE]
> Azure Machine Learning kunnen ook gegevens van andere bronnen tijdens de training registreren, zoals geautomatiseerde machine learning uitvoeringen, of de docker-container die de trainings taak uitvoert. Deze logboeken worden niet gedocumenteerd. Als u problemen ondervindt en contact opneemt met micro soft ondersteuning, kunnen ze deze logboeken gebruiken tijdens het oplossen van problemen.

> [!TIP]
> De informatie in dit document is voornamelijk bedoeld voor gegevens wetenschappers en ontwikkel aars die het model trainings proces willen bewaken. Als u een beheerder bent die geïnteresseerd is in het bewaken van het resource gebruik en gebeurtenissen van Azure machine learning, zoals quota's, voltooide trainings uitvoeringen of voltooide model implementaties, raadpleegt u [bewaking Azure machine learning](monitor-azure-machine-learning.md).

## <a name="available-metrics-to-track"></a>Beschik bare metrische gegevens om bij te houden

De volgende metrische gegevens kunnen worden toegevoegd aan een run tijdens het trainen van een experiment. Zie de [referentie documentatie](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py)voor het uitvoeren van klassen voor een gedetailleerde lijst met wat er kan worden gevolgd bij een uitvoering.

|Type| Funkce Pythonu | Opmerkingen|
|----|:----|:----|
|Scalaire waarden |Functie:<br>`run.log(name, value, description='')`<br><br>Voorbeeld:<br>Run.log ("nauwkeurigheid", 0.95) |Meld u een numerieke of tekenreekswaarde voor de uitvoering met de opgegeven naam. Logboekregistratie van een metrische waarde aan een run zorgt ervoor dat deze metrische gegevens worden opgeslagen in de record uitvoeren in het experiment.  U kunt de dezelfde metrische gegevens meerdere keren binnen een uitvoering, het resultaat wordt beschouwd als een vector van deze metrische gegevens vastleggen.|
|Lijsten|Functie:<br>`run.log_list(name, value, description='')`<br><br>Voorbeeld:<br>Run.log_list ("nauwkeurigheden," [0,6, 0,7, 0.87]) | Meld u een lijst met waarden voor de uitvoering met de opgegeven naam.|
|Rij|Functie:<br>`run.log_row(name, description=None, **kwargs)`<br>Voorbeeld:<br>Run.log_row ("Y via X", x = 1, y = 0.4) | Met *log_row* maakt u een metriek met meerdere kolommen zoals beschreven in kwargs. Elke benoemde parameter genereert een kolom met de opgegeven waarde.  *log_row* kan eenmaal worden aangeroepen om een wille keurige tupel of meerdere keren in een lus te registreren om een volledige tabel te genereren.|
|Tabel|Functie:<br>`run.log_table(name, value, description='')`<br><br>Voorbeeld:<br>Run.log_table ("Y via X", {"x": [1, 2, 3], "y": [0,6, 0,7, 0,89]}) | Meld u aan een dictionary-object de uitvoeren met de opgegeven naam. |
|Installatiekopieën|Functie:<br>`run.log_image(name, path=None, plot=None)`<br><br>Voorbeeld:<br>`run.log_image("ROC", plot=plt)` | Meld u aan een installatiekopie van de record uitvoeren. Log_image gebruiken om aan te melden voor een afbeelding of een matplotlib getekend met het uitvoeren.  Deze installatiekopieën worden zichtbaar en vergelijkbare in de record uitvoeren.|
|Een uitvoering taggen|Functie:<br>`run.tag(key, value=None)`<br><br>Voorbeeld:<br>Run.Tag ("ingeschakeld", "Ja") | Tag de uitvoering met een tekenreekssleutel en een optionele tekenreeks-waarde.|
|Bestand of map uploaden|Functie:<br>`run.upload_file(name, path_or_stream)`<br> <br> Voorbeeld:<br>Run.upload_file ("best_model.pkl", ". / model.pkl") | Upload een bestand naar de record uitvoeren. Wordt uitgevoerd automatisch vastleggen bestand in de map met de opgegeven uitvoer, die standaard ". / levert ' voor de meeste typen die worden uitgevoerd.  Gebruik upload_file alleen als aanvullende bestanden moeten worden geüpload of een map met de uitvoer is niet opgegeven. We raden aan om `outputs` toe te voegen aan de naam, zodat deze wordt geüpload naar de uitvoer Directory. U kunt alle bestanden weer geven die zijn gekoppeld aan deze record voor uitvoeren door de naam `run.get_file_names()`|

> [!NOTE]
> Metrische gegevens voor hoeken, een lijst met rijen en tabellen kunt type hebben: float, geheel getal of tekenreeks.

## <a name="choose-a-logging-option"></a>Kies een optie voor logboek registratie

Als u wilt bijhouden of bewaken van uw experiment, moet u code om te starten wanneer u de uitvoering verzendt logboekregistratie toevoegen. De volgende zijn manieren voor het activeren van het verzenden van de uitvoering:
* __Run. start_logging__ : Hiermee kunt u logboek registratie functies toevoegen aan uw trainings script en een interactieve logboek registratie sessie starten in het opgegeven experiment. **start_logging** maakt een interactieve uitvoering voor gebruik in scenario's zoals notebooks. Alle metrische gegevens die zijn vastgelegd tijdens de sessie worden toegevoegd aan de uitvoerregistratie in het experiment.
* __ScriptRunConfig__ : Voeg logboek registratie functies toe aan uw trainings script en laad de volledige map met de uitvoering.  **ScriptRunConfig** is een klasse voor het instellen van configuraties voor script uitvoeringen. Met deze optie kunt u de code voor bewaking om te worden geïnformeerd over voltooiing of het opvragen van een visual widget voor het bewaken van toevoegen.

## <a name="set-up-the-workspace"></a>Instellen van de werkruimte
Voordat u logboekregistratie en het verzenden van een experiment toevoegt, moet u de werkruimte instellen.

1. Laden van de werkruimte. Zie [werkruimte configuratie bestand](how-to-configure-environment.md#workspace)voor meer informatie over het instellen van de configuratie van de werk ruimte.

[! notebook-python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb? name = load_ws)]


## <a name="option-1-use-start_logging"></a>Optie 1: Start_logging gebruiken

**start_logging** maakt een interactieve uitvoering voor gebruik in scenario's zoals notebooks. Alle metrische gegevens die zijn vastgelegd tijdens de sessie worden toegevoegd aan de uitvoerregistratie in het experiment.

Het volgende voorbeeld traint een eenvoudig model sklearn Ridge lokaal in een lokaal Jupyter-notitieblok. Zie [Compute-doelen voor model training instellen met Azure machine learning](https://docs.microsoft.com/azure/machine-learning/how-to-set-up-training-targets)voor meer informatie over het verzenden van experimenten voor verschillende omgevingen.

### <a name="load-the-data"></a>De gegevens laden

In dit voor beeld wordt de diabetes-gegevensset gebruikt, een bekende kleine gegevensset die wordt geleverd met scikit-learn. In deze cel wordt de gegevensset geladen en gesplitst in wille keurige trainings-en test sets.

[! notebook-python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb? name = load_data)]

### <a name="add-tracking"></a>Tracking toevoegen
Voeg experiment tracking toe met behulp van de Azure Machine Learning SDK en upload een persistent model in de record experiment run. De volgende code wordt toegevoegd tags, Logboeken, en wordt een modelbestand geüpload naar het experiment uitvoeren.

[! notebook-python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb? name = create_experiment)]

Het script eindigt op ```run.complete()```, wat aangeeft dat de uitvoering is voltooid.  Deze functie wordt meestal gebruikt in interactieve scenario's.

## <a name="option-2-use-scriptrunconfig"></a>Optie 2: ScriptRunConfig gebruiken

[**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) is een klasse voor het instellen van configuraties voor script uitvoeringen. Met deze optie kunt u de code voor bewaking om te worden geïnformeerd over voltooiing of het opvragen van een visual widget voor het bewaken van toevoegen.

In dit voorbeeld is een vervolg op het basismodel sklearn Ridge van boven. Hiervoor wordt een eenvoudige parameter vegen te vegen via alpha-waarden van het model om vast te leggen metrische gegevens en getrainde modellen in wordt uitgevoerd onder het experimentcanvas. Het voorbeeld lokaal wordt uitgevoerd op basis van een gebruiker beheerde omgeving. 

1. Een trainings script maken `train.py`.

   [! code-python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]

2. Het `train.py` script verwijst naar `mylib.py` waarmee u de lijst met alfa waarden kunt ophalen die in het ploois model kunnen worden gebruikt.

   [! code-python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/mylib.py)] 

3. Configureer een beheerde gebruiker lokale omgeving.

   [! notebook-python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb? name = user_managed_env)]


4. Dien het ```train.py```-script in om uit te voeren in de door de gebruiker beheerde omgeving. Deze hele script-map wordt verzonden voor training, inclusief het ```mylib.py```-bestand.

   [! notebook-python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb? name = src)] [! notebook-python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb? name = run)]




## <a name="manage-a-run"></a>Een uitvoering beheren

In het [Start-, controle-en annulerings artikel over training worden](how-to-manage-runs.md) specifieke Azure machine learning werk stromen gemarkeerd voor het beheren van uw experimenten.

## <a name="view-run-details"></a>Details van de uitvoering weergeven

### <a name="view-activequeued-runs-from-the-browser"></a>Actieve/in wachtrij geplaatste uitvoeringen weer geven vanuit de browser

Reken doelen voor het trainen van modellen zijn een gedeelde bron. Ze kunnen op een bepaald moment meerdere uitvoeringen in de wachtrij plaatsen of actief zijn. Voer de volgende stappen uit om de uitvoeringen voor een specifiek Compute-doel in uw browser weer te geven:

1. Selecteer in de [Azure machine learning Studio](https://ml.azure.com/)uw werk ruimte en selecteer vervolgens __Compute__ in de linkerkant van de pagina.

1. Selecteer __trainings clusters__ om een lijst weer te geven met reken doelen die worden gebruikt voor de training. Selecteer vervolgens het cluster.

    ![Het trainings cluster selecteren](./media/how-to-track-experiments/select-training-compute.png)

1. Selecteer __uitvoeren__. De lijst met uitvoeringen die gebruikmaken van dit cluster wordt weer gegeven. Als u Details voor een specifieke uitvoering wilt weer geven, gebruikt u de koppeling in de kolom __uitvoeren__ . Als u Details voor het experiment wilt weer geven, gebruikt u de koppeling in de kolom __experiment__ .

    ![Uitvoeringen voor trainings cluster selecteren](./media/how-to-track-experiments/show-runs-for-compute.png)
    
    > [!TIP]
    > Een run kan onderliggende uitvoeringen bevatten, waardoor één trainings taak kan leiden tot meerdere vermeldingen.

Zodra een uitvoering is voltooid, wordt deze niet meer weer gegeven op deze pagina. Als u informatie over voltooide uitvoeringen wilt bekijken, gaat u naar de sectie __experimenten__ van Studio en selecteert u het experiment en voert u uit. Zie de sectie [metrische gegevens over query's uitvoeren](#queryrunmetrics) voor meer informatie.

### <a name="monitor-run-with-jupyter-notebook-widget"></a>Bewaking uitvoeren met Jupyter notebook-widget
Wanneer u de methode **ScriptRunConfig** gebruikt om uitvoeringen te verzenden, kunt u de voortgang van de uitvoering met een [Jupyter-widget](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py)bekijken. Net als het indienen van de run, is de widget asynchroon en biedt deze elke 10-15 seconden live updates totdat de taak is voltooid.

1. Bekijk de Jupyter-widget tijdens het wachten op voor het uitvoeren om te voltooien.

   ```python
   from azureml.widgets import RunDetails
   RunDetails(run).show()
   ```

   ![Schermafbeelding van de Jupyter-notebook widget](./media/how-to-track-experiments/run-details-widget.png)

   U kunt ook een koppeling naar dezelfde weer gave in uw werk ruimte ophalen.

   ```python
   print(run.get_portal_url())
   ```

2. **[Voor automatische machine learning uitvoeringen]** Om toegang te krijgen tot de grafieken van een vorige uitvoering. Vervang `<<experiment_name>>` door de juiste naam van het experiment:

   ``` 
   from azureml.widgets import RunDetails
   from azureml.core.run import Run

   experiment = Experiment (workspace, <<experiment_name>>)
   run_id = 'autoML_my_runID' #replace with run_ID
   run = Run(experiment, run_id)
   RunDetails(run).show()
   ```

   ![Jupyter notebook widget voor geautomatiseerde Machine Learning](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)


Als u meer details van een pijp lijn wilt weer geven, klikt u op de pijp lijn die u in de tabel wilt verkennen. de grafieken worden weer gegeven in een pop-up van de Azure Machine Learning Studio.

### <a name="get-log-results-upon-completion"></a>Resultaten van logboeken weergeven bij voltooiing

Model trainen en bewaking optreden op de achtergrond, zodat u andere taken uitvoeren kunt terwijl u wacht. U kunt ook wachten totdat het model opleiding vóór het uitvoeren van meer code is voltooid. Wanneer u **ScriptRunConfig**gebruikt, kunt u ```run.wait_for_completion(show_output = True)``` gebruiken om te laten zien wanneer de model training is voltooid. De vlag ```show_output``` biedt uitgebreide uitvoer. 

<a id="queryrunmetrics"></a>

### <a name="query-run-metrics"></a>Uitvoering van metrische gegevens van de query

U kunt de metrische gegevens van een getraind model weer geven met behulp van ```run.get_metrics()```. U kunt nu alle van de metrische gegevens die zijn geregistreerd in het voorbeeld hierboven om te bepalen van het beste model krijgen.

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-the-experiment-in-your-workspace-in-azure-machine-learning-studio"></a>Het experiment in uw werk ruimte in [Azure machine learning Studio](https://ml.azure.com) weer geven

Wanneer een experiment is voltooid, kunt u bladeren naar de vastgelegde experiment record worden uitgevoerd. U kunt de geschiedenis openen vanuit de [Azure machine learning Studio](https://ml.azure.com).

Ga naar het tabblad experimenten en selecteer uw experiment. U wordt naar het dash board voor experimenteren geleid, waar u bijgehouden metrische gegevens en grafieken kunt zien die voor elke uitvoering worden vastgelegd. In dit geval geregistreerd we MSE en het alpha-waarden.

  ![Details uitvoeren in de Azure Machine Learning Studio](./media/how-to-track-experiments/experiment-dashboard.png)

U kunt inzoomen op een specifieke uitvoering om de uitvoer of logboeken weer te geven of de moment opname te downloaden van het experiment dat u hebt verzonden, zodat u de map experiment met anderen kunt delen.

### <a name="viewing-charts-in-run-details"></a>Grafieken weergeven in de details van uitvoering

Er zijn verschillende manieren om de logboek registratie-Api's te gebruiken voor het vastleggen van verschillende soorten metrische gegevens tijdens een uitvoering en om ze weer te geven als grafieken in Azure Machine Learning Studio.

|Geregistreerde waarde|Voorbeeldcode| Weergeven in portal|
|----|----|----|
|Meld u een matrix met numerieke waarden| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|één variabele lijndiagram weer te geven|
|Meld u één numerieke waarde met dezelfde metrische naam herhaaldelijk gebruikt (zoals vanuit een for-lus)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| één variabele lijndiagram weer te geven|
|Meld u een rij met 2 numerieke kolommen herhaaldelijk|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Twee variabelen lijndiagram weer te geven|
|Logboektabel met 2 numerieke kolommen|`run.log_table(name='Sine Wave', value=sines)`|Twee variabelen lijndiagram weer te geven|


## <a name="example-notebooks"></a>Voorbeeld-laptops
De volgende notebooks illustratie van concepten in dit artikel:
* [instructies-to-use-azureml/training/training-binnen-notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook)
* [instructies-to-use-azureml/training/trein-on-Local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [instructies voor het gebruik van azureml/track-and-monitor-experimenten/logging-API](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Volgende stappen

Doe het volgende om te leren hoe u de Azure Machine Learning-SDK voor Python kunt gebruiken:

* Bekijk een voor beeld van hoe u het beste model registreert en implementeert in de zelf studie, [een classificatie model voor installatie kopieën traint met Azure machine learning](tutorial-train-models-with-aml.md).

* Meer informatie over het [trainen van PyTorch-modellen met Azure machine learning](how-to-train-pytorch.md).
