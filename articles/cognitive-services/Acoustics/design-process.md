---
title: 音效设计过程概述 - 认知服务
description: 本文档介绍如何在 Project Acoustics 工作流的所有三个阶段中表达设计意图。
services: cognitive-services
author: kegodin
manager: noelc
ms.service: cognitive-services
ms.component: acoustics
ms.topic: article
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: 3b35f78d66a6ae66b0a56818f5d4be455ce00de5
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2018
ms.locfileid: "40181158"
---
# <a name="design-process-overview"></a>设计流程概述
用户可以在 Project Acoustics 工作流的以下三个阶段中表示达设计意图：预先制作设计、声源放置和后期制作设计。 该过程需要更少的与放置混响音量相关的标记，同时保持设计器控制场景的音响效果。

## <a name="pre-bake-design"></a>预先制作设计
预先制作设计过程可生成用于声波模拟的场景和元数据，包括选择要参与模拟的场景元素以提供声笼、反射和混响。 场景的元数据是针对每个场景元素所选的声学材料。 声学材料控制从每个表面反射回来的声音能量的大小。

所有表面的默认吸收系数均为 0.04，表示反射性很强。 通过优化整个场景中不同材料的吸收系数可实现美感和游戏效果，当侦听器听到从场景的某个区域转换到另一区域时，这种效果尤为突出。 例如，从黑暗的混响房间转换到明亮的非混响户外场景会增强转换的影响。 要实现此效果，请调高户外场景材料的吸收系数。

房间中给定材料的混响时间与其吸收系数成反比，并且大多数材料的吸收系数值介于 0.01 到 0.20 之间。 如果材料的吸收系数超出此范围，则其吸收性很强。

![混响时间关系图](media/ReverbTimeGraph.png)

[制作 UI 演练](bake-ui-walkthrough.md)详细介绍了预先制作控件。

## <a name="sound-source-placement"></a>声源放置
在运行时查看体素和探测点有助于调试陷入体素化几何体内的声源的问题。 要切换体素网格和探测点显示，请单击场景视图右上方的 Gizmos 菜单中的相应复选框。 如果声源位于墙壁体素内，请将其移动到空气体素。

![Gizmos 菜单](media/GizmosMenu.png)  

体素显示可以帮助确定游戏中的视觉对象组件是否已应用转换。 如果已应用，请将该转换也应用到承载音效管理器的 GameObject。

## <a name="post-bake-design"></a>后期制作设计
制作结果存储在 ACE 文件中，作为整个场景中所有源侦听器位置对的声笼和混响参数。 此物理上准确的结果可按原样用于项目，是设计的良好开端。 后期制作设计过程指定在运行时转换制作结果参数的规则。

### <a name="distance-based-attenuation"></a>基于距离的衰减
Microsoft Acoustics Unity 空间音响插件提供的音频 DSP 遵守内置于 Unity 编辑器的基于距离的按源衰减。 针对基于距离的衰减的控件位于“三维声音设置”下的声源“检查器”面板的“音频源”组件中：

![距离衰减](media/distanceattenuation.png)

### <a name="tuning-scene-parameters"></a>优化场景参数
要调整所有源的参数，请单击 Uinty 的音频混音器中的声道条带，然后调整“音效混音器”效果的参数。

![混音器自定义](media/MixerParameters.png)

### <a name="tuning-source-parameters"></a>优化源参数
将 AcousticsSourceCustomization 脚本附加到源可支持优化该源的参数。 要附加脚本，请单击“检查器”面板底部的“添加组件”，然后导航到“脚本”>“音效源自定义”。 脚本包含三个参数：

![源自定义](media/SourceCustomization.png)

* 混响功率调整 - 调整混响功率（以 dB 为单位）。 正值可增强声音混响效果，而负值则使声音更干涩。
* 衰减时间刻度 - 调整衰减时间的倍数。 例如，如果制作结果指定衰减时间为 750 毫秒，但此值设置为 1.5，则应用于源的衰减时间将为 1,125 毫秒。
* 启用音效 - 控制是否对此源应用音效。 取消选中后，系统会使用 HRTF 空间化源，但没有音效，也就是说没有声障、声笼、动态混响参数（例如级别和衰减时间）。 系统仍然以固定的级别和衰减时间应用混响。

不同的源可能需要不同的设置，以实现特定美学或游戏效果。 例如，对话。 人耳更适应语音中的混响，而游戏中的对话通常需要明白易懂。 通过调低混响功率，无需使对话变为无源音即可实现此效果。
