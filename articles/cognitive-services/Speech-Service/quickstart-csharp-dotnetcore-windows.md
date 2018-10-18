---
title: 快速入门：在 Windows 上使用认知服务语音 SDK 通过 .NET Core 下的 C# 识别语音
titleSuffix: Microsoft Cognitive Services
description: 了解如何在 Windows 上使用认知服务语音 SDK 通过 .NET Core 下的 C# 识别语音
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 10/12/2018
ms.author: wolfma
ms.openlocfilehash: 04cdfd487036e79eccbb4fb879902a12e077d104
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2018
ms.locfileid: "49341644"
---
# <a name="quickstart-recognize-speech-in-c-under-net-core-on-windows-by-using-the-speech-sdk"></a>快速入门：在 Windows 上使用语音 SDK 通过 .NET Core 下的 C# 识别语音

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

在本文中，将使用认知服务[语音 SDK](speech-sdk.md) 为 Windows 上的 .NET Core 创建 C# 控制台应用程序。 可以通过电脑的麦克风实时将语音转录为文本。 该应用程序是使用[语音 SDK NuGet 包](https://aka.ms/csspeech/nuget)和 Microsoft Visual Studio 2017（任何版本）构建的。

> [!NOTE]
> .NET Core 是一个实现了 [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard) 规范的开源跨平台 .NET 平台。

需要具有语音服务订阅密钥才能完成此快速入门。 你可以免费获得一个。 有关详细信息，请参阅[免费试用语音服务](get-started.md)。


## <a name="create-a-visual-studio-project"></a>创建 Visual Studio 项目

1. 启动 Visual Studio 2017。

1. 确保“.NET 跨平台开发”工作负荷可用。 在 Visual Studio 菜单栏中选择“工具” > “获取工具和功能”，以打开 Visual Studio 安装程序。 如果此工作负荷已启用，请关闭对话框。

    ![Visual Studio 安装程序的屏幕截图，其中突出显示了“工作负荷”选项卡](media/sdk/vs-enable-net-core-workload.png)

    否则，请选择“.NET Core 跨平台开发”旁边的框，然后选择对话框右下角的“修改”。 安装新功能将花费一些时间。

1. 创建新的 Visual C# .NET Core 控制台应用。 在“新建项目”对话框中，从左侧窗格中展开“已安装” > “Visual C#” > “.NET Core”。 然后，选择“控制台应用(.NET Core)”。 在项目名称处，输入 helloworld。

    ![“新建项目”对话框的屏幕截图](media/sdk/qs-csharp-dotnetcore-windows-01-new-console-app.png "创建 Visual C# 控制台应用(.NET Core)")

1. 安装并引用[语音 SDK NuGet 包](https://aka.ms/csspeech/nuget)。 在解决方案资源管理器中，右键单击该解决方案，并选择“为解决方案管理 NuGet 包”。

    ![解决方案资源管理器的屏幕截图，其中突出显示了“为解决方案管理 NuGet 包”选项](media/sdk/qs-csharp-dotnetcore-windows-02-manage-nuget-packages.png "为解决方案管理 NuGet 包")

1. 在右上角的“包源”字段中，选择“nuget.org”。搜索 `Microsoft.CognitiveServices.Speech` 包，并将其安装到“helloworld”项目中。

    ![“管理解决方案包”对话框的屏幕截图](media/sdk/qs-csharp-dotnetcore-windows-03-nuget-install-1.0.0.png "安装 NuGet 包")

1. 接受显示的许可证即可开始安装 NuGet 包。

    ![“接受许可证”对话框的屏幕截图](media/sdk/qs-csharp-dotnetcore-windows-04-nuget-license.png "接受许可证")

安装此包后，在包管理器控制台中会显示一条确认消息。


## <a name="add-sample-code"></a>添加示例代码

1. 打开 `Program.cs` 并将其中的所有代码替换为以下内容。

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnetcore/helloworld/Program.cs#code)]

1. 在同一文件中，将字符串 `YourSubscriptionKey` 替换为你的订阅密钥。

1. 另外，请将字符串 `YourServiceRegion` 替换为与你的订阅关联的[区域](regions.md)（例如，免费试用版订阅的 `westus`）。

1. 保存对项目的更改。

## <a name="build-and-run-the-app"></a>生成并运行应用

1. 构建应用程序。 从菜单栏中，选择“构建” > “构建解决方案”。 编译代码时应不会出错。

    ![Visual Studio 应用程序的屏幕截图，其中突出显示了“生成解决方案”选项](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "成功生成")

1. 启动应用程序。 在菜单栏中，选择“调试” > “开始调试”，或按 F5。

    ![Visual Studio 应用程序的屏幕截图，其中突出显示了“启动调试”选项](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "启动应用进入调试")

1. 此时将显示控制台窗口，提示你说出任意内容。 说一个英语短语或句子。 你的语音将传输到语音服务并转录为文本，该文本将显示在同一窗口中。

    ![成功识别后的控制台输出的屏幕截图](media/sdk/qs-csharp-dotnetcore-windows-07-console-output.png "成功识别后的控制台输出")

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
在 `quickstart/csharp-dotnetcore-windows` 文件夹中查找此示例。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用适用于 C# 的语音 SDK 从语音中识别意向](how-to-recognize-intents-from-speech-csharp.md)

## <a name="see-also"></a>另请参阅

- [翻译语音](how-to-translate-speech-csharp.md)
- [自定义声学模型](how-to-customize-acoustic-models.md)
- [自定义语言模型](how-to-customize-language-model.md)
