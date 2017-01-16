---
title: "使用 Resource Manager 模板自动启用诊断设置 | Microsoft Docs"
description: "了解如何使用 Resource Manager 模板创建诊断设置，以便将诊断日志流式传输到事件中心，或者将其存储在存储帐户中。"
author: johnkemnetz
manager: rboucher
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: a8a88a8c-4a48-4df6-8f7e-d90634d39c57
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2016
ms.author: johnkem
translationtype: Human Translation
ms.sourcegitcommit: 6a98f380dd77372e0db9d5de671c58b7660826bb
ms.openlocfilehash: a7d28566650b21193bb816c9399725babbafc228


---
# <a name="automatically-enable-diagnostic-settings-at-resource-creation-using-a-resource-manager-template"></a>在创建资源时使用 Resource Manager 模板自动启用诊断设置
本文介绍如何使用 [Azure Resource Manager 模板](../resource-group-authoring-templates.md)在创建资源时配置资源的诊断设置。 这样可以让用户在创建资源时自动将诊断日志和指标流式传输到事件中心、将其存档在存储帐户中，或者发送到 Log Analytics。

通过 Resource Manager 模板启用诊断日志时，所用方法取决于资源类型。

* **非计算**资源（例如，网络安全组、逻辑应用、自动化）使用[此文中描述的诊断设置](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings)。
* **计算**（基于 WAD/LAD）资源使用[此文中描述的 WAD/LAD 配置文件](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)。

本文介绍如何使用其中一种方法配置诊断。

基本步骤如下所示：

1. 以 JSON 文件格式创建一个模板，说明如何创建资源和启用诊断。
2. [使用任意部署方法部署模板](../resource-group-template-deploy.md)。

下面是一个需为非计算资源和计算资源生成的模板 JSON 文件的示例。

## <a name="non-compute-resource-template"></a>非计算资源模板
对于非计算资源，需要做两件事：

1. 根据存储帐户名称和服务总线规则 ID 和/或 OMS Log Analytics 工作区 ID 向参数 blob 添加参数（允许在存储帐户中存档诊断日志、将日志流式传输到事件中心和/或将日志发送到 Log Analytics）。
   
    ```json
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
      }
    },
    "serviceBusRuleId": {
      "type": "string",
      "metadata": {
        "description": "Service Bus Rule Id for the Service Bus Namespace in which the Event Hub should be created or streamed to."
      }
    },
    "workspaceId":{
      "type": "string",
      "metadata": {
        "description": "Log Analytics workspace ID for the Log Analytics workspace to which logs will be sent."
      }
    }
    ```
2. 在资源数组（包含需启用诊断日志的资源）中，添加类型为 `[resource namespace]/providers/diagnosticSettings` 的资源。
   
    ```json
    "resources": [
      {
        "type": "providers/diagnosticSettings",
        "name": "Microsoft.Insights/service",
        "dependsOn": [
          "[/*resource Id for which Diagnostic Logs will be enabled>*/]"
        ],
        "apiVersion": "2015-07-01",
        "properties": {
          "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
          "serviceBusRuleId": "[parameters('serviceBusRuleId')]",
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
    ]
    ```

诊断设置的属性 blob 遵循[此文所述的格式](https://msdn.microsoft.com/library/azure/dn931931.aspx)。 添加 `metrics` 属性还可将资源指标发送到这些相同输出。

下面是一个完整的示例，说明了如何创建网络安全组，以及如何启用流式传输到事件中心和在存储帐户中进行存储的功能。

```json

{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "nsgName": {
      "type": "string",
      "metadata": {
        "description": "Name of the NSG that will be created."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
      }
    },
    "serviceBusRuleId": {
      "type": "string",
      "metadata": {
        "description": "Service Bus Rule Id for the Service Bus Namespace in which the Event Hub should be created or streamed to."
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
      "type": "Microsoft.Network/networkSecurityGroups",
      "name": "[parameters('nsgName')]",
      "apiVersion": "2016-03-30",
      "location": "westus",
      "properties": {
        "securityRules": []
      },
      "resources": [
        {
          "type": "providers/diagnosticSettings",
          "name": "Microsoft.Insights/service",
          "dependsOn": [
            "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('nsgName'))]"
          ],
          "apiVersion": "2015-07-01",
          "properties": {
            "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
            "serviceBusRuleId": "[parameters('serviceBusRuleId')]",
            "workspaceId": "[parameters('workspaceId')]",
            "logs": [
              {
                "category": "NetworkSecurityGroupEvent",
                "enabled": true,
                "retentionPolicy": {
                  "days": 0,
                  "enabled": false
                }
              },
              {
                "category": "NetworkSecurityGroupRuleCounter",
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

## <a name="compute-resource-template"></a>计算资源模板
若要允许对计算资源（例如虚拟机或 Service Fabric 群集）进行诊断，需执行以下操作：

1. 将 Azure 诊断扩展添加到 VM 资源定义中。
2. 以参数形式指定存储帐户和/或事件中心。
3. 将 WADCfg XML 文件的内容添加到 XMLCfg 属性中，对所有 XML 字符进行适当的转义。

> [!WARNING]
> 这最后一步操作起来比较复杂。 请[参阅此文](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md#diagnostics-configuration-variables)获取相关示例，了解如何将诊断配置架构拆分成进行了正确转义和格式化操作的变量。
> 
> 

整个过程（包括示例）详见[此文档](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

## <a name="next-steps"></a>后续步骤
* [详细了解 Azure 诊断日志](monitoring-overview-of-diagnostic-logs.md)
* [将 Azure 诊断日志流式传输到事件中心](monitoring-stream-diagnostic-logs-to-event-hubs.md)




<!--HONumber=Nov16_HO4-->


