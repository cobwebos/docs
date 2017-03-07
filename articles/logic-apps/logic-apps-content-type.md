---
title: "逻辑应用的内容类型处理 | Microsoft Docs"
description: "了解逻辑应用在设计时和运行时如何处理内容类型"
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
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 84f1968a4bda08232bb260f74915ef8889b53f3c
ms.openlocfilehash: 77daea3da5bdb8de374058ef7b1ab5926ed59aa7
ms.lasthandoff: 02/27/2017


---
# <a name="logic-apps-content-type-handling"></a>逻辑应用的内容类型处理
逻辑应用中可使用许多不同类型的内容 — 包括 JSON、XML、平面文件和二进制数据。  虽然支持所有类型的内容，但是某些类型可以由逻辑应用引擎直接解析，而其他类型可能需要根据需要进行转换。  以下文章介绍了引擎如何处理不同类型的内容，以及如何根据需要正确地进行处理。

## <a name="content-type-header"></a>Content-Type 标头
为了简单起见，我们来看这两个不需要任何转换或强制转换就可在逻辑应用中使用的 `Content-Types`：`application/json` 和 `text/plain`。

## <a name="applicationjson"></a>Application/json
工作流引擎根据 HTTP 调用中的 `Content-Type` 标头来确定合适的处理方式。  内容类型为 `application/json` 的任何请求被视为 JSON 对象进行存储和处理。  此外，默认情况下可以对 JSON 内容进行解析，而无需任何转换。  因此，具有内容类型标头 `application/json ` 的如下请求：

```
{
    "data": "a",
    "foo": [
        "bar"
    ]
}
```

可在使用表达式如 `@body('myAction')['foo'][0]` 的工作流中进行解析，以获取值（本例中为 `bar`）。  不需要其他任何转换。  如果正在使用未指定标头的 JSON 数据，那么可以使用 `@json()` 函数手动将其转换为 JSON（例如：`@json(triggerBody())['foo']`）。

### <a name="schema-and-schema-generator"></a>架构和架构生成器
可以使用请求触发器为预计要接收的有效负载输入 JSON 架构。 这样可以让设计器生成令牌，方便用户使用请求的内容。 如果还没有架构，可以选择`Use sample payload to generate schema`，从有效负载示例中生成 JSON 架构。

![架构](./media/logic-apps-http-endpoint/manualtrigger.png)

### <a name="use-parse-json-action"></a>使用分析 JSON 操作
可以使用 `Parse JSON` 操作将 JSON 内容分析成适合在逻辑应用中使用的令牌。 与请求触发器类似，它允许用户为要分析的内容输入或生成 JSON 架构。 这是一个很好的工具，极大地方便了用户使用服务总线、DocumentDb 等处的数据。

![分析 JSON](./media/logic-apps-content-type/ParseJSON.png)

## <a name="textplain"></a>Text/plain
与 `application/json` 类似，收到的 `Content-Type` 标头为 `text/plain` 的 HTTP 消息以原始格式进行存储。  此外，如果请求未进行任何转换包含在后续操作中，那么此请求具有 `Content-Type`: `text/plain` 标头。  例如，如果使用平面文件，可能会收到以下 HTTP 内容：

```
Date,Name,Address
Oct-1,Frank,123 Ave.
```

作为 `text/plain` 类型。  如果在下一步操作中将其作为另一个请求的正文发送 (`@body('flatfile')`)，那么该请求具有 `text/plain` 内容类型标头。  如果正在使用未指定标头的纯文本数据，那么可以使用 `@string()` 函数手动将其转换为文本（例如：`@string(triggerBody())`）。

## <a name="applicationxml-and-applicationoctet-stream-and-converter-functions"></a>Application/xml 和 Application/octet-stream 和转换器函数
逻辑应用引擎始终保持从 HTTP 请求或响应中接收的 `Content-Type` 不变。  这意味着如果收到的内容的 `Content-Type` 为 `application/octet-stream`，那么将未经过转换的此内容包含在后续操作中使传出的请求的 `Content-Type` 为 `application/octet-stream`。  这样，引擎可以保证数据在工作流中移动时不会丢失。  但是，当数据在工作流中流动时，操作状态（输入和输出）存储在 JSON 对象中。  这意味着为了保持某些数据类型不变，引擎将内容及相应的元数据转换为二进制 base64 编码的字符串，该字符串同时保留了 `$content` 和 `$content-type`，因此可以自动转换。  你还可以使用内置的转换器函数在内容类型之间手动转换：

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

我可以稍后使用诸如 `@xml(triggerBody())` 这样的函数，或者如 `@xpath(xml(triggerBody()), '/CustomerName')` 这样的函数转换和使用该请求。

## <a name="other-content-types"></a>其他内容类型
支持其他内容类型，并且它们可用于逻辑应用，但是可能需要通过对 `$content` 进行解码来手动检索消息正文。  例如，如果触发了 `application/x-www-url-formencoded` 请求，如下所示：

```
CustomerName=Frank&Address=123+Avenue
```

因为它不是纯文本或 JSON 格式，所以将以下面的格式存储在操作中：

```
...
"body": {
    "$content-type": "application/x-www-url-formencoded",
    "$content": "AAB1241BACDFA=="
}
```

其中 `$content` 是编码为 base64 字符串来保留所有数据的有效负载。  由于当前没有自带函数可用于表单数据，因此仍可以通过使用函数（如 `@string(body('formdataAction'))`）手动访问数据来使用工作流中的数据。  如果想要传出请求也具有 `application/x-www-url-formencoded` 内容类型标头，只需将其添加到操作正文而不需要任何转换，如 `@body('formdataAction')`。  但是，仅当正文是 `body` 输入中的唯一参数时此方法才有效。  如果尝试在 `application/json` 请求中执行 `@body('formdataAction')`，将出现运行时错误，因为发送的是已编码的正文。
