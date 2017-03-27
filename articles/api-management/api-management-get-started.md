---
title: "在 Azure API 管理中管理第一个 API | Microsoft Docs"
description: "了解如何创建 API、添加操作，以及如何开始使用 API 管理。"
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 51b7df8b-1c43-43c6-90c9-0aa24f48206b
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/15/2016
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: 30ec6f45da114b6c7bc081f8a2df46f037de61fd
ms.openlocfilehash: 6376faa50613288a797e2c0683a0890fa21879e1


---
# <a name="manage-your-first-api-in-azure-api-management"></a>在 Azure API 管理中管理第一个 API
## <a name="overview"> </a>概述
本指南演示如何快速开始使用 Azure API 管理，并进行首次 API 调用。

## <a name="concepts"> </a>什么是 Azure API 管理？
可以使用 Azure API 管理处理任何后端，并基于它发布正式的 API 程序。

常见方案包括：

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

## <a name="create-service-instance"> </a>创建 API 管理实例
> [!NOTE]
> 若要完成本教程，你需要一个 Azure 帐户。 如果没有帐户，只需花费几分钟就能创建一个免费帐户。 有关详细信息，请参阅 [Azure 免费试用][Azure Free Trial]。
> 
> 

使用 API 管理的第一步是创建一个服务实例。 登录到 [Azure 门户][Azure Portal]，单击“新建”、“Web + 移动”、“API 管理”。

![API 管理新实例][api-management-create-instance-menu]

对于“名称”，指定一个唯一的子域名用于服务 URL。

为服务实例选择所需的**订阅**、**资源组**和**位置**。

输入 **Contoso Ltd.** 作为**组织名称**，并在“管理员电子邮件”字段中输入电子邮件地址。

> [!NOTE]
> 此电子邮件地址用于来自 API 管理系统的通知。 有关详细信息，请参阅[如何在 Azure API 管理中配置通知和电子邮件模板][How to configure notifications and email templates in Azure API Management]。
> 
> 

![新的 API 管理服务][api-management-create-instance-step1]

API 管理服务实例在以下三层中提供：开发人员、标准和高级。

> [!NOTE]
> “开发人员”层用于开发、测试和试验 API 程序，在其中高可用性不是需要考虑的因素。 在“标准”和“高级”层中，可以扩展保留单位计数以处理更多流量。 “标准”和“高级”层为 API 管理服务提供最多处理能力和性能。 可以使用任何层完成本教程。 有关 API 管理层的详细信息，请参阅 [API 管理定价][API Management pricing]。
> 
> 

单击“创建”开始预配服务实例。

![新的 API 管理服务][api-management-instance-created]

创建服务实例后，下一步是创建或导入 API。

## <a name="create-api"> </a>导入 API
API 包含一组可以从客户端应用程序调用的操作。 API 操作代理到现有的 web 服务。

可以手动创建 API（可以添加操作），也可以导入它们。 在本教程中，将为 Microsoft 提供的示例计算器 Web 服务导入 API，并在 Azure 上托管它。

> [!NOTE]
> 有关创建 API 和手动添加操作的指导，请参阅[如何创建 API](api-management-howto-create-apis.md) 和[如何将操作添加到 API](api-management-howto-add-operations.md)。
> 
> 

可从发布者门户配置 API。 若要访问它，请单击服务工具栏中的“发布者门户”。

![发布者门户][api-management-management-console]

若要导入计算器 API，请单击左侧“API 管理”菜单中的“API”，然后单击“导入 API”。

![“导入 API”按钮][api-management-import-api]

执行以下步骤以配置计算器 API：

1. 单击“来自 URL”，在“规范文档 URL”文本框中输入 **http://calcapi.cloudapp.net/calcapi.json**，然后单击“Swagger”单选按钮。
2. 在“Web API URL 后缀”文本框中键入“calc”。
3. 单击“产品(可选)”框，然后选择“初学者”。
4. 单击“保存”导入 API  。

![添加新的 API][api-management-import-new-api]

> [!NOTE]
> **API 管理** 目前支持导入 Swagger 文档 1.2 和 2.0 版本。 请确保，即使 [Swagger 2.0 规范](http://swagger.io/specification)声明 `host`、`basePath` 和 `schemes` 属性是可选的，Swagger 2.0 文档也**必须**包含这些属性，否则不会导入它。 
> 
> 

导入 API 后，该 API 的摘要页将显示在发布者门户中。

![API 摘要][api-management-imported-api-summary]

API 部分有几个选项卡。 “摘要”选项卡显示有关 API 的基本指标和信息。 [设置](api-management-howto-create-apis.md#configure-api-settings)选项卡用于查看和编辑 API 的配置。 [操作](api-management-howto-add-operations.md)选项卡用于管理 API 的操作。 “安全性”选项卡可用于使用基本身份验证或[相互进行的证书身份验证](api-management-howto-mutual-certificates.md)为后端服务器配置网关身份验证，以及配置[使用 OAuth 2.0 进行用户授权](api-management-howto-oauth2.md)。  “问题”选项卡用于查看使用 API 的开发人员报告的问题。 “产品”选项卡用于配置包含此 API 的产品。

默认情况下，每个 API 管理实例附带两个示例产品：

* **入门**
* **不受限制**

在本教程中，导入 API 时已将基本计算器 API 添加到初学者产品中。

为了调用 API，开发人员必须先订阅一个产品，以提供给他们对 API 的访问权。 开发人员可以在开发人员门户中订阅产品，管理员可以在发布者门户中为开发人员订阅产品。 你是管理员，因为你在本教程的前面步骤中已创建 API 管理实例，所以默认情况下你已订阅每个产品。

## <a name="call-operation"> </a>从开发人员门户调用操作
可以直接从开发人员门户调用操作，这样可以方便地查看和测试 API 的操作。 在此教程步骤中，将调用基本计算器 API 的“添加两个整数”  操作。 单击发布者门户右上角菜单中的“开发人员门户”  。

![开发人员门户][api-management-developer-portal-menu]

单击顶部菜单中的“API”，然后单击“基本计算器”以查看可用的操作。

![开发人员门户][api-management-developer-portal-calc-api]

请注意，已随 API 和操作导入的示例说明和参数，将为使用此操作的开发人员提供文档。 手动添加操作时，也可以添加这些说明。

若要调用“添加两个整数”操作，请单击“试用”。

![试用][api-management-developer-portal-calc-api-console]

可以为参数输入一些值或保留默认值，然后单击“发送” 。

![HTTP Get][api-management-invoke-get]

调用操作后，开发人员门户将显示**响应状态**、**响应标头**以及任何**响应内容**。

![响应][api-management-invoke-get-response]

## <a name="view-analytics"> </a>查看分析
若要查看基本计算器的分析，请选择开发人员门户右上角菜单中的“管理”，切换回发布者门户  。

![管理][api-management-manage-menu]

发布者门户的默认视图是 **仪表板**，它提供 API 管理实例的概述。

![仪表板][api-management-dashboard]

将鼠标悬停在 **基本计算器** 的图表上，以查看给定时间段内 API 使用情况的特定度量值。

> [!NOTE]
> 如果未在图表上看到任何行，请切换回开发人员门户并对 API 进行一些调用，稍等片刻再返回仪表板。
> 
> 

单击“查看详细信息”  以查看 API 的摘要页，包括所显示度量的更大版本。

![分析][api-management-mouse-over]

![摘要][api-management-api-summary-metrics]

有关详细的度量和报告，请单击左侧“API 管理”菜单中的“分析”。

![概述][api-management-analytics-overview]

“分析”部分包含以下四个选项卡  ：

*  。
*  。
*  。
*  。

## <a name="next-steps"> </a>后续步骤
* 了解如何 [使用速率限制保护 API](api-management-howto-product-with-rules.md)。

[Azure Free Trial]: http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=api_management_hero_a

[Create an API Management instance]: #create-service-instance
[Create an API]: #create-api
[Add an operation]: #add-operation
[Add the new API to a product]: #add-api-to-product
[Subscribe to the product that contains the API]: #subscribe
[Call an operation from the Developer Portal]: #call-operation
[View analytics]: #view-analytics
[Next steps]: #next-steps


[How to manage developer accounts in Azure API Management]: api-management-howto-create-or-invite-developers.md
[Configure API settings]: api-management-howto-create-apis.md#configure-api-settings
[How to configure notifications and email templates in Azure API Management]: api-management-howto-configure-notifications.md
[Responses]: api-management-howto-add-operations.md#responses
[How create and publish a product]: api-management-howto-add-products.md
[API Management pricing]: http://azure.microsoft.com/pricing/details/api-management/

[Azure Portal]: https://portal.azure.com/

[api-management-management-console]: ./media/api-management-get-started/api-management-management-console.png
[api-management-create-instance-menu]: ./media/api-management-get-started/api-management-create-instance-menu.png
[api-management-create-instance-step1]: ./media/api-management-get-started/api-management-create-instance-step1.png
[api-management-create-instance-step2]: ./media/api-management-get-started/api-management-create-instance-step2.png
[api-management-instance-created]: ./media/api-management-get-started/api-management-instance-created.png
[api-management-import-api]: ./media/api-management-get-started/api-management-import-api.png
[api-management-import-new-api]: ./media/api-management-get-started/api-management-import-new-api.png
[api-management-imported-api-summary]: ./media/api-management-get-started/api-management-imported-api-summary.png
[api-management-calc-operations]: ./media/api-management-get-started/api-management-calc-operations.png
[api-management-list-products]: ./media/api-management-get-started/api-management-list-products.png
[api-management-add-api-to-product]: ./media/api-management-get-started/api-management-add-api-to-product.png
[api-management-add-myechoapi-to-product]: ./media/api-management-get-started/api-management-add-myechoapi-to-product.png
[api-management-api-added-to-product]: ./media/api-management-get-started/api-management-api-added-to-product.png
[api-management-developers]: ./media/api-management-get-started/api-management-developers.png
[api-management-add-subscription]: ./media/api-management-get-started/api-management-add-subscription.png
[api-management-add-subscription-window]: ./media/api-management-get-started/api-management-add-subscription-window.png
[api-management-subscription-added]: ./media/api-management-get-started/api-management-subscription-added.png
[api-management-developer-portal-menu]: ./media/api-management-get-started/api-management-developer-portal-menu.png
[api-management-developer-portal-calc-api]: ./media/api-management-get-started/api-management-developer-portal-calc-api.png
[api-management-developer-portal-calc-api-console]: ./media/api-management-get-started/api-management-developer-portal-calc-api-console.png
[api-management-invoke-get]: ./media/api-management-get-started/api-management-invoke-get.png
[api-management-invoke-get-response]: ./media/api-management-get-started/api-management-invoke-get-response.png
[api-management-manage-menu]: ./media/api-management-get-started/api-management-manage-menu.png
[api-management-dashboard]: ./media/api-management-get-started/api-management-dashboard.png

[api-management-add-response]: ./media/api-management-get-started/api-management-add-response.png
[api-management-add-response-window]: ./media/api-management-get-started/api-management-add-response-window.png
[api-management-developer-key]: ./media/api-management-get-started/api-management-developer-key.png
[api-management-mouse-over]: ./media/api-management-get-started/api-management-mouse-over.png
[api-management-api-summary-metrics]: ./media/api-management-get-started/api-management-api-summary-metrics.png
[api-management-analytics-overview]: ./media/api-management-get-started/api-management-analytics-overview.png
[api-management-analytics-usage]: ./media/api-management-get-started/api-management-analytics-usage.png
[api-management-]: ./media/api-management-get-started/api-management-.png
[api-management-]: ./media/api-management-get-started/api-management-.png



<!--HONumber=Dec16_HO3-->


