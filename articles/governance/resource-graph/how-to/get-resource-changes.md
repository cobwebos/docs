---
title: 获取资源更改
description: 了解如何查找资源更改时间以及获取已更改属性的列表。
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/10/2019
ms.topic: conceptual
ms.service: resource-graph
ms.openlocfilehash: 2027f56d44be14895a40550d78a79d9e9dda9d97
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2019
ms.locfileid: "71980291"
---
# <a name="get-resource-changes"></a>获取资源更改

在日常使用、重新配置甚至是重新部署的过程中，资源都会发生更改。
更改可能是个人或者自动化过程做出的。 大部分更改是依设计做出的，但有时并非如此。 Azure Resource Graph 提供过去 14 天的更改历史记录，可让你：

- 查找在 Azure 资源管理器属性中检测到更改的时间。
- 查看在发生该更改事件期间更改了哪些属性。

对于以下示例场景，更改检测和详细信息很有作用：

- 在事件管理期间了解潜在相关的更改。 查询特定时段内的更改事件，以及评估更改详细信息。
- 使配置管理数据库（简称为 CMDB）保持最新。 无需按计划的频率刷新所有资源及其完整的属性集，只获取更改的内容。
- 了解当某个资源更改了合规状态时可能已更改的其他属性。 评估这些附加属性可以洞察可能需要通过 Azure Policy 定义进行管理的其他属性。

本文介绍如何通过 Resource Graph 的 SDK 收集此信息。 若要在 Azure 门户中查看此信息，请参阅 Azure 策略的[更改历史记录](../../policy/how-to/determine-non-compliance.md#change-history-preview)或 Azure 活动日志[更改历史记录](../../../azure-monitor/platform/activity-log-view.md#azure-portal)。

> [!NOTE]
> 资源图中的更改详细信息用于资源管理器属性。 若要跟踪虚拟机内的更改，请参阅 Azure 自动化的[更改跟踪](../../../automation/automation-change-tracking.md)或 azure 策略的[Vm 的来宾配置](../../policy/concepts/guest-configuration.md)。

> [!IMPORTANT]
> Azure Resource Graph 中的更改历史记录目前以公共预览版提供。

## <a name="find-when-changes-were-detected"></a>查找检测到更改的时间

查看资源发生的更改的第一步是查找某个时段内与该资源相关的更改事件。 此步骤是通过 **resourceChanges** REST 终结点完成的。

**resourceChanges** 终结点要求在请求正文中使用两个参数：

- **resourceId**：要查看其更改的 Azure 资源。
- **interval**：包含检查更改事件的开始日期和结束日期（采用**祖鲁时区 (Z)** ）的属性。

示例请求正文：

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "interval": {
        "start": "2019-03-28T00:00:00.000Z",
        "end": "2019-03-31T00:00:00.000Z"
    }
}
```

对于上述请求正文，**resourceChanges** 的 REST API URI 是：

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChanges?api-version=2018-09-01-preview
```

响应如以下示例所示：

```json
{
    "changes": [{
            "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}",
            "beforeSnapshot": {
                "timestamp": "2019-03-29T01:32:05.993Z"
            },
            "afterSnapshot": {
                "timestamp": "2019-03-29T01:54:24.42Z"
            }
        },
        {
            "changeId": "9dc352cb-b7c1-4198-9eda-e5e3ed66aec8",
            "beforeSnapshot": {
                "timestamp": "2019-03-28T10:30:19.68Z"
            },
            "afterSnapshot": {
                "timestamp": "2019-03-28T21:12:31.337Z"
            }
        }
    ]
}
```

对 **resourceId** 检测到的每个更改事件具有该资源的唯一 **changeId**。 **changeId** 字符串有时可能包含其他属性，只能保证它是唯一的。 更改记录包括创建快照之前和之后的时间。
更改事件是在此时段内的某个时间点发生的。

## <a name="see-what-properties-changed"></a>查看更改的属性

获取 **resourceChanges** 终结点的 **changeId** 后，可以使用 **resourceChangeDetails** REST 终结点获取更改事件的细节。

**resourceChangeDetails** 终结点要求在请求正文中使用两个参数：

- **resourceId**：要查看其更改的 Azure 资源。
- **changeId**：从 **resourceChanges** 中收集的 **resourceId** 的唯一更改事件。

示例请求正文：

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}"
}
```

对于上述请求正文，**resourceChangeDetails** 的 REST API URI 是：

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChangeDetails?api-version=2018-09-01-preview
```

响应如以下示例所示：

```json
{
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}",
    "beforeSnapshot": {
        "timestamp": "2019-03-29T01:32:05.993Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
            "properties": {
                "networkAcls": {
                    "bypass": "AzureServices",
                    "virtualNetworkRules": [],
                    "ipRules": [],
                    "defaultAction": "Allow"
                },
                "supportsHttpsTrafficOnly": false,
                "encryption": {
                    "services": {
                        "file": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    },
    "afterSnapshot": {
        "timestamp": "2019-03-29T01:54:24.42Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
            "properties": {
                "networkAcls": {
                    "bypass": "AzureServices",
                    "virtualNetworkRules": [],
                    "ipRules": [],
                    "defaultAction": "Allow"
                },
                "supportsHttpsTrafficOnly": true,
                "encryption": {
                    "services": {
                        "file": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    }
}
```

**beforeSnapshot** 和 **afterSnapshot** 分别提供快照创建时间以及当时的属性。 更改是在这些快照之间的某个时间点发生的。 在以上示例中我们可以看到，更改的属性是 **supportsHttpsTrafficOnly**。

若要以编程方式比较结果，请比较每个快照的 **content** 部分来确定差异。 如果比较整个快照，**timestamp** 始终会显示差异，不过这符合预期。

## <a name="next-steps"></a>后续步骤

- 在[初学者查询](../samples/starter.md)中了解使用的语言。
- 在[高级查询](../samples/advanced.md)中了解高级用法。
- 了解如何[浏览资源](../concepts/explore-resources.md)。