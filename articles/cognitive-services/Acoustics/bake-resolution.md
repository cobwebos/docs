---
title: 项目噪声制作解决方法
titlesuffix: Azure Cognitive Services
description: 此概念概述介绍粗和没问题的解决办法，同时烤噪声之间的差异。
services: cognitive-services
author: KyleStorck
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: how-to
ms.date: 04/05/2019
ms.author: KyleStorck
ms.openlocfilehash: 7dbf63ba39c5dcdebb363cfc37a45f0216a07497
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/11/2019
ms.locfileid: "59495269"
---
# <a name="project-acoustics-bake-resolution"></a>项目噪声制作解决方法
此概念概述介绍粗和没问题的解决办法，同时烤噪声之间的差异。 选择此设置面点工作流在探测步骤。

## <a name="Coarse-vs-Fine-Resolution"></a>粗糙与精细分辨率

高分辨率和低分辨率设置之间的唯一区别在于模拟的执行频率。 高分辨率的执行频率是低分辨率的两倍。 这对声学模拟有许多影响：

* 低分辨率的波长是高分辨率的两倍，因此体素是后者的两倍大。
* 模拟时间与体素大小直接相关，因此低分辨率烘焙的速度约为高分辨率烘焙的 16 倍。
* 不能模拟门户 （例如，门或 windows） 小于 voxel 大小。 粗略的设置可能会导致一些这些较小的门户，从而不会模拟;因此，它们不会传递声音通过在运行时。 通过查看体素可确定是否发生该情况。
* 更低的模拟频率将减少角和边周围的衍射。
* 在"填充"voxels (即包含 geometry 的 voxels) 内，不能位于声音的源。 这会不导致任何声音。 会更加困难，因此它们不是内部的粗略比使用精细设置时，它是更大的 voxels 放置声音的源。
* 如下所示，更大的体素将更多地挤入门户。 第一张图像是使用低分辨率创建的，而第二张是使用高分辨率的同一门口。 如红色标记所示，更少体素挤入使用高分辨率设置的门口。 蓝色线条是几何体定义的门廊，而红色线条是体素大小定义的有效声学门户。 这种侵入如何在给定的情况下播放出来完全取决于体素与门户几何体的一致程度，后者由场景中对象的大小和位置决定。

![Unreal 中填充门廊的低分辨率体素的屏幕截图](media/unreal-coarse-bake.png)

![Unreal 的门廊中高分辨率体素的屏幕截图](media/unreal-fine-bake.png)

## <a name="next-steps"></a>后续步骤

使用自己尝试粗和正常解析设置我们[Unreal](unreal-baking.md)或[Unity](unity-baking.md)插件。