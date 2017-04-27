---
title: "估计 Azure RemoteApp 网络带宽的使用情况 |Microsoft Docs"
description: "了解有关 Azure RemoteApp 集合和应用的网络带宽要求。"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 3127f4c7-f532-46c3-ba9b-649f647abec1
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 8b5f4568fa4e505b28011134cc8669ea50e17cf6
ms.lasthandoff: 03/31/2017


---
# <a name="estimate-azure-remoteapp-network-bandwidth-usage"></a>估计 Azure RemoteApp 的网络带宽使用情况
> [!IMPORTANT]
> Azure RemoteApp 将于 2017 年 8 月 31 日停用。 阅读 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 了解详细信息。
> 
> 

Azure RemoteApp 使用远程桌面协议 (RDP) 在运行于 Azure 云的应用程序和你的用户之间进行通信。 本文提供的一些基本指南可用于估计网络使用情况，并对每个 Azure RemoteApp 用户的网络带宽使用情况进行潜在评估。

估计每个用户的带宽使用情况非常复杂，这需要同时在多任务处理方案中运行多个应用程序。基于每个应用程序对网络带宽的需求，它们可能会影响彼此的性能。 即使远程桌面客户端类型（如 Mac 客户端与 HTML5 客户端）也可能会导致不同的带宽结果。 为了帮助你克服这些复杂性，我们将使用方案划分为几个常用的类别以再现现实情况中的方案。 （现实情况中的方案当然是不同的类别混合，并因用户而异。）

在继续之前，请注意，我们假设 RDP 为网络中的大多数使用方案提供了绝佳的体验（ 延迟在 120 毫秒以下，带宽大于 5MB），这基于 RDP 能够根据可用网络带宽和估计的应用程序带宽需要进行动态调整。 本文将透过“大多数使用方案”来了解方案开始松懈、用户体验开始下降的界限。

查看下文以了解详细信息，包括需要考虑的因素、基本建议，以及哪些情况未包括在估计中。

* [网络带宽和高质量体验如何相辅相成？](remoteapp-bandwidthexperience.md)
* [通过一些常用方案测试网络带宽使用情况](remoteapp-bandwidthtests.md)
* [没有时间或能力进行测试时的快速指南](remoteapp-bandwidthguidelines.md)

## <a name="what-are-we-not-including"></a>哪些情况不在讨论范围？
在检查建议的测试和整体（诚然比较一般） 的建议时，请注意有几个因素不在我们的考虑范围。 例如，由于上传和下载带宽的非对称特性而产生的复杂用户体验。 大多数 Wi-fi 网络的非对称特性会额外影响性能和用户体验感受。 对于交互式方案，下游通信的优先级可能低于上游，这可能会增加丢失的视频或音频帧数，从而影响用户对流体验的感受。 你可以运行自己的实验，了解什么最适合你的具体用例和网络。

虽然我们讨论了设备重定向，但是并未考虑由于连接的设备（如存储器、 打印机、 扫描仪、 网络照相机和其他 USB 设备）对网络通信造成的带宽影响。 这些设备的影响通常使带宽需求暂时性达到峰值，任务完成后影响也随之消失。 但是如果经常发生这种情况，带宽需求可能会非常显著。

同一网络中的一位用户可能会影响其他用户的情况也不在讨论范围。 例如，在一个每秒 100 MB的网络中，一位用户观看 4k 的视频，这会严重影响同一网络上正在执行同样任务的其他用户。 遗憾的是，要确定并发使用的影响，从而提供有关系统在聚合时如何执行的常用或完整建议变得愈发困难。 只能说基础协议技术会充分利用可用网络带宽，但它的确有其自身的局限性。


