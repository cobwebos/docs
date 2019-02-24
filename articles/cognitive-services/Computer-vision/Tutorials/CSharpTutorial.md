---
title: 示例：使用 C# 探索图像处理应用
titleSuffix: Computer Vision - Cognitive Services - Azure
description: 介绍一款使用 Microsoft 认知服务中的计算机视觉 API 的基本 Windows 应用。 执行 OCR、创建缩略图，并处理图像中的视觉特征。
services: cognitive-services
author: PatrickFarley
manager: nolachar
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: a23e45cb1e75305f41c81077c90282352327b72d
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2019
ms.locfileid: "56310932"
---
# <a name="sample-explore-an-image-processing-app-with-c"></a>示例：使用 C# 探索图像处理应用

探索一款基本 Windows 应用程序，该应用程序使用计算机视觉执行光学字符识别 (OCR)、创建智能裁剪缩略图，并对图像中的视觉特征（包括人脸）进行检测、分类、标记和描述。 通过以下示例可提交图像 URL 或存储于本地的文件。 可将此开放源代码示例作为模板，使用计算机视觉 API 和 .NET Framework 中的 Windows Presentation Foundation (WPF) 构建自己的 Windows 应用程序。

> [!div class="checklist"]
> * 从 GitHub 获取示例应用
> * 在 Visual Studio 中打开并构建示例应用
> * 运行示例应用并与其交互以执行各种场景
> * 探索示例应用附带的各种场景

## <a name="prerequisites"></a>先决条件

在探索示例应用之前，请确保已满足以下先决条件：

* 必须具有 [Visual Studio 2015](https://visualstudio.microsoft.com/downloads/) 或更高版本。
* 必须具有计算机视觉的订阅密钥。 要获取订阅密钥，请参阅[获取订阅密钥](../Vision-API-How-to-Topics/HowToSubscribe.md)。

## <a name="get-the-sample-app"></a>获取示例应用

计算机视觉示例应用已在 `Microsoft/Cognitive-Vision-Windows` 存储库的 GitHub 上发布。 此存储库还包含 `Microsoft/Cognitive-Common-Windows` 存储库作为 Git 子模块。 可以通过使用命令行中的 `git clone --recurse-submodules` 命令或使用 GitHub 桌面以递归方式克隆此存储库（包括子模块）。

例如，若要从命令提示符以递归方式克隆计算机视觉示例应用的存储库，请运行以下命令：

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Vision-Windows.git
```

> [!IMPORTANT]
> 请勿将此存储库下载为 Zip 文件。 若将存储库下载为 Zip 文件，则 Git 不包含子模块。

### <a name="get-optional-sample-images"></a>获取可选的示例图像

可以选择使用[人脸](../../Face/Overview.md)示例应用中包含的示例图像，该应用已在 `Microsoft/Cognitive-Face-Windows` 存储库的 GitHub 上发布。 该示例应用包含一个文件夹 `/Data`，其中包含多个人物图像。 也可以通过描述用于计算机视觉示例应用的方法以递归方式克隆此存储库。

例如，要从命令提示符递归克隆人脸示例应用的存储库，请运行以下命令：

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Face-Windows.git
```

## <a name="open-and-build-the-sample-app-in-visual-studio"></a>在 Visual Studio 中打开并构建示例应用

首先必须构建示例应用，以让 Visual Studio 解析依赖关系，然后才能运行或浏览示例应用。 要打开并构建示例应用，请执行以下步骤：

1. 在 Visual Studio 中打开 Visual Studio 解决方案文件 `/Sample-WPF/VisionAPI-WPF-Samples.sln`。
1. 确保 Visual Studio 解决方案包含两个项目：  

   * SampleUserControlLibrary
   * VisionAPI-WPF-Samples  

   如果没有 SampleUserControlLibrary 项目，请确认已经以递归方式克隆 `Microsoft/Cognitive-Vision-Windows` 存储库。
1. 在 Visual Studio 中，按 Ctrl+Shift+B 或从功能区菜单中选择“构建”，然后选择“构建解决方案”以构建该解决方案。

## <a name="run-and-interact-with-the-sample-app"></a>运行示例应用并与之交互

可以运行示例应用，以便在执行各种任务（例如生成缩略图或标记图像）时查看它与你以及与计算机视觉客户端库的交互方式。 要运行示例应用并与之交互，请执行以下步骤：

1. 构建完成后，按“F5”或从功能区菜单中选择“调试”，然后选择“开始调试”以运行示例应用。
1. 显示示例应用时，从导航窗格中选择“订阅密钥管理”以显示“订阅密钥管理”页面。
   ![订阅密钥管理页面](../Images/Vision_UI_Subscription.PNG)  
1. 在“订阅密钥”中输入订阅密钥。
1. 在“终结点”中输入订阅密钥的计算机视觉资源的终结点 URL（省略 `/vision/v1.0`）。  
   例如，如果你使用的是计算机视觉免费试用版中的订阅密钥，请输入以下美国中西部 Azure 区域的终结点 URL：`https://westcentralus.api.cognitive.microsoft.com`
1. 如果不想在下次运行该示例应用时输入订阅密钥和终结点 URL，请选择“保存设置”以将订阅密钥和终结点 URL 保存到计算机。 如果要删除以前保存的订阅密钥和终结点 URL，请选择“删除设置”。

   > [!NOTE]
   > 示例应用使用独立存储和 `System.IO.IsolatedStorage` 来存储订阅密钥和终结点 URL。

1. 在导航窗格中的“选择场景”处，选择示例应用当前包含的场景之一：  

   | 场景 | 说明 |
   |----------|-------------|
   |分析图像 | 使用[分析图像](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)操作来分析本地或远程图像。 可以选择视觉特征和语言进行分析，并查看图像和结果。  |
   |使用域模型分析图像 | 使用[列出域特定模型](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fd)操作列出可供选择的域模型，并使用[识别域特定内容](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200)操作使用所选域模型分析本地或远程图像。 此外还可以选择分析的语言。 |
   |描述图像 | 使用[描述图像](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fe)操作创建本地或远程图像的用户可读描述。 此外还可以选择描述的语言。 |
   |生成标记 | 使用[标记图像](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1ff)操作标记本地或远程图像的视觉特征。 此外还可以选择标记的语言。 |
   |识别文本 (OCR) | 使用 [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) 操作识别图像并从图像中提取打印的文本。 可以选择要使用的语言，也可以让计算机视觉自动检测语言。 |
   |识别文本 V2（英语） | 使用[识别文本](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200)和[获取识别文本操作结果](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2cf1154055056008f201)操作以异步方式识别图像和提取图像中的打印文本或手写文本。 |
   |获取缩略图 | 使用[获取缩略图](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb)操作生成本地或远程图像的缩略图。 |

   以下屏幕截图展示了在分析样本图像后为分析图像场景提供的页面。
   ![“分析图像”页的屏幕截图](../Images/Analyze_Image_Example.PNG)

## <a name="explore-the-sample-app"></a>探索示例应用

计算机视觉示例应用的 Visual Studio 解决方案包含两个项目：

* SampleUserControlLibrary  
  SampleUserControlLibrary 项目提供多项认知服务样本共享的功能。 该项目包含以下内容：
  * SampleScenarios  
    UserControl 为样本提供标准化的表示方式，例如标题栏、导航窗格和内容窗格。 计算机视觉示例应用在 MainWindow.xaml 窗口中使用此控件来显示场景页面并访问多个场景共享的信息，例如订阅密钥和终结点 URL。
  * SubscriptionKeyPage  
    是一个提供标准化布局的页面，用于输入示例应用的订阅密钥和终结点 URL。 计算机视觉示例应用使用此页面来管理场景页面使用的订阅密钥和终结点 URL。
  * VideoResultControl  
    UserControl 提供视频信息的标准化表示方式。 计算机视觉示例应用不使用此控件。
* VisionAPI-WPF-Samples  
  是计算机视觉示例应用的主项目，该项目包含计算机视觉全部有意思的功能。 该项目包含以下内容：
  * AnalyzeInDomainPage.xaml  
    分析图像与域模型场景的场景页面。
  * AnalyzeImage.xaml  
    分析图像场景的场景页面。
  * DescribePage.xaml  
    描述图像场景的场景页面。
  * ImageScenarioPage.cs  
    从其中派生示例应用的所有场景页面的 ImageScenarioPage 类。 此类管理所有场景页面的共享功能，例如提供凭据以及输出的格式设置功能。
  * MainWindow.xaml  
    是示例应用的主窗口，它使用 SampleScenarios 控件来显示 SubscriptionKeyPage 和场景页面。
  * OCRPage.xaml  
    识别文本 (OCR) 场景的场景页面。
  * RecognizeLanguage.cs  
    RecognizeLanguage 类：提供有关示例应用中的各种方法支持的语言的信息。
  * TagsPage.xaml  
    生成标记场景的场景页面。
  * TextRecognitionPage.xaml  
    识别文本 V2（英语）场景的场景页面。
  * ThumbnailPage.xaml  
    获取缩略图场景的场景页面。

### <a name="explore-the-sample-code"></a>探索示例代码

示例代码的关键部分包含以 `KEY SAMPLE CODE STARTS HERE` 开头并以 `KEY SAMPLE CODE ENDS HERE` 结尾的注释块，以便更轻松地浏览示例应用。 示例代码的关键部分包含与学习如何使用计算机视觉 API 客户端库执行各种任务最相关的代码。 可以在 Visual Studio 中搜索 `KEY SAMPLE CODE STARTS HERE`，以便在计算机视觉示例应用中最相关的代码段之间移动。 

例如，下面显示并包含在 AnalyzePage.xaml 中的 `UploadAndAnalyzeImageAsync` 方法演示了如何使用客户端库通过调用 `ComputerVisionClient.AnalyzeImageInStreamAsync` 方法来分析本地图像。

```csharp
private async Task<ImageAnalysis> UploadAndAnalyzeImageAsync(string imageFilePath)
{
    // -----------------------------------------------------------------------
    // KEY SAMPLE CODE STARTS HERE
    // -----------------------------------------------------------------------

    //
    // Create Cognitive Services Vision API Service client.
    //
    using (var client = new ComputerVisionClient(Credentials) { Endpoint = Endpoint })
    {
        Log("ComputerVisionClient is created");

        using (Stream imageFileStream = File.OpenRead(imageFilePath))
        {
            //
            // Analyze the image for all visual features.
            //
            Log("Calling ComputerVisionClient.AnalyzeImageInStreamAsync()...");
            VisualFeatureTypes[] visualFeatures = GetSelectedVisualFeatures();
            string language = (_language.SelectedItem as RecognizeLanguage).ShortCode;
            ImageAnalysis analysisResult = await client.AnalyzeImageInStreamAsync(imageFileStream, visualFeatures, null, language);
            return analysisResult;
        }
    }

    // -----------------------------------------------------------------------
    // KEY SAMPLE CODE ENDS HERE
    // -----------------------------------------------------------------------
}
```

### <a name="explore-the-client-library"></a>探索客户端库

此示例应用使用计算机视觉 API 客户端库，这是 Azure 认知服务中的计算机视觉 API 的精简 C# 客户端包装器。 可从 NuGet 的 [Microsoft.Azure.CognitiveServices.Vision.ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) 包中获得客户端库。 在构建 Visual Studio 应用示例时，会从相应的 NuGet 包中检索了客户端库。 还可以在 `Microsoft/Cognitive-Vision-Windows` 存储库的 `/ClientLibrary` 文件夹中查看客户端库的源代码。

客户端库的功能以 `Microsoft.Azure.CognitiveServices.Vision.ComputerVision` 命名空间中的 `ComputerVisionClient` 类为中心，而 `ComputerVisionClient` 类与计算机视觉交互时使用的模型位于 `Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models` 命名空间中。 在示例应用附带的各种 XAML 场景页面中，将找到以下针对这些命名空间的 `using` 指令：

```csharp
// -----------------------------------------------------------------------
// KEY SAMPLE CODE STARTS HERE
// Use the following namespace for ComputerVisionClient.
// -----------------------------------------------------------------------
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;
// -----------------------------------------------------------------------
// KEY SAMPLE CODE ENDS HERE
// -----------------------------------------------------------------------
```

在探索计算机视觉示例应用附带场景时，将详细了解 `ComputerVisionClient` 类中包含的各种方法。

## <a name="explore-the-analyze-image-scenario"></a>探索场景：分析图像

此场景由 AnalyzePage.xaml 页面管理。 可以选择视觉特征和语言进行分析，并查看图像和结果。 场景页面通过使用以下方法之一来执行此操作，具体取决于图像的来源：

* UploadAndAnalyzeImageAsync  
  此方法用于本地图像，其中图像必须编码为 `Stream` 并通过调用 `ComputerVisionClient.AnalyzeImageInStreamAsync` 方法将其发送到计算机视觉。
* AnalyzeUrlAsync  
  此方法用于远程图像，通过调用 `ComputerVisionClient.AnalyzeImageAsync` 方法将图像的 URL 发送到计算机视觉。

`UploadAndAnalyzeImageAsync` 方法使用指定的订阅密钥和终结点 URL 创建新的 `ComputerVisionClient` 实例。 因为示例应用要分析本地图像，所以必须将该图像的内容发送到计算机视觉。 它会打开 `imageFilePath` 中指定的本地文件，以 `Stream` 格式读取，然后获取在场景页面中选择的视觉特征和语言。 它将调用 `ComputerVisionClient.AnalyzeImageInStreamAsync` 方法，传递文件的 `Stream`、视觉特征和语言，然后将结果作为 `ImageAnalysis` 实例返回。 从 `ImageScenarioPage` 类继承的方法会在场景页面中显示返回的结果。

`AnalyzeUrlAsync` 方法使用指定的订阅密钥和终结点 URL 创建新的 `ComputerVisionClient` 实例。 它获取场景页面中选择的视觉特征和语言。 它将调用 `ComputerVisionClient.AnalyzeImageInStreamAsync` 方法，传递图像 URL、视觉特征和语言，然后将结果作为 `ImageAnalysis` 实例返回。 从 `ImageScenarioPage` 类继承的方法会在场景页面中显示返回的结果。

## <a name="explore-the-analyze-image-with-domain-model-scenario"></a>探索场景：使用域模型分析图像

此场景由 AnalyzeInDomainPage.xaml 页面管理。 可以选择域模型（例如 `celebrities` 或 `landmarks`）和语言来执行特定于域的图像分析，并查看图像和结果。 该场景页面使用以下方法，具体取决于图像的来源：

* GetAvailableDomainModelsAsync  
  此方法从计算机视觉获取可用的域模型列表，并使用 `ComputerVisionClient.ListModelsAsync` 方法填充页面上的 `_domainModelComboBox` ComboBox 控件。
* UploadAndAnalyzeInDomainImageAsync  
  此方法用于本地图像，其中图像必须编码为 `Stream` 并通过调用 `ComputerVisionClient.AnalyzeImageByDomainInStreamAsync` 方法将其发送到计算机视觉。
* AnalyzeInDomainUrlAsync  
  此方法用于远程图像，通过调用 `ComputerVisionClient.AnalyzeImageByDomainAsync` 方法将图像的 URL 发送到计算机视觉。

`UploadAndAnalyzeInDomainImageAsync` 方法使用指定的订阅密钥和终结点 URL 创建新的 `ComputerVisionClient` 实例。 因为示例应用要分析本地图像，所以必须将该图像的内容发送到计算机视觉。 它会打开 `imageFilePath` 中指定的本地文件，以 `Stream` 格式读取，然后获取在场景页面中选择的语言。 它将调用 `ComputerVisionClient.AnalyzeImageByDomainInStreamAsync` 方法，传递文件的 `Stream`、域模型的名称和语言，然后将结果作为 `DomainModelResults` 实例返回。 从 `ImageScenarioPage` 类继承的方法会在场景页面中显示返回的结果。

`AnalyzeInDomainUrlAsync` 方法使用指定的订阅密钥和终结点 URL 创建新的 `ComputerVisionClient` 实例。 它获取场景页面中选择的语言。 它将调用 `ComputerVisionClient.AnalyzeImageByDomainAsync` 方法，传递图像 URL、视觉特征和语言，然后将结果作为 `DomainModelResults` 实例返回。 从 `ImageScenarioPage` 类继承的方法会在场景页面中显示返回的结果。

## <a name="explore-the-describe-image-scenario"></a>探索场景：描述图像

此场景由 DescribePage.xaml 页面管理。 可以选择一种语言来创建图像的用户可读的描述，并查看图像和结果。 该场景页面使用以下方法，具体取决于图像的来源：

* UploadAndDescribeImageAsync  
  此方法用于本地图像，其中图像必须编码为 `Stream` 并通过调用 `ComputerVisionClient.DescribeImageInStreamAsync` 方法将其发送到计算机视觉。
* DescribeUrlAsync  
  此方法用于远程图像，通过调用 `ComputerVisionClient.DescribeImageAsync` 方法将图像的 URL 发送到计算机视觉。

`UploadAndDescribeImageAsync` 方法使用指定的订阅密钥和终结点 URL 创建新的 `ComputerVisionClient` 实例。 因为示例应用要分析本地图像，所以必须将该图像的内容发送到计算机视觉。 它会打开 `imageFilePath` 中指定的本地文件，以 `Stream` 格式读取，然后获取在场景页面中选择的语言。 它将调用 `ComputerVisionClient.DescribeImageInStreamAsync` 方法，传递文件的 `Stream`、候选项的最大数量（在本例中为 3）和语言，然后将结果作为 `ImageDescription` 实例返回。 从 `ImageScenarioPage` 类继承的方法会在场景页面中显示返回的结果。

`DescribeUrlAsync` 方法使用指定的订阅密钥和终结点 URL 创建新的 `ComputerVisionClient` 实例。 它获取场景页面中选择的语言。 它将调用 `ComputerVisionClient.DescribeImageAsync` 方法，传递图像 URL、候选项的最大数量（在本例中为 3）和语言，然后将结果作为 `ImageDescription` 实例返回。 从 `ImageScenarioPage` 类继承的方法会在场景页面中显示返回的结果。

## <a name="explore-the-generate-tags-scenario"></a>探索场景：生成标记

此场景由 TagsPage.xaml 页面管理。 可以选择一种语言来标记图像的视觉特征，并查看图像和结果。 该场景页面使用以下方法，具体取决于图像的来源：

* UploadAndGetTagsForImageAsync  
  此方法用于本地图像，其中图像必须编码为 `Stream` 并通过调用 `ComputerVisionClient.TagImageInStreamAsync` 方法将其发送到计算机视觉。
* GenerateTagsForUrlAsync  
  此方法用于远程图像，通过调用 `ComputerVisionClient.TagImageAsync` 方法将图像的 URL 发送到计算机视觉。

`UploadAndGetTagsForImageAsync` 方法使用指定的订阅密钥和终结点 URL 创建新的 `ComputerVisionClient` 实例。 因为示例应用要分析本地图像，所以必须将该图像的内容发送到计算机视觉。 它会打开 `imageFilePath` 中指定的本地文件，以 `Stream` 格式读取，然后获取在场景页面中选择的语言。 它将调用 `ComputerVisionClient.TagImageInStreamAsync` 方法，为传递文件的 `Stream` 和语言，然后将结果作为 `TagResult` 实例返回。 从 `ImageScenarioPage` 类继承的方法会在场景页面中显示返回的结果。

`GenerateTagsForUrlAsync` 方法使用指定的订阅密钥和终结点 URL 创建新的 `ComputerVisionClient` 实例。 它获取场景页面中选择的语言。 它调用 `ComputerVisionClient.TagImageAsync` 方法，传递图像 URL 和语言，然后将结果作为 `TagResult` 实例返回。 从 `ImageScenarioPage` 类继承的方法会在场景页面中显示返回的结果。

## <a name="explore-the-recognize-text-ocr-scenario"></a>探索场景：识别文本 (OCR)

此场景由 OCRPage.xaml 页面管理。 可以选择一种语言来识别和提取图像中的打印文本，并查看图像和结果。 该场景页面使用以下方法，具体取决于图像的来源：

* UploadAndRecognizeImageAsync  
  此方法用于本地图像，其中图像必须编码为 `Stream` 并通过调用 `ComputerVisionClient.RecognizePrintedTextInStreamAsync` 方法将其发送到计算机视觉。
* RecognizeUrlAsync  
  此方法用于远程图像，通过调用 `ComputerVisionClient.RecognizePrintedTextAsync` 方法将图像的 URL 发送到计算机视觉。

`UploadAndRecognizeImageAsync` 方法使用指定的订阅密钥和终结点 URL 创建新的 `ComputerVisionClient` 实例。 因为示例应用要分析本地图像，所以必须将该图像的内容发送到计算机视觉。 它会打开 `imageFilePath` 中指定的本地文件，以 `Stream` 格式读取，然后获取在场景页面中选择的语言。 它将调用 `ComputerVisionClient.RecognizePrintedTextInStreamAsync` 方法，指示未检测到方向并传递文件的 `Stream` 和语言，然后将结果作为 `OcrResult` 实例返回。 从 `ImageScenarioPage` 类继承的方法会在场景页面中显示返回的结果。

`RecognizeUrlAsync` 方法使用指定的订阅密钥和终结点 URL 创建新的 `ComputerVisionClient` 实例。 它获取场景页面中选择的语言。 它将调用 `ComputerVisionClient.RecognizePrintedTextAsync` 方法，指示未检测到方向并传递图像 URL 和语言，然后将结果作为 `OcrResult` 实例返回。 从 `ImageScenarioPage` 类继承的方法会在场景页面中显示返回的结果。

## <a name="explore-the-recognize-text-v2-english-scenario"></a>探索场景：识别文本 V2（英语）

此场景由 TextRecognitionPage.xaml 页面管理。 可以选择识别模式和语言，以异步方式识别和提取图像中的打印或手写文本，并查看图像和结果。 该场景页面使用以下方法，具体取决于图像的来源：

* UploadAndRecognizeImageAsync  
  此方法用于本地图像，其中图像必须编码为 `Stream`，通过调用 `RecognizeAsync` 方法并传递 `ComputerVisionClient.RecognizeTextInStreamAsync` 方法的参数化委托将其发送至计算机视觉。
* RecognizeUrlAsync  
  此方法用于远程图像，通过调用 `RecognizeAsync` 方法并传递 `ComputerVisionClient.RecognizeTextAsync` 方法的参数化委托将图像的 URL 发送到计算机视觉。
* RecognizeAsync 方法处理 `UploadAndRecognizeImageAsync` 和 `RecognizeUrlAsync` 方法的异步调用，并通过调用 `ComputerVisionClient.GetTextOperationResultAsync` 方法轮询结果。

与计算机视觉示例应用中包含的其他场景不同，此场景是异步的，它会调用一个方法来启动该进程，然后调用另一个方法来检查状态并返回该进程的结果。 此场景中的逻辑流与其他场景中的逻辑流略有不同。

`UploadAndRecognizeImageAsync` 方法打开 `imageFilePath` 中指定的本地文件，以 `Stream` 格式读取，然后调用 `RecognizeAsync` 方法，传递以下内容：

* `ComputerVisionClient.RecognizeTextInStreamAsync` 方法的参数化异步委托的 Lambda 表达式，其中将文件的 `Stream` 和识别模式作为 `GetHeadersAsyncFunc` 中的参数。
* 委托的 Lambda表达式，用于获取 `GetOperationUrlFunc` 中的 `Operation-Location` 响应标头值。

`RecognizeUrlAsync` 方法调用 `RecognizeAsync` 方法，传递以下内容：

* `ComputerVisionClient.RecognizeTextAsync` 方法的参数化异步委托的 Lambda 表达式，其中将远程文件的 URL 和识别模式作为 `GetHeadersAsyncFunc` 中的参数。
* 委托的 Lambda表达式，用于获取 `GetOperationUrlFunc` 中的 `Operation-Location` 响应标头值。

完成 `RecognizeAsync` 方法后，`UploadAndRecognizeImageAsync` 和 `RecognizeUrlAsync` 方法都将结果作为 `TextOperationResult` 实例返回。 从 `ImageScenarioPage` 类继承的方法会在场景页面中显示返回的结果。

`RecognizeAsync` 方法为 `GetHeadersAsyncFunc` 中传递的 `ComputerVisionClient.RecognizeTextInStreamAsync` 或 `ComputerVisionClient.RecognizeTextAsync` 方法调用参数化委托，并等待响应。 该方法随后调用 `GetOperationUrlFunc` 中传递的委托，以从响应中获取 `Operation-Location` 响应标头值。 此值是一个 URL，用于从计算机视觉检索 `GetHeadersAsyncFunc` 中传递的方法的结果。

然后，`RecognizeAsync` 方法调用 `ComputerVisionClient.GetTextOperationResultAsync` 方法，传递从 `Operation-Location` 响应标头中检索到的 URL，以获取在 `GetHeadersAsyncFunc` 中传递的方法的状态和结果。 如果状态未指示方法是否已成功完成，则 `RecognizeAsync` 方法将再调用 `ComputerVisionClient.GetTextOperationResultAsync` 3 次，每次调用之间会等待 3 秒。 `RecognizeAsync` 方法将结果返回给调用它的方法。

## <a name="explore-the-get-thumbnail-scenario"></a>探索场景：获取缩略图

此场景由 ThumbnailPage.xaml 页面管理。 可以指示是否使用智能裁剪，并指定所需的高度和宽度，从图像生成缩略图，并查看图像和结果。 该场景页面使用以下方法，具体取决于图像的来源：

* UploadAndThumbnailImageAsync  
  此方法用于本地图像，其中图像必须编码为 `Stream` 并通过调用 `ComputerVisionClient.GenerateThumbnailInStreamAsync` 方法将其发送到计算机视觉。
* ThumbnailUrlAsync  
  此方法用于远程图像，通过调用 `ComputerVisionClient.GenerateThumbnailAsync` 方法将图像的 URL 发送到计算机视觉。

`UploadAndThumbnailImageAsync` 方法使用指定的订阅密钥和终结点 URL 创建新的 `ComputerVisionClient` 实例。 因为示例应用要分析本地图像，所以必须将该图像的内容发送到计算机视觉。 它将打开 `imageFilePath` 中指定的本地文件，以 `Stream` 格式读取。 它将调用 `ComputerVisionClient.GenerateThumbnailInStreamAsync` 方法，传递宽度、高度、文件的 `Stream` 以及是否使用智能裁剪，然后以 `Stream` 格式返回结果。 从 `ImageScenarioPage` 类继承的方法会在场景页面中显示返回的结果。

`RecognizeUrlAsync` 方法使用指定的订阅密钥和终结点 URL 创建新的 `ComputerVisionClient` 实例。 它将调用 `ComputerVisionClient.GenerateThumbnailAsync` 方法，传递图像的宽度、高度、URL 以及是否使用智能裁剪，然后以 `Stream` 格式返回结果。 从 `ImageScenarioPage` 类继承的方法会在场景页面中显示返回的结果。

## <a name="clean-up-resources"></a>清理资源

若不再需要资源，请删除克隆到 `Microsoft/Cognitive-Vision-Windows` 存储库的文件夹。 若选择使用示例图像，则还要删除向其克隆 `Microsoft/Cognitive-Face-Windows` 存储库的文件夹。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [人脸 API 入门](../../Face/Tutorials/FaceAPIinCSharpTutorial.md)
