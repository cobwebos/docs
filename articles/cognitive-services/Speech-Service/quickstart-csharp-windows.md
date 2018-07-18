---
title: 快速入门：使用适用于 Windows 的认知服务语音 C# SDK 识别语音 | Microsoft Docs
description: 了解如何使用适用于语音服务的 C# SDK 识别语音。
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: wolfma
ms.openlocfilehash: 64281215d139731b61365936bc1b837798ad8fbf
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37016828"
---
# <a name="quickstart-recognize-speech-using-the-cognitive-services-speech-c-sdk"></a>快速入门：使用认知服务语音 C# SDK 识别语音

本文介绍如何使用认知服务语音 SDK 在 Windows 中创建 C# 控制台应用程序，将语音转录为文本。

## <a name="prerequisites"></a>先决条件

* 语音服务的订阅密钥。 请参阅[免费试用语音服务](get-started.md)。
* Visual Studio 2017，Community Edition 或更高版本。
* Visual Studio 中的“.NET 桌面开发”工作负载。 可在“工具”\>“获取工具和功能”中启用它。 

## <a name="create-a-visual-studio-project"></a>创建 Visual Studio 项目

1. 在 Visual Studio 2017 中，创建新的 Visual C# 控制台应用。 在“新建项目”对话框的左窗格中，展开“已安装”，然后选择“控制台应用(.NET Framework)”。 在项目名称处，请输入 CsharpHelloSpeech。

    ![创建 Visual C# 控制台应用 (.NET Framework)](media/sdk/speechsdk-05-vs-cs-new-console-app.png "创建 Visual C# 控制台应用")

2. 安装并引用[语音 SDK NuGet 包](https://aka.ms/csspeech/nuget)。 在解决方案资源管理器中，右键单击该解决方案，并选择“为解决方案管理 NuGet 包”。

    ![右键单击“为解决方案管理 NuGet 包”](media/sdk/speechsdk-06-vs-cs-manage-nuget-packages.png "为解决方案管理 NuGet 包")

3. 在右上角的“包源”字段中，选择“Nuget.org”。搜索并安装 `Microsoft.CognitiveServices.Speech` 包，并将其安装到“CsharpHelloSpeech”项目中。

    ![安装 Microsoft.CognitiveServices.Speech NuGet 包](media/sdk/speechsdk-08-vs-cs-nuget-install.png "安装 Nuget 包")

4. 在弹出的许可证屏幕中，接受许可证：

    ![接受许可证](media/sdk/speechsdk-09-vs-cs-nuget-license.png "Accept the license")

## <a name="create-a-platform-configuration-matching-your-pc-architecture"></a>创建与你的电脑体系结构相匹配的平台配置

本节将为配置添加一个与你的处理器体系结构相匹配的新平台。

1. 启动配置管理器。 选择“生成” > “配置管理器”。

    ![启动配置管理器](media/sdk/speechsdk-12-vs-cs-cfg-manager-click.png "Launch the configuration manager")

2. 在“配置管理器”对话框中，添加新平台。 从“活动解决方案平台”下拉列表中，选择“新建”。

    ![在配置管理器窗口下添加新平台](media/sdk/speechsdk-14-vs-cs-cfg-manager-new.png "Add a new platform under the configuration manager window")

3. 如果运行的是 64 位 Windows，请创建名为 `x64` 的新平台配置。 如果运行的是 32 位 Windows，请创建名为 `x86` 的新平台配置。 本文将创建 `x64` 平台配置。 

    ![在 64 位 Windows 上，添加名为“x64”的新平台](media/sdk/speechsdk-15-vs-cs-cfg-manager-add-x64.png "添加 x64 平台")

## <a name="add-the-sample-code"></a>添加示例代码

1. 在 Visual Studio 项目的 `Program.cs` 中，将 `Program` 类的正文替换为以下内容。 请确保使用为服务获取的订阅密钥和区域替换相应项。

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/Windows/quickstart-csharp/Program.cs#code)]

2. 粘贴代码后，`Main()` 方法必须类似于以下屏幕截图所示：

    ![粘贴代码后的 Main 方法](media/sdk/speechsdk-17-vs-cs-paste-code.png "最终 Main 方法")

3. Visual Studio IntelliSense 会突出显示对无法解析的语音 SDK 类的引用。 若要修复此错误，请将以下 `using` 语句添加到代码的开头（手动添加或使用 Visual Studio 的[快速操作](https://docs.microsoft.com/visualstudio/ide/quick-actions)）。

    [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/Windows/quickstart-csharp/Program.cs#usingstatement)]

    ![使用快速操作添加缺少的 using 语句](media/sdk/speechsdk-18-vs-cs-add-using.png "解决 IntelliSense 问题")

4. 请确保已解析 IntelliSense 突出显示部分并将更改保存到项目中。

## <a name="build-and-run-the-sample"></a>生成并运行示例

1. 构建应用程序。 从菜单栏中，选择“构建” > “构建解决方案”。 现在编译代码应该不会提示错误：

    ![成功构建](media/sdk/speechsdk-20-vs-cs-build.png "Successful build")

2. 启动应用程序。 在菜单栏中，选择“调试” > “开始调试”，或按 F5。 

    ![启动应用进入调试](media/sdk/speechsdk-21-vs-cs-f5.png "Start the app into debugging")

3. 将弹出控制台窗口，提示你说出任意内容（英语）。
识别结果会显示在屏幕上。

    ![成功识别后的控制台输出](media/sdk/speechsdk-22-cs-vs-console-output.png "Console output after successful recognition")

## <a name="download-code"></a>下载代码

有关最新的示例集，请参阅[认知服务语音 SDK 示例 GitHub 存储库 ](https://aka.ms/csspeech/samples)。

## <a name="next-steps"></a>后续步骤

- [翻译语音](how-to-translate-speech.md)
- [自定义语音模型](how-to-customize-speech-models.md)
