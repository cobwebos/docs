---
title: 快速入门：从麦克风中识别语音，C# (Xamarin) - 语音服务
titleSuffix: Azure Cognitive Services
description: 本文介绍如何使用认知服务语音 SDK 创建适用于通用 Windows 平台 (UWP)、Android 和 iOS 的跨平台 C# Xamarin 应用程序。 可以通过设备或模拟器的麦克风实时将语音转录为文本。 该应用程序是使用语音 SDK NuGet 包和 Microsoft Visual Studio 2019 构建的。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: ecfdc74c72284da97ebf3107140911e666a3a12d
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818723"
---
## <a name="prerequisites"></a>先决条件

准备工作：

> [!div class="checklist"]
> * [创建一个 Azure 搜索资源](../../../../get-started.md)
> * [设置开发环境](../../../../quickstarts/setup-platform.md?tabs=xamarin)
> * [创建空示例项目](../../../../quickstarts/create-project.md?tabs=xamarin)
> * 请确保你有权访问麦克风，以便进行音频捕获

如果尚未执行此操作，很好！ 让我们继续。

## <a name="add-sample-code-for-the-common-helloworld-project"></a>添加常用 helloworld 项目的示例代码

常用 helloworld 项目包含跨平台应用程序的独立于平台的实现。 现在，添加定义应用程序用户界面的 XAML 代码，并添加隐藏在实现后面的 C# 代码。

1. 在“解决方案资源管理器”中的常用 helloworld 项目下，打开 `MainPage.xaml`。 

1. 在设计器的 XAML 视图中，将以下 XAML 代码片段插入到 `<StackLayout>` 和 `</StackLayout>` 之间的“Grid”  标记中：

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/MainPage.xaml)]

1. 在“解决方案资源管理器”  中，打开代码隐藏源文件 `MainPage.xaml.cs`。 它分组在 `MainPage.xaml` 下。

1. 将其中的所有代码替换为以下片段：

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/MainPage.xaml.cs)]

1. 在源文件的 `OnRecognitionButtonClicked` 处理程序中，找到字符串 `YourSubscriptionKey` 并将其替换为订阅密钥。


1. 在 `OnRecognitionButtonClicked` 处理程序中，找到字符串 `YourServiceRegion` 并将其替换为与订阅关联的[区域](~/articles/cognitive-services/Speech-Service/regions.md)。 （例如，将 `westus` 用于免费试用版订阅。）

1. 接下来，需要创建一项 [Xamarin 服务](https://docs.microsoft.com/xamarin/android/app-fundamentals/services/creating-a-service/)，用于从不同的平台项目（例如 UWP、Android 和 iOS）查询麦克风权限。 为此，请在 helloworld 项目下添加名为 *Services* 的新文件夹，然后在其下创建新的 C# 源文件。 可以右键单击 *Services* 文件夹，然后选择“添加” > “新建项” > “代码文件”。    将文件重命名为 `IMicrophoneService.cs`，然后将以下代码片段中的所有代码置于该文件中：

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/Services/IMicrophoneService.cs)]

#### <a name="androidtabx-android"></a>[Android](#tab/x-android)
## <a name="add-sample-code-for-the-helloworldandroid-project"></a>添加 `helloworld.Android` 项目的示例代码

现在添加 C# 代码，用于定义应用程序的特定于 Android 的部分。

1. 在“解决方案资源管理器”中的 helloworld.Android 项目下，打开 `MainActivity.cs`。 

1. 将其中的所有代码替换为以下片段：

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.Android/MainActivity.cs)]

1. 接下来，在 helloworld.Android 项目下创建新文件夹 *Services*，为 `MicrophoneService` 添加特定于 Android 的实现。 然后，在该文件夹下创建新的 C# 源文件。 将该文件重命名为 `MicrophoneService.cs`。 将以下代码片段复制并粘贴到该文件中：

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.Android/Services/MicrophoneService.cs)]

1. 然后，打开 *Properties* 文件夹下的 `AndroidManifest.xml`。 在 `<manifest>` 和 `</manifest>` 之间添加麦克风的以下使用权限设置：

   ```xml
   <uses-permission android:name="android.permission.RECORD_AUDIO" />
   ```
   
#### <a name="iostabios"></a>[iOS](#tab/ios)
## <a name="add-sample-code-for-the-helloworldios-project"></a>添加 `helloworld.iOS` 项目的示例代码

现在添加 C# 代码，用于定义应用程序的特定于 iOS 的部分。 另外，在 helloworld.iOS 项目中创建 Apple 设备特定的配置。

1. 在“解决方案资源管理器”中的 helloworld.iOS 项目下，打开 `AppDelegate.cs`。 

1. 将其中的所有代码替换为以下片段：

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.iOS/AppDelegate.cs)]

1. 接下来，在 helloworld.iOS 项目下创建新文件夹 *Services*，为 `MicrophoneService` 添加特定于 iOS 的实现。 然后，在该文件夹下创建新的 C# 源文件。 将该文件重命名为 `MicrophoneService.cs`。 将以下代码片段复制并粘贴到该文件中：

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.iOS/Services/MicrophoneService.cs)]

1. 在文本编辑器中打开 helloworld.iOS 项目下的 `Info.plist`。 将以下键值对添加到 dict 节：

   <key>NSMicrophoneUsageDescription</key>
   <string>本示例应用需要访问麦克风</string>

   > [!NOTE]
   > 如果是针对 iPhone 设备生成，请确保 `Bundle Identifier` 与设备的预配配置文件应用 ID 相匹配。 否则，生成会失败。 如果使用 iPhoneSimulator，可将其保留原样。

1. 如果在 Windows 电脑上生成，则与 Mac 设备建立连接，通过“工具” > “iOS” > “与 Mac 配对”进行生成。    按照 Visual Studio 提供的说明向导的要求操作，与 Mac 设备建立连接。

#### <a name="uwptabhelloworlduwp"></a>[UWP](#tab/helloworlduwp)
## <a name="add-sample-code-for-the-helloworlduwp-project"></a>添加 `helloworld.UWP` 项目的示例代码

## <a name="add-sample-code-for-the-helloworlduwp-project"></a>添加 helloworld.UWP 项目的示例代码

现在添加 C# 代码，用于定义应用程序的特定于 UWP 的部分。

1. 在“解决方案资源管理器”中的 helloworld.UWP 项目下，打开 `MainPage.xaml.cs`。 

1. 将其中的所有代码替换为以下片段：

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.UWP/MainPage.xaml.cs)]

1. 接下来，在 helloworld.UWP 项目下创建新文件夹 *Services*，为 `MicrophoneService` 添加特定于 UWP 的实现。 然后，在该文件夹下创建新的 C# 源文件。 将该文件重命名为 `MicrophoneService.cs`。 将以下代码片段复制并粘贴到该文件中：

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.UWP/Services/MicrophoneService.cs)]

1. 接下来，在 Visual Studio 中双击 helloworld.UWP 项目下的 `Package.appxmanifest` 文件。 确保在“功能”下选中“麦克风”，然后保存文件。  

1. 接下来，在 Visual Studio 中双击 `helloworld.UWP` 项目下的 `Package.appxmanifest` 文件，选中“功能” > “麦克风”，然后保存文件。  
   > 注意：如果看到警告：“证书文件不存在: helloworld.UWP_TemporaryKey.pfx”，请查看[语音转文本](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp)示例来了解详细信息。

1. 在菜单栏中，选择“文件”   > “全部保存”  以保存所做的更改。

## <a name="build-and-run-the-uwp-application"></a>生成并运行 UWP 应用程序

1. 将 helloworld.UWP 设置为启动项目。 右键单击 helloworld.UWP 项目并选择“生成”  以生成应用程序。

1. 选择“调试”   > “开始调试”  （或选择 F5  ）以启动应用程序。 此时将显示“helloworld”  窗口。

   ![C# 中的示例 UWP 语音识别应用程序 - 快速入门](../../../../media/sdk/qs-csharp-xamarin-helloworld-uwp-window.png)

1. 选择“启用麦克风”。  当访问权限请求出现是，请选择“是”。 

   ![麦克风访问权限请求](../../../../media/sdk/qs-csharp-xamarin-uwp-access-prompt.png)

1. 选择“启动语音识别”，然后对着设备的麦克风讲出英文短语或句子。  你的语音将传输到语音服务并转录为文本，该文本将显示在窗口中。

   ![语音识别用户界面](../../../../media/sdk/qs-csharp-xamarin-uwp-ui-result.png)
* * *

## <a name="build-and-run-the-android-and-ios-applications"></a>生成并运行 Android 和 iOS 应用程序

在设备或模拟器中生成并运行 Android 与 iOS 应用程序的过程类似于 UWP 中的操作。 请务必正确安装本文“先决条件”部分要求的所有 SDK。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [footer](./footer.md)]
