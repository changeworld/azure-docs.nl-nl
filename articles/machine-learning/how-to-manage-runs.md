---
title: Trainings uitvoeringen in python starten, controleren en annuleren
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe u begint, de status van het label instelt en uw machine learning-experimenten indeelt.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 01/09/2020
ms.openlocfilehash: cd9cada24ba5e7d2a2001d4ef0efef2a157b0fd6
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834730"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>Trainings uitvoeringen in python starten, controleren en annuleren
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

De [Azure machine learning SDK voor python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py), [Machine Learning cli](reference-azure-machine-learning-cli.md)en [Azure machine learning Studio](https://ml.azure.com) bieden verschillende methoden om uw uitvoeringen te controleren, te organiseren en te beheren voor training en experimenten.

In dit artikel vindt u voor beelden van de volgende taken:

* Prestaties van uitvoering bewaken.
* Annuleren of niet uitvoeren.
* Onderliggende uitvoeringen maken.
* Uitvoeringen en zoeken.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig:

* Een Azure-abonnement. Als u nog geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree) .

* Een [Azure machine learning-werk ruimte](how-to-manage-workspace.md).

* De Azure Machine Learning SDK voor python (versie 1.0.21 of hoger). Zie [de SDK installeren of bijwerken](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)om de nieuwste versie van de SDK te installeren of bij te werken.

    Als u uw versie van de Azure Machine Learning SDK wilt controleren, gebruikt u de volgende code:

    ```python
    print(azureml.core.VERSION)
    ```

* De [Azure cli](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) -en [cli-extensie voor Azure machine learning](reference-azure-machine-learning-cli.md).

## <a name="start-a-run-and-its-logging-process"></a>Een uitvoering en het bijbehorende logboek registratie proces starten

### <a name="using-the-sdk"></a>De SDK gebruiken

Stel uw experiment in door de klassen [werk ruimte](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py), [experiment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py), [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py)en [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) te importeren vanuit het pakket [azureml. core](https://docs.microsoft.com/python/api/azureml-core/azureml.core?view=azure-ml-py) .

```python
import azureml.core
from azureml.core import Workspace, Experiment, Run
from azureml.core import ScriptRunConfig

ws = Workspace.from_config()
exp = Experiment(workspace=ws, name="explore-runs")
```

Start een run en het bijbehorende logboek registratie proces met de methode [`start_logging()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#start-logging--args----kwargs-) .

```python
notebook_run = exp.start_logging()
notebook_run.log(name="message", value="Hello from run!")
```

### <a name="using-the-cli"></a>De CLI gebruiken

Gebruik de volgende stappen om een uitvoering van uw experiment te starten:

1. Gebruik vanuit een shell of opdracht prompt de Azure CLI om u te verifiëren bij uw Azure-abonnement:

    ```azurecli-interactive
    az login
    ```

1. Een werkruimte configuratie koppelen aan de map die uw trainings script bevat. Vervang `myworkspace` door de Azure Machine Learning-werk ruimte. Vervang `myresourcegroup` door de Azure-resource groep die uw werk ruimte bevat:

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Met deze opdracht maakt u een `.azureml` submap die voor beelden van runconfig-en Conda-omgevings bestanden bevat. Het bevat ook een `config.json`-bestand dat wordt gebruikt om te communiceren met uw Azure Machine Learning-werk ruimte.

    Zie voor meer informatie [AZ ml map attach](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach).

2. Gebruik de volgende opdracht om de uitvoering te starten. Wanneer u deze opdracht gebruikt, geeft u de naam op van het runconfig-bestand (de tekst vóór \*. runconfig als u uw bestands systeem bekijkt) op basis van de para meter-c.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > De `az ml folder attach`-opdracht heeft een `.azureml`-submap gemaakt die twee voor beelden van runconfig-bestanden bevat.
    >
    > Als u een python-script hebt waarmee een configuratie object voor een uitvoering programmatisch wordt gemaakt, kunt u [RunConfig. Save ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-) gebruiken om het op te slaan als een RunConfig-bestand.
    >
    > Zie [https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml](https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml)voor meer voor beelden van runconfig-bestanden.

    Zie [AZ ml run-script uitvoeren](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script)voor meer informatie.

### <a name="using-azure-machine-learning-studio"></a>Azure Machine Learning Studio gebruiken

Gebruik de volgende stappen om een pijplijn uitvoering te starten in de ontwerp functie (preview):

1. Stel een standaard computie doel in voor de pijp lijn.

1. Selecteer **uitvoeren** boven aan het pijp lijn-canvas.

1. Selecteer een experiment om de pijplijn uitvoeringen te groeperen.

## <a name="monitor-the-status-of-a-run"></a>De status van een uitvoering controleren

### <a name="using-the-sdk"></a>De SDK gebruiken

De status ophalen van een run met de methode [`get_status()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-status--) .

```python
print(notebook_run.get_status())
```

Als u de uitvoerings-ID, uitvoerings tijd en aanvullende details over de uitvoering wilt ophalen, gebruikt u de [`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#get-details--) methode.

```python
print(notebook_run.get_details())
```

Wanneer de uitvoering is voltooid, gebruikt u de methode [`complete()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-) om deze te markeren als voltooid.

```python
notebook_run.complete()
print(notebook_run.get_status())
```

Als u het `with...as` ontwerp patroon van python gebruikt, wordt de uitvoering automatisch als voltooid gemarkeerd wanneer de uitvoering buiten het bereik valt. U hoeft de uitvoering niet hand matig te markeren als voltooid.

```python
with exp.start_logging() as notebook_run:
    notebook_run.log(name="message", value="Hello from run!")
    print(notebook_run.get_status())

print(notebook_run.get_status())
```

### <a name="using-the-cli"></a>De CLI gebruiken

1. Gebruik de volgende opdracht om een lijst met uitvoeringen voor uw experiment weer te geven. Vervang `experiment` door de naam van uw experiment:

    ```azurecli-interactive
    az ml run list --experiment-name experiment
    ```

    Met deze opdracht wordt een JSON-document geretourneerd dat informatie bevat over de uitvoeringen van dit experiment.

    Zie voor meer informatie [AZ ml experimenten List](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).

2. Als u informatie wilt weer geven over een specifieke uitvoering, gebruikt u de volgende opdracht. Vervang `runid` door de ID van de uitvoering:

    ```azurecli-interactive
    az ml run show -r runid
    ```

    Met deze opdracht wordt een JSON-document geretourneerd dat informatie bevat over de uitvoering.

    Zie voor meer informatie [AZ ml run show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-show).


### <a name="using-azure-machine-learning-studio"></a>Azure Machine Learning Studio gebruiken

Om het aantal actieve uitvoeringen voor uw experiment in de Studio weer te geven.

1. Navigeer naar het gedeelte **experimenten** .. 

1. Selecteer een experiment.

    Op de pagina experiment ziet u het aantal actieve Compute-doelen en de duur van elke uitvoering. 

1. Selecteer een specifiek uitvoerings nummer.

1. Op het tabblad **Logboeken** vindt u diagnostische en fout logboeken voor de pijplijn uitvoering.


## <a name="cancel-or-fail-runs"></a>Annuleren of mislukken wordt uitgevoerd

Als u een fout melding krijgt of als de uitvoering te lang duurt om te volt ooien, kunt u de uitvoering annuleren.

### <a name="using-the-sdk"></a>De SDK gebruiken

Als u een uitvoering met de SDK wilt annuleren, gebruikt u de methode [`cancel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#cancel--) :

```python
run_config = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')
local_script_run = exp.submit(run_config)
print(local_script_run.get_status())

local_script_run.cancel()
print(local_script_run.get_status())
```

Als de uitvoering is voltooid, maar er een fout is opgetreden (bijvoorbeeld het onjuiste trainings script is gebruikt), kunt u de methode [`fail()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)#fail-error-details-none--error-code-none---set-status-true-) gebruiken om deze te markeren als mislukt.

```python
local_script_run = exp.submit(run_config)
local_script_run.fail()
print(local_script_run.get_status())
```

### <a name="using-the-cli"></a>De CLI gebruiken

Als u een uitvoering wilt annuleren met de CLI, gebruikt u de volgende opdracht. `runid` vervangen door de ID van de uitvoering

```azurecli-interactive
az ml run cancel -r runid -w workspace_name -e experiment_name
```

Zie voor meer informatie [AZ ml run Cancel](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-cancel).

### <a name="using-azure-machine-learning-studio"></a>Azure Machine Learning Studio gebruiken

Als u een uitvoering in de Studio wilt annuleren, gebruikt u de volgende stappen:

1. Ga naar de actieve pijp lijn in de sectie **experimenten** of **pijp lijnen** . 

1. Selecteer het nummer van de pijplijn uitvoering dat u wilt annuleren.

1. Selecteer op de werk balk **Annuleren**


## <a name="create-child-runs"></a>Onderliggende uitvoeringen maken

Onderliggende uitvoeringen maken om gerelateerde uitvoeringen samen te voegen, zoals voor verschillende afstemming-afstemmings herhalingen.

> [!NOTE]
> Onderliggende uitvoeringen kunnen alleen worden gemaakt met de SDK.

In dit code voorbeeld wordt het `hello_with_children.py` script gebruikt voor het maken van een batch van vijf onderliggende uitvoeringen vanuit een ingediende uitvoering met behulp van de [`child_run()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#child-run-name-none--run-id-none--outputs-none-) methode:

```python
!more hello_with_children.py
run_config = ScriptRunConfig(source_directory='.', script='hello_with_children.py')

local_script_run = exp.submit(run_config)
local_script_run.wait_for_completion(show_output=True)
print(local_script_run.get_status())

with exp.start_logging() as parent_run:
    for c,count in enumerate(range(5)):
        with parent_run.child_run() as child:
            child.log(name="Hello from child run", value=c)
```

> [!NOTE]
> Als ze buiten het bereik worden verplaatst, worden onderliggende uitvoeringen automatisch gemarkeerd als voltooid.

Als u veel onderliggende items efficiënt wilt maken, gebruikt u de [`create_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#create-children-count-none--tag-key-none--tag-values-none-) methode. Omdat bij het maken van een netwerk aanroep een batch-uitvoer wordt gemaakt, is het maken van een reeks uitvoeringen efficiënter dan één voor één.

### <a name="submit-child-runs"></a>Onderliggende uitvoeringen verzenden

Onderliggende uitvoeringen kunnen ook worden verzonden vanuit een bovenliggende run. Hierdoor kunt u hiërarchieën van bovenliggende en onderliggende uitvoeringen maken, die elk worden uitgevoerd op verschillende Compute-doelen, verbonden door de gemeen schappelijke run-ID.

Gebruik de methode [submit_child ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#submit-child-config--tags-none----kwargs-) voor het verzenden van een onderliggende uitvoering vanuit een bovenliggende run. Als u dit wilt doen in het bovenliggende run script, haalt u de uitvoerings context op en verzendt u de onderliggende run met behulp van de ``submit_child`` methode van het context exemplaar.

```python
## In parent run script
parent_run = Run.get_context()
child_run_config = ScriptRunConfig(source_directory='.', script='child_script.py')
parent_run.submit_child(child_run_config)
```

Binnen een onderliggende run kunt u de bovenliggende run-ID bekijken:

```python
## In child run script
child_run = Run.get_context()
child_run.parent.id
```

### <a name="query-child-runs"></a>Query's uitvoeren op onderliggende items

Als u een query wilt uitvoeren op de onderliggende uitvoeringen van een specifiek bovenliggend element, gebruikt u de methode [`get_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) . Met het argument ``recursive = True`` kunt u een geneste structuur van onderliggende items en grandchildren opvragen.

```python
print(parent_run.get_children())
```

## <a name="tag-and-find-runs"></a>Uitvoeringen coderen en zoeken

In Azure Machine Learning kunt u eigenschappen en tags gebruiken om uw uitvoeringen te organiseren en query's uit te voeren op belang rijke informatie.

### <a name="add-properties-and-tags"></a>Eigenschappen en Tags toevoegen

#### <a name="using-the-sdk"></a>De SDK gebruiken

Gebruik de methode [`add_properties()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#add-properties-properties-) om Doorzoek bare meta gegevens toe te voegen aan uw uitvoeringen. Met de volgende code wordt bijvoorbeeld de eigenschap `"author"` toegevoegd aan de run:

```Python
local_script_run.add_properties({"author":"azureml-user"})
print(local_script_run.get_properties())
```

Eigenschappen zijn onveranderbaar, zodat ze een permanente record voor controle doeleinden maken. In het volgende code voorbeeld wordt een fout veroorzaakt, omdat we `"azureml-user"` al hebben toegevoegd als de `"author"` eigenschaps waarde in de voor gaande code:

```Python
try:
    local_script_run.add_properties({"author":"different-user"})
except Exception as e:
    print(e)
```

In tegens telling tot eigenschappen zijn Tags onveranderbaar. Als u zoek bare en nuttige informatie voor gebruikers van uw experiment wilt toevoegen, gebruikt u de [`tag()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#tag-key--value-none-) methode.

```Python
local_script_run.tag("quality", "great run")
print(local_script_run.get_tags())

local_script_run.tag("quality", "fantastic run")
print(local_script_run.get_tags())
```

U kunt ook eenvoudige teken reeks Tags toevoegen. Wanneer deze tags worden weer gegeven in de code woordenlijst als sleutels, hebben ze de waarde `None`.

```Python
local_script_run.tag("worth another look")
print(local_script_run.get_tags())
```

#### <a name="using-the-cli"></a>De CLI gebruiken

> [!NOTE]
> Met de CLI kunt u alleen Tags toevoegen of bijwerken.

Gebruik de volgende opdracht om een tag toe te voegen of bij te werken:

```azurecli-interactive
az ml run update -r runid --add-tag quality='fantastic run'
```

Zie [AZ ml run update](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-update)(Engelstalig) voor meer informatie.

### <a name="query-properties-and-tags"></a>Query-eigenschappen en-labels

U kunt binnen een experiment een query uitvoeren om een lijst met uitvoeringen te retour neren die overeenkomen met specifieke eigenschappen en tags.

#### <a name="using-the-sdk"></a>De SDK gebruiken

```Python
list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
```

#### <a name="using-the-cli"></a>De CLI gebruiken

De Azure CLI ondersteunt [JMESPath](http://jmespath.org) -query's, die kunnen worden gebruikt voor het filteren van uitvoeringen op basis van eigenschappen en labels. Als u een JMESPath-query wilt gebruiken met de Azure CLI, geeft u deze op met de para meter `--query`. In de volgende voor beelden ziet u eenvoudige query's met behulp van eigenschappen en Tags:

```azurecli-interactive
# list runs where the author property = 'azureml-user'
az ml run list --experiment-name experiment [?properties.author=='azureml-user']
# list runs where the tag contains a key that starts with 'worth another look'
az ml run list --experiment-name experiment [?tags.keys(@)[?starts_with(@, 'worth another look')]]
# list runs where the author property = 'azureml-user' and the 'quality' tag starts with 'fantastic run'
az ml run list --experiment-name experiment [?properties.author=='azureml-user' && tags.quality=='fantastic run']
```

Zie voor meer informatie over het uitvoeren van query's in azure CLI-resultaten query uitvoeren op [uitvoer van Azure cli-opdracht](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest).

### <a name="using-azure-machine-learning-studio"></a>Azure Machine Learning Studio gebruiken

1. Navigeer naar het gedeelte **pijp lijnen** .

1. Gebruik de zoek balk om pijp lijnen te filteren met behulp van tags, beschrijvingen, namen van experimenten en naam indiener.

## <a name="example-notebooks"></a>Voorbeeld-laptops

De volgende notitie blokken illustreren de concepten in dit artikel:

* Zie de [API-notebook voor logboek registratie](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api/logging-api.ipynb)voor meer informatie over de logboek registratie-api's.

* Zie voor meer informatie over het beheren van uitvoeringen met de Azure Machine Learning SDK, het [notitie blok voor uitvoeringen beheren](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/manage-runs/manage-runs.ipynb).

## <a name="next-steps"></a>Volgende stappen

* Voor informatie over het vastleggen van metrische gegevens voor uw experimenten, Zie [metrische logboek gegevens tijdens trainings uitvoeringen](how-to-track-experiments.md).
* Zie [Azure machine learning bewaken](monitor-azure-machine-learning.md)voor meer informatie over het bewaken van resources en logboeken van Azure machine learning.