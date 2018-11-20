---
title: 了解如何使用 Azure 数字孪生 Swagger | Microsoft Docs
description: 使用 Azure 数字孪生 Swagger
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: adgera
ms.openlocfilehash: 737c33f6b8cdf9bcb2530816601ff9b5eb994087
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2018
ms.locfileid: "51624244"
---
# <a name="use-azure-digital-twins-swagger"></a>使用 Azure 数字孪生 Swagger

每个预配的 Azure 数字孪生实例都包含自己生成的 Swagger 参考文档。

[Swagger](https://swagger.io/) 或 [OpenAPI](https://www.openapis.org/) 将复杂的 API 信息整合到一个交互式和语言无关的参考资源中。 Swagger 提供有关要使用哪些 JSON 有效负载、HTTP 方法和特定终结点对 API 执行操作的关键参考资料。

> [!IMPORTANT]
> 在公开预览期间暂时禁用对 Swagger 身份验证的支持。

## <a name="swagger-summary"></a>Swagger 摘要

Swagger 提供 API 的交互式摘要，这包括：

* API 和对象模型信息。
* REST API 终结点，用于指定必需的请求有效负载、标头、参数、上下文路径和 HTTP 方法。
* API 功能测试。
* 示例响应信息，用于验证和确认 HTTP 响应。
* 错误代码信息。

Swagger 是一种非常便利的工具，可用于帮助执行开发和对管理 API 作出的测试调用。

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

## <a name="reference-material"></a>参考资料

自动生成的参考资料介绍了关键概念和对象模型。

精简摘要对 API 进行了描述。

![Swagger 顶部][1]

还列出了核心 API 对象模型。

![Swagger 模型][2]

可以选择每个列出的对象模型来获取关键属性的更详细摘要：

![Swagger 模型][3]

生成的 Swagger 对象模型可方便地用于查看所有可用的 Azure 数字孪生[对象和 API](./concepts-objectmodel-spatialgraph.md)。 在 Azure 数字孪生上构建解决方案时，开发人员可以使用此资源。

## <a name="endpoint-summary"></a>终结点摘要

Swagger 还对编写 API 的所有终结点提供了全面概述。

每个列出的终结点还包括所需的请求信息，例如：

* 必需的参数。
* 必需的参数数据类型。
* 用于访问资源的 HTTP 方法。

![Swagger 终结点][4]

若要查看更详细的概述，请选择每个资源。

## <a name="use-swagger-to-test-endpoints"></a>使用 Swagger 测试终结点

Swagger 提供的一个强大功能是能够直接通过文档 UI 测试 API 终结点。

选择特定的终结点后，你会看到**试用**。

![Swagger 试用][5]

展开该部分会显示每个必需字段和可选参数的输入字段。 输入相应的值，然后选择“执行”。

![已试用 Swagger][6]

执行测试后，可以验证响应数据。

## <a name="swagger-response-data"></a>Swagger 响应数据

每个列出的终结点还包括用以验证开发和测试的响应正文数据。 这些示例包括对于成功的 HTTP 请求你希望看到的状态代码和 JSON。

![Swagger 响应][7]

这些示例还包括用以帮助调试或改进失败测试的错误代码。

## <a name="swagger-oauth-20-authorization"></a>Swagger OAuth 2.0 授权

若要详细了解如何以交互方式测试受 OAuth 2.0 保护的请求，请参阅[官方文档](https://swagger.io/docs/specification/authentication/oauth2/)。

> [!NOTE]
> 在公开预览期间暂时禁用对 OAuth 2.0 身份验证的支持。

## <a name="next-steps"></a>后续步骤

若要阅读有关 Azure 数字孪生对象模型和空间智能图的详细信息，请阅读[了解 Azure 数字孪生对象模型](./concepts-objectmodel-spatialgraph.md)。

若要了解如何使用管理 API 进行身份验证，请阅读[使用 API 进行身份验证](./security-authenticating-apis.md)。

<!-- Images -->
[1]: media/how-to-use-swagger/swagger_management_top.PNG
[2]: media/how-to-use-swagger/swagger_management_models.PNG
[3]: media/how-to-use-swagger/swagger_management_model.PNG
[4]: media/how-to-use-swagger/swagger_management_endpoints.PNG
[5]: media/how-to-use-swagger/swagger_management_try.PNG
[6]: media/how-to-use-swagger/swagger_management_tried.PNG
[7]: media/how-to-use-swagger/swagger_management_response.PNG
