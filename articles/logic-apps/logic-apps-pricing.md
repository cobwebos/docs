---
title: 定价和计费 - Azure 逻辑应用 | Microsoft Docs
description: 了解 Azure 逻辑应用的定价和计费原理
services: logic-apps
ms.service: logic-apps
ms.suite: logic-apps
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.assetid: f8f528f5-51c5-4006-b571-54ef74532f32
ms.topic: article
ms.date: 05/22/2019
ms.openlocfilehash: 20e84482b31c4da991f3fdc9c7bbf6ee0e7f902a
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299105"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Azure 逻辑应用的定价模型

借助 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)可以创建和运行可在云中缩放的自动化集成工作流。 本文介绍 Azure 逻辑应用的计费和定价方式。 有关具体的定价信息，请参阅 [Azure 逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps)。

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>消耗量定价模型

对于在公共或“全球”Azure 逻辑应用服务中运行的新逻辑应用，只需根据实际使用的资源付费。 这些逻辑应用使用基于消耗量的计划和定价模型。 在逻辑应用定义中，每个步骤都是操作。 例如，操作包括：

* 触发器（特殊的操作）。 所有逻辑应用需将一个触发器用作第一个步骤。
* “内置”或本机操作，例如 HTTP、对 Azure Functions 和 API 管理的调用，等等
* 对 Outlook 365、Dropbox 等连接器的调用
* 控制流步骤，例如循环、条件语句，等等

Azure 逻辑应用对逻辑应用中运行的所有操作进行计量。 详细了解[触发器](#triggers)和[操作](#actions)的计费方式。

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>固定定价模型

[*集成服务环境*(ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)提供了可创建并运行逻辑应用可以访问 Azure 虚拟网络中的资源的专用、 隔离和专用方法。 对于在 ISE 中运行的新逻辑应用，你需付费[固定每月价格](https://azure.microsoft.com/pricing/details/logic-apps)内置操作和触发器以及标准连接器。

在 ISE 还包括一个免费企业连接器，其中包括作为许多*连接*根据需要。 其他企业连接器的使用情况的基础上收费[企业消耗价格](https://azure.microsoft.com/pricing/details/logic-apps)。 通常仅提供企业连接器是企业消耗价格收费。 公共预览版企业连接器进行收费[标准连接器速率](https://azure.microsoft.com/pricing/details/logic-apps)。

> [!NOTE]
> 在 ISE 中，内置的触发器和操作显示**Core**标记和在作为逻辑应用相同的 ISE 中运行。 标准版和企业版连接器的显示**ISE**标签在作为逻辑应用相同的 ISE 中运行。 不显示在全局的逻辑应用服务中运行的 ISE 标签的连接器。

在 ISE 基本单位具有固定容量，因此如果需要更大的吞吐量，你可以[添加更多缩放单位](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#add-capacity)，可以在创建期间或之后。 在 ISE 中运行的逻辑应用不会产生数据保留成本。

有关具体的定价信息，请参阅 [Azure 逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps)。

<a name="connectors"></a>

## <a name="connectors"></a>连接器

Azure 逻辑应用连接器通过提供[触发器](#triggers)和/或[操作](#actions)，帮助逻辑应用访问云中或本地的应用、服务和系统。 连接器分类为“标准”或“企业”连接器。 有关这些连接器的概述，请参阅[适用于 Azure 逻辑应用的连接器](../connectors/apis-list.md)。 以下部分提供有关触发器和操作的计费方式的详细信息。

<a name="triggers"></a>

## <a name="triggers"></a>触发器

触发器是发生特定事件时创建逻辑应用实例的特殊操作。 触发器以不同方式起作用，从而影响逻辑应用的计量方式。 下面是 Azure 逻辑应用中存在的各种触发器：

* **轮询触发器**：此触发器持续检查终结点是否收到满足创建逻辑应用实例和启动工作流的条件的消息。 即使没有创建逻辑应用实例，逻辑应用也会将每个轮询请求计量为执行。 若要指定轮询间隔，请通过逻辑应用程序设计器设置触发器。

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Webhook 触发器**：此触发器等待客户端向特定的终结点发送请求。 发送到 webhook 终结点的每个请求都会计为操作执行。 例如，请求和 HTTP Webhook 触发器都是 Webhook 触发器。

* 重复周期触发器：  此触发器基于在触发器中设置的重复间隔创建逻辑应用实例。 例如，可以设置每隔三天运行的，或者根据更复杂的计划运行的定期触发器。

<a name="actions"></a>

## <a name="actions"></a>操作

Azure 逻辑应用将 HTTP 等“内置”操作作为本机操作进行计量。 例如，内置操作包括 HTTP 调用、来自 Azure Functions 或 API 管理的调用，以及条件、循环和开关语句等控制流步骤。 每个操作具有自身的操作类型。 例如，调用[连接器](https://docs.microsoft.com/connectors)的操作为“ApiConnection”类型。 这些连接器分类为“标准”或“企业”连接器，根据各自的[定价](https://azure.microsoft.com/pricing/details/logic-apps)进行计量。 预览版的企业连接器按标准连接器计费。 

Azure 逻辑应用将所有成功和不成功的操作作为执行进行计量。 但是，逻辑应用不会计量以下操作：

* 由于未满足条件而跳过的操作
* 由于逻辑应用在完成之前停止而未运行的操作

对于在循环内部运行的操作，Azure 逻辑应用会对循环中每个周期的每个操作进行计数。 例如，假设有一个处理列表的“每个”循环。 逻辑应用通过将列表项的数量乘以循环中的操作数来计量该循环中的操作，并加上启动循环的操作。 因此，包含 10 个项的列表的计算公式为 (10 * 1) + 1，即 11 个操作执行。

## <a name="disabled-logic-apps"></a>禁用的逻辑应用

禁用的逻辑应用在禁用期间不会产生费用，因为它们无法创建新实例。
禁用逻辑应用后，当前正在运行的实例可能需要在一段时间之后才会完全停止。

## <a name="integration-accounts"></a>集成帐户

使用定价适用于[集成帐户](logic-apps-enterprise-integration-create-integration-account.md)可以在其中浏览、 开发和测试[B2B 和 EDI](logic-apps-enterprise-integration-b2b.md)并[XML 处理](logic-apps-enterprise-integration-xml.md)功能在 Azure 逻辑应用中的，无需额外付费。
您可以在每个 Azure 区域中有个集成帐户。 每个集成帐户最多可存储特定[数量的项目](../logic-apps/logic-apps-limits-and-config.md)，其中包含贸易合作伙伴、协议、映射、架构、程序集、证书、批处理配置等。

Azure 逻辑应用提供了免费、 基本和标准集成帐户。 基本和标准层支持的逻辑应用的服务级别协议 (SLA)，而免费层不支持的 sla 保证，而有限制对吞吐量和使用情况。

若要免费、 基本或标准的集成帐户之间进行选择：

* **免费**：有关当你想要尝试探索性的情况下，不是生产方案。

* **基本**：所需唯一消息处理的或者要用作小型企业合作伙伴具有更大的业务实体与贸易合作伙伴关系。

* **标准**：对于后更复杂的 B2B 关系和更高的数量的实体，你必须管理。

有关具体的定价信息，请参阅[Azure 逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps)。

<a name="data-retention"></a>

## <a name="data-retention"></a>数据保留

逻辑应用除外，在集成服务环境 (ISE) 中运行，所有输入和输出存储在逻辑应用的运行历史记录中的都计费基于逻辑应用的[运行期](logic-apps-limits-and-config.md#run-duration-retention-limits)。 在 ISE 中运行的逻辑应用不会产生数据保留成本。 有关具体的定价信息，请参阅[Azure 逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps)。

若要帮助您监视逻辑应用的存储消耗情况，您可以：

* 在逻辑应用将使用每月 GB 中查看存储单元数。
* 在逻辑应用的运行历史记录中查看特定操作的输入和输出的大小。

<a name="storage-consumption"></a>

### <a name="view-logic-app-storage-consumption"></a>查看逻辑应用存储使用情况

1. 在 Azure 门户中，查找并打开逻辑应用。

1. 从逻辑应用的菜单下**监视**，选择**指标**。

1. 在右侧窗格中下,**图表标题**，从**指标**列表中，选择**计费使用情况的存储消耗执行**。

   此指标可存储消耗的单位数以 gb 为单位每月获取计费。

<a name="input-output-sizes"></a>

### <a name="view-action-input-and-output-sizes"></a>查看操作输入和输出大小

1. 在 Azure 门户中，查找并打开逻辑应用。

1. 在逻辑应用的菜单中选择**概述**。

1. 在右侧窗格中下,**运行历史记录**，选择有输入和输出你想要检查的运行。

1. 下**逻辑应用运行**，选择**运行详细信息**。

1. 在中**逻辑应用运行详细信息**窗格中，在操作表中，其中列出了每个操作的状态和持续时间，选择你想要查看的操作。

1. 在中**逻辑应用操作**窗格中，查找该操作的输入和输出的大小会分别下显示**输入链接**并**输出链接**。

## <a name="next-steps"></a>后续步骤

* [了解有关 Azure 逻辑应用的详细信息](logic-apps-overview.md)
* [创建第一个逻辑应用](quickstart-create-first-logic-app-workflow.md)