---
title: 客户拥有的用于日志引入的存储帐户
description: 使用自己的存储帐户将日志数据引入到 Azure Monitor 中的 Log Analytics 工作区中。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/20/2020
ms.openlocfilehash: 7213cb10936fc1c2117b2c5c3fc32a6bfea02d30
ms.sourcegitcommit: fc0431755effdc4da9a716f908298e34530b1238
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/24/2020
ms.locfileid: "83816582"
---
# <a name="customer-owned-storage-accounts-for-log-ingestion-in-azure-monitor"></a>Azure Monitor 上客户拥有的用于日志引入的存储帐户

Azure Monitor 在[自定义日志](data-sources-custom-logs.md)和 [Azure 日志](azure-storage-iis-table.md)等数据类型的数据引入过程中使用存储帐户。 在数据引入过程中，先将日志发送到存储帐户，然后再引入到 Log Analytics 或 Application Insights。 如果希望在数据引入过程中对数据进行控制，可以使用自己的存储帐户，不要使用服务托管存储。 使用自己的存储帐户，可以控制数据引入期间的日志访问、日志内容、日志加密和日志保留期。 我们将其称为自带存储或 BYOS。 

需要 BYOS 的一种方案是通过专用链接进行网络隔离。 使用 VNet 时，通常需要进行网络隔离，并限制公共 internet 访问。 在这种情况下，访问日志数据引入的 Azure Monitor 服务存储可能会被完全阻止，或被视为不好的做法。 相反，应通过 VNet 中客户所有的存储帐户引入日志，或从该帐户轻松访问日志。

另一种方案是通过客户托管密钥 (CMK) 加密日志。 客户可以通过使用存储日志的群集上的 CMK 加密日志数据。 也可以在数据引入过程中使用同一密钥加密日志。

## <a name="data-types-supported"></a>支持的数据类型

从存储帐户中引入的数据类型包括以下各项。 有关数据引入类型的详细信息，请参阅[从 Azure 诊断扩展到 Azure Monitor 日志的数据收集](azure-storage-iis-table.md)。

| 类型 | 表格信息 |
|:-----|:------------------|
| IIS 日志 | Blob:wad-iis-logfiles|
|Windows 事件日志 | 表：WADWindowsEventLogsTable |
| Syslog | 表：LinuxsyslogVer2v0 |
| Windows ETW 日志 | 表：WADETWEventTable|
| Service Fabric | 表：WADServiceFabricSystemEventTable <br/> WADServiceFabricReliableActorEventTable<br/> WADServiceFabricReliableServicEventTable |
| 自定义日志 | 不适用 |
| Azure 安全中心 Watson 转储文件 | 不适用|  

## <a name="storage-account-requirements"></a>存储帐户要求 
存储帐户必须满足以下要求：

- 可以访问写入日志进行存储的 VNet 中的资源。
- 必须与其所链接到的工作区位于同一区域。
- 选择“允许受信任的 MS 服务访问此存储帐户”，明确允许 Log Analytics 从存储帐户中读取日志。

## <a name="process-to-configure-customer-owned-storage"></a>配置客户所有的存储的过程
使用自己的存储帐户进行数据引入的基本过程如下所示：

1. 创建存储帐户或选择现有帐户。
2. 将存储帐户链接到 Log Analytics 工作区。
3. 通过评审其负载和保留期管理存储，确保其按预期运行。

创建和删除链接的唯一方法是通过 REST API。 以下各章节提供了每个进程所需的特定 API 请求详细信息。

## <a name="api-request-values"></a>API 请求值

#### <a name="datasourcetype"></a>dataSourceType 

- AzureWatson - 将此值用于 Azure 安全中心 Azure Watson 转储文件。
- CustomLogs - 将此值用于以下数据类型：
  - 自定义日志
  - IIS 日志
  - 事件 (Windows)
  - Syslog (Linux)
  - ETW 日志
  - Service Fabric 事件
  - 评估数据  

#### <a name="storage_account_resource_id"></a>storage_account_resource_id
此值使用以下结构：

```
subscriptions/{subscriptionId}/resourcesGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName1}
```



## <a name="get-current-links"></a>获取当前链接

### <a name="get-linked-storage-accounts-for-a-specific-data-source-type"></a>获取特定数据源类型的链接存储帐户

#### <a name="api-request"></a>API 请求

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts/{dataSourceType}?api-version=2019-08-01-preview  
```

#### <a name="response"></a>响应 

```json
{
    "properties":
    {
        "dataSourceType": "CustomLogs",
        "storageAccountIds  ": 
        [  
            "<storage_account_resource_id_1>",
            "<storage_account_resource_id_2>"
        ],
    },
    "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/CustomLogs",
    "name": "CustomLogs",
    "type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
}
```

### <a name="get-all-linked-storage-accounts"></a>获取所有链接存储帐户

#### <a name="api-request"></a>API 请求

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts?api-version=2019-08-01-preview  
```

#### <a name="response"></a>响应

```json
{
    [
        {
            "properties":
            {
                "dataSourceType": "CustomLogs",
                "storageAccountIds  ": 
                [  
                    "<storage_account_resource_id_1>",
                    "<storage_account_resource_id_2>"
                ],
            },
            "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/CustomLogs",
            "name": "CustomLogs",
            "type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
        },
        {
            "properties":
            {
                "dataSourceType": " AzureWatson "
                "storageAccountIds  ": 
                [  
                    "<storage_account_resource_id_3>",
                    "<storage_account_resource_id_4>"
                ],
            },
            "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/AzureWatson",
            "name": "AzureWatson",
            "type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
        }
    ]
}
```

## <a name="create-or-modify-a-link"></a>创建或修改链接

将存储帐户链接到工作区后，Log Analytics 将开始使用该存储帐户，而不是使用服务所拥有的存储帐户。 可以同时注册存储帐户列表，多个工作区可以使用同一存储帐户。

如果工作区既处理 VNet 资源，也处理 VNet 外的资源，则应确保其不会拒绝来自 Internet 的流量。 存储空间应与工作区具有相同的设置，并可供 VNet 之外的资源使用。 

### <a name="api-request"></a>API 请求

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts/{dataSourceType}?api-version=2019-08-01-preview  
```

### <a name="payload"></a>有效负载

```json
{
    "properties":
    {
        "storageAccountIds  " : 
        [  
            "<storage_account_resource_id_1>",
            "<storage_account_resource_id_2>"
        ],
    }
}
```

### <a name="response"></a>响应

```json
{
    "properties":
    {
        "dataSourceType": "CustomLogs"
        "storageAccountIds  ": 
        [  
            "<storage_account_resource_id_1>",
            "<storage_account_resource_id_2>"
        ],
    },
"id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/CustomLogs",
"name": "CustomLogs",
"type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
}
```


## <a name="unlink-a-storage-account"></a>取消存储帐户链接
如果决定停止使用存储帐户进行数据引入，或替换使用的工作区，则应取消存储帐户与工作区之间的链接。

取消所有存储帐户与工作区之间的链接意味着引入数据将尝试依赖服务托管存储帐户。 如果代理在具有 Internet 访问权限的 VNet 上运行，预计引入数据会失败。 工作区必须具有可从受监视资源访问的链接存储帐户。

在删除存储帐户之前，应确保其包含的所有数据都已引入到工作区中。 作为预防措施，请在链接到备用存储后保持存储帐户可用。 引入完所有内容后才可将其删除，可看到新数据已写入新连接的存储帐户。


### <a name="api-request"></a>API 请求
```
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts/{dataSourceType}?api-version=2019-08-01-preview  
```

## <a name="replace-a-storage-account"></a>替换存储帐户

若要替换用于数据引入的存储帐户，请先为新存储帐户创建链接。 日志记录代理将获得更新配置，并开始将数据发送到新的存储。

接下来取消与旧存储帐户的链接，以便代理停止写入已删除的帐户。 数据引入过程将一直读取此帐户中的数据，直到全部引入。 在所有日志引入完之前，请不要删除存储帐户。

代理配置将在几分钟后刷新，并将切换到新存储。

## <a name="manage-storage-account"></a>管理存储帐户

### <a name="load"></a>加载

开始限制请求之前，存储帐户可以处理某些读取和写入请求。 限制请求会延长引入日志所花费的时间，并且可能会导致数据丢失。 如果存储已超载，请注册额外的存储帐户，并在帐户之间分布负载。 

### <a name="related-charges"></a>相关费用

存储帐户按存储数据量、存储类型和冗余类型收费。 有关详细信息，请参阅[块 blob 定价](https://azure.microsoft.com/pricing/details/storage/blobs/)和[表存储定价](https://azure.microsoft.com/pricing/details/storage/tables/)。

如果工作区已注册的存储帐户位于另一个区域，则根据[带宽定价详细信息](https://azure.microsoft.com/pricing/details/bandwidth/)对流出量进行收费。



## <a name="next-steps"></a>后续步骤

- 有关设置专用链接的详细信息，请参阅 [使用 Azure 专用链接将网络安全连接到 Azure Monitor} (private-link-security.md)
