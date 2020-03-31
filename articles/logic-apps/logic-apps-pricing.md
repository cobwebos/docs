---
title: 定价&计费模型
description: 定价和计费模型如何适用于 Azure 逻辑应用的概述
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 07/19/2019
ms.openlocfilehash: 795acd67a8d4a9f8b8b7d78799a92134f249cf8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270454"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Azure 逻辑应用的定价模型

借助 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)可以创建和运行可在云中缩放的自动化集成工作流。 本文介绍 Azure 逻辑应用的计费和定价方式。 有关定价率，请参阅[逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps)。

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>消耗量定价模型

对于在公共或“全球”Azure 逻辑应用服务中运行的新逻辑应用，只需根据实际使用的资源付费。 这些逻辑应用使用基于消耗量的计划和定价模型。 在逻辑应用中，每个步骤都是一个操作，Azure 逻辑应用会管理逻辑应用中运行的所有操作。

例如，操作包括：

* 触发器（特殊的操作）。 所有逻辑应用需将一个触发器用作第一个步骤。
* [“内置”或本机操作](../connectors/apis-list.md#built-in)，例如 HTTP、对 Azure Functions 和 API 管理的调用，等等
* 对[托管连接器](../connectors/apis-list.md#managed-connectors)（如 Outlook 365、Dropbox 等）的调用
* 控制流步骤，例如循环、条件语句，等等

[标准连接器](../connectors/apis-list.md#managed-connectors)按[标准连接器价格](https://azure.microsoft.com/pricing/details/logic-apps)收费。 一般可用的[企业连接器](../connectors/apis-list.md#managed-connectors)按[企业连接器价格](https://azure.microsoft.com/pricing/details/logic-apps)收费，而公共预览企业连接器按[标准连接器价格](https://azure.microsoft.com/pricing/details/logic-apps)收费。

详细了解[触发器](#triggers)和[操作](#actions)的计费方式。

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>固定定价模型

[*集成服务环境*（ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)提供了一种独立的方式，用于创建和运行可以访问 Azure 虚拟网络中资源的逻辑应用。 对于在 ISE 内运行的新逻辑应用，您每月为这些功能支付[固定价格](https://azure.microsoft.com/pricing/details/logic-apps)：

* [内置](../connectors/apis-list.md#built-in)触发器和操作

  在 ISE 中，内置触发器和操作显示**核心**标签，并在与逻辑应用相同的 ISE 中运行。

* [标准](../connectors/apis-list.md#managed-connectors)连接器[和企业](../connectors/apis-list.md#enterprise-connectors)连接器（根据需要连接企业数）

   显示**ISE**标签的标准和企业连接器与逻辑应用在相同的 ISE 中运行。 不显示 ISE 标签的连接器在全局逻辑应用服务中运行。 固定每月定价也适用于在全局服务中运行的连接器，当您将其与在 ISE 中运行的逻辑应用一起运行时。

* 基于[ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)的[集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)使用情况，无需额外费用：

  * **高级 SKU**：单个[标准层](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)集成帐户

  * **开发人员 SKU**： 单个[免费套餐](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)集成帐户

  每个 ISE SKU 限制为 5 个总集成帐户。 对于额外的成本，您可以基于 ISE SKU 拥有更多的集成帐户：

  * **高级 SKU**： 最多四个标准帐户。 无免费或基本帐户。

  * **开发人员 SKU**： 最多 4 个标准帐户，或最多 5 个标准帐户。 无基本帐户。

  有关集成帐户限制的详细信息，请参阅[逻辑应用限制和配置](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)。 在本主题稍后部分，您可以了解有关[集成帐户层及其定价模型](#integration-accounts)的详细信息。

如果选择高级 ISE SKU，则基本单位具有固定容量。 如果需要更多吞吐量，可以在创建期间或之后[添加更多比例单位](../logic-apps/ise-manage-integration-service-environment.md#add-capacity)。 开发人员 ISE SKU 无法添加更多缩放单位。 在 ISE 中运行的逻辑应用不会产生数据保留成本。

有关定价费率，请参阅[逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps)。

<a name="connectors"></a>

## <a name="connectors"></a>连接器

Azure 逻辑应用连接器通过提供[触发器](#triggers)和/或[操作](#actions)，帮助逻辑应用访问云中或本地的应用、服务和系统。 连接器分类为“标准”或“企业”连接器。 有关这些连接器的概述，请参阅[适用于 Azure 逻辑应用的连接器](../connectors/apis-list.md)。 如果没有预生成的连接器可用于要在逻辑应用中使用的 REST API，则可以创建[自定义连接器](https://docs.microsoft.com/connectors/custom-connectors)，这些连接器只是这些 REST API 的包装器。 自定义连接器按标准连接器计费。 以下部分提供有关触发器和操作的计费方式的详细信息。

<a name="triggers"></a>

## <a name="triggers"></a>触发器

触发器是发生特定事件时创建逻辑应用实例的特殊操作。 触发器以不同方式起作用，从而影响逻辑应用的计量方式。 下面是 Azure 逻辑应用中存在的各种触发器：

* **轮询触发器**：此触发器会持续检查终结点中满足创建逻辑应用实例和启动工作流条件的消息。 即使没有创建逻辑应用实例，逻辑应用也会将每个轮询请求计量为执行。 若要指定轮询间隔，请通过逻辑应用程序设计器设置触发器。

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Webhook 触发器**：此触发器等待客户端向特定终结点发送请求。 发送到 webhook 终结点的每个请求都会计为操作执行。 例如，请求和 HTTP Webhook 触发器都是 Webhook 触发器。

* **定期触发器**：此触发器基于您在触发器中设置的定期间隔创建逻辑应用实例。 例如，可以设置每隔三天运行的，或者根据更复杂的计划运行的定期触发器。

<a name="actions"></a>

## <a name="actions"></a>操作

Azure 逻辑应用将 HTTP 等“内置”操作作为本机操作进行计量。 例如，内置操作包括 HTTP 调用、来自 Azure Functions 或 API 管理的调用，以及条件、循环和开关语句等控制流步骤。 每个操作具有自身的操作类型。 例如，调用[连接器](https://docs.microsoft.com/connectors)的操作为“ApiConnection”类型。 这些连接器被归类为标准或企业连接器，根据各自的[定价](https://azure.microsoft.com/pricing/details/logic-apps)进行计量。 *预览*中的企业连接器作为标准连接器充电。

Azure 逻辑应用将所有成功和不成功的操作作为执行进行计量。 但是，逻辑应用不会计量以下操作：

* 由于未满足条件而跳过的操作
* 由于逻辑应用在完成之前停止而未运行的操作

对于在循环内部运行的操作，Azure 逻辑应用会对循环中每个周期的每个操作进行计数。 例如，假设有一个处理列表的“每个”循环。 逻辑应用通过将列表项的数量乘以循环中的操作数来计量该循环中的操作，并加上启动循环的操作。 因此，包含 10 个项的列表的计算公式为 (10 * 1) + 1，即 11 个操作执行。

## <a name="disabled-logic-apps"></a>禁用的逻辑应用

禁用的逻辑应用在禁用期间不会产生费用，因为它们无法创建新实例。 禁用逻辑应用后，当前正在运行的实例可能需要在一段时间之后才会完全停止。

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>集成帐户

[固定定价模型](https://azure.microsoft.com/pricing/details/logic-apps)适用于[集成帐户](logic-apps-enterprise-integration-create-integration-account.md)，您可以在其中探索、开发和测试 Azure 逻辑应用中的[B2B 和 EDI](logic-apps-enterprise-integration-b2b.md)和[XML 处理](logic-apps-enterprise-integration-xml.md)功能，无需额外付费。 每个 Azure 订阅可以具有最多特定的[集成帐户限制](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)。 每个集成帐户可以存储多达特定[工件限制](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)，包括贸易伙伴、协议、地图、架构、程序集、证书、批处理配置等。

Azure 逻辑应用提供免费、基本和标准集成帐户。 逻辑应用服务级别协议 （SLA） 支持基本层和标准层，而免费层不受 SLA 支持，并且吞吐量和使用情况有限制。 除免费层集成帐户外，每个 Azure 区域中可以有多个集成帐户。 有关定价费率，请参阅[逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps/)。

如果您有[*集成服务环境*（ISE），](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)[高级或开发人员](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)， 您的 ISE 可以有 5 个总集成帐户。 要了解固定定价模型如何为 ISE 工作，请参阅本主题中的上一个[固定定价模型](#fixed-pricing)部分。 有关定价费率，请参阅[逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps)。

要选择免费、基本或标准集成帐户，请查看这些用例说明：

* **免费**：对于要尝试探索性方案，而不是生产方案

* **基本**：对于您只希望处理消息或充当与较大业务实体有贸易伙伴关系的小业务合作伙伴时

* **标准**：对于具有更复杂的 B2B 关系和必须管理的实体数量增加时

<a name="data-retention"></a>

## <a name="data-retention"></a>数据保留

除了在集成服务环境 （ISE） 中运行的逻辑应用外，存储在逻辑应用运行历史记录中的所有输入和输出都根据逻辑应用[的运行保留期](logic-apps-limits-and-config.md#run-duration-retention-limits)计费。 在 ISE 中运行的逻辑应用不会产生数据保留成本。 有关定价费率，请参阅[逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps)。

为了帮助监视逻辑应用的存储消耗，您可以：

* 查看逻辑应用每月使用的 GB 存储单元数。
* 在逻辑应用的运行历史记录中查看特定操作的输入和输出的大小。

<a name="storage-consumption"></a>

### <a name="view-logic-app-storage-consumption"></a>查看逻辑应用存储消耗

1. 在 Azure 门户中，查找并打开逻辑应用。

1. 从逻辑应用的菜单中，在 **"监视"** 下，选择**指标**。

1. 在右侧窗格中，在"**指标"** 列表中的**图表标题**下，选择**存储消耗量执行的计费使用情况**。

   此指标为您提供了每月按 GB 计费的存储消耗单位数。

<a name="input-output-sizes"></a>

### <a name="view-action-input-and-output-sizes"></a>查看操作输入和输出大小

1. 在 Azure 门户中，查找并打开逻辑应用。

1. 在逻辑应用的菜单中，选择“概述”****。

1. 在右侧窗格的“运行历史记录”**** 下，选择具有要检查的输入和输出的运行。

1. 在“逻辑应用运行”**** 下，选择“运行详细信息”****。

1. 在“逻辑应用运行详细信息”**** 窗格的“操作表”中（其中列出了每个操作的状态和持续时间），选择要查看的操作。

1. 在“逻辑应用操作”**** 窗格中，找到该操作的输入和输出的大小（分别显示在“输入链接”**** 和“输出链接”**** 下）。

## <a name="next-steps"></a>后续步骤

* [了解有关 Azure 逻辑应用的更多信息](logic-apps-overview.md)
* [创建第一个逻辑应用](quickstart-create-first-logic-app-workflow.md)
