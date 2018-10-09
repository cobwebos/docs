---
title: 音响效果插件的已知问题 - 认知服务
description: 针对 Project Acoustics 使用设计器预览时，可能会遇到以下已知问题。
services: cognitive-services
author: kylestorck
manager: noelc
ms.service: cognitive-services
ms.component: acoustics
ms.topic: article
ms.date: 08/17/2018
ms.author: kylestorck
ms.openlocfilehash: c19b19cab33ae868f11ded0b7ce87dac99269596
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2018
ms.locfileid: "47431964"
---
# <a name="known-issues"></a>已知问题
针对 Project Acoustics 使用设计器预览时，可能会遇到以下已知问题。

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>重命名场景时丢失声学参数

如果重命名某场景，属于该场景的所有声学参数均不会自动传输到新场景。 但是，它们将仍存在于旧资产文件中。 在场景文件旁边的“编辑器”目录内查找 SceneName_AcousticParameters.asset 文件。 重命名文件以反映新的场景名称。

## <a name="the-default-path-for-the-acousticsdata-folder-in-probes-tab-is-an-absolute-path"></a>“探测”选项卡中 AcousticsData 文件夹的默认路径为绝对路径

该路径应默认为相对路径，以便更轻松地在协作者之间共享项目。 解决方法是将路径更改为项目目录的相对路径。

## <a name="runtime-voxels-are-a-different-size-than-scene-preview-voxels"></a>运行时体素的大小与场景预览体素的大小不同

如果在“探测”选项卡上执行计算并查看 voxel，然后在运行时对相同场景进行制作并查看 voxel，则两个 voxel 大小不同。 制作前显示的 voxel 是用于模拟中的 voxel。 在运行时显示的 voxel 用于探测点之间的内插。 这可能会导致不一致，其中门户在运行时显示已打开，而实际上未打开。

## <a name="uwp-builds-not-working"></a>UWP 生成运行不正常

在最新版本的 Unity (2018.2+) 上，UWP 生成失败。 生成的运行阶段将停止，你将收到“Unity 扩展尚未初始化”错误。 这由[此 Unity 问题](https://fogbugz.unity3d.com/default.asp?1070491_1rgf14bakv5u779d)跟踪。

## <a name="unity-crashes-when-closing-project"></a>关闭项目时，Unity 崩溃

在最新版本的 Unity (2018.2+) 上，存在一个已知 bug，在关闭项目时 Unity 会崩溃。 这由[此 Unity 问题](https://issuetracker.unity3d.com/issues/crash-on-assetdatabase-getassetimporterversions-when-closing-a-specific-unity-project)跟踪。

## <a name="trouble-deploying-to-android"></a>部署到 Android 时遇到问题
若要在 Android 上使用 Project Acoustics，请将生成目标更改为 Android。 某些版本的 Unity 在部署音频插件方面存在 bug - 请确保你使用的是不受[此 bug](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player) 影响的版本。

## <a name="i-get-an-error-that-could-not-find-metadata-file-systemsecuritydll"></a>收到“找不到元数据文件 System.Security.dll”错误

请确保播放机设置中的“脚本编写运行时版本”已设置为“.NET 4.x 等效版本”，并重启 Unity。

## <a name="im-having-authentication-problems-when-connecting-to-azure"></a>连接到 Azure 时遇到身份验证问题

请仔细检查所用 Azure 帐户凭据是否正确、帐户是否支持制作中请求的节点类型，以及系统时钟是否准确。

## <a name="next-steps"></a>后续步骤
* [在 Unity 项目中集成音响效果](getting-started.md)入门

