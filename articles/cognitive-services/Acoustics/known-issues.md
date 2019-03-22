---
title: Project Acoustics 插件的已知问题
titlesuffix: Azure Cognitive Services
description: 针对 Project Acoustics 使用设计器预览时，可能会遇到以下已知问题。
services: cognitive-services
author: kylestorck
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: resources
ms.date: 03/20/2019
ms.author: kylestorck
ms.openlocfilehash: 50de4d983ed24440d655cf5b9ba3fb5e33d8d7cd
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/21/2019
ms.locfileid: "58309939"
---
# <a name="project-acoustics-known-issues"></a>项目噪声的已知问题
针对 Project Acoustics 使用设计器预览时，可能会遇到以下已知问题。

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>重命名场景时丢失声学参数

如果重命名一个场景，所有属于该场景的声学参数不会自动传输到新的场景。 它们仍将但是存在旧的资产文件中。 在场景文件旁边的“编辑器”目录内查找 SceneName_AcousticParameters.asset 文件。 重命名文件以反映新的场景名称。

## <a name="unity-crashes-when-closing-project"></a>关闭项目时，Unity 崩溃

在最新版本的 Unity (2018.2+) 上，存在一个已知 bug，在关闭项目时 Unity 会崩溃。 这由[此 Unity 问题](https://issuetracker.unity3d.com/issues/crash-on-assetdatabase-getassetimporterversions-when-closing-a-specific-unity-project)跟踪。

## <a name="deploying-to-android-from-some-unity-versions"></a>从某些 Unity 版本部署到 Android

某些版本的 Unity 具有音频插件部署到 Android 的 bug。 请确保你不使用受版本[此 bug](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player)。

## <a name="i-get-an-error-that-could-not-find-metadata-file-systemsecuritydll"></a>收到“找不到元数据文件 System.Security.dll”错误

请确保播放机设置中的“脚本编写运行时版本”已设置为“.NET 4.x 等效版本”，并重启 Unity。

## <a name="im-having-authentication-problems-when-connecting-to-azure"></a>连接到 Azure 时遇到身份验证问题

请仔细检查所用 Azure 帐户凭据是否正确、帐户是否支持制作中请求的节点类型，以及系统时钟是否准确。

## <a name="canceling-a-bake-leaves-the-bake-tab-in-deleting-state"></a>取消制作会使“制作”选项卡处于“正在删除”状态
项目噪声将清除上成功完成或取消的作业的所有 Azure 资源。 这可能需要最多 5 分钟。

## <a name="next-steps"></a>后续步骤
* 请尝试[Unity](unity-quickstart.md)或[Unreal](unreal-quickstart.md)示例内容

