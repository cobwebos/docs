---
title: 定价和计费 - Azure 逻辑应用 | Microsoft Docs
description: 了解 Azure 逻辑应用的定价和计费原理
services: logic-apps
ms.service: logic-apps
ms.suite: logic-apps
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
manager: carmonm
ms.assetid: f8f528f5-51c5-4006-b571-54ef74532f32
ms.topic: article
ms.date: 02/26/2019
ms.openlocfilehash: 9b5452f112c6325dafd5edbe693b90ec2a94abc0
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2019
ms.locfileid: "56990231"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Azure 逻辑应用的定价模型

使用 Azure 逻辑应用时，可以创建和运行可在云中缩放的自动化集成工作流。 下面是有关逻辑应用计费和定价方式的详细信息。 

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>消耗量定价模型

对于在公共或“全局”逻辑应用服务中运行的新逻辑应用，只需根据自己的使用量付费即可。 这些逻辑应用使用基于消耗量的计划和定价模型。 在逻辑应用定义中，每个步骤都是操作。 操作包括触发器、任何控制流步骤、内置操作和连接器调用。 逻辑应用会对逻辑应用中运行的所有操作进行计量。  
有关详细信息，请参阅[逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps)。

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>固定定价模型

为内部运行的新逻辑应用[*集成服务环境*(ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)，固定每月价格支付内置操作和标准连接器。 ISE 提供了一种方法，用于创建和运行可访问 Azure 虚拟网络中资源的独立逻辑应用。 

在 ISE 基本单位具有固定容量，因此如果需要更大的吞吐量，你可以[添加更多缩放单位](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#add-capacity)，可以在创建期间或之后。 ISE 包含一个免费的企业连接器，其中包括任意连接数量。 其他企业连接器的使用情况根据企业消费价格收取费用。 

> [!NOTE]
> 在 ISE 处于[*公共预览版*](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 有关详细信息，请参阅[逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps)。

<a name="triggers"></a>

## <a name="triggers"></a>触发器

触发器是发生特定事件时创建逻辑应用实例的特殊操作。 触发器以不同方式起作用，从而影响逻辑应用的计量方式。

* **轮询触发器** - 此触发器持续检查终结点是否收到满足创建逻辑应用实例和启动工作流的条件的消息。 即使没有创建逻辑应用实例，逻辑应用也会将每个轮询请求计量为执行。 若要指定轮询间隔，请通过逻辑应用程序设计器设置触发器。

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Webhook 触发器** – 此触发器等待客户端向特定的终结点发送请求。 发送到 webhook 终结点的每个请求都会计为操作执行。 例如，请求和 HTTP Webhook 触发器都是 Webhook 触发器。

* **定期触发器** – 此触发器基于在触发器中设置的重复间隔创建逻辑应用实例。 例如，可以设置每隔三天运行的，或者根据更复杂的计划运行的定期触发器。

## <a name="actions"></a>操作

逻辑应用将内置操作计量为本机操作。 例如，内置操作包括通过 HTTP 进行调用、来自 Azure Functions 或 API 管理的调用，以及控制流步骤（如循环和条件） 
- 每项操作都有自己的操作类型。 调用[连接器](https://docs.microsoft.com/connectors)的操作为“ApiConnection”类型。 这些连接器分类为标准或企业连接器，根据各自的[定价][pricing]进行计量。 *预览*中的企业连接器作为标准连接器收费。

逻辑应用将所有成功和不成功的运行操作计量为操作执行。 逻辑应用不会计量以下操作： 

* 由于未满足条件而跳过的操作
* 由于逻辑应用在完成之前停止而未运行的操作

禁用的逻辑应用在禁用期间不会收取费用，因为它们无法创建新实例。

> [!NOTE]
> 禁用逻辑应用后，当前正在运行的实例可能需要在一段时间之后才会完全停止。

对于在循环内运行的操作，逻辑应用会对循环中每个周期的每个操作进行计数。 例如，假设有一个处理列表的“每个”循环。 逻辑应用通过将列表项的数量乘以循环中的操作数来计量该循环中的操作，并加上启动循环的操作。 10 个项列表的计算公式是 (10 * 1) + 1，即 11 个操作执行。

## <a name="integration-account-usage"></a>集成帐户使用情况

基于消耗量的使用情况适用于[集成帐户](logic-apps-enterprise-integration-create-integration-account.md)，可用于免费浏览、开发和测试逻辑应用中的 [B2B/EDI](logic-apps-enterprise-integration-b2b.md) 和 [XML 处理](logic-apps-enterprise-integration-xml.md)功能。 可为每个区域设置一个集成帐户。 每个集成帐户最多可存储特定[数量的项目](../logic-apps/logic-apps-limits-and-config.md)，其中包含贸易合作伙伴、协议、映射、架构、程序集、证书、批处理配置等。

逻辑应用还以支持的逻辑应用 SLA 提供基本和标准集成帐户。 如果只想处理消息，或充当与大型企业实体建立贸易合作关系的小型企业合作伙伴，可以使用基本集成帐户。 标准集成帐户支持更复杂的 B2B 关系，并增加了可以管理的实体数。 有关详细信息，请参阅 [Azure 定价](https://azure.microsoft.com/pricing/details/logic-apps)。

## <a name="next-steps"></a>后续步骤

* [了解有关逻辑应用的详细信息][whatis]
* [创建第一个逻辑应用][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: logic-apps-overview.md
[create]: quickstart-create-first-logic-app-workflow.md

