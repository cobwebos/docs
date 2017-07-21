---
title: "处理内容类型 - Azure 逻辑应用 | Microsoft 文档"
description: "Azure 逻辑应用在设计时和运行时如何处理内容类型"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: cd1f08fd-8cde-4afc-86ff-2e5738cc8288
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: LADocs; jehollan
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: ac67838344bbd10384299c086ff096fbe5dec6a9
ms.contentlocale: zh-cn
ms.lasthandoff: 07/06/2017


---
# <a name="handle-content-types-in-logic-apps"></a>在逻辑应用中处理内容类型

有许多不同类型的内容可以流过逻辑应用，其中包括 JSON、XML、平面文件和二进制数据。 逻辑应用引擎支持所有类型的内容，并且可以原生识别其中的某些类型。 但是，还有一些类型可能需要根据需要进行强制转换或转换。 本文介绍引擎如何处理不同类型的内容，以及如何根据需要正确处理这些类型。

## <a name="content-type-header"></a>内容类型标头

为了简单起见，让我们看看这两个不需要任何转换或强制转换就可在逻辑应用中使用的 `Content-Types`：`application/json` 和 `text/plain`。

## <a name="applicationjson"></a>Application/JSON

工作流引擎根据 HTTP 调用中的 `Content-Type` 标头来确定合适的处理方式。 内容类型为 `application/json` 的任何请求被视为 JSON 对象进行存储和处理。 此外，默认情况下可以对 JSON 内容进行解析，而无需任何转换。 

例如，可以在工作流中使用类似于 `@body('myAction')['foo'][0]` 的表达式来分析包含内容类型标头 `application/json ` 的请求，以获取本例中的 `bar` 值：

```
{
    "data": "a",
    "foo": [
        "bar"
    ]
}
```

不需要其他任何转换。 如果使用未指定标头的 JSON 数据，则可以使用 `@json()` 函数手动将其转换为 JSON（例如：`@json(triggerBody())['foo']`）。

### <a name="schema-and-schema-generator"></a>架构和架构生成器

可以使用请求触发器为预计要接收的有效负载输入 JSON 架构。 此架构可让设计器生成令牌，方便你使用请求的内容。 如果尚未准备好架构，请选择“使用示例有效负载生成架构”，以便从示例有效负载生成 JSON 架构。

![架构](./media/logic-apps-http-endpoint/manualtrigger.png)

### <a name="parse-json-action"></a>“Parse JSON”操作

可以使用 `Parse JSON` 操作将 JSON 内容分析成适合在逻辑应用中使用的令牌。 此操作与请求触发器类似，可让你为要分析的内容输入或生成 JSON 架构。 此工具为使用服务总线、Azure Cosmos DB 等的数据提供了很大的方便。

![分析 JSON](./media/logic-apps-content-type/ParseJSON.png)

## <a name="textplain"></a>Text/plain

与 `application/json` 类似，收到的 `Content-Type` 标头为 `text/plain` 的 HTTP 消息以原始格式进行存储。 此外，如果这些消息未经任何强制转换包含在后续操作中，这些请求将包含 `Content-Type`: `text/plain` 标头。 例如，使用平面文件时，可能会收到 `text/plain` 格式的 HTTP 内容：

```
Date,Name,Address
Oct-1,Frank,123 Ave.
```

如果在下一步操作中将请求作为另一个请求的正文发送 (`@body('flatfile')`)，发送的请求将包含 `text/plain` 内容类型标头。 如果使用未指定标头的纯文本数据，可以使用 `@string()` 函数手动将其转换为文本（例如：`@string(triggerBody())`）。

## <a name="applicationxml-and-applicationoctet-stream-and-converter-functions"></a>Application/xml 和 Application/octet-stream 和转换器函数

逻辑应用引擎始终保持从 HTTP 请求或响应中接收的 `Content-Type` 不变。 因此，如果收到的内容的 `Content-Type` 为 `application/octet-stream`，并且未经强制转换将此内容包含在后续操作中，则传出的请求的 `Content-Type` 为 `application/octet-stream`。 这样，引擎便可以保证数据在工作流中移动时不会丢失。 但是，当状态在工作流中流动时，操作状态（输入和输出）将存储在 JSON 对象中。 因此，为了保持某些数据类型不变，引擎会将内容转换为二进制 base64 编码的字符串，该字符串带有同时保留 `$content` 和 `$content-type`（可自动转换）的相应元数据。 

* `@json()` — 将数据转换为 `application/json`
* `@xml()` — 将数据转换为 `application/xml`
* `@binary()` — 将数据转换为 `application/octet-stream`
* `@string()` — 将数据转换为 `text/plain`
* `@base64()` — 将内容转换为 base64 字符串
* `@base64toString()` — 将 Base64 编码的字符串转换为 `text/plain`
* `@base64toBinary()` — 将 Base64 编码的字符串转换为 `application/octet-stream`
* `@encodeDataUri()` — 将字符串编码为 dataUri 字节数组
* `@decodeDataUri()` — 将 dataUri 解码为一个字节数组

例如，如果收到 `Content-Type` 为 `application/xml` 的 HTTP 请求：

```
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

稍后可以使用类似于 `@xml(triggerBody())` 的函数，或者类似于 `@xpath(xml(triggerBody()), '/CustomerName')` 的函数来强制转换和使用该请求。

## <a name="other-content-types"></a>其他内容类型

支持其他内容类型，并且它们可用于逻辑应用，但是可能需要通过对 `$content` 进行解码来手动检索消息正文。 例如，假设触发了一个 `application/x-www-url-formencoded` 请求来保留所有数据，在该请求中，`$content` 是编码为 base64 字符串的有效负载：

```
CustomerName=Frank&Address=123+Avenue
```

由于该请求未采用纯文本或 JSON 格式，因此将存储在操作中，如下所示：

```
...
"body": {
    "$content-type": "application/x-www-url-formencoded",
    "$content": "AAB1241BACDFA=="
}
```

目前，没有适用于表单数据的本机函数，因此仍可通过使用类似于 `@string(body('formdataAction'))` 的函数手动访问数据来使用工作流中的数据。 如果希望传出的请求也包含 `application/x-www-url-formencoded` 内容类型标头，可将请求添加到操作正文，而无需执行类似于 `@body('formdataAction')` 的任何强制转换。 但是，仅当正文是 `body` 输入中的唯一参数时，此方法才有效。 如果尝试在 `application/json` 请求中使用 `@body('formdataAction')`，将发生运行时错误，因为发送了编码的正文。


