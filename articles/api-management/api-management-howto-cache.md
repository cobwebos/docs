---
title: "添加缓存以提高 Azure API 管理中的性能 | Microsoft Docs"
description: "了解如何改善滞后时间、带宽消耗和 API 管理服务调用的 web 服务负载。"
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
ms.date: 10/25/2016
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c8cdb37fceb7b598c92b7b3cd41655c87c74e639


---
# <a name="add-caching-to-improve-performance-in-azure-api-management"></a>添加缓存以提高 Azure API 管理中的性能
API 管理中的操作可以配置为响应缓存。 响应缓存可以显著减少 API 延迟、带宽消耗和不经常更改数据的 web 服务负载。

本指南将介绍如何为 API 添加响应缓存，以及为示例 Echo API 操作配置策略。 然后，你可以从开发人员门户调用操作以验证缓存起作用。

> [!NOTE]
> 有关使用策略表达式按密钥缓存项目的信息，请参阅 [Azure API 管理中的自定义缓存](api-management-sample-cache-by-key.md)。
> 
> 

## <a name="prerequisites"></a>先决条件
执行本指南中的步骤之前，API 管理服务实例必须已配置 API 和产品。 如果尚未创建 API 管理服务实例，请参阅 [Azure API 管理入门][Azure API 管理入门]教程中的[创建 API 管理服务实例][创建 API 管理服务实例]。

## <a name="configure-caching"> </a>为缓存配置操作
在此步骤中，你将查看示例 Echo API 的“ **GET 资源（已缓存）** ”操作的缓存设置。

> [!NOTE]
> 每个预先配置 Echo API 的 API 管理服务实例，都可用于试验和了解 API 管理。 有关详细信息，请参阅 [Azure API 管理入门][Azure API 管理入门]。
> 
> 

若要开始，请单击 API 管理服务的 Azure 门户中的“发布者门户”。 这将转到 API 管理发布者门户。

![发布者门户][api-management-management-console]

在左侧“API 管理”菜单中，单击“API”，然后单击“Echo API”。

![Echo API][api-management-echo-api]

单击“操作”选项卡，然后在“操作”列表中单击“GET 资源(已缓存)”操作。

![Echo API 操作][api-management-echo-api-operations]

单击“ **缓存** ”选项卡查看此操作的缓存设置。

![缓存选项卡][api-management-caching-tab]

要为操作启用缓存，请选中“ **启用** ”复选框。 在此示例中，已启用缓存。

每个操作的响应基于根据“查询字符串参数变化”和“标头变化”字段中的值进行键控。 如果您要缓存基于查询字符串参数或标头的多个响应，可以在这两个字段中对它们进行配置。

**持续时间** 指定缓存响应的过期时间间隔。 在此示例中，时间间隔是 **3600** 秒，相当于一小时。

在此示例中使用缓存配置，对“ **GET 资源（缓存）** ”操作的第一个请求将从后端服务返回一个响应。 将缓存此响应，由指定的标头和查询字符串参数进行键控。 采用匹配的参数，对操作的后续调用会返回缓存的响应，直到缓存时间间隔过期。

## <a name="caching-policies"> </a>查看缓存策略
在此步骤中，你会查看示例 Echo API 的“ **GET 资源（已缓存）** ”操作的缓存设置。

在“ **缓存** ”选项卡上为操作配置缓存设置时，为操作添加缓存策略。 可以在策略编辑器中查看并编辑这些策略。

在左侧“API 管理”菜单中单击“策略”，然后从“操作”下拉列表中选择“Echo API/GET 资源(已缓存)”。

![策略范围操作][api-management-operation-dropdown]

这将在策略编辑器中显示此操作的策略。

![API 管理策略编辑器][api-management-policy-editor]

此操作的策略定义包括定义缓存配置的策略，使用上一步中“ **缓存** ”选项卡进行审核。

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

> [!NOTE]
> 在策略编辑器中对缓存策略进行的更改将反映在操作的“ **缓存** ”选项卡中，反之亦然。
> 
> 

## <a name="test-operation"> </a>调用操作和测试缓存
要查看作用的缓存，我们可以从开发人员门户调用操作。 单击右上方菜单中的“ **开发人员门户** ”。

![开发人员门户][api-management-developer-portal-menu]

单击顶部菜单中的“API”，然后选择“Echo API”。

![Echo API][api-management-apis-echo-api]

> 如果必须只有一个 API 得到配置或对您的帐户可见，然后单击 API 使您直接进入该 API 的操作。
> 
> 

选择“GET 资源(已缓存)”操作，然后单击“打开控制台”。

![打开控制台][api-management-open-console]

控制台允许直接从开发人员门户调用操作。

![控制台][api-management-console]

保留 param1 和 param2 的默认值。

从“ **订阅密钥** ”下拉列表中选择所需的密钥。 如果你的帐户只有一个订阅，则已处于选中状态。

在“请求标头”文本框中输入“sampleheader:value1”。

单击“ **HTTP Get** ”并记下响应标头。

在“请求标头”文本框中输入“sampleheader:value2”，然后单击“HTTP Get”。

请注意，sampleheader 的值仍是响应中的 value1。 尝试一些不同的值，请注意返回来自第一次调用的缓存响应。

将“25”输入“param2”字段，然后单击“HTTP Get”。

请注意，响应中 sampleheader 的值现在是 value2。 因为操作结果都由查询字符串进行键控，所以没有返回以前缓存的响应。

## <a name="next-steps"> </a>后续步骤
* 有关缓存策略的详细信息，请参阅 [API 管理策略参考][API 管理策略参考]中的[缓存策略][缓存策略]。
* 有关使用策略表达式按密钥缓存项目的信息，请参阅 [Azure API 管理中的自定义缓存](api-management-sample-cache-by-key.md)。

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


[如何将操作添加到 API]: api-management-howto-add-operations.md
[如何添加并发布产品]: api-management-howto-add-products.md
[监视和分析]: api-management-monitoring.md
[向产品添加 API]: api-management-howto-add-products.md#add-apis
[发布产品]: api-management-howto-add-products.md#publish-product
[创建 API 管理服务实例]: api-management-get-started.md

[API 管理策略参考]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[缓存策略]: https://msdn.microsoft.com/library/azure/dn894086.aspx

[创建 API 管理服务实例]: api-management-get-started.md#create-service-instance

[为缓存配置操作]: #configure-caching
[查看缓存策略]: #caching-policies
[调用操作和测试缓存]: #test-operation
[后续步骤]: #next-steps



<!--HONumber=Nov16_HO2-->


