---
title: 使用 Python 从 Azure Databricks 连接到 Azure 数据资源管理器
description: 本主题介绍了如何使用两个身份验证方法中的一个，通过 Azure Databricks 中的 Python 库访问 Azure 数据资源管理器中的数据。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/27/2018
ms.openlocfilehash: 7e91aa0707b1ae8ac4b9b6ddd9ee7142a04a0f37
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756922"
---
# <a name="connect-to-azure-data-explorer-from-azure-databricks-by-using-python"></a>使用 Python 从 Azure Databricks 连接到 Azure 数据资源管理器

[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks) 是基于 Apache Spark 的分析平台，针对 Microsoft Azure 平台进行了优化。 本文介绍了如何通过 Azure Databricks 中的 Python 库访问 Azure 数据资源管理器中的数据。 可以使用 Azure 数据资源管理器通过多种方法进行身份验证，其中包括设备登录和 Azure Active Directory (Azure AD) 应用。

## <a name="prerequisites"></a>必备组件

- [创建 Azure 数据资源管理器群集和数据库](/azure/data-explorer/create-cluster-database-portal)。
- [创建 Azure Databricks 工作区](/azure/azure-databricks/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace)。 在“Azure Databricks 服务”下的“定价层”下拉列表中，选择“高级”。 选择此项可以使用 Azure Databricks 机密来存储凭据并在笔记本和作业中引用它们。

- 在 Azure Databricks 中[创建群集](https://docs.azuredatabricks.net/user-guide/clusters/create.html)，其规格如下（运行示例笔记本所需的最低设置）：

   ![有关创建群集的规范](media/connect-from-databricks/databricks-create-cluster.png)

## <a name="install-the-python-library-on-your-azure-databricks-cluster"></a>在 Azure Databricks 群集上安装 Python 库

若要在 Azure Databricks 群集上安装 [Python 库](/azure/kusto/api/python/kusto-python-client-library)，请执行以下操作：

1. 转到 Azure Databricks 工作区并[创建一个库](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)。
2. [上传 Python PyPI 包或 Python Egg](https://docs.azuredatabricks.net/user-guide/libraries.html#upload-a-python-pypi-package-or-python-egg)。
   - 将库上传、安装和附加到 Databricks 群集。
   - 输入 PyPi 名称：**azure-kusto-data**。

## <a name="connect-to-azure-data-explorer-by-using-a-device-login"></a>使用设备登录名连接到 Azure 数据资源管理器

使用 [Query-ADX-device-login](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-device-login.ipynb) 笔记本[导入笔记本](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook)。 然后可以使用你的凭据连接到 Azure 数据资源管理器。

## <a name="connect-to-adx-by-using-an-azure-ad-app"></a>使用 Azure AD 应用连接到 ADX

1. 通过[预配 Azure AD 应用程序](/azure/kusto/management/access-control/how-to-provision-aad-app)创建 Azure AD 应用。
1. 授予对 Azure 数据资源管理器数据库中的 Azure AD 应用的访问权限，如下所示：

    ```kusto
    .set database <DB Name> users ('aadapp=<AAD App ID>;<AAD Tenant ID>') 'AAD App to connect Spark to ADX
    ```
    |   |   |
    | - | - |
    | ```DB Name``` | 数据库名称 |
    | ```AAD App ID``` | Azure AD 应用 ID |
    | ```AAD Tenant ID``` | Azure AD 租户 ID |

### <a name="find-your-azure-ad-tenant-id"></a>查找 Azure AD 租户 ID

Azure 数据资源管理器使用 Azure AD 租户 ID，以对应用程序进行身份验证。 若要查找租户 ID，请使用以下 URL。 将 *YourDomain* 替换为你的域。

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

例如，如果域名为 contoso.com，则该 URL 将是：[https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/)。 选择此 URL 以查看结果。 第一行如下所示： 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

租户 ID 为 `6babcaad-604b-40ac-a9d7-9fd97c0b779f`。 

### <a name="store-and-secure-your-azure-ad-app-id-and-key"></a>存储 Azure AD 应用 ID 和密钥并确保其安全 

使用 Azure Databricks [机密](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets)存储 Azure AD 应用 ID 和密钥并确保其安全，如下所示：
1. [设置 CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-the-cli)。
1. [安装 CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli)。 
1. [设置身份验证](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-authentication)。
1. 使用以下示例命令来配置[机密](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets)：

    ```databricks secrets create-scope --scope adx```

    ```databricks secrets put --scope adx --key myaadappid```

    ```databricks secrets put --scope adx --key myaadappkey```

    ```databricks secrets list --scope adx```

### <a name="import-a-notebook"></a>导入笔记本
通过使用 [Query-ADX-AAD-App](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-AAD-App.ipynb) 笔记本连接到 Azure 数据资源管理器来[导入笔记本](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook)。 将占位符值更新为群集名称、数据库名称和 Azure AD 租户 ID。
