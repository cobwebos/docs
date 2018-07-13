---
title: 通过使用 C# 桌面库开始使用 Microsoft 语音识别 API | Microsoft Docs
description: 开发使用 Microsoft 语音识别 API 的基本 Windows 应用程序，将语音转换为文本。
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/27/2017
ms.author: zhouwang
ms.openlocfilehash: e59b0e25401fb5182edd52f82985ffed9052286d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365682"
---
# <a name="get-started-with-the-speech-recognition-api-in-c35-for-net-on-windows"></a>开始使用 Windows 上 .NET 适用的 C# 中的语音识别 API

此页面显示如何开发使用语音识别 API 的基本 Windows 应用程序，将语音转换为文本。 使用客户端库允许实时流式处理，这表示当客户端应用程序向服务发送音频时，它同时并异步地接收返回的部分识别结果。

想要从可在任何设备上运行的应用使用语音服务的开发人员可以使用 C# 桌面库。 要使用该库，请为 32 位平台安装 [NuGet 包 Microsoft.ProjectOxford.SpeechRecognition-x86](https://www.nuget.org/packages/Microsoft.ProjectOxford.SpeechRecognition-x86/)，为 64 位平台安装 [NuGet 包 Microsoft.ProjectOxford.SpeechRecognition-x64 ](https://www.nuget.org/packages/Microsoft.ProjectOxford.SpeechRecognition-x64/)。 有关客户端库 API 参考，请参阅 [Microsoft 语音 C# 桌面库](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-Windows/master/docs/SpeechSDK/index.html)。

以下部分介绍如何使用 C# 桌面库安装、构建和运行 C# 示例应用程序。

## <a name="prerequisites"></a>先决条件

### <a name="platform-requirements"></a>平台要求

以下示例是使用 [Visual Studio 2015 社区版](https://www.visualstudio.com/products/visual-studio-community-vs)为 Windows 8+ 和 .NET Framework 4.5+ 开发的。

### <a name="get-the-sample-application"></a>获取示例应用程序

从[语音 C# 桌面库示例](https://github.com/microsoft/cognitive-speech-stt-windows)存储库克隆样本。

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>订阅语音识别 API，并获得订阅密钥免费试用

语音 API 是认知服务（之前的项目 Oxford）的一部分。 你可以从[认知服务订阅](https://azure.microsoft.com/try/cognitive-services/)页面获取订阅密钥免费试用。 选择语音 API 后，选择“获取 API 密钥”以获取密钥。 它将返回主密钥和备用密钥。 两个密钥都绑定到相同的配额，因此你可以使用任一密钥。

> [!IMPORTANT]
> * 获取订阅密钥。 在使用语音客户端库之前，你必须拥有[订阅密钥](https://azure.microsoft.com/try/cognitive-services/)。
>
> * 使用订阅密钥。 使用提供的 C# 桌面示例应用程序，在运行示例时将订阅密钥粘贴到文本框中。 有关详细信息，请参阅[运行示例应用程序](#step-3-run-the-sample-application)。

## <a name="step-1-install-the-sample-application"></a>步骤 1：安装示例应用程序

1. 启动 Visual Studio 2015 ，然后选择“文件” > “打开” > “项目/解决方案”。

2. 浏览到保存下载的语音识别 API 文件的文件夹。 选择“语音” > “Windows”，然后选择 Sample-WP 文件夹。

3. 双击以打开名为 SpeechToText-WPF-Samples.sln 的 Visual Studio 2015 解决方案 (.sln) 文件。 该解决方案随即在 Visual Studio 中打开。

## <a name="step-2-build-the-sample-application"></a>步骤 2：构建示例应用程序

1. 如果你想使用“意向识别”，首先需要注册[语言理解智能服务 (LUIS)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/)。 然后使用 LUIS 应用程序的终结点 URL 在 samples/SpeechRecognitionServiceExample 文件夹的 app.config 文件中设置键 `LuisEndpointUrl` 的值。 有关 LUIS 应用程序的终结点 URL 的详细信息，请参阅[发布应用程序](../../luis/luis-get-started-create-app.md#publish-your-app)。

   > [!TIP]
   > 用 `&amp;` 替换 LUIS 终结点 URL 中的 `&` 字符，以确保 XML 解析器正确解释 URL。

2. 按 Ctrl+Shift+B 或单击功能区菜单上的“生成”。 然后选择“生成解决方案”。

## <a name="step-3-run-the-sample-application"></a>步骤 3：运行示例应用程序

1. 生成完成后，按 F5 或选择功能区菜单上的“启动”来运行该示例。

2. 转到“项目 Oxford 语音转文本示例”窗口。 将订阅密钥粘贴到（在此处粘贴你的订阅密钥进行启动）文本框，如下所示。 要在 PC 或笔记本电脑上保留订阅密钥，请选择“保存密钥”。 若要从系统删除订阅密钥，请选择“删除密钥”将其从 PC 或笔记本电脑中删除。

   ![语音识别粘贴键](../Images/SpeechRecog_paste_key.PNG)

3. 在“语音识别源”下，选择六种语音源中的一种，它们分为两个主要输入类别：

   * 使用计算机的麦克风或附加的麦克风捕获语音。
   * 播放音频文件。

   每个类别有三种识别模式：

    * ShortPhrase 模式：长达 15 秒的语音表达。 当将数据发送到服务器时，客户端接收多个部分结果和一个具有多个 最优选择的最终结果。
    * LongDictation 模式：长达两分钟的语言表达。 当将数据发送到服务器时，基于服务器指示的语句停顿位置，客户端会接收到多个部分结果和多个最终结果。
    * 意向检测：服务器返回有关语音输入的其他结构化信息。 要使用意向检测，需要首先使用 [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) 定型模型。

在此示例应用程序中使用示例音频文件。 在 samples/SpeechRecognitionServiceExample 文件夹下，使用此示例在你下载的存储库中找到此文件。 随着语音输入，选择“将 wav 文件用于 Shortphrase 模式”或“将 wav 文件用于 Longdictation 模式”后，如果没有选择其他文件，则会自动运行这些示例音频文件。 目前，仅支持 WAV 音频格式。

![语音转文本终接口](../Images/HelloJones.PNG)

## <a name="samples-explained"></a>介绍的示例

### <a name="recognition-events"></a>识别事件

* 部分结果事件：每次语音服务预测你可能会说的内容时，甚至在你说完（如果你使用 `MicrophoneRecognitionClient`）或完成发送数据（如果你使用 `DataRecognitionClient`）之前，会调用此事件。
* 错误事件：服务检测到错误时调用。
* 意向事件：在最终识别结果被解析为结构化 JSON 意向后，在“WithIntent”客户端（仅限 ShortPhrase 模式）进行调用。
* 结果事件：
  * 在 `ShortPhrase` 模式下，在你说完后调用此事件并返回 n-best 结果。
  * 在 `LongDictation` 模式下，基于服务指示的语句停顿位置，多次调用事件处理程序。
  * 对于每个最优选择，返回置信度值和几种不同形式的已识别文本。 有关详细信息，请参阅[输出格式](../Concepts.md#output-format)。

事件处理程序已经以代码注释的形式在代码中指出。

## <a name="related-topics"></a>相关主题

* [Microsoft 语音桌面库参考](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-Windows/master/docs/SpeechSDK/index.html)
* [开始使用 Android 上的 Java 中的 Microsoft 语音识别 API](GetStartedJavaAndroid.md)
* [在 iOS 上的 Objective-C 中开始使用 Microsoft 语音识别 API](Get-Started-ObjectiveC-iOS.md)
* [开始使用 JavaScript 中的 Microsoft 语音识别 API](GetStartedJSWebsockets.md)
* [通过 REST 开始使用 Microsoft 语音识别 API](GetStartedREST.md)
