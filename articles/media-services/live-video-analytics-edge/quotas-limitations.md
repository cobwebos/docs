---
title: IoT Edge 上的实时视频分析配额 - Azure
description: 本文介绍 IoT Edge 上的实时视频分析配额和限制。
ms.topic: conceptual
ms.date: 05/22/2020
ms.openlocfilehash: 32a24079b36655bfdacd25b07d419009f5012507
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750229"
---
# <a name="quotas-and-limitations"></a>配额和限制

本文枚举了 IoT Edge 上的实时视频分析模块的配额和限制。

## <a name="maximum-period-of-disconnected-use"></a>在已断开连接的情况下使用的最长期限

边缘模块可以承受网络连接的临时丢失。 如果模块保持已断开连接的状态超过了 36 小时，它将停用任何正在运行的图形实例，并将阻止进一步的直接方法调用。

若要将边缘模块恢复到操作状态，必须还原网络连接，使此模块需要能够与 Azure 媒体服务帐户进行成功通信。

## <a name="maximum-number-of-graph-instances"></a>图形实例的最大数量

每个模块最多可以有 1000 个图形实例（通过 GraphInstanceSet 创建）。

## <a name="maximum-number-of-graph-topologies"></a>图形拓扑的最大数量

每个模块最多可以有 50 个图形拓扑（通过 GraphTopologySet 创建）。

## <a name="limitations-on-graph-topologies-at-preview"></a>预览版图形拓扑的限制

在预览版本中，对于可以在一个媒体图拓扑中连接在一起的不同节点，存在一些限制。

* RTSP 源
   * 每个图形拓扑仅允许有一个 RTSP 源。
* 帧速率筛选器处理器
   * 必须立即成为 RTSP 源或运动检测处理器中的下游。
   * 不能用于 HTTP 或 gRPC 扩展处理器的下游。
   * 不能是运动检测处理器中的上游。
* HTTP 扩展处理器
   * 每个图形拓扑最多可以有一个此类处理器。
* gRPC 扩展处理器
   * 每个图形拓扑最多可以有一个此类处理器。
* 运动检测处理器
   * 必须立即成为 RTSP 源中的下游。
   * 每个图形拓扑最多可以有一个此类处理器。
   * 不能用于 HTTP 或 gRPC 扩展处理器的下游。
* 信号门处理器
   * 必须立即成为 RTSP 源中的下游。
* 资产接收器 
   * 必须立即成为 RTSP 源或信号门处理器中的下游。
* 文件接收器
   * 必须立即成为信号门处理器中的下游。
   * 不能立即成为 HTTP 或 gRPC 扩展处理器或运动检测处理器的下游
* IoT 中心接收器
   * 不能立即成为 IoT 中心源的下游。

如果同时使用了运动检测和筛选器速率处理器节点，它们应位于通向 RTSP 源节点的相同节点链中。

## <a name="limitations-on-media-service-operations-at-preview"></a>预览版中媒体服务操作的限制

预览版发布时，IoT Edge 上的实时视频分析不支持以下功能：

* 能够在不中断的情况下将媒体服务帐户从一个订阅迁移到另一个订阅。
* 能够将多个存储帐户用于媒体服务帐户。
* 能够在不重启的情况下动态更改模块所需属性中的服务主体信息。

## <a name="next-steps"></a>后续步骤

[概述](overview.md)
