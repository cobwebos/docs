---
title: 项目噪声插件已知问题
titlesuffix: Azure Cognitive Services
description: 在项目噪声中，你可能会遇到以下已知问题。
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: b71b93f271608d946d964f70dae9eefbef77e87b
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243045"
---
# <a name="project-acoustics-known-issues"></a>项目噪声已知问题
本文介绍了使用项目噪声时可能会遇到的问题。

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>重命名场景时丢失声学参数

如果重命名场景，则属于该场景的所有声音参数都不会自动传输到新场景。 但它们仍然存在于旧的资产文件中。 在场景文件旁边的*编辑器*目录中查找 *[SceneName] _AcousticParameters*文件。 重命名该文件以反映新的场景名称。

## <a name="deploy-to-android-bug-from-some-unity-versions"></a>从某些 Unity 版本部署到 Android bug

某些版本的 Unity 在如何将音频插件部署到 Android 方面存在[bug](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player) 。 请确保未使用受此 bug 影响的版本。

## <a name="could-not-find-metadata-file-systemsecuritydll-error"></a>"找不到元数据文件 system.string" 错误

确保**播放机**设置中的**脚本运行时版本**是*等效于 .net*4.x 的，并重启 Unity。

## <a name="authentication-problems-when-connecting-to-azure"></a>连接到 Azure 时的身份验证问题

检查：
- 你为你的 Azure 帐户使用了正确的凭据。
- 你的帐户支持你在制作中请求的节点类型。
- 系统时钟设置正确。

## <a name="the-bake-tab-still-shows-deleting-after-you-cancel"></a>取消后，"制作" 选项卡仍显示 "正在删除"
在成功完成或取消后，项目噪声将清除作业的所有 Azure 资源。 此过程最多可能需要5分钟。

## <a name="next-steps"></a>后续步骤
* 尝试[Unity](unity-quickstart.md)或[Unreal](unreal-quickstart.md)示例内容。
