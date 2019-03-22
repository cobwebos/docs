---
title: 项目噪声方面的常见问题
titlesuffix: Azure Cognitive Services
description: 本页面解答了有关 Project Acoustics 的常见问题，包括下载说明和制作流程。
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: resources
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 3426681aad19bbe01c0f7e88ca16e79c0b490c36
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/21/2019
ms.locfileid: "58317300"
---
# <a name="project-acoustics-frequently-asked-questions"></a>项目噪声方面的常见问题

## <a name="what-is-project-acoustics"></a>什么是项目音响效果？

插件的项目噪声套件是一个计算之前运行时，类似于静态照明声波行为的噪声系统。 云可以执行繁重的声波物理学计算，因此运行时 CPU 成本较低。  

## <a name="where-can-i-download-the-plugin"></a>在何处可以下载此插件？

您可以下载[项目噪声 Unity 插件](https://www.microsoft.com/download/details.aspx?id=57346)或[项目噪声 Unreal 插件](https://www.microsoft.com/download/details.aspx?id=58090)。

## <a name="does-project-acoustics-support-x-platform"></a>支持项目噪声<x>平台？

项目的发展平台支持基于客户需求的噪声。 在上，请联系我们[项目噪声论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=projectacoustics)就可以提出有关其他平台的支持。

## <a name="is-azure-used-at-runtime"></a>是否在运行时使用 Azure？

不，云集成仅在预计算阶段使用，作为场景设置的一部分。
 
## <a name="what-is-simulation-input"></a>什么是模拟输入？ 

模拟输入是三维场景、虚拟环境或游戏级别。 Project Acoustics 执行三维体积波模拟，逼真地为声音物理学建模，包括平滑声笼和散射。
 
## <a name="what-is-the-runtime-cost"></a>什么是运行时成本？

每个源每帧的音效占用大约 0.01% 的 CPU。 RAM 使用量取决于场景大小，介于 10 到 100 MB 之间。
 
## <a name="do-i-need-to-simplify-the-level-geometry-control-triangle-count-make-meshes-watertight"></a>是否需要简化水平几何体？ 控制三角形计数？ 使网格严密？

不是。 系统将直接引入详细的水平几何体。 它将体素化，以便进行内部处理。
 
## <a name="whats-in-the-runtime-lookup-table"></a>运行时查找表中是什么？

ACE 文件包括是大量源和侦听器位置对，以及用于参数内插 voxelized 场景几何图形之间的声学参数的表。
 
## <a name="can-project-acoustics-handle-moving-sources"></a>项目噪声可以处理移动的源？

是，项目噪声参考查找表，并更新每个计时周期的音频 DSP，因此它可以处理移动源和侦听器。
 
## <a name="can-project-acoustics-handle-dynamic-geometry-closing-doors-walls-blown-away"></a>项目噪声可以处理动态 geometry？ 关门？ 墙壁被吹走了？

不是。 声学参数是根据静态的游戏级别预先计算出来的。 我们建议保留从噪声，门几何图形，然后将应用其他封闭根据易损坏的状态，并且使用可移动的游戏对象建立技术。
 
## <a name="does-project-acoustics-use-acoustic-materials"></a>项目噪声是否使用声学资料？

是的。 材料从你所处级别的物理材料名称中选取，促进吸音能力。
 
## <a name="what-do-the-probes-represent"></a>“探测”代表什么？

探测就是对可能的播放器位置进行采样。 每个探测表示对探测位置产生的场景单独进行声波模拟。 在运行时，从附近的探测位置内插侦听器位置的声学参数。
 
## <a name="why-spend-so-much-compute-in-the-cloud-what-does-it-buy-me"></a>为什么要在云中进行如此多的计算？ 它提供哪些功能？

Project Acoustics 提供准确可靠的声学参数，即便是面对超复杂的虚拟环境，仍将每个体系结构方面考虑在内。 它提供平滑的阻挡物和障碍物和绘制卷的动态混响而无需手动工作的变体。 同时，保持 CPU 在运行时期间轻负荷运行。

## <a name="what-exactly-happens-during-baking"></a>“制作”期间究竟发生了什么？

制作包含的中心位于每个侦听器探测 cuboid 模拟区域的声学批模拟。

## <a name="next-steps"></a>后续步骤
* 请尝试[项目噪声 Unity 示例内容](unity-quickstart.md)或[Unreal 示例内容](unreal-quickstart.md)

