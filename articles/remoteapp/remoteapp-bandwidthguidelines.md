---
title: "Azure RemoteApp 网络带宽 - 通用指南 | Microsoft Docs"
description: "了解 Azure RemoteApp 集合和应用的一些基本网络带宽指南。"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 529bf318-ae37-4c14-a11c-43fa703d68a3
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b44fcb6cca30acbd5771a0301f72faa7d9105849


---
# <a name="azure-remoteapp-network-bandwidth---general-guidelines-if-you-cant-test-your-own"></a>Azure RemoteApp 网络带宽 - 通用指南（如果你无法自行测试）
> [!IMPORTANT]
> 正在中断 Azure RemoteApp。 阅读 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 了解详细信息。
> 
> 

如果你没有时间或能力运行 Azure RemoteApp 的[网络带宽测试](remoteapp-bandwidthtests.md)，下面有一些常用的指南，可以帮助你估计每个用户的网络带宽。

如果你混合使用这些方案，我们不建议将小于（或等于）10 MB/s 的任何带宽作为远程环境中新式 Internet 连接的应用的最低网络带宽。 （但是，如前所述，这并不保证比一般用户的体验更好。）

## <a name="complex-powerpoint-simple-powerpoint"></a>复杂的 PowerPoint，简单的 PowerPoint
Azure RemoteApp 在 100 MB LAN 上的运行状态最佳。 在 10 MB/s 网络配置文件中，当高于 120 ms 的抖动超过 5% 时，用户将看到一般体验。 速率为 1 MB/s 时，差异比较明显，例如，在幻灯片放映中，用户可能根本看不到动画过渡，因为帧会被跳过。

## <a name="internet-explorer-mixed-pdf-pdf-text"></a>Internet Explorer、混合 PDF、PDF、文本
在大多数方面，10 MB/s 网络配置文件接近 LAN。 1 MB/s 将提供一个不错的体验，虽然用户滚动可能有一些抖动，但屏幕上会有图像。

## <a name="flash-video-youtube"></a>Flash 视频 (YouTube)
100 MB/s LAN 提供最佳体验，而 10 MB/s 是可接受的（意味着可以跟得上帧速率，但抖动会增加）。 速率为 1 MB/s 时，抖动非常高且明显。

## <a name="word-typing-word-remote-input"></a>Word 键入（Word 远程输入）
这是低带宽使用案例。 速率为 256 KB/s 时，我们提供与 LAN 一样好的体验。

## <a name="learn-more"></a>了解详细信息
* [估计 Azure RemoteApp 的网络带宽使用情况](remoteapp-bandwidth.md)
* [Azure RemoteApp - 如何兼顾网络带宽和体验质量？](remoteapp-bandwidthexperience.md)
* [Azure RemoteApp - 通过一些常见案例测试你的网络带宽使用情况](remoteapp-bandwidthtests.md)




<!--HONumber=Nov16_HO3-->


