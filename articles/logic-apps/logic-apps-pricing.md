---
title: 定价 & 计费模式
description: 有关定价和计费模型如何适用于 Azure 逻辑应用的概述
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 07/19/2019
ms.openlocfilehash: 795acd67a8d4a9f8b8b7d78799a92134f249cf8d
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79270454"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Azure 逻辑应用的定价模型

[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)可帮助你创建和运行可在云中缩放的自动集成工作流。 本文介绍 Azure 逻辑应用的计费和定价工作方式。 有关定价费率，请参阅[逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps)。

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>消耗量定价模型

对于在公共或 "全局" Azure 逻辑应用服务中运行的新逻辑应用，只需为所使用的内容付费。 这些逻辑应用使用基于消耗量的计划和定价模型。 在逻辑应用中，每个步骤都是一个操作，Azure 逻辑应用会计量逻辑应用中运行的所有操作。

例如，操作包括：

* 触发器，是一种特殊的操作。 所有逻辑应用都需要触发器作为第一步。
* ["内置" 或本机操作](../connectors/apis-list.md#built-in)（如 HTTP、对 AZURE FUNCTIONS 和 API 管理的调用等）
* 调用[托管连接器](../connectors/apis-list.md#managed-connectors)，如 Outlook 365、Dropbox 等
* 控制流步骤，如循环、条件语句等

[标准连接器](../connectors/apis-list.md#managed-connectors)按[标准连接器价格](https://azure.microsoft.com/pricing/details/logic-apps)收费。 通常可用的[企业连接器](../connectors/apis-list.md#managed-connectors)按[企业连接器价格](https://azure.microsoft.com/pricing/details/logic-apps)收费，而公共预览版企业连接器按[标准连接器价格](https://azure.microsoft.com/pricing/details/logic-apps)收费。

详细了解如何对[触发器](#triggers)和[操作](#actions)进行计费。

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>固定定价模型

[*集成服务环境*（ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)为你创建和运行可访问 Azure 虚拟网络中的资源的逻辑应用提供隔离的方式。 对于在 ISE 中运行的新逻辑应用，你可以为这些功能支付[固定的每月价格](https://azure.microsoft.com/pricing/details/logic-apps)：

* [内置](../connectors/apis-list.md#built-in)触发器和操作

  在 ISE 中，内置触发器和操作显示 "**核心**" 标签，并在与逻辑应用相同的 ISE 中运行。

* [标准](../connectors/apis-list.md#managed-connectors)连接器和[企业](../connectors/apis-list.md#enterprise-connectors)连接器（尽可能多的企业连接）

   显示**ise**标签的标准和企业连接器与逻辑应用在同一 ISE 中运行。 不显示 ISE 标签的连接器在全局逻辑应用服务中运行。 固定的月度定价还适用于在使用在 ISE 中运行的逻辑应用时在全局服务中运行的连接器。

* 基于[ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)，无需额外付费即可使用[集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)：

  * **高级 SKU**：单一[标准层](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)集成帐户

  * **开发人员 SKU**：单一[免费层](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)集成帐户

  每个 ISE SKU 限制为5个集成帐户的总数。 为实现额外的成本，可以根据 ISE SKU 创建更多的集成帐户：

  * **高级 SKU**：最多4个标准帐户。 无免费帐户或基本帐户。

  * **开发人员 SKU**：最多4个标准帐户或最多5个标准帐户。 无基本帐户。

  有关集成帐户限制的详细信息，请参阅[逻辑应用限制和配置](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)。 可以在本主题的后面部分了解有关[集成帐户层及其定价模型](#integration-accounts)的详细信息。

如果选择高级 ISE SKU，基本单位具有固定容量。 如果需要更多的吞吐量，可以在创建期间或之后[添加更多缩放单位](../logic-apps/ise-manage-integration-service-environment.md#add-capacity)。 开发人员 ISE SKU 不能添加更多缩放单位。 在 ISE 中运行的逻辑应用不会产生数据保留成本。

有关定价费率，请参阅[逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps)。

<a name="connectors"></a>

## <a name="connectors"></a>连接器

Azure 逻辑应用连接器通过提供[触发器](#triggers)和/或[操作](#actions)来帮助逻辑应用访问云中或本地的应用、服务和系统。 连接器分类为标准或企业。 有关这些连接器的概述，请参阅[适用于 Azure 逻辑应用的连接器](../connectors/apis-list.md)。 如果你想要在逻辑应用中使用的 REST Api 没有预构建的连接器，则可以创建[自定义连接器](https://docs.microsoft.com/connectors/custom-connectors)，它们只是围绕这些 rest api 的包装。 自定义连接器按标准连接器计费。 以下各节提供了有关触发器和操作的计费方式的详细信息。

<a name="triggers"></a>

## <a name="triggers"></a>触发器

触发器是发生特定事件时创建逻辑应用实例的特殊操作。 触发器以不同方式起作用，从而影响逻辑应用的计量方式。 下面是 Azure 逻辑应用中存在的各种触发器：

* **轮询触发器**：此触发器持续检查终结点是否有满足创建逻辑应用实例和启动工作流的条件的消息。 即使没有创建逻辑应用实例，逻辑应用也会将每个轮询请求计量为执行。 若要指定轮询间隔，请通过逻辑应用程序设计器设置触发器。

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Webhook 触发器**：此触发器等待客户端将请求发送到特定终结点。 发送到 webhook 终结点的每个请求都会计为操作执行。 例如，请求和 HTTP Webhook 触发器都是 Webhook 触发器。

* **定期触发器**：此触发器根据你在触发器中设置的重复间隔创建逻辑应用实例。 例如，可以设置每隔三天或按更复杂的计划运行的定期触发器。

<a name="actions"></a>

## <a name="actions"></a>操作

作为本机操作的 Azure 逻辑应用计量 "内置" 操作，例如 HTTP。 例如，内置操作包括 HTTP 调用、来自 Azure Functions 或 API 管理的调用和控制流步骤，如条件、循环和 switch 语句。 每个操作都具有其自己的操作类型。 例如，调用[连接器](https://docs.microsoft.com/connectors)的操作的类型为 "ApiConnection"。 这些连接器分类为标准或企业连接器，根据各自的[定价](https://azure.microsoft.com/pricing/details/logic-apps)进行计费。 *预览版*中的企业连接器按标准连接器收费。

Azure 逻辑应用将所有成功和失败的操作作为执行进行计量。 但是，逻辑应用不会计量这些操作：

* 由于未满足条件而跳过的操作
* 由于逻辑应用在完成之前停止而未运行的操作

对于在循环内运行的操作，Azure 逻辑应用会对循环中每个循环的每个操作进行计数。 例如，假设有一个处理列表的“每个”循环。 逻辑应用通过将列表项的数量乘以循环中的操作数来计量该循环中的操作，并加上启动循环的操作。 因此，10项列表的计算结果为（10 * 1） + 1，这将导致执行11次操作。

## <a name="disabled-logic-apps"></a>已禁用逻辑应用

禁用的逻辑应用不收费，因为它们在禁用时无法创建新的实例。 禁用逻辑应用后，当前正在运行的实例可能需要在一段时间之后才会完全停止。

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>集成帐户

[固定定价模型](https://azure.microsoft.com/pricing/details/logic-apps)适用于[集成帐户](logic-apps-enterprise-integration-create-integration-account.md)，可在其中浏览、开发和测试 Azure 逻辑应用中的[B2B 和 EDI](logic-apps-enterprise-integration-b2b.md)和[XML 处理](logic-apps-enterprise-integration-xml.md)功能，无需额外付费。 每个 Azure 订阅最多可以有[特定的集成帐户限制](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)。 每个集成帐户最多可存储[项目](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)的特定限制，包括贸易合作伙伴、协议、地图、架构、程序集、证书、批配置等。

Azure 逻辑应用提供免费、基本和标准集成帐户。 逻辑应用服务级别协议（SLA）支持 "基本" 和 "标准" 级别，而 "免费" 级别不受 SLA 支持，并具有吞吐量和使用限制。 除了免费层集成帐户之外，每个 Azure 区域中可以有多个集成帐户。 有关定价费率，请参阅[逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps/)。

如果有[ *integration service 环境*（ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)（[高级或开发人员](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)），ISE 可以有5个集成帐户。 若要了解如何为 ISE 使用固定定价模型，请参阅本主题前面的[固定定价模型](#fixed-pricing)部分。 有关定价费率，请参阅[逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps)。

若要在免费、基本或标准集成帐户之间进行选择，请查看这些用例说明：

* **免费**：要尝试探索方案，而不是生产方案

* **基本**：仅当你只希望消息处理或充当与较大业务实体的贸易合作伙伴关系的小型企业合作伙伴时，

* **标准**：如果你具有更复杂的 B2B 关系并增加了你必须管理的实体数量，

<a name="data-retention"></a>

## <a name="data-retention"></a>数据保留

除了在 integration service 环境（ISE）中运行的逻辑应用，逻辑应用的运行历史记录中存储的所有输入和输出都基于逻辑应用的[运行保持期](logic-apps-limits-and-config.md#run-duration-retention-limits)进行计费。 在 ISE 中运行的逻辑应用不会产生数据保留成本。 有关定价费率，请参阅[逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps)。

为了帮助你监视逻辑应用的存储消耗，你可以：

* 查看逻辑应用每月使用的存储单元数（以 GB 为单位）。
* 查看逻辑应用的运行历史记录中特定操作的输入和输出的大小。

<a name="storage-consumption"></a>

### <a name="view-logic-app-storage-consumption"></a>查看逻辑应用存储消耗

1. 在 Azure 门户中，查找并打开逻辑应用。

1. 从逻辑应用的菜单的 "**监视**" 下，选择 "**指标**"。

1. 在右侧窗格中的 "**图表标题**" 下，从 "**指标**" 列表中，选择 "**计费使用情况" 执行存储消耗**。

   此指标提供按每月计费的存储消耗单位数。

<a name="input-output-sizes"></a>

### <a name="view-action-input-and-output-sizes"></a>查看操作输入和输出大小

1. 在 Azure 门户中，查找并打开逻辑应用。

1. 在逻辑应用的菜单上，选择 "**概述**"。

1. 在右侧窗格中的 "**运行历史记录**" 下，选择包含要检查的输入和输出的运行。

1. 在 "**逻辑应用运行**" 下，选择 "**运行详细信息**"。

1. 在 "**逻辑应用运行详细信息**" 窗格中，在 "操作" 表中列出每个操作的状态和持续时间，选择要查看的操作。

1. 在 "**逻辑应用操作**" 窗格中，查找该操作的输入和输出分别出现在 "**输入链接**" 和 "**输出" 链接**下的大小。

## <a name="next-steps"></a>后续步骤

* [了解有关 Azure 逻辑应用的详细信息](logic-apps-overview.md)
* [创建第一个逻辑应用](quickstart-create-first-logic-app-workflow.md)
