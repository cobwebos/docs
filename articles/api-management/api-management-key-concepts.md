---
title: "Azure API 管理概述和重要概念 | Microsoft 文档"
description: "了解有关 API、产品、角色、组和其他 API 管理关键概念。"
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 11/15/2017
ms.author: apimpm
ms.custom: mvc
ms.openlocfilehash: 727be96828b9217f89214722f474abfc220b0b3c
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2018
---
# <a name="what-is-api-management"></a>什么是 API 管理？

API 管理 (APIM) 可帮助组织将 API 发布给外部、合作伙伴和内部开发人员，以充分发挥其数据和服务的潜力。 所有企业都想要作为数字平台扩大其运营、创建新渠道、查找新客户和深化与现有的契合。 API 管理通过开发人员参与、商业洞察力、分析、安全性和保护提供了核心竞争力以确保成功的 API 程序。 可以使用 Azure API 管理处理任何后端，并基于它发布正式的 API 程序。

观看以下视频，概要了解 Azure API 管理，并学习如何使用 API 管理将更多功能添加到 API，包括访问控制、速率限制、监视、事件日志记录和响应缓存，将工作量降至最低。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-API-Management-Overview/player]
> 
> 

本主题概述了涉及 APIM 的常用方案，  并简单介绍了 APIM 系统的主要组件， 然后较详细地介绍了每个组件。

## <a name="overview"></a>概述

要使用 API 管理，管理员需要创建 API。 每个 API 包括一个或多个操作，并可以将每个 API 添加到一个或多个产品。 要使用的 API，开发人员需要订阅包含该 API 的产品，它们可以调用该 API 的操作，根据任何可能生效的使用情况策略。 常见方案包括：

* **保护移动基础结构** 通过使用 API 密钥控制访问，使用限制或使用高级安全策略（如 JWT 令牌验证）阻止 DOS 攻击。
* **启用 ISV 合作伙伴生态系统** 通过开发人员门户提供快速的合作伙伴加入，并构建 API 外观使其与未准备好供合作伙伴使用的内部实现分离。
* **运行内部的 API 程序** 通过为组织提供一个集中位置来交流 API 的可用性和最新更改，基于组织帐户控制访问所有这一切都基于 API 网关与后端之间的安全通道。

系统由以下组件组成：

* **API 网关** 是具有以下功能的终结点：
  
  * 接受 API 调用，并将调用路由到后端。
  * 验证 API 密钥、JWT 令牌、证书和其他凭据。
  * 强制使用配额和速率限制。
  * 无需修改代码即可随时转换 API。
  * 在设置的位置缓存后端响应。
  * 记录调用元数据以用于分析。
* **发布者门户** 是一个管理界面，可以在其中设置 API 程序。 使用它可执行以下操作：
  
  * 定义或导入 API 架构。
  * 将 API 打包到产品中。
  * 设置策略，如 API 的配额或转换。
  * 从分析中获得见解。
  * 管理用户。
* **开发人员门户** 是面向开发人员的主要 Web 平台，可以在其中执行以下操作：
  
  * 阅读 API 文档。
  * 通过交互式控制台试用 API。
  * 创建帐户并可以订阅以获取 API 密钥。
  * 访问他们自己的使用情况分析。

有关详细信息，请参阅 PDF 版的 [基于云的 API 管理：利用 API 的强大功能](http://j.mp/ms-apim-whitepaper) 白皮书。 由 CITO Research 编撰的有关 API 管理的简介白皮书包括︰ 
 
 * 常见 API 需求和挑战
 * 分离 API 和呈现外观
 * 使开发人员快速启动并运行
 * 保护访问权限
 * 分析和度量值
 * 控制并深入了解 API 管理平台
 * 云解决方案与本地解决方案使用对比
 * Azure API 管理
 
## <a name="apis"> </a>API 和操作
API 是 API 管理服务实例的基础。 每个 API 表示一组可供开发人员使用的操作。 每个 API 包含对实施 API 的后端服务的引用，而其操作映射到后端服务实施的操作。 API 管理中的操作高度可配置，可控制 URL 映射、查询和路径参数、请求和响应内容以及操作响应缓存。 速率限制、配额和 IP 限制策略还可以在 API 或单个操作级别实施。

有关详细信息，请参阅[如何创建 API][How to create APIs] 和[如何将操作添加到 API][How to add operations to an API]。

## <a name="products"> </a> 产品
产品是如何将 API 提供给开发人员。 API 管理中的产品有一个或多个 API，并且配置为包含一个标题、说明和使用条款。 产品可以**公开**或**受保护**。 必须先订阅受保护的产品，才能使用它们，但公开的产品无需订阅即可使用。 产品可以供开发人员使用时，就会发布。 产品一旦发布，开发人员就可以查看（如果是受保护的产品，应先进行订阅）。 在产品级别配置订阅批准，也可由管理员批准或被自动批准。

组用于管理产品对开发人员的可见性。 产品向组授予可见性，并且开发人员可以查看和订阅对他们所属的组可见的产品。 

## <a name="groups"> </a> 组
组用于管理产品对开发人员的可见性。 API 管理具有下列不可变的系统组：

* **管理员** - Azure 订阅管理员是此组的成员。 管理员管理 API 管理服务实例、创建 API、操作，以及开发人员所使用的产品。
* **开发人员** - 已经过身份验证的开发人员门户用户属于此组。 开发人员是使用 API 构建应用程序的客户。 开发人员有权访问开发人员门户，并构建调用 API 操作的应用程序。
* **来宾** - 未经身份验证的开发人员门户用户，如访问此组中 API 管理实例的开发人员门户的潜在客户。 它们可以被授予某些只读访问权限，如能够查看 API，但不能调用它们。

除了这些系统组，管理员还可以创建自定义组或[利用关联 Azure Active Directory 租户中的外部组](api-management-howto-aad.md)。 自定义组和外部组可与系统组一起使用为开发人员提供可见性并可以访问 API 产品。 例如，可以为隶属于一个特定合作伙伴组织的开发人员创建一个自定义组并允许他们从仅包含相关 API 的产品中访问 API。 用户可以是多个组的成员。

有关详细信息，请参阅[如何创建和使用组][How to create and use groups]。

## <a name="developers"> </a> 开发人员
开发人员表示 API 管理服务实例中的用户帐户。 可以让管理员创建或邀请开发人员加入，也可以让开发人员从[开发人员门户][Developer portal]注册。 每个开发人员是一个或多个组的成员，并且可以订阅授予这些组的可见性的产品。

开发人员需要他们订阅被授予该产品的主要和辅助密钥的产品。 在调用产品的 API 时使用此键。

有关详细信息，请参阅[如何创建或邀请开发人员][How to create or invite developers]和[如何将组与开发人员关联][How to associate groups with developers]。

## <a name="policies"></a> 策略
策略是 API 管理的一项强大功能，允许发布者通过配置更改 API 的行为。 策略是一组语句，在请求或 API 的响应时按顺序执行。 流行的语句包含 XML 格式转换为 JSON 和调用速率限制，以限制从开发人员传入的呼叫数，还有许多其他策略可用。

在任何 API 管理策略中，策略表达式可以用作属性值或文本值，除非该策略另外指定。 某些策略（如[控制流](https://msdn.microsoft.com/library/azure/dn894085.aspx#choose)和[设置变量](https://msdn.microsoft.com/library/azure/dn894085.aspx#set-variable)策略）基于策略表达式。 有关详细信息，请参阅[高级策略](https://msdn.microsoft.com/library/azure/dn894085.aspx#AdvancedPolicies)和[策略表达式](https://msdn.microsoft.com/library/azure/dn910913.aspx)。


有关 API 管理策略的完整列表，请参阅[策略参考][Policy reference]。 有关使用和配置策略的详细信息，请参阅 [API 管理策略][API Management policies]。 有关使用速率限制和配额策略创建产品的教程，请参阅[如何创建和配置高级产品设置][How create and configure advanced product settings]。


## <a name="developer-portal"> </a> 开发人员门户
开发人员门户是开发人员可以了解 API、视图和调用操作以及订阅产品的位置。 潜在客户可以访问开发人员门户，查看 API 和操作并注册。 开发人员门户的 URL 位于 API 管理服务实例的 Azure 门户中的仪表板上。

可以通过添加自定义内容、自定义样式并添加品牌定制开发人员门户的外观。

## <a name="api-management-and-the-api-economy"></a>API 管理和 API 经济性

若要了解有关 API 管理的更多信息，请观看以下来自 Microsoft Ignite 2017 大会的演示文稿。

> [!VIDEO https://channel9.msdn.com/Events/Ignite/Microsoft-Ignite-Orlando-2017/BRK2186/player]
> 
> 

## <a name="next-steps"></a>后续步骤

完成以下快速入门，然后即可使用 Azure API 管理：

> [!div class="nextstepaction"]
> [创建一个 Azure API 管理实例](get-started-create-service-instance.md)

[APIs and operations]: #apis
[Products]: #products
[Groups]: #groups
[Developers]: #developers
[Policies]: #policies
[Developer portal]: #developer-portal

[How to create APIs]: api-management-howto-create-apis.md
[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer
[How create and configure advanced product settings]: transform-api.md
[How to create or invite developers]: api-management-howto-create-or-invite-developers.md
[Policy reference]: api-management-policy-reference.md
[API Management policies]: api-management-howto-policies.md
[Create an API Management service instance]: get-started-create-service-instance.md




