---
title: 使用 Python 管理 Azure Data Lake Storage Gen1 帐户
description: 了解如何使用 Python SDK 进行 Azure Data Lake Storage Gen1 帐户管理操作。
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: a0c27c4b6d906a0892735697a8e90f87da6edf9c
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82692109"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-python"></a>使用 Python 对 Azure Data Lake Storage Gen1 进行的帐户管理操作
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

了解如何使用 Python SDK for Azure Data Lake Storage Gen1 来执行基本帐户管理操作，例如创建 Data Lake Storage Gen1 帐户、列出 Data Lake Storage Gen1 帐户等。有关如何使用 Python 在 Data Lake Storage Gen1 上执行文件系统操作的说明，请参阅[使用 python 对 Data Lake Storage Gen1 执行文件系统操作](data-lake-store-data-operations-python.md)。

## <a name="prerequisites"></a>先决条件

* **Python**。 可以从[此处](https://www.python.org/downloads/)下载 Python。 本文使用的是 Python 3.6.2。

* **一个 Azure 订阅**。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

* **一个 Azure 资源组**。 有关说明，请参阅[创建 Azure 资源组](../azure-resource-manager/management/manage-resource-groups-portal.md)。

## <a name="install-the-modules"></a>安装模块

若要通过 Python 使用 Data Lake Storage Gen1，需要安装三个模块。

* `azure-mgmt-resource` 模块，包括用于 Active Directory 的 Azure 模块，等等。
* `azure-mgmt-datalake-store` 模块，包括 Azure Data Lake Storage Gen1 帐户管理操作。 有关此模块的详细信息，请参阅 [Azure Data Lake Storage Gen1 管理模块参考](/python/api/azure-mgmt-datalake-store/)。
* `azure-datalake-store` 模块，包括 Azure Data Lake Storage Gen1 文件系统操作。 有关此模块的详细信息，请参阅 [azure-datalake-store 文件系统模块参考](https://docs.microsoft.com/python/api/azure-datalake-store/azure.datalake.store.core/)。

使用以下命令安装这些模块。

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>创建新的 Python 应用程序

1. 在所选的 IDE 中创建新的 Python 应用程序，例如**mysample.py**。

2. 添加以下代码片段以导入所需的模块

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

3. 将更改保存到 mysample.py。

## <a name="authentication"></a>身份验证

本部分介绍有关使用 Azure AD 进行身份验证的不同方式。 可用选项包括：

* 有关应用程序的最终用户身份验证，请参阅[使用 Python 通过 Data Lake Storage Gen1 进行最终用户身份验证](data-lake-store-end-user-authenticate-python.md)。
* 有关应用程序的服务到服务身份验证，请参阅[使用 Python 通过 Data Lake Storage Gen1 进行服务到服务身份验证](data-lake-store-service-to-service-authenticate-python.md)。

## <a name="create-client-and-data-lake-storage-gen1-account"></a>创建客户端和 Data Lake Storage Gen1 帐户

以下代码段首先创建 Data Lake Storage Gen1 帐户客户端。 它使用客户端对象来创建一个 Data Lake Storage Gen1 帐户。 最后，此代码段创建一个文件系统客户端对象。

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

    
## <a name="list-the-data-lake-storage-gen1-accounts"></a>列出 Data Lake Storage Gen1 帐户

    ## List the existing Data Lake Storage Gen1 accounts
    result_list_response = adlsAcctClient.account.list()
    result_list = list(result_list_response)
    for items in result_list:
        print(items)

## <a name="delete-the-data-lake-storage-gen1-account"></a>删除 Data Lake Storage Gen1 帐户

    ## Delete an existing Data Lake Storage Gen1 account
    adlsAcctClient.account.delete(adlsAccountName)
    

## <a name="next-steps"></a>后续步骤
* [使用 Python 在 Data Lake Storage Gen1 上进行的文件系统操作](data-lake-store-data-operations-python.md)。

## <a name="see-also"></a>另请参阅

* [azure-datalake-store Python（文件系统）参考](https://docs.microsoft.com/python/api/azure-datalake-store/azure.datalake.store.core)
* [与 Azure Data Lake Storage Gen1 兼容的开源大数据应用程序](data-lake-store-compatible-oss-other-applications.md)
