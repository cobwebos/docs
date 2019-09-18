---
title: 示例：实时视频分析 - 计算机视觉
titleSuffix: Azure Cognitive Services
description: 了解如何使用计算机视觉 API 对实时视频流中的帧进行近实时分析。
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 25aed0f042050ebadbc6054fcbf0c68dbf782e5e
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2019
ms.locfileid: "70859074"
---
# <a name="how-to-analyze-videos-in-real-time"></a>如何实时分析视频

本指南将演示如何对从实时视频流中获取的帧执行近实时分析。 此类系统的基本组成如下：

- 从视频源获取帧
- 选择要分析的帧
- 将这些帧提交给 API
- 使用从 API 调用返回的每个分析结果

这些示例是使用 C# 编写的，代码可以在 GitHub 上找到，网址为：[https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/)。

## <a name="the-approach"></a>方法

可通过多种方法解决对视频流运行近实时分析的问题。 我们先概述三种复杂程度渐增的方法。

### <a name="a-simple-approach"></a>简单的方法

近实时分析系统的最简单设计是无限循环，我们在每次迭代中捕捉一个帧，对它进行分析，然后使用结果：

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

如果分析由轻量级客户端算法组成，则适合采用这种方法。 但是，当在云端进行分析时，所涉及的延迟意味着 API 调用可能需要几秒钟的时间，在此期间，我们不会捕获图像，而线程基本上不执行任何操作。 我们的最大帧速率受 API 调用延迟的限制。

### <a name="parallelizing-api-calls"></a>并行化 API 调用

简单的单线程循环适用于轻量级客户端算法，但不适用于云 API 调用中涉及的延迟。 该问题的解决方案是允许长时间运行的 API 调用与帧捕捉并行执行。 在 C# 中，我们可以使用基于任务的并行来实现这一点，例如：

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

此方法会在单独的任务中启动每个分析，当我们继续捕捉新帧时，这些任务可在后台运行。 这可避免在等待 API 调用返回时阻塞主线程，但是我们失去了简单版本提供的一些保证：多个 API 调用可能并行执行，但结果可能以错误的顺序返回。 此方法还可能导致多个线程同时进入 ConsumeResult() 函数，如果该函数非线程安全，这可能会很危险。 最后，这个简单的代码不会跟踪所创建的任务，因此异常将以无提示方式消失。 因此，我们要添加的最终要素是“使用者”线程，它将跟踪分析任务，引发异常，终止长时间运行的任务，并确保以正确的顺序使用结果，一次使用一个。

### <a name="a-producer-consumer-design"></a>生产者-使用者设计

在最终的“生产者-使用者”系统中，我们有一个生产者线程，看起来与我们之前的无限循环类似。 但是，生产者只需将任务放入队列即可跟踪它们，而不必在分析结果可用时立即使用它们。

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

我们还有一个使用者线程，它会将任务从队列中取出，等待它们完成，并显示结果或引发已引发的异常。 通过使用队列，我们​​可以保证按正确的顺序一次使用一个结果，而不限制系统的最大帧速率。

```csharp
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

为了尽快启动和运行应用，我们实现了上述系统，希望它足够灵活，可以实现许多方案，同时又易于使用。 若要访问代码，请转到 [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis)。

该库包含 FrameGrabber 类，该类可实现上面所述的生产者-使用者系统，以处理来自网络摄像头的视频帧。 用户可以指定 API 调用的确切形式，该类将使用事件来让调用代码知道何时获取新帧或者新的分析结果何时可用。

为了说明一些可能性，下面例举了使用该库的两个示例应用。 第一个是简单的控制台应用，下面再现了它的简化版本。 它从默认网络摄像头捕捉帧，并将它们提交到人脸 API 进行人脸检测。

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

        private static void Main(string[] args)
        {
            // Create grabber.
            FrameGrabber<DetectedFace[]> grabber = new FrameGrabber<DetectedFace[]>();

            // Create Face API Client.
            FaceClient faceClient = new FaceClient(new ApiKeyServiceClientCredentials(ApiKey))
            {
                Endpoint = Endpoint
            };

            // Set up a listener for when we acquire a new frame.
            grabber.NewFrameProvided += (s, e) =>
            {
                Console.WriteLine($"New frame acquired at {e.Frame.Metadata.Timestamp}");
            };

            // Set up Face API call.
            grabber.AnalysisFunction = async frame =>
            {
                Console.WriteLine($"Submitting frame acquired at {frame.Metadata.Timestamp}");
                // Encode image and submit to Face API.
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

            // Tell grabber when to call API.
            // See also TriggerAnalysisOnPredicate
            grabber.TriggerAnalysisOnInterval(TimeSpan.FromMilliseconds(3000));

            // Start running in the background.
            grabber.StartProcessingCameraAsync().Wait();

            // Wait for key press to stop.
            Console.WriteLine("Press any key to stop...");
            Console.ReadKey();

            // Stop, blocking until done.
            grabber.StopProcessingAsync().Wait();
        }
    }
}
```

第二个示例应用更有趣，允许针对视频帧选择调用哪个 API。 在左侧，应用显示实时视频预览，在右侧，它显示重叠在相应帧上的最新 API 结果。

在大多数模式中，左侧的实时视频和右侧的可视化分析之间会有明显的延迟。 此延迟是进行 API 调用所需的时间。 例外情况是“EmotionsWithClientFaceDetect”模式，它使用 OpenCV 在客户端计算机上本地执行人脸检测，然后将全部图像提交给认知服务。 通过这样做，我们可以立即可视化检测到的人脸，然后在 API 调用返回后更新情感。 这证明了“混合”方法的可行性，即，可以在客户端上执行一些简单的处理，然后在必要时使用认知服务 API 通过更高级的分析进行补充。

![LiveCameraSample 应用的屏幕截图，其中显示带有标签的图像](../../Video/Images/FramebyFrame.jpg)

### <a name="integrating-into-your-codebase"></a>集成到基本代码中

若要开始使用此示例，请按照下列步骤操作：

1. 从[订阅](https://azure.microsoft.com/try/cognitive-services/)获取视觉 API 的 API 密钥。 对于视频帧分析，适用的 API 包括：
    - [计算机视觉 API](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
    - [人脸 API](https://docs.microsoft.com/azure/cognitive-services/face/overview)
2. 克隆 [Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) GitHub 存储库

3. 在 Visual Studio 2015 或更高版本中打开示例，然后生成并运行示例应用程序：
    - 对于 BasicConsoleSample，人脸 API 密钥直接在 [BasicConsoleSample/Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs) 中进行硬编码。
    - 对于 LiveCameraSample，应将密钥输入应用的“设置”窗格。 它们将作为用户数据保留在各会话中。

当准备好进行集成时，只需从你自己的项目中引用 VideoFrameAnalyzer 库。 

VideoFrameAnalyzer 的图像、语音、视频或文本理解功能使用 Azure 认知服务。 Microsoft 将接收你（通过此应用）上传的图像、音频、视频和其他数据，并可能将其用于服务改进目的。 你的应用发送了用户的数据给 Azure 认知服务，请协助我们保护这些用户。

## <a name="summary"></a>摘要

在本指南中，你已了解如何使用人脸 API 和计算机视觉 API 对实时视频流运行近实时分析，以及如何使用我们的示例代码开始操作。 你可以在 [Azure 认知服务注册页](https://azure.microsoft.com/try/cognitive-services/)上使用免费 API 密钥开始构建应用。

请随时在 [GitHub 存储库](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/)中提供反馈和建议，或者在我们的 [UserVoice 网站](https://cognitive.uservoice.com/)上提供更广泛的 API 反馈。

