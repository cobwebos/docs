---
title: "逻辑应用定价模型 | Microsoft Docs"
description: "有关定价在逻辑应用中的工作方式的详细信息"
author: kevinlam1
manager: dwrede
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: f8f528f5-51c5-4006-b571-54ef74532f32
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2016
ms.author: klam
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 6c1b8f5d87b15948f4b892a27dd0375547e3a65a


---
# <a name="logic-apps-pricing-model"></a>逻辑应用定价模型
Azure 逻辑应用使你可以在云中扩展和执行集成工作流。  以下是有关逻辑应用的计费和定价计划的详细信息。

## <a name="consumption-pricing"></a>消耗定价
新创建的逻辑应用使用消耗计划。 借助逻辑应用消耗定价模型，只需为使用的部分付费。  使用消耗计划时，逻辑应用不会受到限制。
逻辑应用实例运行中执行的所有操作都按流量计费。

### <a name="what-are-action-executions"></a>什么是操作执行？
逻辑应用定义中的每个步骤都是操作。  这包括触发器、 控制流步骤（如条件、范围、for each 循环、do until 循环）、对连接器的调用以及对本地操作的调用。
触发器只是特殊操作而已，为在特定事件发生时实例化逻辑应用的新实例而设计。  对于触发器有一些不同的行为，它们可能会影响逻辑应用按流量计费的方式。

* **轮询触发器** – 此触发器持续轮询终结点，直到收到满足创建逻辑应用新实例的条件的消息。  可以在逻辑应用设计器中，在触发器中配置轮询间隔。  每个轮询请求（即使不会创建逻辑应用的新实例）都会计为操作执行。
* **Webhook 触发器** – 此触发器等待客户端在特定终结点上向它发送请求。  发送到 webhook 终结点的每个请求都会计为操作执行。 请求和 HTTP Webhook 触发器都是 webhook 触发器。
* **重复触发器** – 此触发器会基于在触发器中配置的重复间隔创建逻辑应用的新实例。  例如，重复触发器可以配置为每 3 天甚至是每分钟运行一次。

可以在逻辑应用资源边栏选项卡的“触发器历史记录”部分中查看触发器执行。

执行的所有操作（无论是成功还是失败）都会作为操作执行按流量计费。  由于未满足条件而跳过的操作或由于逻辑应用在完成之前终止而未执行的操作不会计为操作执行。

在循环中执行的操作会按循环的每个迭代进行计数。  例如，循环访问包含 10 个项的列表的 for each 循环中的单个操作会按照列表中的项数 (10) 乘以循环中的操作数 (1) 再加一（用于表示循环的启动）来计数，在此示例中是 (10 * 1) + 1 = 11 个操作执行。

处于禁用状态的逻辑应用不能实例化新实例，因此在禁用期间不会进行收费。  请注意，禁用逻辑应用之后，在完全禁用之前可能需要一点时间使实例停止。

## <a name="app-service-plans"></a>应用服务计划
创建逻辑应用不再需要应用服务计划。  还可以通过现有逻辑应用来引用应用服务计划。  以前使用应用服务计划创建的逻辑应用的行为会继续如同以前一样，根据所选计划，会在超过一定数量的每日执行之后受到限制，不会使用操作执行指示器进行计费。

应用服务计划及其每日允许的操作执行次数：

|  | 免费/共享/基本 | 标准 | 高级 |
| --- | --- | --- | --- |
| 每日操作执行次数 |200 |10,000 |50,000 |

### <a name="convert-from-consumption-to-app-service-plan-pricing"></a>从消耗转换为应用服务计划定价
若要为消耗逻辑应用引用应用服务计划，可以只需[运行以下 PowerShell 脚本](https://github.com/logicappsio/ConsumptionToAppServicePlan)。  确保已首先安装了 [Azure PowerShell 工具](https://github.com/Azure/azure-powershell)。

``` powershell
Param(
    [string] $AppService_RG = '<app-service-resource-group>',
    [string] $AppService_Name = '<app-service-name>',
    [string] $LogicApp_RG = '<logic-app-resource-group>',
    [string] $LogicApp_Name = '<logic-app-name>',
    [string] $subscriptionId = '<azure-subscription-id>'
)

Login-AzureRmAccount 
$subscription = Get-AzureRmSubscription -SubscriptionId $subscriptionId
$appserviceplan = Get-AzureRmResource -ResourceType "Microsoft.Web/serverFarms" -ResourceGroupName $AppService_RG -ResourceName $AppService_Name
$logicapp = Get-AzureRmResource -ResourceType "Microsoft.Logic/workflows" -ResourceGroupName $LogicApp_RG -ResourceName $LogicApp_Name

$sku = @{
    "name" = $appservicePlan.Sku.tier;
    "plan" = @{
      "id" = $appserviceplan.ResourceId;
      "type" = "Microsoft.Web/ServerFarms";
      "name" = $appserviceplan.Name  
    }
}

$updatedProperties = $logicapp.Properties | Add-Member @{sku = $sku;} -PassThru

$updatedLA = Set-AzureRmResource -ResourceId $logicapp.ResourceId -Properties $updatedProperties -ApiVersion 2015-08-01-preview
```

### <a name="convert-from-app-service-plan-pricing-to-consumption"></a>从应用服务计划定价转换为消耗
若要将与应用服务计划关联的逻辑应用更改为消耗模型，请在逻辑应用定义中删除对应用服务计划的引用。  这可以通过调用 PowerShell cmdlet 来完成：

`Set-AzureRmLogicApp -ResourceGroupName ‘rgname’ -Name ‘wfname’ –UseConsumptionModel -Force`

## <a name="pricing"></a>定价
有关定价详细信息，请参阅[逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps/)。

## <a name="next-steps"></a>后续步骤
* [逻辑应用的概述][whatis]
* [创建第一个逻辑应用][创建]

[定价]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: app-service-logic-what-are-logic-apps.md
[创建]: app-service-logic-create-a-logic-app.md




<!--HONumber=Nov16_HO3-->


