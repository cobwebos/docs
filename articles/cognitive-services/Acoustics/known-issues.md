---
title: 项目声学插件已知问题
titlesuffix: Azure Cognitive Services
description: 在"项目声学"中，您可能会遇到以下已知问题。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "72243045"
---
# <a name="project-acoustics-known-issues"></a>项目声学已知问题
本文介绍了在使用"项目声学"时可能会遇到的问题。

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>重命名场景时丢失声学参数

如果重命名场景，则属于该场景的所有声学参数不会自动传输到新场景。 但它们仍然存在于旧资产文件中。 在场景文件旁边的*编辑器*目录中查找 *[SceneName] _AcousticParameters.资产*文件。 重命名文件以反映新的场景名称。

## <a name="deploy-to-android-bug-from-some-unity-versions"></a>从某些 Unity 版本部署到安卓错误

Unity 的某些版本在如何将音频插件部署到 Android 方面存在[漏洞](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player)。 请确保您没有使用受此 Bug 影响的版本。

## <a name="could-not-find-metadata-file-systemsecuritydll-error"></a>"找不到元数据文件系统.安全.dll"错误

请确保**播放器**设置中的**脚本运行时版本**为 *.NET 4.x 等效*版本，然后重新启动 Unity。

## <a name="authentication-problems-when-connecting-to-azure"></a>连接到 Azure 时的身份验证问题

检查：
- 对于 Azure 帐户，您使用了正确的凭据。
- 您的帐户支持您在烘焙中请求的节点类型。
- 系统时钟设置正确。

## <a name="the-bake-tab-still-shows-deleting-after-you-cancel"></a>取消后，"烘焙"选项卡仍显示"删除"
项目声学在成功完成或取消后清理作业的所有 Azure 资源。 此过程最多可能需要 5 分钟。

## <a name="next-steps"></a>后续步骤
* 尝试[统一](unity-quickstart.md)或[虚幻](unreal-quickstart.md)示例内容。
