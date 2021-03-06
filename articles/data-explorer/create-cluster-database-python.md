---
title: Een Azure Data Explorer-cluster maken & DB met behulp van python
description: Meer informatie over het maken van een Azure Data Explorer-cluster en-data base met behulp van python.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 8425058c9f6ac5b90c37a99f749a810672b406fc
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/22/2020
ms.locfileid: "77560504"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-python"></a>Een Azure Data Explorer-cluster en-data base maken met behulp van python

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [ARM-sjabloon](create-cluster-database-resource-manager.md)

In dit artikel maakt u een Azure Data Explorer-cluster en-data base met behulp van python. Azure Data Explorer is een snelle, volledig beheerde service voor gegevensanalyses waarmee grote hoeveelheden gegevens van toepassingen, websites, IoT-apparaten en dergelijke in real-time kunnen worden geanalyseerd. Als u Azure Data Explorer wilt gebruiken, maakt u eerst een cluster en maakt u een of meer data bases in dat cluster. Vervolgens gegevens opnemen of laden in een Data Base, zodat u er query's op kunt uitvoeren.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Maak er gratis een](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Python 3.4 +](https://www.python.org/downloads/).

* [Een Azure AD-toepassing en service-principal die toegang hebben tot resources](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal). Waarden ophalen voor `Directory (tenant) ID`, `Application ID`en `Client Secret`.

## <a name="install-python-package"></a>Python-pakket installeren

Als u het python-pakket voor Azure Data Explorer (Kusto) wilt installeren, opent u een opdracht prompt met python in het pad. Voer deze opdracht uit:

```
pip install azure-common
pip install azure-mgmt-kusto
```
## <a name="authentication"></a>Authentication
Voor het uitvoeren van de voor beelden in dit artikel hebben we een Azure AD-toepassing en service-principal nodig die toegang hebben tot resources. Schakel het selectie vakje [een Azure AD-toepassing maken](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) in om een gratis Azure AD-toepassing te maken en roltoewijzing toe te voegen aan het abonnements bereik. U ziet ook hoe u de `Directory (tenant) ID`, `Application ID`en `Client Secret`kunt ophalen.

## <a name="create-the-azure-data-explorer-cluster"></a>Het Azure Data Explorer-cluster maken

1. Maak uw cluster met behulp van de volgende opdracht:

    ```Python
    from azure.mgmt.kusto import KustoManagementClient
    from azure.mgmt.kusto.models import Cluster, AzureSku
    from azure.common.credentials import ServicePrincipalCredentials

    #Directory (tenant) ID
    tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
    #Application ID
    client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
    #Client Secret
    client_secret = "xxxxxxxxxxxxxx"
    subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
    credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )

    location = 'Central US'
    sku_name = 'Standard_D13_v2'
    capacity = 5
    tier = "Standard"
    resource_group_name = 'testrg'
    cluster_name = 'mykustocluster'
    cluster = Cluster(location=location, sku=AzureSku(name=sku_name, capacity=capacity, tier=tier))
    
    kustoManagementClient = KustoManagementClient(credentials, subscription_id)
    
    cluster_operations = kustoManagementClient.clusters
    
    poller = cluster_operations.create_or_update(resource_group_name, cluster_name, cluster)
    ```

   |**Instelling** | **Voorgestelde waarde** | **Beschrijving van veld**|
   |---|---|---|
   | cluster_name | *mykustocluster* | De gewenste naam van uw cluster.|
   | sku_name | *Standard_D13_v2* | De SKU die wordt gebruikt voor uw cluster. |
   | laag | *Standard* | De SKU-laag. |
   | capacity | *Telwoord* | Het aantal exemplaren van het cluster. |
   | resource_group_name | *testrg* | Naam van de resourcegroep waar het cluster wordt gemaakt. |

    > [!NOTE]
    > **Een cluster maken** is een langlopende bewerking. **Create_or_update** -methode retourneert een exemplaar van LROPoller. Zie [LROPoller](/python/api/msrest/msrest.polling.lropoller?view=azure-python) voor meer informatie.

1. Voer de volgende opdracht uit om te controleren of het cluster is gemaakt:

    ```Python
    cluster_operations.get(resource_group_name = resource_group_name, cluster_name= clusterName, custom_headers=None, raw=False)
    ```

Als het resultaat `provisioningState` met waarde `Succeeded` bevat, is het maken van het cluster geslaagd.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>De database in het Azure Data Explorer-cluster maken

1. Maak uw database met behulp van de volgende opdracht:

    ```Python
    from azure.mgmt.kusto.models import Database
    from datetime import timedelta
    
    softDeletePeriod = timedelta(days=3650)
    hotCachePeriod = timedelta(days=3650)
    databaseName="mykustodatabase"
    
    database_operations = kusto_management_client.databases 
    _database = ReadWriteDatabase(location=location,
                        soft_delete_period=softDeletePeriod,
                        hot_cache_period=hotCachePeriod)
    
    #Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
    poller =database_operations.create_or_update(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName, parameters = _database)
    ```

        [!NOTE]
        If you are using Python version 0.4.0 or below, use Database instead of ReadWriteDatabase.

   |**Instelling** | **Voorgestelde waarde** | **Beschrijving van veld**|
   |---|---|---|
   | cluster_name | *mykustocluster* | De naam van het cluster waar de database wordt gemaakt.|
   | database_name | *mykustodatabase* | De naam van uw database.|
   | resource_group_name | *testrg* | Naam van de resourcegroep waar het cluster wordt gemaakt. |
   | soft_delete_period | *3650 dagen, 0:00:00* | Hoe lang gegevens worden opgeslagen om query's erop te kunnen uitvoeren. |
   | hot_cache_period | *3650 dagen, 0:00:00* | Hoe lang gegevens worden opgeslagen in de cache. |

1. Voer de volgende opdracht uit om de database te bekijken die u hebt gemaakt:

    ```Python
    database_operations.get(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName)
    ```

U hebt nu een cluster en een database.

## <a name="clean-up-resources"></a>Resources opschonen

* Als u van plan bent om onze andere artikelen te volgen, moet u de resources die u hebt gemaakt, blijven gebruiken.
* Als u resources wilt opschonen, moet u het cluster verwijderen. Wanneer u een cluster verwijdert, worden alle databases hierin ook verwijderd. Gebruik de volgende opdracht om uw cluster te verwijderen:

    ```Python
    cluster_operations.delete(resource_group_name = resource_group_name, cluster_name = clusterName)
    ```

## <a name="next-steps"></a>Volgende stappen

* [Gegevens opnemen met behulp van de Azure Data Explorer python-bibliotheek](python-ingest-data.md)
