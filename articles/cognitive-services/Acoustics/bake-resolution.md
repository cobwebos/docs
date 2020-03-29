---
title: 项目声学烘焙分辨率
titlesuffix: Azure Cognitive Services
description: 此概念概述描述了烘焙声学时粗分辨率和精细分辨率之间的区别。
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: d8eb3b2cbaf7b4e842d8338eefde756f6d381111
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "68854344"
---
# <a name="project-acoustics-bake-resolution"></a>项目声学烘焙分辨率
此概念概述描述了烘焙声学时粗分辨率和精细分辨率之间的区别。 在烘焙工作流的"探测"步骤中选择此设置。

## <a name="coarse-vs-fine-resolution"></a><a name="Coarse-vs-Fine-Resolution"></a>粗糙与精细分辨率

高分辨率和低分辨率设置之间的唯一区别在于模拟的执行频率。 高分辨率的执行频率是低分辨率的两倍。 这对声学仿真具有许多影响：

* 低分辨率的波长是高分辨率的两倍，因此体素是后者的两倍大。
* 模拟时间与体素大小直接相关，因此低分辨率烘焙的速度约为高分辨率烘焙的 16 倍。
* 无法模拟小于体素大小的门户（例如，门或窗）。 粗糙设置可能会导致某些较小的门户不被模拟;因此，如果设置粗糙，则可能导致某些较小的门户被模拟。因此，它们不会在运行时传递声音。 通过查看体素可确定是否发生该情况。
* 更低的模拟频率将减少角和边周围的衍射。
* 声源不能位于"填充"体素（即包含几何体的体素）内。 这会导致没有声音。 放置声源比使用精细设置时不在较大的粗体内更难放置声音源。
* 如下所示，更大的体素将更多地挤入门户。 第一张图像是使用低分辨率创建的，而第二张是使用高分辨率的同一门口。 如红色标记所示，更少体素挤入使用高分辨率设置的门口。 蓝色线条是几何体定义的门廊，而红色线条是体素大小定义的有效声学门户。 这种侵入如何在给定的情况下播放出来完全取决于体素与门户几何体的一致程度，后者由场景中对象的大小和位置决定。

![Unreal 中填充门廊的低分辨率体素的屏幕截图](media/unreal-coarse-bake.png)

![Unreal 的门廊中高分辨率体素的屏幕截图](media/unreal-fine-bake.png)

## <a name="next-steps"></a>后续步骤

使用我们的[虚幻](unreal-baking.md)或[Unity](unity-baking.md)插件自行试用粗糙和精细的分辨率设置。
