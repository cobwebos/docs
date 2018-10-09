---
title: Project Acoustics 示例演练 - 认知服务
description: 本演练介绍 Project Acoustics 的 Unity 示例场景，包括到桌面和 VR 的部署。
services: cognitive-services
author: kegodin
manager: noelc
ms.service: cognitive-services
ms.component: acoustics
ms.topic: article
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: eaf7ff9f7f791fd6d04e6b76d256b4987c50cd13
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2018
ms.locfileid: "47434086"
---
# <a name="unity-sample-walkthrough"></a>Unity 示例演练
这是有关 Project Acoustics 示例的演练。 有关什么是 Project Acoustics 的详细信息，请参阅 [Project Acoustics 简介](what-is-acoustics.md)。 有关将 Project Acoustics 包添加到现有 Unity 项目的帮助，请使用[入门指南](getting-started.md)。

## <a name="requirements-for-running-the-sample-project"></a>运行示例项目的要求
* Unity 2018.2+，使用 .NET 4.x 脚本编写运行时版本
* Windows 64 位 Unity 编辑器
* 该示例支持 Windows 桌面、UWP 和 Android 目标，包括头戴式显示器 (HMD)
* 制作过程所需的 Azure Batch 订阅

## <a name="sample-project-setup"></a>示例项目设置
下载并导入 MicrosoftAcoustics.Sample.unitypackage。 导入时，项目设置包括“空间音响”，并且更新“脚本编写运行时版本”以满足插件要求。 此操作完成后，将通过 AcousticsGeometry.cs 在 Unity 控制台中看到一个错误，错误关于将脚本编写运行时版本更改为 .NET 4.x 等效版本。 此设置更改作为包导入的一部分执行，但需要重启 Unity 才能生效。 立即重启 Unity。

## <a name="running-the-sample"></a>运行示例
此示例包括演示场景 Assets/AcousticsDemo/ProjectAcousticsDemo.unity。 此场景有三个声源。 默认情况下，只有一个声源在播放，另外两个声源暂停。 这些声源位于“层次结构”中的“声源”下。 为了帮助编写泛型导航脚本，Main Camera 为 CameraHolder 对象的子级。 

![层次结构视图](media/SampleHierarchyView.png)

已制作场景，并且该场景包含与“层次结构”中的 MicrosoftAcoustics 预制件相关联的 ACE 文件。 

通过单击 Unity 编辑器中的播放按钮，侦听场景的声音效果。 在桌面上，使用 W、A、S、D 和鼠标进行移动。 若要比较有音响效果和没有音响效果的场景声音，请按下 **R** 按钮，直到覆盖文本变成红色，并显示“音响效果: 已禁用”。 若要查看更多控件的键盘快捷方式，请按 **F1**。 通过右键单击以选择要执行的操作，然后左键单击以执行操作，也可以使用所有控件。

## <a name="targeting-other-platforms"></a>面向其他平台
该示例包含在 Windows 桌面、UWP、Windows Mixed Reality、Android 和 Oculus Go 上运行的设置。 默认情况下，针对 Windows 桌面配置该项目。 若要面向 VR 平台，请转到播放机设置（“编辑”>“项目设置”>“播放机”），查找“XR 设置”，然后选中“受支持的虚拟现实”复选框。

![启用 VR](media/VRSupport.png)  

将 VR 头戴显示设备连接电脑。 转到“文件”>“生成设置”，然后单击“生成并运行”，将示例部署到 VR 头戴显示设备。 使用头戴显示设备的动作控制器在场景中导航，或尝试使用键盘上的 W、A、S、D。    
若要面向 Android 和 Oculus Go，请从“生成设置”菜单中选择 Android。 单击“切换目标”，然后选择“生成并运行”。 这会将示例场景部署到已连接的 Android 设备。 有关适用于 Android 的 Unity 开发，请参阅 [Unity 文档](https://docs.unity3d.com/Manual/android-GettingStarted.html)。

![面向 Android](media/TargetAndroid.png)  

## <a name="next-steps"></a>后续步骤
* 为自己的制作[创建 Azure 帐户](create-azure-account.md)
* 了解[设计过程](design-process.md)

