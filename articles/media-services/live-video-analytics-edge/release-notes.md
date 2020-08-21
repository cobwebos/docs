---
title: IoT Edge 上的实时视频分析发行说明 - Azure
description: 本主题提供 IoT Edge 上的实时视频分析版本的发行说明、改进、bug 修复和已知问题。
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: c18977807d8cdca264a27d7fcff37aec7a61eb55
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690645"
---
# <a name="live-video-analytics-on-iot-edge-release-notes"></a>IoT Edge 上的实时视频分析发行说明

>通过将此 URL (`https://docs.microsoft.com/api/search/rss?search=%22Live+Video+Analytics+on+IoT+Edge+release+notes%22&locale=en-us`) 复制并粘贴到 RSS 源阅读器中获取有关何时重新访问此页以获得更新的通知。

本文提供以下事项的信息：

* 最新版本
* 已知问题
* Bug 修复
* 已弃用的功能

## <a name="august-19-2020"></a>2020年8月19日

此模块2020年8月刷新的此版本标记为：

```
mcr.microsoft.com/media/live-video-analytics:1.0.3
```

> [!NOTE]
> 在快速入门和教程中，部署清单使用标记 1 (实时视频分析： 1) 。 因此，只需重新部署此类清单，就会 > 设备上更新该模块。

## <a name="new-features"></a>新增功能 

* 你现在可以在 IoT Edge 上的实时视频分析与使用 gRPC framework 的自定义扩展之间获取高数据内容传输性能。 请参阅 [此](analyze-live-video-use-your-grpc-model-quickstart.md) 快速入门。
* 更广泛的实时视频分析区域部署，并且仅更新了云服务。  
* 实时视频分析现已在全球25个其他区域推出。 下面是所有可用区域的 [列表](https://azure.microsoft.com/global-infrastructure/services/?products=media-services) 。  
* " [设置](https://aka.ms/lva-edge/setup-resources-for-samples) " "快速启动" 也已经更新。 

## <a name="bug-fixes"></a>Bug 修复 

无 

## <a name="july-13-2020"></a>7月13日，2020

此模块2020年7月刷新的此版本标记为：

```
mcr.microsoft.com/media/live-video-analytics:1.0.2
```

> [!NOTE]
> 在快速入门和教程中，部署清单使用标记 1 (实时视频分析： 1) 。 因此，只需重新部署此类清单，就会 > 设备上更新该模块。

### <a name="new-features"></a>新增功能
* 你现在可以创建具有 "资产接收器" 节点以及 "信号入口处理器" 节点下游的 "文件接收器" 节点的图形拓扑。 有关示例，请参阅 [此](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-motion-assets-files) 示例。

### <a name="bug-fixes"></a>Bug 修复
* 对所需属性验证的改进

## <a name="june-1-2020"></a>2020 年 6 月 1 日

此版本是 IoT Edge 上的实时视频分析的第一个公共预览版本。 发行标记为

```
     mcr.microsoft.com/media/live-video-analytics:1.0.0
```

### <a name="supported-functionalities"></a>支持的功能
* 使用所选的 AI 模块分析实时视频流，还可以选择在边缘设备上或云中录制视频
* 在 IoT Edge [支持](../../iot-edge/support.md)的 Linux AMD64 操作系统上使用
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
