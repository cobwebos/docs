---
title: 快速入门：识别语音，C# (Xamarin) - 语音服务
titleSuffix: Azure Cognitive Services
description: 本文介绍如何使用认知服务语音 SDK 创建适用于 Windows UWP、Android 和 iOS 的跨平台 C# Xamarin 应用程序。 可以通过设备或模拟器的麦克风实时将语音转录为文本。 该应用程序是使用语音 SDK NuGet 包和 Microsoft Visual Studio 2019 构建的。
services: cognitive-services
author: jhakulin
manager: robch
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: jhakulin
ms.custom: ''
ms.openlocfilehash: c9467bac8b5998252c021faca4eb4177c42a1736
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387389"
---
# <a name="quickstart-recognize-speech-using-cross-platform-xamarin-app-by-using-the-speech-sdk"></a>快速入门：使用语音 SDK 通过跨平台 Xamarin 应用识别语音

我们还提供了有关[语音转文本](quickstart-csharp-uwp.md)、[文本转语音](quickstart-text-to-speech-csharp-uwp.md)和[语音翻译](quickstart-translate-speech-uwp.md)的快速入门。

在本文中，你将使用[语音 SDK](speech-sdk.md) 通过适用于通用 Windows 平台 (UWP)、Android 和 iOS 的 Xamarin 开发跨平台 C# 应用程序。 该程序将通过设备的麦克风实时将语音转录为文本。 该应用程序是使用[语音 SDK NuGet 包](https://aka.ms/csspeech/nuget)和 Microsoft Visual Studio 2019（任何版本）构建的。

## <a name="prerequisites"></a>先决条件

本快速入门需要：

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)。
* 语音服务的 Azure 订阅密钥。 [免费获得一个](get-started.md)。
* 一台装有 Windows 10 Fall Creators Update（10.0；内部版本 16299）或更高版本并配备正常运行的麦克风的 Windows 电脑。
* [Visual Studio 上安装的 Xamarin](https://docs.microsoft.com/xamarin/get-started/installation/?pivots=windows)。
* [Windows 上安装的 Xamarin Android](https://docs.microsoft.com/xamarin/android/get-started/installation/windows)。
* [Windows 上安装的 Xamarin iOS](https://docs.microsoft.com/xamarin/ios/get-started/installation/windows/?pivots=windows)。
* 针对 Android： 
   * 一台 Android 设备（ARM32/64、x86；API 23：Android 6.0 Marshmallow 或更高版本），[可用于开发](https://developer.android.com/studio/debug/dev-options)，并配备正常工作的麦克风。
* 针对 iOS：一台 iOS 设备(ARM64) 或 iOS 模拟器 (x64)，[可用于开发](https://docs.microsoft.com/xamarin/ios/get-started/installation/device-provisioning/)，并配备正常工作的麦克风。
* 为了支持 Windows ARM64 生成，请安装[可选生成工具，以及适用于 ARM/ARM64 的 Windows 10 SDK](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/)。

## <a name="create-a-visual-studio-project"></a>创建 Visual Studio 项目

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-xamarin-create-proj.md)]

## <a name="add-sample-code-for-the-common-helloworld-project"></a>添加通用 `helloworld` 项目的示例代码

通用 `helloworld` 项目包含跨平台应用程序的平台相关实现。
现在，添加定义应用程序用户界面的 XAML 代码，并添加 C# 代码隐藏实现。

1. 在“解决方案资源管理器”中的通用 `helloworld` 项目下，打开 `MainPage.xaml`。 

1. 在设计器的 XAML 视图中，将以下 XAML 代码片段插入到“Grid”  标记中（位于 `<StackLayout>` 和 `</StackLayout>` 之间）：

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/MainPage.xaml)]

1. 在“解决方案资源管理器”  中，打开代码隐藏源文件 `MainPage.xaml.cs`。 （其分组在 `MainPage.xaml` 下。）

1. 将其中的所有代码替换为以下片段：

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/MainPage.xaml.cs)]

1. 在源文件的 `OnRecognitionButtonClicked` 处理程序中，找到字符串 `YourSubscriptionKey` 并将其替换为订阅密钥。

1. 在 `OnRecognitionButtonClicked` 处理程序中，找到字符串 `YourServiceRegion` 并将其替换为与订阅关联的[区域](regions.md)。 （例如，将 `westus` 用于免费试用版订阅。）

1. 接下来，需要创建一个 [Xamarin 服务](https://docs.microsoft.com/xamarin/android/app-fundamentals/services/creating-a-service/)，用于从不同的平台项目（UWP、Android 和 iOS）查询麦克风权限。 为此，请在 `helloworld` 项目下添加新文件夹 `Services`，在该文件夹下创建新的 C# 源文件（右键单击 `Services` 文件夹并选择“添加” > “新建项” > “代码文件”，将该文件重命名为 `IMicrophoneService.cs`，然后将以下代码片段中的所有代码放入该文件：   

[!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/Services/IMicrophoneService.cs)]

## <a name="add-sample-code-for-the-helloworldandroid-project"></a>添加 `helloworld.Android` 项目的示例代码

现在，添加 C# 代码用于定义应用程序的 Android 特定部分。

1. 在“解决方案资源管理器”中的 `helloworld.Android` 项目下，打开 `MainActivity.cs`。 

1. 将其中的所有代码替换为以下片段：

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.Android/MainActivity.cs)]

1. 接下来，在 `helloworld.Android` 项目下创建新文件夹 `Services`，为 `MicrophoneService` 添加 Android 特定的实现。 然后，在该文件夹下创建新的 C# 源文件，将该文件重命名为 `MicrophoneService.cs`，然后将以下代码片段复制并粘贴到该文件中。

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.Android/Services/MicrophoneService.cs)]

1. 然后，打开 `Properties` 文件夹下的 `AndroidManifest.xml`，在 `<manifest>` 与 `</manifest>` 之间添加麦克风的以下使用权限设置。
```xml
   <uses-permission android:name="android.permission.RECORD_AUDIO" />
```

## <a name="add-sample-code-for-the-helloworldios-project"></a>添加 `helloworld.iOS` 项目的示例代码

现在，添加 C# 代码用于定义应用程序的 iOS 特定部分，并在 helloworld.iOS 项目中创建 Apple 设备特定的配置。

1. 在“解决方案资源管理器”中的 `helloworld.iOS` 项目下，打开 `AppDelegate.cs`。 

1. 将其中的所有代码替换为以下片段：

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.iOS/AppDelegate.cs)]

1. 接下来，在 `helloworld.iOS` 项目下创建新文件夹 `Services`，为 `MicrophoneService` 添加 iOS 特定的实现。 然后，在该文件夹下创建新的 C# 源文件，将该文件重命名为 `MicrophoneService.cs`，然后将以下代码片段复制并粘贴到该文件中。

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.iOS/Services/MicrophoneService.cs)]

1. 在文本编辑器中打开 `helloworld.iOS` 项目下的 info.plist，在 dict 节 <key>NSMicrophoneUsageDescription</key> 下添加以下键值对
   <string>本示例应用需要访问麦克风</string>
   > 注意：如果目标是针对 iPhone 设备生成，请确保 `Bundle Identifier` 与设备的预配配置文件应用 ID 相匹配，否则生成将会失败。 如果使用 iPhoneSimulator，可将此标识符保留原样。

1. 如果在 Windows 电脑上生成，则需要与 Mac 设备建立连接，以通过“工具” > “iOS” > “与 Mac 配对”来进行生成。    按照 Visual Studio 向导中提供的说明来与 Mac 设备建立连接。

## <a name="add-sample-code-for-the-helloworlduwp-project"></a>添加 `helloworld.UWP` 项目的示例代码

现在，添加 C# 代码用于定义应用程序的 UWP 特定部分。

1. 在“解决方案资源管理器”中的 `helloworld.UWP` 项目下，打开 `MainPage.xaml.cs`。 

1. 将其中的所有代码替换为以下片段：

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.UWP/MainPage.xaml.cs)]

1. 接下来，在 `helloworld.UWP` 项目下创建新文件夹 `Services`，为 `MicrophoneService` 添加 UWP 特定的实现。 然后，在该文件夹下创建新的 C# 源文件，将该文件重命名为 `MicrophoneService.cs`，然后将以下代码片段复制并粘贴到该文件中。

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.UWP/Services/MicrophoneService.cs)]

1. 接下来，在 Visual Studio 中双击 `helloworld.UWP` 项目下的 `Package.appxmanifest` 文件，选中“功能” > “麦克风”，然后保存文件。  
   > 注意：如果看到警告：“证书文件不存在: helloworld.UWP_TemporaryKey.pfx”，请查看[语音转文本](quickstart-csharp-uwp.md)示例来了解详细信息。

1. 在菜单栏中，选择“文件”   > “全部保存”  以保存所做的更改。

## <a name="build-and-run-the-uwp-application"></a>生成并运行 UWP 应用程序

1. 将 `helloworld.UWP` 设置为启动项目，在 `helloworld.UWP` 项目上单击鼠标右键，然后选择“生成”以生成应用程序。  

1. 选择“调试”   > “开始调试”  （或按 F5  ）以启动应用程序。 此时将显示“helloworld”  窗口。

   ![C# 中的示例 UWP 语音识别应用程序 - 快速入门](media/sdk/qs-csharp-xamarin-helloworld-uwp-window.png)

1. 选择“启用麦克风”  ，并在弹出访问权限请求时选择“是”  。

   ![麦克风访问权限请求](media/sdk/qs-csharp-xamarin-uwp-access-prompt.png)

1. 选择“启动语音识别”，然后对着设备的麦克风讲出英文短语或句子。  你的语音将传输到语音服务并转录为文本，该文本将显示在窗口中。

   ![语音识别用户界面](media/sdk/qs-csharp-xamarin-uwp-ui-result.png)

## <a name="build-and-run-the-android-and-ios-applications"></a>生成并运行 Android 和 iOS 应用程序

在设备或模拟器中生成和运行 Android 与 iOS 应用程序的过程类似于 UWP 中的操作。 请务必正确安装本文档的 `Prerequisites` 部分规定的所有 SDK。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [浏览 GitHub 上的 C# 示例](https://aka.ms/csspeech/samples)
