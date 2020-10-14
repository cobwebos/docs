---
ms.openlocfilehash: e96975bce5e5d99db4ea78c80c027a95d4b662e3
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92038489"
---
![概述](../../../media/quickstarts/gRPC-extension.svg)

此图显示本快速入门中信号的流动方式。 [Edge 模块](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)模拟托管实时流式处理协议 (RTSP) 服务器的 IP 相机。 [RTSP 源](../../../media-graph-concept.md#rtsp-source)节点从该服务器拉取视频源，并将视频帧发送到[运动检测处理器](../../../media-graph-concept.md#motion-detection-processor)节点。 该处理器将检测运动，并在检测到运动后将视频帧推送到 [gRPC 扩展处理器](../../../media-graph-concept.md#grpc-extension-processor)节点。

gRPC 扩展节点扮演代理的角色。 它将视频帧转换为指定的图像类型。 然后，它将图像通过 gRPC 中继到另一个 Edge 模块，该模块通过[共享内存](https://en.wikipedia.org/wiki/Shared_memory)在 gRPC 终结点后运行 AI 模型。 在此示例中，使用 [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) 模型构建该 Edge 模块，该模型能够检测多种类型的对象。 gRPC 扩展处理器节点收集检测结果并将事件发布到 [IoT 中心接收器](../../../media-graph-concept.md#iot-hub-message-sink)节点。 然后该节点将这些事件发送到 [IoT Edge 中心](../../../../../iot-edge/iot-edge-glossary.md#iot-edge-hub)。

在本快速入门中，请执行以下操作：

1. 创建并部署媒体图。
1. 解释结果。
1. 清理资源。