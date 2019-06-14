---
title: 服务到服务身份验证：通过 Azure Active Directory 将 Python 与 Azure 数据湖存储 Gen1 |Microsoft Docs
description: 了解如何通过 Python 使用 Azure Active Directory 进行 Azure Data Lake Storage Gen1 服务到服务身份验证
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 84b7fac10374c1c8f23d17ad775d522b4cb261e8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60195719"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-python"></a>使用 Python 进行 Azure Data Lake Storage Gen1 服务到服务身份验证
> [!div class="op_single_selector"]
> * [使用 Java](data-lake-store-service-to-service-authenticate-java.md)
> * [使用 .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [使用 Python](data-lake-store-service-to-service-authenticate-python.md)
> * [使用 REST API](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
>  

本文介绍如何使用 Python SDK 执行 Azure Data Lake Storage Gen1 服务到服务身份验证。 若要了解使用 Python 的 Data Lake Storage Gen1 最终用户身份验证，请参阅[使用 Python 通过 Data Lake Storage Gen1 进行最终用户身份验证](data-lake-store-end-user-authenticate-python.md)。


## <a name="prerequisites"></a>必备组件

* **Python**。 可以从[此处](https://www.python.org/downloads/)下载 Python。 本文使用的是 Python 3.6.2。

* **Azure 订阅**。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

* **创建 Azure Active Directory“Web”应用程序**。 必须已完成[使用 Azure Active Directory 进行 Data Lake Storage Gen1 服务到服务身份验证](data-lake-store-service-to-service-authenticate-using-active-directory.md)中的步骤。

## <a name="install-the-modules"></a>安装模块

若要通过 Python 使用 Data Lake Storage Gen1，需要安装三个模块。

* `azure-mgmt-resource` 模块，包括用于 Active Directory 的 Azure 模块，等等。
* `azure-mgmt-datalake-store` 模块，包括 Data Lake Storage Gen1 帐户管理操作。 有关此模块的详细信息，请参阅 [Azure Data Lake Storage Gen1 管理模块参考](https://docs.microsoft.com/python/api/azure.mgmt.datalake.store?view=azure-python)。
* `azure-datalake-store` 模块，其中包括 Data Lake Storage Gen1 文件系统操作。 有关此模块的详细信息，请参阅 [azure-datalake-store 文件系统模块参考](https://azure-datalake-store.readthedocs.io/en/latest/)。

使用以下命令安装这些模块。

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>创建新的 Python 应用程序

1. 在所选的 IDE 中创建新的 Python 应用程序，例如，**mysample.py**。

2. 添加以下代码片段以导入所需的模块

    ```
    ## Use this for Azure AD authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Data Lake Storage Gen1 filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    import adal
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, getpass, pprint, uuid, time
    ```

3. 将更改保存到 mysample.py。

## <a name="service-to-service-authentication-with-client-secret-for-account-management"></a>为帐户管理使用结合客户端机密的服务到服务身份验证

使用此代码片段通过 Azure AD 对 Data Lake Storage Gen1 上的帐户管理操作（例如，创建 Data Lake Storage Gen1 帐户、删除 Data Lake Storage Gen1 帐户，等等）进行身份验证。可以使用以下代码片段通过现有 Azure AD“Web 应用”应用程序的应用程序 / 服务主体的客户端机密以非交互方式对应用程序进行身份验证。

    authority_host_uri = 'https://login.microsoftonline.com'
    tenant = '<TENANT>'
    authority_uri = authority_host_uri + '/' + tenant
    RESOURCE = 'https://management.core.windows.net/'
    client_id = '<CLIENT_ID>'
    client_secret = '<CLIENT_SECRET>'
    
    context = adal.AuthenticationContext(authority_uri, api_version=None)
    mgmt_token = context.acquire_token_with_client_credentials(RESOURCE, client_id, client_secret)
    armCreds = AADTokenCredentials(mgmt_token, client_id, resource=RESOURCE)

## <a name="service-to-service-authentication-with-client-secret-for-filesystem-operations"></a>对文件系统操作使用结合客户端密码的服务到服务身份验证

使用以下代码片段通过 Azure AD 对 Data Lake Storage Gen1 上的文件系统操作（例如，创建文件夹、上传文件，等等）进行身份验证。可以使用以下代码片段通过应用程序/服务主体的客户端机密对应用程序进行非交互式身份验证。 请对现有的 Azure AD“Web 应用”应用程序使用这种身份验证。

    tenant = '<TENANT>'
    RESOURCE = 'https://datalake.azure.net/'
    client_id = '<CLIENT_ID>'
    client_secret = '<CLIENT_SECRET>'
    
    adlCreds = lib.auth(tenant_id = tenant,
                    client_secret = client_secret,
                    client_id = client_id,
                    resource = RESOURCE)

<!-- ## Service-to-service authentication with certificate for account management

Use this snippet to authenticate with Azure AD for account management operations on Data Lake Storage Gen1 such as create a Data Lake Storage Gen1 account, delete a Data Lake Storage Gen1 account, etc. The following snippet can be used to authenticate your application non-interactively, using the certificate of an existing Azure AD "Web App" application. For instructions on how to create an Azure AD application, see [Create service principal with certificates](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

    authority_host_uri = 'https://login.microsoftonline.com'
    tenant = '<TENANT>'
    authority_uri = authority_host_uri + '/' + tenant
    resource_uri = 'https://management.core.windows.net/'
    client_id = '<CLIENT_ID>'
    client_cert = '<CLIENT_CERT>'
    client_cert_thumbprint = '<CLIENT_CERT_THUMBPRINT>'

    context = adal.AuthenticationContext(authority_uri, api_version=None)
    mgmt_token = context.acquire_token_with_client_certificate(resource_uri, client_id, client_cert, client_cert_thumbprint)
    credentials = AADTokenCredentials(mgmt_token, client_id) -->

## <a name="next-steps"></a>后续步骤
本文介绍了如何通过 Python 使用服务到服务身份验证进行 Data Lake Storage Gen1 身份验证。 接下来，可以查看以下介绍如何使用 Python 在 Data Lake Storage Gen1 中执行操作的文章。

* [使用 Python 在 Data Lake Storage Gen1 中进行的帐户管理操作](data-lake-store-get-started-python.md)
* [使用 Python 在 Data Lake Storage Gen1 中进行的数据操作](data-lake-store-data-operations-python.md)


