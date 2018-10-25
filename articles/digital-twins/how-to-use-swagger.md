---
title: 了解如何使用 Azure 数字孪生 Swagger | Microsoft Docs
description: 如何使用 Azure 数字孪生 Swagger
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: adgera
ms.openlocfilehash: dbadc90a206937d4c9f1d7b75a872d93b1a8a587
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323754"
---
# <a name="how-to-use-azure-digital-twins-swagger"></a>如何使用 Azure 数字孪生 Swagger

每个预配的 Azure 数字孪生实例都包含自己生成的 Swagger 参考文档。

[ Swagger ](https://swagger.io/)（或 [OpenAPI](https://www.openapis.org/)）将复杂的 API 信息整合到一个交互式和语言无关的参考资源中。 具体来说，Swagger 提供有关要使用哪些 JSON 有效负载、HTTP 方法和特定终结点对 API 执行操作的关键参考资料。

## <a name="swagger-summary"></a>Swagger 摘要

Swagger 提供 API 的交互式摘要，包括：

* API 和对象模型信息。
* 指定所需请求有效负载、标头、参数、上下文路径和 HTTP方法的 REST API 终结点。
* API 功能测试。
* 用于验证和确认 HTTP 响应的示例响应信息。
* 错误代码信息。

因此，Swagger 是非常便利的工具，可用于帮助执行开发和对管理 API 作出的测试调用。

> [!TIP]
> 有关参考，我们提供了 Swagger 非公开预览版，来演示 API 功能集。
> 它托管在 [ docs.westcentralus.azuresmartspaces.net/management/swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger) 中。

可以在以下位置访问自己生成的管理 API Swagger 文档：

```plaintext
https://yourInstanceName.yourLocation.azuresmartspaces.net/management/swagger
```

| 自定义属性名称 | 替换为 |
| --- | --- |
| `yourInstanceName` | Azure 数字孪生实例的名称 |
| `yourLocation` | 托管实例的服务器区域 |

## <a name="reference-material"></a>参考资料

自动生成的参考资料介绍了关键概念和对象模型。

精简摘要对 API 进行了描述：

![Swagger 顶部][1]

还列出了核心 API 对象模型：

![Swagger 模型][2]

单击每个列出的对象模型可获取关键属性的更详细摘要：

![Swagger 模型][3]

生成的 Swagger 对象模型可方便地用于查看所有可用的 Azure 数字孪生[对象和 API](./concepts-objectmodel-spatialgraph.md)。 这些模型是开发人员在 Azure 数字孪生上构建解决方案时使用的优秀资源。

## <a name="endpoint-summary"></a>终结点摘要

Swagger 还对编写 API 的所有终结点提供了全面概述。

每个列出的终结点还包括所需的请求信息，例如：

* 必需的参数。
* 必需的参数数据类型。
* 用于访问资源的 HTTP 方法。

![Swagger 终结点][4]

单击每个资源可查看更详细的概述。

## <a name="using-swagger-to-test-endpoints"></a>使用 Swagger 测试终结点

Swagger 提供的一个强大功能是能够试用或直接通过文档 UI 测试 API 终结点。

单击特定终结点后，会看到“试用”按钮：

![Swagger 试用][5]

展开该部分会显示每个必需字段和可选参数的输入字段。 输入相应的值，然后单击“执行”：

![已试用 Swagger][6]

执行测试后，可以验证响应数据。

## <a name="swagger-response-data"></a>Swagger 响应数据

每个列出的终结点还包括用以验证开发和测试的响应正文数据。 这些示例包括成功 HTTP 请求的所需状态代码和 JSON。

![Swagger 响应][7]

这些示例还包括用以帮助调试或改进失败测试的错误代码。

## <a name="swagger-oauth-20-authorization"></a>Swagger OAuth 2.0 授权

要针对 OAuth 2.0 保护的 API 资源以交互方式测试请求，请参阅[官方文档](https://swagger.io/docs/specification/authentication/oauth2/)。

## <a name="next-steps"></a>后续步骤

要了解有关 Azure 数字孪生对象模型和空间智能图的更多信息，请阅读[本文](./concepts-objectmodel-spatialgraph.md)。

要了解如何使用管理 API 进行身份验证，请阅读[使用 API 进行身份验证](./security-authenticating-apis.md)。

<!-- Images -->
[1]: media/how-to-use-swagger/swagger_management_top.png
[2]: media/how-to-use-swagger/swagger_management_models.png
[3]: media/how-to-use-swagger/swagger_management_model.png
[4]: media/how-to-use-swagger/swagger_management_endpoints.png
[5]: media/how-to-use-swagger/swagger_management_try.png
[6]: media/how-to-use-swagger/swagger_management_tried.png
[7]: media/how-to-use-swagger/swagger_management_response.png
