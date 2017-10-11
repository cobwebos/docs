---
title: "添加缓存以提高性能，在 Azure API 管理 |Microsoft 文档"
description: "了解如何改进延迟、 带宽消耗和 API 管理服务调用的 web 服务负载。"
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 740f6a27-8323-474d-ade2-828ae0c75e7a
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 59c595f0d5ce849f44c46fdb6cab0b44d35fffa0
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="add-caching-to-improve-performance-in-azure-api-management"></a>添加缓存以提高性能，在 Azure API 管理
API 管理中的操作可以配置为响应缓存。 响应缓存可以显著减少 API 延迟、 带宽消耗和 web 服务负载不经常更改的数据。

本指南演示如何添加对你的 API 的响应缓存并配置示例 Echo API 操作的策略。 然后，可以从开发人员门户，以验证操作中的缓存调用该操作。

> [!NOTE]
> 有关使用策略表达式的键的缓存项的信息，请参阅[缓存 Azure API 管理中自定义](api-management-sample-cache-by-key.md)。
> 
> 

## <a name="prerequisites"></a>必要條件
按照此指南中的步骤，你必须具有一个 API 的 API 管理服务实例前后产品配置。 如果尚未创建 API 管理服务实例，请参阅[创建 API 管理服务实例][ Create an API Management service instance]中[开始使用 Azure API 管理][ Get started with Azure API Management]教程。

## <a name="configure-caching"></a>配置用于缓存的操作
在此步骤中，你将查看缓存设置**GET 资源 （缓存）**示例 Echo API 的操作。

> [!NOTE]
> 每个 API 管理服务实例是预配置 Echo api 可以用于试验和了解 API 管理。 有关详细信息，请参阅[开始使用 Azure API 管理][Get started with Azure API Management]。
> 
> 

若要开始，单击**发布者门户**Azure 门户中的 API 管理系统服务。 将你转到 API 管理发布者门户。

![发布者门户][api-management-management-console]

单击**Api**从**API 管理**左侧的菜单，然后单击**Echo API**。

![Echo API][api-management-echo-api]

单击**操作**选项卡上，并依次**GET 资源 （缓存）**操作从**操作**列表。

![Echo API 操作][api-management-echo-api-operations]

单击**Caching**选项卡以查看此操作的缓存设置。

![高速缓存选项卡][api-management-caching-tab]

若要启用缓存的操作，请选择**启用**复选框。 在此示例中，启用缓存。

每个操作响应进行键控中的值基于**随查询字符串参数变化**和**Vary 标头**字段。 如果你想要缓存基于查询字符串参数或标头的多个响应，你可以在这两个字段中对它们进行配置。

**持续时间**指定缓存响应的过期时间间隔。 在此示例中的时间间隔是**3600**秒，相当于一小时。

在此示例中，对第一个请求中使用的缓存配置**GET 资源 （缓存）**操作返回来自后端服务的响应。 将缓存此响应，由指定的标头和查询字符串参数进行键控。 到具有匹配参数的操作的后续调用会返回，直到缓存持续时间间隔已过期的缓存的响应。

## <a name="caching-policies"></a>查看缓存策略
在此步骤中，你查看的缓存设置**GET 资源 （缓存）**示例 Echo API 的操作。

当缓存设置都配置为一个操作上**Caching**选项卡上，缓存策略添加操作。 可查看并在策略编辑器中编辑这些策略。

单击**策略**从**API 管理**左侧的菜单，然后选择**Echo API 获取资源 （缓存） /**从**操作**下拉列表。

![策略范围操作][api-management-operation-dropdown]

这将在策略编辑器中显示此操作的策略。

![API 管理策略编辑器][api-management-policy-editor]

此操作的策略定义包括定义缓存配置使用审核策略**Caching**上一步中的选项卡。

```xml
<policies>
    <inbound>
        <base />
        <cache-lookup vary-by-developer="false" vary-by-developer-groups="false">
            <vary-by-header>Accept</vary-by-header>
            <vary-by-header>Accept-Charset</vary-by-header>
        </cache-lookup>
        <rewrite-uri template="/resource" />
    </inbound>
    <outbound>
        <base />
        <cache-store caching-mode="cache-on" duration="3600" />
    </outbound>
</policies>
```

> [!NOTE]
> 在策略编辑器中的缓存策略所做的更改将反映在**Caching**选项卡上的操作，反之亦然。
> 
> 

## <a name="test-operation"></a>调用操作和测试缓存
若要查看缓存的作用，我们可以从开发人员门户调用该操作。 单击**开发人员门户**右上方菜单中。

![开发人员门户][api-management-developer-portal-menu]

单击**Api**在顶部菜单中，，然后选择**Echo API**。

![Echo API][api-management-apis-echo-api]

> 如果必须只有一个 API 得到配置或对你的帐户可见，然后单击 Api 使您直接进入该 API 的操作。
> 
> 

选择**GET 资源 （缓存）**操作，，然后单击**打开的控制台**。

![打开控制台][api-management-open-console]

控制台允许你调用直接从开发人员门户的操作。

![控制台][api-management-console]

保留默认值为**param1**和**param2**。

选择所需的密钥从**订阅密钥**下拉列表。 如果你的帐户具有只有一个订阅，它已被选中。

输入**sampleheader: value1**中**请求标头**文本框。

单击**HTTP Get**并记下响应标头。

输入**sampleheader: value2**中**请求标头**文本中，然后单击**HTTP Get**。

请注意，值**sampleheader**仍**value1**在响应中。 请尝试一些不同的值，并请注意，返回第一次调用的缓存的响应。

输入**25**到**param2**字段，然后再单击**HTTP Get**。

请注意，值**sampleheader**在响应现在是**value2**。 因为操作结果由查询字符串进行键控，没有返回以前缓存的响应。

## <a name="next-steps"></a>后续步骤
* 有关缓存策略的详细信息，请参阅[缓存策略][ Caching policies]中[API 管理策略参考][API Management policy reference]。
* 有关使用策略表达式的键的缓存项的信息，请参阅[缓存 Azure API 管理中自定义](api-management-sample-cache-by-key.md)。

[api-management-management-console]: ./media/api-management-howto-cache/api-management-management-console.png
[api-management-echo-api]: ./media/api-management-howto-cache/api-management-echo-api.png
[api-management-echo-api-operations]: ./media/api-management-howto-cache/api-management-echo-api-operations.png
[api-management-caching-tab]: ./media/api-management-howto-cache/api-management-caching-tab.png
[api-management-operation-dropdown]: ./media/api-management-howto-cache/api-management-operation-dropdown.png
[api-management-policy-editor]: ./media/api-management-howto-cache/api-management-policy-editor.png
[api-management-developer-portal-menu]: ./media/api-management-howto-cache/api-management-developer-portal-menu.png
[api-management-apis-echo-api]: ./media/api-management-howto-cache/api-management-apis-echo-api.png
[api-management-open-console]: ./media/api-management-howto-cache/api-management-open-console.png
[api-management-console]: ./media/api-management-howto-cache/api-management-console.png


[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: api-management-get-started.md

[API Management policy reference]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[Caching policies]: https://msdn.microsoft.com/library/azure/dn894086.aspx

[Create an API Management service instance]: api-management-get-started.md#create-service-instance

[Configure an operation for caching]: #configure-caching
[Review the caching policies]: #caching-policies
[Call an operation and test the caching]: #test-operation
[Next steps]: #next-steps
