---
title: 使用资源管理器模板接收有关 Azure 服务通知的活动日志警报
description: 在 Azure 服务发生时，通过短信、电子邮件或 webhook 接收通知。
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 06/29/2020
ms.openlocfilehash: 84c888195ab7e2f3288691948706d31160393d25
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85918907"
---
# <a name="quickstart-create-activity-log-alerts-on-service-notifications-using-an-arm-template"></a>快速入门：使用 ARM 模板创建有关服务通知的活动日志警报

本文介绍如何使用 Azure 资源管理器模板（ARM 模板）为服务运行状况通知设置活动日志警报。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

服务运行状况通知存储在[Azure 活动日志](../azure-monitor/platform/platform-logs-overview.md)中。 鉴于活动日志中存储的信息量可能很大，因此有一个单独的用户界面，以便更轻松地查看和设置有关服务运行状况通知的警报。

当 Azure 将服务运行状况通知发送到 Azure 订阅时，你可以收到警报。 可以基于以下内容配置警报：

- 服务运行状况通知的类别（服务问题、计划内维护、运行状况公告）。
- 受影响的订阅。
- 受影响的服务。
- 受影响的区域。

> [!NOTE]
> 服务运行状况通知不会发送有关资源运行状况事件的警报。

还可以配置向其发送警报的人员：

- 选择现有操作组。
- 创建新操作组（可以用于将来的警报）。

若要了解有关操作组的详细信息，请参阅[创建和管理操作组](../azure-monitor/platform/action-groups.md)。

## <a name="prerequisites"></a>先决条件

- 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 若要从本地计算机运行命令，请安装 Azure CLI 或 Azure PowerShell 模块。 有关详细信息，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)和[安装 Azure PowerShell](/powershell/azure/install-az-ps)。

## <a name="review-the-template"></a>查看模板

以下模板用电子邮件目标创建操作组，并为目标订阅启用所有服务运行状况通知。 将此模板保存为*CreateServiceHealthAlert.js*。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroups_name": {
      "defaultValue": "SubHealth",
      "type": "String"
    },
    "activityLogAlerts_name": {
      "defaultValue": "ServiceHealthActivityLogAlert",
      "type": "String"
    },
    "emailAddress":{
      "type":"string"
    }
  },
  "variables": {
    "alertScope":"[concat('/','subscriptions','/',subscription().subscriptionId)]"
  },
  "resources": [
    {
      "comments": "Action Group",
      "type": "microsoft.insights/actionGroups",
      "apiVersion": "2019-06-01",
      "name": "[parameters('actionGroups_name')]",
      "location": "Global",
      "tags": {},
      "scale": null,
      "properties": {
        "groupShortName": "[parameters('actionGroups_name')]",
        "enabled": true,
        "emailReceivers": [
          {
            "name": "[parameters('actionGroups_name')]",
            "emailAddress": "[parameters('emailAddress')]"
          }
        ],
        "smsReceivers": [],
        "webhookReceivers": []
      },
      "dependsOn": []
    },
    {
      "comments": "Service Health Activity Log Alert",
      "type": "microsoft.insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlerts_name')]",
      "location": "Global",
      "tags": {},
      "scale": null,
      "properties": {
        "scopes": [
          "[variables('alertScope')]"
        ],
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "ServiceHealth"
            },
            {
              "field": "properties.incidentType",
              "equals": "Incident"
            }
          ]
        },
        "actions": {
          "actionGroups": [
            {
              "actionGroupId": "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]",
              "webhookProperties": {}
            }
          ]
        },
        "enabled": true,
        "description": ""
      },
      "dependsOn": [
        "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]"
      ]
    }
  ]
}
```

该模板定义了两个资源：

- [Microsoft Insights/actionGroups](/azure/templates/microsoft.insights/actiongroups)
- [Microsoft Insights/activityLogAlerts](/azure/templates/microsoft.insights/activityLogAlerts)

## <a name="deploy-the-template"></a>部署模板

使用用于[部署 ARM 模板](../azure-resource-manager/templates/deploy-portal.md)的任何标准方法（如以下示例，使用 CLI 和 PowerShell）部署模板。 将 "**资源组**" 和 " **emailAddress** " 的示例值替换为环境的相应值。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az login
az deployment group create --name CreateServiceHealthAlert --resource-group my-resource-group --template-file CreateServiceHealthAlert.json --parameters emailAddress='user@contoso.com'
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name CreateServiceHealthAlert -ResourceGroupName my-resource-group -TemplateFile CreateServiceHealthAlert.json -emailAddress user@contoso.com
```

---

## <a name="validate-the-deployment"></a>验证部署

验证是否已使用以下命令之一创建了工作区。 将**资源组**的示例值替换为你在上面使用的值。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az monitor activity-log alert show --resource-group my-resource-group --name ServiceHealthActivityLogAlert
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Get-AzActivityLogAlert -ResourceGroupName my-resource-group -Name ServiceHealthActivityLogAlert
```

---

## <a name="clean-up-resources"></a>清理资源

如果打算继续使用后续的快速入门和教程，则可能需要保留这些资源。 如果不再需要资源组，请删除该资源组，该资源组将删除警报规则和相关资源。 使用 Azure CLI 或 Azure PowerShell 删除资源组

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group delete --name my-resource-group
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Remove-AzResourceGroup -Name my-resource-group
```

---

## <a name="next-steps"></a>后续步骤

- 了解[设置 Azure 服务运行状况警报的最佳实践](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa)。
- 了解如何[为 Azure 服务运行状况设置移动推送通知](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw)。
- 了解如何[为现有问题管理系统配置 webhook 通知](service-health-alert-webhook-guide.md)。
- 了解[服务运行状况通知](service-notifications.md)。
- 了解[通知速率限制](../azure-monitor/platform/alerts-rate-limiting.md)。
- 查看[活动日志警报 webhook 架构](../azure-monitor/platform/activity-log-alerts-webhook.md)。
- 获取[活动日志警报概述](../azure-monitor/platform/alerts-overview.md)，了解如何接收警报。
- 详细了解[操作组](../azure-monitor/platform/action-groups.md)。
