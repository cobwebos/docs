---
title: 项目音响效果 Unreal 设计教程
titlesuffix: Azure Cognitive Services
description: 本教程介绍了在 Unreal 和 Wwise 中项目音响效果的设计工作流。
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: kegodin
ms.openlocfilehash: c65ae71350383896c81fd7057d425822069fc5aa
ms.sourcegitcommit: f68b0e128f0478444740172f54e92b453df696be
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58136913"
---
# <a name="project-acoustics-unrealwwise-design-tutorial"></a>项目音响效果 Unreal/Wwise 设计教程
本教程介绍了在 Unreal 和 Wwise 中项目音响效果的设计设置和工作流。

软件先决条件：
* 具有项目音响效果 Wwise 和 Unreal 插件的 Unreal 项目

要获取具有项目音响效果的 Unreal 项目，可以：
* 按照[项目音响效果 Unreal 集成](unreal-integration.md)说明操作，将项目音响效果添加到 Unreal 项目中
* 或者，使用[项目音响效果示例项目](unreal-quickstart.md)。

## <a name="setup-project-wide-wwise-properties"></a>设置项目范围的 Wwise 属性
Wwise 具有声笼和声障曲线，这两条曲线可影响项目音响效果插件驱动 Wwise 音频 DSP 的方式。

### <a name="design-wwise-occlusion-curves"></a>设计 Wwise 声障曲线
项目音响效果处于活动状态时，它会响应在 Wwise 中设置的声障音量、低通滤波器 (LPF) 和高通滤波器 (HPF) 曲线。 如果声障值为 100 的值，建议将音量的曲线类型设置为线性，并且值为 -100 分贝。

使用此设置，如果项目音响效果模拟计算 -18 分贝的声障，它将在 X = 18 处输入下面的曲线，并且相应的 Y 值是应用的衰减。 要实现半声笼，请将终结点设置为 -50 分贝而不是 -100 分贝，或设置为 -200 分贝以扩大声笼。 可以剪裁和微调任何最适合游戏的曲线。
 
![项目声障曲线](media/wwise-occlusion-curve.png)

### <a name="disable-wwise-obstruction-curves"></a>禁用 Wwise 声笼曲线
Wwise 声笼曲线会影响隔离的干声电平，但项目音响效果使用设计控制和模拟来强制执行湿声/干声比。 建议禁用声笼音量曲线。 要设计湿度，可使用后面所述的“湿度调整”控件。
 
如果将声笼 LPF/HPF 曲线用于其他目的，请确保在 X=0 时将其设置为 Y=0（即没有声笼时就没有 LPF 或 HPF）。

![项目声笼曲线](media/wwise-obstruction-curve.png)

### <a name="design-project-acoustics-mixer-parameters"></a>设计项目音响效果混音器参数
可以通过访问项目音响效果总线的混音器插件选项卡来控制全局混响属性。 双击“Project Acoustics Mixer (Custom)”（项目音响效果混音器(自定义)）”，打开混音器插件的设置面板。

此外可以看到混音器插件具有“Perform Spatialization”（执行空间化）选项。 如果更喜欢使用项目音响效果的内置空间化，请选中“执行空间化”复选框，然后从 HRTF 或声像中进行选择。 请确保禁用了设置的所有干声辅助总线，否则将两次听到直接路径。 使用“Wetness Adjust”（湿度调整）和“Reverb Time Scale Factor”（混响时间比例因子）对混响混音进行全局控制。 请注意，必须重启 Unreal，然后重写音库，再点击播放才能获取诸如“执行空间化”复选框之类的混音器插件配置更改。

![项目音响效果混音器插件选项](media/mixer-plugin-global-settings.png)

## <a name="set-project-acoustics-design-controls-in-the-wwise-actor-mixer-hierarchy"></a>在 Wwise 角色混音器层次结构中设置项目音响效果设计控件
要控制单个角色混音器的参数，请双击“Actor-Mixer”（角色混音器），然后单击其“Mixer Plug-in”（混音器插件）选项卡。此处你可以更改每个声音级别的任何参数。 这些值与 Unreal 方面设置的值相结合（如下所述）。 例如，如果项目音响效果 Unreal 插件将对象上的室外调整设置为 0.5，并且 Wwise 将其设置为 -0.25，则导致应用于该声音的室外调整为 0.25。

![每混音器设置](media/per-sound-mixer-settings.png)

### <a name="ensure-the-aux-bus-has-dry-send-and-output-bus-has-wet-send"></a>请确保辅助总线已发送干声并且输出总线已发送湿声
请记住，所需的角色混音器设置在 Wwise 中交换了通常的干声和湿声路由。 它在角色混音器的输出总线（设置为项目音响效果总线）上产生混响信号，并在用户定义的辅助总线上产生干声信号。 由于项目音响效果 Wwise 插件使用的 Wwise 混音器插件 API 的功能，因此需要此路由。

![语音设计指南](media/voice-design-guidelines.png)
 
### <a name="set-up-distance-attenuation-curves"></a>设置距离衰减曲线
请确保使用项目音响效果的角色混音器使用的任何衰减曲线都将用户定义的辅助发送设置为“输出总线音量”。 对于新创建的衰减曲线，Wwise 默认执行此操作。 如果要迁移现有项目，请检查曲线设置。 

默认情况下，项目音响效果模拟在玩家位置周围的半径为 45 米。 通常建议将衰减曲线设置为该距离附近的 -200 分贝。 此距离没有严格限制。 对于某些类似武器的声音，可能需要更大的半径。 在这种情况下，需要注意的是，只有玩家位置 45 米范围内的几何体才能参与。 如果玩家位于房间内且声源位于室外 100 米远，那么将适当地屏蔽该声源。 如果源位于房间内且玩家位于室外 100 米远，那么则不会屏蔽该声源。

![衰减曲线](media/atten-curve.png)

## <a name="set-up-scene-wide-project-acoustics-properties"></a>设置场景范围内的项目音响效果属性

音响效果空间角色公布了许多修改系统行为的控件，并且这些控件在调试时非常有用。

![音响效果空间控件](media/acoustics-space-controls.png)

* **音响效果数据：** 必须从“内容/音响效果”目录中为该字段分配烘焙的音响效果资产。 项目音响效果插件会自动将内容/音响效果目录添加到项目的打包目录中。
* **磁贴大小：** 将音响效果数据加载到 RAM 中的侦听器周围区域的盘区。 只要加载玩家周围的侦听器探测，结果就与加载所有探测器的音响效果数据相同。 较大的磁贴使用更多的 RAM，但使用更少的磁盘 I/O。
* **自动 Stream：** 启用后，侦听器到达加载区域的边缘时，会自动加载新磁贴。 禁用时，需要通过代码或蓝图手动加载新磁贴
* **缓存规模：** 控制用于音响效果查询的缓存大小。 较小的缓存使用较少的 RAM，但可能会增加每个查询的 CPU 使用率。
* **音响效果已启用：** 一个调试控件，可以快速 A/B 切换音响效果模拟。 在传送配置中忽略此控件。 该控件可用于查找特定音频错误是否源自音响效果计算或 Wwise 项目中的一些其他问题。
* **更新距离：** 如果想使用预先烘焙的音响效果信息进行距离查询，请使用此选项。 这些查询类似于光线投射，但它们已经预先计算，因此占用的 CPU 少得多。 一个示例用法是从最靠近侦听器的曲面离散反射。 要充分利用这一点，将需要使用代码或蓝图来查询距离。
* **绘制统计信息：** 尽管 UE 的`stat Acoustics` 可以为你提供 CPU 信息，但此状态显示将在屏幕左上角显示当前加载的地图、RAM 使用情况和其他状态信息。
* **绘制三维像素：** 覆盖靠近侦听器的体素，该侦听器显示在运行时补色期间使用的体素网格。 如果发射器位于运行时体素内，则它将无法进行音响效果查询。
* **绘制探测：** 显示有关此场景的所有探测。 根据它们的负载状态，它们将是不同的颜色。
* **绘制距离：** 如果启用了“更新距离”，则会在侦听器周围的量化方向上显示最接近侦听器曲面的框。

## <a name="actor-specific-acoustics-design-controls"></a>特定于角色的音响效果设计控件
这些设计控件的范围限定为 Unreal 中的单个音频组件。

![音频组件控件](media/audio-component-controls.png)

* **声障乘数：** 控制声障效果。 值 > 1 将放大声障。 值 < 1 将缩小声障。
* **湿度调整：** 其他混响分贝
* **衰减时间乘数：** 根据音响效果模拟的输出，用乘法控制 RT60
* **室外调整：** 控制混响的室外情况。 值越接近 0，越靠近室内；值越接近 1，越靠近室外。 此调整是附加的，因此将其设置为 -1 将在室内强制执行，将其设置为 +1 将在室外强制执行。
* **传输分贝：** 通过此响度与基于视线的距离衰减相结合，呈现其他的穿墙声音。
* **湿声比距离弯曲：** 调整源上的混响特性，就好像它离源更近/更远一样，而不影响直接路径。
* **显示音响效果参数：** 在游戏中的组件顶部直接显示调试信息。 （仅适用于非传送配置）

## <a name="blueprint-functionality"></a>蓝图功能
音响效果空间角色可通过蓝图访问，并提供诸如通过电平脚本加载地图或修改设置等功能。 我们提供了以下两个示例。

### <a name="add-finer-grained-control-over-streaming-load"></a>添加对流式处理负载更精细的控制
要自行管理音响效果数据流式处理而不是根据玩家位置自动执行流式处理，可以使用强制加载磁贴蓝图功能：

![B P 流式处理](media/blueprint-streaming.png)

* **目标：** AcousticsSpace 角色
* **中心位置：** 需要加载数据的区域的中心
* **卸载外部磁贴的探测：** 如果选中，则不会从 RAM 中卸载不在新区域中的所有探测。 如果未选中，则会将新区域加载到内存中，同时还会将现有探测加载到内存中
* **完成时阻止：** 使磁贴加载同步操作

必须先设置磁贴大小，才能调用“强制加载”磁贴。 例如，可以执行以下操作来加载 ACE 文件、设置磁贴大小以及在区域中流式传输：

![流式处理设置](media/streaming-setup.png)

### <a name="optionally-query-for-surface-proximity"></a>（可选）查询曲面接近度
如果要查看围绕侦听器的特定方向上的曲面的接近程度，可以使用“查询距离”功能。 此功能可用于驱动定向延迟反射，或用于由曲面接近度驱动的其他游戏逻辑。 查询比光线投射更经济实惠，因为结果是从音响效果查找表中提取的。

![距离查询](media/distance-query.png)

* **目标：** AcousticsSpace 角色
* **查找方向：** 查询的方向，以侦听器为中心
* **距离：** 如果查询成功，则为离最近曲面的距离
* **返回值：** 如果查询成功，布尔值为 true，否则为 false

## <a name="next-steps"></a>后续步骤
* 探索[设计过程](design-process.md)背后的概念
* [创建 Azure 帐户](create-azure-account.md)来烘焙自己的场景


