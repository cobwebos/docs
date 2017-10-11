---
title: "定价和计费 - Azure 逻辑应用 | Microsoft Docs"
description: "了解有关 Azure 逻辑应用的定价和计费原理。"
author: kevinlam1
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: f8f528f5-51c5-4006-b571-54ef74532f32
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: LADocs; klam
ms.openlocfilehash: 63784c5e3af360b2f3f8cb330a9df8b27a85d859
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="logic-apps-pricing-model"></a>逻辑应用定价模型
Azure 逻辑应用，可缩放和在云中执行集成工作流。  下面是有关逻辑应用的计费和定价计划的详细信息。
## <a name="consumption-pricing"></a>消耗量定价
新创建的逻辑应用使用消耗量计划。 借助逻辑应用消耗量定价模型，只需为使用的部分付费。  使用消耗量计划时，逻辑应用不会受到限制。
逻辑应用实例运行中执行的所有操作都按流量计费。
### <a name="what-are-action-executions"></a>什么是操作执行？
逻辑应用定义中的每个步骤都是一个操作，这包括触发器、控制流步骤（例如条件、作用域、for each 循环、do until 循环）、对连接器的调用以及对本地操作的调用。
触发器是特殊操作，为在特定事件发生时实例化逻辑应用的新实例而设计。  对于触发器有一些不同的行为，它们可能会影响逻辑应用按流量计费的方式。
* **轮询触发器** – 此触发器持续轮询终结点，直到收到满足创建逻辑应用实例的条件的消息。  可以在逻辑应用设计器中的触发器中配置轮询间隔。  每个轮询请求（即使不会创建逻辑应用的实例）都会计为一个操作执行。
* **Webhook 触发器** – 此触发器等待客户端在特定终结点上向它发送请求。  发送到 webhook 终结点的每个请求都会计为操作执行。 请求和 HTTP Webhook 触发器都是 webhook 触发器。
* **重复触发器** – 此触发器会基于在触发器中配置的重复间隔创建逻辑应用的实例。  例如，重复触发器可以配置为每三天甚至是每分钟运行一次。

可以在逻辑应用资源边栏选项卡的“触发器历史记录”部分中查看触发器执行。

执行的所有操作（无论是成功还是失败）都会作为操作执行按流量计费。  由于未满足条件而跳过的操作或由于逻辑应用在完成之前终止而未执行的操作不会计为操作执行。

在循环中执行的操作会按循环的每个迭代进行计数。  例如，循环访问包含 10 个项的列表的 for each 循环中的单个操作会按照列表中的项数 (10) 乘以循环中的操作数 (1) 再加一（用于表示循环的启动）来计数，在此示例中是 (10 * 1) + 1 = 11 个操作执行。
已禁用的逻辑应用不能实例化新实例，因此在禁用期间不会收费。  请注意，禁用逻辑应用之后，在完全禁用之前可能需要一点时间使实例停止。
### <a name="integration-account-usage"></a>集成帐户使用情况
基于消耗量的使用情况中包含一个用于浏览、开发和测试用途的[集成帐户](logic-apps-enterprise-integration-create-integration-account.md)，允许使用逻辑应用的 [B2B/EDI](logic-apps-enterprise-integration-b2b.md) 和 [XML 处理](logic-apps-enterprise-integration-xml.md)功能且不需要另外付费。 在每个区域最多可以创建一个帐户并且可以存储最多 10 个协议和 25 个地图。 架构、证书和合作伙伴没有限制，可以根据需要任意上传。

除了包括带有消耗量的集成帐户之外，还可以创建没有这些限制且采用我们的标准逻辑应用 SLA 的标准集成帐户。 有关详细信息，请参阅 [Azure 定价](https://azure.microsoft.com/pricing/details/logic-apps)。

## <a name="app-service-plans"></a>应用服务计划
以前创建的引用了某个应用服务计划的逻辑应用将继续像以前一样运行。 根据所选择的计划，在规定的每日执行后被限制，但是将使用操作执行计量表进行开单。
如果 EA 客户的订阅中有不必与逻辑应用显式关联的应用服务计划，那么这些客户会得到由包含的数量带来的好处。  例如，如果 EA 订阅中有一个标准应用服务计划并且在同一订阅中有一个逻辑应用，则每天有 10,000 次操作执行是免费的（请参阅下表）。 

应用服务计划及其每日允许的操作执行次数：
|  | 免费/共享/基本 | 标准 | 高级 |
| --- | --- | --- | --- |
| 每日操作执行次数 |200 |10,000 |50,000 |
### <a name="convert-from-app-service-plan-pricing-to-consumption"></a>从应用服务计划定价转换为消耗量
要将与应用服务计划关联的逻辑应用更改为消耗量模型，请在逻辑应用定义中删除对应用服务计划的引用。  可以通过调用一个 PowerShell cmdlet 来执行此更改：`Set-AzureRmLogicApp -ResourceGroupName ‘rgname’ -Name ‘wfname’ –UseConsumptionModel -Force`。
## <a name="pricing"></a>定价
有关定价详细信息，请参阅[逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps)。

## <a name="next-steps"></a>后续步骤
* [逻辑应用概述][whatis]
* [创建第一个逻辑应用][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: logic-apps-what-are-logic-apps.md
[create]: logic-apps-create-a-logic-app.md

