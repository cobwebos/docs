---
title: 什么是项目音响效果？
titlesuffix: Azure Cognitive Services
description: 项目音响效果 Unity 插件为以 VR 和传统屏幕为对象的项目提供阻挡、混响和空间化效果。
services: cognitive-services
author: kegodin
manager: cgronlun
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: overview
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: 175203269f524f5c680345602e6382f5c26f1bd1
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55178896"
---
# <a name="what-is-project-acoustics"></a>什么是项目音响效果？
项目音响效果 Unity 插件为以 VR 和传统屏幕为对象的项目提供阻挡、混响和空间化效果。 可以通过它来设计游戏音响效果，将设计者意向分层堆积在基于物理的波动模拟上。

## <a name="why-use-acoustics-in-virtual-environments"></a>为何在虚拟环境中使用音响效果？
人类使用听觉-视觉线索来了解周围的世界。 在虚拟世界中，将空间的音频和音响效果组合起来可以提高用户沉浸度。 此处介绍的音响效果工具会通过分析虚拟世界进行逼真的音响效果模拟，并支持模拟后设计过程。 分析包括世界中每个表面的几何图形和材料。 模拟包括各种参数，例如到达方向（传送）、混响效果、衰减时间、以及阻挡和障碍效果。

## <a name="how-does-this-approach-to-acoustics-work"></a>这种探索音响效果的方法的原理是什么？
此系统依赖于对虚拟世界的脱机计算，与在运行时进行分析相比，这样可以完成更复杂的模拟。 脱机计算会生成一个包含音响效果参数的查找表。 设计人员可以指定在运行时应用到这些参数的规则。 对于高度情绪化的或亚现实的场景来说，调整这些规则就可以实现超逼真的效果，因而可以提供更多的背景音频声音。

## <a name="design-process-comparison"></a>设计过程比较
项目音响效果插件支持在 Unity 场景中采用适合音响效果的新设计过程。 为了说明这种新的设计过程，让我们将它与当今某个常用的音响效果方法进行比较。

### <a name="typical-approach-to-acoustics-today"></a>当今使用的典型音响效果方法
在当今使用的典型音响效果方法中，先绘制混响音量图：

![设计视图](media/reverbZonesAltSPace2.png)

然后调整每个区域的参数：

![设计视图](media/TooManyReverbParameters.png)

最后，添加光线跟踪逻辑，以便获取整个场景的正确阻挡和障碍筛选，以及针对传送的路径搜索逻辑。 此代码可能会增加运行时成本。 它还存在角落光滑度问题，在形状不规则的场景中会出现极端例子。

### <a name="an-alternative-approach-with-physics-based-design"></a>一种使用基于物理的设计的替代方法
使用项目音响效果的 Unity 插件提供的方法时，可以提供静态场景的形状和材料。 由于场景已体素化且过程不使用光线跟踪，因此不需提供简化的或严密的音响效果网格。 也不需使用混响音量标记场景。 此插件将场景上传到云，在云中使用基于物理的波动模拟。 结果以查找表的形式集成到项目中，可以根据美学或游戏效果进行修改。

![设计视图](media/GearsWithVoxels.jpg)

## <a name="requirements"></a>要求
* 适用于音响效果制作的 Unity 2018.2+，以及适用于声音设计和部署的 Unity 5.2+
* Windows 64 位 Unity 编辑器
* 用于音响效果制作的 Azure Batch 订阅
* Unity 脚本运行时必须设置为“与 .NET 4.x 等效”

## <a name="platform-support"></a>平台支持
* Windows 桌面（x86 和 AMD64）
* Windows UWP（x86、AMD64 和 ARM）
* Android（x86 和 ARM64）

## <a name="download"></a>下载
如果有兴趣评估此音效插件，请在[此处](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRwMoAEhDCLJNqtVIPwQN6rpUOFRZREJRR0NIQllDOTQ1U0JMNVc4OFNFSy4u)注册以获取设计器预览版。

## <a name="next-steps"></a>后续步骤
* 详细了解[设计过程](design-process.md)
* [在 Unity 项目中集成音响效果](getting-started.md)入门

