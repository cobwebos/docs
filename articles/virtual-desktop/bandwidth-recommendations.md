---
title: 远程会话的带宽建议-Azure
description: 远程会话的可用带宽建议。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/30/2019
ms.author: helohr
ms.openlocfilehash: 1a88cfb2fcd5f0b607e7a2b43ae833789a584e34
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695317"
---
# <a name="bandwidth-recommendations-for-remote-sessions"></a>远程会话的带宽建议

使用远程 Windows 会话时，网络的可用带宽会极大地影响你的体验质量。 不同的应用程序和显示分辨率需要不同的网络配置，因此请务必确保网络已配置为满足你的需求。 本文介绍每个工作负荷的建议带宽。

>[!NOTE]
>以下建议适用于低于 0.1% 的网络丢失。

## <a name="applications"></a>应用程序

下表列出了有关平滑用户体验的最低要求。 

|工作负荷        |示例应用程序                                                                                           |建议的带宽|
|----------------|--------------------------------------------------------------------------------------------------------------|---------------------|
|任务辅助     |Microsoft Word、Outlook、Excel、Adobe Reader                                                                  |1.5 @ no__t-0Mbps             |
|Office 辅助角色   |Microsoft Word、Outlook、Excel、Adobe Reader、PowerPoint、照片查看器                                        |3 @ no__t-0Mbps               |
|知识工作者|Microsoft Word，Outlook，Excel，Adobe Reader，PowerPoint，照片查看器，Java                                  |5 @ no__t-0Mbps               |
|Power worker    |Microsoft Word，Outlook，Excel，Adobe Reader，PowerPoint，照片查看器，Java，CAD/CAM，插图/发布|15 @ no__t-0Mbps              |

请记住，在网络上施加压力取决于应用工作负荷的输出帧速率和显示分辨率。 如果帧速率或显示分辨率增加，带宽需求也会增加。 例如，使用高分辨率显示器的轻型工作负荷比使用常规或低分辨率的轻型工作负荷需要更多的带宽。

其他方案可能会根据使用方式来更改其带宽要求，例如：

- 语音或视频会议
- 实时通信
- 流式处理4K 视频

请确保使用登录 .VSI 等模拟工具在部署中对这些方案进行负载测试。 在远程会话中改变负载大小、运行压力测试和测试常见用户方案，以便更好地了解网络需求。 

## <a name="display-resolutions"></a>显示分辨率

不同的显示分辨率需要不同的可用带宽。 下表列出了我们建议在典型显示分辨率下实现平滑用户体验的带宽，帧速率为每秒30帧（fps）。 请记住，在 30 fps 下出现帧速率（如读取静态文本）的方案需要较少的可用带宽。 

|典型显示分辨率为 30 fps    |建议的带宽|
|-----------------------------------------|---------------------|
|大约1024× 768 px                      |1.5 Mbps             |
|大约1280× 720 px                      |3 Mbps               |
|大约1920× 1080 px                     |5 Mbps               |
|大约3840× 2160 px （4K）                |15 Mbps              |
