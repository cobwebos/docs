---
title: 快速入门：用于 C# Unity 的语音 SDK 平台设置 - 语音服务
titleSuffix: Azure Cognitive Services
description: 使用本指南，通过语音服务 SDK 设置用于 C# Unity 的平台。
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/10/2019
ms.author: erhopf
ms.openlocfilehash: 8d9e05b5d8105cce8355d217cc6922c25bbe1730
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75467166"
---
本指南介绍如何安装用于 [Unity](https://unity3d.com/) 的[语音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)。

> [!NOTE]
> 适用于 Unity 的语音 SDK 支持 Windows 桌面版（x86 和 x64）或通用 Windows 平台（x86、x64、ARM/ARM64）、Android（x86、ARM32/64）或 iOS（x64 模拟器、ARM32 和 ARM64）

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>必备条件

本快速入门需要：

- [Unity 2018.3 或更高版本](https://store.unity.com/)，以及[支持 UWP ARM64 的 Unity 2019.1](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal)。
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)。 也可以使用 Visual Studio 2017 版本 15.9 或更高版本。
- 为了支持 Windows ARM64，请安装[适用于 ARM64 的可选版本工具，以及适用于 ARM64 的 Windows 10 SDK](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/)

## <a name="install-the-speech-sdk"></a>安装语音 SDK

若要安装适用于 Unity 的语音 SDK，请执行以下步骤：

1. 下载并打开[适用于 Unity 的语音 SDK](https://aka.ms/csspeech/unitypackage)，该程序打包为 Unity 资产包 (.unitypackage)，应该已经与 Unity 关联。 打开资产包后，会显示“导入 Unity 包”对话框。  可能需要创建并打开一个空项目才能使用此步骤。

   [![Unity 编辑器中的“导入 Unity 包”对话框](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-unity-01-import.png)](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-unity-01-import.png#lightbox)

1. 确保选择所有文件，然后选择“导入”。  片刻之后，Unity 资产包即会导入到项目中。

有关将资产包导入 Unity 的详细信息，请参阅 [Unity 文档](https://docs.unity3d.com/Manual/AssetPackages.html)。

现在可以继续完成下面的[后续步骤](#next-steps)。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [windows](../quickstart-list.md)]
