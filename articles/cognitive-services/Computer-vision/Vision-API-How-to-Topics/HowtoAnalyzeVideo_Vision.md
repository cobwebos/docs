---
title: 使用计算机视觉 API 进行实时视频分析 | Microsoft Docs
description: 了解如何使用认知服务中的计算机视觉 API 对实时视频流中的帧进行准实时分析。
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 01/20/2017
ms.author: kefre
ms.openlocfilehash: d75b1a887e5e4557d5464d8062e1bde628e7adab
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365752"
---
# <a name="how-to-analyze-videos-in-real-time"></a>如何实时分析视频
本指南将演示如何对从实时视频流中获取的帧进行准实时分析。 此类系统的基本组件是：
- 从视频源获取帧
- 选择要分析的帧
- 将这些帧提交到 API
- 使用从 API 调用返回的每个分析结果

这些示例是使用 C# 编写的，代码可以在 GitHub 上找到，网址为：[https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/)。

## <a name="the-approach"></a>方法
有多种方法可以解决对视频流进行准实时分析的问题。 我们将首先概述三种复杂程度渐增的方法。

### <a name="a-simple-approach"></a>简单方法
准实时分析系统的最简单设计是无限循环，在每次迭代中我们抓取一个帧，分析它，然后使用结果：
```CSharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        AnalysisResult r = await Analyze(f);
        ConsumeResult(r);
    }
}
```
如果分析由轻量级客户端算法组成，那么这种方法将是合适的。 但是，当在云中进行分析时，所涉及的延迟意味着 API 调用可能需要几秒钟时间，在此期间，我们不会捕获图像，而线程基本上不执行任何操作。 最大帧速率受 API 调用的延迟限制。

### <a name="parallelizing-api-calls"></a>并行化 API 调用
虽然简单的单线程循环适合轻量级客户端算法，但它不适合云 API 调用中涉及的延迟。 该问题的解决方案是允许长时间运行的 API 调用以与帧抓取并行执行。 在 C# 中，我们可以使用基于任务的并行来实现这一点，例如：
```CSharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        var t = Task.Run(async () => 
        {
            AnalysisResult r = await Analyze(f);
            ConsumeResult(r);
        }
    }
}
```
这将在一个单独的任务中启动每个分析，该分析在我们继续抓取新帧的同时可以在后台运行。 这样可以避免在等待 API 调用返回时阻塞主线程，但是我们已经失去了一些简单版本提供的保证 - 可能并行发生多个 API 调用，并且结果可能以错误的顺序返回。 这也可能导致多个线程同时进入 ConsumeResult() 函数，如果函数非线程安全，这可能会很危险。 最后，此简单代码不会跟踪创建的任务，因此异常将以无提示方式消失。 因此，我们要添加的最终要素是“使用者”线程，它将跟踪分析任务，引发异常，终止长时间运行的任务，并确保以正确的顺序使用结果，一次使用一个。

### <a name="a-producer-consumer-design"></a>制造者-使用者设计
在我们最终的“制造者 - 消费者”系统中，我们有一个制造者线程，看起来与之前的无限循环非常相似。 但是，制造者只需将任务放在一个队列中即可跟踪它们，而不是在分析结果可用时立即使用它们。
```CSharp
// Queue that will contain the API call tasks. 
var taskQueue = new BlockingCollection<Task<ResultWrapper>>();
     
// Producer thread. 
while (true)
{
    // Grab a frame. 
    Frame f = GrabFrame();
 
    // Decide whether to analyze the frame. 
    if (ShouldAnalyze(f))
    {
        // Start a task that will run in parallel with this thread. 
        var analysisTask = Task.Run(async () => 
        {
            // Put the frame, and the result/exception into a wrapper object.
            var output = new ResultWrapper(f);
            try
            {
                output.Analysis = await Analyze(f);
            }
            catch (Exception e)
            {
                output.Exception = e;
            }
            return output;
        }
        
        // Push the task onto the queue. 
        taskQueue.Add(analysisTask);
    }
}
```
我们还有一个使用者线程，即将任务从队列中取出，等待它们完成，并显示结果或提升引发的异常。 通过使用队列，我们可以保证以正确的顺序一次使用一个结果，而不限制系统的最大帧速率。
```CSharp
// Consumer thread. 
while (true)
{
    // Get the oldest task. 
    Task<ResultWrapper> analysisTask = taskQueue.Take();
 
    // Await until the task is completed. 
    var output = await analysisTask;
     
    // Consume the exception or result. 
    if (output.Exception != null)
    {
        throw output.Exception;
    }
    else
    {
        ConsumeResult(output.Analysis);
    }
}
```

## <a name="implementing-the-solution"></a>实现解决方案
### <a name="getting-started"></a>入门
为了尽快启动和运行应用程序，我们已经实现了上述系统，希望它足够灵活，易于使用，可以实现许多方案。 若要访问代码，请转到 [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis)。

该库包含 FrameGrabber 类，它实现了上面讨论的制造者 - 使用者系统，以处理来自网络摄像头的视频帧。 用户可以指定 API 调用的确切形式，并且类使用事件让调用代码知道何时获取新帧，或者何时新的分析结果可用。

为了说明一些可能性，有两个使用该库的示例应用程序。 第一个是一个简单的控制台应用程序，下面再现了它的简化版本。 它从默认网络摄像头抓取帧，并将它们提交到人脸 API 进行人脸检测。
```CSharp
using System;
using VideoFrameAnalyzer;
using Microsoft.ProjectOxford.Face;
using Microsoft.ProjectOxford.Face.Contract;
     
namespace VideoFrameConsoleApplication
{
    class Program
    {
        static void Main(string[] args)
        {
            // Create grabber, with analysis type Face[]. 
            FrameGrabber<Face[]> grabber = new FrameGrabber<Face[]>();
            
            // Create Face API Client. Insert your Face API key here.
            FaceServiceClient faceClient = new FaceServiceClient("<subscription key>");

            // Set up our Face API call.
            grabber.AnalysisFunction = async frame => return await faceClient.DetectAsync(frame.Image.ToMemoryStream(".jpg"));

            // Set up a listener for when we receive a new result from an API call. 
            grabber.NewResultAvailable += (s, e) =>
            {
                if (e.Analysis != null)
                    Console.WriteLine("New result received for frame acquired at {0}. {1} faces detected", e.Frame.Metadata.Timestamp, e.Analysis.Length);
            };
            
            // Tell grabber to call the Face API every 3 seconds.
            grabber.TriggerAnalysisOnInterval(TimeSpan.FromMilliseconds(3000));

            // Start running.
            grabber.StartProcessingCameraAsync().Wait();

            // Wait for keypress to stop
            Console.WriteLine("Press any key to stop...");
            Console.ReadKey();
            
            // Stop, blocking until done.
            grabber.StopProcessingAsync().Wait();
        }
    }
}
```
第二个示例应用更有趣，允许针对视频帧选择调用哪个 API。 在左侧，应用显示实时视频预览，在右侧，它显示重叠在相应帧上的最新 API 结果。

在大多数模式中，左侧的实时视频和右侧的可视化分析之间会有明显的延迟。 此延迟是进行 API 调用所需的时间。 例外情况是“EmotionsWithClientFaceDetect”模式，它使用 OpenCV 在客户端计算机上本地执行人脸检测，然后将全部图像提交给认知服务。 通过这样做，我们可以立即可视化检测到的人脸，然后在 API 调用返回后更新情感。 该操作证明了“混合”方法的可能性，其中可以在客户端上执行一些简单的处理，然后可以使用认知服务 API 在必要时通过更高级的分析来增强它。

![HowToAnalyzeVideo](../../Video/Images/FramebyFrame.jpg)

### <a name="integrating-into-your-codebase"></a>集成到基本代码
要开始使用此示例，请按照下列步骤操作：

1. 从[订阅](https://azure.microsoft.com/try/cognitive-services/)获取视觉 API 的 API 密钥。 对于视频帧分析，适用的 API 包括：
    - [计算机视觉 API](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
    - [情感 API](https://docs.microsoft.com/azure/cognitive-services/emotion/home)
    - [人脸 API](https://docs.microsoft.com/azure/cognitive-services/face/overview)
2. 克隆 [Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) GitHub 存储库

3. 在 Visual Studio 2015 中打开示例，构建并运行示例应用程序：
    - 对于 BasicConsoleSample，人脸 API 密钥直接在 [BasicConsoleSample / Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs) 中进行硬编码。
    - 对于 LiveCameraSample，应将密钥输入应用的“设置”窗格。 它们将作为用户数据在会话中保留。
        

当准备好进行集成时，只需从你自己的项目中引用 VideoFrameAnalyzer 库。 



## <a name="developer-code-of-conduct"></a>开发人员行为准则
与所有认知服务一样，使用 API 和示例进行开发的开发人员必须遵循“[Microsoft 认知服务开发人员行为准则](https://azure.microsoft.com/support/legal/developer-code-of-conduct/)”。 


VideoFrameAnalyzer 的图像、语音、视频或文本理解功能使用 Microsoft 认知服务。 Microsoft 将接收你（通过此应用）上传的图像、音频、视频和其他数据，并可能将其用于服务改进目的。 我们呼吁你保护应用将其数据发送给 Microsoft 认知服务的人员。 


## <a name="summary"></a>摘要
在本指南中，你学习了如何使用人脸、计算机视觉和情感 API 对实时视频流进行准实时分析，以及如何开始使用示例代码。 你可以在 [Microsoft 认知服务注册页面](https://azure.microsoft.com/try/cognitive-services/)上使用免费 API 密钥开始构建应用。 

请随时在 [GitHub 存储库](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/)中提供反馈和建议，或者在我们的 [UserVoice 网站](https://cognitive.uservoice.com/)上获取更广泛的 API 反馈。

