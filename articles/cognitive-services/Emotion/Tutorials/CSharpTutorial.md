---
title: 情感 API C# 教程 | Microsoft Docs
description: 探索使用认知服务情感 API 识别图像中人脸所表达的情感的基本 Windows 应用。
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 01/23/2017
ms.author: anroth
ms.openlocfilehash: f015e5720f65d0951a77de76ce8882a6dcdc1c3b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365754"
---
# <a name="emotion-api-in-c35-tutorial"></a>面向 C# 的情感 API 教程

> [!IMPORTANT]
> 视频 API 预览将于 2017 年 10 月 30 日结束。 请试用新的[视频索引器 API 预览](https://azure.microsoft.com/services/cognitive-services/video-indexer/)，通过检测口语、人脸、字符和情感，轻松地从视频中提取见解，增强内容发现体验，例如搜索结果。 [了解详细信息](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview)。

探索使用情感 API 识别图像中人脸所表达的情感的基本 Windows 应用。 通过以下示例可提交图像 URL 或存储于本地的文件。 可将此开放源代码示例作为模板，使用情感 API 和 .NET Framework 中的 WPF (Windows Presentation Foundation) 生成自己的 Windows 应用。

## <a name="Prerequisites">先决条件</a>
#### <a name="platform-requirements"></a>平台要求  
以下示例是使用 [Visual Studio 2015 Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs) 针对 .NET Framework 开发的。  

#### <a name="subscribe-to-emotion-api-and-get-a-subscription-key"></a>订阅情感 API 并获得订阅密钥  
创建示例前，必须先订阅 Microsoft 认知服务中包含的情感 API。 请参阅[订阅](https://azure.microsoft.com/try/cognitive-services/)。 主密钥和辅助密钥均适用于本教程。 请务必遵循最佳做法，以保证 API 密钥的机密性和安全性。  

#### <a name="get-the-client-library-and-example"></a>获取客户端库和示例  
可以通过 [SDK](https://www.github.com/microsoft/cognitive-emotion-windows) 下载情感 API 客户端库。 下载的 zip 文件需要提取到选定的文件夹，许多用户会选择 Visual Studio 2015 文件夹。
## <a name="Step1">步骤 1：打开示例</a>
1.  启动 Microsoft Visual Studio 2015 并单击“文件”，选择“打开”，然后选择“项目/解决方案”。
2.  浏览到保存下载的情感 API 文件的文件夹。 依次单击“情感”、“Windows”、“Sample-WPF”文件夹。
3.  双击打开名为“EmotionAPI-WPF-Samples.sln”的 Visual Studio 2015 解决方案 (.sln) 文件。 这将在 Visual Studio 中打开解决方案。

## <a name="Step2">步骤 2：生成示例</a>
1. 在“解决方案资源管理器”中，右键单击“引用”并选择“管理 NuGet 包”。

  ![打开 Nuget 包管理器](../Images/EmotionNuget.png)

2.  系统会打开“NuGet 包管理器”窗口。 首先选择左上角的“浏览”，然后在搜索框中键入“Newtonsoft.Json”，选择 **Newtonsoft.Json** 包并单击“安装”。  

  ![浏览到 NuGet 包](../Images/EmotionNugetBrowse.png)  

3.  按 Ctrl+Shift+B 或单击功能区菜单上的“生成”，然后选择“生成解决方案”。

## <a name="Step3">步骤 3：运行示例</a>
1.  生成完成后，按 **F5** 或单击功能区菜单上的“启动”来运行该示例。
2.  找到情感 API 窗口，其**文本框**显示有“将订阅密钥粘贴到此处进行启动”。 将订阅密钥粘贴到文本框中，如下面的屏幕截图所示。 可单击“保存密钥”按钮，选择将订阅密钥保存在 PC 或笔记本电脑上。 若要从系统删除订阅密钥，请单击“删除密钥”将其从 PC 或笔记本电脑中删除。
  
  ![情感功能界面](../Images/EmotionKey.png)

3.  在“选择方案”下，单击以使用两种方案之一（“使用流检测情感”或“使用 URL 检测情感”），然后按屏幕上的说明操作。 Microsoft 会收到上传的图像，并且可能会将其用于改进情感 API 和相关服务。 提交图像即表明你确认遵循我们的[开发人员行为准则](https://azure.microsoft.com/support/legal/developer-code-of-conduct/)。
4.  我们提供了可用于此示例应用程序的示例图像。 可在[人脸 API Github 存储库](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data)中的“数据”文件夹下找到这些图像。 请注意，这些图像的使用受公平使用协议许可，这表示它们可用于测试此示例，但不可重新发布。

## <a name="Review">温故知新</a>
你的应用程序已经在运行了，现在让我们回顾一下这个示例应用是如何与 Microsoft 认知服务集成的。 这样你就能更轻松地继续生成此应用，或使用 Microsoft 情感 API 开发自己的应用。 

此示例应用采用了情感 API 客户端库，这是 Microsoft 情感 API 的精简 C# 客户端包装器。 在按上文所述生成示例应用时，从 NuGet 包中获得了客户端库。 可在“情感”、“Windows”、“客户端库”下的“[客户端库](https://github.com/Microsoft/Cognitive-Emotion-Windows/tree/master/ClientLibrary)”文件夹中查看客户端库源代码，该代码包含在上面的[先决条件](#Prerequisites)中提到的已下载文件存储库中。
 
还可了解如何在“解决方案资源管理器”中使用客户端库代码：在 **EmotionAPI-WPF_Samples** 下，展开 **DetectEmotionUsingStreamPage.xaml** 找到 **DetectEmotionUsingStreamPage.xaml.cs**（用于浏览本地存储的文件），或者展开 **DetectEmotionUsingURLPage.xaml** 找到 **DetectEmotionUsingURLPage.xaml.cs**（在上传图像 URL 时使用）。 双击 .xaml.cs 文件，将其在 Visual Studio 的新窗口中打开。 

我们来看一看 **DetectEmotionUsingStreamPage.xaml.cs** 和 **DetectEmotionUsingURLPage.xaml.cs** 中的两个代码片段，回顾一下情感客户端库在示例应用中的用法。 每个文件均包含指示“KEY SAMPLE CODE STARTS HERE”和“KEY SAMPLE CODE ENDS HERE”的代码注释，可帮助你查找下面再现的代码片段。

情感 API 可处理输入的图像 URL 或二进制图像数据（采用八进制数流形式）。 下面将回顾这两种选项。 在这两种情况下，首先找到一个允许使用情感客户端库的 using 指令。 
```csharp

            // ----------------------------------------------------------------------- 
            // KEY SAMPLE CODE STARTS HERE 
            // Use the following namespace for EmotionServiceClient 
            // ----------------------------------------------------------------------- 
            using Microsoft.ProjectOxford.Emotion; 
            using Microsoft.ProjectOxford.Emotion.Contract; 
            // ----------------------------------------------------------------------- 
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------- 
```
#### <a name="detectemotionusingurlpagexamlcs"></a>DetectEmotionUsingURLPage.xaml.cs 

此代码片段展示了如何使用客户端库将订阅密钥和照片 URL 提交到情感 API 服务。 

```csharp

            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // -----------------------------------------------------------------------

            window.Log("EmotionServiceClient is created");

            //
            // Create Project Oxford Emotion API Service client
            //
            EmotionServiceClient emotionServiceClient = new EmotionServiceClient(subscriptionKey);

            window.Log("Calling EmotionServiceClient.RecognizeAsync()...");
            try
            {
                //
                // Detect the emotions in the URL
                //
                Emotion[] emotionResult = await emotionServiceClient.RecognizeAsync(url);
                return emotionResult;
            }
            catch (Exception exception)
            {
                window.Log("Detection failed. Please make sure that you have the right subscription key and proper URL to detect.");
                window.Log(exception.ToString());
                return null;
            }
            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE
            // -----------------------------------------------------------------------
```
#### <a name="detectemotionusingstreampagexamlcs"></a>DetectEmotionUsingStreamPage.xaml.cs 

下面展示了如何将订阅密钥和本地存储的图像提交到情感 API。 


```csharp


            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // -----------------------------------------------------------------------

            //
            // Create Project Oxford Emotion API Service client
            //
            EmotionServiceClient emotionServiceClient = new EmotionServiceClient(subscriptionKey);

            window.Log("Calling EmotionServiceClient.RecognizeAsync()...");
            try
            {
                Emotion[] emotionResult;
                using (Stream imageFileStream = File.OpenRead(imageFilePath))
                {
                    //
                    // Detect the emotions in the URL
                    //
                    emotionResult = await emotionServiceClient.RecognizeAsync(imageFileStream);
                    return emotionResult;
                }
            }
            catch (Exception exception)
            {
                window.Log(exception.ToString());
                return null;
            }
            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE
            // -----------------------------------------------------------------------
```
<!--
## <a name="Related">Related Topics</a>
[Emotion API Overview](.)
-->
