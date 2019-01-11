---
title: Azure SaaS 应用程序产品/服务技术配置 |Microsoft Docs
description: 在 Azure 市场上为 SaaS 应用程序产品/服务配置技术信息。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: da67213b708b3ff26fe1ddd926c5cb0bba33458d
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/27/2018
ms.locfileid: "53788542"
---
# <a name="saas-application-technical-info-tab"></a>SaaS 应用程序“技术信息”选项卡

“技术信息”选项卡提供了“技术配置”窗体。 使用此窗体选取你要创建的 SaaS 应用程序（应用）的类型，并配置如何将你的应用提供给客户。

![“技术配置”窗体](./media/saas-techinfo-techconfig.png)

## <a name="technical-configuration-form"></a>“技术配置”窗体

此窗体具有 2 个字段：“产品”和“行动号召”。

### <a name="product-field"></a>“产品”字段

可以为以下两种店面提供 SaaS 应用：
- 对于业务用户，请选择“列表”选项。
- 对于 IT 管理员用户，请选择“通过 Microsoft 进行销售”。
请阅读[了解店面选择](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type#understand-storefront-selection)以帮助你决定要构建哪种类型的 SaaS 应用。

#### <a name="sell-through-microsoft"></a>通过 Microsoft 进行销售
若要构建此体验，需要配置以下部分：

- 使用 Microsoft 的 SaaS API 连接你的 SaaS 服务网站。 [通过 Azure 销售 SaaS – API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-saas-subscription-apis) 一文介绍了如何创建此连接。
- 在云合作伙伴门户上的“技术配置”窗体中启用“通过 Azure 进行销售”，并提供必需的信息。 有关此计费模型及其实现方式的详细信息，请参阅 [SaaS – 通过 Azure 进行销售](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-saas-offer-subscriptions#overview)。

 ![“通过 Microsoft 进行销售”窗体](./media/saas-techinfo-sellthrough-ms.png)

下表介绍了“通过 Microsoft 进行销售”的必需字段。

|  **字段名称**   |  **说明**  |
|  ---------------  |  ---------------  |
|    预览订阅 ID               |    在正式版推出之前，用于在预览版中测试套餐的所有 Azure 订阅标识符。               |
|     入门说明              |   有关与客户共享，以帮助他们连接到 SaaS 应用的指导。 允许基本的 HTML 标记，例如：&lt;p&gt;、&lt;h1&gt;、&lt;li&gt;，等等。                |
|    登陆页 URL  |   从 Azure 门户获取套餐后，将客户定向到的登陆站点 URL。 此 URL 也是接收连接 API 以方便与 Microsoft 开展商务活动的终结点。                |
|  连接 Webhook    |  对于 Microsoft 需要代表客户向你发送的所有异步事件（示例：Azure 订阅已无效），我们需要你提供连接 Webhook。 如果你尚未部署 Webhook 系统，最简单的配置是使用一个 HTTP 终结点逻辑应用来侦听发布到该终结点的所有事件，并相应地进行处理。 有关详细信息，请参阅<a href="https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint">在逻辑应用中使用 HTTP 终结点调用、触发或嵌套工作流</a>                |
|  Azure AD 租户 ID 和应用 ID      |   在 Azure 门户中，我们要求创建一个 Active Directory 应用，以便可以验证两个服务之间的连接是否属于经过身份验证的通信。 对于这些字段，请创建一个 AD 应用，并粘贴所需的相应租户 ID 和应用 ID。 请注意，应用 ID 将关联到 publisherID。 因此，请确保所有产品/服务中的应用 ID 都相同。             |


最后，如果你选择“通过 Microsoft 进行销售”，则“新建产品/服务”中还会有名为“计划”的另一个选项卡。 

[“计划”选项卡](./cpp-plans-tab.md)列出特定的计划，以及 SaaS 应用支持的相应价格。 到目前为止，我们允许按月定价，并允许 1 个月或 3 个月的免费访问。 这些计划和价格应与你自己的 SaaS 应用站点中的计划和价格完全匹配。

>[!NOTE] 
>只有选择了“通过 Microsoft 进行销售”时才需要使用计划。

### <a name="call-to-action-field"></a>“行动号召”字段

“行动号召”字段允许你选取在产品/服务的购置按钮上显示的消息。 提供了以下选项：

- 免费版 – 如果选择此选项，则会提示你输入客户可以用来访问 SaaS 应用的一个试用版 URL。 例如： https://contoso.com/trial
- 免费试用版 – 如果选择此选项，则会提示你输入客户可以用来访问 SaaS 应用的一个试用版 URL。 例如： https://contoso.com/trial
- 与我联系

有关“行动号召”选项的详细信息，请参阅“选择发布选项”。

## <a name="next-steps"></a>后续步骤

- [“计划”选项卡（可选）](./cpp-plans-tab.md)
- [“通道信息”选项卡](./cpp-channel-info-tab.md)
