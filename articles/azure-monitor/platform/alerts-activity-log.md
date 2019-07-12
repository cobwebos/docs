---
title: 创建、 查看和管理在 Azure Monitor 活动日志警报
description: 通过使用 Azure 门户、 Azure 资源管理器模板和 Azure PowerShell 创建活动日志警报。
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: vinagara
ms.openlocfilehash: 71e61228fcdbd52abedbc1f1205baa60b1aea923
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705298"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>创建、 查看和管理通过使用 Azure Monitor 活动日志警报  

## <a name="overview"></a>概述
活动日志警报是新发生的活动日志事件与警报中指定的条件匹配时激活的警报。

这些警报是针对 Azure 资源，并可以使用 Azure 资源管理器模板创建。 此外，还可以在 Azure 门户中创建、更新或删除它们。 通常情况下，将创建活动日志警报，以便在 Azure 订阅中的资源发生特定更改时接收通知。 警报通常限于特定资源组或资源。 例如，你可能想要当任何一个时收到通知的示例资源组中的虚拟机**myProductionResourceGroup**被删除。 或者，你可能想要向你发送通知任何新角色分配给你的订阅中的用户。

> [!IMPORTANT]
> 不能通过活动日志警报创建的接口创建有关服务运行状况通知警报。 若要了解有关如何创建和使用服务运行状况通知的详细信息，请参阅[接收有关服务运行状况通知活动日志警报](alerts-activity-log-service-notifications.md)。

在创建警报规则时，请确保以下各项：

- 作用域中的订阅并没有不同订阅中创建警报。
- 条件必须级别、 状态、 调用方、 资源组、 资源 ID 或在其配置警报的资源类型事件类别。
- 没有"anyOf"条件或嵌套在警报配置 JSON 中的条件。 基本上，只有一个"allOf"条件允许不带任何进一步的"所有"或"anyOf"条件。
- 当类别是"管理"时，您必须在警报中指定至少一个上述条件。 不能创建每次在活动日志中创建事件时激活的警报。


## <a name="azure-portal"></a>Azure 门户

可以使用 Azure 门户创建和修改活动日志警报规则。 与 Azure 活动日志，以确保无缝的警报创建的特定事件感兴趣的集成体验。

### <a name="create-with-the-azure-portal"></a>使用 Azure 门户创建

使用以下过程。

1. 在 Azure 门户中，选择**监视器** > **警报**。
2. 选择**新的警报规则**的左上角**警报**窗口。

     ![新建警报规则](media/alerts-activity-log/AlertsPreviewOption.png)

     此时将显示“创建规则”窗口。 

      ![新的警报规则选项](media/alerts-activity-log/create-new-alert-rule-options.png)

3. 下**定义警报条件**，提供以下信息，并选择**完成**:

   - **警报目标：** 若要查看和选择新警报的目标，请使用**按订阅筛选器** / **按资源类型筛选**。 从显示的列表中选择的资源或资源组。

     > [!NOTE]
     > 
     > 可以选择资源、 资源组或整个订阅的活动日志信号。

     **警报目标示例视图**

     ![选择目标](media/alerts-activity-log/select-target.png)

   - 下**目标条件**，选择**添加条件**。 显示为目标的所有可用信号，其中包括各种类别的来自**活动日志**。 类别名称追加到**监视器服务**名称。

   - 从**活动日志**类型的各种可能操作的显示列表中选择信号。

     可为此目标信号选择日志历史记录时间线和相应的警报逻辑：

     **添加条件屏幕**

     ![添加条件](media/alerts-activity-log/add-criteria.png)

     - **历史记录时间**：在上一次 6、 12 或 24 小时或过去一周，可以绘制事件可用于所选操作。

     - **警报逻辑**：

       - **事件级别**:事件的严重性级别：“详细”、“信息性”、“警告”、“错误”或“严重”。    
       - **状态**：事件的状态：例如，“已启动”、“失败”或“成功”。  
       - **事件发起者**：也称为调用方。 电子邮件地址或执行操作的用户的 Azure Active Directory 标识符。

       此示例信号图已应用的警报逻辑：

       ![所选条件](media/alerts-activity-log/criteria-selected.png)

4. 下**定义警报详细信息**，提供以下详细信息：

    - **警报规则名称**:新的警报规则的名称。
    - **说明**：新的警报规则的说明。
    - **将警报保存到资源组**:选择你想要保存此新规则的资源组。

5. 在“操作组”下，从下拉菜单中指定要分配到此新警报规则的操作组。  或者，[创建新的操作组](../../azure-monitor/platform/action-groups.md)并将其分配到新的规则。 若要创建新组，请选择 **+ 新建组**。

6. 若要启用这些规则后创建它们，请选择**是**有关**创建后的启用规则**选项。
7. 选择**创建警报规则**。

    创建新的活动日志警报规则，并在窗口的右上角会显示一条确认消息。

    可以启用、禁用、编辑或删除规则。 了解有关如何管理活动日志规则的详细信息。


了解活动日志中可以在其创建警报规则的条件的一个简单的类比是浏览或筛选事件通过[在 Azure 门户中的活动日志](activity-log-view.md#azure-portal)。 在中**Azure Monitor-活动日志**屏幕上，您可以筛选或查找必要的事件，然后通过使用创建警报**添加活动日志警报**按钮。 然后按照所示的步骤 4 到 7 像以前一样。
    
 ![添加从活动日志警报](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-the-azure-portal"></a>在 Azure 门户中查看和管理

1. 在 Azure 门户中，选择**监视器** > **警报**。 选择**管理警报规则**在窗口的左上角。

    ![管理警报规则](media/alerts-activity-log/manage-alert-rules.png)

    此时将显示可用规则的列表。

2. 搜索要修改的活动日志规则。

    ![搜索活动日志警报规则](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    可以使用可用的筛选器，_订阅_，_资源组_，_资源_，_信号类型_，或_状态_，若要查找你想要编辑的活动规则。

   > [!NOTE]
   > 
   > 您可以只编辑**描述**，**目标条件**，并**操作组**。

3. 选择规则，然后双击以编辑规则选项。 进行必要的更改，然后选择**保存**。

   ![管理警报规则](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. 可以启用、 禁用或删除规则。 步骤 2 中所述选择规则后，请选择窗口顶部相应的选项。


## <a name="azure-resource-manager-template"></a>Azure 资源管理器模板
若要使用 Azure 资源管理器模板创建活动日志警报，你创建的类型的资源`microsoft.insights/activityLogAlerts`。 然后，填充所有相关属性。 下面是用于创建活动日志警报的模板：

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
前面的示例 JSON 可以另存为，例如，出于本演练的目的 sampleActivityLogAlert.json 和可以通过使用部署[在 Azure 门户中的 Azure 资源管理器](../../azure-resource-manager/resource-group-template-deploy-portal.md)。

> [!NOTE]
> 可能需要最多 5 分钟的新活动日志警报规则变为活动状态。

## <a name="rest-api"></a>REST API 
[Azure Monitor 活动日志警报 API](https://docs.microsoft.com/rest/api/monitor/activitylogalerts)是一个 REST API。 它是与 Azure 资源管理器 REST API 完全兼容。 它可通过 PowerShell 使用资源管理器 cmdlet 或 Azure CLI。

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>部署资源管理器模板与 PowerShell
若要使用 PowerShell 部署中所示的示例资源管理器模板[Azure 资源管理器模板](#azure-resource-manager-template)部分中，使用以下命令：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

其中，sampleActivityLogAlert.parameters.json 包含为创建警报规则时所需的参数提供的值。

### <a name="use-activity-log-powershell-cmdlets"></a>使用活动日志 PowerShell cmdlet

活动日志警报具有专用的 PowerShell cmdlet 可用：

- [Set-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Set-AzActivityLogAlert):创建新的活动日志警报或更新现有活动日志警报。
- [Get-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Get-AzActivityLogAlert):获取一个或多个活动日志警报资源。
- [启用 AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Enable-AzActivityLogAlert):启用现有活动日志警报，并设置其标记。
- [Disable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Disable-AzActivityLogAlert):禁用现有活动日志警报并设置其标记。
- [Remove-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Remove-AzActivityLogAlert):删除活动日志警报。

## <a name="azure-cli"></a>Azure CLI

set [az monitor activity-log alert](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert) 下的专用 Azure CLI 命令可用于管理活动日志警报规则。

若要创建新的活动日志警报规则，请按以下顺序使用以下命令：

1. [az monitor activity-log alert create](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create)：创建新的活动日志警报规则资源。
1. [az monitor activity-log alert scope](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/scope)：添加用于创建的活动日志预警规则的作用域。
1. [az monitor activity-log alert action-group](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/action-group)：将操作组添加到活动日志警报规则。

若要检索活动日志警报规则的一个资源，请使用 Azure CLI 命令[az monitor 活动日志警报显示](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
)。 若要查看活动日志警报规则的所有资源的资源组中，使用[az monitor 活动日志警报列表](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list)。
使用 Azure CLI 命令可删除活动日志警报规则资源[az monitor 活动日志警报删除](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete)。

## <a name="next-steps"></a>后续步骤

- 了解如何[活动日志的 webhook 架构](../../azure-monitor/platform/activity-log-alerts-webhook.md)。
- 读取[活动日志概述](../../azure-monitor/platform/activity-log-alerts.md)。
- 详细了解[操作组](../../azure-monitor/platform/action-groups.md)。  
- 了解[服务运行状况通知](../../azure-monitor/platform/service-notifications.md)。
