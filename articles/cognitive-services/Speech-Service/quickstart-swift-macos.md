---
title: 快速入门：识别语音，Swift - 语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何在 macOS 上使用语音 SDK 通过 Swift 识别语音
services: cognitive-services
author: cbasoglu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 06/28/2019
ms.author: cbasoglu
ms.openlocfilehash: c1407e8a133013054ef33d4fe55fb8ecb46de654
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035684"
---
# <a name="quickstart-recognize-speech-in-swift-on-macos-using-the-speech-sdk"></a>快速入门：在 macOS 上使用语音 SDK 通过 Swift 识别语音

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

本文介绍如何使用认知服务语音 SDK 在 Swift 中创建一个 macOS 应用，以便将通过麦克风录制的语音转录为文本。

## <a name="prerequisites"></a>先决条件

在开始之前，请满足以下一系列先决条件：

* 语音服务的[订阅密钥](get-started.md)。
* 安装了 [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) 或更高版本以及 [CocoaPods](https://cocoapods.org/) 的 macOS 计算机。

## <a name="get-the-speech-sdk-for-macos"></a>获取适用于 macOS 的语音 SDK

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

认知服务语音 SDK 的当前版本是 `1.6.0`。 请注意，如果没有对 SDK 的任何早期版本进行更改，本教程将无法正常工作。

适用于 macOS 的认知服务语音 SDK 目前以框架捆绑包的形式分发。
可在 Xcode 项目将它作为 [CocoaPod](https://cocoapods.org/) 使用，或者从 https://aka.ms/csspeech/macosbinary 下载，然后手动与它建立链接。 本指南使用 CocoaPod。

## <a name="create-an-xcode-project"></a>创建 Xcode 项目

启动 Xcode，然后通过单击“文件” > “新建” > “项目”来启动新项目。   
在模板选择对话框中，选择“Cocoa 应用”模板。

在随后的对话框中，进行以下选择：

1. 项目选项对话框
    1. 为快速入门应用输入一个名称，例如 `helloworld`。
    1. 如果已经有 Apple 开发人员帐户，请输入相应的组织名称和组织标识符。 可以直接选取任意名称（例如 `testorg`）进行测试。 若要对应用进行签名，需要适当的预配配置文件。 有关详细信息，请参阅 [Apple 开发人员站点](https://developer.apple.com/)。
    1. 确保选择 Swift 作为项目的语言。
    1. 禁用使用情节提要和创建基于文档的应用程序的复选框。 将以编程方式创建示例应用的简单 UI。
    1. 禁用所有用于测试和核心数据的复选框。
1. 选择项目目录
    1. 选择用于放置该项目的目录。 这样会在所选目录中创建一个 `helloworld` 目录，其中包含 Xcode 项目的所有文件。
    1. 禁止创建适用于此示例项目的 Git 存储库。
1. 设置网络和麦克风访问权限。 单击左侧概述中第一行内的应用名称转到应用配置，然后选择“功能”选项卡。
    1. 为该应用启用“应用沙盒”设置。
    1. 启用“传出连接”和“麦克风”访问权限对应的复选框。
    ![沙盒设置](media/sdk/qs-swift-macos-sandbox.png)
1. 该应用还需要在 `Info.plist` 文件中声明使用麦克风。 单击概述中的文件，然后添加“隐私 - 麦克风使用说明”键，其值类似于“语音识别所需的麦克风”。
    ![Info.plist 中的设置](media/sdk/qs-swift-macos-info-plist.png)
1. 关闭 Xcode 项目。 稍后在设置 CocoaPods 后，将使用该项目的另一个实例。

## <a name="add-the-sample-code"></a>添加示例代码

1. 将名为 `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` 的新头文件放置到 helloworld 项目内的 `helloworld` 目录中，并将以下代码粘贴到其中：[!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-macos/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]
1. 在“Objective-C 桥接头文件”  字段![标头属性](media/sdk/qs-swift-macos-bridging-header.png)中，将桥接头文件的相对路径 `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` 添加到 helloworld 目标的 Swift 项目设置中
1. 将自动生成的 `AppDelegate.swift` 文件的内容替换为：[!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-macos/helloworld/helloworld/AppDelegate.swift#code)]
1. 在 `AppDelegate.swift` 中，将字符串 `YourSubscriptionKey` 替换为你的订阅密钥。
1. 将字符串 `YourServiceRegion` 替换为与订阅关联的区域（例如，对于免费试用版订阅，为 `westus`）。

## <a name="install-the-sdk-as-a-cocoapod"></a>安装用作 CocoaPod 的 SDK

1. 根据[安装说明](https://guides.cocoapods.org/using/getting-started.html)中所述，安装 CocoaPod 依赖项管理器。
1. 导航到示例应用所在的目录 (`helloworld`)。 在该目录中添加一个包含以下内容的名为 `Podfile` 的文本文件：[!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-macos/helloworld/Podfile)]
1. 在终端中导航到 `helloworld` 目录并运行命令 `pod install`。 这会生成一个 `helloworld.xcworkspace` Xcode 工作区，其中包含示例应用以及用作依赖项的语音 SDK。 在后续步骤中将使用此工作区。

## <a name="build-and-run-the-sample"></a>生成并运行示例

1. 在 Xcode 中打开 `helloworld.xcworkspace` 工作区。
1. 使调试输出可见（“视图”   > “调试区域”   >   “激活控制台”）。
1. 在菜单中选择“产品” > “运行”，或者单击“播放”按钮，以生成并运行示例代码。   
1. 单击应用中的“识别”按钮并讲几句话后，应会在应用窗口的下部看到讲出的文本。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [浏览 GitHub 上的示例](https://aka.ms/csspeech/samples)

