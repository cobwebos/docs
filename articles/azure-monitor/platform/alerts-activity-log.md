---
title: 在 Azure Monitor 中创建、查看和管理活动日志警报
description: 使用 Azure 门户、Azure 资源管理器模板和 Azure PowerShell 创建活动日志警报。
ms.topic: conceptual
ms.subservice: alerts
ms.date: 06/25/2019
ms.openlocfilehash: bfbe2bc3ae3edf9285d3ec006ab0451f070cabd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132396"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>使用 Azure Monitor 创建、查看和管理活动日志警报  

## <a name="overview"></a>概述

活动日志警报是新发生的活动日志事件与警报中指定的条件匹配时激活的警报。

这些警报针对 Azure 资源，可以使用 Azure 资源管理器模板来创建。 此外，还可以在 Azure 门户中创建、更新或删除它们。 通常，你可以创建活动日志警报，以便在 Azure 订阅中的资源发生特定的更改时接收通知。 警报通常限于特定的资源组或资源。 例如，你可能希望在删除示例资源组 **myProductionResourceGroup** 中的任何虚拟机时收到通知。 或者，你可能希望在任何新角色分配到订阅中的用户时收到通知。

> [!IMPORTANT]
> 无法通过活动日志警报创建界面创建服务运行状况警报通知。 若要详细了解如何创建和使用服务运行状况通知，请参阅[接收有关服务运行状况通知的活动日志警报](alerts-activity-log-service-notifications.md)。

创建警报规则时，请确保：

- 范围中的订阅不同于创建警报的订阅。
- 条件必须是配置警报所依据的级别、状态、调用方、资源组、资源 ID 或资源类型事件类别。
- 警报配置 JSON 中没有“anyOf”条件或嵌套的条件。 简单而言，只允许一个“allOf”条件，而不允许更多的“allOf”或“anyOf”条件。
- 当类别是“管理”时，必须在警报中至少指定上述条件之一。 不能创建每次在活动日志中创建事件时激活的警报。

## <a name="azure-portal"></a>Azure 门户

可以使用 Azure 门户创建和修改活动日志警报规则。 该体验与 Azure 活动日志相集成，以确保针对相关的特定事件顺利创建警报。

### <a name="create-with-the-azure-portal"></a>使用 Azure 门户创建

使用以下过程。

1. 在 Azure 门户中，选择 **"监视** > **警报**"。
2. 选择“警报”窗口左上角的“新建警报规则”。********

     ![新建警报规则](media/alerts-activity-log/AlertsPreviewOption.png)

     此时将显示“创建规则”窗口。****

      ![新建警报规则选项](media/alerts-activity-log/create-new-alert-rule-options.png)

3. 在“定义警报条件”下提供以下信息，然后选择“完成”：********

   - **警报目标：** 要查看和选择新警报的目标，请使用 / **按订阅筛选器（按资源类型**）**筛选**。 从显示的列表中选择资源或资源组。

     > [!NOTE]
     > 
     > 只能为活动日志信号选择 [Azure 资源管理器](../../azure-resource-manager/management/overview.md)跟踪的资源、资源组或整个订阅。 

     **警报目标示例视图**

     ![选择目标](media/alerts-activity-log/select-target.png)

   - 在“目标条件”下，选择“添加条件”。******** 此时会显示目标的所有可用信号，包括来自各种“活动日志”类别的信号。**** “监视服务”名称的后面追加了类别名称。****

   - 从**活动日志**类型的各种可能操作的显示列表中选择信号。

     可为此目标信号选择日志历史记录时间线和相应的警报逻辑：

     **添加条件屏幕**

     ![添加条件](media/alerts-activity-log/add-criteria.png)

     - **历史记录时间**：可用于所选操作的事件可以绘制在过去 6、12 或 24 小时或过去一周。

     - **警报逻辑**：

       - **事件级别**：事件的严重性级别：_详细_、_信息_、_警告_、_错误_或_严重_。
       - **状态**：事件的状态：_已启动_、_失败_或_成功_。
       - **由 启动的事件**： 也称为调用方。 电子邮件地址或执行操作的用户的 Azure Active Directory 标识符。

       此示例信号图已应用警报逻辑：

       ![已选择条件](media/alerts-activity-log/criteria-selected.png)

4. 在“定义警报详细信息”下提供以下详细信息：****

    - **警报规则名称**：新警报规则的名称。
    - **说明**：新警报规则的说明。
    - **将警报保存到资源组**：选择要保存此新规则的资源组。

5. 在“操作组”下，从下拉菜单中指定要分配到此新警报规则的操作组。**** 或者，[创建新的操作组](../../azure-monitor/platform/action-groups.md)并将其分配到新规则。 若要创建新组，请选择“+ 新建组”。****

6. 若要在创建规则后启用规则，请选择“创建后启用规则”选项对应的“是”。********
7. 选择“创建警报规则”。****

    随即为活动日志创建了新的警报规则，同时，窗口的右上角会显示一条确认消息。

    可以启用、禁用、编辑或删除规则。 详细了解如何管理活动日志规则。


可以通过简单的类比来理解在活动日志上创建警报规则时可以基于的条件，那就是通过 [Azure 门户中的活动日志](activity-log-view.md#azure-portal)浏览或筛选事件。 在“Azure Monitor - 活动日志”屏幕中，可以筛选或查找所需的事件，并使用“添加活动日志警报”按钮创建警报********。 然后遵循上文所述的步骤 4 到 7 操作。
    
 ![从活动日志添加警报](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-the-azure-portal"></a>在 Azure 门户中查看和管理

1. 在 Azure 门户中，选择 **"监视** > **警报**"。 在窗口的左上角选择“管理警报规则”。****

    ![管理警报规则](media/alerts-activity-log/manage-alert-rules.png)

    此时将显示可用规则的列表。

2. 搜索要修改的活动日志规则。

    ![搜索活动日志警报规则](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    可以使用可用的筛选器（“订阅”、“资源组”、“资源”、“信号类型”或“状态”）来查找想要编辑的活动规则。__________

   > [!NOTE]
   > 
   > 只能编辑“说明”、“目标条件”和“操作组”。************

3. 选择规则并双击以编辑规则选项。 进行所需的更改，然后选择“保存”。****

   ![管理警报规则](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. 可以启用、禁用或删除规则。 根据步骤 2 中的详述选择规则后，在窗口顶部选择相应的选项。


## <a name="azure-resource-manager-template"></a>Azure 资源管理器模板
要使用 Azure 资源管理器模板创建活动日志警报规则，请创建类型 的资源`microsoft.insights/activityLogAlerts`。 然后，填充所有相关属性。 下面是创建活动日志警报规则的模板：

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
上面的示例 JSON 可以保存为（例如）sampleActivityLogAlert.json，并且可以使用 [Azure 门户中的 Azure 资源管理器](../../azure-resource-manager/templates/deploy-portal.md)进行部署。

以下字段是可用于条件字段的 Azure 资源管理器模板中的选项：请注意，"资源运行状况"、"顾问"和"服务运行状况"具有其特殊字段的额外属性字段。 
1. 资源 Id：应在生成警报的活动日志事件中受影响的资源的资源 ID。
2. 类别：活动日志事件中 的类别。 例如：管理、服务运行状况、资源运行状况、自动缩放、安全性、建议、策略。
3. 调用方：执行活动日志事件操作的用户的电子邮件地址或 Azure 活动目录标识符。
4. 级别：应在生成警报的活动日志事件中的活动级别。 例如：严重、错误、警告、信息、详细。
5. 操作名称：活动日志事件中的操作的名称。 例如：微软.资源/部署/写入
6. 资源组：活动日志事件中受影响资源的资源组的名称。
7. 资源提供程序[：Azure 资源提供程序和类型说明](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fresource-providers-and-types&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373543634&sdata=4RjpTkO5jsdOgPdt%2F%2FDOlYjIFE2%2B%2BuoHq5%2F7lHpCwQw%3D&reserved=0)。 有关将资源提供程序映射到 Azure 服务的列表，请参阅 [Azure 服务的资源提供程序](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fazure-services-resource-providers&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373553639&sdata=0ZgJPK7BYuJsRifBKFytqphMOxMrkfkEwDqgVH1g8lw%3D&reserved=0)。
8. 状态：描述活动事件中操作状态的字符串。 例如：已启动、正在进行、成功、失败、活动、已解决
9. 子状态：通常对应 REST 调用的 HTTP 状态代码，但也可以包括描述子状态的其他字符串。   例如：确定（HTTP 状态代码： 200）、已创建 （HTTP 状态代码： 201）、已接受（HTTP 状态代码：202）、无内容（HTTP 状态代码：204）、错误请求（HTTP 状态代码：400）、未找到（HTTP 状态代码：404）、冲突（HTTP 状态代码：409）、内部服务器错误（HTTP 状态代码： 500），服务不可用（HTTP 状态代码：503），网关超时（HTTP 状态代码：504）。
10. 资源类型：受事件影响的资源的类型。 例如：微软.资源/部署

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
有关活动日志字段的更多详细信息，请[在此处](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-monitor%2Fplatform%2Factivity-log-schema&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373563632&sdata=6QXLswwZgUHFXCuF%2FgOSowLzA8iOALVgvL3GMVhkYJY%3D&reserved=0)找到 。



> [!NOTE]
> 新的活动日志警报规则可能需要最多 5 分钟才变为活动状态。

## <a name="rest-api"></a>REST API 
[Azure Monitor 活动日志警报 API](https://docs.microsoft.com/rest/api/monitor/activitylogalerts) 是一个 REST API。 它与 Azure 资源管理器 REST API 完全兼容。 可以使用资源管理器 cmdlet 或 Azure CLI 通过 PowerShell 来使用它。

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>使用 PowerShell 部署资源管理器模板
若要使用 PowerShell 部署前面的 [Azure 资源管理器模板](#azure-resource-manager-template)部分所示的示例资源管理器模板，请使用以下命令：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

其中，sampleActivityLogAlert.parameters.json 包含为创建警报规则时所需的参数提供的值。

### <a name="use-activity-log-powershell-cmdlets"></a>使用活动日志 PowerShell cmdlet

活动日志警报具有专用的 PowerShell cmdlet 可用：

- [设置-AzActivityLogAlert：](https://docs.microsoft.com/powershell/module/az.monitor/Set-AzActivityLogAlert)创建新的活动日志警报或更新现有活动日志警报。
- [获取"AzActivityLogAlert"：](https://docs.microsoft.com/powershell/module/az.monitor/Get-AzActivityLogAlert)获取一个或多个活动日志警报资源。
- [启用-AzActivityLogAlert：](https://docs.microsoft.com/powershell/module/az.monitor/Enable-AzActivityLogAlert)启用现有活动日志警报并设置其标记。
- [禁用 AzActivityLogAlert：](https://docs.microsoft.com/powershell/module/az.monitor/Disable-AzActivityLogAlert)禁用现有活动日志警报并设置其标记。
- [删除"删除活动日志警报](https://docs.microsoft.com/powershell/module/az.monitor/Remove-AzActivityLogAlert)"：删除活动日志警报。

## <a name="azure-cli"></a>Azure CLI

set [az monitor activity-log alert](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert) 下的专用 Azure CLI 命令可用于管理活动日志警报规则。

若要创建新的活动日志警报规则，请依序使用以下命令：

1. [az 监视器活动日志警报创建](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create)：创建新的活动日志警报规则资源。
1. [az 监视器活动日志警报范围](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/scope)：为创建的活动日志警报规则添加作用域。
1. [az 监视器活动日志警报操作组](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/action-group)：向活动日志警报规则添加操作组。

若要检索一个活动日志警报规则资源，请使用 Azure CLI 命令 [az monitor activity-log alert show](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
)。 若要查看某个资源组中的所有活动日志警报规则资源，请使用 [az monitor activity-log alert list](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list)。
可以使用 Azure CLI 命令 [az monitor activity-log alert delete](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete) 删除活动日志警报规则资源。

## <a name="next-steps"></a>后续步骤

- 了解 [活动日志的 Webhook 架构](../../azure-monitor/platform/activity-log-alerts-webhook.md)。
- 阅读[活动日志概述](../../azure-monitor/platform/activity-log-alerts.md)。
- 了解有关[操作组](../../azure-monitor/platform/action-groups.md)的更多。  
- 了解[服务运行状况通知](../../azure-monitor/platform/service-notifications.md)。
