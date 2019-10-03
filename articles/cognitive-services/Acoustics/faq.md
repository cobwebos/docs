---
title: 项目噪声常见问题解答
titlesuffix: Azure Cognitive Services
description: 本页面解答了有关 Project Acoustics 的常见问题，包括下载说明和制作流程。
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: f0484fc9fc2af5514ba0f5b61277146a51757057
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855027"
---
# <a name="project-acoustics-frequently-asked-questions"></a>项目噪声常见问题解答

## <a name="what-is-project-acoustics"></a>什么是项目音响效果？

项目的噪音套件是一个噪声系统, 它在运行时之前计算声波行为, 与静态照明类似。 云可以执行繁重的声波物理学计算，因此运行时 CPU 成本较低。  

## <a name="where-can-i-download-the-plugin"></a>在何处可以下载此插件？

您可以下载项目的 "[噪声 Unity" 插件](https://www.microsoft.com/download/details.aspx?id=57346)或 "[项目噪声 Unreal 插件](https://www.microsoft.com/download/details.aspx?id=58090)"。

## <a name="does-project-acoustics-support-ltxgt-platform"></a>项目噪声是否支持&lt;x&gt;平台？

项目噪声平台支持根据客户需求发展。 请在[项目噪声问题论坛](https://github.com/microsoft/ProjectAcoustics/issues)上联系我们以查询对其他平台的支持。

## <a name="is-azure-used-at-runtime"></a>是否在运行时使用 Azure？

不，云集成仅在预计算阶段使用，作为场景设置的一部分。
 
## <a name="what-is-simulation-input"></a>什么是模拟输入？ 

模拟输入是三维场景、虚拟环境或游戏级别。 Project Acoustics 执行三维体积波模拟，逼真地为声音物理学建模，包括平滑声笼和散射。
 
## <a name="what-is-the-runtime-cost"></a>什么是运行时成本？

每个源每帧的音效占用大约 0.01% 的 CPU。 RAM 使用量取决于场景大小，介于 10 到 100 MB 之间。
 
## <a name="do-i-need-to-simplify-the-level-geometry-control-triangle-count-make-meshes-watertight"></a>是否需要简化水平几何体？ 控制三角形计数？ 使网格严密？

否。 系统将直接引入详细的水平几何体。 它将体素化，以便进行内部处理。
 
## <a name="whats-in-the-runtime-lookup-table"></a>运行时查找表中是什么？

ACE 文件包含在许多源和侦听器位置对之间的声音参数表以及用于参数内插的 voxelized 场景几何。
 
## <a name="can-project-acoustics-handle-moving-sources"></a>能否项目噪声处理移动源？

是的, 项目噪声参考查找表, 并更新每个时钟周期的音频 DSP, 使其可以处理移动源和侦听器。
 
## <a name="can-project-acoustics-handle-dynamic-geometry-closing-doors-walls-blown-away"></a>项目噪声是否可以处理动态几何？ 关门？ 墙壁被吹走了？

否。 声学参数是根据静态的游戏级别预先计算出来的。 建议使用已建立的技术, 根据易损坏和可移动游戏对象的状态, 将门几何图形从噪声中封闭出来。
 
## <a name="does-project-acoustics-use-acoustic-materials"></a>项目噪音是否使用音响材料？

是的。 材料从你所处级别的物理材料名称中选取，促进吸音能力。
 
## <a name="what-do-the-probes-represent"></a>“探测”代表什么？

探测就是对可能的播放器位置进行采样。 每个探测表示对探测位置产生的场景单独进行声波模拟。 在运行时，从附近的探测位置内插侦听器位置的声学参数。
 
## <a name="why-spend-so-much-compute-in-the-cloud-what-does-it-buy-me"></a>为什么要在云中进行如此多的计算？ 它提供哪些功能？

Project Acoustics 提供准确可靠的声学参数，即便是面对超复杂的虚拟环境，仍将每个体系结构方面考虑在内。 它提供平滑的封闭、障碍和动态回音变化, 无需手动处理卷。 同时，保持 CPU 在运行时期间轻负荷运行。

## <a name="what-exactly-happens-during-baking"></a>“制作”期间究竟发生了什么？

制作包含 cuboid 模拟区域的声波模拟, 并以每个侦听器探测为中心。

## <a name="next-steps"></a>后续步骤
* 尝试[项目噪声 Unity 示例内容](unity-quickstart.md)或[Unreal 示例内容](unreal-quickstart.md)

