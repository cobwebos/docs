---
title: 使用 Python 创建 Azure 数据资源管理器群集和数据库
description: 了解如何使用 Python 创建 Azure 数据资源管理器群集和数据库。
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 8d43965e87ab57d9f0c79c6661a761b06ccb7073
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76902096"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-python"></a>使用 Python 创建 Azure 数据资源管理器群集和数据库

> [!div class="op_single_selector"]
> * [端口](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [ARM 模板](create-cluster-database-resource-manager.md)

Azure 数据资源管理器是一种快速、完全托管的数据分析服务，用于实时分析来自应用程序、网站、IoT 设备等的大量数据流。 若要使用 Azure 数据资源管理器，首先要创建群集，并在该群集中创建一个或多个数据库。 然后，将数据引入（加载）到数据库中，以便可以对其运行查询。 本文介绍如何使用 Python 创建群集和数据库。

## <a name="prerequisites"></a>Prerequisites

如果你没有 Azure 订阅，请在开始之前创建一个[免费的 azure 帐户](https://azure.microsoft.com/free/)。

## <a name="install-python-package"></a>安装 Python 包

若要安装 Azure 数据资源管理器的 Python 包（Kusto），请打开其路径中包含 Python 的命令提示符。 运行以下命令：

```
pip install azure-common
pip install azure-mgmt-kusto
```
## <a name="authentication"></a>身份验证
为了运行本文中的示例，我们需要 Azure AD 应用程序和可访问资源的服务主体。 选中 "[创建 Azure AD 应用程序](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)" 以创建免费的 Azure AD 应用程序，并在订阅范围内添加角色分配。 它还演示如何获取 `Directory (tenant) ID`、`Application ID`和 `Client Secret`。

## <a name="create-the-azure-data-explorer-cluster"></a>创建 Azure 数据资源管理器群集

1. 使用以下命令创建群集：

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

   |**设置** | **建议的值** | **字段说明**|
   |---|---|---|
   | cluster_name | *mykustocluster* | 群集所需的名称。|
   | sku_name | *Standard_D13_v2* | 将用于群集的 SKU。 |
   | 单层 | *标准* | SKU 层。 |
   | 功能 | *number* | 群集实例的数目。 |
   | resource_group_name | *testrg* | 将在其中创建群集的资源组名称。 |

    > [!NOTE]
    > **创建群集**是一个长时间运行的操作。 方法**create_or_update**返回 LROPoller 的实例，请参阅[LROPoller 类](/python/api/msrest/msrest.polling.lropoller?view=azure-python)以获取详细信息。

1. 运行以下命令，检查是否已成功创建群集：

    ```Python
    cluster_operations.get(resource_group_name = resource_group_name, cluster_name= clusterName, custom_headers=None, raw=False)
    ```

如果结果包含具有 `Succeeded` 值 `provisioningState`，则已成功创建群集。

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>在 Azure 数据资源管理器群集中创建数据库

1. 使用以下命令创建数据库：

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

   |**设置** | **建议的值** | **字段说明**|
   |---|---|---|
   | cluster_name | *mykustocluster* | 要在其中创建数据库的群集的名称。|
   | database_name | *mykustodatabase* | 数据库的名称。|
   | resource_group_name | *testrg* | 将在其中创建群集的资源组名称。 |
   | soft_delete_period | *3650天，0:00:00* | 数据将保持可用于查询的时间量。 |
   | hot_cache_period | *3650天，0:00:00* | 数据将在缓存中保留的时间。 |

1. 运行以下命令以查看创建的数据库：

    ```Python
    database_operations.get(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName)
    ```

现在，你有了一个群集和一个数据库。

## <a name="clean-up-resources"></a>清理资源

* 如果你打算追随我们的其他文章，请保留你创建的资源。
* 若要清理资源，请删除群集。 删除群集时，它还会删除其中的所有数据库。 使用以下命令删除群集：

    ```Python
    cluster_operations.delete(resource_group_name = resource_group_name, cluster_name = clusterName)
    ```

## <a name="next-steps"></a>后续步骤

* [使用 Azure 数据资源管理器 Python 库引入数据](python-ingest-data.md)
