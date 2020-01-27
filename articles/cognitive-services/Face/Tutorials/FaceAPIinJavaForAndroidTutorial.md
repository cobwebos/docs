---
title: 教程：使用 Android SDK 检测和定格图像中的人脸
titleSuffix: Azure Cognitive Services
description: 在本教程中，你将创建一个简单的 Android 应用，它使用人脸服务来检测和定格图像中的人脸。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 8d5bef141f83eedaa996bb63c1fb814aeb6af197
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2020
ms.locfileid: "76165961"
---
# <a name="tutorial-create-an-android-app-to-detect-and-frame-faces-in-an-image"></a>教程：创建一个用于检测和定格图像中人脸的 Android 应用

本教程将创建一个 Android 应用程序，它通过 Java SDK 使用 Azure 人脸服务来检测图像中的人脸。 此应用程序显示一个选定的图像，然后围绕每个检测到的人脸绘制一个框。

本教程演示如何：

> [!div class="checklist"]
> - 创建 Android 应用程序
> - 安装人脸客户端库
> - 使用客户端库检测图像中的人脸
> - 围绕每个检测到的人脸绘制一个框架

![一张照片的 Android 屏幕截图，其中的人脸已使用红色矩形定格](../Images/android_getstarted2.1.PNG)

完整的示例代码在 GitHub 上的[认知服务人脸 Android](https://github.com/Azure-Samples/cognitive-services-face-android-sample) 存储库中提供。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。 

## <a name="prerequisites"></a>必备条件

- 人脸订阅密钥。 可以从[试用认知服务](https://azure.microsoft.com/try/cognitive-services/?api=face-api)获取免费试用的订阅密钥。 或者，按照[创建认知服务帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)中的说明订阅人脸服务并获取密钥。 然后，为密钥和服务终结点字符串[创建环境变量](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)，分别名为 `FACE_SUBSCRIPTION_KEY` 和 `FACE_ENDPOINT`。
- 任何版本的 [Visual Studio 2015 或 2017](https://www.visualstudio.com/downloads/)。
- [Android Studio](https://developer.android.com/studio/)，装有 API 22 或更高级别（这是人脸客户端库需要的）。

## <a name="create-the-android-studio-project"></a>创建 Android Studio 项目

执行以下步骤，以便创建新的 Android 应用程序项目。

1. 在 Android Studio 中，选择“启动新的 Android Studio 项目”。 
1. 在“创建 Android 项目”屏幕上根据需要修改默认字段，然后单击“下一步”。  
1. 在“目标 Android 设备”屏幕上使用下拉列表选择器选择“API 22”或更高版本，然后单击“下一步”。   
1. 选择“空活动”，然后单击“下一步”。  
1. 取消选中“后向兼容性”，然后单击“完成”。  

## <a name="add-the-initial-code"></a>添加初始代码

### <a name="create-the-ui"></a>创建 UI

打开 *activity_main.xml*。 在布局编辑器中，选择“文本”选项卡，然后将其中的内容替换为以下代码。 

[!code-xml[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/res/layout/activity_main.xml?name=snippet_activitymain)]

### <a name="create-the-main-class"></a>创建主类

打开 *MainActivity.java*，将现有 `import` 语句替换为以下代码。

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_imports)]

然后，将 **MainActivity** 类的内容替换为以下代码。 这样会在“按钮”  上设置一个事件处理程序，以便启动一项让用户选择图片的新活动。 它会在 **ImageView** 中显示图片。

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_mainactivity_methods)]

### <a name="try-the-app"></a>试用应用

在 **onActivityResult** 方法中注释掉对 **detectAndFrame** 的调用。 然后按菜单上的“运行”，对应用进行测试。  当应用打开后，在模拟器或连接的设备中单击底部的“浏览”。  此时会显示设备的文件选择对话框。 选择一个图像，验证它是否显示在窗口中。 然后，关闭应用并转到下一步。

![一张包含人脸的照片的 Android 屏幕截图](../Images/android_getstarted1.1.PNG)

## <a name="add-the-face-sdk"></a>添加人脸 SDK

### <a name="add-the-gradle-dependency"></a>添加 Gradle 依赖项

在“项目”窗格中，使用下拉列表选择器选择“Android”。   展开“Gradle 脚本”，  然后打开“build.gradle (模块: 应用)”。  为人脸客户端库 `com.microsoft.projectoxford:face:1.4.3` 添加一个依赖项，如以下屏幕截图所示，然后单击“立即同步”。 

![应用 build.gradle 文件的 Android Studio 屏幕截图](../Images/face-tut-java-gradle.png)

### <a name="add-the-face-related-project-code"></a>添加与人脸相关的项目代码

返回到 **MainActivity.java**，添加以下 `import` 语句：

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_face_imports)]

然后，在 **MainActivity** 类中 **onCreate** 方法上面插入以下代码：

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_mainactivity_fields)]

在“项目”窗格中  展开“应用”，接着展开“清单”   ，然后打开 *AndroidManifest.xml*。 插入以下元素作为 `manifest` 元素的直接子级：

[!code-xml[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/AndroidManifest.xml?name=snippet_manifest_entry)]

## <a name="upload-image-and-detect-faces"></a>上传图像并检测人脸

应用会通过调用 **faceClient.Face.DetectWithStreamAsync** 方法来检测人脸，该方法会包装[检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) REST API 并返回**人脸**实例的列表。

返回的每张**人脸**均包含一个指示其位置的矩形，以及一系列可选的人脸特性。 在本示例中，只请求了人脸矩形。

将以下两个方法插入到 **MainActivity** 类中。 当人脸检测完成后，该应用会调用 **drawFaceRectanglesOnBitmap** 方法修改 **ImageView**。 接下来将定义此方法。

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_detection_methods)]

## <a name="draw-face-rectangles"></a>绘制人脸矩形

将以下帮助程序方法插入到 **MainActivity** 类中。 此方法围绕每个检测到的人脸绘制一个矩形，使用每个**人脸**实例的矩形坐标。

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_drawrectangles)]

最后，在 **onActivityResult** 中取消注释对 **detectAndFrame** 方法的调用。

## <a name="run-the-app"></a>运行应用

运行此应用程序，以浏览方式查找包含人脸的图像。 等待几秒钟，以便人脸服务响应。 此时会在图像中的每个人脸上看到一个红色矩形。

![人脸的 Android 屏幕截图，围绕这些人脸绘制了红色矩形](../Images/android_getstarted2.1.PNG)

## <a name="next-steps"></a>后续步骤

本教程介绍了人脸 Java SDK 的基本使用过程，并创建了一个应用程序来检测并定格图像中的人脸。 接下来，请深入了解人脸检测的详细信息。

> [!div class="nextstepaction"]
> [如何检测图像中的人脸](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
