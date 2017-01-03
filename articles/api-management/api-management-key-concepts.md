---
title: "API 管理关键概念"
description: "了解有关 API、产品、角色、组和其他 API 管理关键概念。"
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: e71da405-835a-48f3-956f-45c1a85698d7
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/15/2016
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: b0ead7ecbf9327e9df6846d6e046ed69f25832cf
ms.openlocfilehash: 872fbd44a9bccf0d6e9f7f3f3bd839e384874ae5


---
# <a name="what-is-api-management"></a>什么是 API 管理？
API 管理可帮助组织将 API 发布给外部、合作伙伴和内部开发人员，以充分发挥其数据和服务的潜力。 所有企业都想要作为数字平台扩大其运营、创建新渠道、查找新客户和深化与现有的契合。 API 管理提供了核心能力，通过开发人员参与、商业洞察力、分析、安全和保护确保成功的 API 程序。

观看以下视频，概要了解 Azure API 管理，并学习如何使用 API 管理将更多功能添加到 API，包括访问控制、速率限制、监视、事件日志记录和响应缓存，将你的工作量降至最低。

> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/Azure-API-Management-Overview/player]
> 
> 

要使用 API 管理，管理员需要创建 API。 每个 API 包括一个或多个操作，并可以将每个 API 添加到一个或多个产品。 要使用的 API，开发人员需要订阅包含该 API 的产品，然后它们可以调用该 API 的操作，根据任何可能生效的使用情况策略。

本主题概述了 API 管理的关键概念。

> [!NOTE]
> 有关详细信息，请参阅 PDF 版的 [基于云的 API 管理：利用 API 的强大功能](http://j.mp/ms-apim-whitepaper) 白皮书。 由 CITO Research 编撰的有关 API 管理的简介白皮书包括︰ 
> 
> * 常见 API 需求和挑战
> * 分离 API 和呈现外观
> * 使开发人员快速启动并运行
> * 保护访问权限
> * 分析和度量值
> * 控制并深入了解 API 管理平台
> * 云解决方案与本地解决方案使用对比
> * Azure API 管理
> 
> 

## <a name="apis"> </a>API 和操作
API 是 API 管理服务实例的基础。 每个 API 表示一组可供开发人员使用的操作。 每个 API 包含对实施 API 的后端服务的引用，而其操作映射到后端服务实施的操作。 API 管理中的操作高度可配置，可控制 URL 映射、查询和路径参数、请求和响应内容以及操作响应缓存。 速率限制、配额和 IP 限制策略还可以在 API 或单个操作级别实施。

有关详细信息，请参阅[如何创建 API][How to create APIs] 和[如何将操作添加到 API][How to add operations to an API]。

## <a name="products"> </a> 产品
产品是如何将 API 提供给开发人员。 API 管理中的产品有一个或多个 API，并且配置为包含一个标题、说明和使用条款。 产品可以**公开**或**受保护**。 必须先订阅受保护的产品，然后才能使用它们，但公开的产品无需订阅即可使用。 产品可以发布时准备供开发人员使用。 产品一旦发布，开发人员就可以查看（如果是受保护的产品，应先进行订阅）。 在产品级别配置订阅批准，也可由管理员批准或被自动批准。

组用于管理产品对开发人员的可见性。 产品向组授予可见性，并且开发人员可以查看和订阅对他们所属的组可见的产品。 

有关详细信息，请参阅[如何创建和发布产品][How to create and publish a product]及以下视频。

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Using-Products/player]
> 
> 

## <a name="groups"> </a> 组
组用于管理产品对开发人员的可见性。 API 管理具有下列不可变的系统组。

* **管理员** - Azure 订阅管理员是此组的成员。 管理员管理 API 管理服务实例、创建 API、操作，以及开发人员所使用的产品。
* **开发人员** - 已经过身份验证的开发人员门户用户属于此组。 开发人员是使用你的 API 构建应用程序的客户。 开发人员有权访问开发人员门户，并构建调用 API 操作的应用程序。
* **来宾** - 未经身份验证的开发人员门户用户，如访问此组中 API 管理实例的开发人员门户的潜在客户。 它们可以被授予某些只读访问权限，如能够查看 API，但不能调用它们。

除了这些系统组，管理员还可以创建自定义组或[利用关联 Azure Active Directory 租户中的外部组](api-management-howto-aad.md#how-to-add-an-external-azure-active-directory-group)。 自定义组和外部组可与系统组一起使用为开发人员提供可见性并可以访问 API 产品。 例如，可以为隶属于一个特定合作伙伴组织的开发人员创建一个自定义组并允许他们从仅包含相关 API 的产品中访问 API。 用户可以是多个组的成员。

有关详细信息，请参阅[如何创建和使用组][How to create and use groups]。

## <a name="developers"> </a> 开发人员
开发人员表示 API 管理服务实例中的用户帐户。 可以让管理员创建或邀请开发人员加入，也可以让开发人员从[开发人员门户][Developer portal]注册。 每个开发人员是一个或多个组的成员，并且可以订阅授予这些组的可见性的产品。

开发人员需要他们订阅被授予该产品的主要和辅助密钥的产品。 在调用产品的 API 时使用此键。

有关详细信息，请参阅[如何创建或邀请开发人员][How to create or invite developers]和[如何将组与开发人员关联][How to associate groups with developers]。

## <a name="policies"> </a> 策略
策略是 API 管理的一项强大功能，允许发布者通过配置更改 API 的行为。 策略是一组语句，在请求或 API 的响应时按顺序执行。 流行的语句包含 XML 格式转换为 JSON 和调用速率限制，以限制从开发人员传入的呼叫量，还有许多其他策略可用。

在任何 API 管理策略中，策略表达式可以用作属性值或文本值，除非该策略另外指定。 某些策略（如[控制流](https://msdn.microsoft.com/library/azure/dn894085.aspx#choose)和[设置变量](https://msdn.microsoft.com/library/azure/dn894085.aspx#set-variable)策略）基于策略表达式。 有关详细信息，请参阅[高级策略](https://msdn.microsoft.com/library/azure/dn894085.aspx#AdvancedPolicies)、[策略表达式](https://msdn.microsoft.com/library/azure/dn910913.aspx)，并观看以下视频。

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Policy-Expressions-in-Azure-API-Management/player]
> 
> 

有关 API 管理策略的完整列表，请参阅[策略参考][Policy reference]。 有关使用和配置策略的详细信息，请参阅 [API 管理策略][API Management policies]。 有关使用速率限制和配额策略创建产品的教程，请参阅[如何创建和配置高级产品设置][How create and configure advanced product settings]。 有关演示，请观看下面的视频。

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Rate-Limits-and-Quotas/player]
> 
> 

## <a name="developer-portal"> </a> 开发人员门户
开发人员门户是开发人员可以了解您的 API、视图和调用操作以及订阅产品的位置。 潜在客户可以访问开发人员门户，查看 API 和操作并注册。 你的开发人员门户的 URL 位于你的 API 管理服务实例的 Azure 经典门户中的仪表板上。

您可以通过添加自定义内容、自定义样式并添加您的品牌定制开发人员门户的外观。

## <a name="api-management-and-the-api-economy"></a>API 管理和 API 经济性
若要了解有关 API 管理的更多信息，请观看以下来自 Microsoft Ignite 2015 大会的演示文稿。

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3708/player]
> 
> 

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
[How create and configure advanced product settings]: api-management-howto-product-with-rules.md
[How to create or invite developers]: api-management-howto-create-or-invite-developers.md
[Policy reference]: api-management-policy-reference.md
[API Management policies]: api-management-howto-policies.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance







<!--HONumber=Dec16_HO3-->


