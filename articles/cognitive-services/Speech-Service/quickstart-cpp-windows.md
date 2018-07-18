---
title: 适用于 C++ 和 Windows 的语音 SDK 快速入门 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 获取信息和代码示例，帮助你快速开始使用认知服务中适用于 Windows 和 C++ 的语音 SDK。
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 0bcdc3c4357cb8985fad16c607957bffad4a2b8c
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37049224"
---
# <a name="quickstart-for-c-and-windows"></a>适用于 C + + 和 Windows 的快速入门

认知服务语音 SDK 的当前版本是 `0.4.0`。

介绍了如何为 Windows 桌面创建使用语音 SDK 且基于 C++-的控制台应用程序。
该应用程序基于 [Microsoft 认知服务 SDK NuGet 包](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech)和 Microsoft Visual Studio 2017。

> [!NOTE]
> 如果要寻找适用于 C# 和 Linux 的快速入门，请前往[此处](quickstart-cpp-linux.md)。<br>
> 如果要寻找适用于 C# 和 Windows 的快速入门，请前往[此处](quickstart-csharp-windows.md)。

> [!NOTE]
> 本快速入门需要使用个人电脑和正常工作的麦克风。<br>
> 有关识别给定音频输入文件中的语音的示例，请参阅[示例](speech-to-text-sample.md#speech-recognition-from-a-file)。

> [!NOTE]
> 确保你的 Visual Studio 安装包括**使用 C++ 的桌面开发**工作负载。
> 如果不确定，请使用以下步骤进行检查和修复：在 Visual Studio 2017 中，选择“工具”\>“获取工具和功能”，然后选择“是”来确认用户帐户控制提示。
> 在“工作负载”选项卡上，如果**使用 C++ 的桌面开发**没有设置它旁边的复选框，请设置该复选框，然后单击“修改”来保存更改。

[!include[Get a Subscription Key](includes/get-subscription-key.md)]

## <a name="creating-an-empty-console-application-project"></a>创建一个空的控制台应用程序项目

在 Visual Studio 2017 中，创建一个新的名为“CppHelloSpeech”的 Visual C++ Windows 桌面 Windows 控制台应用程序：

![创建 Visual C++ Windows 桌面 Windows 控制台应用程序](media/sdk/speechsdk-05-vs-cpp-new-console-app.png)

如果是在 64 位 Windows 安装上运行，可以选择将生成平台切换到 `x64`：

![将生成平台切换到 x64](media/sdk/speechsdk-07-vs-cpp-switch-to-x64.png)

## <a name="install-and-reference-the-speech-sdk-nuget-package"></a>安装并引用语音 SDK NuGet 包

> [!NOTE]
> 确保为你的 Visual Studio 2017 安装启用 NuGet 包管理器。
> 在 Visual Studio 2017 中，选择“工具”\>“获取工具和功能”，然后选择“是”来确认用户帐户控制提示。 然后，选择“单个组件”选项卡，在“代码工具”下查找“NuGet 包管理器”。
> 如果未设置其左侧的复选框，请确保设置该复选框并单击“修改”以保存更改。
>
> ![在 Visual Studio 中启用 NuGet 包管理器 ](media/sdk/speechsdk-05-vs-enable-nuget-package-manager.png)

在解决方案资源管理器中，右键单击该解决方案，并单击“为解决方案管理 NuGet 包”。

![右键单击“为解决方案管理 NuGet 包”](media/sdk/speechsdk-09-vs-cpp-manage-nuget-packages.png)

在右上角的“包源”字段中，选择“Nuget.org”。
从“浏览”选项卡中，搜索“Microsoft.CognitiveServices.Speech”包，选择该包，选中右侧的“项目”和“CppHelloSpeech”框，然后选择“安装”以将其安装到 CppHelloSpeech 项目中。

![安装 Microsoft.CognitiveServices.Speech NuGet 包](media/sdk/speechsdk-11-vs-cpp-manage-nuget-install.png)

在弹出的许可证屏幕中，接受许可证：

![接受许可证](media/sdk/speechsdk-12-vs-cpp-manage-nuget-license.png)

## <a name="add-the-sample-code"></a>添加示例代码

将默认的起始代码替换为以下代码：

[!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/Windows/quickstart-cpp/CppHelloSpeech.cpp#code)]

> [!IMPORTANT]
> 将订阅密钥替换为你获取的密钥。 <br>
> 将区域替换为[语音服务 REST API](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/rest-apis) 中的区域，例如，替换为“westus”。

![添加订阅密钥](media/sdk/sub-key-recognize-speech-cpp.png)

## <a name="build-and-run-the-sample"></a>生成并运行示例

现在，编译代码时应该不会提示错误：

![成功的生成](media/sdk/speechsdk-16-vs-cpp-build.png)

在调试器下通过“启动”按钮或使用 F5 键盘快捷键来启动项目：

![启动应用进行调试](media/sdk/speechsdk-17-vs-cpp-f5.png)

应该会弹出一个控制台窗口，提示你说一些内容（用英语）。
识别结果将显示在屏幕上。

![成功识别后的控制台输出](media/sdk/speechsdk-18-vs-cpp-console-output-release.png)

## <a name="downloading-the-sample"></a>下载示例

有关最新的示例集，请参阅[认知服务语音 SDK 示例 GitHub 存储库](https://aka.ms/csspeech/samples)。

## <a name="next-steps"></a>后续步骤

* 访问[示例页](samples.md)来查看更多示例。
