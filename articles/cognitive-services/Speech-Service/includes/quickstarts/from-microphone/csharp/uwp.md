---
title: 快速入门：从麦克风中识别语音，C# (UWP) - 语音服务
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: 5f83f249228c96021979edc14c092eded03b96e4
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818868"
---
## <a name="prerequisites"></a>先决条件

准备工作：

> [!div class="checklist"]
> * [创建一个 Azure 搜索资源](../../../../get-started.md)
> * [设置开发环境](../../../../quickstarts/setup-platform.md?tabs=uwp)
> * [创建空示例项目](../../../../quickstarts/create-project.md?tabs=uwp)
> * 请确保你有权访问麦克风，以便进行音频捕获

如果尚未执行此操作，很好！ 让我们继续。

## <a name="open-your-project-in-visual-studio"></a>在 Visual Studio 中打开项目

第一步是确保项目在 Visual Studio 中打开。

## <a name="start-with-some-boilerplate-code"></a>从一些样板代码开始

让我们添加一些代码作为项目的主干。

1. 在“解决方案资源管理器”  中打开 `MainPage.xaml`。

2. 在设计器的 XAML 视图中，将以下 XAML 代码片段插入到“Grid”  标记中（位于 `<Grid>` 和 `</Grid>` 之间）：

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml#StackPanel)]

3. 在“解决方案资源管理器”  中，打开代码隐藏源文件 `MainPage.xaml.cs`。 （其分组在 `MainPage.xaml` 下。）

4. 将此代码替换为以下基代码：

   [!code-csharp[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=6-50,55-56,94-154)]

## <a name="create-a-speech-configuration"></a>创建语音配置

在初始化 `SpeechRecognizer` 对象之前，需要创建一个使用订阅密钥和订阅区域的配置。 将此代码插入 `RecognizeSpeechAsync()` 方法。

> [!NOTE]
> 此示例使用 `FromSubscription()` 方法来生成 `SpeechConfig`。 有关可用方法的完整列表，请参阅 [SpeechConfig 类](https://docs.microsoft.com/dotnet/api/) [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=51-53)]

## <a name="initialize-a-speechrecognizer"></a>初始化 SpeechRecognizer

现在，让我们创建 `SpeechRecognizer`。 此对象是在 using 语句中创建的，以确保正确释放非托管资源。 将此代码插入语音配置下的 `RecognizeSpeechAsync()` 方法。
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=58,59,93)]

## <a name="recognize-a-phrase"></a>识别短语

在 `SpeechRecognizer` 对象中，我们将调用 `RecognizeOnceAsync()` 方法。 此方法是告知语音服务你要发送单个需识别的短语，在确定该短语后会停止识别语音。

在 using 语句中，添加以下代码：[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=66)]

## <a name="display-the-recognition-results-or-errors"></a>显示识别结果（或错误）

如果语音服务返回了识别结果，则需执行一些操作。 我们会简单地将结果输出到状态面板。

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=68-93)]

## <a name="build-and-run-the-application"></a>构建并运行应用程序

现已准备好构建并测试应用程序。

1. 从菜单栏中，选择“构建”   > “构建解决方案”  以构建应用程序。 现在，编译代码时应不会提示错误。

1. 选择“调试”   > “开始调试”  （或按 F5  ）以启动应用程序。 此时将显示“helloworld”  窗口。

   ![C# 中的示例 UWP 语音识别应用程序 - 快速入门](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-helloworld-window.png)

1. 选择“启用麦克风”  ，并在弹出访问权限请求时选择“是”  。

   ![麦克风访问权限请求](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. 选择“使用麦克风输入进行语音识别”，然后对着设备的麦克风说一个英文短语或句子  。 你的语音将传输到语音服务并转录为文本，该文本将显示在窗口中。

   ![语音识别用户界面](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-11-ui-result.png)

## <a name="next-steps"></a>后续步骤

[!INCLUDE [footer](./footer.md)]
