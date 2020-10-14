---
title: HTTP 扩展协议 - Azure
description: 在本文中，你将学习如何使用 HTTP 扩展协议在实时视频分析模块与 AI 或 CV 模块之间发送消息。
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: f1e1fb0e8fe63b3a83c59a4ec48abdac7f22096a
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92016648"
---
# <a name="http-extension-protocol"></a>HTTP 扩展协议

在本文中，你将学习如何使用 HTTP 扩展协议在实时视频分析模块与 AI 或 CV 模块之间发送消息。

HTTP 协定在以下两个组件之间定义：

* HTTP 服务器
* IoT Edge 上的实时视频分析模块（充当 HTTP 客户端）

## <a name="request"></a>请求

从实时视频分析模块到 HTTP 服务器的请求如下所示：

|密钥|值|
|---|---|
|POST|`https://hostname/optional-path?optional-query`|
|Accept|application/json,  */*|
|授权|基本、摘要式和持有者（通过自定义标头支持）|
|Content-Type|image/jpeg<br/>image/png<br/>image/bmp<br/>image/x-raw|
|Content-Length 正文长度（以字节为单位）|
|User-Agent|Azure 媒体服务|
|正文|图像字节数，二进制编码为某个受支持的内容类型。|

### <a name="example"></a>示例

```
POST http://localhost:8080/inference HTTP/1.1
Host: localhost:8080
x-ms-client-request-id: d6050cd4-c9f2-42d3-9adc-53ba7e440f17
Content-Type: image/bmp
Content-Length: 519222

(Image Binary Content)
```

## <a name="response"></a>响应

你的模块对实时视频分析模块的响应如下所示：

|密钥|值|
|---|---|
|状态代码|200 正常 - 找到了推理结果<br/>204 无内容 - AI 未找到内容<br/>400 错误的请求 - 非预期<br/>500 内部服务器错误 - 非预期<br/>503 服务器繁忙 - AMS 将基于“Retry-After”标头（如果未预设标头，则基于默认时间量）回退。|
|Content-Type|application/json|
|Content-Length|以字节为单位的正文长度。|
|正文|具有单个“inferences”属性的 JSON 对象。|

### <a name="example"></a>示例

```
HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 468
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 17 Apr 2020 04:44:01 GMT

{
  "inferences": [
    {
      "type": "entity",
      "entity": {
        "tag": { "value": "car", "confidence": 0.9048132 },
        "box": { "l": 0.42681578, "t": 0.47660735, "w": 0.019501392, "h": 0.020954132 }
      }
    },
    {
      "type": "entity",
      "entity": {
        "tag": { "value": "car", "confidence": 0.8953932 },
        "box": { "l": 0.55083525, "t": 0.4843858, "w": 0.046550274, "h": 0.046502113 }
      }
    }    
  ]
}
```

强烈建议使用有效的 JSON 文档按照下面定义的预定义架构返回响应。 这可以更好地确保与其他组件的互操作性，以及与未来可能添加到实时视频分析模块的功能的互操作性。

如果你的模块返回的响应的内容类型不是“application/json”，则实时视频分析会将消息编码为 base 64 内容，并将其序列化为不透明的 JSON 有效负载。

如果你的模块返回的响应的内容类型为“application/json”，但 JSON 架构不遵循下面列出的推理元数据架构，则将通过管道转发消息有效负载，但互操作性会降低。

> [!NOTE]
> 如果你的模块未生成任何结果，则它应返回响应正文为空的 HTTP 204 状态代码（无内容）。 实时视频分析会将此内容视为空结果，不会在整个管道中转发该事件。

## <a name="data-contracts---class-hierarchy"></a>数据协定 - 类层次结构

![类层次结构](./media/http-extension-protocol/class-hierarchy.png)

## <a name="next-steps"></a>后续步骤

[gRPC 数据协定](./grpc-extension-protocol.md)