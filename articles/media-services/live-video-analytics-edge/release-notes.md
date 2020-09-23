---
title: IoT Edge 上的实时视频分析发行说明 - Azure
description: 本主题提供 IoT Edge 上的实时视频分析版本的发行说明、改进、bug 修复和已知问题。
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: 0bdf11cd99d99067dc53dde7d55fd37b96a382c9
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90882735"
---
# <a name="live-video-analytics-on-iot-edge-release-notes"></a>IoT Edge 上的实时视频分析发行说明

>通过将此 URL (`https://docs.microsoft.com/api/search/rss?search=%22Live+Video+Analytics+on+IoT+Edge+release+notes%22&locale=en-us`) 复制并粘贴到 RSS 源阅读器中获取有关何时重新访问此页以获得更新的通知。

本文提供以下事项的信息：

* 最新版本
* 已知问题
* Bug 修复
* 已弃用的功能

<hr width=100%>

## <a name="september-22-2020"></a>2020年9月22日

此模块2020年9月刷新的此版本标记为：

```
mcr.microsoft.com/media/live-video-analytics:1.0.4
```

> [!NOTE]
> 在快速入门和教程中，部署清单使用标记 1 (live-video-analytics:1)。 因此，只需重新部署此类清单，即可在“边缘”>“设备”上更新该模块。

### <a name="module-updates"></a>模块更新

* 新的 "图形扩展" 节点， [MediaGraphCognitiveServicesVisionExtension](custom-vision-tutorial.md) 可与 " [空间分析" 模块](spatial-analysis-tutorial.md) (预览 ") 从认知服务集成。
* 添加了对 Linux ARM64 设备的支持-使用手动部署到此类设备的 [步骤](deploy-iot-edge-device.md) 。

### <a name="documentation-updates"></a>文档更新

* 可在 Azure Stack 边缘设备上的 IoT Edge 上使用实时视频分析的[说明](deploy-azure-stack-edge-how-to.md)。
* 新教程介绍了如何使用 [自定义视觉服务](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) 开发方案特定的计算机视觉模型，以及如何使用它来实时 [分析实时视频](custom-vision-tutorial.md) 。

<hr width=100%>

## <a name="august-19-2020"></a>2020 年 8 月 19 日

此模块 2020 年 8 月刷新的此版本标记为：

```
mcr.microsoft.com/media/live-video-analytics:1.0.3
```

> [!NOTE]
> 在快速入门和教程中，部署清单使用标记 1 (live-video-analytics:1)。 因此，只需重新部署此类清单，即可在“边缘”>“设备”上更新该模块。

### <a name="module-updates"></a>模块更新

* 现在，你可以使用 gRPC 框架在 IoT Edge 的实时视频分析和自定义扩展之间获得高数据内容传输性能。 请参阅[此内容](analyze-live-video-use-your-grpc-model-quickstart.md)以开始使用。
* 实时视频分析的区域部署更广泛，且只更新了云服务。  
* 实时视频分析现已在全球25个其他区域推出。 下面是所有可用区域的[列表](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)。  
* 快速入门的[设置](https://aka.ms/lva-edge/setup-resources-for-samples)已更新，并支持新区域。
    * 对于已设置资源的用户，不需要进行任何操作

### <a name="bug-fixes"></a>Bug 修复 

* 在设置脚本中删除弃用的 azure 扩展的使用

<hr width=100%>

## <a name="july-13-2020"></a>2020 年 7 月 13 日

此模块 2020 年 7 月刷新的此版本标记为：

```
mcr.microsoft.com/media/live-video-analytics:1.0.2
```

> [!NOTE]
> 在快速入门和教程中，部署清单使用标记 1 (live-video-analytics:1)。 因此，只需重新部署此类清单，即可在“边缘”>“设备”上更新该模块。

### <a name="module-updates"></a>模块更新

* 现在，你可以创建具有资产接收器节点以及信号门处理器节点下游的文件接收器节点的图形拓扑。 请参阅[此内容](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-motion-assets-files)获取示例。

### <a name="bug-fixes"></a>Bug 修复

* 对所需属性验证的改进

<hr width=100%>

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
