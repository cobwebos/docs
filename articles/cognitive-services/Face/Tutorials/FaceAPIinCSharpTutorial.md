---
title: 教程：通过 .NET SDK 检测和显示图像中的人脸数据
titleSuffix: Azure Cognitive Services
description: 本教程将创建一个 Windows 应用，以便使用人脸 API 来检测和定格图像中的人脸。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: a444294497b82f316e7407999f5203cd13878928
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977958"
---
# <a name="tutorial-create-a-windows-presentation-framework-wpf-app-to-display-face-data-in-an-image"></a>教程：创建 Windows Presentation Framework (WPF) 应用以显示图像中的人脸数据

本教程介绍如何通过 .NET 客户端 SDK 使用 Azure 人脸 API 检测图像中的人脸，然后在 UI 中显示该数据。 你将创建一个 WPF 应用程序，用于检测人脸，围绕每张人脸绘制一个框架，并在状态栏中显示人脸描述。 

本教程演示如何：

> [!div class="checklist"]
> - 创建 WPF 应用程序
> - 安装人脸 API 客户端库
> - 使用客户端库检测图像中的人脸
> - 围绕每个检测到的人脸绘制一个框架
> - 在状态栏上显示被框出的人脸的描述

![显示使用矩形将检测到的人脸定格的屏幕截图](../Images/getting-started-cs-detected.png)

完整的示例代码在 GitHub 上的[认知人脸 CSharp 示例](https://github.com/Azure-Samples/Cognitive-Face-CSharp-sample)存储库中提供。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。 


## <a name="prerequisites"></a>先决条件

- 人脸 API 订阅密钥。 可以从[试用认知服务](https://azure.microsoft.com/try/cognitive-services/?api=face-api)获取免费试用的订阅密钥。 或者，按照[创建认知服务帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)中的说明订阅人脸 API 服务并获取密钥。 然后，为密钥和服务终结点字符串[创建环境变量](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)，分别名为 `FACE_SUBSCRIPTION_KEY` 和 `FACE_ENDPOINT`。
- 任何版本的 [Visual Studio 2015 或 2017](https://www.visualstudio.com/downloads/)。

## <a name="create-the-visual-studio-project"></a>创建 Visual Studio 项目

执行以下步骤，以便创建新的 WPF 应用程序项目。

1. 在 Visual Studio 中打开“新建项目”对话框。 展开“已安装”，接着展开“Visual C#”，然后选择“WPF 应用(.NET Framework)”。   
1. 将应用程序命名为“FaceTutorial”，然后单击“确定”   。
1. 获取所需的 NuGet 包。 右键单击解决方案资源管理器中的项目，选择“管理 NuGet 包”，然后找到并安装以下包： 
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.2.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)

## <a name="add-the-initial-code"></a>添加初始代码

在此部分，需添加应用的基本框架，没有特定于人脸的功能。

### <a name="create-the-ui"></a>创建 UI

打开 *MainWindow.xaml*，将其中的内容替换为以下代码&mdash;此代码将创建 UI 窗口。 请注意，`FacePhoto_MouseMove` 和 `BrowseButton_Click` 方法是将在稍后定义的事件处理程序。

[!code-xaml[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml?name=snippet_xaml)]

### <a name="create-the-main-class"></a>创建 main 类

打开 *MainWindow.xaml.cs*，添加客户端库命名空间和其他必需的命名空间。 

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_using)]

接下来，在 **MainWindow** 类中插入以下代码。 此代码将使用订阅密钥和终结点创建 FaceClient  实例。

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mainwindow_fields)]

接下来，添加 MainWindow  构造函数。 它将检查终结点 URL 字符串，然后将其与客户端对象关联。

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mainwindow_constructor)]

最后，向类添加 **BrowseButton_Click** 和 **FacePhoto_MouseMove** 方法。 这两个方法对应于在 *MainWindow.xaml* 中声明的事件处理程序。 **BrowseButton_Click** 方法创建 **OpenFileDialog**，供用户选择 .jpg 图像。 然后，它会在主窗口中显示图像。 将会在后面的步骤中插入 **BrowseButton_Click** 和 **FacePhoto_MouseMove** 的剩余代码。 另请注意 `faceList` 引用&mdash;**DetectedFace** 对象的列表。 此引用是应用存储和调用实际人脸数据的位置。

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_start)]

<!-- [!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_end)] -->

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_start)]

<!-- [!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_end)] -->

### <a name="try-the-app"></a>试用应用

按菜单上的“启动”，对应用进行测试。  应用窗口打开后，单击左下角的“浏览”。  此时会打开“打开的文件”对话框。  从文件系统中选择一个图像，验证它是否显示在窗口中。 然后，关闭应用并转到下一步。

![显示未修改的人脸图像的屏幕截图](../Images/getting-started-cs-ui.png)

## <a name="upload-image-and-detect-faces"></a>上传图像并检测人脸

应用会通过调用 **FaceClient.Face.DetectWithStreamAsync** 方法来检测人脸，该方法可包装用于上传本地图像的[检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) REST API。

在 **MainWindow** 类中 **FacePhoto_MouseMove** 方法下面插入以下方法。 此方法会定义一系列人脸特性，用于检索提交的图像文件并将其读取到**流**中。 然后，它将这两个对象传递到 **DetectWithStreamAsync** 方法调用。

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_uploaddetect)]

## <a name="draw-rectangles-around-faces"></a>在人脸周围绘制矩形

接下来，将添加用于在图像中每个检测到的人脸周围绘制矩形的代码。 在 **MainWindow** 类的 **BrowseButton_Click** 方法末尾的 `FacePhoto.Source = bitmapSource` 行后插入以下代码。 此代码会通过调用 **UploadAndDetectFaces** 填充检测到的人脸的列表。 然后，它会围绕每个人脸绘制一个矩形，并将修改的图像显示在主窗口中。

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_mid)]

## <a name="describe-the-faces"></a>描述人脸

将以下方法添加到 **MainWindow** 类的 **UploadAndDetectFaces** 方法下面。 此方法会将检索到的人脸特性转换成描述人脸的字符串。

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_facedesc)]

## <a name="display-the-face-description"></a>显示人脸描述

将以下代码添加到 **FacePhoto_MouseMove** 方法。 当光标悬停在检测的人脸矩形上时，此事件处理程序会显示 `faceDescriptionStatusBar` 中的人脸描述字符串。

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_mid)]

## <a name="run-the-app"></a>运行应用

运行此应用程序并通过浏览方式查找包含人脸的图像。 等待几秒钟，以便人脸服务响应。 此时会在图像中的每个人脸上看到一个红色矩形。 如果将鼠标移到人脸矩形上，状态栏中会显示该人脸的描述。

![显示使用矩形将检测到的人脸定格的屏幕截图](../Images/getting-started-cs-detected.png)


## <a name="next-steps"></a>后续步骤

本教程介绍了人脸服务 .NET SDK 的基本使用过程，并创建了一个应用程序来检测并定格图像中的人脸。 接下来，请深入了解人脸检测的详细信息。

> [!div class="nextstepaction"]
> [如何检测图像中的人脸](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
