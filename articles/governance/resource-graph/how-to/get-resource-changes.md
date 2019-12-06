---
title: 获取资源更改
description: 了解如何查找资源的更改时间，获取已更改属性的列表，并评估差异。
ms.date: 10/09/2019
ms.topic: how-to
ms.openlocfilehash: 9504ac77fc4a3b03434912cc65284e2001df6e03
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873023"
---
# <a name="get-resource-changes"></a>获取资源更改

资源在每天使用、重新配置和重新部署的过程中发生更改。
更改可以来自单个或自动化的过程。 大多数更改都是由设计决定的，但有时也是如此。 在过去14天的更改历史记录中，可以通过 Azure 资源关系图执行以下操作：

- 查找 Azure 资源管理器属性中检测到更改的时间
- 对于每个资源更改，请参阅属性更改详细信息
- 查看检测到的更改前后的资源的完整比较

更改检测和详细信息对于以下示例方案非常重要：

- 在事件管理期间，了解_可能_的相关更改。 查询特定时间段内的更改事件，并评估更改详细信息。
- 使配置管理数据库（称为 CMDB）保持最新。 不会按计划的频率刷新所有资源及其完整属性集，只会获取更改的内容。
- 了解资源更改符合性状态时可能更改的其他属性。 评估这些附加属性可以提供对可能需要通过 Azure 策略定义进行管理的其他属性的见解。

本文介绍如何通过资源图的 SDK 收集此信息。 若要在 Azure 门户中查看此信息，请参阅 Azure 策略的[更改历史记录](../../policy/how-to/determine-non-compliance.md#change-history-preview)或 Azure 活动日志[更改历史记录](../../../azure-monitor/platform/activity-log-view.md#azure-portal)。
有关从基础结构层对应用程序所做的更改的详细信息，请参阅使用 Azure Monitor 中的[应用程序更改分析（预览版）](../../../azure-monitor/app/change-analysis.md) 。

> [!NOTE]
> 资源图中的更改详细信息用于资源管理器属性。 若要跟踪虚拟机内的更改，请参阅 Azure 自动化的[更改跟踪](../../../automation/automation-change-tracking.md)或 azure 策略的[Vm 的来宾配置](../../policy/concepts/guest-configuration.md)。

> [!IMPORTANT]
> Azure 资源关系图中的更改历史记录采用公共预览。

## <a name="find-detected-change-events-and-view-change-details"></a>查找检测到的更改事件和查看更改详细信息

查看资源变化的第一步是在时间窗口中查找与该资源相关的更改事件。 每个 change 事件还包含有关资源更改的详细信息。 此步骤通过**resourceChanges** REST 终结点完成。

**ResourceChanges**终结点接受请求正文中的以下参数：

- **resourceId** \[必需\]：要在其上查找更改的 Azure 资源。
- **间隔**\[所需的\]：在使用**祖鲁时区（Z）** 检查更改事件时的_开始_日期和_结束_日期属性。
- **fetchPropertyChanges** （可选）：一个布尔值属性，设置响应对象是否包括属性更改。

示例请求正文：

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "interval": {
        "start": "2019-09-28T00:00:00.000Z",
        "end": "2019-09-29T00:00:00.000Z"
    },
    "fetchPropertyChanges": true
}
```

对于上述请求正文， **resourceChanges**的 REST API URI 是：

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChanges?api-version=2018-09-01-preview
```

响应类似于以下示例：

```json
{
    "changes": [
        {
            "changeId": "{\"beforeId\":\"3262e382-9f73-4866-a2e9-9d9dbee6a796\",\"beforeTime\":\"2019-09-28T00:45:35.012Z\",\"afterId\":\"6178968e-981e-4dac-ac37-340ee73eb577\",\"afterTime\":\"2019-09-28T00:52:53.371Z\"}",
            "beforeSnapshot": {
                "snapshotId": "3262e382-9f73-4866-a2e9-9d9dbee6a796",
                "timestamp": "2019-09-28T00:45:35.012Z"
            },
            "afterSnapshot": {
                "snapshotId": "6178968e-981e-4dac-ac37-340ee73eb577",
                "timestamp": "2019-09-28T00:52:53.371Z"
            },
            "changeType": "Create"
        },
        {
            "changeId": "{\"beforeId\":\"a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c\",\"beforeTime\":\"2019-09-28T00:43:38.366Z\",\"afterId\":\"3262e382-9f73-4866-a2e9-9d9dbee6a796\",\"afterTime\":\"2019-09-28T00:45:35.012Z\"}",
            "beforeSnapshot": {
                "snapshotId": "a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c",
                "timestamp": "2019-09-28T00:43:38.366Z"
            },
            "afterSnapshot": {
                "snapshotId": "3262e382-9f73-4866-a2e9-9d9dbee6a796",
                "timestamp": "2019-09-28T00:45:35.012Z"
            },
            "changeType": "Delete"
        },
        {
            "changeId": "{\"beforeId\":\"b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c\",\"beforeTime\":\"2019-09-28T00:43:15.518Z\",\"afterId\":\"a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c\",\"afterTime\":\"2019-09-28T00:43:38.366Z\"}",
            "beforeSnapshot": {
                "snapshotId": "b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c",
                "timestamp": "2019-09-28T00:43:15.518Z"
            },
            "afterSnapshot": {
                "snapshotId": "a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c",
                "timestamp": "2019-09-28T00:43:38.366Z"
            },
            "propertyChanges": [
                {
                    "propertyName": "tags.org",
                    "afterValue": "compute",
                    "changeCategory": "User",
                    "changeType": "Insert"
                },
                {
                    "propertyName": "tags.team",
                    "afterValue": "ARG",
                    "changeCategory": "User",
                    "changeType": "Insert"
                }
            ],
            "changeType": "Update"
        },
        {
            "changeId": "{\"beforeId\":\"19d12ab1-6ac6-4cd7-a2fe-d453a8e5b268\",\"beforeTime\":\"2019-09-28T00:42:46.839Z\",\"afterId\":\"b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c\",\"afterTime\":\"2019-09-28T00:43:15.518Z\"}",
            "beforeSnapshot": {
                "snapshotId": "19d12ab1-6ac6-4cd7-a2fe-d453a8e5b268",
                "timestamp": "2019-09-28T00:42:46.839Z"
            },
            "afterSnapshot": {
                "snapshotId": "b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c",
                "timestamp": "2019-09-28T00:43:15.518Z"
            },
            "propertyChanges": [{
                "propertyName": "tags.cgtest",
                "afterValue": "hello",
                "changeCategory": "User",
                "changeType": "Insert"
            }],
            "changeType": "Update"
        }
    ]
}
```

**ResourceId**的每个检测到的更改事件都具有以下属性：

- **changeId** -此值对于该资源是唯一的。 尽管**changeId**字符串有时可能包含其他属性，但它只能确保唯一。
- **beforeSnapshot** -包含检测到更改之前拍摄的资源快照的**snapshotId**和**时间戳**。
- **afterSnapshot** -包含检测到更改后拍摄的资源快照的**snapshotId**和**时间戳**。
- **changeType** -描述在**beforeSnapshot**和**afterSnapshot**之间为整个更改记录检测到的更改类型。 值为： _Create_、 _Update_和_Delete_。 仅当**changeType**为_Update_时才包含**propertyChanges**属性数组。
- **propertyChanges** -此属性数组详细说明了在**beforeSnapshot**和**afterSnapshot**之间更新的所有资源属性：
  - **propertyName** -已更改的资源属性的名称。
  - **changeCategory** -描述进行更改的内容。 值为：_系统_和_用户_。
  - **changeType** -描述检测到的单个资源属性的更改类型。
    值为：_插入_、_更新_、_删除_。
  - **beforeValue** - **beforeSnapshot**中资源属性的值。 当**changeType**为_Insert_时不显示。
  - **afterValue** - **afterSnapshot**中资源属性的值。 _删除_ **changeType**时不显示。

## <a name="compare-resource-changes"></a>比较资源更改

使用**resourceChanges**终结点中的**changeId** ，然后使用**resourceChangeDetails** REST 终结点获取已更改资源的快照和快照。

**ResourceChangeDetails**终结点需要请求正文中的两个参数：

- **resourceId**：用于比较更改的 Azure 资源。
- **changeId**：从**ResourceChanges**收集的**resourceId**的唯一更改事件。

示例请求正文：

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"afterTime\":'2019-05-10T00:00:00.000Z\"}"
}
```

对于上述请求正文， **resourceChangeDetails**的 REST API URI 是：

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChangeDetails?api-version=2018-09-01-preview
```

响应类似于以下示例：

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

**beforeSnapshot**和**afterSnapshot**分别给出拍摄快照的时间和该时间的属性。 更改发生在这些快照之间的某个时间点。 查看上面的示例，可以看到更改的属性是**supportsHttpsTrafficOnly**的。

若要对结果进行比较，请使用**resourceChanges**中的**更改**属性，或在**resourceChangeDetails**中评估每个快照的**内容**部分以确定不同之处。 如果比较快照，则**时间戳**始终显示为差异（尽管需要）。

## <a name="next-steps"></a>后续步骤

- 请参阅[Starter 查询](../samples/starter.md)中使用的语言。
- 请参阅高级[查询](../samples/advanced.md)中的高级使用。
- 了解有关如何[浏览资源](../concepts/explore-resources.md)的详细信息。