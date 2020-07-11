---
title: 通过所选的 AI 分析实时视频-Azure
description: 在本文中，你将学习如何构建 IoT Edge 模块，该模块可与 IoT Edge 上的实时视频分析集成，以便使用所选的计算机视觉模型分析实时视频。
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: 6a1ea3ebd8c7de4c691d7a982dbc08e9d08d9e38
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2020
ms.locfileid: "86182859"
---
# <a name="analyze-live-video-with-ai-of-your-choice"></a>使用你选择的 AI 分析实时视频

在本文中，你将学习如何构建 IoT Edge 模块，该模块可与 IoT Edge 上的实时视频分析集成，以便使用所选的计算机视觉模型分析实时视频。 

## <a name="pre-reading"></a>预读

[媒体图概念](media-graph-concept.md)

## <a name="media-graph-extension"></a>Media graph 扩展

IoT Edge 上的实时视频分析定义了一个扩展性模型，使用该模型，可以通过图形扩展将介质图形处理功能扩展到自己的媒体分析组件。 分析组件可以利用传统的图像处理方法或计算机视觉 AI 模型。 通过在 media graph 中包含[HTTP 扩展处理器](media-graph-concept.md#http-extension-processor)节点来启用图形扩展。 HTTP 扩展处理器可将视频帧中继到你指定的 HTTP 终结点，并通过该终结点充当你的组件的接口。 可以连接到本地或远程终结点，并且可以根据需要使用身份验证和 TLS 加密来保护它。 此外，处理器还允许在转发之前对视频帧进行可选缩放和编码。

你可以选择在任何可用的推理运行时（如 ONNX、TensorFlow、PyTorch 或你自己的 docker 容器上的其他运行时）运行所选的推理模型。 你还可以使用所选的编程语言和库，通过你自己的容器上的 HTTP 服务器来公开推断功能的映像。 为了获得最佳性能，应将推断容器与实时视频分析边缘模块一起部署，并将在图形拓扑中通过 HTTP 扩展处理器进行调用。
此外，还可以根据需要将[运动检测器处理器](media-graph-concept.md#motion-detection-processor)和[帧速率筛选器处理器](media-graph-concept.md#frame-rate-filter-processor)向 HTTP 扩展处理器向上游添加，从而限制调用自定义模块的频率。

下图描绘了高级数据流：

![边缘设备](./media/analyze-live-video-with-ai-your-choice-how-to/edge-device.png)

这使您可以使用所选的 AI 模型分析视频，以满足您独特的业务需求。 AI 模型可以来自开放源社区，或来自你自己的数据科学家或从专用的 AI 提供商。

## <a name="media-graph-http-extension-contract-definitions"></a>Media graph HTTP 扩展协定定义

本节定义用于定义数据流的 HTTP 约定。

### <a name="http-extensibility-protocol"></a>HTTP 扩展性协议  

HTTP 约定定义如下：

* 模块充当 HTTP 服务器。
* IoT Edge 模块上的实时视频分析作为 HTTP 客户端。

### <a name="request"></a>请求

从实时视频分析模块到你的模块的请求将如下所示：

| 密钥 | 值 |
|---|---|
|POST| `https://hostname/optional-path?optional-query`|
|接受|application/json，*/*|
|授权| 基本、摘要式、载荷 (通过自定义标头支持) |
|Content-Type|image/jpeg<br/>image/png<br/>image/bmp<br/>image/x-raw|
|Content-Length|正文长度（字节）|
|User-Agent|Azure 媒体服务|
|正文|图像字节数，二进制编码为某个受支持的内容类型。|

#### <a name="example"></a>示例

```
POST http://localhost:8080/inference HTTP/1.1
Host: localhost:8080
x-ms-client-request-id: d6050cd4-c9f2-42d3-9adc-53ba7e440f17
Content-Type: image/bmp
Content-Length: 519222

(Image Binary Content)
```

### <a name="response"></a>响应

模块到实时视频分析模块的响应应如下所示

| 密钥 | 值 |
|---|---|
|状态代码|200正常-找到推理结果<br/>204无内容-AI 找不到内容<br/>400请求错误-不需要<br/>500内部服务器错误-不需要<br/>503服务器繁忙-基于 "重试后重试" 标头或基于标头 "标头未预设" 的默认时间量，AMS 将恢复。|
|Content-Type|application/json|
|Content-Length|    正文长度（字节）|
|正文|具有单个 "推断" 属性的 JSON 对象。|

#### <a name="example"></a>示例

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

强烈建议在下面定义的预定义架构后使用有效的 JSON 文档返回响应。 这可以更好地确保与其他组件的互操作性，以及未来添加到实时视频分析模块的功能。

如果模块返回的响应中内容类型不是 "application/json"，则实时视频分析会将消息编码为基本64内容，并将其序列化为不透明的 JSON 有效负载。

如果模块返回的响应的内容类型为 "application/json"，但 JSON 架构不遵循下面所[述的推理元数据架构](#inference-metadata-schema)，则将通过管道转发消息负载，但会降低互操作性。

> [!NOTE]
> 如果你的模块未生成任何结果，则它应返回 HTTP 204 状态代码， (没有空的响应正文的内容) 。 实时视频分析将此理解为空结果，不会在整个管道中转发事件。

### <a name="inference-metadata-schema"></a>推理元数据架构

每个推理对象都遵循此超集架构：

```
{
  "type": "[classification|motion|entity|text|other]",
  "subtype": "",              // Free form subtype id

  // Object has *at most one of* the following
  "classification":
  {
    "tag": 
    {
      "value": "",            // Tag value
      "confidence": 0.0       // Tag confidence
    },
    "attributes":[            // Optional attributes
      {
        "name": "",           
        "value": "",          
        "confidence": 0.0     
      }
    ]
  },

  "motion":
  {
    "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
    // "regionId": ""
  },

  "entity":
  {
    "tag": 
    {
      "value": "",            // Tag value
      "confidence": 0.0       // Tag confidence
    },
    "attributes":[            // Optional attributes
      {
        "name": "",           
        "value": "",          
        "confidence": 0.0     
      }
    ],
    "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
  },

  "text":
  {
    "value": "",              // Text value
    "language": "",           // Optional BCP47 Language Code (https://tools.ietf.org/html/bcp47)
    "startTimestamp": 0,      // Optional start timestamp
    "endTimestamp": 0         // Optional end timestamp
  },

  // Plus every object can have zero or more extensions
  "extensions":
  {
    "name1": "value1",
    "name2": "value2"
    // ...
  }
}
```
 
### <a name="data-contracts---class-hierarchy"></a>数据协定-类层次结构

![数据协定-类层次结构](./media/analyze-live-video-with-ai-your-choice-how-to/data-contracts.png)

### <a name="examples"></a>示例  

下面的示例包含一个具有所有受支持的推理类型的事件：

```
{
  "inferences": [
    // Light detection
    {
      "type": "classification",
      "subtype": "lightDetection",
      "classification": {
        "tag": { "value": "daylight", "confidence": 0.86 },
        "attributes": [
            { "name": "isBlackAndWhite", "value": "false", "confidence": 0.71 }
        ]
      }
    },

    // Motion detection
    {
      "type": "motion",
      "subtype": "motionDetection",
      "motion":
      {
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      }
    },

    // Object detection
    {
      "type": "entity",
      "subtype": "objectDetection",    
      "entity":
      {
        "tag": { "value": "dog", "confidence": 0.97 },
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      }
    },

    // Vehicle identification
    {
      "type": "entity",
      "subtype": "vehicleIdentification",    
      "entity":
      {
        "tag": { "value": "007-SPY", "confidence": 0.82 },
        "attributes":[  
          { "name": "color", "value": "black", "confidence": 0.90 },
          { "name": "body", "value": "coupe", "confidence": 0.87 },
          { "name": "make", "value": "Aston Martin", "confidence": 0.35 },
          { "name": "model", "value": "DBS V12", "confidence": 0.33 }
        ],
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      }
    },

    // People identification
    {
      "type": "entity",
      "subtype": "peopleIdentification",    
      "entity":
      {
        "tag": { "value":"Erwin Schrödinger", "confidence": 0.50 },
        "attributes":[  
          { "name": "age", "value": "73", "confidence": 0.87 },
          { "name": "glasses", "value": "yes", "confidence": 0.94 }
        ],
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      },
    }
  ]
}
```
 
## <a name="sample-http-extension-modules"></a>示例 HTTP 扩展模块

可在[实时视频分析 GitHub](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis)存储库中找到几个示例 HTTP 扩展模块。 其中一种[视频分析示例](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx)显示了如何使用[YOLOv3](https://pjreddie.com/darknet/yolo/) [ONNX](http://onnx.ai/)模型为对象检测构建 IoT Edge 模块。 另一个[视频分析示例](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx-tiny)演示了如何使用小型 YOLOv3 （YOLOv3 ONNX 模型的轻型版本）。 您可以使用与所选 AI 模型相同的方法来构建自己的模块。

## <a name="next-steps"></a>后续步骤

[故障排除](troubleshoot-how-to.md)
