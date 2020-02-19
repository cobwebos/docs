---
title: 使用 Python 创建 Azure 数据资源管理器的事件网格数据连接
description: 本文介绍如何使用 Python 创建 Azure 数据资源管理器的事件网格数据连接。
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 1439383598517f57bc77e718d4ded7f53941d3bb
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444190"
---
# <a name="create-an-event-grid-data-connection-for-azure-data-explorer-by-using-python"></a>使用 Python 创建 Azure 数据资源管理器的事件网格数据连接

> [!div class="op_single_selector"]
> * [门户](ingest-data-event-grid.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Azure Resource Manager 模板](data-connection-event-grid-resource-manager.md)

本文介绍如何使用 Python 创建 Azure 数据资源管理器的事件网格数据连接。 Azure 数据资源管理器是一项快速且高度可缩放的数据探索服务，适用于日志和遥测数据。 Azure 数据资源管理器提供从事件中心、IoT 中心和写入 blob 容器的 blob 的引入或数据加载。

## <a name="prerequisites"></a>必备条件

* 具有活动订阅的 Azure 帐户。 [免费创建一个](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

* [Python 3.4+](https://www.python.org/downloads/)。

* [一个群集和数据库](create-cluster-database-python.md)。

* [表和列映射](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)。

* [数据库和表策略](database-table-policies-csharp.md)（可选）。

* [具有事件网格订阅的存储帐户](ingest-data-event-grid.md#create-an-event-grid-subscription-in-your-storage-account)。

[!INCLUDE [data-explorer-data-connection-install-package-python](../../includes/data-explorer-data-connection-install-package-python.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-event-grid-data-connection"></a>添加事件网格数据连接

下面的示例演示如何以编程方式添加事件网格数据连接。 请参阅[在 Azure 中创建事件网格数据连接数据资源管理器](ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer)使用 Azure 门户添加事件网格数据连接。


```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import EventGridDataConnection
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
kusto_management_client = KustoManagementClient(credentials, subscription_id)

resource_group_name = "testrg"
#The cluster and database that are created as part of the Prerequisites
cluster_name = "mykustocluster"
database_name = "mykustodatabase"
data_connection_name = "myeventhubconnect"
#The event hub and storage account that are created as part of the Prerequisites
event_hub_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx"
storage_account_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Storage/storageAccounts/xxxxxx"
consumer_group = "$Default"
location = "Central US"
#The table and column mapping that are created as part of the Prerequisites
table_name = "StormEvents"
mapping_rule_name = "StormEvents_CSV_Mapping"
data_format = "csv"

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.data_connections.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, data_connection_name=data_connection_name,
                                            parameters=EventGridDataConnection(storage_account_resource_id=storage_account_resource_id, event_hub_resource_id=event_hub_resource_id, 
                                                                                consumer_group=consumer_group, table_name=table_name, location=location, mapping_rule_name=mapping_rule_name, data_format=data_format))
```
|**设置** | **建议的值** | **字段说明**|
|---|---|---|
| tenant_id | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxxxxxxx* | 租户 ID。 也称为目录 ID。|
| subscription_id | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxxxxxxx* | 用于创建资源的订阅 ID。|
| client_id | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxxxxxxx* | 可以访问租户中资源的应用程序的客户端 ID。|
| client_secret | *xxxxxxxxxxxxxx* | 可以访问租户中资源的应用程序的客户端机密。 |
| resource_group_name | *testrg* | 包含群集的资源组的名称。|
| cluster_name | mykustocluster | 群集的名称。|
| database_name | mykustodatabase | 群集中的目标数据库的名称。|
| data_connection_name | *myeventhubconnect* | 所需的数据连接名称。|
| table_name | *StormEvents* | 目标数据库中目标表的名称。|
| mapping_rule_name | *StormEvents_CSV_Mapping* | 与目标表相关的列映射的名称。|
| data_format | *.csv* | 消息的数据格式。|
| event_hub_resource_id | *资源 ID* | 事件中心的资源 ID，事件网格将配置为发送事件。 |
| storage_account_resource_id | *资源 ID* | 保存用于引入的数据的存储帐户的资源 ID。 |
| consumer_group | *$Default* | 事件中心的使用者组。|
| location | 美国中部 | 数据连接资源的位置。|

[!INCLUDE [data-explorer-data-connection-clean-resources-python](../../includes/data-explorer-data-connection-clean-resources-python.md)]