---
title: 项目声学统一集成和部署
titlesuffix: Azure Cognitive Services
description: 本文介绍如何将项目声学 Unity 插件集成到 Unity 项目中。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "72242998"
---
# <a name="project-acoustics-unity-integration"></a>项目声学统一集成
本文介绍如何将项目声学 Unity 插件集成到 Unity 项目中。

所需软件：
* 用于 Windows 的 [Unity 2018.2 及更高版本](https://unity3d.com)
* [项目声学统一包](https://www.microsoft.com/download/details.aspx?id=57346)

## <a name="import-the-plug-in"></a>导入插件
1. 将声学 UnityPackage 导入您的项目。 
 在 Unity 中，转到**资产** > **导入包** > **自定义包**。

    ![统一导入包菜单](media/import-package.png)  

1. 选择 **"项目声学"，unity 包**。

1. 选择“导入”**** 按钮，将 Unity 包集成到项目中。

    ![Unity 导入包对话框](media/import-dialog.png)  

如果要将插件导入现有项目，则项目可能已在项目根目录中具有*mcs.rsp*文件。 此文件指定 C# 编译器的选项。 将该文件的内容与项目声学插件附带的 mcs.rsp 文件合并。

## <a name="enable-the-plug-in"></a>启用插件
声学工具包的烘焙部分需要 .NET 4。*x*脚本运行时版本。 包导入更新您的 Unity 播放器设置。 重启 Unity，使此设置生效。

!["统一播放器设置"面板](media/player-settings.png)

![选择 .NET 4.5 的 Unity 播放器设置面板](media/net45.png)

## <a name="set-up-audio-dsp"></a>设置音频 DSP
项目声学包括音频运行时 DSP，集成到 Unity 音频引擎空间仪框架中。 它既包括基于 HRTF 和平移空间化。 要启用项目声学 DSP，请转到 **"编辑** > **项目设置** > **音频**"以打开 Unity 音频设置。 选择**项目声学**作为项目**的空间插件**。 确保**DSP 缓冲区大小**设置为*最佳性能*。

![“Unity 项目设置”菜单](media/project-settings.png)  

![选择"项目声学空间器"的 Unity 空间设置面板](media/choose-spatializer.png)

接下来，打开音频混合器 （**窗口** > **音频混合器**）。 确保至少有一个混合器，一个组。 如果没有，请选择**+****混合器**右侧的按钮。 右键单击效果部分的通道条底部，添加“Microsoft Acoustics 混音器”效果****。 一次只支持一个项目声学混合器。

![托管项目声学混音器的 Unity 音频混音器](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>在声源上启用声学
创建音频源：选择"音频源"检查面板底部的 **"空间化**"复选框。 确保**空间混合**设置为完全*3D*。  

![Unity 音频源面板](media/audio-source.png)

## <a name="enable-acoustic-design"></a>启用声学设计
将*声学调整*脚本附加到场景中的声源，以启用其他源设计参数：选择 **"添加组件"** 并选择 **"脚本** > **声学调整**"。

![Unity AcousticsAdjust 脚本](media/acoustics-adjust.png)

## <a name="next-steps"></a>后续步骤
* [烘焙你的场景与团结项目声学](unity-baking.md)。
* [创建 Azure 批处理帐户](create-azure-account.md)以在云中烘焙场景。
* 探索[项目声学统一设计过程](unity-workflow.md)。
