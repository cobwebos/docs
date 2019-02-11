---
title: Project Acoustics 入门
titlesuffix: Azure Cognitive Services
description: 本快速入门指南将演示如何在 Unity 项目中集成插件、制作场景，以及将音效应用于声源。
services: cognitive-services
author: kegodin
manager: cgronlun
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: 0aad231b4a57c2d9927a553a43394681282b5494
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55155946"
---
# <a name="getting-started-with-project-acoustics"></a>Project Acoustics 入门
本快速入门指南将演示如何在 Unity 项目中集成插件、制作场景，以及将音效应用于声源。 对于本快速入门教程，首先需要创建 [Azure Batch 帐户](create-azure-account.md)。 本指南假设用户比较熟悉 Unity。

## <a name="download-the-plugin"></a>下载插件
在[此处](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRwMoAEhDCLJNqtVIPwQN6rpUOFRZREJRR0NIQllDOTQ1U0JMNVc4OFNFSy4u)注册，以获取设计器预览版。

## <a name="supported-platforms-for-quickstart"></a>快速入门的支持平台
* [Unity 2018.2+](http://www.unity3d.com)
  * 需要将项目设置为 .NET 4.x 等效脚本编写运行时版本 
  * 需要基于 Windows 的 Unity 编辑器

## <a name="import-the-plugin"></a>导入插件
将声学 UnityPackage 导入项目。 
* 在 Unity 中，转到“资产”>“导入包”>“自定义包...”

![导入包](media/ImportPackage.png)  

* 选择“MicrosoftAcoustics.unitypackage”

如果要将插件导入现有的项目，项目根目录下可能已具有 mcs.rsp 文件，该文件指定 C# 编译器选项。 需要将该文件与 Project Acoustics 插件附带的 mcs.rsp 文件的内容合并。

## <a name="enable-the-plugin"></a>启用插件
音效工具包的制作部分需要 .NET 4.x 脚本编写运行时版本。 包导入将更新 Unity 播放器设置。 重启 Unity，使此设置生效。

![播放器设置](media/PlayerSettings.png)

![.NET 4.5](media/Net45.png)

## <a name="create-a-navigation-mesh"></a>创建导航网格
使用标准的 [Unity 工作流](https://docs.unity3d.com/Manual/nav-BuildingNavMesh.html)为项目创建导航网格。 有关如何使用自己的导航网格的信息，请参阅[制作 UI 演练](bake-ui-walkthrough.md)。

## <a name="mark-static-meshes-for-acoustics"></a>为音效标记静态网格
在 Unity 中使用“窗口”>“音效”打开音效窗口。 此窗口可停靠在检查器旁边。

![打开“音效”窗口](media/WindowAcoustics.png)

在 Unity 的层次结构窗口中，取消选择所有选定项目。 在音效“对象”选项卡中，单击“声学几何体”复选框，将场景中的所有网格和地形标记为声学几何体。

在“材料”选项卡上，向场景中所用的材料分配声学材料。 “默认”材料具有等效于混凝土的吸收能力。 有关如何指定自己的材料属性的详细信息，请参阅[设计过程页](design-process.md)。

![“材料”选项卡](media/MaterialsTab.png)

## <a name="preview-the-probes"></a>预览探测
在“探测”选项卡上，单击“计算”。 此计算可能需要几分钟，具体取决于场景大小。 计算完成后，场景视图中会显示悬浮的球体，该球体标记音效模拟的位置，名为“探测点”。 如果在场景窗口中足够接近某个对象，还可以看到场景体素化。 绿色体素应与标记为几何体的对象对齐。 探测点和体素显示可在场景视图右上角的 Gizmos 菜单中切换。

![GizmosPreview](media/BakePreviewWithGizmos.png)

## <a name="bake-the-scene"></a>制作场景
在“制作”选项卡上，输入 Azure 凭据并单击“制作”。 如果还没有 Azure Batch 帐户，请参阅[本演练，了解建议的帐户设置](create-azure-account.md)。
制作完成后，数据文件将自动下载到项目中的 Assets/AcousticsData 目录。

## <a name="set-up-audio-runtime-dsp"></a>设置音频运行时 DSP
我们将在 Unity 的空间音响框架中针对音效嵌入音频运行时 DSP，并将它与基于 HRTF 的空间化集成。 要启用音效处理，请转到“编辑”>“项目设置”>“音频”切换到“Microsoft Acoustics”空间音响，然后选择“Microsoft Acoustics”作为项目的“空间音响插件”。 此外，请确保 DSP 缓冲区大小已设置为“最佳性能”。

![项目设置](media/ProjectSettings.png)  

![Project Acoustics 空间音响](media/ChooseSpatializer.png)

打开音频混音器（“窗口”>“音频混音器”）。 请确保一组至少具有一个混音器。 如果没有，请单击“混音器”右侧的“+”按钮。 右键单击效果部分的通道条底部，添加“Microsoft Acoustics 混音器”效果。 请注意，一次仅支持一个 Project Acoustics 混音器。

![音频混音器](media/AudioMixer.png)

## <a name="set-up-the-acoustics-lookup-table"></a>设置音效查找表
将 Microsoft Acoustics 预设从项目面板拖放到场景中：

![音效预设](media/AcousticsPrefab.png)

单击 ProjectAcoustics 游戏对象，然后转到其检查器面板。 通过将制做结果拖放到音效管理器脚本或单击文本框旁边的圆形按钮，指定制作结果的位置（Assets/AcousticsData 中的 .ACE 文件）。

![音效管理器](media/AcousticsManager.png)  

## <a name="apply-acoustics-to-sound-sources"></a>将音效应用于声源
创建音频源。 单击显示“空间化”的 AudioSource 检查器面板底部的复选框。 请确保“空间混合”已设置为“全三维”。  

![音频源](media/AudioSource.png)

## <a name="apply-post-bake-design"></a>应用后期制作设计
可以将脚本 AcousticsAdjust 附加到场景中的声源，以启用其他源设计参数，方法是单击“添加组件”，然后选择“脚本”>“音效调整”：

![AcousticsAdjust](media/AcousticsAdjust.png)

还存在 Microsoft Acoustics 混音器的参数。 有关后期制作设计的详细信息，请参阅[设计参数](design-process.md)。

## <a name="next-steps"></a>后续步骤
* 试用[示例场景](sample-walkthrough.md)
* 了解整套[制作功能](bake-ui-walkthrough.md)
* 了解更多详细的[设计参数](design-process.md)

