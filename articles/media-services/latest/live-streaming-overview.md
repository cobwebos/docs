---
title: 使用 Azure 媒体服务实时传送视频流概述 | Microsoft Docs
description: 本文概述如何使用 Azure 媒体服务 v3 实时传送视频流。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/27/2019
ms.author: juliako
ms.openlocfilehash: a3e4821d9deb7ceee815d804f58d0b1ba14925b4
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2019
ms.locfileid: "55103558"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>使用 Azure 媒体服务 v3 实时传送视频流

使用 Azure 媒体服务可将实时事件传送到 Azure 云中的客户。 若要使用媒体服务流式传输实时事件，需要以下组件：  

- 一个相机，用于捕获实时事件。<br/>有关设置建议，请查看[简单且可移植的事件视频设备设置]( https://link.medium.com/KNTtiN6IeT)。
- 一个实时视频编码器，用于将相机（或其他设备，例如便携式计算机）的信号转换为可发送到媒体服务的贡献源。 贡献源可包括与广告相关的信号，例如 SCTE-35 标记。<br/>有关推荐的实时传送视频流编码器的列表，请参阅[实时传送视频流编码器](recommended-on-premises-live-encoders.md)。 另外，请查看以下博客：[采用 OBS 的实时传送视频流生产](https://link.medium.com/ttuwHpaJeT)。
- 媒体服务中的组件，用于引入、预览、打包、记录、加密实时事件并将其广播给客户，或者广播给 CDN 进行进一步分发。

借助媒体服务，可以利用**动态打包**，以便预览和广播要发送到服务的贡献源中采用 [MPEG DASH、HLS 和平滑流式处理格式](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)的实时流。 观看者可以使用任何与 HLS、DASH 或平滑流式处理兼容的播放器播放实时流。 可以使用 Web 应用程序或移动应用程序中的 [Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html) 传送采用上述任何协议的流。

借助媒体服务，可以传送使用高级加密标准 (AES-128) 或三个主要数字版权管理 (DRM) 系统（Microsoft PlayReady、Google Widevine 和 Apple FairPlay）中的任意一个动态加密（**动态加密**）的内容。 媒体服务还提供用于向已授权客户端传送 AES 密钥和 DRM 许可证的服务。 有关如何使用媒体服务加密内容的详细信息，请参阅[保护内容概述](content-protection-overview.md)

还可以应用动态筛选，以便控制发送到播放器的篇目数目、格式、比特率和呈现时间窗口。 有关详细信息，请参阅[筛选器和动态清单](filters-dynamic-manifest-overview.md)。

本文提供了使用媒体服务实时传送视频流的概述和指南。

## <a name="prerequisites"></a>先决条件

若要了解媒体服务 v3 中的实时传送视频流工作流，你需要查看并了解以下概念： 

- [流式处理终结点](streaming-endpoint-concept.md)
- [实时事件和实时输出](live-events-outputs-concept.md)

## <a name="live-streaming-workflow"></a>实时传送视频流工作流

下面是实时传送视频流工作流的步骤：

1. 导航到你的媒体服务帐户，并确保**流式处理终结点**正在运行。 
2. 创建**实时事件**。 <br/>创建事件时，可以将其启动方式指定为自动启动。 或者，可以在准备好开始流式传输后，启动事件。<br/> 如果将 autostart 设置为 true，则实时事件会在创建后立即启动。 这意味着，只要实时事件开始运行，就会开始计费。 必须显式对实时事件资源调用停止操作才能停止进一步计费。 有关详细信息，请参阅[实时事件状态和计费](live-event-states-billing.md)。
3. 获取引入 URL 并配置本地编码器以使用 URL 发送贡献源。<br/>请参阅[推荐的实时编码器](recommended-on-premises-live-encoders.md)。
4. 获取预览 URL 并使用它验证来自编码器的输入是否实际接收。
5. 创建新的**资产**对象。
6. 创建**实时输出**并使用创建的资产名称。<br/>**实时输出**会将流存档到**资产**中。
7. 使用内置的**流式处理策略**类型创建**流式处理定位符**。<br/>如果想要加密内容，请查看[内容保护概述](content-protection-overview.md)。
8. 列出流式处理定位器的路径，以取回要使用的 URL（这些是确定性的）。
9. 获取要从中流式传输的“流式处理终结点”的主机名。
10. 将步骤 8 中的 URL 与步骤 9 中的主机名合并，获取完整的 URL。
11. 如果希望停止查看**实时事件**，则需要停止流式处理事件并删除**流式处理定位符**。

## <a name="other-important-articles"></a>其他重要文章

- [推荐的实时编码器](recommended-on-premises-live-encoders.md)
- [使用云 DVR](live-event-cloud-dvr.md)
- [实时事件类型功能比较](live-event-types-comparison.md)
- [状态和计费](live-event-states-billing.md)
- [延迟](live-event-latency.md)

## <a name="next-steps"></a>后续步骤

* [实时传送视频流教程](stream-live-tutorial-with-api.md)
* [有关从媒体服务 v2 迁移到 v3 的指导](migrate-from-v2-to-v3.md)
