---
title: 项目噪声 Unity 集成和部署
titlesuffix: Azure Cognitive Services
description: 本文介绍如何将项目噪声 Unity 插件集成到 Unity 项目。
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: a8ddb0e4ca2ee4396a25a70c8b60b653aebb72d8
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242998"
---
# <a name="project-acoustics-unity-integration"></a>项目噪声 Unity 集成
本文介绍如何将项目噪声 Unity 插件集成到 Unity 项目。

所需软件：
* 用于 Windows 的 [Unity 2018.2 及更高版本](https://unity3d.com)
* [项目噪声 Unity 包](https://www.microsoft.com/download/details.aspx?id=57346)

## <a name="import-the-plug-in"></a>导入插件
1. 将噪声 UnityPackage 导入到项目中。 
 在 Unity 中，请参阅**资产** > **导入包** > **自定义包**。

    ![Unity 导入包菜单](media/import-package.png)  

1. 选择**ProjectAcoustics. unitypackage**。

1. 选择 "**导入**" 按钮，将 Unity 包集成到项目中。

    !["Unity 导入包" 对话框](media/import-dialog.png)  

如果要将插件导入到现有项目中，则项目中可能已有一个*mcs*文件。 此文件指定编译器的C#选项。 将该文件的内容与项目噪声插件附带的 mcs .rsp 文件合并。

## <a name="enable-the-plug-in"></a>启用插件
噪声工具包的制作部分需要 .NET 4。*x*脚本运行时版本。 包导入会更新 Unity 播放机设置。 重启 Unity，使此设置生效。

![Unity 播放器设置面板](media/player-settings.png)

![选择了 .NET 4.5 的 Unity 播放器设置面板](media/net45.png)

## <a name="set-up-audio-dsp"></a>设置音频 DSP
项目噪声包括集成到 Unity 音频引擎 spatializer 框架中的音频运行时 DSP。 它同时包含基于 HRTF 的 spatialization 和平移。 若要启用项目噪声 DSP，请参阅**编辑** > **项目设置** > **音频**以打开 Unity 音频设置。 选择 "**项目噪声**" 作为项目的**Spatializer 插件**。 请确保将 " **DSP 缓冲区大小**" 设置为 "*最佳性能*"。

![Unity 项目设置菜单](media/project-settings.png)  

![选择了项目噪声 Spatializer 的 Unity Spatializer 设置面板](media/choose-spatializer.png)

接下来，打开音频混音器（**Window** > **音频混音**器）。 请确保至少有一个混音器，其中包含一个组。 如果没有，请选择 " **Mixers**" 右侧的 " **+** " 按钮。 右键单击效果部分的通道条底部，添加“Microsoft Acoustics 混音器”效果。 一次只支持一个项目噪声混音器。

![托管项目噪声混音器的 Unity 音频混音器](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>在声音源上启用噪声
创建音频源：选中 AudioSource 检查器面板底部的 " **Spatialize** " 复选框。 请确保 "**空间 Blend** " 设置为 "完整*3d*"。  

![Unity 音频源面板](media/audio-source.png)

## <a name="enable-acoustic-design"></a>启用音响设计
将*AcousticsAdjust*脚本附加到场景中的声音源，以启用其他源设计参数：选择 "**添加组件**"，然后选择 "**脚本**"  >  "**噪声调整**"。

![Unity AcousticsAdjust 脚本](media/acoustics-adjust.png)

## <a name="next-steps"></a>后续步骤
* [制作你的场景，并为 Unity 提供项目噪声](unity-baking.md)。
* [创建 Azure Batch 帐户](create-azure-account.md)，在云中制作场景。
* 浏览[项目噪声 Unity 设计过程](unity-workflow.md)。
