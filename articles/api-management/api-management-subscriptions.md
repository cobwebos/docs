---
title: Azure API 管理中的订阅 | Microsoft Docs
description: 了解 Azure API 管理中的订阅的概念。
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
ms.openlocfilehash: 6f577530c42952c6340a15110bcd37383a5fca57
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60693531"
---
# <a name="subscriptions-in-azure-api-management"></a>Azure API 管理中的订阅

在 Azure API 管理中，订阅是很重要的概念。 它们是 API 使用者访问通过 API 管理实例发布的 API 时最常用的方式。 本文概述了此概念。

## <a name="what-are-subscriptions"></a>什么是订阅？

通过 API 管理发布 API 时，使用订阅密钥保护对这些 API 的访问容易且常见。 需要使用已发布 API 的开发人员在调用这些 API 时必须在 HTTP 请求中包括一个有效的订阅密钥。 否则，API 管理网关会立即拒绝调用。 不会将它们转发到后端服务。

若要获取访问 API 所需的订阅密钥，必须有一个订阅。 订阅实质上是一个已命名的容器，该容器包含一对订阅密钥。 需要使用已发布 API 的开发人员可以获取订阅。 不需要 API 发布者批准。 API 发布者也可以直接为 API 使用者创建订阅。

> [!TIP]
> API 管理还支持使用其他机制来保护对 API 的访问，包括以下示例：
> - [OAuth2.0](api-management-howto-protect-backend-with-aad.md)
> - [客户端证书](api-management-howto-mutual-certificates-for-clients.md)
> - [IP 允许列表](https://docs.microsoft.com/azure/api-management/api-management-access-restriction-policies#RestrictCallerIPs)

## <a name="scope-of-subscriptions"></a>订阅范围

订阅可以与各种范围相关联：产品、所有 API 或单个 API。

### <a name="subscriptions-for-a-product"></a>产品的订阅

传统上，API 管理中的订阅始终与单个 [API 产品](api-management-terminology.md)范围相关联。 开发人员在开发人员门户上找到产品列表。 然后，它们将提交对要使用的产品的订阅请求。 在订阅请求获得批准后（不管是自动批准还是由 API 发布者批准），开发人员可以使用其中的密钥来访问产品中的所有 API。

![产品订阅](./media/api-management-subscriptions/product-subscription.png)

> [!TIP]
> 有时候，API 发布者可能希望在不要求订阅的情况下将 API 产品发布给公众。 发布者可以在 Azure 门户中在产品的“设置”页上取消选择“需要订阅”选项。 这样一来，用户就可以在没有 API 密钥的情况下访问该产品的所有 API。

### <a name="subscriptions-for-all-apis-or-an-individual-api"></a>订阅所有 API 或单个 API

引入 API 管理的[消耗](https://aka.ms/apimconsumptionblog)层时，我们进行了一些更改以简化密钥管理。
- 首先，我们在原来的基础上添加了两个订阅范围：所有 API 和单个 API。 订阅范围不再限于一个 API 产品。 现在可以创建密钥，以授予对 API 管理实例中的一个 API 或所有 API 的访问权限，而不需先创建一个产品并向其添加 API。 此外，每个 API 管理实例现在都有一个不可变的包含所有 API 的订阅。 此订阅使得在测试控制台中测试和调试 API 更为容易且简单明了。

- 其次，API 管理现在允许“独立”订阅。 订阅不再需要与开发人员帐户相关联。 此功能在某些情况下非常有用，例如当多个开发人员或团队共享某个订阅时。

- 最后，API 发布者现在可以直接在 Azure 门户中[创建订阅](api-management-howto-create-subscriptions.md)：

    ![灵活的订阅](./media/api-management-subscriptions/flexible-subscription.png)

## <a name="next-steps"></a>后续步骤
获取有关 API 管理的详细信息：

+ 了解 API 管理中的其他[概念](api-management-terminology.md)。
+ 按[教程](import-and-publish.md)操作，详细了解 API 管理。
+ 查看[常见问题解答页](api-management-faq.md)，了解常见问题。