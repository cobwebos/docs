---
title: 什么是媒体图扩展 - Azure
description: 使用 IoT Edge 上的实时视频分析，可以通过图形扩展节点扩展媒体图处理功能。
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: 74929cc51a868d20952f1e25432f5343e4821d08
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89569317"
---
# <a name="media-graph-extension"></a>媒体图扩展

使用 IoT Edge 上的实时视频分析，可以通过图形扩展节点扩展媒体图处理功能。 你的分析扩展插件可以利用传统的图像处理技术或计算机视觉 AI 模型。 通过在媒体图中包含扩展处理器节点来启用图形扩展。 扩展处理器节点将视频帧中继到配置的终结点，并充当扩展的接口。 可建立到本地或远程终结点的连接，并且可使用身份验证和 TLS 加密来保护它（如果需要）。 此外，图形扩展处理器节点允许在将视频帧提交到自定义扩展之前对其进行可选的缩放和编码。

实时视频分析支持两种媒体图扩展处理器：

* [HTTP 扩展处理器](media-graph-concept.md#http-extension-processor)
* [gRPC 扩展处理器](media-graph-concept.md#grpc-extension-processor)

## <a name="http-extension-processor"></a>HTTP 扩展处理器

HTTP 扩展处理器支持使用 HTTP 协议的可扩展性方案，其中性能和/或最佳资源利用率不是主要问题。 可以通过 HTTP REST 终结点将自己的 AI 公开给媒体图。 

在以下情况下使用 HTTP 扩展处理器节点：

* 你想与现有的 HTTP 推断系统实现更好的互操作性。
* 低性能数据传输是可接受的。
* 你想将简单的请求-响应接口用于实时视频分析。

## <a name="grpc-extension-processor"></a>gRPC 扩展处理器

gRPC 扩展处理器使用基于 gRPC 的高性能结构化协议实现可扩展性方案。 它非常适用于性能和/或最佳资源利用率优先的场景。 通过 gRPC 扩展处理器，你可以充分利用结构化数据定义。 gRPC 通过以下方式提供优秀的内容传输性能：

* [内置共享内存](https://en.wikipedia.org/wiki/Shared_memory)或 
* 将内容直接嵌入到 gRPC 消息正文中。 

gRPC 扩展处理器可用于发送媒体属性以及交换推理消息。
因此，在下列情况下，请使用 gRPC 扩展处理器节点：

* 需要使用结构化的协定（例如请求和响应的结构化消息）
* 想要使用协议缓冲区 ([protobuf](https://developers.google.com/protocol-buffers)) 作为通信的基础消息交换格式。
* 希望在单流会话（而不是传统的请求-响应模型）中与 gRPC 服务器通信，它需要自定义请求处理程序来分析传入的请求并调用正确的实现函数。 
* 需要在实时视频分析和你的模块之间进行低延迟和高吞吐量的通信。

## <a name="use-your-inferencing-model-with-live-video-analytics"></a>将你的推断模型用于实时视频分析

媒体图扩展允许你在任何可用的推理运行时（如 ONNX、TensorFlow、PyTorch 或你自己的 docker 容器中的其他运行时）上运行所选的推理模型。 推断自定义扩展应与实时视频分析边缘模块一起部署，以获得最佳性能，然后将通过图形拓扑中包含的 HTTP 扩展处理器或 gRPC 扩展处理器进行调用。 另外，可以通过在媒体扩展处理器的上游添加[运动检测器处理器](media-graph-concept.md#motion-detection-processor)和[帧速率筛选处理器](media-graph-concept.md#frame-rate-filter-processor)来限制对自定义扩展的调用频率。

下图描绘了大致数据流：

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/media-graph-extension/analyze-live-video-with-AI-inference-service.svg" alt-text="AI 推理服务":::

## <a name="samples"></a>示例

查看关于实时视频分析的 [Jupyter 笔记本](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/notebooks/readme.md)示例。 这些笔记本将为你提供以下方面的媒体图扩展分步说明：

* 如何创建扩展服务的 Docker 容器映像
* 如何将扩展服务与实时视频分析容器一起部署为容器
* 如何使用带有扩展客户端的实时视频分析媒体图并将其指向扩展终结点 (HTTP/gRPC)