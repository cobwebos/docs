---
title: Azure API 管理中的订阅 | Microsoft Docs
description: 了解 Azure API 管理中订阅的概念。
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: 45a65c7a94f3e6917b2882f27c9ad7d764ef97d7
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2018
ms.locfileid: "52621693"
---
# <a name="subscriptions-in-azure-api-management"></a>Azure API 管理中的订阅

在 Azure API 管理 (APIM) 中，订阅是一个重要的概念。 API 使用者获取通过 APIM 实例发布的 API 的访问权限时，最常使用的方式就是它。 本文概述了此概念。

## <a name="what-is-subscriptions"></a>什么是订阅

通过 APIM 发布 API 时，若要确保对这些 API 进行安全的访问，最容易且最常用的方式是使用订阅密钥。 换句话说，需使用已发布 API 的开发人员必须在调用这些 API 时在 HTTP 请求中包括一个有效的订阅密钥。 否则，这些调用会立即被 APIM 网关拒绝，不会转发到后端服务。

若要获取访问 API 所需的订阅密钥，必须有一个订阅。 订阅实质上是一个命名的容器，对应于一对订阅密钥。 订阅可以由需要使用已发布 API 的开发人员获取，不一定需要 API 发布者的批准。 API 发布者也可代表 API 使用者直接创建订阅。

> [!TIP]
> APIM 还支持通过其他机制来确保对 API 进行安全的访问，这些机制包括 [OAuth2.0](api-management-howto-protect-backend-with-aad.md)、[客户端证书](api-management-howto-mutual-certificates-for-clients.md)和 [IP 允许列表](https://docs.microsoft.com/azure/api-management/api-management-access-restriction-policies#RestrictCallerIPs)

## <a name="scope-of-subscriptions"></a>订阅范围

订阅可以与各种范围相关联：产品、所有 API 或单个 API。

### <a name="subscriptions-for-a-product"></a>产品的订阅

传统上，APIM 中的订阅始终与单个 [API 产品](api-management-terminology.md)范围相关联。 开发人员可以在开发人员门户中找到产品列表，并针对要使用的产品提交订阅请求。 订阅请求获得批准后（不管是自动批准还是由 API 发布者批准），开发人员可以使用其中的密钥来访问产品中的所有 API。

![产品订阅](./media/api-management-subscriptions/product-subscription.png)

> [!TIP]
> 有时候，API 发布者可能希望在不要求订阅的情况下将 API 产品发布给公众。 发布者可以在 Azure 门户中取消选中产品的“设置”页中的“需要订阅”选项。 这样一来，用户就可以在没有 API 密钥的情况下访问该产品的所有 API。

### <a name="subscriptions-for-all-apis-or-an-individual-api"></a>订阅所有 API 或单个 API

> [!NOTE]
> 目前，此功能仅在 API 管理的“消耗”层中提供。

引入 APIM 的[消耗](https://aka.ms/apimconsumptionblog)层时，我们进行了一些更改以简化密钥管理。 首先，我们在原来的基础上添加了两个订阅范围 - 所有 API 和单个 API。 订阅范围不再限于一个 API 产品。 现在可以创建密钥，授予对一个 API（或 APIM 实例中的所有 API）的访问权限，而不需先创建一个产品并向其添加 API。 另外，每个 APIM 实例现在都有一个不可变的范围为“所有 API”的订阅，因此可以更轻松、更直观地在测试控制台中测试和调试 API。

其次，APIM 现在允许“独立”订阅。 订阅不再需要与开发人员帐户相关联。 这适用于特定的情况，例如一个订阅由多个开发人员或团队共享。

最后，API 发布者现在可以直接在 Azure 门户中[创建订阅](api-management-howto-create-subscriptions.md)：

![灵活的订阅](./media/api-management-subscriptions/flexible-subscription.png)

## <a name="next-steps"></a>后续步骤
有关 API 管理的详细信息，请执行以下操作：

+ 了解 API 管理中的其他[概念](api-management-terminology.md)
+ 按[教程](import-and-publish.md)操作，详细了解 API 管理
+ 查看[常见问题解答页](api-management-faq.md)，了解常见问题