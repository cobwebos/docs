---
title: 最终用户身份验证-Data Lake Storage Gen1-Azure 的 Python
description: 了解如何通过 Python 使用 Azure Active Directory 进行 Azure Data Lake Storage Gen1 最终用户身份验证
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.custom: has-adal-ref
ms.openlocfilehash: 6d95e8bae428741c82de270507e41b49d23a3793
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691793"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-python"></a>通过 Python 使用 Azure Data Lake Storage Gen1 进行最终用户身份验证
> [!div class="op_single_selector"]
> * [使用 Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [使用 .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [使用 Python](data-lake-store-end-user-authenticate-python.md)
> * [使用 REST API](data-lake-store-end-user-authenticate-rest-api.md)
>
>

本文介绍如何使用 Python SDK 进行 Azure Data Lake Storage Gen1 最终用户身份验证。 最终用户身份验证可以进一步拆分为两个类别：

* 无需多重身份验证的最终用户身份验证
* 使用多重身份验证的最终用户身份验证

本文将讨论这两个选项。 若要了解使用 Python 的 Data Lake Storage Gen1 服务到服务身份验证，请参阅[使用 Python 进行 Data Lake Storage Gen1 的服务到服务身份验证](data-lake-store-service-to-service-authenticate-python.md)。

## <a name="prerequisites"></a>先决条件

* **Python**。 可以从[此处](https://www.python.org/downloads/)下载 Python。 本文使用的是 Python 3.6.2。

* **一个 Azure 订阅**。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

* **创建 Azure Active Directory“本机”应用程序**。 必须已完成[使用 Azure Active Directory 进行 Data Lake Storage Gen1 最终用户身份验证](data-lake-store-end-user-authenticate-using-active-directory.md)中的步骤。

## <a name="install-the-modules"></a>安装模块

若要通过 Python 使用 Data Lake Storage Gen1，需要安装三个模块。

* `azure-mgmt-resource` 模块，包括用于 Active Directory 的 Azure 模块，等等。
* `azure-mgmt-datalake-store` 模块，包括 Azure Data Lake Storage Gen1 帐户管理操作。 有关此模块的详细信息，请参阅 [Azure Data Lake Storage Gen1 管理模块参考](/python/api/azure-mgmt-datalake-store/)。
* `azure-datalake-store` 模块，包括 Azure Data Lake Storage Gen1 文件系统操作。 有关此模块的详细信息，请参阅[datalake-存储文件系统模块参考](https://docs.microsoft.com/python/api/azure-datalake-store/azure.datalake.store.core/)。

使用以下命令安装这些模块。

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>创建新的 Python 应用程序

1. 在所选的 IDE 中创建新的 Python 应用程序，例如 **mysample.py**。

2. 添加以下代码片段以导入所需的模块

    ```
    ## Use this for Azure AD authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Azure Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Azure Data Lake Storage Gen1 filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    import adal
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, pprint, uuid, time
    ```

3. 将更改保存到 mysample.py。

## <a name="end-user-authentication-with-multi-factor-authentication"></a>使用多重身份验证的最终用户身份验证

### <a name="for-account-management"></a>适用于帐户管理

使用以下代码片段可对 Data Lake Storage Gen1 帐户的帐户管理操作进行 Azure AD 身份验证。 可以使用以下代码片段通过多重身份验证在应用程序中进行身份验证。 为现有 Azure AD **本机**应用程序提供以下值。

    authority_host_url = "https://login.microsoftonline.com"
    tenant = "FILL-IN-HERE"
    authority_url = authority_host_url + '/' + tenant
    client_id = 'FILL-IN-HERE'
    redirect = 'urn:ietf:wg:oauth:2.0:oob'
    RESOURCE = 'https://management.core.windows.net/'

    context = adal.AuthenticationContext(authority_url)
    code = context.acquire_user_code(RESOURCE, client_id)
    print(code['message'])
    mgmt_token = context.acquire_token_with_device_code(RESOURCE, code, client_id)
    armCreds = AADTokenCredentials(mgmt_token, client_id, resource = RESOURCE)

### <a name="for-filesystem-operations"></a>适用于文件系统操作

使用此方法可对 Data Lake Storage Gen1 帐户的文件系统操作进行 Azure AD 身份验证。 可以使用以下代码片段通过多重身份验证在应用程序中进行身份验证。 为现有 Azure AD **本机**应用程序提供以下值。

    adlCreds = lib.auth(tenant_id='FILL-IN-HERE', resource = 'https://datalake.azure.net/')

## <a name="end-user-authentication-without-multi-factor-authentication"></a>无需多重身份验证的最终用户身份验证

此方法已弃用。 有关详细信息，请参阅[使用 Python SDK 的 Azure 身份验证](/azure/python/python-sdk-azure-authenticate)。

## <a name="next-steps"></a>后续步骤
本文介绍了如何使用 Python 通过最终用户身份验证进行 Azure Data Lake Storage Gen1 身份验证。 接下来，可以查看以下介绍如何使用 Python 在 Azure Data Lake Storage Gen1 中执行操作的文章。

* [使用 Python 在 Data Lake Storage Gen1 中进行的帐户管理操作](data-lake-store-get-started-python.md)
* [使用 Python 在 Data Lake Storage Gen1 中进行的数据操作](data-lake-store-data-operations-python.md)
