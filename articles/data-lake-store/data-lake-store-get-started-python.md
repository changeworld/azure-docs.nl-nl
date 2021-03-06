---
title: 'Python: account beheer bewerkingen op Azure Data Lake Storage Gen1 | Microsoft Docs'
description: Meer informatie over het gebruik van python SDK voor het werken met Azure Data Lake Storage Gen1 account beheer bewerkingen.
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 75f6de6f-6fd8-48f4-8707-cb27d22d27a6
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: c71136ecb57fac460514b5f4815ba19cc22d86cb
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2020
ms.locfileid: "76290611"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-python"></a>Account beheer bewerkingen op Azure Data Lake Storage Gen1 met behulp van python
> [!div class="op_single_selector"]
> * [.NET-SDK](data-lake-store-get-started-net-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Meer informatie over het gebruik van de python-SDK voor Azure Data Lake Storage Gen1 voor het uitvoeren van elementaire account beheer bewerkingen, zoals het maken van een Data Lake Storage Gen1 account, het weer geven van de Data Lake Storage Gen1 accounts, enzovoort. Zie [bestandssysteem bewerkingen op Data Lake Storage gen1 met python](data-lake-store-data-operations-python.md)voor instructies over het uitvoeren van bestandssysteem bewerkingen op Data Lake Storage gen1 met python.

## <a name="prerequisites"></a>Vereisten

* **Python**. U kunt Python [hier](https://www.python.org/downloads/) downloaden. In dit artikel wordt Python 3.6.2 gebruikt.

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

* **Een Azure-resourcegroep**. Zie [Een Azure-resourcegroep maken](../azure-resource-manager/management/manage-resource-groups-portal.md) voor instructies.

## <a name="install-the-modules"></a>De modules installeren

Als u wilt werken met Data Lake Storage Gen1 met behulp van python, moet u drie modules installeren.

* Module `azure-mgmt-resource`, die Azure-modules bevat voor Active Directory enzovoort.
* De module `azure-mgmt-datalake-store`, met inbegrip van de Azure Data Lake Storage Gen1 account beheer bewerkingen. Zie voor meer informatie over deze module, de [referentie voor Azure data Lake Storage gen1 management module](/python/api/azure-mgmt-datalake-store/).
* De module `azure-datalake-store`, die de Azure Data Lake Storage Gen1 bestandssysteem bewerkingen bevat. Zie voor meer informatie over deze module [Azure-datalake-Store module Reference](https://docs.microsoft.com/python/api/azure-datalake-store/azure.datalake.store.core/).

Gebruik de volgende opdrachten om de modules te installeren.

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Een nieuwe Python-toepassing maken

1. Gebruik de gewenste IDE om een nieuwe Python-toepassing te maken, bijvoorbeeld **mysample.py**.

2. Voeg het volgende codefragment toe om de vereiste modules te importeren

    ```
    ## Use this only for Azure AD service-to-service authentication
    from azure.common.credentials import ServicePrincipalCredentials

    ## Use this only for Azure AD end-user authentication
    from azure.common.credentials import UserPassCredentials

    ## Use this only for Azure AD multi-factor authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Data Lake Storage Gen1 filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, getpass, pprint, uuid, time
    ```

3. Sla de wijzigingen in mysample.py op.

## <a name="authentication"></a>Verificatie

In deze sectie bespreken we de verschillende manieren om te verifiëren met Azure AD. De beschikbare opties zijn:

* Voor verificatie door eind gebruikers voor uw toepassing raadpleegt [u verificatie door eind gebruikers met data Lake Storage gen1 met behulp van python](data-lake-store-end-user-authenticate-python.md).
* Zie [service-to-service-verificatie met data Lake Storage gen1 met behulp van python](data-lake-store-service-to-service-authenticate-python.md)voor service-naar-service verificatie voor uw toepassing.

## <a name="create-client-and-data-lake-storage-gen1-account"></a>Client-en Data Lake Storage Gen1 account maken

In het volgende code fragment maakt u eerst de client voor het Data Lake Storage Gen1-account. Er wordt gebruikgemaakt van het-client object om een Data Lake Storage Gen1-account te maken. Ten slotte maakt het fragment een clientobject voor het bestandssysteem.

    ## Declare variables
    subscriptionId = 'FILL-IN-HERE'
    adlsAccountName = 'FILL-IN-HERE'
    resourceGroup = 'FILL-IN-HERE'
    location = 'eastus2'

    ## Create Data Lake Storage Gen1 account management client object
    adlsAcctClient = DataLakeStoreAccountManagementClient(armCreds, subscriptionId)

    ## Create a Data Lake Storage Gen1 account
    adlsAcctResult = adlsAcctClient.account.create(
        resourceGroup,
        adlsAccountName,
        DataLakeStoreAccount(
            location=location
        )
    ).wait()

    
## <a name="list-the-data-lake-storage-gen1-accounts"></a>De Data Lake Storage Gen1-accounts weer geven

    ## List the existing Data Lake Storage Gen1 accounts
    result_list_response = adlsAcctClient.account.list()
    result_list = list(result_list_response)
    for items in result_list:
        print(items)

## <a name="delete-the-data-lake-storage-gen1-account"></a>Het Data Lake Storage Gen1 account verwijderen

    ## Delete an existing Data Lake Storage Gen1 account
    adlsAcctClient.account.delete(adlsAccountName)
    

## <a name="next-steps"></a>Volgende stappen
* [Bestandssysteem bewerkingen op Data Lake Storage gen1 met behulp van python](data-lake-store-data-operations-python.md).

## <a name="see-also"></a>Zie ook

* [Naslag informatie over Azure-datalake-Store python (File System)](https://docs.microsoft.com/python/api/azure-datalake-store/azure.datalake.store.core)
* [Open source Big Data-toepassingen die compatibel zijn met Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)
