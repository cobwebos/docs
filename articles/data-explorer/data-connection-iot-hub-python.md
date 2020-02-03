---
title: 使用 Python 创建 Azure 数据资源管理器的 IoT 中心数据连接
description: 本文介绍如何使用 Python 创建适用于 Azure 数据资源管理器的 IoT 中心数据连接。
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 86e966cc3bf98e63edbe90d7649242dcb1ccdf42
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964373"
---
# <a name="create-an-iot-hub-data-connection-for-azure-data-explorer-by-using-python-preview"></a>使用 Python 创建适用于 Azure 数据资源管理器的 IoT 中心数据连接（预览版）

> [!div class="op_single_selector"]
> * [门户](ingest-data-iot-hub.md)
> * [C#](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)
> * [Azure Resource Manager 模板](data-connection-iot-hub-resource-manager.md)

Azure 数据资源管理器是一项快速且高度可缩放的数据探索服务，适用于日志和遥测数据。 Azure 数据资源管理器提供从事件中心、IoT 中心和写入 blob 容器的 blob 的引入（数据加载）。 本文介绍如何使用 Python 创建适用于 Azure 数据资源管理器的 IoT 中心数据连接。

## <a name="prerequisites"></a>必备组件

* 如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。
* 创建[群集和数据库](create-cluster-database-python.md)
* 创建[表和列映射](python-ingest-data.md#create-a-table-on-your-cluster)
* 设置[数据库和表策略](database-table-policies-python.md)（可选）
* 创建[配置了共享访问策略的 IoT 中心](ingest-data-iot-hub.md#create-an-iot-hub)。

[!INCLUDE [data-explorer-data-connection-install-package-python](../../includes/data-explorer-data-connection-install-package-python.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-iot-hub-data-connection"></a>添加 IoT 中心数据连接 

下面的示例演示如何以编程方式添加 IoT 中心数据连接。 请参阅使用 Azure 门户将[Azure 数据资源管理器表连接到 Iot 中心](ingest-data-iot-hub.md#connect-azure-data-explorer-table-to-iot-hub)，以便添加 iot 中心数据连接。

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import IotHubDataConnection
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
#The IoT hub that is created as part of the Prerequisites
iot_hub_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Devices/IotHubs/xxxxxx";
shared_access_policy_name = "iothubforread"
consumer_group = "$Default"
location = "Central US"
#The table and column mapping that are created as part of the Prerequisites
table_name = "StormEvents"
mapping_rule_name = "StormEvents_CSV_Mapping"
data_format = "csv"

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.data_connections.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, data_connection_name=data_connection_name,
                                            parameters=IotHubDataConnection(iot_hub_resource_id=iot_hub_resource_id, shared_access_policy_name=shared_access_policy_name, 
                                                                                consumer_group=consumer_group, table_name=table_name, location=location, mapping_rule_name=mapping_rule_name, data_format=data_format))
```

|**设置** | **建议的值** | **字段说明**|
|---|---|---|
| tenant_id | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxxxxxxx* | 租户 ID。 也称为目录 ID。|
| subscriptionId | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxxxxxxx* | 用于创建资源的订阅 ID。|
| client_id | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxxxxxxx* | 可以访问租户中资源的应用程序的客户端 ID。|
| client_secret | *xxxxxxxxxxxxxx* | 可以访问租户中资源的应用程序的客户端机密。 |
| resource_group_name | *testrg* | 包含群集的资源组的名称。|
| cluster_name | mykustocluster | 群集的名称。|
| database_name | mykustodatabase | 群集中的目标数据库的名称。|
| data_connection_name | *myeventhubconnect* | 所需的数据连接名称。|
| table_name | *StormEvents* | 目标数据库中目标表的名称。|
| mapping_rule_name | *StormEvents_CSV_Mapping* | 与目标表相关的列映射的名称。|
| data_format | *.csv* | 消息的数据格式。|
| iot_hub_resource_id | *资源 ID* | 包含用于引入的数据的 IoT 中心的资源 ID。|
| shared_access_policy_name | *iothubforread* | 共享访问策略的名称，该策略定义设备和服务连接到 IoT 中心的权限。 |
| consumer_group | *$Default* | 事件中心的使用者组。|
| location | 美国中部 | 数据连接资源的位置。|

[!INCLUDE [data-explorer-data-connection-clean-resources-python](../../includes/data-explorer-data-connection-clean-resources-python.md)]