---
title: 快速入门：从麦克风中识别语音，Java (Android) - 语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何在 Android 上使用语音 SDK 通过 Java 识别语音
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/05/2019
ms.author: wolfma
ms.openlocfilehash: 6348d09351cf627624340083e2c419def38dfc01
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2020
ms.locfileid: "77445617"
---
## <a name="prerequisites"></a>必备条件

准备工作：

> [!div class="checklist"]
> * [创建一个 Azure 搜索资源](../../../../get-started.md)
> * [设置开发环境](../../../../quickstarts/setup-platform.md?tabs=android)
> * 请确保你有权访问麦克风，以便进行音频捕获

## <a name="create-a-user-interface"></a>创建用户界面

现在，我们将创建应用程序的基本用户界面。 编辑主活动的布局 `activity_main.xml` 。 一开始的布局包含一个带应用程序名称的标题栏，以及包含“Hello World!”文本的 TextView。

* 选择 TextView 元素。 在右上角将其 ID 属性更改为 `hello`。

* 从 `activity_main.xml` 窗口左上角的调色板中，将按钮拖到文本上方的空白区域。

* 在右侧的按钮属性中，在 `onClick` 属性的值中输入 `onSpeechButtonClicked`。 我们将使用此名称编写一个方法来处理按钮事件。 在右上角将其 ID 属性更改为 `button`。

* 若要推断布局约束，请使用设计器顶部的魔术棒图标。

  ![魔术棒图标的屏幕截图](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-10-infer-layout-constraints.png)

现在，UI 的文本和图形表示形式应如下所示：

![用户界面](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-11-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/from-microphone/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>添加示例代码

1. 打开源文件 `MainActivity.java`。 将此文件中的所有代码替换为以下内容：

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/from-microphone/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * `onCreate` 方法包含的代码可请求麦克风和 Internet 权限并初始化本机平台绑定。 只需配置本机平台绑定一次。 这应该尽快在应用程序初始化期间完成。

   * 如前所述，`onSpeechButtonClicked` 方法是按钮单击处理程序。 按下按钮会触发语音到文本的听录。

1. 在同一文件中，将字符串 `YourSubscriptionKey` 替换为你的订阅密钥。

1. 另外，将字符串 `YourServiceRegion` 替换为与订阅关联的[区域](https://aka.ms/speech/sdkregion)中的“区域标识符”。  例如，将 `westus` 用于免费试用版订阅。

## <a name="build-and-run-the-app"></a>生成并运行应用

1. 将 Android 设备连接到开发电脑。 确保已在设备上启用[开发模式和 USB 调试](https://developer.android.com/studio/debug/dev-options)。

1. 若要生成应用程序，请选择 Ctrl+F9，或者从菜单栏中选择“生成” > “生成项目”   。

1. 若要启动应用程序，请选择 Shift+F10 或选择“运行” > “运行‘应用’”   。

1. 在出现的部署目标窗口中，选择 Android 设备。

   ![“选择部署目标”窗口的屏幕截图](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-12-deploy.png)

选择应用程序中的按钮开始语音识别部分。 随后的 15 秒英语语音将会发送到语音服务进行转录。 结果显示在 Android 应用程序中，以及 Android Studio 的 logcat 窗口中。

![Android 应用程序的屏幕截图](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-13-gui-on-device.png)

## <a name="next-steps"></a>后续步骤

[!INCLUDE [footer](./footer.md)]

