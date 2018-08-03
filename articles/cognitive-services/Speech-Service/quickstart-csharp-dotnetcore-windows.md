---
title: 快速入门：使用认知服务语音 SDK 在 .NET Core 下的 C# 中识别语音 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 了解如何使用认知服务语音 SDK 在 .NET Core 下的 C# 中识别语音
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 50cf1bbbe529b30da6bfe39281d11eee1c788dd8
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2018
ms.locfileid: "39259154"
---
# <a name="quickstart-recognize-speech-in-c-under-net-core-on-windows-using-the-speech-sdk"></a>快速入门：使用语音 SDK 在 .NET Core 下的 C# 中识别语音

本文介绍如何使用认知服务语音 SDK 在 Windows 中创建 .NET Core 的 C# 控制台应用程序，将语音转录为文本。
该应用程序使用 [Microsoft 认知服务 SDK NuGet 包](https://aka.ms/csspeech/nuget)和 Microsoft Visual Studio 2017 生成。

> [!NOTE]
> .NET Core 是一个实现 [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard) 规范的开源跨平台 .NET 平台。

## <a name="prerequisites"></a>先决条件

* 语音服务的订阅密钥。 请参阅[免费试用语音服务](get-started.md)。
* 具有可正常工作的麦克风的 Windows 电脑。
* [Microsoft Visual Studio 2017](https://www.visualstudio.com/)，Community Edition 或更高版本。
* Visual Studio 中的“.NET Core 跨平台”工作负载。 可在“工具”\>“获取工具和功能”中启用它。

  ![启用 .NET Core 跨平台开发](media/sdk/vs-enable-net-core-workload.png)

## <a name="create-a-visual-studio-project"></a>创建 Visual Studio 项目

1. 在 Visual Studio 2017 中，创建新的 Visual C# .NET Core 控制台应用。 在“新建项目”对话框的左窗格中，展开“已安装”\>“Visual C#”\>“.NET Core”，然后选择“控制台应用(.NET Core)”。 在项目名称处，请输入 helloworld。

    ![创建 Visual C# 控制台应用 (.NET Core)](media/sdk/qs-csharp-dotnetcore-windows-01-new-console-app.png "创建 Visual C# 控制台应用 (.NET Core)")

1. 安装并引用[语音 SDK NuGet 包](https://aka.ms/csspeech/nuget)。 在解决方案资源管理器中，右键单击该解决方案，并选择“为解决方案管理 NuGet 包”。

    ![右键单击“为解决方案管理 NuGet 包”](media/sdk/qs-csharp-dotnetcore-windows-02-manage-nuget-packages.png "为解决方案管理 NuGet 包")

1. 在右上角的“包源”字段中，选择“Nuget.org”。搜索并安装 `Microsoft.CognitiveServices.Speech` 包，并将其安装到“helloworld”项目中。

    ![安装 Microsoft.CognitiveServices.Speech NuGet 包](media/sdk/qs-csharp-dotnetcore-windows-03-nuget-install-0.5.0.png "安装 Nuget 包")

1. 接受对话框中出现的许可证。

    ![接受许可证](media/sdk/qs-csharp-dotnetcore-windows-04-nuget-license.png "Accept the license")

## <a name="add-the-sample-code"></a>添加示例代码

1. 在 Visual Studio 项目中打开 `Program.cs`，然后将此文件中的所有代码替换为以下内容。

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnetcore-windows/helloworld/Program.cs#code)]

1. 将字符串 `YourSubscriptionKey` 替换为订阅密钥。

1. 将字符串 `YourServiceRegion` 替换为与订阅（例如，`westus` 免费试用版订阅）相关联的[区域](regions.md)。

1. 保存对项目的更改。

## <a name="build-and-run-the-sample"></a>生成并运行示例

1. 构建应用程序。 从菜单栏中，选择“构建” > “构建解决方案”。 现在，编译代码时应不会提示错误。

    ![成功构建](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Successful build")

1. 启动应用程序。 在菜单栏中，选择“调试” > “开始调试”，或按 F5。

    ![启动应用进入调试](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "Start the app into debugging")

1. 将显示控制台窗口，提示你说出任意内容（英语）。 然后已识别的文本在同一窗口中显示。

    ![成功识别后的控制台输出](media/sdk/qs-csharp-dotnetcore-windows-07-console-output.png "Console output after successful recognition")

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
在 `quickstart/csharp-dotnetcore-windows` 文件夹中查找此示例。

## <a name="next-steps"></a>后续步骤

- [翻译语音](how-to-translate-speech.md)
- [自定义声学模型](how-to-customize-acoustic-models.md)
- [自定义语言模型](how-to-customize-language-model.md)
