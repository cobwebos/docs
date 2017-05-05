---
title: "Azure RemoteApp - 网络带宽和体验质量如何相辅相成？ | Microsoft 文档"
description: "了解 Azure RemoteApp 中的网扩带宽如何影响用户的体验质量。"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 74ebc1fb-5187-4056-b08c-0e03b5ecaca6
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 1a1f772c12b2eb4470bd239f0325cc4ae18b9680
ms.lasthandoff: 03/31/2017


---
# <a name="azure-remoteapp---how-do-network-bandwidth-and-quality-of-experience-work-together"></a>Azure RemoteApp - 网络带宽和体验质量如何相辅相成？
> [!IMPORTANT]
> Azure RemoteApp 将于 2017 年 8 月 31 日停用。 阅读 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 了解详细信息。
> 
> 

在查看 Azure RemoteApp 所需的[整体网络带宽](remoteapp-bandwidth.md)时，记住以下因素 - 它们是影响整体用户体验的动态系统的所有组成部分。 

* **可用网络带宽和当前网络条件** - 在给定时间位于同一网络的参数集（丢失、延迟、抖动），可以影响应用程序流式处理体验，这意味着一种降低的整体用户体验。 网络中的可用带宽是拥塞、随机丢失、延迟的函数，因为所有的这些参数都会影响拥塞控制机制，该机制会反过来控制传输速度以避免冲突。  例如，性能不好的网络或具有高延迟的网络即使在 1000 MB 带宽的网络上也会给用户带来很糟糕的体验。 丢失和延迟情况基于在同一网络上的用户数量和用户正在执行的操作（例如，观看视频、下载或上载大型文件、打印）而不同。
* **使用方案** - 体验取决于用户作为个体和作为在同一网络上的组正在执行的操作。 例如，阅读幻灯片仅需要更新单个帧；如果用户扫读和滚动文本文档的内容，则需要每秒更新更多数量的帧。 此方案中往返到服务器的通信最终会消耗更多的网络带宽。 还可以考虑一个极端示例：多个用户在观看高清视频（例如 4K 分辨率）、进行高清会议电话、玩 3D 视频游戏或正在使用 CAD 系统。 所有的这些操作都可使一个具有非常高带宽的网络在实际使用中无法使用。
* **屏幕分辨率和屏幕的数量** - 相对较小的屏幕，较大的屏幕完全更新需要的网络带宽更高。 基础技术在仅对已更新屏幕区域进行编码和传输方面做得不错，但是有时需要更新整个屏幕。 用户的屏幕分辨率更高时（例如 4K 分辨率），则更新需要的网络带宽高于具有较低分辨率（例如 1024x768px）的屏幕。 同样的逻辑适用于使用多个屏幕进行重定向的情况。 带宽需要随屏幕数量的增加而增加。
* **剪贴板和设备重定向** - 这不是非常明显的问题，但是在许多情况下，如果用户将大量数据存储到剪贴板，则需要一些时间才能将此信息从远程桌面客户端传输到服务器。 下游体验可能受上游发送剪贴板内容体验的影响。 这也适用于设备重定向 - 如果扫描仪或 Web 摄像头生成需要上游传送到服务器的大量数据、或者打印机需要接收大文档、或需要本地存储可供在云中运行的应用复制大型文件使用，用户可能会注意到丢弃的帧或或视频暂时“冻结”的现象，因为设备重定向需要的数据增加了网络带宽的需求。 

评估网络带宽需求时，请确保考虑到所有的这些因素作为一个系统发挥作用。

现在，返回到[主网络带宽文章](remoteapp-bandwidth.md)，或者开始测试[网络带宽](remoteapp-bandwidthtests.md)。

## <a name="learn-more"></a>了解详细信息
* [估计 Azure RemoteApp 的网络带宽使用情况](remoteapp-bandwidth.md)
* [Azure RemoteApp - 通过一些常见方案测试网络带宽使用情况](remoteapp-bandwidthtests.md)
* [Azure RemoteApp 网络带宽 - 通用指南（如果无法自行测试）](remoteapp-bandwidthguidelines.md)


