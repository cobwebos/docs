---
title: 快速入门：使用语音服务 SDK 在 Android 上的 Java 中识别语音
titleSuffix: Azure Cognitive Services
description: 了解如何使用语音服务 SDK 在 Android 上的 Java 中识别语音
services: cognitive-services
author: fmegen
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 10/12/2018
ms.author: wolfma
ms.openlocfilehash: 8c974b3d2a53210b49c3f29a8984038da93dd64c
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/19/2018
ms.locfileid: "49466505"
---
# <a name="quickstart-recognize-speech-in-java-on-android-by-using-the-speech-sdk"></a>快速入门：使用语音 SDK 在 Android 上的 Java 中识别语音

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

本文介绍如何使用认知服务语音 SDK 将语音转为文本，进而创建适用于 Android 的 Java 应用程序。
该应用程序基于Microsoft 认知服务语音 SDK Maven 包（版本 1.0.1）和 Android Studio 3.1。
语音 SDK 目前兼容使用 32 位或 64 位 ARM 处理器的 Android 设备。

> [!NOTE]
> 对于语音设备 SDK 和 Roobo 设备，请参阅[语音设备 SDK](speech-devices-sdk.md)。

## <a name="prerequisites"></a>先决条件

需要具有语音服务订阅密钥才能完成此快速入门。 你可以免费获得一个。 有关详细信息，请参阅[免费试用语音服务](get-started.md)。

## <a name="create-and-configure-a-project"></a>创建并配置项目

1. 启动 Android Studio，然后在“欢迎”窗口中选择“启动新的 Android Studio 项目”。

    ![Android Studio 的“欢迎”窗口的屏幕截图](media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. 此时会显示“新建项目”向导。 在“创建 Android 项目”屏幕中，输入“快速入门”作为应用程序名称，输入“samples.speech.cognitiveservices.microsoft.com”作为公司域，并选择项目目录。 不勾选 C++ 和 Kotlin 复选框，选择“下一步”。

   ![“新建项目”向导的屏幕截图](media/sdk/qs-java-android-02-create-android-project.png)

1. 在“目标 Android 设备”屏幕中，仅选择“手机和平板电脑”。 在它下面的下拉列表中选择“API 23: Android 6.0 (Marshmallow)”，然后选择“下一步”。

   ![“新建项目”向导的屏幕截图](media/sdk/qs-java-android-03-target-android-devices.png)

1. 在“将活动添加到移动设备”屏幕中，选择“空活动”并单击“下一步”。

   ![“新建项目”向导的屏幕截图](media/sdk/qs-java-android-04-add-an-activity-to-mobile.png)

1. 在“配置活动”屏幕中，使用“MainActivity”作为活动名称，并使用“activity\_main”作为布局名称。 选择这两个复选框，然后选择“完成”。

   ![“新建项目”向导的屏幕截图](media/sdk/qs-java-android-05-configure-activity.png)

Android Studio 需要一定的时间来准备你的新 Android 项目。 接下来对项目进行配置，以便了解 Speech SDK 并使用 Java 8。

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

认知服务语音 SDK 的当前版本是 `1.0.1`。

将适用于 Android 的语音 SDK 打包为[ AAR（Android 库）](https://developer.android.com/studio/projects/android-library)，其内附必要的库以及使用它所需的 Android 权限。
它托管在 Maven 存储库 (https://csspeechstorage.blob.core.windows.net/maven/) 中。

设置项目以使用语音 SDK。 打开“项目结构”窗口，方法是从 Android Studio 菜单栏中选择“文件” > “项目结构”。 在“项目结构”窗口中进行以下更改： 

1. 在窗口左侧的列表中，选择“项目”。 编辑“默认库存储库”设置，方法是附加用单引号引起来的逗号和 Maven 存储库 URL。 'https://csspeechstorage.blob.core.windows.net/maven/'

   ![“项目结构”窗口的屏幕截图](media/sdk/qs-java-android-06-add-maven-repository.png)

1. 在同一屏幕的左侧选择“应用”。 然后在窗口顶部选择“依赖项”选项卡。 选择绿色加号 (+)，然后从下拉菜单中选择“库依赖项”。

   ![“项目结构”窗口的屏幕截图](media/sdk/qs-java-android-07-add-module-dependency.png)

1. 在出现的窗口中，输入适用于 Android 的语音 SDK 的名称和版本 (`com.microsoft.cognitiveservices.speech:client-sdk:1.0.1`)。 然后选择“确定”。
   现应将语音 SDK 添加到依赖项列表中，如下所示：

   ![“项目结构”窗口的屏幕截图](media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. 选择“属性”选项卡。“源兼容性”和“目标兼容性”都选择 1.8。

   ![](media/sdk/qs-java-android-09-dependency-added.png)

1. 选择“确定”，关闭“项目结构”窗口并应用对项目所做的更改。

## <a name="create-user-interface"></a>创建用户界面

我们将创建应用程序的基本用户界面。 编辑主活动的布局 `activity_main.xml` 。 一开始的布局包含一个带应用程序名称的标题栏，以及包含“Hello World!”文本的 TextView。

* 单击 TextView 元素。 在右上角将其 ID 属性更改为 `hello`。

* 从 `activity_main.xml` 窗口左上角的调色板中，将“按钮”拖到文本上方的空白区域。

* 在右侧的按钮属性中，在 `onClick` 属性的值中输入 `onSpeechButtonClicked`。 我们将使用此名称编写一个方法来处理按钮事件。  在右上角将其 ID 属性更改为 `button`。

* 若要推断布局约束，请使用设计器顶部的魔术棒图标。

  ![魔术棒图标的屏幕截图](media/sdk/qs-java-android-10-infer-layout-constraints.png)

现在，UI 的文本和图形表示形式应如下所示。

<table>
<tr>
<td valign="top">
![](media/sdk/qs-java-android-11-gui.png)
</td>
<td valign="top">
[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/res/layout/activity_main.xml)]
</td>
</tr>
</table>

## <a name="add-sample-code"></a>添加示例代码

1. 打开源文件 `MainActivity.java`。 将此文件中的所有代码替换为以下内容。

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * `onCreate` 方法包含的代码可请求麦克风和 Internet 权限并初始化本机平台绑定。 只需配置本机平台绑定一次。 这应该尽快在应用程序初始化期间完成。
   
   * 如前所述，`onSpeechButtonClicked` 方法是按钮单击处理程序。 按下按钮会触发语音到文本的听录。

1. 在同一文件中，将字符串 `YourSubscriptionKey` 替换为订阅密钥。

1. 另外，请将字符串 `YourServiceRegion` 替换为与你的订阅关联的[区域](regions.md)（例如，免费试用版订阅的 `westus`）。

## <a name="build-and-run-the-app"></a>生成并运行应用

1. 将 Android 设备连接到开发电脑。 确保已在设备上启用[开发模式和 USB 调试](https://developer.android.com/studio/debug/dev-options)。

1. 若要生成应用程序，请按 Ctrl+F9，或者从菜单栏中选择“生成” > “生成项目”。

1. 若要启动应用程序，请按 Shift+F10 或选择“运行” > “运行‘应用’”。

1. 在出现的部署目标窗口中，选择 Android 设备。

   ![“选择部署目标”窗口的屏幕截图](media/sdk/qs-java-android-12-deploy.png)

按应用程序中的按钮，启动语音识别部分。 随后的 15 秒英语语音将会发送到语音服务进行转录。 结果显示在 Android 应用程序中，以及 Android Studio 的 logcat 窗口中。

![Android 应用程序的屏幕截图](media/sdk/qs-java-android-13-gui-on-device.png)

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
在 `quickstart/java-android` 文件夹中查找此示例。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用适用于 Java 的语音 SDK 从语音中识别意向](how-to-recognize-intents-from-speech-java.md)

## <a name="see-also"></a>另请参阅

- [翻译语音](how-to-translate-speech-csharp.md)
- [自定义声学模型](how-to-customize-acoustic-models.md)
- [自定义语言模型](how-to-customize-language-model.md)
