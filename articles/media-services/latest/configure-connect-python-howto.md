---
title: 连接到 Azure 媒体服务 v3 API-Python
description: 了解如何连接到媒体服务 v3 API 与 Python 配合使用。
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
ms.openlocfilehash: 971e36b600a2c6be516e39ce84ca5780a2f23bbd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60733090"
---
# <a name="connect-to-media-services-v3-api---python"></a>连接到媒体服务 v3 API-Python

本文介绍您如何连接到 Azure 媒体服务 v3 Python SDK 在方法中使用服务主体登录。

## <a name="prerequisites"></a>必备组件

- 下载 Python 的[python.org](https://www.python.org/downloads/)
- 请务必设置`PATH`环境变量
- [创建媒体服务帐户](create-account-cli-how-to.md)。 请务必记住的资源组名称和媒体服务帐户名称。
- 按照中的步骤[访问 Api](access-api-cli-how-to.md)主题。 在稍后的步骤中记录的订阅 ID、 应用程序 ID (客户端 ID)、 身份验证密钥 （机密） 和所需的租户 ID。

## <a name="install-the-modules"></a>安装模块

若要使用使用 Python 的 Azure 媒体服务，你需要安装这些模块。

* `azure-mgmt-resource`模块，其中包括 Active Directory 的 Azure 模块。
* `azure-mgmt-media`模块，其中包括媒体服务实体。

打开一个命令行工具并使用以下命令安装的模块。

```
pip3 install azure-mgmt-resource
pip3 install azure-mgmt-media==1.1.1
```

## <a name="connect-to-the-python-client"></a>连接到 Python 客户端

1. 创建具有文件`.py`扩展
1. 在你喜爱的编辑器中打开文件
1. 将添加到此文件的代码。 代码导入所需的模块，并创建连接到媒体服务所需的 Active Directory 凭据对象。

      将变量的值设置为从获取的值[访问 Api](access-api-cli-how-to.md)

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
