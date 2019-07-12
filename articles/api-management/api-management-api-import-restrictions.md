---
title: Azure API 管理 API 导入中的限制和已知问题 | Microsoft Docs
description: 有关使用 Open API、WSDL 或 WADL 格式导入到 Azure API 管理的已知问题和限制的详细信息。
services: api-management
documentationcenter: ''
author: vladvino
manager: vlvinogr
editor: ''
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2019
ms.author: apimpm
ms.openlocfilehash: af550d3cdf359fc79b3cc2c799e531e5ec491c4e
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2019
ms.locfileid: "67613632"
---
# <a name="api-import-restrictions-and-known-issues"></a>API 导入限制和已知问题

## <a name="about-this-list"></a>关于此列表

导入 API 时，可能会遇到一些限制或识别问题，需要对其进行纠正才能成功导入。 本文记录了这些限制或问题，并按照 API 的导入格式对其进行了组织。

## <a name="open-api"> </a>OpenAPI/Swagger

如果在导入 OpenAPI 文档时收到错误，请确保事先已对其进行了验证。 可以使用 Azure 门户中的设计器（设计 - 前端 - OpenAPI 规范编辑器）或使用第三方工具（例如 <a href="https://editor.swagger.io">Swagger 编辑器</a>）进行验证。

### <a name="open-api-general"> </a>常规

-   路径和查询所需的参数必须具有唯一名称。 （在 OpenAPI 中，参数名称只需要在一个位置内是唯一的，例如路径、查询、标头。 但是，在 API 管理中，我们允许操作通过路径和查询参数进行区分（OpenAPI 不支持此方法）。 这就是要求参数名称在整个 URL 模板中是唯一的原因。）
-   **\$ref**指针不能引用外部文件。
-   仅支持 **x-ms-paths** 和 **x-servers** 扩展。
-   自定义扩展在导入时将被忽略，并且不会为导出保存或保留。
-   **递归** - API 管理目前不支持以递归方式定义的定义（例如，引用自身的架构）。
-   源文件 URL（如果可用）应用于相对服务器 URL。

### <a name="open-api-v2"> </a>OpenAPI 版本 2

-   仅支持 JSON 格式。

### <a name="open-api-v3"> </a>OpenAPI 版本 3

-   如果指定了多个服务器，API 管理将尝试选择第一个 HTTP URL  。 如果不存在任何 HTTP URL，则为第一个 HTTP URL。 如果不存在任何 HTTP URL，则服务器 URL 将为空。
-   不支持“Examples”，但支持“example”   。
-   不支持“Multipart/form-data”  。

> [!IMPORTANT]
> 如需与 OpenAPI 导入相关的重要信息和提示，请参阅此[文档](https://blogs.msdn.microsoft.com/apimanagement/2018/04/11/important-changes-to-openapi-import-and-export/)。

## <a name="wsdl"></a>WSDL

WSDL 文件用于创建 SOAP 直通和 SOAP 到 REST Api。

-   **SOAP 绑定** - 仅支持样式“文档”和“文本”编码的 SOAP 绑定。 不支持“rpc”样式或 SOAP 编码。
-   **WSDL:Import** - 不支持此属性。 客户应将导入项合并到一个文档中。
-   **包含多个部分的消息** - 不支持这些类型的消息。
-   **WCF wsHttpBinding** - 使用 Windows Communication Foundation 创建的 SOAP 服务应使用 basicHttpBinding - 不支持 wsHttpBinding。
-   **MTOM** - 使用 MTOM 的服务<em>可能</em>正常工作。 目前暂未提供官方支持。
-   **递归** - APIM 不支持以递归方式定义的类型（例如，引用这些类型本身的数组）。
-   **多个命名空间** - 可以在架构中使用多个名称空间，但只能使用目标名称空间来定义消息部分。 不保留用于定义其他输入或输出元素的目标以外的命名空间。 虽然可以导入这样的 WSDL 文档，但在导出时，所有消息部分都将具有 WSDL 的目标命名空间。
-   **数组**-SOAP 到 REST 转换支持仅包装数组在下面的示例所示：

```xml
    <complexType name="arrayTypeName">
        <sequence>
            <element name="arrayElementValue" type="arrayElementType" minOccurs="0" maxOccurs="unbounded"/>
        </sequence>
    </complexType>
    <complexType name="typeName">
        <sequence>
            <element name="element1" type="someTypeName" minOccurs="1" maxOccurs="1"/>
            <element name="element2" type="someOtherTypeName" minOccurs="0" maxOccurs="1" nillable="true"/>
            <element name="arrayElement" type="arrayTypeName" minOccurs="1" maxOccurs="1"/>
        </sequence>
    </complexType>
```

## <a name="wadl"></a>WADL

目前没有已知的 WADL 导入问题。
