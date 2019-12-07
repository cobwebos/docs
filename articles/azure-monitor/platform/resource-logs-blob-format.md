---
title: 准备 Azure Monitor 资源日志的格式更改
description: 在2018年11月1日，迁移到的 Azure 资源日志使用追加 blob。
author: johnkemnetz
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 09a5d95ead9f294d54a7491734b11c7247353444
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894497"
---
# <a name="prepare-for-format-change-to-azure-monitor-platform-logs-archived-to-a-storage-account"></a>准备 Azure Monitor 平台日志存档到存储帐户的格式更改

> [!WARNING]
> 如果使用[日志配置文件](activity-log-export.md)将[Azure 资源日志或指标](resource-logs-collect-storage.md)发送到存储帐户，则存储帐户中的数据格式将更改为2018年11月1日的 JSON 行。 以下说明介绍了影响以及如何更新工具以处理新格式。
>

## <a name="what-changed"></a>更改内容

Azure Monitor 提供了一项功能，使你能够将资源日志和活动日志发送到 Azure 存储帐户、事件中心命名空间或 Azure Monitor 的 Log Analytics 工作区中。 若要解决系统性能问题，请在**2018 年11月1日上午12:00，** 发送到 blob 存储的日志数据的格式已更改。 如果工具从 blob 存储中读取数据，则需要更新工具以了解新的数据格式。

* 在星期四，12:00 2018 年11月1日，UTC 格式已更改为[JSON 行](http://jsonlines.org/)。 这意味着每个记录将由换行符分隔，JSON 记录之间没有外部记录数组和逗号。
* 同时为所有订阅中的所有诊断设置更改 blob 格式。 为11月1日发出的第一个 PT1H.JSON 文件使用此新格式。 Blob 和容器名称保持不变。
* 将诊断设置设置为介于11月1日前之前，会一直在11月1日前发出当前格式的数据。
* 此更改一次在所有公有云区域发生。 此更改不会在世纪互联、Azure 德国或 Azure 政府云运营的 Microsoft Azure 中发生。
* 此更改会影响以下数据类型：
  * [Azure 资源日志](archive-diagnostic-logs.md)（[请参阅此处的资源列表](diagnostic-logs-schema.md)）
  * [由诊断设置导出的 Azure 资源指标](diagnostic-settings.md)
  * [由日志配置文件导出的 Azure 活动日志数据](activity-log-collect.md)
* 此更改不会影响：
  * 网络流日志
  * 尚未通过 Azure Monitor 提供 Azure 服务日志（例如，Azure App Service 资源日志、存储分析日志）
  * 将 Azure 资源日志和活动日志路由到其他目标（事件中心、Log Analytics）

### <a name="how-to-see-if-you-are-impacted"></a>如何查看是否受到影响

只有在以下情况下，才会受到此更改的影响：
1. 使用诊断设置将日志数据发送到 Azure 存储帐户，并
2. 拥有依赖于存储中这些日志的 JSON 结构的工具。
 
若要确定是否有将数据发送到 Azure 存储帐户的诊断设置，可以导航到门户的 "**监视**" 部分，单击 "**诊断设置**"，并识别将**诊断状态**设置为 "**已启用**" 的所有资源：

![“Azure Monitor 诊断设置”边栏选项卡](media/diagnostic-logs-append-blobs/portal-diag-settings.png)

如果“诊断状态”设置为已启用，则该资源上具有活动诊断设置。 单击资源以查看是否有诊断设置将数据发送到存储帐户：

![已启用存储帐户](media/diagnostic-logs-append-blobs/portal-storage-enabled.png)

如果确实有资源使用这些资源诊断设置将数据发送到存储帐户，则此更改将影响该存储帐户中的数据格式。 除非拥有运行这些存储帐户的自定义工具，否则格式更改将不会产生影响。

### <a name="details-of-the-format-change"></a>格式更改的详细信息

Azure blob 存储中 PT1H.json 文件的当前格式使用 JSON 数组记录。 以下是 KeyVault 日志文件的示例：

```json
{
    "records": [
        {
            "time": "2016-01-05T01:32:01.2691226Z",
            "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
            "operationName": "VaultGet",
            "operationVersion": "2015-06-01",
            "category": "AuditEvent",
            "resultType": "Success",
            "resultSignature": "OK",
            "resultDescription": "",
            "durationMs": "78",
            "callerIpAddress": "104.40.82.76",
            "correlationId": "",
            "identity": {
                "claim": {
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com",
                    "appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"
                }
            },
            "properties": {
                "clientInfo": "azure-resource-manager/2.0",
                "requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01",
                "id": "https://contosokeyvault.vault.azure.net/",
                "httpStatusCode": 200
            }
        },
        {
            "time": "2016-01-05T01:33:56.5264523Z",
            "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
            "operationName": "VaultGet",
            "operationVersion": "2015-06-01",
            "category": "AuditEvent",
            "resultType": "Success",
            "resultSignature": "OK",
            "resultDescription": "",
            "durationMs": "83",
            "callerIpAddress": "104.40.82.76",
            "correlationId": "",
            "identity": {
                "claim": {
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com",
                    "appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"
                }
            },
            "properties": {
                "clientInfo": "azure-resource-manager/2.0",
                "requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01",
                "id": "https://contosokeyvault.vault.azure.net/",
                "httpStatusCode": 200
            }
        }
    ]
}
```

新格式使用 [JSON lines](http://jsonlines.org/)，其中每个事件都是一行，换行符表示新事件。 以下是更改后以上示例在 PT1H.json 文件中的外观：

```json
{"time": "2016-01-05T01:32:01.2691226Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "78","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
{"time": "2016-01-05T01:33:56.5264523Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "83","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
```

通过此新格式，Azure Monitor 能够使用[追加 blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs) 来推送日志文件，这对于连续追加新事件数据更加高效。

## <a name="how-to-update"></a>更新方法

如果具有引入这些日志文件以进行进一步处理的自定义工具，则只需进行更新即可。 如果正在使用外部日志分析或 SIEM 工具，则建议[改为使用事件中心来引入此数据](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)。 在处理许多服务中的日志以及为特定日志中的位置添加书签方面，事件中心集成更加容易。

应更新自定义工具以处理当前格式和上述 JSON Lines 格式。 这将确保当数据开始以新格式显示时，工具不会中断。

## <a name="next-steps"></a>后续步骤

* 了解如何[将资源资源日志存档到存储帐户](./../../azure-monitor/platform/archive-diagnostic-logs.md)
* 了解如何[将活动日志数据存档到存储帐户](./../../azure-monitor/platform/archive-activity-log.md)

