---
title: 建立 Azure 资源运行状况警报-资源管理器模板
description: 以编程方式创建在 Azure 资源不可用时发出通知的警报。
ms.topic: conceptual
ms.date: 9/4/2018
ms.openlocfilehash: 34367c89666fd486f233136ce7968c7f3bf61912
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2019
ms.locfileid: "75551709"
---
# <a name="configure-resource-health-alerts-using-resource-manager-templates"></a>使用资源管理器模板创建资源运行状况警报

本文介绍如何使用 Azure 资源管理器模板和 Azure PowerShell 以编程方式创建资源运行状况活动日志警报。

通过 Azure 资源运行状况可得知 Azure 资源的当前及历史运行状况。 Azure 资源运行状况警报会在这些资源的运行状况发生变化时几乎实时地发出通知。 通过以编程方式创建资源运行状况警报，用户可以批量创建警报并对其进行自定义。

> [!NOTE]
> 资源运行状况警报目前处于预览阶段。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>必备组件

若要按本页中的说明操作，需事先进行几项设置：

1. 需要安装[Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-Az-ps)
2. [创建或重新使用](../azure-monitor/platform/action-groups.md)配置为向你发出通知的操作组

## <a name="instructions"></a>Instructions
1. 使用 PowerShell，使用你的帐户登录到 Azure，并选择需与之交互的订阅

        Login-AzAccount
        Select-AzSubscription -Subscription <subscriptionId>

    > 可以使用 `Get-AzSubscription` 列出有权访问的订阅。

2. 查找并保存操作组的完整 Azure 资源管理器 ID

        (Get-AzActionGroup -ResourceGroupName <resourceGroup> -Name <actionGroup>).Id

3. 创建资源运行状况警报的资源管理器模板，并保存为 `resourcehealthalert.json`（[请参阅下面的详细信息](#resource-manager-template-options-for-resource-health-alerts)）

4. 使用该模板创建一个新的 Azure 资源管理器部署

        New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <resourceGroup> -TemplateFile <path\to\resourcehealthalert.json>

5. 系统将提示你键入之前复制的警报名称和操作组资源 ID：

        Supply values for the following parameters:
        (Type !? for Help.)
        activityLogAlertName: <Alert Name>
        actionGroupResourceId: /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/microsoft.insights/actionGroups/<actionGroup>

6. 如果一切正常，PowerShell 中将显示一条确认消息

        DeploymentName          : ExampleDeployment
        ResourceGroupName       : <resourceGroup>
        ProvisioningState       : Succeeded
        Timestamp               : 11/8/2017 2:32:00 AM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
                                Name                     Type       Value
                                ===============          =========  ==========
                                activityLogAlertName     String     <Alert Name>
                                activityLogAlertEnabled  Bool       True
                                actionGroupResourceId    String     /...
        
        Outputs                 :
        DeploymentDebugLogLevel :

请注意，如果你打算使该进程完全自动化，只需在第 5 步中将资源管理器模板编辑为不提示值。

## <a name="resource-manager-template-options-for-resource-health-alerts"></a>资源管理器资源运行状况警报的模板选项

可将此基本模板用作创建资源运行状况警报的起点。 此模板按创建时指定的方式运行，将你注册为接收订阅中所有资源所有新激活的资源运行状况事件的警报。

> 在本文末，我们还附有一个更为复杂的警报模板，与本模板相比，该模板增强了资源运行状况的噪音比信号。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": true,
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "ResourceHealth"
            },
            {
              "field": "status",
              "equals": "Active"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```

但，不建议使用和本警报类似的广泛警报。 了解如何将本警报的范围精细调整为关注我们关心的以下事件。

### <a name="adjusting-the-alert-scope"></a>调整警报范围

可将资源运行状况警报配置为监视三种不同的范围的事件：

 * 订阅级别
 * 资源组级别
 * 资源级别

警报模板是在订阅级别配置的，但如果你需要将警报配置为仅通知关于某些资源或某个资源组内资源的运行状况，只需修改上述模板的 `scopes` 部分。

对于资源组级别的范围而言，范围部分应如下所示：
```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>"
],
```

对于资源级别的范围而言，范围部分应如下所示：

```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/<resource>"
],
```

例如： `"/subscriptions/d37urb3e-ed41-4670-9c19-02a1d2808ff9/resourcegroups/myRG/providers/microsoft.compute/virtualmachines/myVm"`

> 查看 Azure 资源以获取此字符串时，你可以转到 Azure 门户并查看 URL。

### <a name="adjusting-the-resource-types-which-alert-you"></a>调整向你发出警报的资源类型

订阅级别或资源组级别的警报可能具有不同类型的资源。 如果要将警报限制为仅来自某个资源组子集，可在模板的 `condition` 部分进行定义，如下所示：

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "resourceType",
                    "equals": "MICROSOFT.COMPUTE/VIRTUALMACHINES",
                    "containsAny": null
                },
                {
                    "field": "resourceType",
                    "equals": "MICROSOFT.STORAGE/STORAGEACCOUNTS",
                    "containsAny": null
                },
                ...
            ]
        }
    ]
},
```

其中，我们使用 `anyOf` 包装器使资源运行状况警报符合指定的任何条件，从而实现以特定资源类型为目标的警报。

### <a name="adjusting-the-resource-health-events-that-alert-you"></a>调整向你发出警报的资源运行状况事件
在资源经历运行状况事件时，它们可经过几个代表运行状况事件状态的阶段：`Active`、`In Progress`、`Updated` 和 `Resolved`。

你可能希望在资源运行状况不正常时获得通知，在这种情况下需将警报配置为仅在 `status` 为 `Active` 时发出通知。 而如果希望在其他阶段也得到通知，可以像下面的示例那样添加相关详细信息：

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "status",
                    "equals": "Active"
                },
                {
                    "field": "status",
                    "equals": "In Progress"
                },
                {
                    "field": "status",
                    "equals": "Resolved"
                },
                {
                    "field": "status",
                    "equals": "Updated"
                }
            ]
        }
    ]
}
```

如果希望在运行状况的全部四个阶段都获得通知，可以将这一条件全部删除，这样不管 `status` 属性是什么，警报都会向你发出通知。

### <a name="adjusting-the-resource-health-alerts-to-avoid-unknown-events"></a>将资源运行状况警报调整为避免“Unknown”事件

Azure 资源运行状况可通过使用测试运行器持续监控资源，向你报告资源的最新运行状况。 其报告的相关运行状况为：“Available”、“Unavailable”和“Degraded”。 但是，在运行器和 Azure 资源无法通信的情况下，会将资源的运行状况报告为被视为“Active”运行状况的“Unknown”。

但是，如果资源报告为“Unknown”，很有可能自上一次准确报告以来，其运行状况未发生变化。 如果想取消关于“Unknown”事件的警报，可以在模板中指明该逻辑：

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Available",
                    "containsAny": null
                },
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Unavailable",
                    "containsAny": null
                },
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Degraded",
                    "containsAny": null
                }
            ]
        },
        {
            "anyOf": [
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Available",
                    "containsAny": null
                },
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Unavailable",
                    "containsAny": null
                },
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Degraded",
                    "containsAny": null
                }
            ]
        },
    ]
},
```

在此示例中，我们仅对当前和以前的运行状况不是“Unknown”的事件发出通知。 如果你的警报被直接发送到移动电话货电子邮件，这一变化可能比较有用。 

请注意，currentHealthStatus 和 previousHealthStatus 属性在某些事件中可能为 null。 例如，当发生更新的事件时，资源的运行状况状态可能自上次报告以来未发生更改，只提供该额外事件信息（例如，原因）。 因此，使用上述子句可能会导致某些警报不会触发，因为 currentHealthStatus 和 previousHealthStatus 值将设置为 null。

### <a name="adjusting-the-alert-to-avoid-user-initiated-events"></a>将警报调整为避免通知用户发起的事件

资源运行状况事件可以由平台启动和用户启动的事件触发。 它的意义在于仅在运行状况事件由 Azure 平台引发时发送通知。

很容易地将警报配置为仅筛选以下事件类型：

```json
"condition": {
    "allOf": [
        ...,
        {
            "field": "properties.cause",
            "equals": "PlatformInitiated",
            "containsAny": null
        }
    ]
}
```
请注意，"原因" 字段可能在某些事件中为空。 也就是说，会发生运行状况转换（例如，不可用），并立即记录事件以防止发出通知延迟。 因此，使用上面的子句可能导致未触发警报，因为 properties 属性值将设置为 null。

## <a name="complete-resource-health-alert-template"></a>完成资源运行状况警报模板

使用上一节中所述的不同调整，此处是一个示例模板，该模板配置为最大程度地提高信噪比。 请记住前面所述的注意事项，其中在某些事件中 currentHealthStatus、previousHealthStatus 和原因属性值可能为 null。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "activityLogAlertName": {
            "type": "string",
            "metadata": {
                "description": "Unique name (within the Resource Group) for the Activity log alert."
            }
        },
        "actionGroupResourceId": {
            "type": "string",
            "metadata": {
                "description": "Resource Id for the Action group."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Insights/activityLogAlerts",
            "apiVersion": "2017-04-01",
            "name": "[parameters('activityLogAlertName')]",
            "location": "Global",
            "properties": {
                "enabled": true,
                "scopes": [
                    "[subscription().id]"
                ],
                "condition": {
                    "allOf": [
                        {
                            "field": "category",
                            "equals": "ResourceHealth",
                            "containsAny": null
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Available",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Unavailable",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Degraded",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Available",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Unavailable",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Degraded",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.cause",
                                    "equals": "PlatformInitiated",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "status",
                                    "equals": "Active",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "Resolved",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "In Progress",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "Updated",
                                    "containsAny": null
                                }
                            ]
                        }
                    ]
                },
                "actions": {
                    "actionGroups": [
                        {
                            "actionGroupId": "[parameters('actionGroupResourceId')]"
                        }
                    ]
                }
            }
        }
    ]
}
```

不过，什么样的配置最有效只有自己知道，请使用本文所述工具进行自定义吧。

## <a name="next-steps"></a>后续步骤

了解有关资源运行状况的详细信息：
-  [Azure 资源运行状况概述](Resource-health-overview.md)
-  [可通过 Azure 资源运行状况使用的资源类型和运行状况检查](resource-health-checks-resource-types.md)


创建服务运行状况警报：
-  [配置服务运行状况的警报](../azure-monitor/platform/alerts-activity-log-service-notifications.md) 
-  [Azure 活动日志事件架构](../azure-monitor/platform/activity-log-schema.md)
