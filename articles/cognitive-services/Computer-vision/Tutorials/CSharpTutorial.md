---
title: 计算机视觉 API C# 教程 | Microsoft Docs
description: 介绍一款使用 Microsoft 认知服务中的计算机视觉 API 的基本 Windows 应用。 执行 OCR、创建缩略图，并在图像中使用可视化功能。
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 05/22/2017
ms.author: kefre
ms.openlocfilehash: f2aeb1734f8858ed8b80e5cdf48ef7e558703919
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365898"
---
# <a name="computer-vision-api-c35-tutorial"></a>计算机视觉 API C# 教程

介绍一款使用计算机视觉 API 的基本 Windows 应用，该应用可执行光学字符识别 (OCR)、创建智能裁剪缩略图，并对图像（包括人脸）执行检测、分类、标记和描述等可视化功能。 通过以下示例可提交图像 URL 或存储于本地的文件。 可将此开放源代码示例作为模板，使用视觉 API 和 .NET Framework 中的 WPF (Windows Presentation Foundation) 构建自己的 Windows 应用。

### <a name="prerequisites"></a>先决条件

#### <a name="platform-requirements"></a>平台要求

以下示例是使用 [Visual Studio 2015 Community Edition](https://www.visualstudio.com/downloads/) 针对 .NET Framework 而开发的。

#### <a name="subscribe-to-computer-vision-api-and-get-a-subscription-key"></a>订阅计算机视觉 API 并获得订阅密钥 

创建示例前，必须先订阅 Microsoft 认知服务（之前称为牛津项目）中包含的计算机视觉 API。 有关订阅和密钥管理的详细信息，请参阅[订阅](https://azure.microsoft.com/try/cognitive-services/)。 主密钥和辅助密钥均适用于本教程。 

> [!NOTE]
> 本教程计划使用 westcentralus 区域中的订阅密钥。 免费试用版计算机视觉生成的订阅密钥使用的是 westcentralus 区域，因此可以使用该密钥。 如果使用 Azure 帐户通过 [https://azure.microsoft.com/](https://azure.microsoft.com/) 生成订阅密钥，则必须指定 westcentralus 区域。 在 westcentralus 区域外生成的密钥则无法使用。

#### <a name="get-the-client-library-and-example"></a>获取客户端库和示例

可通过 [SDK](https://www.github.com/microsoft/cognitive-vision-windows) 将计算机视觉 API 客户端库和示例应用程序克隆到计算机。 请勿以 ZIP 格式下载。

### <a name="Step1">步骤 1：安装示例</a>

在 GitHub 桌面版中，打开 Sample-WPF\VisionAPI-WPF-Samples.sln。

### <a name="Step2">步骤 2：生成示例</a>

* 按 Ctrl+Shift+B 或单击功能区菜单上的“生成”，然后选择“生成解决方案”。

### <a name="Step3">步骤 3：运行示例</a>

1. 生成完成后，按 F5 或单击功能区菜单上的“启动”来运行该示例。
2. 转到计算机视觉 API 用户界面窗口，其文本编辑框显示有“将订阅密钥粘贴到此处进行启动”。
可单击“保存密钥”按钮，选择将订阅密钥保存在 PC 或笔记本电脑上。 若要从系统删除订阅密钥，请单击“删除密钥”将其从 PC 或笔记本电脑中删除。

    ![视觉订阅密钥](../Images/Vision_UI_Subscription.PNG)

3. 在“选择方案”下，单击以使用六种方案中的一种，然后按照屏幕上的说明进行操作。 Microsoft 会收到上传的图像，并且可能会将其用于改进计算机视觉 API 和相关服务。 提交图像即表明用户确认遵循我们的[开发人员行为准则](https://azure.microsoft.com/support/legal/developer-code-of-conduct/)。

    ![分析图像界面](../Images/Analyze_Image_Example.PNG)

4. 我们提供了可用于此示例应用程序的示例图像。 可在[数据文件夹](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data)中的人脸 API Windows Github 存储库中找到这些图像。 请注意，这些图像已获得了协议 [ LICENSE-IMAGE](https://github.com/Microsoft/Cognitive-Face-Windows/blob/master/LICENSE-IMAGE.md) 的许可。

### <a name="Review">温故知新</a>

既然现在应用程序已经在运行了，那我们来回顾一下这个示例应用是如何与认知服务技术集成的。 这样用户能更轻松地继续构建此应用或是使用 Microsoft 计算机视觉 API 开发自己的应用。

此示例应用采用了计算机视觉 API 客户端库，这是 Microsoft 计算机视觉 API 的精简 C# 客户端包装器。 在按上述步骤生成示例应用时，从 NuGet 包中获得了客户端库。 可在“视觉”、“Windows”、“客户端库”下的“客户端库”文件夹中查看客户端库源代码，该代码包含在上方先决条件中提到的可下载文件存储库中。

还可在解决方案资源管理器中了解如何使用客户端库代码：在 VisionAPI-WPF_Samples 下，展开 AnalyzePage.xaml，找到 AnalyzePage.xaml.cs，它可将图像提交到图像分析终结点。 双击 .xaml.cs 文件，将其在 Visual Studio 的新窗口中打开。

我们来看一看 AnalyzePage.xaml.cs 中的两个代码片段，回顾一下视觉客户端库在示例中的用法。 该文件包含代码注释，指示“KEY SAMPLE CODE STARTS HERE”和“KEY SAMPLE CODE ENDS HERE”帮助用户查找下方呈现的代码片段。

分析终结点可处理输入的图像 URL 或二进制图像数据（采用八位字节流形式）。 首先，找到一个能使用视觉客户端库的 using 指令。

```
                // ----------------------------------------------------------------------
                // KEY SAMPLE CODE STARTS HERE
                // Use the following namespace for VisionServiceClient 
                // ---------------------------------------------------------------------- 
                using Microsoft.ProjectOxford.Vision; 
                using Microsoft.ProjectOxford.Vision.Contract; 
                // ----------------------------------------------------------------------
                // KEY SAMPLE CODE ENDS HERE 
                // ----------------------------------------------------------------------

```
**UploadAndAnalyzeImage(…)** 此代码段显示如何使用客户端库将订阅密钥和存储于本地的图像提交到计算机视觉 API 服务的分析终结点。

```
    private async Task<AnalysisResult> UploadAndAnalyzeImage(string imageFilePath)
    {
        // -----------------------------------------------------------------------
        // KEY SAMPLE CODE STARTS HERE
        // -----------------------------------------------------------------------  
        //
        // Create Project Oxford Computer Vision API Service client
        //
        VisionServiceClient VisionServiceClient = new VisionServiceClient(SubscriptionKey);
        Log("VisionServiceClient is created");
    
        using (Stream imageFileStream = File.OpenRead(imageFilePath))
        {
            //
            // Analyze the image for all visual features
            //
            Log("Calling VisionServiceClient.AnalyzeImageAsync()...");
         VisualFeature[] visualFeatures = new VisualFeature[] { VisualFeature.Adult, VisualFeature.Categories, VisualFeature.Color, VisualFeature.Description, VisualFeature.Faces, VisualFeature.ImageType, VisualFeature.Tags };
            AnalysisResult analysisResult = await VisionServiceClient.AnalyzeImageAsync(imageFileStream, visualFeatures);
            return analysisResult;
        }
    
        // -----------------------------------------------------------------------
        // KEY SAMPLE CODE ENDS HERE
        // -----------------------------------------------------------------------
        }
```
**AnalyzeUrl(…)** 此代码段显示如何使用客户端库将订阅密钥和图像 URL 提交到计算机视觉 API 服务的分析终结点。

```
    private async Task<AnalysisResult> AnalyzeUrl(string imageUrl)
    {
        // -----------------------------------------------------------------------
        // KEY SAMPLE CODE STARTS HERE
        // -----------------------------------------------------------------------
    
        //
        // Create Project Oxford Computer Vision API Service client
        //
     VisionServiceClient VisionServiceClient = new VisionServiceClient(SubscriptionKey);
        Log("VisionServiceClient is created");
    
        //
        // Analyze the url for all visual features
        //
        Log("Calling VisionServiceClient.AnalyzeImageAsync()...");
        VisualFeature[] visualFeatures = new VisualFeature[] { VisualFeature.Adult, VisualFeature.Categories, VisualFeature.Color, VisualFeature.Description, VisualFeature.Faces, VisualFeature.ImageType, VisualFeature.Tags };
        AnalysisResult analysisResult = await VisionServiceClient.AnalyzeImageAsync(imageUrl, visualFeatures);
     return analysisResult;
    }
        // -----------------------------------------------------------------------
        // KEY SAMPLE CODE ENDS HERE
        // -----------------------------------------------------------------------
```
**其他页面和终结点** 若要了解如何与计算机视觉 API 服务公开的其他终结点进行交互，可查看示例中的其他页面，例如，OCRPage.xaml.cs 包含的代码中显示了 OCR 终结点 

### <a name="Related">相关主题</a>
 * [人脸 API 入门](../../Face/Tutorials/FaceAPIinCSharpTutorial.md)
 
 


