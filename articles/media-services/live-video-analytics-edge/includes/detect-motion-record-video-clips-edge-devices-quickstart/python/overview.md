---
ms.openlocfilehash: 768e79c6a2471715b336f90748ad97ecfcc4bbc2
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2020
ms.locfileid: "88682084"
---

![概览](../../../media/quickstarts/overview-qs4.png)

上图显示本快速入门中信号的流动方式。 [Edge 模块](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)模拟托管实时流协议 (RTSP) 服务器的 IP 相机。 [RTSP 源](../../../media-graph-concept.md#rtsp-source)节点从该服务器拉取视频源，并将视频帧发送到[运动检测处理器](../../../media-graph-concept.md#motion-detection-processor)节点。 RTSP 源会将同一视频帧发送到[信号入口处理器](../../../media-graph-concept.md#signal-gate-processor)节点，该节点在被事件触发前会保持关闭状态。

当运动检测处理器检测到视频中的运动时，它会将事件发送到信号入口处理器节点，并触发该节点。 门会打开，并将这一状态保持到配置的时间结束为止，并将视频帧发送到[文件接收器](../../../media-graph-concept.md#file-sink)节点。 该接收器节点以 MP4 文件形式将视频录制到边缘设备上的本地文件系统中。 该文件保存在配置的位置。

在本快速入门中，请执行以下操作：

1. 创建并部署媒体图。
1. 解释结果。
1. 清理资源。
