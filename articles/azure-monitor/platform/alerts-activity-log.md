---
title: 在 Azure Monitor 中创建、查看和管理活动日志警报
description: 使用 Azure 门户、Azure 资源管理器模板和 Azure PowerShell 创建活动日志警报。
ms.topic: conceptual
ms.subservice: alerts
ms.date: 06/25/2019
ms.openlocfilehash: 9791ebaadeb1ee724692a9e1a0d61aff5cbae6a3
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77668479"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>使用 Azure Monitor 创建、查看和管理活动日志警报  

## <a name="overview"></a>概述

活动日志警报是新发生的活动日志事件与警报中指定的条件匹配时激活的警报。

这些警报适用于 Azure 资源，可通过使用 Azure 资源管理器模板来创建。 此外，还可以在 Azure 门户中创建、更新或删除它们。 通常，你可以创建活动日志警报，以便在 Azure 订阅中的资源发生特定更改时接收通知。 警报通常作用于特定的资源组或资源。 例如，你可能想要在删除示例资源组**myProductionResourceGroup**中的任何虚拟机时获得通知。 或者，如果将任何新角色分配到订阅中的用户，你可能希望收到通知。

> [!IMPORTANT]
> 对于活动日志警报创建，无法通过接口创建有关服务运行状况通知的警报。 若要了解有关如何创建和使用服务运行状况通知的详细信息，请参阅[接收有关服务运行状况通知的活动日志警报](alerts-activity-log-service-notifications.md)。

创建警报规则时，请确保以下各项：

- 作用域中的订阅与创建警报的订阅不同。
- 条件必须是在其上配置警报的 "级别"、"状态"、"调用方"、"资源组"、"资源 ID" 或 "资源类型" 事件类别。
- 警报配置 JSON 中没有 "anyOf" 条件或嵌套条件。 基本上，只允许一个 "allOf" 条件，无需进一步的 "allOf" 或 "anyOf" 条件。
- 当类别是 "管理" 时，必须在警报中至少指定上述条件之一。 不能创建每次在活动日志中创建事件时激活的警报。

## <a name="azure-portal"></a>Azure 门户

您可以使用 Azure 门户来创建和修改活动日志警报规则。 此体验与 Azure 活动日志集成，以确保为感兴趣的特定事件无缝创建警报。

### <a name="create-with-the-azure-portal"></a>用 Azure 门户创建

使用以下过程。

1. 在 Azure 门户中，选择 "**监视** > **警报**"。
2. 选择 "**警报**" 窗口左上角的 "**新建警报规则**"。

     ![新建警报规则](media/alerts-activity-log/AlertsPreviewOption.png)

     此时将显示“创建规则”窗口。

      ![新建警报规则选项](media/alerts-activity-log/create-new-alert-rule-options.png)

3. 在 "**定义警报条件**" 下，提供以下信息并选择 "**完成**"：

   - **警报目标：** 若要查看和选择新警报的目标，请使用**按订阅筛选** / **按资源类型筛选**。 从显示的列表中选择资源或资源组。

     > [!NOTE]
     > 
     > 只能选择[Azure 资源管理器](../../azure-resource-manager/management/overview.md)跟踪的资源、资源组或活动日志信号的整个订阅。 

     **警报目标示例视图**

     ![选择目标](media/alerts-activity-log/select-target.png)

   - 在 "**目标条件**" 下，选择 "**添加条件**"。 将显示目标的所有可用信号，其中包括来自各种类别的**活动日志**的信号。 类别名称将追加到**监视器服务**名称上。

   - 从**活动日志**类型的各种可能操作的显示列表中选择信号。

     可为此目标信号选择日志历史记录时间线和相应的警报逻辑：

     **添加条件屏幕**

     ![添加条件](media/alerts-activity-log/add-criteria.png)

     - **历史记录时间**：可用于所选操作的事件可在过去6小时、12小时或24小时或过去一周内绘制。

     - **警报逻辑**：

       - **事件级别**：事件的严重级别： "_详细_"、"_信息_"、"_警告_"、"_错误_" 或 "_严重_"。
       - **状态**：事件的状态： "_已启动_"、"已_失败_" 或 "已_成功_"。
       - **事件发起者**：也称为调用方。 电子邮件地址或执行操作的用户的 Azure Active Directory 标识符。

       此示例信号图应用了警报逻辑：

       ![选择的条件](media/alerts-activity-log/criteria-selected.png)

4. 在 "**定义警报详细信息**" 下提供以下详细信息：

    - **警报规则名称**：新警报规则的名称。
    - **说明**：新警报规则的说明。
    - **将警报保存到资源组**：选择要在其中保存此新规则的资源组。

5. 在“操作组”下，从下拉菜单中指定要分配到此新警报规则的操作组。 或者，[创建一个新的操作组](../../azure-monitor/platform/action-groups.md)，并将其分配给新规则。 若要创建新组，请选择 " **+ 新建组**"。

6. 若要在创建规则后启用规则，请在 "**创建时启用规则**" 选项中选择 **"是"** 。
7. 选择 "**创建警报规则**"。

    将创建活动日志的新警报规则，并在窗口的右上角出现一条确认消息。

    可以启用、禁用、编辑或删除规则。 了解有关如何管理活动日志规则的详细信息。


在活动日志中，可以通过[活动 Azure 门户日志](activity-log-view.md#azure-portal)浏览或筛选事件的一个简单类比是在活动日志中创建警报规则。 在 " **Azure Monitor 活动日志**" 屏幕中，可以筛选或查找必需的事件，然后使用 "**添加活动日志警报**" 按钮创建警报。 然后按照前面所示执行步骤4到步骤7。
    
 ![从活动日志添加警报](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-the-azure-portal"></a>在 Azure 门户中查看和管理

1. 在 Azure 门户中，选择 "**监视** > **警报**"。 在窗口的左上角选择 "**管理警报规则**"。

    ![管理警报规则](media/alerts-activity-log/manage-alert-rules.png)

    此时将显示可用规则的列表。

2. 搜索要修改的活动日志规则。

    ![搜索活动日志警报规则](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    您可以使用 "可用筛选器"、"_订阅_"、"_资源组_"、"_资源_"、"_信号类型_" 或 "_状态_"，查找要编辑的活动规则。

   > [!NOTE]
   > 
   > 你只能编辑 "**说明**"、"**目标条件**" 和 "**操作组**"。

3. 选择规则，然后双击编辑规则选项。 进行所需的更改，然后选择 "**保存**"。

   ![管理警报规则](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. 您可以启用、禁用或删除规则。 根据步骤2中所述选择规则后，在窗口顶部选择相应的选项。


## <a name="azure-resource-manager-template"></a>Azure 资源管理器模板
若要使用 Azure 资源管理器模板创建活动日志警报，请创建 `microsoft.insights/activityLogAlerts`类型的资源。 然后，填充所有相关属性。 下面是创建活动日志警报的模板：

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
之前的示例 JSON 可以保存为（例如，sampleActivityLogAlert），并可使用[Azure 资源管理器在 Azure 门户中](../../azure-resource-manager/templates/deploy-portal.md)进行部署。

> [!NOTE]
> 新的活动日志警报规则最长可能需要5分钟才会生效。

## <a name="rest-api"></a>REST API 
[Azure Monitor 活动日志警报 API](https://docs.microsoft.com/rest/api/monitor/activitylogalerts)是一个 REST API。 它与 Azure 资源管理器 REST API 完全兼容。 可以通过 PowerShell 使用资源管理器 cmdlet 或 Azure CLI 使用该方法。

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>通过 PowerShell 部署资源管理器模板
若要使用 PowerShell 部署上一个[Azure 资源管理器模板](#azure-resource-manager-template)部分中显示的示例资源管理器模板，请使用以下命令：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

其中，sampleActivityLogAlert 包含为创建警报规则所需的参数提供的值。

### <a name="use-activity-log-powershell-cmdlets"></a>使用活动日志 PowerShell cmdlet

活动日志警报具有专用的 PowerShell cmdlet 可用：

- [AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Set-AzActivityLogAlert)：创建新的活动日志警报，或更新现有的活动日志警报。
- [AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Get-AzActivityLogAlert)：获取一个或多个活动日志警报资源。
- [AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Enable-AzActivityLogAlert)：启用现有活动日志警报并设置其标记。
- [AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Disable-AzActivityLogAlert)：禁用现有活动日志警报并设置其标记。
- [AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Remove-AzActivityLogAlert)：删除活动日志警报。

## <a name="azure-cli"></a>Azure CLI

set [az monitor activity-log alert](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert) 下的专用 Azure CLI 命令可用于管理活动日志警报规则。

若要创建新的活动日志警报规则，请按以下顺序使用以下命令：

1. [az monitor 活动-日志警报创建](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create)：创建新的活动日志警报规则资源。
1. [az monitor 活动-日志警报范围](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/scope)：添加创建的活动日志警报规则的作用域。
1. [az monitor 活动-日志警报操作-组](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/action-group)：将操作组添加到活动日志警报规则。

若要检索一个活动日志警报规则资源，请使用 Azure CLI 命令[az monitor monitor activity 日志 alert show](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
)。 若要查看资源组中的所有活动日志警报规则资源，请使用[az monitor 活动日志警报列表](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list)。
可以通过使用 Azure CLI 命令[az monitor monitor alert delete](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete)来删除活动日志警报规则资源。

## <a name="next-steps"></a>后续步骤

- 了解[活动日志的 webhook 架构](../../azure-monitor/platform/activity-log-alerts-webhook.md)。
- 阅读[活动日志的概述](../../azure-monitor/platform/activity-log-alerts.md)。
- 详细了解[操作组](../../azure-monitor/platform/action-groups.md)。  
- 了解[服务运行状况通知](../../azure-monitor/platform/service-notifications.md)。
