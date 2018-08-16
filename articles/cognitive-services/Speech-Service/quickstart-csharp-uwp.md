---
title: 快速入门：使用认知服务语音 SDK 在 UWP 应用中的 C# 中识别语音
titleSuffix: Microsoft Cognitive Services
description: 了解如何使用认知服务语音 SDK 在 UWP 应用中识别语音
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: d1245b07ac0de680c13542b9af86b25bdf517c21
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2018
ms.locfileid: "39576128"
---
# <a name="quickstart-recognize-speech-in-a-uwp-app-using-the-speech-sdk"></a>快速入门：使用语音 SDK 在 UWP 应用中识别语音

[!include[Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

本文介绍如何使用认知服务语音 SDK 创建通用 Windows 平台 (UWP) 应用程序，将语音转录为文本。
该应用程序使用 [Microsoft 认知服务 SDK NuGet 包](https://aka.ms/csspeech/nuget)和 Microsoft Visual Studio 2017 生成。

> [!NOTE]
> 通用 Windows 平台允许开发在支持 Windows 10 的任何设备上运行的应用，包括电脑、Xbox、Surface Hub 和其他设备。 使用语音 SDK 的应用尚未通过 Windows 应用认证工具包 (WACK)。 可以旁加载应用，但目前可能无法提交到 Windows 应用商店。

## <a name="prerequisites"></a>先决条件

* 语音服务的订阅密钥。 请参阅[免费试用语音服务](get-started.md)。
* Windows 电脑（Windows 10 Fall Creators Update 或更高版本）与可正常使用的麦克风。
* [Microsoft Visual Studio 2017](https://www.visualstudio.com/)，Community Edition 或更高版本。
* Visual Studio 中的通用 Windows 平台开发工作负荷。可以在“工具”\>“获取工具和功能”中启用它。

  ![启用通用 Windows 平台开发](media/sdk/vs-enable-uwp-workload.png)

## <a name="create-a-visual-studio-project"></a>创建 Visual Studio 项目

1. 在 Visual Studio 2017 中，创建新的 Visual C# Windows 通用空白应用。 在“新建项目”对话框的左窗格中，展开“已安装”\>“Visual C#”\>“Windows 通用”，然后选择“空白应用(通用 Windows)”。 在项目名称处，请输入 helloworld。

    ![](media/sdk/qs-csharp-uwp-01-new-blank-app.png)

1. 在弹出的“新建通用 Windows 平台项目”窗口中，为“最低版本”选择“Windows 10 Fall Creators Update(10.0; 内部版本 16299)”，为“目标版本”选择此版本或任何更高版本，然后单击“确定”。

    ![](media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. 如果是在 64 位 Windows 安装上运行，可以将生成平台切换到 `x64`。

   ![将生成平台切换到 x64](media/sdk/qs-csharp-uwp-03-switch-to-x64.png)

   > [!NOTE]
   > 在此期间，语音 SDK 支持 Intel 兼容的处理器，但不支持 ARM。

1. 安装并引用[语音 SDK NuGet 包](https://aka.ms/csspeech/nuget)。 在解决方案资源管理器中，右键单击该解决方案，并选择“为解决方案管理 NuGet 包”。

    ![右键单击“为解决方案管理 NuGet 包”](media/sdk/qs-csharp-uwp-04-manage-nuget-packages.png)

1. 在右上角的“包源”字段中，选择“Nuget.org”。搜索并安装 `Microsoft.CognitiveServices.Speech` 包，并将其安装到“helloworld”项目中。

    ![安装 Microsoft.CognitiveServices.Speech NuGet 包](media/sdk/qs-csharp-uwp-05-nuget-install-0.5.0.png "安装 Nuget 包")

1. 接受显示的许可证。

    ![接受许可证](media/sdk/qs-csharp-uwp-06-nuget-license.png "Accept the license")

1. 包管理器控制台中将显示以下输出行。

   ```text
   Successfully installed 'Microsoft.CognitiveServices.Speech 0.5.0' to helloworld
   ```

## <a name="add-the-sample-code"></a>添加示例代码

1. 在解决方案资源管理器中，双击“Package.appxmanifest”，编辑应用程序清单。
   选择“功能”选项卡，选择“麦克风”功能的复选框，然后保存所做的更改。

   ![](media/sdk/qs-csharp-uwp-07-capabilities.png)

1. 双击解决方案资源管理器中的 `MainPage.xaml`，编辑应用程序的用户界面。 

    在设计器的 XAML 视图中，将以下 XAML 代码片段插入到 Grid 标记中（位于 `<Grid>` 和 `</Grid>` 之间）。

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. 双击解决方案资源管理器中的 `MainPage.xaml.cs`，编辑 XAML 代码隐藏（它在 `MainPage.xaml` 项下分组）。
   将此文件中的所有代码替换为以下内容。

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. 在 `SpeechRecognitionFromMicrophone_ButtonClicked` 处理程序中，将字符串 `YourSubscriptionKey` 替换为订阅密钥。

1. 在 `SpeechRecognitionFromMicrophone_ButtonClicked` 处理程序中，将字符串 `YourServiceRegion` 替换为与订阅（例如，`westus` 免费试用版订阅）相关联的[区域](regions.md)。

1. 保存对项目的所有更改。

## <a name="build-and-run-the-sample"></a>生成并运行示例

1. 构建应用程序。 从菜单栏中，选择“构建” > “构建解决方案”。 现在，编译代码时应不会提示错误。

    ![成功构建](media/sdk/qs-csharp-uwp-08-build.png "Successful build")

1. 启动应用程序。 在菜单栏中，选择“调试” > “开始调试”，或按 F5。

    ![启动应用进入调试](media/sdk/qs-csharp-uwp-09-start-debugging.png "Start the app into debugging")

1. 弹出 GUI 窗口。 首先单击“启用麦克风”按钮，然后确认弹出的权限请求。

    ![启动应用进入调试](media/sdk/qs-csharp-uwp-10-access-prompt.png "Start the app into debugging")

1. 单击“使用麦克风输入进行语音识别”，然后对着设备的麦克风说一个短语。 已识别的文本在窗口中显示。

    ![](media/sdk/qs-csharp-uwp-11-ui-result.png)

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
在 `quickstart/csharp-uwp` 文件夹中查找此示例。

## <a name="next-steps"></a>后续步骤

- [翻译语音](how-to-translate-speech-csharp.md)
- [自定义声学模型](how-to-customize-acoustic-models.md)
- [自定义语言模型](how-to-customize-language-model.md)
