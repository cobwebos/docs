---
title: 项目噪声 Unity 集成和部署
titlesuffix: Azure Cognitive Services
description: 本操作说明解释如何将项目噪声 Unity 插件集成到 Unity 项目中。
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: kegodin
ROBOTS: NOINDEX
ms.openlocfilehash: 1a90f6102d35dc1a3bb97c840f2955b54f35bbad
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706644"
---
# <a name="project-acoustics-unity-integration"></a>项目噪声 Unity 集成
本操作说明解释如何将项目噪声 Unity 插件集成到 Unity 项目中。

所需软件：
* 用于 Windows 的 [Unity 2018.2 及更高版本](https://unity3d.com)
* [项目噪声 Unity 包](https://www.microsoft.com/download/details.aspx?id=57346)

## <a name="import-the-plugin"></a>导入插件
将声学 UnityPackage 导入项目。 
* 在 Unity 中，转到“资产”>“导入包”>“自定义包...”

    ![Unity 导入包菜单的屏幕截图](media/import-package.png)  

* 选择 ProjectAcoustics.unitypackage

如果要将插件导入现有的项目，项目根目录下可能已具有 mcs.rsp 文件，该文件指定 C# 编译器选项。 需要将该文件与 Project Acoustics 插件附带的 mcs.rsp 文件的内容合并。

## <a name="enable-the-plugin"></a>启用插件
音效工具包的制作部分需要 .NET 4.x 脚本编写运行时版本。 包导入将更新 Unity 播放器设置。 重启 Unity，使此设置生效。

![Unity 播放器设置面板的屏幕截图](media/player-settings.png)

![选中了 .NET 4.5 的 Unity 播放器设置面板的屏幕截图](media/net45.png)

## <a name="set-up-audio-dsp"></a>设置音频 DSP
项目噪声包括集成到 Unity 音频引擎 spatializer 框架中的音频运行时 DSP。 它同时包含基于 HRTF 的 spatialization 和平移。 通过使用 "**编辑 > 项目设置 >" 音频**", 然后选择"**项目噪声**"作为项目的**Spatializer 插件**来启用项目噪声 DSP。 请确保将**DSP 缓冲区大小**设置为最佳性能。

![Unity 项目设置面板的屏幕截图](media/project-settings.png)  

![选择了项目噪声 Spatializer 的 Unity Spatializer 设置面板的屏幕截图](media/choose-spatializer.png)

然后打开音频混音器 (**窗口 > 音频混音**器)。 请确保一组至少具有一个混音器。 如果没有，请单击“混音器”右侧的“+”按钮。 右键单击 "效果" 部分中的通道条底部, 然后添加**项目 "噪声混音**器" 效果。 请注意，一次仅支持一个 Project Acoustics 混音器。

![Unity 音频混音器托管项目噪声混音器的屏幕截图](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>在声音源上启用噪声
创建音频源。 单击显示“空间化”的 AudioSource 检查器面板底部的复选框。 请确保“空间混合”已设置为“全三维”。  

![Unity 音频源面板的屏幕截图](media/audio-source.png)

## <a name="enable-acoustic-design"></a>启用音响设计
将脚本**AcousticsAdjust**附加到场景中的声音源, 通过单击 "**添加组件**", 然后选择 "**脚本" > 噪声调整**, 来启用其他源设计参数:

![Unity AcousticsAdjust 脚本的屏幕截图](media/acoustics-adjust.png)

## <a name="next-steps"></a>后续步骤
* [制作你的场景, 并为 Unity 提供项目噪声](unity-baking.md)
* [创建 Azure Batch 帐户](create-azure-account.md), 以便在云中制作场景
* 浏览[项目噪声 Unity 设计过程](unity-workflow.md)。

