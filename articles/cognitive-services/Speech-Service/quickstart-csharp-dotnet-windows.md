---
title: 快速入门：使用认知服务语音 SDK 在 .NET Framework 下的 C# 中识别语音
titleSuffix: Microsoft Cognitive Services
description: 了解如何使用认知服务语音 SDK 在 .NET Framework 下的 C# 中识别语音
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 587ae9f6452f85dee867047e47ccc272ee508b81
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2018
ms.locfileid: "39325184"
---
# <a name="quickstart-recognize-speech-in-c-under-net-framework-on-windows-using-the-speech-sdk"></a>快速入门：使用语音 SDK 在 .NET Framework 下的 C# 中识别语音

[!include[Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

本文介绍如何使用认知服务语音 SDK 在 Windows 中创建 .NET Framework 的 C# 控制台应用程序，将语音转录为文本。
该应用程序使用 [Microsoft 认知服务 SDK NuGet 包](https://aka.ms/csspeech/nuget)和 Microsoft Visual Studio 2017 生成。

## <a name="prerequisites"></a>先决条件

* 语音服务的订阅密钥。 请参阅[免费试用语音服务](get-started.md)。
* 具有可正常工作的麦克风的 Windows 电脑。
* Visual Studio 2017，Community Edition 或更高版本。
* Visual Studio 中的“.NET 桌面开发”工作负载。 可在“工具”\>“获取工具和功能”中启用它。

  ![启用 .NET 桌面开发](media/sdk/vs-enable-net-desktop-workload.png)

  ![启用 .NET Core 跨平台开发](media/sdk/vs-enable-net-desktop-workload.png)

## <a name="create-a-visual-studio-project"></a>创建 Visual Studio 项目

1. 在 Visual Studio 2017 中，创建新的 Visual C# 控制台应用。 在“新建项目”对话框的左窗格中，展开“已安装”\>“Visual C#”\>“Windows 桌面”，然后选择“控制台应用 (.NET Framework)”。 在项目名称处，请输入 helloworld。

    ![创建 Visual C# 控制台应用 (.NET Framework)](media/sdk/qs-csharp-dotnet-windows-01-new-console-app.png "Create Visual C# Console App (.NET Framework)")

1. 安装并引用[语音 SDK NuGet 包](https://aka.ms/csspeech/nuget)。 在解决方案资源管理器中，右键单击该解决方案，并选择“为解决方案管理 NuGet 包”。

    ![右键单击“为解决方案管理 NuGet 包”](media/sdk/qs-csharp-dotnet-windows-02-manage-nuget-packages.png "为解决方案管理 NuGet 包")

1. 在右上角的“包源”字段中，选择“Nuget.org”。搜索 `Microsoft.CognitiveServices.Speech` 包，并将其安装到“helloworld”项目中。

    ![安装 Microsoft.CognitiveServices.Speech NuGet 包](media/sdk/qs-csharp-dotnet-windows-03-nuget-install-0.5.0.png "安装 Nuget 包")

1. 接受对话框中出现的许可证。

    ![接受许可证](media/sdk/qs-csharp-dotnet-windows-04-nuget-license.png "Accept the license")

1. 包管理器控制台中将显示以下输出行。

   ```text
   Successfully installed 'Microsoft.CognitiveServices.Speech 0.5.0' to helloworld
   ```

## <a name="create-a-platform-configuration-matching-your-pc-architecture"></a>创建与你的电脑体系结构相匹配的平台配置

本节将为配置添加一个与你的处理器体系结构相匹配的新平台。

1. 启动配置管理器。 选择“生成” > “配置管理器”。

    ![启动配置管理器](media/sdk/qs-csharp-dotnet-windows-05-cfg-manager-click.png "Launch the configuration manager")

1. 在“配置管理器”对话框中，添加新平台。 从“活动解决方案平台”下拉列表中，选择“新建”。

    ![在配置管理器窗口下添加新平台](media/sdk/qs-csharp-dotnet-windows-06-cfg-manager-new.png "Add a new platform under the configuration manager window")

1. 如果运行的是 64 位 Windows，请创建名为 `x64` 的新平台配置。 如果运行的是 32 位 Windows，请创建名为 `x86` 的新平台配置。 本文将创建 `x64` 平台配置。

    ![在 64 位 Windows 上，添加名为“x64”的新平台](media/sdk/qs-csharp-dotnet-windows-07-cfg-manager-add-x64.png "添加 x64 平台")

## <a name="add-the-sample-code"></a>添加示例代码

1. 在 Visual Studio 项目中打开 `Program.cs`，然后将此文件中的所有代码替换为以下内容。

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. 将字符串 `YourSubscriptionKey` 替换为订阅密钥。

1. 将字符串 `YourServiceRegion` 替换为与订阅（例如，`westus` 免费试用版订阅）相关联的[区域](regions.md)。

1. 保存对项目的更改。

## <a name="build-and-run-the-sample"></a>生成并运行示例

1. 构建应用程序。 从菜单栏中，选择“构建” > “构建解决方案”。 现在，编译代码时应不会提示错误。

    ![成功构建](media/sdk/qs-csharp-dotnet-windows-08-build.png "Successful build")

1. 启动应用程序。 在菜单栏中，选择“调试” > “开始调试”，或按 F5。

    ![启动应用进入调试](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "Start the app into debugging")

1. 将显示控制台窗口，提示你说出任意内容（英语）。 然后已识别的文本在同一窗口中显示。

    ![成功识别后的控制台输出](media/sdk/qs-csharp-dotnet-windows-10-console-output.png "Console output after successful recognition")

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
在 `quickstart/csharp-dotnet-windows` 文件夹中查找此示例。

## <a name="next-steps"></a>后续步骤

- [翻译语音](how-to-translate-speech-csharp.md)
- [自定义声学模型](how-to-customize-acoustic-models.md)
- [自定义语言模型](how-to-customize-language-model.md)
