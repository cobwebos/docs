---
title: 快速入门：识别语音，C# (UWP) - 语音服务
titleSuffix: Azure Cognitive Services
description: 在本文中，请使用认知服务语音 SDK 创建 C# 通用 Windows 平台 (UWP) 应用程序。 可以通过设备的麦克风实时将语音转录为文本。 该应用程序是使用语音 SDK NuGet 包和 Microsoft Visual Studio 2019 构建的。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/19/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: fe5ff376a7895e2ca5246c0b9eb575752b07c7a1
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382277"
---
# <a name="quickstart-recognize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>快速入门：使用语音 SDK 在 UWP 应用中识别语音

针对[语音合成](quickstart-text-to-speech-csharp-uwp.md)、[语音翻译](quickstart-translate-speech-uwp.md)和[语音优先虚拟助手](quickstart-virtual-assistant-csharp-uwp.md)也提供了快速入门。

如果愿意，请选择其他编程语言和/或环境：<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

在本文中，请使用认知服务[语音 SDK](speech-sdk.md) 开发 C# 通用 Windows 平台 (UWP) 应用程序。 该程序将通过设备的麦克风实时将语音转录为文本。 该应用程序是使用[语音 SDK NuGet 包](https://aka.ms/csspeech/nuget)和 Microsoft Visual Studio 2019（任何版本）构建的。

> [!NOTE]
> 通用 Windows 平台允许开发在支持 Windows 10 的任何设备上运行的应用，包括电脑、Xbox、Surface Hub 和其他设备。

## <a name="prerequisites"></a>先决条件

本快速入门需要：

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)。
* 语音服务的 Azure 订阅密钥。 [免费获得一个](get-started.md)。

## <a name="create-a-visual-studio-project"></a>创建 Visual Studio 项目

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>添加示例代码

现在，添加定义应用程序用户界面的 XAML 代码，并添加 C# 代码隐藏实现。

1. 在“解决方案资源管理器”  中打开 `MainPage.xaml`。

1. 在设计器的 XAML 视图中，将以下 XAML 代码片段插入到“Grid”  标记中（位于 `<Grid>` 和 `</Grid>` 之间）：

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. 在“解决方案资源管理器”  中，打开代码隐藏源文件 `MainPage.xaml.cs`。 （其分组在 `MainPage.xaml` 下。）

1. 将其中的所有代码替换为以下片段：

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. 在源文件的 `SpeechRecognitionFromMicrophone_ButtonClicked` 处理程序中，找到字符串 `YourSubscriptionKey` 并将其替换为订阅密钥。

1. 在 `SpeechRecognitionFromMicrophone_ButtonClicked` 处理程序中，找到字符串 `YourServiceRegion` 并将其替换为与订阅关联的[区域](regions.md)。 （例如，将 `westus` 用于免费试用版订阅。）

1. 在菜单栏中，选择“文件”   > “全部保存”  以保存所做的更改。

## <a name="build-and-run-the-application"></a>构建并运行应用程序

现已准备好构建并测试应用程序。

1. 从菜单栏中，选择“构建”   > “构建解决方案”  以构建应用程序。 现在，编译代码时应不会提示错误。

1. 选择“调试”   > “开始调试”  （或按 F5  ）以启动应用程序。 此时将显示“helloworld”  窗口。

   ![C# 中的示例 UWP 语音识别应用程序 - 快速入门](media/sdk/qs-csharp-uwp-helloworld-window.png)

1. 选择“启用麦克风”  ，并在弹出访问权限请求时选择“是”  。

   ![麦克风访问权限请求](media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. 选择“使用麦克风输入进行语音识别”，然后对着设备的麦克风说一个英文短语或句子  。 你的语音将传输到语音服务并转录为文本，该文本将显示在窗口中。

   ![语音识别用户界面](media/sdk/qs-csharp-uwp-11-ui-result.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [浏览 GitHub 上的 C# 示例](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>另请参阅

- [快速入门：使用适用于 C# 的语音 SDK 翻译语音 (UWP)](quickstart-translate-speech-uwp.md)
- [训练自定义语音模型](how-to-custom-speech-train-model.md)
