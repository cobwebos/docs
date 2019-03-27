---
title: 项目音响效果 Unity 设计教程
titlesuffix: Azure Cognitive Services
description: 本教程介绍了在 Unity 中项目音响效果的设计工作流。
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: kegodin
ms.openlocfilehash: 7f079c511a32cfcf0fa018d40abb737ad08f3821
ms.sourcegitcommit: f68b0e128f0478444740172f54e92b453df696be
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58138006"
---
# <a name="project-acoustics-unity-design-tutorial"></a>项目音响效果 Unity 设计教程
本教程介绍了在 Unity 中项目音响效果的设计工具和工作流。

先决条件：
* 用于 Windows 的 Unity 2018.2 及更高版本
* 具有烘焙的音响效果资产的 Unity 场景

在本教程中，你可以通过两种方式获得具有烘焙的音响效果资产的 Unity 场景：
* [将项目音响效果添加到 Unity 项目](unity-integration.md)，然后[获取 Azure Batch 帐户](create-azure-account.md)，然后[烘焙 Unity 场景](unity-baking.md)
* 或者，使用[项目音响效果 Unity 示例内容](unity-quickstart.md)。

## <a name="review-design-process-concepts"></a>查看设计过程概念
项目音响效果使用常见的音频数字信号处理 (DSP) 方法处理声源，并让你控制熟悉的音响效果属性，包括闭塞、湿/干混合和混响尾长 (RT60)。 但核心[项目音响效果设计过程概念](design-process.md)不是直接设置这些属性，而是控制模拟结果如何用于驱动这些属性。 每个控件的默认设置代表物理上准确的音响效果。

## <a name="design-acoustics-for-each-source"></a>为每个源设计音响效果
项目音响效果提供了许多特定于源的音响效果设计控件。 这使你可以通过强调某些源和不强调其他来源来控制场景中的混音。

### <a name="adjust-distance-based-attenuation"></a>调整基于距离的衰减
项目音响效果 Unity 空间音响插件提供的音频 DSP 遵守内置于 Unity 编辑器的基于距离的按源衰减。 针对基于距离的衰减的控件位于“三维声音设置”下的声源“检查器”面板的“音频源”组件中：

![距离衰减](media/distance-attenuation.png)

音效在以播放器位置为中心的“模拟区域”框中执行计算。 如果声源离播放器较远，位于模拟区域之外，那么只有框内的几何图形会影响声音传播（如造成遮挡），当遮挡物在播放器附近时，此效果会相当好。 然而，如果播放器在开放空间内，但遮挡物靠近远处的声源，则声音会变得不真实。 在这种情况下，我们建议的解决方法是，确保声音衰减在 45 米左右（即播放机到框边缘的默认水平距离）下降到 0。

![SpeakerMode](media/speaker-mode.png)

### <a name="adjust-occlusion-and-transmission"></a>调整闭塞和传输
将 AcousticsAdjust 脚本附加到源可支持优化该源的参数。 要附加脚本，请单击“检查器”面板底部的“添加组件”，然后导航到“脚本”>“音效调节”。 该脚本有六个控件：

![AcousticsAdjust](media/acoustics-adjust.png)

* 启用音效 - 控制是否对此源应用音效。 取消选中后，源将通过 HRTF 或平移进行空间化，但不会有任何音效。 这意味着无障碍、无闭塞或动态混响参数，如级别和衰减时间。 系统仍然以固定的级别和衰减时间应用混响。
* 遮挡 - 将乘数应用到由音效系统计算出的遮挡 dB 级别。 如果此乘数大于 1，遮挡效果将被扩大，而小于 1 的值会使遮挡效果变得较细微，值 0 则会使遮挡失效。
* 传输 (dB) - 设置通过几何传输引起的衰减 (dB)。 将此滑块设置到其最低级别，以禁用传输。 音效围绕场景几何图形 (portaling) 在初始干涩音频到达时对其进行空间化。 传输提供一个额外的干涩到达，它在视线方向空间化。 请注意，针对源的距离衰减曲线同样适用。

### <a name="adjust-reverberation"></a>调整混响
* 湿度 (dB) - 在 dB 中，根据源的距离调整混响功率。 正值可增强声音混响效果，而负值则使声音更干涩。 单击曲线控件（绿线）以打开曲线编辑器。 通过左键单击来添加点并拖动这些点以形成所需的函数来修改曲线。 X 轴表示源距离，y 轴表示 dB 中的混响调整。 有关编辑曲线的详细信息，请参阅此 [Unity 手册](https://docs.unity3d.com/Manual/EditingCurves.html)。 若要将曲线重置回默认值，右键单击“湿度”并选择“重置”。
* 衰减时间刻度 - 调整衰减时间的倍数。 例如，如果制作结果指定衰减时间为 750 毫秒，但此值设置为 1.5，则应用于源的衰减时间将为 1,125 毫秒。
* **室外** - 音效系统对源上混响在室外音效估计的附加调整。 将此值设置为 1，将使源声音完全始终在室外发出，而设置为 -1，将使源声音完全在室内发出。

将 AcousticsAdjustExperimental 脚本附加到源，可支持优化该源的其他实验性参数。 要附加脚本，请单击“检查器”面板底部的“添加组件”，然后导航到“脚本”>“音效调节试验”。 目前有一个实验性控件：

![AcousticsAdjustExperimental](media/acoustics-adjust-experimental.png)

* 可感知距离扭曲 - 将指数型扭曲应用到用于计算干湿比率的距离。 音效系统将计算整个空间的湿度级别，后者会随距离平稳变化，并提供可感知的距离提示。当扭曲值大于 1，会增加与距离相关的混响级别，从而扩大这种效果，使声音变得“遥远”。 当扭曲值小于 1，使基于距离的混响变化更加细微，进而使声音更加“贴近”。

## <a name="design-acoustics-for-all-sources"></a>为所有源设计音响效果
要调整所有源的参数，请单击 Uinty“音频混音器”中的声道条带，然后调整“项目音效混音器”效果的参数。

![混音器自定义](media/mixer-parameters.png)

* **湿度调整** - 根据源侦听器的距离调整场景中所有源的混响功率 (dB)。 正值可增强声音混响效果，而负值则使声音更干涩。
* **RT60 刻度** - 混响时间的乘法标量。
* **使用平移** - 控制音频输出是双耳平移 (0) 还是多声道平移 (1)。 除了 1 以外的任何值都表示双耳。 使用 HRTF 对双耳输出进行空间化，以用于耳机；使用 VBAP 对多声道输出进行空间化，以用于多声道环绕扬声器系统。 如果使用多声道平移器，请务必选择与设备设置相匹配的扬声器模式，可在“项目设置” > “音频”下找到。

## <a name="check-proper-sound-source-placement"></a>检查适当的声源位置
放置在占用体素内的声源不会得到音响效果处理。 因为体素延伸超过可见场景几何体，所以可以将源放置在体素内部，并且它看起来未被视觉几何体遮挡。 可以通过在“场景”视图右上角的“Gizmos”菜单中切换体素网格复选框来查看项目音响效果体素。

![Gizmos 菜单](media/gizmos-menu.png)  

体素显示也可以帮助确定游戏中的视觉对象组件是否已应用转换。 如果已应用，请将该转换也应用到承载音效管理器的 GameObject。

### <a name="bake-time-vs-run-time-voxels"></a>烘焙时间与运行时间体素
可以在游戏设计时在编辑器窗口中查看体素，也可以在运行时在游戏窗口中查看体素。 这些视图中体素的大小不同。 这是因为音响效果运行时间插值使用更精细的体素网格以获得更平滑的插值结果。 应使用运行时体素验证声源放置。

设计时体素：

![VoxelsDesignTime](media/voxels-design-time.png)

运行时体素：

![VoxelsRuntime](media/voxels-runtime.png)

## <a name="next-steps"></a>后续步骤
* 了解重点介绍[设计过程](design-process.md)概念的案例研究

