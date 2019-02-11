---
title: 有关 Project Acoustics 的常见问题解答
titlesuffix: Azure Cognitive Services
description: 本页面解答了有关 Project Acoustics 的常见问题，包括下载说明和制作流程。
services: cognitive-services
author: kegodin
manager: cgronlun
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: 07e65e6ff544d6372197010a2b9d7f3f647eeb0f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55166554"
---
# <a name="frequently-asked-questions"></a>常见问题

## <a name="what-is-project-acoustics"></a>什么是项目音响效果？

Project Acoustics Unity 插件是一个在运行时之前计算声波行为的音效系统，类似于静态照明。 云可以执行繁重的声波物理学计算，因此运行时 CPU 成本较低。  

## <a name="where-can-i-download-the-plugin"></a>在何处可以下载此插件？

如果有兴趣评估此音效插件，请在[此处](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRwMoAEhDCLJNqtVIPwQN6rpUOFRZREJRR0NIQllDOTQ1U0JMNVc4OFNFSy4u)注册以获取设计器预览版。

## <a name="is-azure-used-at-runtime"></a>是否在运行时使用 Azure？

不，云集成仅在预计算阶段使用，作为场景设置的一部分。
 
## <a name="what-is-simulation-input"></a>什么是模拟输入？ 

模拟输入是三维场景、虚拟环境或游戏级别。 Project Acoustics 执行三维体积波模拟，逼真地为声音物理学建模，包括平滑声笼和散射。
 
## <a name="what-is-the-runtime-cost"></a>什么是运行时成本？

每个源每帧的音效占用大约 0.01% 的 CPU。 RAM 使用量取决于场景大小，介于 10 到 100 MB 之间。
 
## <a name="do-i-need-to-simplify-the-level-geometry-control-triangle-count-make-meshes-watertight"></a>是否需要简化水平几何体？ 控制三角形计数？ 使网格严密？

不是。 系统将直接引入详细的水平几何体。 它将体素化，以便进行内部处理。
 
## <a name="whats-in-the-runtime-lookup-table"></a>运行时查找表中是什么？

ACE 文件是多个源和侦听器位置对之间的声学参数表。
 
## <a name="can-it-handle-moving-sources"></a>它是否可以处理移动源？

可以，Microsoft Acoustics Unity 空间音响插件在每个音频处理时钟周期使用当前源和侦听器位置查询查找表。 空间音响的 DSP 在每个时钟周期会顺利地更新声学处理参数。
 
## <a name="can-it-handle-dynamic-geometry-closing-doors-walls-blown-away"></a>它能否处理动态几何体？ 关门？ 墙壁被吹走了？

不是。 声学参数是根据静态的游戏级别预先计算出来的。 建议音效中不包括门几何体，然后使用已有技术根据易损坏和可移动游戏对象的状态应用其他声笼。
 
## <a name="does-it-handle-materials"></a>它是否会处理材料？

是的。 材料从你所处级别的物理材料名称中选取，促进吸音能力。
 
## <a name="what-do-the-probes-represent"></a>“探测”代表什么？

探测就是对可能的播放器位置进行采样。 每个探测表示对探测位置产生的场景单独进行声波模拟。 在运行时，从附近的探测位置内插侦听器位置的声学参数。
 
## <a name="why-spend-so-much-compute-in-the-cloud-what-does-it-buy-me"></a>为什么要在云中进行如此多的计算？ 它提供哪些功能？

Project Acoustics 提供准确可靠的声学参数，即便是面对超复杂的虚拟环境，仍将每个体系结构方面考虑在内。 它可以自动提供平滑的声笼/声障，并且无需提取音量即可提供动态混响变体。 同时，保持 CPU 在运行时期间轻负荷运行。

## <a name="what-exactly-happens-during-baking"></a>“制作”期间究竟发生了什么？

系统会考虑潜在的播放器位置，以生成一组间距均匀的“探测”采样位置。 级别的制作由每个探测的独立任务组成：系统会考虑中心位于探测处的立方体“模拟区域”，并以高达 25 cm 的分辨率在该区域内执行详细的声波模拟。

## <a name="next-steps"></a>后续步骤
* 了解[示例场景](sample-walkthrough.md)

