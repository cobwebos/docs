---
title: 包含音效模拟的设计概念
titlesuffix: Azure Cognitive Services
description: 此概念性概述介绍了项目噪音如何将声音模拟并入声音设计过程。
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 11e1e3f45b5198ddedb6c31fcd354185adef445d
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854296"
---
# <a name="project-acoustics-design-process-concepts"></a>项目噪声设计过程概念

此概念性概述介绍了项目噪音如何将物理声音模拟并入声音设计过程。

## <a name="sound-design-with-audio-dsp-parameters"></a>音频设计与音频 DSP 参数

三维交互式标题使用在音频引擎中托管的音频数字信号处理 (DSP) 块来实现其特定声音。 这些块的范围从简单混合到 reverberation、回显、延迟、均衡、压缩和限制以及其他影响。 选择、排列和设置这些效果的参数是声音设计器的责任, 该设计器会构建一个音频图形, 该图形可实现经验的美观和游戏目标。

在交互式标题中, 当声音和侦听器在整个三维空间中移动时, 这些参数如何适应变化的条件？ 声音设计器通常会对空间中的卷进行编程, 以触发参数更改, 以在 reverberation 的效果发生变化时, 或将声音从场景的一个部分移到另一个部分。 还提供了噪声系统, 可自动执行其中的某些影响。

3D 标题使用具有物理学的照明和运动物理系统, 并调整设计器, 以实现浸入式和游戏目标的混合。 可视化设计器不会设置单个像素值, 而是调整从物理上说出的、用于权衡视觉美观和 CPU 成本的3D 模型、材料和轻型传输系统。 音频的等效过程是什么？ 项目噪声是探索此问题的第一步。 首先, 我们将了解通过空间传输 acoustical 能量的含义。

![与回音区重叠的 AltSpace 场景屏幕截图](media/reverb-zones-altspace.png)

## <a name="impulse-responses-acoustically-connecting-two-points-in-space"></a>脉冲响应:Acoustically 在空间中连接两个点

如果你熟悉音频设计, 可能会熟悉声音脉冲响应。 声音脉冲响应可对从源到侦听器的声音传输建模。 因此, 脉冲响应可以捕获会议室噪声的每个有趣效果, 如封闭和 reverberation。 脉冲响应还具有一些功能强大的属性, 这些属性允许缩放音频 DSP 效果。 同时添加两个音频信号, 并使用脉冲响应进行处理可提供与将脉冲响应分别应用于每个信号并添加结果相同的结果。 声音传播和脉冲响应也不依赖于正在进行建模的场景以及源和侦听器位置上正在处理的音频。 简而言之, 脉冲响应提取场景对声音传播的影响。

脉冲响应捕获每个有意义的会议室声音效果, 可以使用筛选器有效地将其应用于音频, 我们可以从测量或模拟获得脉冲响应。 但是, 如果我们不想要准确地完全匹配物理学, 而是将它与场景的情绪需求相匹配, 那该怎么办呢？ 但与像素值非常类似, 脉冲响应只是数千个数字的列表, 如何调整它来满足美观需求？ 如果我们想要让封闭/障碍在通过两侧或障碍后变得更加平稳, 则需要多少脉冲响应才能获得平滑效果？ 如果源移动速度快, 会怎么样？ 如何插入？

在交互式标题中, 对于噪声的某些方面, 很难使用模拟和脉冲响应。 但我们仍可以构建支持设计器调整的音频传输系统, 前提是我们可以使用熟悉的音频 DSP 效果参数连接脉冲响应。

## <a name="connecting-simulation-to-audio-dsp-with-parameters"></a>用参数将模拟连接到音频 DSP

脉冲响应包含每个有趣 (以及每个不相关的) acoustical 效果。 音频 DSP 块在其参数设置正确时, 可能会导致感兴趣的 acoustical 效果。 在三维场景中使用 acoustical 模拟驱动音频 DSP 来自动传输音频, 只需从脉冲响应测量音频 DSP 参数即可。 对于某些常见的重要 acoustical 效果 (包括封闭、障碍、portalling 和 reverberation), 很好地理解这一衡量。

但如果模拟直接连接到音频 DSP 参数, 其中是设计器调整？ 我们的目标是什么？ 好了, 我们会丢弃脉冲响应并保留几个 DSP 参数, 从而获得大量内存。 为了使设计人员能够对最终结果进行一些了解, 只需寻找一种方法在模拟和音频 DSP 之间插入设计器。

![带有已重叠参数的具有风格脉冲响应的图形](media/acoustic-parameters.png)

## <a name="sound-design-by-transforming-audio-dsp-parameters-from-simulation"></a>通过从模拟转换音频 DSP 参数来设计声音

请考虑太阳镜对世界视图的影响。 在一天的某一天, 眼镜可以减少对某些内容的照射。 在深灰色空间中, 可能根本看不到任何内容。 眼镜在所有情况下不会设置一定程度的亮度;它们只会使所有内容变得更暗。

如果我们使用模拟来驱动音频 DSP, 使用封闭和 reverberation 参数, 则可以在模拟器之后添加筛选器, 以调整 DSP "发现" 的参数。 筛选器不会强制实施特定级别的封闭或回音尾部长度, 这非常类似于太阳镜不能使每个空间都具有相同的亮度。 筛选器可能只是使每个 occluder 遮蔽更少。 或遮蔽更多。 通过添加和调整一个 "变暗" 的封闭参数筛选器, 大型打开的房间仍不会有封闭的影响, 而两侧将从中等增长到强封闭效果, 同时保留平滑转换提供的。

在此模式下, 设计器的任务从选择每种情况的声音参数更改为, 选择并调整筛选器, 以应用于模拟的最重要 DSP 参数。 它从设置平滑转换到封闭和 reverberation 效果强度较高的问题以及组合中存在源的更大顾虑, 提升了设计人员的活动。 当然, 在这种情况下, 一个筛选器始终可用只是在特定情况下返回到特定源的 DSP 参数。

## <a name="sound-design-in-project-acoustics"></a>项目噪音中的声音设计

项目噪声包集成了上述每个组件: 一个模拟器、一个提取参数的编码器, 并生成了一个声音资产、音频 DSP 和一个选择的筛选器。 具有项目噪音的声音设计需要选择筛选器的参数, 这些筛选器会调整派生自模拟的封闭和 reverberation 参数, 并应用于音频 DSP, 并在游戏编辑器和音频引擎内公开动态控件。

## <a name="next-steps"></a>后续步骤
* 试用[适用于 Unity 的项目噪声快速入门](unity-quickstart.md)或[Unreal 的项目噪声快速入门](unreal-quickstart.md)的设计模式
* 探索适用于 Unity 或[项目的 Unreal](unreal-workflow.md)的噪声设计[控件](unity-workflow.md)

