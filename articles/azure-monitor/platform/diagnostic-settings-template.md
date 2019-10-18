---
title: 使用资源管理器模板在 Azure 中创建诊断设置 |Microsoft Docs
description: 使用资源管理器模板创建诊断设置，以便将 Azure 平台日志转发到 Azure Monitor 日志、Azure 存储或 Azure 事件中心。
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: f65e3c4f9582fcc5c28412d44e513fa6bcb9e870
ms.sourcegitcommit: 6eecb9a71f8d69851bc962e2751971fccf29557f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "71262331"
---
# <a name="create-diagnostic-setting-in-azure-using-a-resource-manager-template"></a>使用资源管理器模板在 Azure 中创建诊断设置
Azure 中的[平台日志](platform-logs-overview.md)提供有关 azure 资源及其所依赖的 azure 平台的详细诊断和审核信息。 本文详细介绍了如何使用[Azure 资源管理器模板](../../azure-resource-manager/resource-group-authoring-templates.md)来配置诊断设置，以便将平台日志收集到不同的目标。 这样，便可以在创建资源时自动开始收集平台日志。


## <a name="resource-manager-template"></a>Resource Manager 模板
资源管理器模板中有两个部分，需要对其进行编辑以创建诊断设置。 以下部分介绍了这些部分。

### <a name="parameters"></a>parameters
根据诊断设置的[目标](diagnostic-settings.md#destinations)，将参数添加到存储帐户名称、事件中心授权规则 id 和 Log Analytics 工作区 ID 的参数 blob。
   
```json
"settingName": {
  "type": "string",
  "metadata": {
    "description": "Name for the diagnostic setting resource. Eg. 'archiveToStorage' or 'forSecurityTeam'."
  }
},
"storageAccountName": {
  "type": "string",
  "metadata": {
    "description": "Name of the Storage Account in which platform logs should be saved."
  }
},
"eventHubAuthorizationRuleId": {
  "type": "string",
  "metadata": {
    "description": "Resource ID of the event hub authorization rule for the Event Hubs namespace in which the event hub should be created or streamed to."
  }
},
"eventHubName": {
  "type": "string",
  "metadata": {
    "description": "Optional. Name of the event hub within the namespace to which logs are streamed. Without this, an event hub is created for each log category."
  }
},
"workspaceId":{
  "type": "string",
  "metadata": {
    "description": "Azure Resource ID of the Log Analytics workspace for the Log Analytics workspace to which logs will be sent."
  }
}
```

### <a name="resources"></a>资源
在要为其创建诊断设置的资源的资源数组中，添加一个 `[resource namespace]/providers/diagnosticSettings` 类型的资源。 "属性" 部分遵循[诊断设置-创建或更新](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate)中所述的格式。 添加 `metrics` 属性，以便在[资源支持度量值](metrics-supported.md)时将资源度量收集到相同的目标。
   
```json
"resources": [
  {
    "type": "providers/diagnosticSettings",
    "name": "[concat('Microsoft.Insights/', parameters('settingName'))]",
    "dependsOn": [
      "[/*resource Id for which resource logs will be enabled>*/]"
    ],
    "apiVersion": "2017-05-01-preview",
    "properties": {
      "name": "[parameters('settingName')]",
      "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
      "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
      "eventHubName": "[parameters('eventHubName')]",
      "workspaceId": "[parameters('workspaceId')]",
      "logs": [ 
        {
          "category": "/* log category name */",
          "enabled": true,
          "retentionPolicy": {
            "days": 0,
            "enabled": false
          }
        }
      ],
      "metrics": [
        {
          "category": "AllMetrics",
          "enabled": true,
          "retentionPolicy": {
            "enabled": false,
            "days": 0
          }
        }
      ]
    }
  }
]
```



## <a name="example"></a>示例
下面是一个完整的示例，该示例创建了一个逻辑应用并创建了一个诊断设置，该设置允许将资源日志流式传输到事件中心和存储帐户中的存储。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "logicAppName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Logic App that will be created."
      }
    },
    "testUri": {
      "type": "string",
      "defaultValue": "https://azure.microsoft.com/status/feed/"
    },
    "settingName": {
      "type": "string",
      "metadata": {
        "description": "Name of the setting. Name for the diagnostic setting resource. Eg. 'archiveToStorage' or 'forSecurityTeam'."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which resource logs should be saved."
      }
    },
    "eventHubAuthorizationRuleId": {
      "type": "string",
      "metadata": {
        "description": "Resource ID of the event hub authorization rule for the Event Hubs namespace in which the event hub should be created or streamed to."
      }
    },
    "eventHubName": {
      "type": "string",
      "metadata": {
        "description": "Optional. Name of the event hub within the namespace to which logs are streamed. Without this, an event hub is created for each log category."
      }
    },
    "workspaceId": {
      "type": "string",
      "metadata": {
        "description": "Log Analytics workspace ID for the Log Analytics workspace to which logs will be sent."
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Logic/workflows",
      "name": "[parameters('logicAppName')]",
      "apiVersion": "2016-06-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "definition": {
          "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "testURI": {
              "type": "string",
              "defaultValue": "[parameters('testUri')]"
            }
          },
          "triggers": {
            "recurrence": {
              "type": "recurrence",
              "recurrence": {
                "frequency": "Hour",
                "interval": 1
              }
            }
          },
          "actions": {
            "http": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('testUri')"
              },
              "runAfter": {}
            }
          },
          "outputs": {}
        },
        "parameters": {}
      },
      "resources": [
        {
          "type": "providers/diagnosticSettings",
          "name": "[concat('Microsoft.Insights/', parameters('settingName'))]",
          "dependsOn": [
            "[resourceId('Microsoft.Logic/workflows', parameters('logicAppName'))]"
          ],
          "apiVersion": "2017-05-01-preview",
          "properties": {
            "name": "[parameters('settingName')]",
            "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
            "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
            "eventHubName": "[parameters('eventHubName')]",
            "workspaceId": "[parameters('workspaceId')]",
            "logs": [
              {
                "category": "WorkflowRuntime",
                "enabled": true,
                "retentionPolicy": {
                  "days": 0,
                  "enabled": false
                }
              }
            ],
            "metrics": [
              {
                "timeGrain": "PT1M",
                "enabled": true,
                "retentionPolicy": {
                  "enabled": false,
                  "days": 0
                }
              }
            ]
          }
        }
      ],
      "dependsOn": []
    }
  ]
}

```


## <a name="next-steps"></a>后续步骤
* 详细了解[Azure 中的平台日志](platform-logs-overview.md)。
* 了解[诊断设置](diagnostic-settings.md)。