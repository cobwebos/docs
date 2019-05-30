---
title: 创建、 Azure Monitor 中的视图和管理活动日志警报
description: 如何使用 Azure 门户、 Azure 资源管理器模板和 Azure PowerShell 创建活动日志警报。
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/15/2018
ms.author: vinagara
ms.openlocfilehash: f25321fa5a13ed5a39a62a4115bb0bc10306d36f
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244961"
---
# <a name="create-view-and-manage-activity-log-alerts-using-azure-monitor"></a>使用 Azure Monitor 创建、查看和管理活动日志警报  

## <a name="overview"></a>概述
活动日志警报是新发生的活动日志事件与警报中指定的条件匹配时激活的警报。

这些警报针对 Azure 资源，可以使用 Azure 资源管理器模板来创建。 此外，还可以在 Azure 门户中创建、更新或删除它们。 通常，可以创建活动日志警报，以便在 Azure 订阅中的资源发生特定更改时接收通知（通常限于特定资源组或资源）。 例如，你可能希望在删除 **myProductionResourceGroup**（示例资源组）中的任何虚拟机时接收通知，或者，可能希望在任何新角色分配到订阅中的用户时接收通知。

> [!IMPORTANT]
> 无法通过活动日志警报创建界面创建服务运行状况警报通知。 若要了解有关创建和使用服务运行状况通知的详细信息，请参阅[接收有关服务运行状况通知的活动日志警报](alerts-activity-log-service-notifications.md)。

## <a name="azure-portal"></a>Azure 门户

> [!NOTE]
> 
>  创建警报规则时，请确保：
> 
> - 范围中的订阅不同于创建警报的订阅。
> - 条件必须是配置警报所依据的级别/状态/调用方/资源组/资源 ID/资源类型/事件类别。
> - 警报配置 JSON 中没有“anyOf”条件或嵌套的条件（简单而言，只允许一个 allOf，而不允许更多的 allOf/anyOf）。
> - 当类别是“管理”时， 必须在警报中至少指定上述条件之一。 不能创建每次在活动日志中创建事件时激活的警报。

### <a name="create-with-azure-portal"></a>使用 Azure 门户进行创建

请按以下过程操作：

1. 在 Azure 门户中，选择“监视” > “警报”  
2. 单击“警报”窗口顶部的“新建警报规则”   。

     ![新建警报规则](media/alerts-activity-log/AlertsPreviewOption.png)

     此时将显示“创建规则”窗口。 

      ![新建警报规则选项](media/alerts-activity-log/create-new-alert-rule-options.png)

3. 在“定义警报条件”下提供以下信息，然后单击“完成”。  

   - **警报目标：** 若要查看并选择新警报的目标，请使用“按订阅筛选” / “按资源类型筛选”，并从显示的列表中选择资源或资源组。  

     > [!NOTE]
     > 
     > 可以为活动日志信号选择资源、资源组或整个订阅。

     **警报目标示例视图**
     ![选择目标](media/alerts-activity-log/select-target.png)

   - 下**目标条件**，单击**添加条件**并且包括来自各种类别的显示目标的所有可用信号**活动日志**;在类别名称后追加**监视器服务**名称。

   - 从显示的类型的各种可能操作的列表中选择信号**活动日志**。

     可为此目标信号选择日志历史记录时间线和相应的警报逻辑：

     **添加条件屏幕**

     ![添加条件](media/alerts-activity-log/add-criteria.png)

     **历史记录时间**：可以绘制在过去 6/12/24 小时内或过去一周内为所选操作提供的事件。

     **警报逻辑**：

     - **事件级别** - 事件的严重性级别。 “详细”、“信息性”、“警告”、“错误”或“严重”。     
     - **状态**：事件的状态。 例如，“已启动”、“失败”或“成功”。   
     - **事件发起者**：也称为“调用方”；执行操作的用户的电子邮件地址或 Azure Active Directory 标识符。

       应用了警报逻辑的示例信号图：

       ![ 已选择条件](media/alerts-activity-log/criteria-selected.png)

4. 在“定义警报规则详细信息”下提供以下详细信息： 

    - **警报规则名称** – 新警报规则的名称
    - **说明** – 新警报规则的说明
    - **将警报保存到资源组** – 选择要在其中保存此新规则的资源组。

5. 在“操作组”下，从下拉菜单中指定要分配到此新警报规则的操作组。  或者，[创建新的操作组](../../azure-monitor/platform/action-groups.md)并将其分配到新规则。 若要创建新组，请单击“+ 新建组”。 

6. 若要在创建规则后启用规则，请单击“创建后启用规则”选项对应的“是”。  
7. 单击“创建警报规则”。 

    随即为活动日志创建了新的警报规则，同时，窗口的右上角会显示一条确认消息。

    可以启用、禁用、编辑或删除规则。 详细了解如何管理活动日志规则。


或者，了解活动日志，可以在其创建警报规则的条件的一个简单的类比是以浏览或筛选事件通过[在 Azure 门户中的活动日志](activity-log-view.md#azure-portal)。 Azure Monitor 的活动日志中可以筛选或查找必要的事件，并使用，然后创建警报**添加活动日志警报**按钮; 然后按照步骤 4 及更高版本，如上面的教程中所述。
    
 ![ 从活动日志添加警报](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-azure-portal"></a>在 Azure 门户中查看和管理

1. 在 Azure 门户中，单击“监视” > “警报”，然后单击窗口左上角的“管理规则”    。

    ![ 管理警报规则](media/alerts-activity-log/manage-alert-rules.png)

    此时将显示可用规则的列表。

2. 搜索要修改的活动日志规则。

    ![ 搜索活动日志警报规则](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    可以使用可用的筛选器（“订阅”、“资源组”、“资源”、“信号类型”或“状态”）来查找想要编辑的活动规则。     

   > [!NOTE]
   > 
   > 只能编辑“说明”、“目标条件”和“操作组”。   

3. 选择规则并双击以编辑规则选项。 进行所需的更改，然后单击“保存”  。

   ![ 管理警报规则](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. 可以禁用、启用或删除规则。 根据步骤 2 中的详述选择规则后，在窗口顶部选择相应的选项。


## <a name="azure-resource-template"></a>Azure 资源模板
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
对于此演练，上面的示例 json 可以保存为（例如）sampleActivityLogAlert.json，并且可以使用 [Azure 门户中的 Azure 资源管理器](../../azure-resource-manager/resource-group-template-deploy-portal.md)进行部署。

> [!NOTE]
> 新的活动日志警报规则可能需要最多 5 分钟才变为活动状态。

## <a name="rest-api"></a>REST API 
[Azure 监视器-活动日志警报 API](https://docs.microsoft.com/rest/api/monitor/activitylogalerts)为 REST API 并与 Azure 资源管理器 REST API 完全兼容。 因此，可以使用资源管理器 cmdlet 和 Azure CLI 通过 Powershell 使用它。

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-resource-manager-template-with-powershell"></a>使用 PowerShell 部署资源管理器模板
若要使用 PowerShell 部署前面的[资源模板部分](#resource-manager-template 中显示的示例资源模板，请使用以下命令：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

其中，sampleActivityLogAlert.parameters.json 包含为创建警报规则时所需的参数提供的值。

### <a name="use-activity-log-powershell-cmdlets"></a>使用活动日志 PowerShell cmdlet

活动日志警报具有专用的 PowerShell cmdlet 可用：

- [Set-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Set-AzActivityLogAlert?view=azps-1.3.0)：新建或更新现有活动日志警报。
- [Get-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Get-AzActivityLogAlert?view=azps-1.3.0)：获取一个或多个活动日志警报资源。
- [Enable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Enable-AzActivityLogAlert?view=azps-1.3.0)：启用现有活动日志警报并设置其标记。
- [Disable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Disable-AzActivityLogAlert?view=azps-1.3.0)：禁用现有活动日志警报并设置其标记。
- [Remove-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Remove-AzActivityLogAlert?view=azps-1.3.0)    :删除活动日志警报。

## <a name="cli"></a>CLI

set [az monitor activity-log alert](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert) 下的专用 Azure CLI 命令可用于管理活动日志警报规则。

若要创建新的活动日志警报规则，请使用以下顺序：

1. [az monitor activity-log alert create](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create)：创建新的活动日志警报规则资源
1. [az monitor activity-log alert scope](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/scope)：为已创建的活动日志警报规则添加作用域
1. [az monitor activity-log alert action-group](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/action-group)：向活动日志警报规则中添加操作组

若要检索一个活动日志警报规则资源，可以使用 Azure CLI 命令 [az monitor activity-log alert show](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
)。 若要查看某个资源组中的所有活动日志警报规则资源，请使用 [az monitor activity-log alert list](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list)。
可以使用 Azure CLI 命令 [az monitor activity-log alert delete](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete) 删除活动日志警报规则资源。

## <a name="next-steps"></a>后续步骤

- [活动日志的 Webhook 架构](../../azure-monitor/platform/activity-log-alerts-webhook.md)
- [活动日志概述](../../azure-monitor/platform/activity-log-alerts.md) 
- 详细了解[操作组](../../azure-monitor/platform/action-groups.md)。  
- 了解[服务运行状况通知](../../azure-monitor/platform/service-notifications.md)。
