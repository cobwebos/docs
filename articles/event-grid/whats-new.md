---
title: 新增功能 发行说明 - Azure 事件网格
description: 了解 Azure 事件网格的最新信息，例如最新的发行说明、已知问题、bug 修复、已弃用的功能和即将应用的更改。
ms.topic: overview
ms.date: 07/23/2020
ms.openlocfilehash: 1edfa3e2bc4c8adae113b2215b7fb0483fba4c02
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2020
ms.locfileid: "87172681"
---
# <a name="whats-new-in-azure-event-grid"></a>Azure 事件网格的最新信息

>通过复制此 URL (`https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Event+Grid%22&locale=en-us`) 并粘贴到 ![RSS 源阅读器图标](./media/whats-new/feed-icon-16x16.png) 订阅源阅读器中，获取有关何时重新访问此页以获得更新的通知。

Azure 事件网格会不断改进。 为了让大家随时了解最新的开发成果，本文将提供以下方面的信息：

- 最新版本
- 已知问题
- Bug 修复
- 已弃用的功能
- 更改计划

## <a name="600-2020-06"></a>6.0.0 (2020-06)
- 添加对新的正式发布 (GA) 服务 API 版本 2020-06-01 的支持。
- 已正式发布的新功能包括：
    - [输入映射](input-mappings.md)
    - [自定义输入架构](input-mappings.md)
    - [云事件 V10 架构](cloud-event-schema.md)
    - [将服务总线主题作为目标](handler-service-bus.md)
    - [将 Azure 函数作为目标](handler-functions.md)
    - [Webhook 批处理](./edge/delivery-output-batching.md)
    - [安全 Webhook（Azure Active Directory 支持）](secure-webhook-delivery.md)
    - [IP 筛选](configure-firewall.md)
    - [专用链接服务支持](configure-private-endpoints.md)
    - [事件传递架构](event-schema.md)

## <a name="532-preview-2020-05"></a>5.3.2-preview (2020-05)
- 此版本包含旨在提高质量的新增 bug 修补程序。
- 此版本是版本 5.3.1-preview，与 2020-04-01-Preview API 版本相对应，其中包括以下新功能： 
    - [将事件发布到域和主题时支持 IP 筛选](configure-firewall.md)
    - [合作伙伴主题](partner-topics-overview.md)
    - [在 Azure 门户中将系统主题作为受跟踪的资源](system-topics.md)
    - [使用托管服务标识进行事件传送](managed-service-identity.md) 
    - [专用链接服务支持](configure-private-endpoints.md)

## <a name="531-preview-2020-04"></a>5.3.1-preview (2020-04)
- 此版本包含旨在提高质量的多种 bug 修补程序。
- 此版本是版本 5.3.0-preview，与 2020-04-01-Preview API 版本相对应，其中包括以下新功能： 
    - [将事件发布到域和主题时支持 IP 筛选](configure-firewall.md)
    - [合作伙伴主题](partner-topics-overview.md)
    - [在 Azure 门户中将系统主题作为受跟踪的资源](system-topics.md)
    - [使用托管服务标识进行事件传送](managed-service-identity.md) 
    - [专用链接服务支持](configure-private-endpoints.md)

## <a name="530-preview-2020-03"></a>5.3.0-preview (2020-03)
- 我们在版本 5.2.0-preview 中已添加的功能基础之上，还引入了一些新功能。 
- 此版本是版本 5.2.0-preview，与 2020-04-01-Preview API 版本相对应。
- 它添加了对以下新功能的支持： 
    - [将事件发布到域和主题时支持 IP 筛选](configure-firewall.md)
    - [合作伙伴主题](partner-topics-overview.md)
    - [在 Azure 门户中将系统主题作为受跟踪的资源](system-topics.md)
    - [使用托管服务标识进行事件传送](managed-service-identity.md) 
    - [专用链接服务支持](configure-private-endpoints.md)

## <a name="520-preview-2020-01"></a>5.2.0-preview (2020-01)
- 此版本与 2020-04-01-Preview API 版本相对应。
- 它添加了对以下新功能的支持：
    - [将事件发布到域和主题时支持 IP 筛选](configure-firewall.md)

## <a name="500-2019-05"></a>5.0.0 (2019-05)
- 此版本与 `2019-06-01` API 版本相对应。
- 它添加了对以下新功能的支持：
    * [域](event-domains.md)
    * 用于资源列表操作的分页和搜索筛选器。 有关示例，请参阅[主题 - 按订阅列出](/rest/api/eventgrid/version2020-04-01-preview/topics/listbysubscription)。
    * [将服务总线队列作为目标](handler-service-bus.md)
    * [高级筛选](event-filtering.md#advanced-filtering)

## <a name="410-preview-2019-03"></a>4.1.0-preview (2019-03)
- 此版本与 2019-02-01-preview API 版本相对应。
- 它添加了对以下新功能的支持：
    * 用于资源列表操作的分页和搜索筛选器。 有关示例，请参阅[主题 - 按订阅列出](/rest/api/eventgrid/version2020-04-01-preview/topics/listbysubscription)。
    * [手动创建/删除域主题](how-to-event-domains.md)
    * [将服务总线队列作为目标](handler-service-bus.md)

## <a name="400-2018-12"></a>4.0.0 (2018-12)
- 此版本与 `2019-01-01` API 稳定版本相对应。
- 它支持对以下与事件订阅相关的功能的正式发布 (GA)：
    * [死信目标](manage-event-delivery.md)
    * [将 Azure 存储队列作为目标](handler-storage-queues.md)
    * [Azure 中继 - 将混合连接作为目标](handler-relay-hybrid-connections.md)
    * [手动握手验证](webhook-event-delivery.md)
    * [支持重试策略](delivery-and-retry.md)
- 对于仍处于预览阶段的功能（如[事件网格域](event-domains.md)或[高级筛选器支持](event-filtering.md#advanced-filtering)），仍可使用该 SDK 的 3.0.1-preview 版本进行访问。

## <a name="301-preview-2018-10"></a>3.0.1-preview (2018-10)
- 依赖于 [Newtonsoft NuGet 包 10.0.3 版本](https://www.nuget.org/packages/Newtonsoft.Json/10.0.3)。

## <a name="300-preview-2018-10"></a>3.0.0-preview (2018-10)
- 此版本是 SDK 预览版，适用于 2018-09-15-preview API 版本引入的新功能。 - 此版本提供以下支持：
    - [域和域主题](event-domains.md)
    - 引入[事件订阅到期日期](concepts.md#event-subscription-expiration)
    - 引入对事件订阅的[高级筛选](event-filtering.md#advanced-filtering)
    - 面向 `2018-01-01` API 版本的 SDK 稳定版本作为版本 1.3.0 继续存在

## <a name="next-steps"></a>后续步骤
