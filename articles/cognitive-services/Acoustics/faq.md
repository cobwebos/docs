---
title: 项目声学 常见问题
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
ms.openlocfilehash: fa4b6499260219b0eb8ea4df4b4ccfd5263b57bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "75770197"
---
# <a name="project-acoustics-frequently-asked-questions"></a>项目声学 常见问题

## <a name="what-is-project-acoustics"></a>什么是项目音响效果？

项目声学插件套件是一个声学系统，用于在运行时之前计算声波行为，类似于静态照明。 云可以执行繁重的声波物理学计算，因此运行时 CPU 成本较低。  

## <a name="where-can-i-download-the-plugin"></a>在何处可以下载此插件？

您可以下载[项目声学统一插件](https://www.microsoft.com/download/details.aspx?id=57346)或[项目声学虚幻插件](https://www.microsoft.com/download/details.aspx?id=58090)。

## <a name="does-project-acoustics-support-ltxgt-platform"></a>项目声学是否支持&lt;x&gt;平台？

项目声学平台支持根据客户需求而发展。 请通过[项目声学问题论坛](https://github.com/microsoft/ProjectAcoustics/issues)与我们联系，询问对其他平台的支持。

## <a name="is-azure-used-at-runtime"></a>是否在运行时使用 Azure？

不，云集成仅在预计算阶段使用，作为场景设置的一部分。
 
## <a name="what-is-simulation-input"></a>什么是模拟输入？ 

模拟输入是三维场景、虚拟环境或游戏级别。 Project Acoustics 执行三维体积波模拟，逼真地为声音物理学建模，包括平滑声笼和散射。
 
## <a name="what-is-the-runtime-cost"></a>什么是运行时成本？

每个源每帧的音效占用大约 0.01% 的 CPU。 RAM 使用量取决于场景大小，介于 10 到 100 MB 之间。
 
## <a name="do-i-need-to-simplify-the-level-geometry-control-triangle-count-make-meshes-watertight"></a>是否需要简化水平几何体？ 控制三角形计数？ 使网格严密？

不是。 系统将直接引入详细的水平几何体。 它将体素化，以便进行内部处理。
 
## <a name="whats-in-the-runtime-lookup-table"></a>运行时查找表中是什么？

ACE 文件包括一个多个源和侦听器位置对之间的声学参数表，以及用于参数插值的体素化场景几何体。
 
## <a name="can-project-acoustics-handle-moving-sources"></a>项目声学可以处理移动源吗？

是的，Project 声学会查阅查找表并更新每个刻度上的音频 DSP，以便它可以处理移动源和侦听器。
 
## <a name="can-project-acoustics-handle-dynamic-geometry-closing-doors-walls-blown-away"></a>项目声学可以处理动态几何？ 关门？ 墙壁被吹走了？

不是。 声学参数是根据静态的游戏级别预先计算出来的。 我们建议将门几何体排除在声学外，然后根据可破坏和可移动的游戏对象的状态使用已建立的技术应用额外的遮挡。
 
## <a name="does-project-acoustics-use-acoustic-materials"></a>项目声学是否使用声学材料？

是的。 材料从你所处级别的物理材料名称中选取，促进吸音能力。
 
## <a name="what-do-the-probes-represent"></a>“探测”代表什么？

探测就是对可能的播放器位置进行采样。 每个探测表示对探测位置产生的场景单独进行声波模拟。 在运行时，从附近的探测位置内插侦听器位置的声学参数。
 
## <a name="why-spend-so-much-compute-in-the-cloud-what-does-it-buy-me"></a>为什么要在云中进行如此多的计算？ 它提供哪些功能？

Project Acoustics 提供准确可靠的声学参数，即便是面对超复杂的虚拟环境，仍将每个体系结构方面考虑在内。 它提供了平滑的遮挡和阻塞和动态混响变化，而无需绘图卷的手动工作。 同时，保持 CPU 在运行时期间轻负荷运行。

## <a name="what-exactly-happens-during-baking"></a>“制作”期间究竟发生了什么？

烘焙由以每个侦听器探测器为中心的立体模拟区域的声波模拟组成。

## <a name="is-my-source-content-secure"></a>我的源内容是否安全？

项目声学不会将源场景几何体上载到云。 相反，模拟在场景的体素化上运行，该体化与探测位置数据结合使用，并以专有格式存储。     

## <a name="next-steps"></a>后续步骤
* 尝试[项目声学统一示例内容](unity-quickstart.md)或[虚幻示例内容](unreal-quickstart.md)

