---
title: 处理内容类型 - Azure 逻辑应用 | Microsoft 文档
description: 了解逻辑应用在设计时和运行时如何处理内容类型
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: article
ms.date: 07/20/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 82eb9c895f016efe569651dc89885d2e4850fd59
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2018
ms.locfileid: "39159085"
---
# <a name="handle-content-types-in-azure-logic-apps"></a>在 Azure 逻辑应用中处理内容类型

有各种类型的内容可以流过逻辑应用，例如 JSON、XML、平面文件和二进制数据。 逻辑应用支持所有内容类型，并且原生支持其中的某些类型，而不需要在逻辑应用中进行强制转换或转换。 但是，还有一些类型可能需要进行强制转换或转换。 本文介绍逻辑应用如何处理内容类型，以及我们在必要时如何正确强制转换或转换这些类型。

逻辑应用依赖于 HTTP 调用中的 `Content-Type` 标头值来确定处理内容类型的适当方式，例如：

* [application/json](#application-json)（本机类型）
* [text/plain](#text-plain)（本机类型）
* [application/xml and application/octet-stream](#application-xml-octet-stream)
* [其他内容类型](#other-content-types)

<a name="application-json"></a>

## <a name="applicationjson"></a>application/json

逻辑应用会存储并处理使用 *application/json* 内容类型作为 JavaScript 表示法 (JSON) 对象的任何请求。 默认情况下，无需任何强制转换，即可分析 JSON 内容。 若要分析包含内容类型为“application/json”的标头的请求，可以使用表达式。 此示例返回 `animal-type` 数组中的 `dog` 值，且无需强制转换： 
 
`@body('myAction')['animal-type'][0]` 
  
  ```json
  {
    "client": {
       "name": "Fido",
       "animal-type": [ "dog", "cat", "rabbit", "snake" ]
    }
  }
  ```

如果处理的 JSON 数据未指定标头，可以使用 [json() 函数](../logic-apps/workflow-definition-language-functions-reference.md#json)手动将该数据强制转换为 JSON，例如： 
  
`@json(triggerBody())['animal-type']`

### <a name="create-tokens-for-json-properties"></a>创建 JSON 属性的标记

逻辑应用提供相应的功能来生成用户友好的标记，用于表示 JSON 内容中的属性，以便在逻辑应用的工作流中更轻松地引用和使用这些属性。

* **请求触发器**

  在逻辑应用设计器中使用此触发器时，可以提供一个 JSON 架构用于描述预期要接收的有效负载。 
  设计器使用此架构分析 JSON 内容，并生成用户友好的标记来表示 JSON 内容中的属性。 
  然后，你可以在整个逻辑应用工作流中轻松引用和使用这些属性。 
  
  如果没有架构，可以生成架构。 
  
  1. 在请求触发器中，选择“使用示例有效负载生成架构”。  
  
  2. 在“输入或粘贴示例 JSON 有效负载”下，提供示例有效负载并选择“完成”。 例如： 

     ![提供示例 JSON 有效负载](./media/logic-apps-content-type/request-trigger.png)

     此时，生成的架构会显示在触发器中。

     ![提供示例 JSON 有效负载](./media/logic-apps-content-type/generated-schema.png)

     下面是代码视图编辑器中请求触发器的基础定义：

     ```json
     "triggers": { 
        "manual": {
           "type": "Request",
           "kind": "Http",
           "inputs": { 
              "schema": {
                 "type": "object",
                 "properties": {
                    "client": {
                       "type": "object",
                       "properties": {
                          "animal-type": {
                             "type": "array",
                             "items": {
                                "type": "string"
                             },
                          },
                          "name": {
                             "type": "string"
                          }
                       }
                    }
                 }
              }
           }
        }
     }
     ```

  3. 在请求中，请务必包含 `Content-Type` 标头，并将标头值设置为 `application/json`。

* **Parse JSON 操作**

  在逻辑应用设计器中使用此操作时，可以分析 JSON 输出，并生成用户友好的标记用于表示 JSON 内容中的属性。 
  然后，你可以在整个逻辑应用工作流中轻松引用和使用这些属性。 与请求触发器类似，可以提供或生成一个 JSON 架构用于描述想要分析的 JSON 内容。 
  这样，便可以更轻松地使用 Azure 服务总线、Azure Cosmos DB 等服务中的数据。

  ![分析 JSON](./media/logic-apps-content-type/parse-json.png)

<a name="text-plain"></a>

## <a name="textplain"></a>text/plain

当逻辑应用收到 `Content-Type` 标头设置为 `text/plain` 的 HTTP 消息时，逻辑应用将以原始格式存储这些消息。 如果在后续操作中包含这些消息且不进行强制转换，则会在将 `Content-Type` 标头设置为 `text/plain` 的情况下传出请求。 

例如，在处理某个平面文件时，可能会收到 `Content-Type` 标头设置为 `text/plain` 内容类型的 HTTP 请求：

`Date,Name,Address`</br>
`Oct-1,Frank,123 Ave`

如果随后在某个后续操作中发送此请求作为另一个请求的正文（例如 `@body('flatfile')`），则第二个请求的 `Content-Type` 标头也会设置为 `text/plain`。 如果正在处理纯文本数据但未指定标头，可按以下表达式中所示，使用 [string() 函数](../logic-apps/workflow-definition-language-functions-reference.md#string)手动将该数据强制转换为文本： 

`@string(triggerBody())`

<a name="application-xml-octet-stream"></a>

## <a name="applicationxml-and-applicationoctet-stream"></a>application/xml and application/octet-stream

逻辑应用始终保留收到的 HTTP 请求或响应中的 `Content-Type`。 因此，如果逻辑应用收到了 `Content-Type` 设置为 `application/octet-stream` 的内容，而你在某个后续操作中包含该内容且不进行强制转换，则传出的请求的 `Content-Type` 也会设置为 `application/octet-stream`。 这样，逻辑应用可以保证在经历工作流的不同阶段时，该数据不会丢失。 但是，当状态在工作流中转移时，操作状态或输入和输出将存储在 JSON 对象中。 

## <a name="converter-functions"></a>转换器函数

为了保留某些数据类型，逻辑应用会将内容转换为二进制 base64 编码的字符串，该字符串包含同时保留 `$content` 有效负载和 `$content-type`（可自动转换）的相应元数据。 

此列表描述了使用这些[函数](../logic-apps/workflow-definition-language-functions-reference.md)时逻辑应用如何转换内容：

* `json()`：将数据强制转换为 `application/json`
* `xml()`：将数据强制转换为 `application/xml`
* `binary()`：将数据强制转换为 `application/octet-stream`
* `string()`：将数据强制转换为 `text/plain`
* `base64()`：将内容转换为 base64 字符串
* `base64toString()`：将 base64 编码的字符串转换为 `text/plain`
* `base64toBinary()`：将 base64 编码的字符串转换为 `application/octet-stream`
* `encodeDataUri()`：将字符串编码为 dataUri 字节数组
* `decodeDataUri()`：将 `dataUri` 解码为字节数组

例如，如果收到 `Content-Type` 设置为 `application/xml` 的 HTTP 请求，如以下内容所示：

```html
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

可以使用包含 `xml()` 和 `triggerBody()` 函数的 `@xml(triggerBody())` 表达式来强制转换此内容，然后再使用此内容。 或者，可以使用包含 `xpath()` 和 `xml()` 函数的 `@xpath(xml(triggerBody()), '/CustomerName')` 表达式。 

## <a name="other-content-types"></a>其他内容类型

逻辑应用使用并支持其他内容类型，但你可能需要通过解码 `$content` 变量来手动获取消息正文。

例如，假设具有 `application/x-www-url-formencoded` 内容类型的请求触发了逻辑应用。 为了保留所有数据，请求正文中的 `$content` 变量包含一个编码为 base64 字符串的有效负载：

`CustomerName=Frank&Address=123+Avenue`

由于该请求未采用纯文本或 JSON 格式，因此将存储在操作中，如下所示：

```json
"body": {
   "$content-type": "application/x-www-url-formencoded",
   "$content": "AAB1241BACDFA=="
}
```

逻辑应用提供本机函数用于处理表单数据： 

* [triggerFormDataValue()](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue)
* [triggerFormDataMultiValues()](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues)
* [formDataValue()](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) 
* [formDataMultiValues()](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues)

或者，可以使用以下示例所示的表达式来手动访问数据：

`@string(body('formdataAction'))` 

如果希望传出的请求包含相同的 `application/x-www-url-formencoded` 内容类型标头，可将请求添加到操作的正文，而无需使用类似于 `@body('formdataAction')` 的表达式执行任何强制转换。 但是，仅当正文是 `body` 输入中的唯一参数时，此方法才有效。 如果尝试在 `application/json` 请求中使用 `@body('formdataAction')` 表达式，将发生运行时错误，因为发送了编码的正文。
