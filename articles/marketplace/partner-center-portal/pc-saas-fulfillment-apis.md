---
title: Microsoft 商用 marketplace 中的 SaaS 履单 Api
description: 介绍实现 Api，使你能够在 Microsoft AppSource 和 Azure Marketplace 中集成 SaaS 产品。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/18/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 2e6381afb19018822f6f37171a5ca4b3d929b42e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88037518"
---
# <a name="saas-fulfillment-apis-in-the-microsoft-commercial-marketplace"></a>Microsoft 商用 marketplace 中的 SaaS 履单 Api

SaaS 履单 Api 允许发布者（也称为独立软件供应商） (Isv) ，用于在 Microsoft AppSource、Azure Marketplace 和 Azure 门户中发布和销售其 SaaS 应用程序。 这些 Api 使 ISV 应用程序能够参与所有已启用商务的频道：直接、合作伙伴 led (经销商) 和现场 led。  与这些 Api 集成是在合作伙伴中心创建和发布事务 SaaS 产品/服务的一项要求。

Isv 必须实现以下 API 流，方法是将添加到其 SaaS 服务代码，以便为 Isv 和 Microsoft 维护相同的订阅状态：

* 登陆页面流： Microsoft 通知发布者，发布者已在 marketplace 中购买了发布者的 SaaS 优惠。
* 激活流：发布者通知 Microsoft 已在发布者端配置了新购买的 SaaS 帐户。
* 更新流：更改购买的计划和/或购买的座位数。
* 挂起和恢复流：如果客户的付款方式不再有效，则暂停购买的 SaaS 产品/服务。 解决了付款方式问题后，可以恢复暂停的产品/服务。
* Webhook 流： Microsoft 将通知发布者来自 Microsoft 端的客户触发的 SaaS 订阅更改和取消。

对于已购买的 SaaS 订阅，集成是可选的，因为它可由 Microsoft 端的客户进行。

与 SaaS 履单 Api 的正确集成对于确保

* 购买发布者的 SaaS 产品/服务的最终客户将由 Microsoft 进行正确计费。
* 最终客户将获得正确的用户体验，并使用和管理在 marketplace 中购买的 SaaS 订阅。

通过这些 Api，发布者的产品/服务可以参与所有启用了商务的频道：

* 直通
*  (经销商、CSP) 的伙伴 led
* 现场 led

在经销商 (CSP) 情况下，CSP 会代表最终客户购买 SaaS 产品/服务。 客户应使用 SaaS 产品/服务，但 CSP 是执行以下操作的实体：

* 向客户计费
* 更改订阅计划/购买的座位数量
* 取消订阅

对于此方案，发布服务器不需要实现任何不同的 API 调用流。

有关 CSP 的详细信息，请参阅 https://partner.microsoft.com/licensing 。

>[!Warning]
>此 API 的当前版本是版本2，适用于所有新的 SaaS 产品/服务。 API 的版本1已弃用，并将进行维护以支持现有的产品/服务。

>[!Note]
>SaaS 履单 Api 只应从发布者的后端服务中调用。 不支持直接从发布者的网页与 Api 集成。 只应使用服务到服务的身份验证流。

## <a name="next-steps"></a>后续步骤

如果尚未这样做，请在 [Azure 门户](https://ms.portal.azure.com) 中注册 SaaS 应用程序，如 [注册 Azure AD 应用程序](./pc-saas-registration.md)中所述。  之后，使用此接口的最新版本进行开发： [SaaS 履单 API 版本 2](./pc-saas-fulfillment-api-v2.md)。
