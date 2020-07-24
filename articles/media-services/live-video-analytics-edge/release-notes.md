---
title: IoT Edge 发行说明上的实时视频分析-Azure
description: 本主题提供有关 IoT Edge 版本、改进、bug 修复和已知问题的实时视频分析的发行说明。
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 28260728532d9db52b8d36488c2e456bd11803ea
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87091773"
---
# <a name="live-video-analytics-on-iot-edge-release-notes"></a>IoT Edge 发行说明上的实时视频分析

>通过将此 URL (`https://docs.microsoft.com/api/search/rss?search=%22Live+Video+Analytics+on+IoT+Edge+release+notes%22&locale=en-us`) 复制并粘贴到 RSS 源阅读器中获取有关何时重新访问此页以获得更新的通知。

本文提供以下事项的信息：

* 最新版本
* 已知问题
* Bug 修复
* 已弃用的功能

## <a name="july-13-2020"></a>7月13日，2020

此模块2020年7月刷新的此版本标记为：

```
     mcr.microsoft.com/media/live-video-analytics:1.0.2
```

> [!NOTE]
> 在快速入门和教程中，部署清单使用标记1（实时视频分析：1）。 因此，只需重新部署此类清单，就会 > 设备上更新该模块。

### <a name="new-features"></a>新增功能
* 你现在可以创建具有 "资产接收器" 节点以及 "信号入口处理器" 节点下游的 "文件接收器" 节点的图形拓扑。 有关示例，请参阅[此](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-motion-assets-files)示例。

### <a name="bug-fixes"></a>Bug 修复
* 对所需属性验证的改进

## <a name="june-1-2020"></a>2020 年 6 月 1 日

此版本是 IoT Edge 上的实时视频分析的第一个公共预览版本。 版本标记为

```
     mcr.microsoft.com/media/live-video-analytics:1.0.0
```

### <a name="supported-functionalities"></a>支持的功能
* 使用所选的 AI 模块分析实时视频流，还可以选择在边缘设备或云中录制视频
* 在 IoT Edge[支持](../../iot-edge/support.md)的 Linux AMD64 操作系统上使用
* 使用 Azure 门户或 Visual Studio Code 通过 IoT 中心部署和配置模块
* 通过以下直接方法调用来远程或本地管理[图形拓扑和图形实例](media-graph-concept.md#media-graph-topologies-and-instances)

    *   GraphTopologyGet
    *   GraphTopologySet
    *   GraphTopologyDelete
    *   GraphTopologyList
    *   GraphInstanceGet
    *   GraphInstanceSet
    *   GraphInstanceDelete
    *   GraphInstanceList


## <a name="next-steps"></a>后续步骤

[概述](overview.md)
