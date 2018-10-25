---
title: SaaS - 通过 Azure 销售 | Microsoft Docs
description: 介绍 SaaS 应用程序的订阅计费模型，以及如何实现该模型。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: pbutlerm
ms.openlocfilehash: f193138fbc5e779c3a6671757320d8918e08db46
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2018
ms.locfileid: "48805755"
---
<a name="saas---sell-through-azure"></a>SaaS - 通过 Azure 销售
=========================

本文介绍 SaaS 应用程序的订阅计费模型，以及如何在云合作伙伴门户中实现该模型。


<a name="overview"></a>概述
--------

可以通过 Azure 市场发布 SaaS 套餐并从中获得收益。 可以直接通过 Azure 的计费系统向客户收费。对于客户激活的所有资源和服务，客户只会收到 Azure 发送的一份帐单。 在发布方面，SaaS 订阅提供以下优势：

-   **一致的发布体验** -- 如果你在 Azure 市场中发布过 SaaS 套餐或其他任何套餐，发布门户中的发布体验是相同的。
-   **可发现的新店面** -- 所有已发布的套餐将显示在外部 Azure 市场店面中以及 Azure 门户内部的 Azure 市场扩展中。
-   **集成计费** -- 现在，可在 Azure 支持的所有区域中销售套餐，让 Azure 处理计费问题。
-   **集成的潜在顾客生成** -- 现在，每当 Azure 用户使用 Azure 市场订阅你的 SaaS 服务时，你可以自动从 Azure 中的所选 CRM 接收潜在顾客。
-   **与 Microsoft 经销商联合销售** -- 有资格享受双向潜在顾客共享和目录列表优先排名，并有机会与 Microsoft 卖家一同合作销售。

<a name="billing-models"></a>计费模型
--------------

目前，唯一支持的计费模型是按照 SaaS 服务的订阅收取每月固定费用。

**注意：** 将来可能支持其他计费模型。

每个 SaaS 套餐可以有一个或多个计划（例如“基本”或“高级”）。 每个计划有一些关联的基本元数据和关联的价格。

你可以完全控制计划的定义。 例如，基本计划由哪些部分构成？ 计划由你定义，在发布计划的过程中，可以提供所需的文本来描述它。

与计划关联的价格是 Azure 用户每月支付的固定服务使用费。

### <a name="what-is-not-supported-today"></a>目前不支持哪些计费模型？

-   基于自定义单位的计费 - 例如，基础请求数的固定价格。
-   基于席位分配的计费 - 例如，允许 Azure 用户根据用户数购买许可证。

<a name="end-to-end-flow"></a>端到端流
---------------

从最终用户立场出发的 SaaS 订阅套餐流

**注意：** 此流说明假设已在 Azure 市场中发布名为“Contoso 演示 SaaS”的 SaaS 套餐。

![SaaS 订阅用户流](media/saas-offer-subscription/saas-subscription-user-flow.png)

Azure 用户可与 SaaS 服务进行以下交互：

-   在 Azure 市场中发现 SaaS 服务
-   在 Azure 中订阅 SaaS 服务
-   从 Azure 门户导航到 SaaS 服务
-   在 SaaS 服务中创建一个帐户，并在 SaaS 中管理（更改计划/删除）该帐户
-   在 Azure 门户中取消订阅 SaaS 服务

<a name="discover-your-saas-service-in-azure-marketplace"></a>在 Azure 市场中发现 SaaS 服务
-----------------------------------------------

当用户在 Azure 门户中启动 Azure 市场时，会看到名为“软件即服务(SaaS)”的类别。

![使用类别菜单发现 SaaS](media/saas-offer-subscription/saas-category-menu.png)

用户还可以搜索 SaaS 服务。

![使用搜索功能发现 SaaS](media/saas-offer-subscription/saas-cpp-search.png)

用户可以查看服务的详细信息，然后单击“创建”。

![创建 SaaS 订阅](media/saas-offer-subscription/saas-create-subscription.png)

### <a name="subscribe-to-saas-service-in-azure"></a>在 Azure 中订阅 SaaS 服务

然后，用户可以从 Azure 订阅 SaaS 服务。

![订阅 SaaS 服务](media/saas-offer-subscription/saas-subscribe-signup.png)

当用户订阅 SaaS 服务时，需提供以下信息

-   名称 - 用户在 Azure 中发现或管理此 SaaS 订阅实例时可以使用的名称。
-   订阅 - 要与 SaaS 服务计费关联的订阅上下文。
-   计划 - 用户要订阅的 SaaS 服务计划。

在用户订阅 SaaS 服务之前，还会向其显示在发布套餐过程中提供的“使用条款”文档。

现在，用户可以单击“订阅”来订阅该服务。
订阅完成后，Azure 会在门户中发送通知。

### <a name="navigate-to-the-saas-service-from-azure-portal"></a>从 Azure 门户导航到 SaaS 服务

然后，用户单击“转到资源”以查看或管理其 SaaS 订阅实例。

![查看或管理 SaaS 实例](media/saas-offer-subscription/saas-go-to-resource.png)

通知用户必须先在 SaaS 服务中配置帐户。 SaaS 服务通知 Azure 开始计费后（当用户在 SaaS 服务站点上创建帐户时），计费将会开始。

![配置 SaaS 实例](media/saas-offer-subscription/saas-configure-account.png)

用户单击“配置帐户”时，会重定向到你的 SaaS 服务终结点。 在重定向期间，系统会连同查询参数一起传递令牌。 例如：

![SaaS 帐户令牌](media/saas-offer-subscription/saas-account-token.png)

请记下此令牌值。 此令牌的生存期较短，并需要解析才能在 Azure 中获取订阅标识符。

<a name="creating-a-saas-offer-subscription"></a>创建 SaaS 套餐订阅
----------------------------------

若要生成此体验，需要完成两项工作：

-   使用 Microsoft\' 的 SaaS API 连接 SaaS 服务网站。 文档[通过 Azure 销售 SaaS - API](./cloud-partner-portal-saas-subscription-apis.md) 介绍了如何建立此连接。  
-   在云合作伙伴门户的“技术信息”部分启用“通过 Azure 销售”，并提供所有配置详细信息。

![SaaS 新套餐技术信息](media/cpp-creating-saas-offers/saas-new-offer-technical-info-2.png)

下面是“技术信息”部分中所有必填字段的说明：

|  **套餐字段**                 |  **说明**                                   |
|  ----------------                 |  -------------------------------------             |
| 预览订阅 ID          | 在正式版推出之前，用于在预览版中测试套餐的所有 Azure 订阅标识符。 |
| 入门说明      | 有关与客户共享，以帮助他们连接到 SaaS 应用的指导。 允许使用基本 HTML，例如 `<p>`、`<h1>`、`<li>` 等标记。  |
| 登陆页 URL                  | 从 Azure 门户获取套餐后，将客户定向到的登陆站点 URL。 此 URL 也是接收连接 API 以方便与 Microsoft 开展商务活动的终结点。  |
| 连接 Webhook                | 对于 Microsoft 需要代表客户向你发送的所有异步事件（例如：Azure 订阅已失效），我们要求提供连接 Webhook。 如果你尚未部署 Webhook 系统，最简单的配置是使用一个 HTTP 终结点逻辑应用来侦听发布到该终结点的所有事件，并相应地进行处理。  有关详细信息，请参阅[在逻辑应用中使用 HTTP 终结点调用、触发或嵌套工作流](https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint)。 |
| Azure AD 租户 ID 和应用 ID     | 在 Azure 门户中，我们要求创建一个 Active Directory 应用，以便可以验证两个服务之间的连接是否属于经过身份验证的通信。 对于这些字段，请创建一个 AD 应用，并粘贴所需的相应租户 ID 和应用 ID。 |
|  |  |


最后，如果选择了“通过 Azure 销售”，则还会出现一个名为“计划”的附加部分。 仅当选择了“通过 Azure 销售”时，才需要指定计划。 此部分列出特定的计划，以及 SaaS 应用支持的相应价格。 到目前为止，我们允许按月定价，并允许 1 个月或 3 个月的免费访问。 这些计划和价格应与你自己的 SaaS 应用站点中的计划和价格完全匹配。
