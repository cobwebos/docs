---
title: 近实时分析视频 - 计算机视觉
titleSuffix: Azure Cognitive Services
description: 了解如何使用计算机视觉 API 对实时视频流中的帧执行近实时分析。
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 18b158b7a4881619b93ab404de67f7bb25f92b6a
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2020
ms.locfileid: "76166828"
---
# <a name="analyze-videos-in-near-real-time"></a>近实时分析视频

本文演示如何使用计算机视觉 API 对实时视频流中的帧执行近实时分析。 此类分析的基本要素包括：

- 从视频源获取帧。
- 选择要分析的帧。
- 将这些帧提交给 API。
- 使用从 API 调用返回的每个分析结果。

本文中的示例是用 C# 编写的。 若要访问代码，请转到 GitHub 上的[视频帧分析示例](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/)页。

## <a name="approaches-to-running-near-real-time-analysis"></a>近实时分析的运行方法

可以使用各种方法来解决对视频流运行近实时分析时出现的问题。 本文按从简单到复杂的顺序概述了三种方法。

### <a name="design-an-infinite-loop"></a>设计无限循环

近实时分析的最简单设计是无限循环。 在此循环的每个迭代中，可以抓取帧、对其进行分析，然后使用结果：

```csharp
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

如果分析包括轻量客户端算法，则适合使用此方法。 但是，如果分析在云中发生，则出现的延迟可能会导致 API 调用花费数秒。 在此期间，我们不会捕获图像，而线程基本上不执行任何操作。 最大帧速率受 API 调用延迟的限制。

### <a name="allow-the-api-calls-to-run-in-parallel"></a>允许 API 调用并行运行

尽管简单的单线程循环适比较适合轻量客户端算法，但不太适用于云 API 调用存在延迟的情况。 此问题的解决方法是让长时间运行的 API 调用与抓帧操作并行运行。 在 C# 中，可以使用基于任务的并行度来实现此目的。 例如，可以运行以下代码：

```csharp
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

使用此方法可在单独的任务中启动每项分析。 继续抓取新帧时，该任务可在后台运行。 该方法可以避免在等待 API 调用返回时阻塞主线程。 但是，该方法也可能存在几项弱点：
* 你会失去简单版本提供的某些保障。 也就是说，多个 API 调用可能并行执行，但结果可能以错误的顺序返回。 
* 这也可能导致多个线程同时进入 ConsumeResult() 函数，如果该函数非线程安全，这可能会很危险。 
* 最后，此简单代码不会跟踪创建的任务，异常将以无提示方式消失。 因此，需要添加“使用者”线程，用于跟踪分析任务，引发异常，终止长时间运行的任务，并确保以正确的顺序逐个使用结果。

### <a name="design-a-producer-consumer-system"></a>设计生成者-使用者系统

最后一种方法是设计一个“生成者-使用者”系统：构建一个类似于上述无限循环的生成者线程。 但是，生成者不会在分析结果可用后立即使用这些结果，而仅仅是将任务放入队列，以对其进行跟踪。

```csharp
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

还需要创建一个使用者线程，它会将任务从队列中取出，等待它们完成，并显示结果或引发已引发的异常。 使用队列可以保证按正确的顺序逐个使用结果，而不会限制系统的最大帧速率。

```csharp
// Consumer thread.
while (true)
{
    // Get the oldest task.
    Task<ResultWrapper> analysisTask = taskQueue.Take();
 
    // Wait until the task is completed.
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

## <a name="implement-the-solution"></a>实施解决方案

### <a name="get-started-quickly"></a>快速入门

为帮助你尽快启动并运行应用，我们已实施前面部分中所述的系统。 该系统具有足够的灵活性，可适应多种方案，并且易于使用。 若要访问代码，请转到 GitHub 上的[视频帧分析示例](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/)页。

该库包含 `FrameGrabber` 类，该类会通过实现上述生成者-使用者系统来处理网络摄像头中的视频帧。 用户可以指定 API 调用的确切形式，该类将使用事件来让调用代码知道何时获取新帧或者新的分析结果何时可用。

为了演示某些可行性，我们提供了使用该库的两个示例应用。 

第一个示例应用是一个简单的控制台应用，它从默认的网络摄像头抓帧，然后将其提交到人脸服务进行人脸检测。 以下代码再现了该应用的简化版本：

```csharp
using System;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Vision.Face;
using Microsoft.Azure.CognitiveServices.Vision.Face.Models;
using VideoFrameAnalyzer;

namespace BasicConsoleSample
{
    internal class Program
    {
        const string ApiKey = "<your API key>";
        const string Endpoint = "https://<your API region>.api.cognitive.microsoft.com";

        private static async Task Main(string[] args)
        {
            // Create grabber.
            FrameGrabber<DetectedFace[]> grabber = new FrameGrabber<DetectedFace[]>();

            // Create Face Client.
            FaceClient faceClient = new FaceClient(new ApiKeyServiceClientCredentials(ApiKey))
            {
                Endpoint = Endpoint
            };

            // Set up a listener for when we acquire a new frame.
            grabber.NewFrameProvided += (s, e) =>
            {
                Console.WriteLine($"New frame acquired at {e.Frame.Metadata.Timestamp}");
            };

            // Set up a Face API call.
            grabber.AnalysisFunction = async frame =>
            {
                Console.WriteLine($"Submitting frame acquired at {frame.Metadata.Timestamp}");
                // Encode image and submit to Face service.
                return (await faceClient.Face.DetectWithStreamAsync(frame.Image.ToMemoryStream(".jpg"))).ToArray();
            };

            // Set up a listener for when we receive a new result from an API call.
            grabber.NewResultAvailable += (s, e) =>
            {
                if (e.TimedOut)
                    Console.WriteLine("API call timed out.");
                else if (e.Exception != null)
                    Console.WriteLine("API call threw an exception.");
                else
                    Console.WriteLine($"New result received for frame acquired at {e.Frame.Metadata.Timestamp}. {e.Analysis.Length} faces detected");
            };

            // Tell grabber when to call the API.
            // See also TriggerAnalysisOnPredicate
            grabber.TriggerAnalysisOnInterval(TimeSpan.FromMilliseconds(3000));

            // Start running in the background.
            await grabber.StartProcessingCameraAsync();

            // Wait for key press to stop.
            Console.WriteLine("Press any key to stop...");
            Console.ReadKey();

            // Stop, blocking until done.
            await grabber.StopProcessingAsync();
        }
    }
}
```

第二个示例应用更有趣。 它允许选择对视频帧调用哪个 API。 在左侧，该应用会显示实时视频的预览。 在右侧，它会将最新 API 结果重叠在相应的帧上。

在大多数模式下，左侧的实时视频与右侧的可视化分析之间存在明显的延迟。 这种延迟是发出 API 调用所花费的时间。 例外情况是“EmotionsWithClientFaceDetect”模式，它使用 OpenCV 在客户端计算机上本地执行人脸检测，然后将所有图像提交给 Azure 认知服务。 

使用此方法可以立即可视化检测到的人脸。 然后可以在 API 调用返回后更新情绪。 这证明了“混合”方法的可行性。 即，可以在客户端上执行一些简单的处理，然后在必要时使用认知服务 API 通过更高级的分析对这种处理进行补充。

![显示带标记图像的 LiveCameraSample 应用](../../Video/Images/FramebyFrame.jpg)

### <a name="integrate-the-samples-into-your-codebase"></a>将示例集成到基础代码中

若要开始使用此示例，请执行以下操作：

1. 从[订阅](https://azure.microsoft.com/try/cognitive-services/)获取视觉 API 的 API 密钥。 对于视频帧分析，适用的服务包括：
    - [计算机视觉](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
    - [人脸](https://docs.microsoft.com/azure/cognitive-services/face/overview)
2. 克隆 [Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) GitHub 存储库。

3. 在 Visual Studio 2015 或更高版本中打开示例，然后生成并运行示例应用程序：
    - 对于 BasicConsoleSample，人脸密钥直接在 [BasicConsoleSample/Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs) 中进行硬编码。
    - 对于 LiveCameraSample，请在应用的“设置”窗格中输入密钥。  在切换不同的会话后，这些密钥将持久保存为用户数据。

准备好集成示例时，请从自己的项目引用 VideoFrameAnalyzer 库。

VideoFrameAnalyzer 的图像、语音、视频和文本理解功能使用 Azure 认知服务。 Microsoft 将接收你（通过此应用）上传的图像、音频、视频和其他数据，并可能将其用于服务改进目的。 你的应用发送了用户的数据给 Azure 认知服务，请协助我们保护这些用户。

## <a name="summary"></a>总结

本文介绍了如何使用人脸服务和计算机视觉服务对实时视频流运行近实时分析。 此外还介绍了如何通过示例代码开始使用这些功能。 若要开始使用免费 API 密钥生成应用，请转到 [Azure 认知服务注册页](https://azure.microsoft.com/try/cognitive-services/)。

欢迎在 [GitHub 存储库](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/)中提供反馈和建议。 若要提供更广泛的 API 反馈，请访问 [UserVoice 站点](https://cognitive.uservoice.com/)。

