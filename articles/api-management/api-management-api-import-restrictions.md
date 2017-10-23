---
title: "Azure API 管理 API 导入中的限制和已知问题 | Microsoft Docs"
description: "有关使用 Open API、WSDL 或 WADL 格式导入到 Azure API 管理的已知问题和限制的详细信息。"
services: api-management
documentationcenter: 
author: vladvino
manager: vlvinogr
editor: 
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: apipm
ms.openlocfilehash: 4cb6ad53b59b81f906a85027f4ff988bbb78706a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="api-import-restrictions-and-known-issues"></a>API 导入限制和已知问题
## <a name="about-this-list"></a>关于此列表
尽管我们尽一切努力确保将 API 导入到 Azure API 管理的过程尽可能流畅且不出问题，但偶尔也会强制实施限制或识别需要纠正的问题，以便导入成功。 本文记录了这些问题，并按照 API 的导入格式进行了分类。

## <a name="open-api"></a>Open API/Swagger
一般来说，如果在导入 Open API 文档时遇到错误，请确保已通过使用新版 Azure 门户（设计 - 前端 - Open API 规范编辑器）中的设计器或使用第三方工具（例如 <a href="http://www.swagger.io">Swagger 编辑器</a>）对其进行了验证。

* **主机名** 需要主机名属性。
* **基路径** 需要基路径属性。
* **方案** 需要方案数组。 

## <a name="wsdl"></a>WSDL
WSDL 文件用于生成 SOAP 直通 API，或用作 SOAP 到 REST API 的后端。

* **WSDL:Import** 目前不支持使用该属性的 API。 客户应将导入的元素合并到一个文档中。
* 目前不支持**包含多个部分的消息**。
* **WCF wsHttpBinding** 使用 Windows Communication Foundation 创建的 SOAP 服务应使用 basicHttpBinding - 不支持 wsHttpBinding。
* **MTOM** 使用 MTOM 的服务<em>可能</em>工作。 目前暂未提供官方支持。
* 不支持以递归方式定义**递归**类型（例如，引用这些类型本身的数组）。

## <a name="wadl"></a>WADL
目前没有已知的 WADL 导入问题。


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
