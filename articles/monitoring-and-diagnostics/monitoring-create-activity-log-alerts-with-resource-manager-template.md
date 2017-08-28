---
title: "使用资源管理器模板创建活动日志警报 | Microsoft Docs"
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
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 92076c7fe1f867919b7e02abf79cf0fb74fb7eb4
ms.contentlocale: zh-cn
ms.lasthandoff: 08/24/2017

---
# <a name="create-an-activity-log-alert-with-a-resource-manager-template"></a>使用资源管理器模板创建活动日志警报
本文说明如何使用 [Azure 资源管理器模板](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authoring-templates)配置活动日志警报。 使用模板，可以轻松设置在自动执行的部署过程中基于特定活动日志事件条件激活的多个警报。

基本步骤如下：

1. 以 JSON 文件的形式创建一个描述如何创建活动日志警报的模板。

2. 使用[任意部署方法](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)部署模板。

## <a name="resource-manager-template-for-an-activity-log-alert"></a>活动日志警报的 Resource Manager 模板
若要使用资源管理器模板创建活动日志警报，需要创建 `microsoft.insights/activityLogAlerts` 类型的资源。 然后，填充所有相关属性。 下面是用于创建活动日志警报的模板。

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

有关活动日志警报模板的一些示例，请访问我们的 [Azure 快速入门库](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Insights)。

## <a name="next-steps"></a>后续步骤
- 详细了解[警报](monitoring-overview-alerts.md)。
- 了解如何[使用资源管理器模板添加操作组](monitoring-create-action-group-with-resource-manager-template.md)。
- 了解如何[创建活动日志警报以监视订阅上的所有自动缩放引擎操作](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)。
- 了解如何[创建活动日志警报以监视订阅上所有失败的自动缩放缩小/扩大操作](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)。

