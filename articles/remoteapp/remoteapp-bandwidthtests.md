---
title: "Azure RemoteApp - 通过一些常见方案测试网络带宽使用情况 | Microsoft Docs"
description: "了解常见的使用方案如何帮助确定 Azure RemoteApp 的网络带宽需求。"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 06417c75-0c63-4ecf-b9d1-66a7af6b7b91
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 1729d9ba3f873800127ba3ca77676b843611fded


---
# <a name="azure-remoteapp---testing-your-network-bandwidth-usage-with-some-common-scenarios"></a>Azure RemoteApp - 通过一些常见方案测试网络带宽使用情况
> [!IMPORTANT]
> 正在中断 Azure RemoteApp。 阅读 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 了解详细信息。
> 
> 

正如我们在[估计 Azure RemoteApp 的网络带宽使用情况](remoteapp-bandwidth.md)中所讨论的那样，确定 Azure RemoteApp 对网络的影响的最佳方法是运行一些使用情况测试。 将这些测试运行特定的一段时间，然后测量每个方案需要的带宽。 如果可以，还可以测量网络数据包丢失率和网络抖动率，以了解将在特定环境中创建的网络模式。

评估带宽使用情况时，请注意公司内不同用户的使用情况存在差异。 例如，通常文本读取用户和写入用户消耗的带宽要少于使用视频工作的用户。 为了获取最佳结果，请研究自己用户的需求，并创建由以下方案组成的组合，以便能够最全面地代表公司中的用户。 请务必[查看影响带宽使用情况和用户体验的因素](remoteapp-bandwidthexperience.md) - 这将有助于标识出最佳测试。

首先了解这些测试，选择组合，然后运行测试。 可以使用下表来帮助跟踪性能。

> [!NOTE]
> 如果无法完成自己的网络测试，或者没有时间执行测试，则请查看我们的[基本网络带宽估计/建议](remoteapp-bandwidthguidelines.md)。 过程可能会有所变化，但是如果*可以*运行自己的测试，则应该运行。
> 
> 

## <a name="the-usage-tests"></a>使用情况测试
其中的每个测试运行的时长不同，测试的消耗网络带宽的功能也不同。 请务必选择最匹配个人公司用户的测试组合。

### <a name="executivecomplex-powerpoint---run-for-900-1000-seconds"></a>高级/复杂 PowerPoint - 运行时间为 900-1000 秒
用户使用 Microsoft Office PowerPoint 以全屏模式演示 45-50 张高保真幻灯片。 幻灯片应含有图像、过渡效果（动画）和带有颜色渐变效果的公司典型背景。 用户在每张幻灯片上应至少花费 20 秒的时间。

此方案在演示过程中将幻灯片切换到下一张时会产生突发流量。

### <a name="simple-powerpoint---run-for-620-seconds"></a>简单 PowerPoint - 运行时间为 ~620 秒
用户使用 Microsoft Office PowerPoint 以全屏模式演示大约含有 30 张幻灯片的简单 PowerPoint 文件。 这些幻灯片的文本密集程度高于高级/复杂 PowerPoint 方案中的幻灯片，其背景和图像（黑色图表）更为简单。 

### <a name="internet-explorer---run-for-250-seconds"></a>Internet Explorer - 运行时间为 ~250 秒
用户使用 Internet Explorer 浏览 Web。 用户浏览和滚动查看文本、自然图像和一些示意图的组合。 Web 页面存储为远程桌面会话主机（RD 会话主机）服务器的本地磁盘驱动器上的 .MHT 文件。 用户使用“上一页”、“下一页”、“向上”、“向下”键滚动，每个键/类型滚动的间隔不同：

    - 向下 - 每 500 ms 250 次击键
    - 上一页 - 每 1000 ms 36 次击键
    - 向下 - 每 100 ms 75 次击键
    - 下一页 - 每 500 ms 20 次击键
    - 向上 - 每 300 ms 120 次击键

### <a name="pdf-document---simple---run-for-610-seconds"></a>PDF 文档 - 简单 - 运行时间为 ~610 秒
用户使用 Adobe Acrobat Reader 以各种方式读取和搜索 PDF 文档。 文档应包含表格、简单图表和多种文本字体。 文档长达 35-40 页。 用户能以两种不同的速度，向后和向前，以四种不同的缩放大小（适应页面、适应宽度、100% 和所选的另一种大小）进行滚动。 缩放保证文本（字体）以不同的大小显示。 使用“上一页”、“下一页”、“向上”、“向下”键进行滚动，每个滚动的间隔不同。

### <a name="pdf-document---mixed---run-for-320-seconds"></a>PDF 文档 - 混合 - 运行时间为 ~320 秒
用户使用 Adobe Acrobat Reader 以各种方式读取和搜索 PDF 文档。 文档包含高质量图像（包括照片）、表格、简单图表和多种文本字体。 用户能以两种不同的速度，向后和向前，以四种不同的缩放大小（适应页面、适应宽度、100% 和所选的另一种大小）进行滚动。 缩放保证文本（字体）以不同的大小显示。 使用“上一页”、“下一页”、“向上”、“向下”键进行滚动，每个滚动的间隔不同。

### <a name="flash-video-playback---run-for-180-seconds"></a>Flash 视频播放 - 运行时间为 ~180 秒
用户查看嵌入 Web 页面的 Adobe Flash 编码视频。 Web 页面存储在 RD 会话主机服务器的本地硬盘驱动器中。 视频通过嵌入的播放器插件在 Internet Explorer 中播放。

此方案模拟用户查看包含多媒体的内容丰富的 Web 页面。 其中大多数数据通过 VOBR 传送。

### <a name="word-remote-typing---run-for-1800-seconds"></a>Word 远程键入 - 运行时间 ~1800 秒
用户通过 RDP 会话键入文档。 击键通过 RDP 会话从客户端发送至远程会话中运行的 Microsoft Word 中的文档。 键入速度为每 250 ms 1 个字符（总计 7050 个字符）。 

这是知识工作者最常见的方案之一。 此方案测试用户键入新式工作处理器的响应能力。 此方案甚至可以区分带宽使用情况中的微小变化。

## <a name="tracking-the-test-results"></a>跟踪测试结果
可以使用下表评估环境中的方案。 下列数据仅供说明使用 - 与观测到的数据可能会存在巨大差异。 

为简单起见，我们假设使用相同的 1920x1080 像素屏幕分辨率测试所有方案，且 TCP 在延迟时间（滞后时间）低于 200 ms、在 120 ms+ 标记中的网络抖动率大约为 1% 的网络上传输。

关于表格：

* **中等体验**包含未对用户工作效率产生重大影响的网络带宽，但是未排除偶尔的视频或音频问题。 系统可以通过使用动态逻辑快速恢复。 网络带宽估计尝试保障用户体验的质量。
  * **显著问题(断点)**包含用户可能会在他们的体验过程中发现重大问题且他们的工作效率受到重大影响的网络带宽。 此时 RDP 算法出现问题，因为网络带宽不足而无法保证用户的体验质量。
  * **建议**包含建议的网络带宽以实现良好或最佳的体验。 通常要比对应的**中等体验**列中的值高一个级别。
  * **注意**包含观察数据和注释。

| 测试 | 中等体验 | 显著问题(断点) | 建议的网络带宽 | 说明 |
| --- | --- | --- | --- | --- |
| 高级/复杂 PPT |10 MB/秒 |1 MB/秒 |>10 MB/秒，首选 100 MB/秒 |带宽为 1 MB/秒时，会造成许多动画效果丢失 |
| 简单 PPT |5 MB/秒 |256 KB/秒 |10 MB/秒 |带宽为 256 KB/秒时，幻灯片加载出现明显滞后 |
| Internet Explorer |10 MB/秒 |1 MB/秒 |>10 MB/秒，首选 100 MB/秒 |带宽为 1 MB/秒时，Web 视频模糊且不连贯，快速滚动时会出现问题 |
| 简单 PDF |1 MB/秒 |256 KB/秒 |5 MB/秒 |带宽为 256 KB/秒时，加载页面需要一些时间 |
| 组合 PDF |1 MB/秒 |256 KB/秒 |5 MB/秒 |带宽为 256 KB/秒时，页面加载需要很长一段时间 |
| Flash 视频播放 |10 MB/秒 |1 MB/秒 |>10 MB/秒，首选 100 MB/秒 |带宽为 1 MB/秒时，视频颗粒感过强并且会丢掉一些帧 |
| Word 远程键入 |256 KB/秒 |128 KB/s |1 MB/秒 |带宽为 256 KB/秒时，用户可能会注意到击键之间的时间延迟 |

若要评估每位用户的网络带宽，创建以上方案的组合和所需的网络带宽的相应比例。 选择方案需要的最大带宽。 因为用户很少单独使用系统，请考虑为在同一个网络上同时工作的用户预留一些带宽。

## <a name="learn-more"></a>了解详细信息
* [估计 Azure RemoteApp 的网络带宽使用情况](remoteapp-bandwidth.md)
* [Azure RemoteApp - 网络带宽和体验质量如何相辅相成？](remoteapp-bandwidthexperience.md)
* [Azure RemoteApp 网络带宽 - 通用指南（如果无法自行测试）](remoteapp-bandwidthguidelines.md)




<!--HONumber=Nov16_HO3-->


