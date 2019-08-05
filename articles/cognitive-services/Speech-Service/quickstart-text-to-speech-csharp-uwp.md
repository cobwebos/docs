---
title: 快速入门：合成语音，C# (UWP) - 语音服务
titleSuffix: Azure Cognitive Services
description: 在本文中，请使用认知服务语音 SDK 创建 C# 通用 Windows 平台 (UWP) 应用程序。 将文本中的语音实时合成到设备的扬声器中。 该应用程序是使用语音 SDK NuGet 包和 Microsoft Visual Studio 2017 构建的。
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 6/24/2019
ms.author: yinhew
ms.openlocfilehash: 5e1f8aea1b00cbba7fec6c7ca416a965458ab526
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68607757"
---
# <a name="quickstart-synthesize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>快速入门：使用语音 SDK 在 UWP 应用中合成语音

针对[语音识别](quickstart-csharp-uwp.md)、[语音翻译](quickstart-translate-speech-uwp.md)和[语音优先虚拟助手](quickstart-virtual-assistant-csharp-uwp.md)也提供了快速入门。

在本文中，请使用认知服务[语音 SDK](speech-sdk.md) 开发 C# 通用 Windows 平台（UWP；Windows 版本 1709 或更高版本）应用程序。 此程序会将文本中的语音实时合成到设备的扬声器中。 该应用程序是使用[语音 SDK NuGet 包](https://aka.ms/csspeech/nuget)和 Microsoft Visual Studio 2017 或更高版本（任何版本）生成的。

> [!NOTE]
> 通用 Windows 平台允许开发在支持 Windows 10 的任何设备上运行的应用，包括电脑、Xbox、Surface Hub 和其他设备。

## <a name="prerequisites"></a>先决条件

本快速入门需要：

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) 或更高版本
* 语音服务的 Azure 订阅密钥。 [免费获得一个](get-started.md)。

## <a name="create-a-visual-studio-project"></a>创建 Visual Studio 项目

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>添加示例代码

1. 应用程序的用户界面使用 XAML 进行定义。 在解决方案资源管理器中打开 `MainPage.xaml`。 在设计器的 XAML 视图中，将以下 XAML 代码片段插入到 Grid 标记中（位于 `<Grid>` 和 `</Grid>` 之间）。

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. 打开代码隐藏源文件 `MainPage.xaml.cs`（可以发现它归在 `MainPage.xaml` 下）。 将其中的所有代码替换为以下内容。

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. 在此文件的 `Speak_ButtonClicked` 处理程序中，将字符串 `YourSubscriptionKey` 替换为订阅密钥。

1. 在 `Speak_ButtonClicked` 处理程序中，将字符串 `YourServiceRegion` 替换为与订阅（例如，`westus` 免费试用版订阅）相关联的[区域](regions.md)。

1. 保存对项目的所有更改。

## <a name="build-and-run-the-app"></a>生成并运行应用

1. 构建应用程序。 从菜单栏中，选择“构建” > “构建解决方案”   。 现在，编译代码时应不会提示错误。

    ![Visual Studio 应用程序的屏幕截图，其中突出显示了“生成解决方案”选项](media/sdk/qs-csharp-uwp-08-build.png "成功生成")

1. 启动应用程序。 在菜单栏中，选择“调试” > “开始调试”，或按 F5    。

    ![Visual Studio 应用程序的屏幕截图，其中突出显示了“启动调试”选项](media/sdk/qs-csharp-uwp-09-start-debugging.png "启动应用进入调试")

1. 在文本框中输入一些文本，然后单击“朗读”  。 你的文本将传输到语音服务，并合成为语音，在扬声器上播放。

    ![语音合成用户界面的屏幕截图](media/sdk/qs-tts-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [浏览 GitHub 上的 C# 示例](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>另请参阅

- [自定义语音字体](how-to-customize-voice-font.md)
- [录制语音示例](record-custom-voice-samples.md)
