---
title: "如何将操作添加到 Azure API 管理中的 API | Microsoft Docs"
description: "了解如何将操作添加到 Azure API 管理中的 API"
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 1158a023-1913-4e9c-93de-9164b672f9b3
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 8b047c0826590d1cb6a79a2f14ca07764dc2b409
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2017
---
# <a name="how-to-add-operations-to-an-api-in-azure-api-management"></a>如何将操作添加到 Azure API 管理中的 API
在可使用 API 管理中的 API 之前，必须添加操作。 本指南演示如何添加和配置不同类型的 API 操作到 API 管理。

## <a name="add-operation"> </a>添加操作
在发布者门户中将操作添加并配置到 API。 若要访问发布者门户，请在 API 管理服务的 Azure 门户中单击“发布者门户”。

![发布者门户][api-management-management-console]

> 如果尚未创建 API 管理服务实例，请参阅 [Azure API 管理入门][Get started with Azure API Management]教程中的[创建 API 管理服务实例][Create an API Management service instance]。
> 
> 

在发布者门户中选择所需的 API，并选择“操作”选项卡。 

![操作][api-management-operations]

单击“添加操作”添加新操作。 将显示“新操作”，并且在默认情况下将选择“签名”选项卡。

![添加操作][api-management-add-operation]

通过从下拉列表中选择指定“HTTP 谓词”。

![HTTP 方法][api-management-http-method]

<a name="url-template"></a>

通过键入一个或多个 URL 路径段和零个或多个查询字符串参数组成的 URL 片段定义 URL 目标。 URL 模板附加到 API 的基础 URL，标识单个 HTTP 操作。 它可能包含一个或多个由大括号标识的命名变量部分。 这些变量部分称为模板参数，并在管理平台处理请求时是从请求的 URL 中提取的动态分配值。

> URL 模板可以包含通配符模式。 例如，指定 `/*` 会将该 HTTP 方法的所有请求都转发到后端服务。

![URL 模板][api-management-url-template]

<a name="rewrite-url-template"></a>

如果需要，请指定“重写 URL 模板”。 这样既可在前端使用标准的 URL 模板处理传入的请求，同时通过根据重写模板转换的 URL 调用后端。 来自 URL 模板的模板参数应用在重写模板中。 下面的示例演示内容类型如何编码为上一示例 web 服务中的路径段，它可以提供作为通过使用 URL 模板的 API 管理平台发布的 API 中的查询参数。

![URL 模板重写][api-management-url-template-rewrite]

操作的调用方将使用格式 `/customers?customerid=ALFKI`，调用后端服务时这会映射到 `/Customers('ALFKI')`。

“显示”名称和“说明”提供操作的说明，并用于在开发人员门户中向使用此 API 的开发人员提供文档。

![说明][api-management-description]

操作说明可指定为“说明”文本框中的纯文本或 HTML。

## <a name="operation-caching"> </a>操作缓存
响应缓存可减少 API 使用者的延迟，降低带宽消耗并减少执行 API 的 HTTP web 服务上的负载。 

若要轻松快速地启用操作缓存，请选择“培训”选项卡并选中“启用”复选框。

![缓存][api-management-caching-tab]

“持续时间”指定在此期间操作响应保留在缓存中的时间段。 默认值为 3600 秒或 1 小时。

缓存键用于区分这两个响应，以便与每个不同缓存键对应的响应会获得自己单独的缓存值。 （可选）分别输入特定的查询字符串参数，和/或要在“通过查询字符串参数变化”和“按标头变化”文本框中的计算缓存键值的 HTTP 标头。 如果指定无，完整请求 URL 和以下 HTTP 标头值用在缓存密钥生成过程中：“接受”和“Accept-Charset”。

> 有关缓存和缓存策略的详细信息，请参阅[如何在 Azure API 管理中缓存操作结果][How to cache operation results in Azure API Management]。
> 
> 

## <a name="request-parameters"> </a>请求参数
在“参数”选项卡上管理操作参数。“签名”选项卡上的“URL 模板”中指定的参数会自动添加，并且只能在编辑 URL 模板时更改。 可以手动输入其他参数。

若要添加新的查询参数，请单击“添加查询参数”并输入以下信息：

* **名称** - 参数名称。
* **说明** - 参数的简短说明（可选）。
* **类型** - 参数类型，选择下拉菜单中选择。
* **值** - 可以分配给此参数的值。 其中一个值可被标记为默认（可选）。
* **所需** - 选中复选框让参数为必须。 

![请求参数][api-management-request-parameters]

## <a name="request-body"> </a>请求正文
如果允许该操作（例如 PUT、 POST）并且要求一个实例的正文，可采用受支持的表示形式格式(例如 json、 XML）提供一个示例。 

> 请求正文仅用于文档目的而并非验证。
> 
> 

若要输入请求正文，请切换到“正文”选项卡。

单击“添加表示”，开始键入所需的内容类型名称（例如应用程序/json），在下拉列表中选择它，并按所选格式将所需的请求正文示例粘贴到文本框。 

![请求正文][api-management-request-body]

除了表示形式，还可在“说明”文本框中指定一个可选文本说明。

## <a name="responses"> </a>响应
为操作可能生成的所有状态代码提供相应示例是不错的做法。 每个状态代码可能有多个响应正文示例，分别针对每种支持的内容类型。 

若要添加一个响应，请单击“添加”并开始键入所需的状态代码。 此示例中状态代码是 **200 OK**。 代码显示在下拉列表中后，选择它，创建响应代码并将其添加到操作。

![响应代码][api-management-response-code]

单击“添加表示形式”，开始键入所需的内容类型名称（例如 application/json），并在下拉菜单中选择它。

![正文内容类型][api-management-response-body-content-type]

将响应正文示例按所选格式粘贴到文本框中。 

![响应正文][api-management-response-body]

如果需要，请将可选说明添加到“说明”文本框。

配置该操作后，单击“保存”。

## <a name="next-steps"> </a>后续步骤
将操作添加到一个 API 后,下一步是将该 API 与产品相关联并将其发布，以便开发人员可调用其操作。

* [如何创建和发布产品][How to create and publish a product]

[api-management-management-console]: ./media/api-management-howto-add-operations/api-management-management-console.png
[api-management-operations]: ./media/api-management-howto-add-operations/api-management-operations.png
[api-management-add-operation]: ./media/api-management-howto-add-operations/api-management-add-operation.png
[api-management-http-method]: ./media/api-management-howto-add-operations/api-management-http-method.png
[api-management-url-template]: ./media/api-management-howto-add-operations/api-management-url-template.png
[api-management-url-template-rewrite]: ./media/api-management-howto-add-operations/api-management-url-template-rewrite.png
[api-management-description]: ./media/api-management-howto-add-operations/api-management-description.png
[api-management-caching-tab]: ./media/api-management-howto-add-operations/api-management-caching-tab.png
[api-management-request-parameters]: ./media/api-management-howto-add-operations/api-management-request-parameters.png
[api-management-request-body]: ./media/api-management-howto-add-operations/api-management-request-body.png
[api-management-response-code]: ./media/api-management-howto-add-operations/api-management-response-code.png
[api-management-response-body-content-type]: ./media/api-management-howto-add-operations/api-management-response-body-content-type.png
[api-management-response-body]: ./media/api-management-howto-add-operations/api-management-response-body.png


[api-management-contoso-api]: ./media/api-management-howto-add-operations/api-management-contoso-api.png

[api-management-add-new-api]: ./media/api-management-howto-add-operations/api-management-add-new-api.png
[api-management-api-settings]: ./media/api-management-howto-add-operations/api-management-api-settings.png
[api-management-api-settings-credentials]: ./media/api-management-howto-add-operations/api-management-api-settings-credentials.png
[api-management-api-summary]: ./media/api-management-howto-add-operations/api-management-api-summary.png
[api-management-echo-operations]: ./media/api-management-howto-add-operations/api-management-echo-operations.png

[Add an operation]: #add-operation
[Operation caching]: #operation-caching
[Request parameters]: #request-parameters
[Request body]: #request-body
[Responses]: #responses
[Next steps]: #next-steps

[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance

[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[How to cache operation results in Azure API Management]: api-management-howto-cache.md
