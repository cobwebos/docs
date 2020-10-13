---
title: 在 Azure Monitor 中创建、查看和管理活动日志警报
description: 使用 Azure 门户、Azure 资源管理器模板和 Azure PowerShell 创建活动日志警报。
ms.topic: conceptual
ms.subservice: alerts
ms.date: 06/25/2019
ms.openlocfilehash: 7914f3ee57d014de2f34fca301b587c6297bebe3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90983087"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>使用 Azure Monitor 创建、查看和管理活动日志警报  

## <a name="overview"></a>概述

活动日志警报是新发生的活动日志事件与警报中指定的条件匹配时激活的警报。

这些警报适用于 Azure 资源，可以使用 Azure 资源管理器模板来创建。 此外，还可以在 Azure 门户中创建、更新或删除它们。 通常，你可以创建活动日志警报，以便在 Azure 订阅中的资源发生特定的更改时接收通知。 警报通常限于特定的资源组或资源。 例如，你可能希望在示例资源组 **myProductionResourceGroup** 中的任何虚拟机被删除时收到通知。 或者，你可能希望在任何新角色分配到订阅中的用户时收到通知。

> [!IMPORTANT]
> 有关服务运行状况通知的警报无法通过用于创建活动日志警报的界面来创建。 若要详细了解如何创建和使用服务运行状况通知，请参阅[接收有关服务运行状况通知的活动日志警报](../../service-health/alerts-activity-log-service-notifications-portal.md)。

创建警报规则时，请确保：

- 范围中的订阅并未不同于创建警报的订阅。
- 条件必须是配置警报时所依据的级别、状态、调用方、资源组、资源 ID 或资源类型事件类别。
- 警报配置 JSON 中没有“anyOf”条件或嵌套的条件。 简单而言，只允许一个“allOf”条件，而不允许更多的“allOf”或“anyOf”条件。
- 当类别是“管理”时，必须在警报中至少指定上述条件之一。 不能创建每次在活动日志中创建事件时激活的警报。
- 无法为活动日志的“警报”类别中的事件创建警报。

## <a name="azure-portal"></a>Azure 门户

可以使用 Azure 门户创建和修改活动日志警报规则。 该体验与 Azure 活动日志相集成，以确保针对所关注的特定事件顺利创建警报。

### <a name="create-with-the-azure-portal"></a>使用 Azure 门户创建

使用以下过程。

1. 在 Azure 门户中，选择“监视” > “警报”。 
2. 选择“警报”窗口左上角的“新建警报规则”。 

     ![新建警报规则](media/alerts-activity-log/AlertsPreviewOption.png)

     此时将显示“创建规则”窗口。

      ![“新建警报规则”选项](media/alerts-activity-log/create-new-alert-rule-options.png)

3. 在“定义警报条件”下提供以下信息，然后选择“完成”： 

   - **警报目标：** 若要查看并选择新警报的目标，请使用“按订阅筛选” / “按资源类型筛选”。  从显示的列表中选择资源或资源组。

     > [!NOTE]
     > 
     > 只能为活动日志信号选择 [Azure 资源管理器](../../azure-resource-manager/management/overview.md)跟踪的资源、资源组或整个订阅。 

     **警报目标示例视图**

     ![选择目标](media/alerts-activity-log/select-target.png)

   - 在“目标条件”下，选择“添加条件”。  此时会显示目标的所有可用信号，包括来自各种类别的“活动日志”的信号。 类别名称会追加到“监视服务”名称后面。

   - 从**活动日志**类型的各种可能操作的显示列表中选择信号。

     可为此目标信号选择日志历史记录时间线和相应的警报逻辑：

     **添加条件屏幕**

     ![添加条件](media/alerts-activity-log/add-criteria.png)
     
     > [!NOTE]
     > 
     >  为了获得优质高效的规则，我们要求在规则中至少再添加一个信号为“所有管理操作”的条件。 
     > 作为警报定义的一部分，必须填写一个下拉选项：“事件级别”、“状态”或“发起者”，这会使规则变得更具体。

     - **历史记录时间**：可以绘制在过去 6、12、24 小时内或过去一周内为所选操作提供的事件。

     - **警报逻辑**：

       - **事件级别**：事件的严重级别：“详细”、“信息性”、“警告”、“错误”或“严重”。    
       - **状态**：事件的状态：例如，“已启动”、“失败”或“成功”。  
       - **事件发起者**：也称为“调用方”。 电子邮件地址或执行操作的用户的 Azure Active Directory 标识符。

       此示例信号图已应用警报逻辑：

       ![已选择条件](media/alerts-activity-log/criteria-selected.png)

4. 在“定义警报详细信息”下提供以下详细信息：

    - **警报规则名称**：新警报规则的名称。
    - **说明**：新警报规则的说明。
    - **将警报保存到资源组**：选择要在其中保存此新规则的资源组。

5. 在“操作组”下，从下拉菜单中指定要分配到此新警报规则的操作组。 或者，[创建新的操作组](./action-groups.md)并将其分配到新规则。 若要创建新组，请选择“+ 新建组”。

6. 若要在创建规则后启用规则，请选择“创建后启用规则”选项对应的“是”。 
7. 选择“创建警报规则”。

    随即会为活动日志创建新的警报规则，并且窗口的右上角会显示一条确认消息。

    可以启用、禁用、编辑或删除规则。 详细了解如何管理活动日志规则。


可以通过一个简单的类比来理解在活动日志上创建警报规则时可以基于的条件，那就是通过 [Azure 门户中的活动日志](./activity-log.md#view-the-activity-log)浏览或筛选事件。 在“Azure Monitor - 活动日志”屏幕中，可以筛选或查找所需的事件，然后使用“添加活动日志警报”按钮创建警报。  然后遵循上面所示的步骤 4 到 7 操作。
    
 ![从活动日志添加警报](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-the-azure-portal"></a>在 Azure 门户中查看和管理

1. 在 Azure 门户中，选择“监视” > “警报”。  在窗口的左上角选择“管理警报规则”。

    ![屏幕截图显示具有突出显示的 "搜索" 框的活动日志。](media/alerts-activity-log/manage-alert-rules.png)

    此时将显示可用规则的列表。

2. 搜索要修改的活动日志规则。

    ![搜索活动日志警报规则](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    可以使用可用的筛选器（“订阅”、“资源组”、“资源”、“信号类型”或“状态”）来查找想要编辑的活动规则。    

   > [!NOTE]
   > 
   > 只能编辑“说明”、“目标条件”和“操作组”。  

3. 选择规则并双击以编辑规则选项。 进行所需的更改，然后选择“保存”。

   ![管理警报规则](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. 可以启用、禁用或删除规则。 根据步骤 2 中所述选择规则后，在窗口顶部选择相应的选项。


## <a name="azure-resource-manager-template"></a>Azure 资源管理器模板
若要使用 Azure 资源管理器模板创建活动日志警报规则，请创建 `microsoft.insights/activityLogAlerts` 类型的资源。 然后，填充所有相关属性。 下面是一个用于创建活动日志警报规则的模板：

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
对于此演练，可将上面的示例 JSON 保存为类似于 sampleActivityLogAlert.json 的文件名，然后可以使用 [Azure 门户中的 Azure 资源管理器](../../azure-resource-manager/templates/deploy-portal.md)部署该文件。

  > [!NOTE]
  > 
  > 请注意，可以定义的最高级别的活动日志警报是订阅。
  > 这意味着没有定义针对几个订阅的警报的选项，因此定义应该是针对每个订阅的警报。

以下字段是可以在 Azure 资源管理器模板中用于条件字段的选项：请注意，“资源运行状况”、“顾问”和“服务运行状况”有额外的属性字段，这是它们的特殊字段。 
1. resourceId：应该对其生成警报的活动日志事件中受影响资源的资源 ID。
2. category：活动日志事件的类别。 例如：Administrative、ServiceHealth、ResourceHealth、Autoscale、Security、Recommendation、Policy。
3. caller：执行活动日志事件操作的用户的电子邮件地址或 Azure Active Directory 标识符。
4. level：应该对其生成警报的活动日志事件中的活动级别。 例如：Critical、Error、Warning、Informational、Verbose。
5. operationName：活动日志事件中的操作名称。 例如：Microsoft.Resources/deployments/write
6. resourceGroup：活动日志事件中受影响资源的资源组名称。
7. resourceProvider：[Azure 资源提供程序和类型解释](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fresource-providers-and-types&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373543634&sdata=4RjpTkO5jsdOgPdt%2F%2FDOlYjIFE2%2B%2BuoHq5%2F7lHpCwQw%3D&reserved=0)。 有关资源提供程序到 Azure 服务的映射列表，请参阅 [Azure 服务的资源提供程序](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fazure-services-resource-providers&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373553639&sdata=0ZgJPK7BYuJsRifBKFytqphMOxMrkfkEwDqgVH1g8lw%3D&reserved=0)。
8. status：描述活动事件中操作状态的字符串。 例如：Started、In Progress、Succeeded、Failed、Active、Resolved
9. subStatus：通常为响应 REST 调用的 HTTP 状态码，但还可以包含其他用于描述子状态的字符串。   例如：正常(HTTP 状态代码:200)、已创建(HTTP 状态代码:201)、已接受(HTTP 状态代码:202)、没有任何内容(HTTP 状态代码:204)、错误的请求(HTTP 状态代码:400)、找不到(HTTP 状态代码:404)、冲突(HTTP 状态代码:409)、内部服务器错误(HTTP 状态代码:500)、服务不可用(HTTP 状态代码:503)、网关超时(HTTP 状态代码: 504)。
10. resourceType：受事件影响的资源的类型。 例如：Microsoft.Resources/deployments

例如：

```json
"condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        }

```
在[此处](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-monitor%2Fplatform%2Factivity-log-schema&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373563632&sdata=6QXLswwZgUHFXCuF%2FgOSowLzA8iOALVgvL3GMVhkYJY%3D&reserved=0)可以找到有关活动日志字段的更多详细信息。



> [!NOTE]
> 新的活动日志警报规则可能需要 5 分钟才能变为活动状态。

## <a name="rest-api"></a>REST API 
[Azure Monitor 活动日志警报 API](/rest/api/monitor/activitylogalerts) 是一个 REST API。 它与 Azure 资源管理器 REST API 完全兼容。 可以使用资源管理器 cmdlet 或 Azure CLI 通过 PowerShell 来使用它。

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>使用 PowerShell 部署资源管理器模板
若要使用 PowerShell 部署前面的 [Azure 资源管理器模板](#azure-resource-manager-template)部分所示的示例资源管理器模板，请使用以下命令：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

其中，sampleActivityLogAlert.parameters.json 包含一些值，这些值是为在创建警报规则时所需的参数提供的。

### <a name="use-activity-log-powershell-cmdlets"></a>使用活动日志 PowerShell cmdlet

活动日志警报具有专用的 PowerShell cmdlet 可用：

- [Set-AzActivityLogAlert](/powershell/module/az.monitor/set-azactivitylogalert)：创建新的活动日志警报，或更新现有的活动日志警报。
- [Get-AzActivityLogAlert](/powershell/module/az.monitor/get-azactivitylogalert)：获取一个或多个活动日志警报资源。
- [Enable-AzActivityLogAlert](/powershell/module/az.monitor/enable-azactivitylogalert)：启用现有活动日志警报并设置其标记。
- [Disable-AzActivityLogAlert](/powershell/module/az.monitor/disable-azactivitylogalert)：禁用现有活动日志警报并设置其标记。
- [Remove-AzActivityLogAlert](/powershell/module/az.monitor/remove-azactivitylogalert)：删除活动日志警报。

## <a name="azure-cli"></a>Azure CLI

set [az monitor activity-log alert](/cli/azure/monitor/activity-log/alert) 下的专用 Azure CLI 命令可用于管理活动日志警报规则。

若要创建新的活动日志警报规则，请下面的顺序使用以下命令：

1. [az monitor activity-log alert create](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create)：创建新的活动日志警报规则资源。
1. [az monitor activity-log alert scope](/cli/azure/monitor/activity-log/alert/scope)：为已创建的活动日志警报规则添加范围。
1. [az monitor activity-log alert action-group](/cli/azure/monitor/activity-log/alert/action-group)：将操作组添加到活动日志警报规则。

若要检索一个活动日志警报规则资源，请使用 Azure CLI 命令 [az monitor activity-log alert show](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
)。 若要查看某个资源组中的所有活动日志警报规则资源，请使用 [az monitor activity-log alert list](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list)。
可以使用 Azure CLI 命令 [az monitor activity-log alert delete](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete) 删除活动日志警报规则资源。

## <a name="next-steps"></a>后续步骤

- 了解[活动日志的 Webhook 架构](./activity-log-alerts-webhook.md)。
- 阅读[活动日志概述](./activity-log-alerts.md)。
- 详细了解[操作组](./action-groups.md)。  
- 了解[服务运行状况通知](../../service-health/service-notifications.md)。

