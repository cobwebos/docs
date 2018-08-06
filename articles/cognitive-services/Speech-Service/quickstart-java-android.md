---
title: 快速入门：使用认知服务语音 SDK 在 Android 上的 Java 中识别语音
titleSuffix: Microsoft Cognitive Services
description: 了解如何使用认知服务语音 SDK 在 Android 上的 Java 中识别语音
services: cognitive-services
author: fmegen
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: fmegen
ms.openlocfilehash: ad1bf15f198529487dbed92d8f8639704b0c5fc7
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2018
ms.locfileid: "39325119"
---
# <a name="quickstart-recognize-speech-in-java-on-android-using-the-speech-sdk"></a>快速入门：使用语音 SDK 在 Android 上的 Java 中识别语音

[!include[Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

本文介绍如何使用认知服务语音 SDK 将语音转为文本，进而创建适用于 Android 的 Java 应用程序。
该应用程序基于Microsoft 认知服务语音 SDK Maven 包（版本 0.5.0）和 Android Studio 3.1。

> [!NOTE]
> 对于语音设备 SDK 和 Roobo 设备，请访问[语音设备 SDK](speech-devices-sdk.md) 页面。

## <a name="prerequisites"></a>先决条件

* 语音服务的订阅密钥。 请参阅[免费试用语音服务](get-started.md)。
* 电脑（Windows、Linux、Mac）支持运行 Android Studio。
* [Android Studio](https://developer.android.com/studio/) 版本 3.1。
* 基于 ARM 的 Android 设备（API 23：Android 6.0 Marshmallow 或更高版本）支持使用工作麦克风[进行开发](https://developer.android.com/studio/debug/dev-options)。

## <a name="create-an-android-studio-project"></a>创建 Android Studio 项目

启动 Android Studio，然后选择“启动新的 Android Studio 项目”。

![](media/sdk/qs-java-android-01-start-new-android-studio-project.png)

在出现的“创建新项目”向导中，进行以下选择：

1. 在“创建 Android 项目”屏幕中，输入“快速入门”作为应用程序名称，输入“samples.speech.cognitiveservices.microsoft.com”作为公司域，并选择项目位置。 不勾选复选框，然后单击“下一步”。

   ![](media/sdk/qs-java-android-02-create-android-project.png)

1. 在“目标 Android 设备”屏幕中，选中“手机和平板电脑”作为唯一选项，从下面的下拉列表中选择“API 23: Android 6.0 (Marshmallow)”，然后单击“下一步”。

   ![](media/sdk/qs-java-android-03-target-android-devices.png)

1. 在“将活动添加到移动设备”屏幕中，选择“空活动”并单击“下一步”。

   ![](media/sdk/qs-java-android-04-add-an-activity-to-mobile.png)

1. 在“配置活动”屏幕中，使用“MainActivity”作为活动名称，并使用“activity\_main”作为布局名称。 选中这两个复选框，然后单击“完成”。

   ![](media/sdk/qs-java-android-05-configure-activity.png)

运行一段时间后，将会出现新创建的 Android Studio 项目。

## <a name="configure-your-project-for-the-speech-sdk"></a>为语音 SDK 配置项目

[!include[License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

认知服务语音 SDK 的当前版本是 `0.5.0`。

将适用于 Android 的语音 SDK 打包为[ AAR（Android 库）](https://developer.android.com/studio/projects/android-library)，其内附必要的库以及使用它所需的 Android 权限。
它托管在 Maven 存储库 (https://csspeechstorage.blob.core.windows.net/maven/) 中。

我们在下面介绍如何设置项目以使用语音 SDK。

在“文件”\>“项目结构”下打开项目结构窗口。
在出现的窗口中进行以下更改（只在完成所有步骤后单击“确定”）：

1. 选择“项目”，然后编辑“默认库存储库”设置，方式是后面附加用单引号引起来的逗号和 Maven 存储库 URL `'https://csspeechstorage.blob.core.windows.net/maven/'`：

  ![](media/sdk/qs-java-android-06-add-maven-repository.png)

1. 仍在此屏幕中，在左侧选择“应用”模块，在顶部选择“依赖项”选项卡。然后，单击右上角的绿色加号，再选择“库依赖项”。

  ![](media/sdk/qs-java-android-07-add-module-dependency.png)

1. 在出现的窗口中，输入适用于 Android 的语音 SDK 的名称和版本 (`com.microsoft.cognitiveservices.speech:client-sdk:0.5.0`)，然后单击“确定”。
   现应将语音 SDK 添加到依赖项列表中，如下所示：

  ![](media/sdk/qs-java-android-08-dependency-added.png)

1. 在顶部，选择“属性”选项卡。“源兼容性”和“目标兼容性”都选择 1.8。

  ![](media/sdk/qs-java-android-09-dependency-added.png)

1. 最后，单击“确定”，以关闭“项目结构”窗口并应用所有更新。

## <a name="create-a-minimal-ui"></a>创建最小化 UI

编辑主活动的布局 `activity_main.xml` 。
默认情况下，显示方式是带应用程序名称的标题栏，还有一个显示“Hello World！”的 TextView。

* 单击 TextView。 在右上角将其 ID 属性更改为 `hello`。

* 从 `activity_main.xml` 窗口左上角的调色板中，将“按钮”拖到文本上方的空白区域。

* 在右侧的按钮属性中，在 `onClick` 属性的值中输入 `onSpeechButtonClicked`，它将用作按钮处理程序的名称。
  在右上角将其 ID 属性更改为 `button`。

* 如果要推断布局约束，请使用设计器顶部的魔术棒图标。

  ![](media/sdk/qs-java-android-10-infer-layout-constraints.png)

现在，UI 的文本和图形版本如下所示：

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

## <a name="add-the-sample-code"></a>添加示例代码

1. 编辑 `MainActivity.java` 源文件，然后将其代码替换为以下内容（在包语句下面）：

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * `onCreate` 方法包含的代码可请求麦克风和 Internet 权限并初始化本机平台绑定。 只需配置本机平台绑定一次，即应尽快在应用程序初始化期间完成。
   
   * 之前已连接 `onSpeechButtonClicked` 方法作为按钮单击处理程序。 按下按钮会触发实际的语音识别。

1. 将字符串 `YourSubscriptionKey` 替换为你的订阅密钥。

1. 将字符串 `YourServiceRegion` 替换为与订阅关联的[区域](regions.md)（例如，免费试用版订阅的 `westus`）。

## <a name="build-and-run-the-sample"></a>生成并运行示例

* 要生成示例，请按 Ctrl+F9，再选择“生成”\>“生成计划”。

* 将 Android 设备连接到开发电脑。 确保已启用[开发模式和 USB 调试](https://developer.android.com/studio/debug/dev-options)。

* 要启动应用，请按 Shift+F10 或选择“运行”\>[运行“应用”]。

* 在出现的部署目标窗口中，选择 Android 设备。

  ![启动应用进行调试](media/sdk/qs-java-android-12-deploy.png)

* 设备上应会启动应用。
  按下按钮后，接下来的 15 秒内将识别并显示在 UI 中（还能在 Android Studio 的 logcat 窗口中看到响应）：

  ![成功识别后的 UI](media/sdk/qs-java-android-13-gui-on-device.png)

Android 快速入门到此屏幕截图时结束。 可从示例存储库下载完整的项目示例代码。

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
在 `quickstart/java-android` 文件夹中查找此示例。

## <a name="next-steps"></a>后续步骤

* [获取我们的示例](speech-sdk.md#get-the-samples)
