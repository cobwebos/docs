---
title: 项目噪声制作解决方案
titlesuffix: Azure Cognitive Services
description: 此概念概述介绍了收录噪声时的粗糙分辨率和精细分辨率之间的差异。
services: cognitive-services
author: KyleStorck
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: kylsto
ROBOTS: NOINDEX
ms.openlocfilehash: e237a010bfecc038816c1eedf94e2f94b1e12472
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704874"
---
# <a name="project-acoustics-bake-resolution"></a>项目噪声制作解决方案
此概念概述介绍了收录噪声时的粗糙分辨率和精细分辨率之间的差异。 在收录工作流的探测步骤中选择此设置。

## <a name="Coarse-vs-Fine-Resolution"></a>粗糙与精细分辨率

高分辨率和低分辨率设置之间的唯一区别在于模拟的执行频率。 高分辨率的执行频率是低分辨率的两倍。 这对声音模拟有很多影响:

* 低分辨率的波长是高分辨率的两倍，因此体素是后者的两倍大。
* 模拟时间与体素大小直接相关，因此低分辨率烘焙的速度约为高分辨率烘焙的 16 倍。
* 不能模拟小于 voxel 大小的门户 (例如, 门或 windows)。 粗设置可能会导致不模拟某些较小的门户;因此, 它们不会在运行时传递声音。 通过查看体素可确定是否发生该情况。
* 更低的模拟频率将减少角和边周围的衍射。
* 无法在 "填充的" voxels (即包含几何的 voxels) 中找到声音源。 这不会产生任何声音。 更难放置声音源, 使其不在 voxels 的更大范围内, 而不是在使用精细设置时。
* 如下所示，更大的体素将更多地挤入门户。 第一张图像是使用低分辨率创建的，而第二张是使用高分辨率的同一门口。 如红色标记所示，更少体素挤入使用高分辨率设置的门口。 蓝色线条是几何体定义的门廊，而红色线条是体素大小定义的有效声学门户。 这种侵入如何在给定的情况下播放出来完全取决于体素与门户几何体的一致程度，后者由场景中对象的大小和位置决定。

![Unreal 中填充门廊的低分辨率体素的屏幕截图](media/unreal-coarse-bake.png)

![Unreal 的门廊中高分辨率体素的屏幕截图](media/unreal-fine-bake.png)

## <a name="next-steps"></a>后续步骤

使用我们的[Unreal](unreal-baking.md)或[Unity](unity-baking.md)插件尝试使用粗和精细分辨率设置。
