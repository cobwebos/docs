---
title: "使用 Resource Manager 模板创建活动日志 | Microsoft Docs"
description: "创建 Azure 资源时收到通知。"
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: ancav
ms.translationtype: HT
ms.sourcegitcommit: cddb80997d29267db6873373e0a8609d54dd1576
ms.openlocfilehash: 26b140fef46a176b21bddbd7588543e71c251ed6
ms.contentlocale: zh-cn
ms.lasthandoff: 07/18/2017

---
# <a name="create-an-activity-log-alert-with-a-resource-manager-template"></a>使用 Resource Manager 模板创建活动日志
本文说明如何使用 [Azure Resource Manager 模板](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authoring-templates)配置活动日志警报。 这样，便可以在创建资源时自动对资源设置警报（作为自动部署过程的一部分）。

基本步骤如下所示：

1.  以 JSON 文件的形式创建一个描述如何创建活动日志警报的模板。
2.  [使用任意部署方法部署模板。](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)

## <a name="resource-manager-template-for-an-activity-log-alert"></a>活动日志警报的 Resource Manager 模板
若要使用 Resource Manager 模板创建活动日志警报，需要创建 `microsoft.insights/activityLogAlerts` 类型的资源，并填充所有相关属性。 下面是创建活动日志警报的模板。

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
    "activityLogAlertEnabled": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "Indicates whether or not the alert is enabled."
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
        "enabled": "[parameters('activityLogAlertEnabled')]",
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Resources/deployments/write"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
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

有关活动日志警报模板的一些示例，还可以[访问我们的快速入门库](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Insights)。

## <a name="next-steps"></a>后续步骤
- 详细了解[警报](monitoring-overview-alerts.md)  
- 如何[使用 Resource Manager 模板添加操作组](monitoring-create-action-group-with-resource-manager-template.md)
- [创建活动日志警报以监视订阅上的所有自动缩放引擎操作。](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [创建活动日志警报以监视订阅上所有失败的自动缩放缩小/扩大操作](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

