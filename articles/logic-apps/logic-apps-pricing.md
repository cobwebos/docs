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
ms.date: 03/25/2019
ms.openlocfilehash: 7e1868dd5ce62c28c9a8aac724862c58a5e0e1da
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2019
ms.locfileid: "58805159"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Azure 逻辑应用的定价模型

[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)可帮助你创建并运行自动化的集成工作流可以扩展到云中。 本文介绍 Azure 逻辑应用的计费和定价的起作用。 有关具体的定价信息，请参阅[Azure 逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps)。

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>消耗量定价模型

对于在公共或"全局"Azure 逻辑应用服务中运行的新逻辑应用，你只需支付所使用的内容。 这些逻辑应用使用基于消耗量的计划和定价模型。 在逻辑应用定义中，每个步骤都是操作。 例如，操作包括： 

* 触发器是特殊操作。 所有逻辑应用都需要触发器作为第一步。
* "内置"或本机操作，例如 HTTP，对 Azure Functions 和 API 管理等的调用
* 对 Outlook 365、 Dropbox 等的连接器的调用
* 控制流步骤，例如循环、 条件语句等

Azure 逻辑应用会计量逻辑应用中运行的所有操作。 了解有关计费的工作原理的详细信息[触发器](#triggers)并[操作](#actions)。

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>固定定价模型

[*集成服务环境*(ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)提供了可创建并运行逻辑应用可以访问 Azure 虚拟网络中的资源的专用、 隔离和专用方法。 对于在 ISE 中运行的新逻辑应用，以及内置的操作和标准连接器支付固定月费。 在 ISE 还包括一个免费企业连接器，其中包括所需的任意多个连接。 使用情况的其他企业连接器基于企业消耗价格进行收费。 

在 ISE 基本单位具有固定容量，因此如果需要更大的吞吐量，你可以[添加更多缩放单位](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#add-capacity)，可以在创建期间或之后。

> [!NOTE]
> 在 ISE 处于[*公共预览版*](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 有关具体的定价信息，请参阅[Azure 逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps)。

<a name="connectors"></a>

## <a name="connectors"></a>连接器

Azure 逻辑应用连接器，从而帮助逻辑应用访问应用、 服务和在云中或本地系统[触发器](#triggers)，[操作](#actions)，和 / 或。 连接器分类为标准版或企业。 有关这些连接器的概述，请参阅[适用于 Azure 逻辑应用连接器](../connectors/apis-list.md)。 以下部分提供有关计费的触发方式的详细信息和操作工作。

<a name="triggers"></a>

## <a name="triggers"></a>触发器

触发器是发生特定事件时创建逻辑应用实例的特殊操作。 触发器以不同方式起作用，从而影响逻辑应用的计量方式。 以下是各种类型的 Azure 逻辑应用中存在的触发器：

* **轮询触发器**:此触发器持续检查满足创建逻辑应用实例和启动工作流的条件的消息的终结点。 即使没有创建逻辑应用实例，逻辑应用也会将每个轮询请求计量为执行。 若要指定轮询间隔，请通过逻辑应用程序设计器设置触发器。

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Webhook 触发器**:此触发器等待客户端将请求发送到特定终结点。 发送到 webhook 终结点的每个请求都会计为操作执行。 例如，请求和 HTTP Webhook 触发器都是 Webhook 触发器。

* 重复周期触发器：此触发器创建逻辑应用实例基于在触发器中设置的重复周期间隔。 例如，可以设置每三天运行一次的定期触发器或在更复杂的计划。

<a name="actions"></a>

## <a name="actions"></a>操作

Azure 逻辑应用计量为本机操作的"内置"操作，例如 HTTP。 例如，内置的操作包括 HTTP 调用，从 Azure Functions 或 API 管理中，调用并控制流步骤，例如条件、 循环，并切换语句。 每个操作都有其自己的操作类型。 例如，调用的操作[连接器](https://docs.microsoft.com/connectors)具有"ApiConnection"类型。 这些连接器分类为标准或企业连接器，我们会计量数根据各自[定价](https://azure.microsoft.com/pricing/details/logic-apps)。 在企业连接器*预览版*作为标准连接器收费。

Azure 逻辑应用计量为执行的所有成功和不成功的操作。 但是，逻辑应用不会计算这些操作：

* 由于未满足条件而跳过的操作
* 由于逻辑应用在完成之前停止而未运行的操作

对于在循环中运行的操作，Azure 逻辑应用对循环中每个周期的每个操作进行计数。 例如，假设有一个处理列表的“每个”循环。 逻辑应用通过将列表项的数量乘以循环中的操作数来计量该循环中的操作，并加上启动循环的操作。 因此，10 个项列表的计算是 (10 * 1) + 1，这会导致 11 个操作执行。

## <a name="disabled-logic-apps"></a>已禁用的逻辑应用

禁用的逻辑应用不收费，因为它们不能创建新实例，尽管它们处于禁用状态。
禁用逻辑应用后，当前正在运行的实例可能需要在一段时间之后才会完全停止。

## <a name="integration-accounts"></a>集成帐户

使用定价适用于[集成帐户](logic-apps-enterprise-integration-create-integration-account.md)可以在其中浏览、 开发和测试[B2B 和 EDI](logic-apps-enterprise-integration-b2b.md)并[XML 处理](logic-apps-enterprise-integration-xml.md)功能在 Azure 逻辑应用中的，无需额外付费。
您可以在每个 Azure 区域中有个集成帐户。 每个集成帐户最多可存储特定[数量的项目](../logic-apps/logic-apps-limits-and-config.md)，其中包含贸易合作伙伴、协议、映射、架构、程序集、证书、批处理配置等。

Azure 逻辑应用还提供了与受支持的逻辑应用 SLA 的基本和标准集成帐户。 以下是您可以选择是否要使用基本或标准集成帐户的方式：

* 您只是想要处理消息或充当具有更大的业务实体与贸易合作伙伴关系的小型企业合作伙伴，请使用基本集成帐户。

* 如果有更复杂的 B2B 关系并想要提高可管理的实体数，请使用标准集成帐户。

有关具体的定价信息，请参阅[Azure 逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps)。

<a name="data-retention"></a>

## <a name="data-retention"></a>数据保留

所有输入和输出存储在逻辑应用的运行历史记录中获取基于逻辑应用的都计费[运行期](logic-apps-limits-and-config.md#run-duration-retention-limits)。 有关具体的定价信息，请参阅[Azure 逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps)。

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