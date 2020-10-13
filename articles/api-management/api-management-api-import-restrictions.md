---
title: API 格式支持的限制和详细信息
titleSuffix: Azure API Management
description: 有关 Azure API 管理中 OpenAPI、WSDL 和 WADL 格式支持的已知问题和限制详细信息。
services: api-management
documentationcenter: ''
author: vladvino
manager: vlvinogr
editor: ''
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/02/2020
ms.author: apimpm
ms.openlocfilehash: 6a53cc2b2ec6d46b4bde54af58b4e5542ff6cf79
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91932628"
---
# <a name="api-import-restrictions-and-known-issues"></a>API 导入限制和已知问题

## <a name="about-this-list"></a>关于此列表

导入 API 时，可能会遇到一些限制或识别问题，需要对其进行纠正才能成功执行导入。 本文将阐述这些限制，内容按照 API 的导入格式进行组织。 本文还将介绍 OpenAPI 导出的工作原理。

## <a name="openapiswagger-import-limitations"></a><a name="open-api"> </a>OpenAPI/Swagger 导入限制

如果在导入 OpenAPI 文档时收到错误，请确保事先已对其进行了验证。 可以使用 Azure 门户中的设计器（设计 - 前端 - OpenAPI 规范编辑器）或使用第三方工具（例如 <a href="https://editor.swagger.io">Swagger 编辑器</a>）进行验证。

### <a name="general"></a><a name="open-api-general"> </a>总则

-   路径和查询所需的参数必须具有唯一名称。 （在 OpenAPI 中，参数名称只需要在一个位置内是唯一的，例如路径、查询、标头。 但是，在 API 管理中，我们允许操作通过路径和查询参数进行区分（OpenAPI 不支持此方法）。 这就是要求参数名称在整个 URL 模板中是唯一的原因。）
-   `\$ref` 指针不能引用外部文件。
-   `x-ms-paths` 和 `x-servers` 是唯一受支持的扩展。
-   自定义扩展在导入时将被忽略，并且不会为导出保存或保留。
-   `Recursion` -API 管理不支持以递归方式定义的定义 (例如，引用自身的架构) 。
-   源文件 URL（如果可用）应用于相对服务器 URL。
-   忽略安全定义。
-   不支持 API 操作的内联架构定义。 架构定义在 API 范围内定义，可在 API 操作请求或响应范围内引用。
-   定义的 URL 参数需要是 URL 模板的一部分。
-   `server` API 操作级别上不支持对象。
-   `Produces` 关键字（描述 API 返回的 MIME 类型）不受支持。 

### <a name="openapi-version-2"></a><a name="open-api-v2"> </a>OpenAPI 版本 2

-   仅支持 JSON 格式。

### <a name="openapi-version-3"></a><a name="open-api-v3"> </a>OpenAPI 版本 3

-   如果 `servers` 指定了多个，则 API 管理将尝试选择第一个 HTTPS URL。 如果不存在任何 HTTP URL，则为第一个 HTTP URL。 如果不存在任何 HTTP URL，则服务器 URL 将为空。
-   `Examples` 不支持，但 `example` 为。

## <a name="openapi-import-update-and-export-mechanisms"></a>OpenAPI 导入、更新和导出机制

### <a name="general"></a><a name="open-import-export-general"> </a>常规

-   从 API 管理服务导出的 API 定义主要适用于需要调用 api 管理服务中托管的 API 的 API 管理服务的外部应用程序。 导出的 API 定义不应重新导入到相同或不同的 API 管理服务中。 对于跨不同 serivces/envionments 的 API defiitions 的配置管理，请参阅有关使用带有 Git 的 API 管理服务的文档。 

### <a name="add-new-api-via-openapi-import"></a>通过 OpenAPI 导入添加新 API

对于在 OpenAPI 文档中找到的每个操作，将使用 Azure 资源名称和显示名称分别设置为 `operationId` 和 `summary` 。 `operationId` 按照下述规则规范化值。 `summary` 值按原样导入，其长度限制为300个字符。

如果未 `operationId` 指定 (（不存在、 `null` 或空) ），则将通过组合 HTTP 方法和路径模板生成 Azure 资源名称值，例如 `get-foo` 。

如果未 `summary` 指定 (即不存在、 `null` 或空) ，则 `display name` 值将设置为 `operationId` 。 如果 `operationId` 未指定，则通过组合 HTTP 方法和路径模板生成显示名称值，例如 `Get - /foo` 。

### <a name="update-an-existing-api-via-openapi-import"></a>通过 OpenAPI 导入更新现有 API

在导入过程中，将更改 OpenAPI 文档中所述的 API。 OpenAPI 文档中的每个操作都通过将其 `operationId` 值与现有操作的 Azure 资源名称进行比较来匹配现有操作。

如果找到匹配项，现有操作的属性将以 "就地" 更新。

如果找不到匹配项，则将使用上述部分中描述的规则创建新操作。 对于每个新操作，导入将尝试从具有相同 HTTP 方法和路径模板的现有操作复制策略。

将删除所有现有的不匹配操作。

若要使导入更具可预测性，请遵循以下准则：

- 请确保为 `operationId` 每个操作指定属性。
- 避免 `operationId` 在初始导入后发生更改。
- 切勿 `operationId` 同时更改和 HTTP 方法或路径模板。

### <a name="export-api-as-openapi"></a>将 API 导出为 OpenAPI

对于每个操作，其 Azure 资源名称将导出为 `operationId` ，显示名称将导出为 `summary` 。
OperationId 的规范化规则

- 转换为小写字母。
- 将每个非字母数字字符序列替换为单个破折号，例如， `GET-/foo/{bar}?buzz={quix}` 将转换为 `get-foo-bar-buzz-quix-` 。
- 例如，在两侧剪裁虚线 `get-foo-bar-buzz-quix-` 将变为 `get-foo-bar-buzz-quix`
- 截断以容纳76个字符，4个字符小于资源名称的最大限制。
- 如果需要，请使用其余四个字符（如有必要） `-1, -2, ..., -999` 。


## <a name="wsdl"></a><a name="wsdl"> </a>WSDL

WSDL 文件用于创建 SOAP 传递和 SOAP到 REST API。

-   **SOAP 绑定** - 仅支持样式“文档”和“文本”编码的 SOAP 绑定。 不支持“rpc”样式或 SOAP 编码。
-   **WSDL:Import** - 不支持此属性。 客户应将导入项合并到一个文档中。
-   **包含多个部分的消息** - 不支持这些类型的消息。
-   **WCF wsHttpBinding** - 使用 Windows Communication Foundation 创建的 SOAP 服务应使用 basicHttpBinding - 不支持 wsHttpBinding。
-   **MTOM** - 使用 MTOM 的服务<em>可能</em>正常工作。 目前暂未提供官方支持。
-   **递归** - APIM 不支持以递归方式定义的类型（例如，引用这些类型本身的数组）。
-   **多个命名空间** - 可以在架构中使用多个名称空间，但只能使用目标名称空间来定义消息部分。 不会保留用于定义其他输入或输出元素的目标以外的其他命名空间。 虽然可以导入这样的 WSDL 文档，但在导出时，所有消息部分都将具有 WSDL 的目标命名空间。
-   **数组** - SOAP 到 REST 转换仅支持包装数组，如下例所示：

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

## <a name="wadl"></a><a name="wadl"> </a>WADL

目前没有已知的 WADL 导入问题。
