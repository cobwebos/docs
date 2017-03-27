---
title: "如何在 Azure API 管理中创建和发布产品"
description: "了解如何在 Azure API 管理中创建和发布产品。"
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 31de55cb-9384-490b-a2f2-6dfcf83da764
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: 30ec6f45da114b6c7bc081f8a2df46f037de61fd
ms.openlocfilehash: 1568bbba999018e26493654e0286a6b8d93f3ae7


---
# <a name="how-to-create-and-publish-a-product-in-azure-api-management"></a>如何在 Azure API 管理中创建和发布产品
在 Azure API 管理中，产品包含一个或多个 API 以及使用配额和使用条款。 一旦产品发布，开发人员可以订阅该产品，并开始使用产品的 API。 本主题为开发人员提供创建产品、添加一个 API 以及将其发布的指南。

## <a name="create-product"> </a>创建产品
在发布者门户中将操作添加并配置到 API。 若要访问发布者门户，请在 API 管理服务的 Azure 门户中单击“发布者门户”。

![发布者门户][api-management-management-console]

> 如果尚未创建 API 管理服务实例，请参阅 [Azure API 管理入门][Get started with Azure API Management]教程中的[创建 API 管理服务实例][Create an API Management service instance]。
> 
> 

单击左侧菜单中的“产品”显示“产品”页，然后单击“添加产品”。

![产品][api-management-products]

![新产品][api-management-add-new-product]

在“名称”字段中输入产品的描述性名称，在“说明”字段中输入产品的说明。

API 管理中的产品可处于“打开”或“受保护”状态。 必须先订阅受保护的产品，然后才能使用它们，但公开的产品无需订阅即可使用。 选中“需要订阅”创建需要订阅的受保护产品。 此设置为默认设置。

如果希望管理员审查并接受或拒绝此产品的订阅尝试，请选中“需要订阅审批”。 如果未选中此框，则将自动批准订阅尝试。 有关订阅的详细信息，请参阅[查看产品的订户][View subscribers to a product]。

若要允许开发人员帐户多次订阅产品，请选中“允许多个订阅”复选框。 如果未选中此框，则每个开发人员帐户只能订阅产品一次。

![无限多个订阅][api-management-unlimited-multiple-subscriptions]

若要限制多个同时订阅的数量，请选中“将同时订阅数量限制为”复选框并输入订阅限制。 在下面的示例中，每个开发人员帐户仅限四个同时订阅。

![四个多个订阅][api-management-four-multiple-subscriptions]

配置所有新产品选项后，单击“保存”创建新产品。

![产品][api-management-products-page]

> 默认情况下新产品未发布，并仅对“管理员”组可见。
> 
> 

若要配置产品，请单击“产品”选项卡中的产品名称。

## <a name="add-apis"> </a>将 API 添加到产品
“产品”页包含四个配置链接：“摘要”、“设置”、“可见性”和“订户”。 “摘要”选项卡是可在其中添加 API 和发布或取消发布产品的位置。

![摘要][api-management-new-product-summary]

在发布您的产品前，您需要添加一个或多个 API。 若要执行此操作，请单击“将 API 添加到产品”。

![添加 API][api-management-add-apis-to-product]

选择所需的 API，然后单击“保存”。

## <a name="add-description"> </a>将描述性产品添加到产品
“设置”选项卡允许提供有关该产品的详细的信息，如其用途，它提供访问权的 API 和其他有用的信息。 内容针对调用 API 的开发人员，并可采用纯文本或 HTML 标记编写。

![产品设置][api-management-product-settings]

选中“需要订阅”创建需要使用订阅的受保护产品，或清除该复选框以创建可在没有订阅的情况下调用的打开产品。

如果要手动批准所有产品订阅请求，请选中“需要订阅批准”。 默认情况下会自动授予所有产品订阅。

若要允许开发人员帐户多次订阅产品，请选中“允许多个订阅”复选框并（可选）指定限制。 如果未选中此框，则每个开发人员帐户只能订阅产品一次。

选择填写“使用条款”字段描述必须接受哪些订户才能使用该产品的使用条款。

## <a name="publish-product"> </a>发布产品
在产品中的 API 可调用前，必须先发布该产品。 在产品的“摘要”选项卡上，单击“发布”，然后单击“是，发布以确认”。 若要将以前发布的产品专用，请单击“取消发布”。

![发布产品][api-management-publish-product]

## <a name="make-visible"> </a>使产品对开发人员可见
“可见性”选项卡允许选择可在开发人员门户网站上查看该产品并订阅该产品的角色。

![产品可见性][api-management-product-visiblity]

若要启用或禁用组中对开发人员的产品的可见性，请选中或取消选中组旁边的复选框，然后单击“保存”。

> 有关详细信息，请参阅[如何创建和使用组以管理 Azure API 管理中的开发人员帐户][How to create and use groups to manage developer accounts in Azure API Management]。
> 
> 

## <a name="view-subscribers"> </a>查看产品的订户
“订户”选项卡列出订阅产品的开发人员。 通过单击开发人员的名称，可以查看每个开发人员的详细信息和设置。 此示例中还没有开发人员订阅该产品。

![开发人员][api-management-developer-list]

## <a name="next-steps"> </a>后续步骤
一旦添加所需的 API 且产品发布，开发人员可以订阅该产品并开始调用 API。 有关演示这些项目以及高级产品配置的教程，请参阅[如何在 Azure API 管理中创建和配置高级产品设置][How create and configure advanced product settings in Azure API Management]。

有关演练产品的详细信息，请参阅以下视频。

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Using-Products/player]
> 
> 

[Create a product]: #create-product
[Add APIs to a product]: #add-apis
[Add descriptive information to a product]: #add-description
[Publish a product]: #publish-product
[Make a product visible to developers]: #make-visible
[View subscribers to a product]: #view-subscribers
[Next steps]: #next-steps

[api-management-management-console]: ./media/api-management-howto-add-products/api-management-management-console.png
[api-management-add-product]: ./media/api-management-howto-add-products/api-management-add-product.png
[api-management-add-new-product]: ./media/api-management-howto-add-products/api-management-add-new-product.png
[api-management-unlimited-multiple-subscriptions]: ./media/api-management-howto-add-products/api-management-unlimited-multiple-subscriptions.png
[api-management-four-multiple-subscriptions]: ./media/api-management-howto-add-products/api-management-four-multiple-subscriptions.png
[api-management-products-page]: ./media/api-management-howto-add-products/api-management-products-page.png
[api-management-new-product-summary]: ./media/api-management-howto-add-products/api-management-new-product-summary.png
[api-management-add-apis-to-product]: ./media/api-management-howto-add-products/api-management-add-apis-to-product.png
[api-management-product-settings]: ./media/api-management-howto-add-products/api-management-product-settings.png
[api-management-publish-product]: ./media/api-management-howto-add-products/api-management-publish-product.png
[api-management-product-visiblity]: ./media/api-management-howto-add-products/api-management-product-visibility.png
[api-management-developer-list]: ./media/api-management-howto-add-products/api-management-developer-list.png



[api-management-products]: ./media/api-management-howto-add-products/api-management-products.png
[api-management-]: ./media/api-management-howto-add-products/
[api-management-]: ./media/api-management-howto-add-products/


[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[Next steps]: #next-steps
[How to create and use groups to manage developer accounts in Azure API Management]: api-management-howto-create-groups.md
[How create and configure advanced product settings in Azure API Management]: api-management-howto-product-with-rules.md 



<!--HONumber=Dec16_HO3-->


