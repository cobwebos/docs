---
title: IoT Edge 配额的实时视频分析-Azure
description: 本文介绍 IoT Edge 配额和限制的实时视频分析。
ms.topic: conceptual
ms.date: 05/22/2020
ms.openlocfilehash: 90141fa850c9ab3e3abbea15001249da0736ac45
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87091804"
---
# <a name="quotas-and-limitations"></a>配额和限制

本文枚举 IoT Edge 模块上的实时视频分析的配额和限制。

## <a name="maximum-period-of-disconnected-use"></a>断开使用的最长时间

边缘模块可维持暂时失去网络连接。 如果模块的断开连接超过36小时，它将停用任何正在运行的图形实例，并将阻止进一步的直接方法调用。

若要将边缘模块恢复到操作状态，必须还原网络连接，并且该模块需要能够成功与 Azure 媒体服务帐户进行通信。

## <a name="maximum-number-of-graph-instances"></a>图形实例的最大数目

每个模块最多可以有1000个图形实例（通过 GraphInstanceSet 创建）。

## <a name="maximum-number-of-graph-topologies"></a>最大图形拓扑数

每个模块最多可以有50个图形拓扑（通过 GraphTopologySet 创建）。

## <a name="limitations-on-graph-topologies-at-preview"></a>预览版上图形拓扑的限制

对于预览版本，在媒体图形拓扑中，可以将不同节点上的限制连接在一起。

* RTSP 源
   * 每个图形拓扑仅允许有一个 RTSP 源。
* 帧速率筛选器处理器
   * 必须立即从 RTSP 源或运动检测处理器下游。
   * 不能在 HTTP 扩展处理器下游使用。
   * 不能从运动检测处理器上游。
* HTTP 扩展处理器
   * 每个图形拓扑最多可以有一个此类处理器。
* 运动检测处理器
   * 必须从 RTSP 源立即下游。
   * 每个图形拓扑最多可以有一个此类处理器。
   * 不能在 HTTP 扩展处理器下游使用。
* 信号入口处理器
   * 必须从 RTSP 源立即下游。
* 资产接收器 
   * 必须立即从 RTSP 源或信号入口处理器下游。
* 文件接收器
   * 必须立即从信号入口处理器下游。
   * 不能立即成为 HTTP 扩展处理器或动作检测处理器的下游
* IoT 中心接收器
   * 不能立即成为 IoT 中心源的下游。

如果同时使用了运动检测和筛选器速率处理器节点，它们应该位于通向 RTSP 源节点的相同节点链中。

## <a name="limitations-on-media-service-operations-at-preview"></a>媒体服务操作的预览限制

预览版本时，IoT Edge 上的实时视频分析不支持以下功能：

* 能够在不中断的情况下将媒体服务帐户从一个订阅迁移到另一个订阅。
* 能够将多个存储帐户用于媒体服务帐户。
* 在不重新启动的情况下动态更改模块的所需属性中的服务主体信息。

## <a name="next-steps"></a>后续步骤

[概述](overview.md)
