---
title: 快速入门：识别来自麦克风、Objective-C 的语音 - 语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何在 macOS 上使用语音 SDK 通过 Objective-C 识别语音
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/23/2019
ms.author: chlandsi
ms.openlocfilehash: c2f0fbe66b26c6eca6e0c0b2530efacba9bae958
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75380586"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-macos-by-using-the-speech-sdk"></a>快速入门：在 macOS 上使用语音 SDK 通过 Objective-C 识别语音

针对[语音合成](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/objectivec-macos.md)也提供了快速入门。

本文介绍如何使用 Azure 认知服务语音 SDK 在 Objective-C 中创建一个 macOS 应用，以便将通过麦克风录制的语音转录为文本。

## <a name="prerequisites"></a>必备条件

准备工作：

* 语音服务的[订阅密钥](~/articles/cognitive-services/Speech-Service/get-started.md)。
* 装有 [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) 或更高版本以及 macOS 10.13 或更高版本的 macOS 计算机。

## <a name="get-the-speech-sdk-for-macos"></a>获取适用于 macOS 的语音 SDK

[!INCLUDE [License notice](~/includes/cognitive-services-speech-service-license-notice.md)]

适用于 Mac 的认知服务语音 SDK 目前以框架捆绑包的形式分发。 可在 Xcode 项目将它作为 [CocoaPod](https://cocoapods.org/) 使用，或者从 https://aka.ms/csspeech/macosbinary 下载，然后手动与它建立链接。 本文使用 CocoaPod。

## <a name="create-an-xcode-project"></a>创建 Xcode 项目

启动 Xcode，然后通过选择“文件” > “新建” > “项目”来启动新项目。    在模板选择对话框中，选择“Cocoa 应用”模板。 

在随后的对话框中，进行以下选择。

1. 在“项目选项”  对话框中，执行以下操作：
    1. 为快速入门应用输入一个名称，例如 *helloworld*。
    1. 如果已经有 Apple 开发人员帐户，请输入相应的组织名称和组织标识符。 出于测试目的，请使用 *testorg* 之类的名称。 若要对应用进行签名，需要适当的预配配置文件。 有关详细信息，请参阅 [Apple 开发人员站点](https://developer.apple.com/)。
    1. 确保选择 **Objective-C** 作为项目的语言。
    1. 清除使用情节提要和创建基于文档的应用程序的复选框。 以编程方式创建示例应用的简单 UI。
    1. 清除所有用于测试和核心数据的复选框。

    ![项目设置](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-project-settings.png)

1. 选择项目目录：
    1. 选择用于放置该项目的目录。 此步骤会在主目录中创建一个 helloworld 目录，其中包含 Xcode 项目的所有文件。
    1. 禁止创建适用于此示例项目的 Git 存储库。
1. 设置网络和麦克风访问权限。 选择左侧概述中第一行内的应用名称转到应用配置， 然后选择“功能”选项卡。 
    1. 为该应用启用“应用沙盒”设置。 
    1. 选择“传出连接”和“麦克风”访问权限对应的复选框。  

    ![沙盒设置](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-sandbox.png)

1. 该应用还需要在 `Info.plist` 文件中声明使用麦克风。 选择概述中的文件，然后添加“隐私 - 麦克风使用说明”键，其值类似于“语音识别所需的麦克风”。  

    ![Info.plist 中的设置](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-info-plist.png)

1. 关闭 Xcode 项目。 稍后在设置 CocoaPods 后，将使用该项目的另一个实例。

## <a name="install-the-sdk-as-a-cocoapod"></a>安装用作 CocoaPod 的 SDK

1. 根据[安装说明](https://guides.cocoapods.org/using/getting-started.html)中所述，安装 CocoaPod 依赖项管理器。
1. 转到示例应用所在的目录，即 helloworld。 在该目录中添加一个包含以下内容的名为 *Podfile* 的文本文件：

   [!code-ruby[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/from-microphone/helloworld/Podfile)]
1. 在终端中导航到 helloworld 目录并运行命令 `pod install`。 此命令生成一个 `helloworld.xcworkspace` Xcode 工作区，其中包含示例应用以及用作依赖项的语音 SDK。 在以下步骤中使用此工作区。

## <a name="add-the-sample-code"></a>添加示例代码

1. 在 Xcode 中打开 `helloworld.xcworkspace` 工作区。
1. 将自动生成的 `AppDelegate.m` 文件的内容替换为以下代码：

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/from-microphone/helloworld/helloworld/AppDelegate.m#code)]
1. 将字符串 `YourSubscriptionKey` 替换为你的订阅密钥。
1. 将字符串 `YourServiceRegion` 替换为与订阅关联的[区域](~/articles/cognitive-services/Speech-Service/regions.md)。 例如，将 `westus` 用于免费试用版订阅。

## <a name="build-and-run-the-sample"></a>生成并运行示例

1. 使调试输出可见，方法是：选择“视图”   > “调试区域”   >   “激活控制台”。
1. 在菜单中选择“产品” > “运行”，，以生成并运行示例代码。   也可选择“播放”。 
1. 选择相应按钮并讲几句话后，应会在屏幕下方看到讲出的文本。 首次运行该应用时，系统应会提示是否允许该应用访问计算机的麦克风。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [浏览 GitHub 上的 Objective-C 示例](https://aka.ms/csspeech/samples)
