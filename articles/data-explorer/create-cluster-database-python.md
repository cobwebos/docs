---
title: 快速入门：使用 Python 创建 Azure 数据资源管理器群集和数据库
description: 了解如何使用 Python 创建 Azure 数据资源管理器群集和数据库
services: data-explorer
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: quickstart
ms.date: 03/17/2019
ms.openlocfilehash: 4f87c5996ea323c26c32c1680ba6f627bf8f95c2
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58287353"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-python"></a>使用 Python 创建 Azure 数据资源管理器群集和数据库

> [!div class="op_single_selector"]
> * [门户](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  

该快速入门介绍了如何使用 Python 创建 Azure 数据资源管理器群集和数据库。

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，需要一个 Azure 订阅。 如果没有订阅，请在开始之前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="install-python-package"></a>安装 Python 包

要为 Azure 数据资源管理器 (Kusto) 安装 Python 包，请打开其路径中包含 Python 的命令提示符，然后运行以下命令：

```
pip install azure-mgmt-kusto
```

## <a name="create-the-azure-data-explorer-cluster"></a>创建 Azure 数据资源管理器群集

1. 请使用以下命令创建群集：

    

   |**设置** | **建议的值** | **字段说明**|
   |---|---|---|
   | cluster_name | mykustocluster | 所需的群集名称。|
   | sku | *D13_v2* | 将用于群集的 SKU。 |
   | resource_group_name | *testrg* | 将在其中创建群集的资源组名称。 |

    可以使用其他可选参数，例如群集的容量。
    
    对凭据设置“credentials”（有关详细信息，请参阅 https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python。）

2. 运行以下命令，检查群集是否已成功创建：

    ```Python
    cluster_operations.get(resource_group_name = resource_group_name, cluster_name= clusterName, custom_headers=None, raw=False)
    ```

如果结果包含带 `Succeeded` 值的 `provisioningState`，则表示已成功创建群集。

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>在 Azure 数据资源管理器群集中创建数据库

1. 请使用以下命令创建数据库：

    ```Python
    from azure.mgmt.kusto.models import Database
    from datetime import timedelta
    
    softDeletePeriod = timedelta(days=3650)
    hotCachePeriod = timedelta(days=3650)
    databaseName="mykustodatabase"
    
    database_operations = kusto_management_client.databases 
    _database = Database(location=location,
                        soft_delete_period=softDeletePeriod,
                        hot_cache_period=hotCachePeriod)
    
    database_operations.create_or_update(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName, parameters = _database)
    ```

   |**设置** | **建议的值** | **字段说明**|
   |---|---|---|
   | cluster_name | mykustocluster | 将在其中创建数据库的群集的名称。|
   | database_name | mykustodatabase | 数据库名称。|
   | resource_group_name | *testrg* | 将在其中创建群集的资源组名称。 |
   | soft_delete_period | 3650 天，0:00:00 | 供查询使用的数据的保留时间。 |
   | hot_cache_period | 3650 天，0:00:00 | 数据将在缓存中保留的时间。 |

2. 若要查看已创建的数据库，请运行以下命令：

    ```Python
    database_operations.get(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName)
    ```

现在，你有了一个群集和一个数据库。

## <a name="clean-up-resources"></a>清理资源

* 如果计划学习其他快速入门和教程，请保留创建的资源。
* 若要清理资源，请删除群集。 删除群集时，也会删除其中的所有数据库。 使用以下命令删除群集：

    ```Python
    cluster_operations.delete(resource_group_name = resource_group_name, cluster_name = clusterName)
    ```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [快速入门：使用 Azure 数据资源管理器 Python 库引入数据](python-ingest-data.md)
