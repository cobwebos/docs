---
title: "添加缓存以提高 Azure API 管理中的性能 | Microsoft Docs"
description: "了解如何改善滞后时间、带宽消耗和 API 管理服务调用的 web 服务负载。"
services: api-management
documentationcenter: 
author: vladvino
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
ms.openlocfilehash: 7458ad6e0a864d742f74ce743ce3179594113c00
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2017
---
# <a name="add-caching-to-improve-performance-in-azure-api-management"></a>添加缓存以提高 Azure API 管理中的性能
API 管理中的操作可以配置为响应缓存。 响应缓存可以显著减少 API 延迟、带宽消耗和不经常更改数据的 web 服务负载。
 
若要更详细地了解缓存，请参阅 [API 管理缓存策略](api-management-caching-policies.md)和 [Azure API 管理中的自定义缓存](api-management-sample-cache-by-key.md)。

![缓存策略](media/api-management-howto-cache/cache-policies.png)

学习内容：

> [!div class="checklist"]
> * 为 API 添加响应缓存
> * 验证作用的缓存

## <a name="prerequisites"></a>先决条件

完成本教程：

+ [创建一个 Azure API 管理实例](get-started-create-service-instance.md)
+ [导入和发布 API](import-and-publish.md)

## <a name="caching-policies"> </a>添加缓存策略

使用此示例中显示的缓存策略时，向 **GetSpeakers** 操作发出的第一个请求返回来自后端服务的响应。 将缓存此响应，由指定的标头和查询字符串参数进行键控。 采用匹配的参数，对操作的后续调用会返回缓存的响应，直到缓存时间间隔过期。

1. 登录 Azure 门户 ([https://portal.azure.com](https://portal.azure.com))。
2. 浏览到自己的 APIM 实例。
3. 选择“API”选项卡。
4. 在 API 列表中单击“演示会议 API”。
5. 选择“GetSpeakers”。
6. 选择屏幕顶部的“设计”选项卡。
7. 在“入站处理”窗口中，单击三角形（铅笔旁边）。

    ![代码编辑器](media/api-management-howto-cache/code-editor.png)
8. 选择“代码编辑器”。
9. 在 **inbound** 元素中添加以下策略：

        <cache-lookup vary-by-developer="false" vary-by-developer-groups="false">
            <vary-by-header>Accept</vary-by-header>
            <vary-by-header>Accept-Charset</vary-by-header>
            <vary-by-header>Authorization</vary-by-header>
        </cache-lookup>

10. 在 **outbound** 元素中添加以下策略：

        <cache-store caching-mode="cache-on" duration="20" />

    **持续时间** 指定缓存响应的过期时间间隔。 此示例中的时间间隔为 **20** 秒。

## <a name="test-operation"> </a>调用操作和测试缓存
若要查看作用的缓存，请从开发人员门户调用操作。

1. 在 Azure 门户中浏览到自己的 APIM 实例。
2. 选择“API”选项卡。
3. 选择向其添加了缓存策略的 API。
4. 选择“GetSpeakers”操作。
5. 单击右上方菜单中的“测试”选项卡。
6. 按“发送”。

## <a name="next-steps"> </a>后续步骤
* 有关缓存策略的详细信息，请参阅 [API 管理策略参考][API Management policy reference]中的[缓存策略][Caching policies]。
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


[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md

[API Management policy reference]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[Caching policies]: https://msdn.microsoft.com/library/azure/dn894086.aspx

[Create an API Management service instance]: get-started-create-service-instance.md

[Configure an operation for caching]: #configure-caching
[Review the caching policies]: #caching-policies
[Call an operation and test the caching]: #test-operation
[Next steps]: #next-steps
