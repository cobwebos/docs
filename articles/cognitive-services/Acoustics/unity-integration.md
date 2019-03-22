---
title: 项目噪声 Unity 集成和部署
titlesuffix: Azure Cognitive Services
description: 本操作指南介绍了与你的 Unity 项目的项目噪声 Unity 插件集成。
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: how-to
ms.date: 03/14/2019
ms.author: kegodin
ms.openlocfilehash: 7d8edd7b092ee1ed4f953d753b2bbe864e075378
ms.sourcegitcommit: f68b0e128f0478444740172f54e92b453df696be
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58137724"
---
# <a name="project-acoustics-unity-integration"></a>项目噪声 Unity 集成
本操作指南介绍了与你的 Unity 项目的项目噪声 Unity 插件集成。

软件要求：
* [Unity 2018.2 +](http://unity3d.com)的 Windows
* [项目噪声 Unity 包](https://www.microsoft.com/en-us/download/details.aspx?id=57346)

## <a name="import-the-plugin"></a>导入插件
将声学 UnityPackage 导入项目。 
* 在 Unity 中，转到“资产”>“导入包”>“自定义包...”

    ![导入包](media/import-package.png)  

* 选择**ProjectAcoustics.unitypackage**

如果要将插件导入现有的项目，项目根目录下可能已具有 mcs.rsp 文件，该文件指定 C# 编译器选项。 需要将该文件与 Project Acoustics 插件附带的 mcs.rsp 文件的内容合并。

## <a name="enable-the-plugin"></a>启用插件
音效工具包的制作部分需要 .NET 4.x 脚本编写运行时版本。 包导入将更新 Unity 播放器设置。 重启 Unity，使此设置生效。

![播放器设置](media/player-settings.png)

![.NET 4.5](media/net45.png)

## <a name="set-up-audio-dsp"></a>设置音频 DSP
项目噪声包括音频运行时将集成到 Unity 音频引擎 spatializer 框架的 DSP。 它包括基于 HRTF 和平移 spatialization。 通过打开使用 Unity 音频设置启用项目噪声 DSP**编辑 > 项目设置 > 音频**，然后选择**项目噪声**作为**Spatializer 插件**为你的项目。 请确保**DSP 缓冲区大小**设置为获得最佳性能。

![项目设置](media/project-settings.png)  

![Project Acoustics 空间音响](media/choose-spatializer.png)

然后打开音频 Mixer (**窗口 > 音频 Mixer**)。 请确保一组至少具有一个混音器。 如果没有，请单击“混音器”右侧的“+”按钮。 右键单击底部的效果部分中，在通道条带，添加**项目噪声 Mixer**效果。 请注意，一次仅支持一个 Project Acoustics 混音器。

![音频混音器](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>启用声音的源上的噪声
创建音频源。 单击显示“空间化”的 AudioSource 检查器面板底部的复选框。 请确保“空间混合”已设置为“全三维”。  

![音频源](media/audio-source.png)

## <a name="enable-acoustic-design"></a>启用声学设计
附加脚本**AcousticsAdjust**到场景中，从而通过单击启用其他源设计参数中的声音源**添加组件**，然后选择**脚本 > 噪声调整**:

![AcousticsAdjust](media/acoustics-adjust.png)

## <a name="next-steps"></a>后续步骤
* [制作您的场景使用 Unity 的项目噪声](unity-baking.md)
* [创建 Azure Batch 帐户](create-azure-account.md)来制作您在云中的场景
* 探索[项目噪声 Unity 设计过程](unity-workflow.md)。

