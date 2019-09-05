---
title: 连接到 Azure 媒体服务 v3 API-Python
description: 了解如何通过 Python 连接到媒体服务 v3 API。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/15/2019
ms.author: juliako
ms.openlocfilehash: 2ceebd88f4988f23bf9cd32bd827aaca67d70461
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307861"
---
# <a name="connect-to-media-services-v3-api---python"></a>连接到媒体服务 v3 API-Python

本文介绍如何使用服务主体登录方法连接到 Azure 媒体服务 v3 Python SDK。

## <a name="prerequisites"></a>先决条件

- 从[python.org](https://www.python.org/downloads/)下载 Python
- 请确保设置`PATH`环境变量
- [创建媒体服务帐户](create-account-cli-how-to.md)。 请确保记住资源组名称和媒体服务帐户名。
- 按照[访问 api](access-api-cli-how-to.md)主题中的步骤进行操作。 记录稍后步骤中所需的订阅 ID、应用程序 ID （客户端 ID）、身份验证密钥（机密）和租户 ID。

> [!IMPORTANT]
> 查看[命名约定](media-services-apis-overview.md#naming-conventions)。

## <a name="install-the-modules"></a>安装模块

若要通过 Python 使用 Azure 媒体服务，需要安装这些模块。

* `azure-mgmt-resource`模块，其中包括用于 Active Directory 的 Azure 模块。
* `azure-mgmt-media`模块，其中包含媒体服务实体。

打开一个命令行工具，然后使用以下命令安装这些模块。

```
pip3 install azure-mgmt-resource
pip3 install azure-mgmt-media==1.1.1
```

## <a name="connect-to-the-python-client"></a>连接到 Python 客户端

1. 创建`.py`扩展名为的文件
1. 在你最喜爱的编辑器中打开文件
1. 将下面的代码添加到文件。 此代码将导入所需的模块，并创建连接到媒体服务所需的 Active Directory 凭据对象。

      将变量的值设置为从[Access api](access-api-cli-how-to.md)获取的值

      ```
      import adal
      from msrestazure.azure_active_directory import AdalAuthentication
      from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD
      from azure.mgmt.media import AzureMediaServices
      from azure.mgmt.media.models import MediaService

      RESOURCE = 'https://management.core.windows.net/'
      # Tenant ID for your Azure Subscription
      TENANT_ID = '00000000-0000-0000-000000000000'
      # Your Service Principal App ID
      CLIENT = '00000000-0000-0000-000000000000'
      # Your Service Principal Password
      KEY = '00000000-0000-0000-000000000000'
      # Your Azure Subscription ID
      SUBSCRIPTION_ID = '00000000-0000-0000-000000000000'
      # Your Azure Media Service (AMS) Account Name
      ACCOUNT_NAME = 'amsv3account'
      # Your Resource Group Name
      RESOUCE_GROUP_NAME = 'AMSv3ResourceGroup'

      LOGIN_ENDPOINT = AZURE_PUBLIC_CLOUD.endpoints.active_directory
      RESOURCE = AZURE_PUBLIC_CLOUD.endpoints.active_directory_resource_id

      context = adal.AuthenticationContext(LOGIN_ENDPOINT + '/' + TENANT_ID)
      credentials = AdalAuthentication(
          context.acquire_token_with_client_credentials,
          RESOURCE,
          CLIENT,
          KEY
      )

      # The AMS Client
      # You can now use this object to perform different operations to your AMS account.
      client = AzureMediaServices(credentials, SUBSCRIPTION_ID)

      print("signed in")

      # Now that you are authenticated, you can manipulate the entities.
      # For example, list assets in your AMS account
      print (client.assets.list(RESOUCE_GROUP_NAME, ACCOUNT_NAME).get(0))
      ```

1. 运行文件

## <a name="next-steps"></a>后续步骤

- 使用 [Python SDK](https://aka.ms/ams-v3-python-sdk)。
- 查看媒体服务 [Python 参考](https://aka.ms/ams-v3-python-ref)文档。
