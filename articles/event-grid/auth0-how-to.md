---
title: 如何使用 Azure 事件网格将事件从 Auth0 发送到 Azure
description: 如何使用 Azure 事件网格将事件从 Auth0 发送到 Azure 服务。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 6cd440075c6400b58c23f879b53da26abe4f9e75
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86103273"
---
# <a name="integrate-azure-event-grid-with-auth0"></a>将 Azure 事件网格与 Auth0 集成

本文介绍了如何通过创建事件网格合作伙伴主题来连接 Auth0 和 Azure 帐户。

有关 Auth0 支持的事件的完整列表，请参阅 [Auth0 事件类型代码](https://auth0.com/docs/logs/references/log-event-type-codes)

## <a name="send-events-from-auth0-to-azure-event-grid"></a>将事件从 Auth0 发送到 Azure 事件网格
若要将 Auth0 事件发送到 Azure，请执行以下操作：

1. 启用事件网格资源提供程序
1. 在 Auth0 仪表板中设置 Auth0 合作伙伴主题
1. 在 Azure 中激活合作伙伴主题
1. 订阅来自 Auth0 的事件

有关这些概念的详细信息，请参阅事件网格的[概念](concepts.md)。

### <a name="enable-event-grid-resource-provider"></a>启用事件网格资源提供程序
除非你以前用过事件网格，否则你需要注册事件网格资源提供程序。 如果你以前用过事件网格，请转到下一部分。

在 Azure 门户中：
1. 在左侧菜单中选择“订阅”
1. 选择要用于事件网格的订阅
1. 在左侧菜单中，在“设置”下，选择“资源提供程序”
1. 找到 Microsoft.EventGrid
1. 选择“注册”
1. 刷新页面，确保状态更改为“已注册”

### <a name="set-up-an-auth0-partner-topic"></a>设置 Auth0 合作伙伴主题
集成过程的一部分是将 Auth0 设置为事件源（此步骤是在 [Auth0 仪表板](https://manage.auth0.com/)中进行的）。

1. 登录到 [Auth0 仪表板](https://manage.auth0.com/)。
1. 导航到“日志”>“流”。
1. 单击“+ 创建流”。
1. 选择“Azure 事件网格”，然后为新流输入一个唯一名称。
1. 通过提供 Azure 订阅 ID、Azure 区域和资源组名称来创建事件源。 
1. 单击“保存”。

### <a name="activate-your-auth0-partner-topic-in-azure"></a>在 Azure 中激活 Auth0 合作伙伴主题
在 Azure 中激活 Auth0 主题可使事件从 Auth0 流向 Azure。

1. 登录到 Azure 门户。
1. 在顶部搜索 `Partner Topics`，然后在“服务”下单击 `Event Grid Partner Topics`。
1. 单击与你在 Auth0 仪表板中创建的流匹配的主题。
1. 确认 `Source` 字段与你的 Auth0 帐户匹配。
1. 单击“激活”。

### <a name="subscribe-to-auth0-events"></a>订阅 Auth0 事件

#### <a name="create-an-event-handler"></a>创建事件处理程序
若要测试合作伙伴主题，需要一个事件处理程序。 转到你的 Azure 订阅，并启动一个支持用作[事件处理程序](event-handlers.md)的服务（例如 [Azure Function](custom-event-to-function.md)）。

#### <a name="subscribe-to-your-auth0-partner-topic"></a>订阅 Auth0 合作伙伴主题
订阅 Auth0 合作伙伴主题可以告知事件网格你要将 Auth0 事件传送到何处。

1. 在你的 Auth0 集成的“合作伙伴主题”边栏选项卡上，选择顶部的“+ 事件订阅”。
1. 在“创建事件订阅”页面上：
    1. 为事件订阅输入一个名称。
    1. 为“终结点类型”选择你创建的 Azure 服务或 WebHook。
    1. 按照适用于特定服务的说明进行操作。
    1. 单击“创建”。

## <a name="testing"></a>测试
现在，Auth0 合作伙伴主题与 Azure 的集成应当已可供使用。

### <a name="verify-the-integration"></a>验证集成
若要验证该集成是否按预期方式工作，请执行以下操作：

1. 登录到 Auth0 仪表板。
1. 导航到“日志”>“流”。
1. 单击你的事件网格流。
1. 在该流中，单击“运行状况”选项卡。该流应处于活动状态，只要未看到任何错误，就表示该流正常运行。

尝试[调用可触发待发布事件的任何 Auth0 操作](https://auth0.com/docs/logs/references/log-event-type-codes)，以查看事件流。

## <a name="delivery-attempts-and-retries"></a>传送尝试和重试
Auth0 事件将通过流式传输机制传送到 Azure。 在 Auth0 中触发每个事件时，将发送该事件。 如果事件网格无法接收事件，Auth0 将最多三次重试传送事件。 如果重试失败，Auth0 将在其系统中记录“传送失败”。

## <a name="next-steps"></a>后续步骤

- [Auth0 合作伙伴主题](auth0-overview.md)
- [合作伙伴主题概述](partner-topics-overview.md)
- [成为事件网格合作伙伴](partner-onboarding-overview.md)