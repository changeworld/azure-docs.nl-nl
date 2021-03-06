---
title: Scikit trainen-meer informatie over machine learning modellen
titleSuffix: Azure Machine Learning
description: Meer informatie over het uitvoeren van uw scikit-training-trainings scripts op ondernemings schaal met behulp van de Estimator-klasse Azure Machine Learning SKlearn. De voorbeeld scripts classificeren de installatie van Iris bloem om een machine learning model te bouwen op basis van de Iris-gegevensset van scikit-leer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 03/09/2020
ms.custom: seodec18
ms.openlocfilehash: bdd2cc400c3df75742689258caea8cb87ee8ccc6
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2020
ms.locfileid: "78942257"
---
# <a name="build-scikit-learn-models-at-scale-with-azure-machine-learning"></a>Scikit bouwen-modellen op schaal leren met Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u hoe u uw scikit-trainings scripts kunt uitvoeren op ENTER prise Scale door gebruik te maken van de Estimator-klasse Azure Machine Learning [SKlearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) . 

De voorbeeld scripts in dit artikel worden gebruikt voor het classificeren van Iris bloem installatie kopieën om een machine learning model te bouwen op basis van de [Iris-gegevensset](https://archive.ics.uci.edu/ml/datasets/iris)van scikit-leer.

Of u nu op de hoogte bent van een machine learning scikit-model of u een bestaand model in de Cloud hebt, kunt u Azure Machine Learning gebruiken om open-source trainings taken uit te schalen met behulp van elastische Cloud Compute-resources. U kunt productie-kwaliteits modellen bouwen, implementeren, versie en bewaken met Azure Machine Learning.

## <a name="prerequisites"></a>Vereisten

Voer deze code uit in een van de volgende omgevingen:
 - Azure Machine Learning Compute-instantie-geen down loads of installatie vereist

    - Voltooi de [zelf studie: installatie omgeving en werk ruimte](tutorial-1st-experiment-sdk-setup.md) om een toegewezen notebook server te maken vooraf geladen met de SDK en de voor beeld-opslag plaats.
    - Zoek in de map met voor beelden-trainingen op de notebook server een volledig en uitgebreid notitie blok door naar deze map te navigeren: **How-to-use-azureml > ml-frameworks > scikit-leer > training > Train-afstemming-Tune-Deploy-with-sklearn** .

 - Uw eigen Jupyter Notebook-server

    - [Installeer de Azure machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Maak een configuratie bestand voor de werk ruimte](how-to-configure-environment.md#workspace).
    - Het gegevensset-en voorbeeld script bestand downloaden 
        - [Iris gegevensset](https://archive.ics.uci.edu/ml/datasets/iris)
        - [train_iris. py](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn)
    - U kunt ook een voltooide [Jupyter notebook versie](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb) van deze hand leiding vinden op de pagina met voor beelden van github. De notebook bevat een uitgebreide sectie voor het afstemmen van intelligente afstemming en het ophalen van het beste model op basis van primaire meet waarden.

## <a name="set-up-the-experiment"></a>Het experiment instellen

In deze sectie wordt het trainings experiment opgesteld door het laden van de vereiste Python-pakketten, het initialiseren van een werk ruimte, het maken van een experiment en het uploaden van de trainings gegevens en trainings scripts.

### <a name="import-packages"></a>Pakketten importeren

Importeer eerst de benodigde python-bibliotheken.

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Een werk ruimte initialiseren

De [Azure machine learning werk ruimte](concept-workspace.md) is de resource op het hoogste niveau voor de service. Het biedt u een centrale locatie voor het werken met alle artefacten die u maakt. In de python-SDK hebt u toegang tot de werkruimte artefacten door een [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) -object te maken.

Maak een werkruimte object vanuit het `config.json` bestand dat in de [sectie vereisten](#prerequisites)is gemaakt.

```Python
ws = Workspace.from_config()
```

### <a name="create-a-machine-learning-experiment"></a>Een machine learning-experiment maken

Maak een experiment en een map om uw trainings scripts te bewaren. In dit voor beeld maakt u een experiment met de naam ' sklearn-Iris '.

```Python
project_folder = './sklearn-iris'
os.makedirs(project_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='sklearn-iris')
```

### <a name="prepare-training-script"></a>Trainings script voorbereiden

In deze zelf studie wordt het trainings script **train_iris. py** al voor u ingevuld. In de praktijk moet u een aangepast trainings script kunnen uitvoeren zoals dat is en dit kan worden uitgevoerd met Azure ML zonder dat u de code hoeft te wijzigen.

Als u de mogelijkheden voor bijhouden en metrische gegevens van Azure ML wilt gebruiken, voegt u een kleine hoeveelheid Azure ML-code toe binnen uw trainings script.  In het trainings script **train_iris. py** wordt uitgelegd hoe u met het `Run`-object in het script bepaalde metrische gegevens registreert bij uw Azure ml.

In het meegeleverde trainings script worden voorbeeld gegevens uit de `iris = datasets.load_iris()` functie gebruikt.  Voor uw eigen gegevens moet u mogelijk stappen zoals [gegevensset uploaden en scripts](how-to-train-keras.md#data-upload) gebruiken om gegevens beschikbaar te maken tijdens de training.

Kopieer het trainings script **train_iris. py** in de projectmap.

```
import shutil
shutil.copy('./train_iris.py', project_folder)
```

## <a name="create-or-get-a-compute-target"></a>Een reken doel maken of ophalen

Maak een compute-doel voor uw scikit-leer taak om uit te voeren. Scikit: biedt alleen ondersteuning voor één knoop punt, CPU computing.

Met de volgende code wordt een Azure Machine Learning beheerde Compute (AmlCompute) gemaakt voor uw externe trainings Compute-resource. Het maken van AmlCompute duurt ongeveer 5 minuten. Als de AmlCompute met die naam al in uw werk ruimte staat, wordt het aanmaak proces overs Laan.

```Python
cluster_name = "cpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

Zie voor meer informatie over Compute-doelen het artikel [Wat is een reken doel](concept-compute-target.md) .

## <a name="create-a-scikit-learn-estimator"></a>Een scikit maken-leer estimator

De [scikit-Learn Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn?view=azure-ml-py) biedt een eenvoudige manier om een scikit-training-trainings taak te starten op een compute-doel. Het wordt geïmplementeerd via de klasse [`SKLearn`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) , die kan worden gebruikt voor het ondersteunen van CPU-training met één knoop punt.

Als uw trainings script extra PIP-of Conda-pakketten nodig heeft om uit te voeren, kunt u de pakketten op de resulterende docker-installatie kopie installeren door hun namen door te geven via de argumenten `pip_packages` en `conda_packages`.

```Python
from azureml.train.sklearn import SKLearn

script_params = {
    '--kernel': 'linear',
    '--penalty': 1.0,
}

estimator = SKLearn(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train_iris.py'
                    pip_packages=['joblib']
                   )
```


Zie [omgevingen maken en beheren voor training en implementatie](how-to-use-environments.md)voor meer informatie over het aanpassen van uw python-omgeving. 

## <a name="submit-a-run"></a>Een run verzenden

Het [object run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) biedt de interface voor de uitvoerings geschiedenis terwijl de taak wordt uitgevoerd en nadat deze is voltooid.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

Wanneer de uitvoering wordt uitgevoerd, worden de volgende fasen door lopen:

- **Voorbereiden**: een docker-installatie kopie wordt gemaakt op basis van de tensor flow-Estimator. De afbeelding wordt geüpload naar het container register van de werk ruimte en opgeslagen in de cache voor latere uitvoeringen. Logboeken worden ook gestreamd naar de uitvoerings geschiedenis en kunnen worden weer gegeven om de voortgang te bewaken.

- **Schalen**: het cluster probeert omhoog te schalen als het batch AI-cluster meer knoop punten nodig heeft om de uitvoering uit te voeren dan momenteel beschikbaar zijn.

- **Uitvoeren**: alle scripts in de map script worden geüpload naar het Compute-doel, gegevens archieven worden gekoppeld of gekopieerd en de entry_script worden uitgevoerd. Uitvoer van stdout en de map./logs worden gestreamd naar de uitvoerings geschiedenis en kunnen worden gebruikt om de uitvoering te bewaken.

- **Na de verwerking**: de map./outputs van de uitvoering wordt gekopieerd naar de uitvoerings geschiedenis.

## <a name="save-and-register-the-model"></a>Het model opslaan en registreren

Zodra u het model hebt getraind, kunt u het opslaan en registreren in uw werk ruimte. Met model registratie kunt u uw modellen in uw werk ruimte opslaan en versieren om het [model beheer en de implementatie](concept-model-management-and-deployment.md)te vereenvoudigen.

Voeg de volgende code toe aan het trainings script, train_iris. py, om het model op te slaan. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Registreer het model in uw werk ruimte met de volgende code. Als u de para meters `model_framework`, `model_framework_version`en `resource_configuration`opgeeft, wordt de implementatie van geen code model beschikbaar. Zo kunt u uw model rechtstreeks implementeren als een webservice van het geregistreerde model en het [`ResourceConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.resource_configuration.resourceconfiguration?view=azure-ml-py) -object definieert de reken resource voor de webservice.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='sklearn-iris', 
                           model_path='outputs/model.joblib',
                           model_framework=Model.Framework.SCIKITLEARN,
                           model_framework_version='0.19.1',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

## <a name="deployment"></a>Implementatie

Het model dat u zojuist hebt geregistreerd, kan op dezelfde manier worden geïmplementeerd als andere geregistreerde modellen in Azure Machine Learning, ongeacht welke Estimator u hebt gebruikt voor de training. De implementatie-instructie bevat een sectie over het registreren van modellen, maar u kunt direct door gaan naar het [maken van een reken doel](how-to-deploy-and-where.md#choose-a-compute-target) voor implementatie, omdat u al een geregistreerd model hebt.

### <a name="preview-no-code-model-deployment"></a>Evaluatie Implementatie van geen code model

In plaats van de traditionele implementatie route kunt u ook de functie voor het implementeren van geen code (preview) gebruiken voor scikit-learn. Implementatie van geen code model wordt ondersteund voor alle ingebouwde scikit-informatie over model typen. Als u het model registreert zoals hierboven wordt weer gegeven, kunt u de para meters van het `model_framework`, `model_framework_version`en `resource_configuration` gewoon [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) gebruiken om uw model te implementeren.

```python
web_service = Model.deploy(ws, "scikit-learn-service", [model])
```

Opmerking: deze afhankelijkheden zijn opgenomen in de vooraf gemaakte scikit.

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

De volledige [instructies voor](how-to-deploy-and-where.md) het implementeren van een implementatie in azure machine learning meer dieper.


## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een scikit-leer model getraind en geregistreerd en hebt u geleerd over implementatie opties. Raadpleeg de volgende artikelen voor meer informatie over Azure Machine Learning.

* [Metrische uitvoerings gegevens tijdens de training volgen](how-to-track-experiments.md)
* [Hyper parameters afstemmen](how-to-tune-hyperparameters.md)
* [Referentie architectuur voor gedistribueerde training van diep gaande lessen in azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
