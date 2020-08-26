---
title: 使用资源管理器模板为新建议创建 Azure 顾问警报
description: 为新建议创建 Azure 顾问警报
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 06/29/2020
ms.openlocfilehash: 2becfbbc63beb6451e5e877c5a60553d98650494
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87057815"
---
# <a name="quickstart-create-azure-advisor-alerts-on-new-recommendations-using-an-arm-template"></a>快速入门：使用 ARM 模板为创建针对新建议的 Azure 顾问警报

本文介绍如何使用 Azure 资源管理器模板（ARM 模板）针对 Azure 顾问给出的新建议设置警报。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

当 Azure 顾问检测到针对某项资源的新建议时，将在 [Azure 活动日志](../azure-monitor/platform/platform-logs-overview.md)中存储一个事件。 可以使用特定于建议的警报创建体验，为来自 Azure 顾问的这些事件设置警报。 可以选择订阅和资源组（可选）来指定想要接收其警报的资源。

还可以使用以下属性来确定建议类型：

- 类别
- 影响级别
- 建议类型

还可通过以下方式配置触发警报时将发生的操作：  

- 选择现有操作组
- 创建新的操作组

若要了解有关操作组的详细信息，请参阅[创建和管理操作组](../azure-monitor/platform/action-groups.md)。

> [!NOTE]
> 顾问警报目前仅适用于高可用性、性能和成本建议。 不支持安全建议。

## <a name="prerequisites"></a>先决条件

- 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 若要从本地计算机运行命令，请安装 Azure CLI 或 Azure PowerShell 模块。 有关详细信息，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli) 和[安装 Azure PowerShell](/powershell/azure/install-az-ps)。

## <a name="review-the-template"></a>查看模板

以下模板将创建以电子邮件为目标的操作组，并为目标订阅启用所有服务运行状况通知。 将此模板保存为 CreateAdvisorAlert.json。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroups_name": {
      "defaultValue": "advisorAlert",
      "type": "string"
    },
    "activityLogAlerts_name": {
      "defaultValue": "AdvisorAlertsTest",
      "type": "string"
    },
    "emailAddress": {
      "defaultValue": "<email address>",
      "type": "string"
    }
  },
  "variables": {
    "alertScope": "[concat('/','subscriptions','/',subscription().subscriptionId)]"
  },
  "resources": [
    {
      "comments": "Action Group",
      "type": "microsoft.insights/actionGroups",
      "name": "[parameters('actionGroups_name')]",
      "apiVersion": "2019-06-01",
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
      "comments": "Azure Advisor Activity Log Alert",
      "type": "microsoft.insights/activityLogAlerts",
      "name": "[parameters('activityLogAlerts_name')]",
      "apiVersion": "2017-04-01",
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
              "equals": "Recommendation"
            },
            {
              "field": "properties.recommendationCategory",
              "equals": "Cost"
            },
            {
              "field": "properties.recommendationImpact",
              "equals": "Medium"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Advisor/recommendations/available/action"
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

该模板定义了两项资源：

- [Microsoft.Insights/actionGroups](/azure/templates/microsoft.insights/actiongroups)
- [Microsoft.Insights/activityLogAlerts](/azure/templates/microsoft.insights/activityLogAlerts)

## <a name="deploy-the-template"></a>部署模板

使用[部署 ARM 模板](../azure-resource-manager/templates/deploy-portal.md)的任何标准方法来部署模板，如以下使用 CLI 和 PowerShell 的示例。 将“资源组”和“emailAddress”的示例值替换为环境的相应值。  工作区名称在所有 Azure 订阅中必须唯一。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az login
az deployment group create --name CreateAdvisorAlert --resource-group my-resource-group --template-file CreateAdvisorAlert.json --parameters emailAddress='user@contoso.com'
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name CreateAdvisorAlert -ResourceGroupName my-resource-group -TemplateFile CreateAdvisorAlert.json -emailAddress user@contoso.com
```

---

## <a name="validate-the-deployment"></a>验证部署

验证是否已使用以下命令之一创建工作区。 将“资源组”的示例值替换为上面使用的值。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az monitor activity-log alert show --resource-group my-resource-group --name AdvisorAlertsTest
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Get-AzActivityLogAlert -ResourceGroupName my-resource-group -Name AdvisorAlertsTest
```

---

## <a name="clean-up-resources"></a>清理资源

如果打算继续使用后续的快速入门和教程，则可能需要保留这些资源。 如果不再需要资源组，可以将其删除，这将删除警报规则和相关的资源。 使用 Azure CLI 或 Azure PowerShell 删除资源组

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

- 获取[活动日志警报概述](../azure-monitor/platform/alerts-overview.md)，了解如何接收警报。
- 详细了解[操作组](../azure-monitor/platform/action-groups.md)。
