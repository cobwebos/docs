---
title: 快速入门：使用认知服务语音 SDK 在 UWP 应用中的 C# 中识别语音
titleSuffix: Microsoft Cognitive Services
description: 了解如何使用认知服务语音 SDK 在 UWP 应用中识别语音
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: 2eb6bda7066a01e5532fe7c0e20b0ee13f4289b6
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2018
ms.locfileid: "47432981"
---
# <a name="quickstart-recognize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>快速入门：使用语音 SDK 在 UWP 应用中识别语音

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

在本文中，请使用认知服务[语音 SDK](speech-sdk.md) 创建 C# 通用 Windows 平台 (UWP) 应用程序。 可以通过设备的麦克风实时将语音转录为文本。 该应用程序使用[语音 SDK NuGet 包](https://aka.ms/csspeech/nuget)和 Microsoft Visual Studio 2017（任何版本）生成。

> [!NOTE]
> 通用 Windows 平台允许开发在支持 Windows 10 的任何设备上运行的应用，包括电脑、Xbox、Surface Hub 和其他设备。

## <a name="prerequisites"></a>先决条件

需要具有语音服务订阅密钥才能完成此快速入门。 你可以免费获得一个。 有关详细信息，请参阅[免费试用语音服务](get-started.md)。

## <a name="create-a-visual-studio-project"></a>创建 Visual Studio 项目

1. 启动 Visual Studio 2017。

1. 确保“通用 Windows 平台开发”工作负荷可用。 在 Visual Studio 菜单栏中选择“工具” > “获取工具和功能”，以便打开 Visual Studio 安装程序。 如果此工作负荷已启用，请关闭对话框。 

    ![Visual Studio 安装程序的屏幕截图，其中突出显示了“工作负荷”选项卡](media/sdk/vs-enable-uwp-workload.png)

    否则，请选择“.NET 跨平台开发”旁边的框，然后选择对话框右下角的“修改”。 安装新功能需要一定的时间。

1. 创建空白的 Visual C# 通用 Windows 应用。 首先，从菜单中选择“文件” > “新建” > “项目”。 在“新建项目”对话框的左窗格中，展开“已安装” > “Visual C#” > “Windows 通用”， 然后选择“空白应用(通用 Windows)”。 在项目名称处，输入 helloworld。

    ![“新建项目”对话框屏幕截图](media/sdk/qs-csharp-uwp-01-new-blank-app.png)

1. Speed SDK 要求为 Windows 10 Fall Creators Update 或更高版本生成应用程序。 在弹出的“新建通用 Windows 平台项目”窗口中，选择“Windows 10 Fall Creators Update(10.0; 内部版本 16299)”作为“最低版本”。 在“目标版本”框中，选择此版本或任何更高版本，然后单击“确定”。

    ![“新式通用 Windows 平台项目”窗口的屏幕截图](media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. 如果运行 64 位 Windows，可以使用 Visual Studio 工具栏中的下拉菜单将生成平台切换为 `x64`。 （64 位 Windows 可以运行 32 位应用程序，因此可以根据自己的偏好让它设置为 `x86`。）

   ![Visual Studio 工具栏的屏幕截图，其中突出显示了 x64](media/sdk/qs-csharp-uwp-03-switch-to-x64.png)

   > [!NOTE]
   > 语音 SDK 仅支持 Intel 兼容的处理器。 目前不支持 ARM。

1. 安装并引用[语音 SDK NuGet 包](https://aka.ms/csspeech/nuget)。 在解决方案资源管理器中，右键单击该解决方案，并选择“为解决方案管理 NuGet 包”。

    ![解决方案资源管理器的屏幕截图，其中突出显示了“为解决方案管理 NuGet 包”选项](media/sdk/qs-csharp-uwp-04-manage-nuget-packages.png)

1. 在右上角的“包源”字段中，选择“nuget.org”。搜索 `Microsoft.CognitiveServices.Speech` 包，并将其安装到“helloworld”项目中。

    ![“管理解决方案包”对话框的屏幕截图](media/sdk/qs-csharp-uwp-05-nuget-install-1.0.0.png "安装 NuGet 包")

1. 接受显示的许可证即可开始安装 NuGet 包。

    ![“接受许可证”对话框的屏幕截图](media/sdk/qs-csharp-uwp-06-nuget-license.png "接受许可证")

1. 包管理器控制台中将显示以下输出行。

   ```text
   Successfully installed 'Microsoft.CognitiveServices.Speech 1.0.0' to helloworld
   ```

1. 由于应用程序使用麦克风进行语音输入，因此请向项目添加“麦克风”功能。 在解决方案资源管理器中，双击“Package.appxmanifest”，编辑应用程序清单。 然后切换到“功能”选项卡，选择“麦克风”功能的框，然后保存所做的更改。

   ![Visual Studio 应用程序清单的屏幕截图，其中突出显示了“功能”和“麦克风”](media/sdk/qs-csharp-uwp-07-capabilities.png)


## <a name="add-sample-code"></a>添加示例代码

1. 应用程序的用户界面使用 XAML 进行定义。 在解决方案资源管理器中打开 `MainPage.xaml`。 在设计器的 XAML 视图中，将以下 XAML 代码片段插入到 Grid 标记中（位于 `<Grid>` 和 `</Grid>` 之间）。

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. 打开代码隐藏源文件 `MainPage.xaml.cs`（可以发现它归在 `MainPage.xaml` 下）。 将其中的所有代码替换为以下内容。

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. 在此文件的 `SpeechRecognitionFromMicrophone_ButtonClicked` 处理程序中，将字符串 `YourSubscriptionKey` 替换为订阅密钥。

1. 在 `SpeechRecognitionFromMicrophone_ButtonClicked` 处理程序中，将字符串 `YourServiceRegion` 替换为与订阅（例如，`westus` 免费试用版订阅）相关联的[区域](regions.md)。

1. 保存对项目的所有更改。

## <a name="build-and-run-the-app"></a>生成并运行应用

1. 构建应用程序。 从菜单栏中，选择“构建” > “构建解决方案”。 现在，编译代码时应不会提示错误。

    ![Visual Studio 应用程序的屏幕截图，其中突出显示了“生成解决方案”选项](media/sdk/qs-csharp-uwp-08-build.png "成功生成")

1. 启动应用程序。 在菜单栏中，选择“调试” > “开始调试”，或按 F5。

    ![Visual Studio 应用程序的屏幕截图，其中突出显示了“启动调试”选项](media/sdk/qs-csharp-uwp-09-start-debugging.png "启动应用进入调试")

1. 弹出一个窗口。 选择“启用麦克风”，然后确认弹出的权限请求。

    ![权限请求的屏幕截图](media/sdk/qs-csharp-uwp-10-access-prompt.png "启动应用进入调试")

1. 选择“使用麦克风输入进行语音识别”，然后对着设备的麦克风说一个英文短语或句子。 你的语音将传输到语音服务并转录为文本，该文本将显示在窗口中。

    ![语音识别用户界面的屏幕截图](media/sdk/qs-csharp-uwp-11-ui-result.png)

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
在 `quickstart/csharp-uwp` 文件夹中查找此示例。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用适用于 C# 的语音 SDK 从语音中识别意向](how-to-recognize-intents-from-speech-csharp.md)

## <a name="see-also"></a>另请参阅

- [翻译语音](how-to-translate-speech-csharp.md)
- [自定义声学模型](how-to-customize-acoustic-models.md)
- [自定义语言模型](how-to-customize-language-model.md)
