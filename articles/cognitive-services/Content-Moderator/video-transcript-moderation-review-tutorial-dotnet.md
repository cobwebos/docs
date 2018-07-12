---
title: Azure 内容审查器 - 在 .NET 中审查视频和脚本 | Microsoft Docs
description: 如何在 .NET 中使用内容审查器来审查视频和脚本。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 1/27/2018
ms.author: sajagtap
ms.openlocfilehash: a084b50e44fe26ba2547d0f7b7ed184fb71b190c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2018
ms.locfileid: "35365775"
---
# <a name="video-and-transcript-moderation-tutorial"></a>视频和脚本审查教程

可使用内容审查器的视频 API 审查视频并在人工评论工具中创建视频评论。 

此详细教程可帮助了解如何使用机器辅助审查和人工介入评论创建，来生成完整的视频和脚本审查解决方案。

为本教程下载 [C# 控制台应用程序](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp)。 此控制台应用程序使用 SDK 和相关包来执行以下任务：

- 压缩输入视频以加快处理速度
- 审查视频以获得有洞察力的镜头和帧
- 使用帧时间戳创建缩略图（图像）
- 提交时间戳和缩略图以创建视频评论
- 使用 Media Indexer API 将视频语音转换为文本（脚本）
- 使用文本审查服务审查脚本
- 将已审查的脚本添加到视频评论中

## <a name="sample-program-outputs"></a>程序输出示例

在展开进一步讨论之前，我们来看看该程序的以下输出示例：

- [控制台输出](#program-output)
- [视频评论](#video-review-default-view)
- [脚本视图](#video-review-transcript-view)

## <a name="prerequisites"></a>先决条件

1. 注册[内容审查器评论工具](https://contentmoderator.cognitive.microsoft.com/)网站，并[创建自定义标记](Review-Tool-User-Guide/tags.md)，C# 控制台应用程序将从代码中分配这些标记。 以下屏幕显示了自定义标记。

  ![视频审查自定义标记](images/video-tutorial-custom-tags.png)

1. 若要运行示例应用程序，需要有 Azure 帐户和 Azure 媒体服务帐户。 此外，还需要访问内容审查器个人预览版。 最后，需要 Azure Active Directory 身份验证凭据。 有关获取此信息的详细信息，请参阅[视频审查 API 快速入门](video-moderation-api.md)。

1. 编辑文件 `App.config` 并添加 Active Directory 租户名称、服务终结点和用 `#####` 表示的订阅密钥。 你需要以下信息：

|密钥|说明|
|-|-|
|`AzureMediaServiceRestApiEndpoint`|Azure 媒体服务 (AMS) API 的终结点|
|`ClientSecret`|Azure 媒体服务的订阅密钥|
|`ClientId`|Azure 媒体服务的客户端 ID|
|`AzureAdTenantName`|表示组织的 Active Directory 租户名称|
|`ContentModeratorReviewApiSubscriptionKey`|内容审查器评论 API 的订阅密钥|
|`ContentModeratorApiEndpoint`|内容审查器 API 的终结点|
|`ContentModeratorTeamId`|内容审查器团队 ID|

## <a name="getting-started"></a>入门

`Program.cs` 中的 `Program` 类是视频审查应用程序的主要入口点。

### <a name="methods-of-class-program"></a>Program 类的方法

|方法|说明|
|-|-|
|`Main`|分析命令行、收集用户输入并开始进行处理。|
|`ProcessVideo`|压缩、上传、审查和创建视频评论。|
|`CreateVideoStreamingRequest`|创建用于上传视频的流|
|`GetUserInputs`|收集用户输入；在没有命令行选项时使用|
|`Initialize`|初始化审查过程所需的对象|

### <a name="the-main-method"></a>Main 方法

`Main()` 是执行起始位置，因此，将从这里开始了解视频审查过程。

    static void Main(string[] args)
    {
        if (args.Length == 0)
        {
            string videoPath = string.Empty;
            GetUserInputs(out videoPath);
            Initialize();
            AmsConfigurations.logFilePath = Path.Combine(Path.GetDirectoryName(videoPath), "log.txt");
            try
            {
                ProcessVideo(videoPath).Wait();
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
        }
        else
        {
            DirectoryInfo directoryInfo = new DirectoryInfo(args[0]);
            if (args.Length == 2)
                bool.TryParse(args[1], out generateVtt);
            Initialize();
            AmsConfigurations.logFilePath = Path.Combine(args[0], "log.txt");
            var files = directoryInfo.GetFiles("*.mp4", SearchOption.AllDirectories);
            foreach (var file in files)
            {
                try
                {
                    ProcessVideo(file.FullName).Wait();
                }
                catch (Exception ex)
                {
                    Console.WriteLine(ex.Message);
                }
            }
        }
    }

`Main()` 处理以下命令行参数：

- 目录路径，该目录包含要提交进行审查的 MPEG-4 视频文件。 系统会提交此目录及其子目录中的所有 `*.mp4` 文件进行审查。
- （可选）一个布尔 (true/false) 标志，指示是否应该为了审查音频而生成文本脚本。

如果不存在命令行参数，`Main()` 将调用 `GetUserInputs()`。 此方法提示用户输入单个视频文件的路径，并指定是否应生成文本脚本。

> [!NOTE]
> 控制台应用程序使用 [Azure Media Indexer API](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2) 根据上传视频的音频轨道生成脚本。结果以 WebVTT 格式提供。 有关此格式的详细信息，请参阅 [Web Video Text Tracks Format](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API)（Web 视频文本轨道格式）。

### <a name="initialize-and-processvideo-methods"></a>Initialize 和 ProcessVideo 方法

无论程序的选项来自命令行还是来自交互式用户输入，`Main()` 接下来都会调用 `Initialize()` 创建以下实例：

|类|说明|
|-|-|
|`AMSComponent`|在提交视频文件进行审查之前先对其进行压缩。|
|`AMSconfigurations`|与在 `App.config` 中找到的应用程序配置数据交互。|
|`VideoModerator`| 使用 AMS SDK 上传、编码、加密和审查|
|`VideoReviewApi`|管理内容审查器服务中的视频评论|

本教程的后续部分更详细地介绍了这些类（`AMSConfigurations` 除外，该类非常简单）。

最后，通过为每个视频文件调用 `ProcessVideo()`，一次处理一个视频文件。

    private static async Task ProcessVideo(string videoPath)
    {
        Stopwatch sw = new Stopwatch();
        sw.Start();
        Console.ForegroundColor = ConsoleColor.White;
        Console.WriteLine("\nVideo compression process started...");

        var compressedVideoPath = amsComponent.CompressVideo(videoPath);
        if (string.IsNullOrWhiteSpace(compressedVideoPath))
        {
            Console.ForegroundColor = ConsoleColor.Red;
            Console.WriteLine("Video Compression failed.");
        }

        Console.WriteLine("\nVideo compression process completed...");

        UploadVideoStreamRequest uploadVideoStreamRequest = CreateVideoStreamingRequest(compressedVideoPath);
        UploadAssetResult uploadResult = new UploadAssetResult();

        if (generateVtt)
        {
            uploadResult.GenerateVTT = generateVtt;
        }
        Console.WriteLine("\nVideo moderation process started...");

        if (!videoModerator.CreateAzureMediaServicesJobToModerateVideo(uploadVideoStreamRequest, uploadResult))
        {
            Console.ForegroundColor = ConsoleColor.Red;
            Console.WriteLine("Video Review process failed.");
        }

        Console.WriteLine("\nVideo moderation process completed...");
        Console.WriteLine("\nVideo review process started...");
        string reviewId = await videoReviewApi.CreateVideoReviewInContentModerator(uploadResult);
        Console.WriteLine("\nVideo review successfully completed...");
        sw.Stop();
        Console.WriteLine("\nTotal Elapsed Time: {0}", sw.Elapsed);
        using (var stw = new StreamWriter(AmsConfigurations.logFilePath, true))
        {
            stw.WriteLine("Video File Name: " + Path.GetFileName(videoPath));
            stw.WriteLine($"ReviewId: {reviewId}");
            stw.WriteLine("Total Elapsed Time: {0}", sw.Elapsed);
        }
    }


`ProcessVideo()` 方法非常简单。 它按顺序执行以下操作：

- 压缩视频
- 将视频上传到 Azure 媒体服务资产
- 创建 AMS 作业以审查视频
- 在内容审查器中创建视频评论

以下部分更详细地考虑了 `ProcessVideo()` 调用的某些单独进程。 

## <a name="compressing-the-video"></a>压缩视频

为了最大限度地减少网络流量，应用程序将视频文件转换为 H.264 (MPEG-4 AVC) 格式，并将其缩放到 640 像素的最大宽度。 推荐使用 H.264 编解码器，因为其效率（压缩率）较高。 压缩操作通过免费的 `ffmpeg` 命令行工具完成，该工具包含在 Visual Studio 解决方案的 `Lib` 文件夹中。 输入文件可以是 `ffmpeg` 支持的任何格式，包括最常用的视频文件格式和编解码器。

> [!NOTE]
> 使用命令行选项启动程序时，指定一个目录，其中包含要提交进行审查的视频文件。 系统会处理此目录中具有 `.mp4` 文件扩展名的所有文件。 若要处理其他文件扩展名，请更新 `Program.cs` 中的 `Main()` 方法，以包含所需的扩展名。

用于压缩单个视频文件的代码是 `AMSComponent.cs` 中的 `AmsComponent` 类。 负责此功能的方法是 `CompressVideo()`，如下所示。

    public string CompressVideo(string videoPath)
    {
        string ffmpegBlobUrl;
        if (!ValidatePreRequisites())
        {
            Console.WriteLine("Configurations check failed. Please cross check the configurations!");
            throw new Exception();
        }

        if (File.Exists(_configObj.FfmpegExecutablePath))
        {
            ffmpegBlobUrl = this._configObj.FfmpegExecutablePath;
        }
        else
        {
            Console.WriteLine("ffmpeg.exe is missing. Please check the Lib folder");
            throw new Exception();
        }

        string videoFilePathCom = videoPath.Split('.')[0] + "_c.mp4";
        ProcessStartInfo processStartInfo = new ProcessStartInfo();
        processStartInfo.WindowStyle = ProcessWindowStyle.Hidden;
        processStartInfo.FileName = ffmpegBlobUrl;
        processStartInfo.Arguments = "-i \"" + videoPath + "\" -vcodec libx264 -n -crf 32 -preset veryfast -vf scale=640:-1 -c:a aac -aq 1 -ac 2 -threads 0 \"" + videoFilePathCom + "\"";
        var process = Process.Start(processStartInfo);
        process.WaitForExit();
        process.Close();
        return videoFilePathCom;
    }

该代码执行以下步骤：

- 检查以确保 `App.config` 中的配置包含所有必要数据
- 检查以确保存在 `ffmpeg` 二进制文件
- 通过将 `_c.mp4` 附加到文件的基本名称（例如 `Example.mp4` -> `E>xample_c.mp4`）来生成输出文件名
- 生成命令行字符串以执行转换
- 使用命令行启动 `ffmpeg` 进程
- 等待处理视频

> [!NOTE]
> 如果知道视频已使用 H.264 进行压缩且尺寸合适，则可以重写 `CompressVideo()` 以跳过压缩。

该方法返回已压缩输出文件的文件名。

## <a name="uploading-and-moderating-the-video"></a>上传和审查视频

视频必须先存储在 Azure 媒体服务中，然后才能由内容审查服务进行处理。 `Program.cs` 中的 `Program` 类有一个简短的方法 `CreateVideoStreamingRequest()`，该方法返回一个对象，表示用于上传视频的流式处理请求。

    private static UploadVideoStreamRequest CreateVideoStreamingRequest(string compressedVideoFilePath)
    {
        return
            new UploadVideoStreamRequest
            {
                VideoStream = File.ReadAllBytes(compressedVideoFilePath),
                VideoName = Path.GetFileName(compressedVideoFilePath),
                EncodingRequest = new EncodingRequest()
                {
                    EncodingBitrate = AmsEncoding.AdaptiveStreaming
                },
                VideoFilePath = compressedVideoFilePath
            };
    }

生成的 `UploadVideoStreamRequest` 对象在 `UploadVideoStreamRequest.cs` 中定义（其父级 `UploadVideoRequest` 在 `UploadVideoRequest.cs` 中定义）。 此处未显示这些类；它们很短，仅用于保存压缩的视频数据及其相关信息。 另一个仅包含数据的类 `UploadAssetResult` (`UploadAssetResult.cs`) 用于保存上传过程的结果。 现在可以理解 `ProcessVideo()` 中的这些行了：

    UploadVideoStreamRequest uploadVideoStreamRequest = CreateVideoStreamingRequest(compressedVideoPath);
    UploadAssetResult uploadResult = new UploadAssetResult();

    if (generateVtt)
    {
        uploadResult.GenerateVTT = generateVtt;
    }
    Console.WriteLine("\nVideo moderation process started...");

    if (!videoModerator.CreateAzureMediaServicesJobToModerateVideo(uploadVideoStreamRequest, uploadResult))
    {
        Console.ForegroundColor = ConsoleColor.Red;
        Console.WriteLine("Video Review process failed.");
    }

这些行执行以下任务：

- 创建 `UploadVideoStreamRequest` 以上传压缩的视频
- 如果用户请求了文本脚本，则设置请求的 `GenerateVTT` 标志
- 调用 `CreateAzureMediaServicesJobToModerateVideo()` 以执行上传并接收结果

## <a name="deep-dive-into-video-moderation"></a>深入了解视频审查

`CreateAzureMediaServicesJobToModerateVideo()` 方法位于 `VideoModerator.cs` 中，它包含与 Azure 媒体服务交互的大量代码。 该方法的源代码显示在以下摘录中。

    public bool CreateAzureMediaServicesJobToModerateVideo(UploadVideoStreamRequest uploadVideoRequest, UploadAssetResult uploadResult)
    {
        asset = CreateAsset(uploadVideoRequest);
        uploadResult.VideoName = uploadVideoRequest.VideoName;
        // Encoding the asset , Moderating the asset, Generating transcript in parallel
        IAsset encodedAsset = null;
        //Creates the job for the tasks.
        IJob job = this._mediaContext.Jobs.Create("AMS Review Job");

        //Adding encoding task to job.
        ConfigureEncodeAssetTask(uploadVideoRequest.EncodingRequest, job);

        ConfigureContentModerationTask(job);

        //adding transcript task to job.
        if (uploadResult.GenerateVTT)
        {
            ConfigureTranscriptTask(job);
        }

        Stopwatch timer = new Stopwatch();
        timer.Start();
        //submit and execute job.
        job.Submit();
        job.GetExecutionProgressTask(new CancellationTokenSource().Token).Wait();
        timer.Stop();
        using (var sw = new StreamWriter(AmsConfigurations.logFilePath, true))
        {
            sw.WriteLine("AMS Job Elapsed Time: {0}", timer.Elapsed);
        }

        if (job.State == JobState.Error)
        {
            throw new Exception("Video moderation has failed due to AMS Job error.");
        }

        UploadAssetResult result = uploadResult;
        encodedAsset = job.OutputMediaAssets[0];
        result.ModeratedJson = GetCmDetail(job.OutputMediaAssets[1]);
        // Check for valid Moderated JSON
        var jsonModerateObject = JsonConvert.DeserializeObject<VideoModerationResult>(result.ModeratedJson);

        if (jsonModerateObject == null)
        {
            return false;
        }
        if (uploadResult.GenerateVTT)
        {
            GenerateTranscript(job.OutputMediaAssets.Last());
        }

        uploadResult.StreamingUrlDetails = PublishAsset(encodedAsset);
        string downloadUrl = GenerateDownloadUrl(asset, uploadVideoRequest.VideoName);
        uploadResult.StreamingUrlDetails.DownloadUri = downloadUrl;
        uploadResult.VideoName = uploadVideoRequest.VideoName;
        uploadResult.VideoFilePath = uploadVideoRequest.VideoFilePath;
        return true;
    }

此代码执行以下任务：

- 创建 AMS 作业以完成处理
- 添加用于对视频文件进行编码、审查以及生成文本脚本的任务
- 提交作业、上传文件并开始进行处理
- 检索审查结果、文本脚本（如果需要）和其他信息

## <a name="sample-video-moderation-response"></a>视频审查响应示例

视频审查作业的结果（请参阅[视频审查快速入门](video-moderation-api.md)）是一种包含审查结果的 JSON 数据结构。 这些结果包括细分的视频片段（镜头），每个片段均包含一些事件（剪辑），这些事件具有标记为待评论的关键帧。 系统根据每个关键帧包含成人内容或不雅内容的可能性对其进行评分。 以下示例展示了 JSON 响应：

    {
        "version": 2,
        "timescale": 90000,
        "offset": 0,
        "framerate": 50,
        "width": 1280,
        "height": 720,
        "totalDuration": 18696321,
        "fragments": [
        {
            "start": 0,
            "duration": 18000
        },
        {
            "start": 18000,
            "duration": 3600,
            "interval": 3600,
            "events": [
            [
            {
                "reviewRecommended": false,
                "adultScore": 0.00001,
                "racyScore": 0.03077,
                "index": 5,
                "timestamp": 18000,
                "shotIndex": 0
            }
            ]
        ]
        },
        {
            "start": 18386372,
            "duration": 119149,
            "interval": 119149,
            "events": [
            [
            {
                "reviewRecommended": true,
                "adultScore": 0.00000,
                "racyScore": 0.91902,
                "index": 5085,
                "timestamp": 18386372,
                "shotIndex": 62
            }
        ]
        ]
        }
    ]
    }

当设置 `GenerateVTT` 标志时，还会生成来自视频的音频脚本。

> [!NOTE]
> 控制台应用程序使用 [Azure Media Indexer API](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2) 根据上传视频的音频轨道生成脚本。结果以 WebVTT 格式提供。 有关此格式的详细信息，请参阅 [Web Video Text Tracks Format](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API)（Web 视频文本轨道格式）。


## <a name="creating-the-human-in-the-loop-review"></a>创建人为介入评论

审查过程会从视频返回一个关键帧列表，以及其音频轨道的脚本。 下一步是在内容审查器评论工具中为人工审查器创建评论。 回到 `Program.cs` 中的 `ProcessVideo()` 方法，你会看到对 `CreateVideoReviewInContentModerator()` 方法的调用。 此方法位于 `videoReviewApi` 类中，该类位于 `VideoReviewAPI.cs` 中，如下所示。

    public async Task<string> CreateVideoReviewInContentModerator(UploadAssetResult uploadAssetResult)
    {
    
        string reviewId = string.Empty;
        List<ProcessedFrameDetails> frameEntityList = framegenerator.CreateVideoFrames(uploadAssetResult);
        string path = uploadAssetResult.GenerateVTT == true ? this._amsConfig.FfmpegFramesOutputPath + Path.GetFileNameWithoutExtension (uploadAssetResult.VideoName) + "_aud_SpReco.vtt" : "";
        TranscriptScreenTextResult screenTextResult = new TranscriptScreenTextResult();
        
    if (File.Exists(path))
        {
            screenTextResult = await GenerateTextScreenProfanity(reviewId, path, frameEntityList);
            uploadAssetResult.Category1TextScore = screenTextResult.Category1Score;
            uploadAssetResult.Category2TextScore = screenTextResult.Category2Score;
            uploadAssetResult.Category3TextScore = screenTextResult.Category3Score;
            uploadAssetResult.Category1TextTag = screenTextResult.Category1Tag;
            uploadAssetResult.Category2TextTag = screenTextResult.Category2Tag;
            uploadAssetResult.Category3TextTag = screenTextResult.Category3Tag;
        }
        
        var reviewVideoRequestJson = CreateReviewRequestObject(uploadAssetResult, frameEntityList);
        if (string.IsNullOrWhiteSpace(reviewVideoRequestJson))
        {
            throw new Exception("Video review process failed in CreateVideoReviewInContentModerator");
        }
        
        reviewId = JsonConvert.DeserializeObject<List<string>>(ExecuteCreateReviewApi(reviewVideoRequestJson).Result).FirstOrDefault();
        frameEntityList = framegenerator.GenerateFrameImages(frameEntityList, uploadAssetResult, reviewId);
        await CreateAndPublishReviewInContentModerator(uploadAssetResult, frameEntityList, reviewId, path, screenTextResult);
        return reviewId;
    
    }

`CreateVideoReviewInContentModerator()` 调用其他数种方法来执行以下任务：

> [!NOTE]
> 控制台应用程序使用 [FFmpeg](https://ffmpeg.org/) 库生成缩略图。 这些缩略图（图像）对应于[视频审查输出](#sample-video-moderation-response)中的帧时间戳。

|任务|方法|文件|
|-|-|-|
|从视频中提取关键帧并创建它们的缩略图|`CreateVideoFrames()`<br>`GenerateFrameImages()`|`FrameGeneratorServices.cs`|
|扫描文本脚本（如果可行）以查找成人音频或不雅音频|`GenerateTextScreenProfanity()`| `VideoReviewAPI.cs`|
|准备并提交视频评论请求以供人工检查|`CreateReviewRequestObject()`<br> `ExecuteCreateReviewApi()`<br>`CreateAndPublishReviewInContentModerator()`|`VideoReviewAPI.cs`|

## <a name="video-review-default-view"></a>视频评论默认视图

以下屏幕展示了前述步骤的结果。

![视频评论默认视图](images/video-tutorial-default-view.PNG)

## <a name="transcript-generation"></a>脚本生成

到目前为止，本教程中提供的代码主要侧重于可视内容。 评论语音内容是一个单独的可选过程，如前文所述，它使用根据音频生成的脚本。 现在是时候看一下如何在评论过程中创建和使用文本脚本。 生成脚本的任务由 [Azure Media Indexer](https://docs.microsoft.com/azure/media-services/media-services-index-content) 服务执行。

应用程序执行以下任务：

|任务|方法|文件|
|-|-|-|
|确定是否要生成文本脚本|`Main()`<br>`GetUserInputs()`|`Program.cs`|
|如果是，则在审查过程中提交脚本作业|`ConfigureTranscriptTask()`|`VideoModerator.cs`|
|获取脚本的本地副本|`GenerateTranscript()`|`VideoModerator.cs`|
|标记包含不当音频的视频帧|`GenerateTextScreenProfanity()`<br>`TextScreen()`|`VideoReviewAPI.cs`|
|将结果添加到评论中|`UploadScreenTextResult()`<br>`ExecuteAddTranscriptSupportFile()`|`VideoReviewAPI.cs`|

### <a name="task-configuration"></a>任务配置

让我们直接提交脚本作业。 `CreateAzureMediaServicesJobToModerateVideo()`（已介绍）调用 `ConfigureTranscriptTask()`。

    private void ConfigureTranscriptTask(IJob job)
    {
        string mediaProcessorName = _amsConfigurations.MediaIndexer2MediaProcessor;
        IMediaProcessor processor = _mediaContext.MediaProcessors.GetLatestMediaProcessorByName(mediaProcessorName);

        string configuration = File.ReadAllText(_amsConfigurations.MediaIndexerConfigurationJson);
        ITask task = job.Tasks.AddNew("AudioIndexing Task", processor, configuration, TaskOptions.None);
        task.InputAssets.Add(asset);
        task.OutputAssets.AddNew("AudioIndexing Output Asset", AssetCreationOptions.None);
    }

从解决方案的 `Lib` 文件夹中的文件 `MediaIndexerConfig.json` 读取脚本任务的配置。 为配置文件和脚本过程的输出创建 AMS 资产。 当 AMS 作业运行时，此任务会根据视频文件的音频轨道创建文本脚本。

> [!NOTE]
> 示例应用程序仅识别美国英语语音。

### <a name="transcript-generation"></a>脚本生成

脚本作为 AMS 资产发布。 为了扫描脚本以筛选令人反感的内容，应用程序将从 Azure 媒体服务下载资产。 `CreateAzureMediaServicesJobToModerateVideo()` 调用此处所示的 `GenerateTranscript()` 来检索文件。

    public bool GenerateTranscript(IAsset asset)
    {
        try
        {
            var outputFolder = this._amsConfigurations.FfmpegFramesOutputPath;
            IAsset outputAsset = asset;
            IAccessPolicy policy = null;
            ILocator locator = null;
            policy = _mediaContext.AccessPolicies.Create("My 30 days readonly policy", TimeSpan.FromDays(360), AccessPermissions.Read);
            locator = _mediaContext.Locators.CreateLocator(LocatorType.Sas, outputAsset, policy, DateTime.UtcNow.AddMinutes(-5));
            DownloadAssetToLocal(outputAsset, outputFolder);
            locator.Delete();
            return true;
        }
        catch
        {   //TODO:  Logging
            Console.WriteLine("Exception occured while generating index for video.");
            throw;
        }
    }

在进行一些必要的 AMS 设置之后，通过调用 `DownloadAssetToLocal()`（一种将 AMS 资产复制到本地文件的泛型函数）来执行实际下载。

## <a name="transcript-moderation"></a>脚本审查

获得脚本后，对其进行扫描并用于评论中。 创建评论属于 `CreateVideoReviewInContentModerator()` 的范畴，它调用 `GenerateTextScreenProfanity()` 来执行该工作。 此方法转而调用包含大部分功能的 `TextScreen()`。 

`TextScreen()` 执行以下任务：

- 分析脚本的时间戳和字幕
- 提交每个字幕以进行文本审查
- 标记任何可能包含令人反感的语音内容的帧

让我们更详细地研究一下这些任务：

### <a name="initialize-the-code"></a>初始化代码

首先，初始化所有变量和集合。

    private async Task<TranscriptScreenTextResult> TextScreen(string filepath, List<ProcessedFrameDetails> frameEntityList)
    {
        List<TranscriptProfanity> profanityList = new List<TranscriptProfanity>();
        string responseContent = string.Empty;
        HttpResponseMessage response;
        bool category1Tag = false;
        bool category2Tag = false;
        bool category3Tag = false;
        double category1Score = 0;
        double category2Score = 0;
        double category3Score = 0;
        List<string> vttLines = File.ReadAllLines(filepath).Where(line => !line.Contains("NOTE Confidence:") && line.Length > 0).ToList();
        StringBuilder sb = new StringBuilder();
        List<CaptionScreentextResult> csrList = new List<CaptionScreentextResult>();
        CaptionScreentextResult captionScreentextResult = new CaptionScreentextResult() { Captions = new List<string>() };

        // Code from the next sections in the tutorial
    

### <a name="parse-the-transcript-for-captions"></a>分析脚本字幕

接下来，分析 VTT 格式的脚本的字幕和时间戳。 评论工具会在视频评论屏幕的“脚本”选项卡中显示这些字幕。 时间戳用于将字幕与相应的视频帧同步。

        // Code from the previous section(s) in the tutorial

        //
        // Parse the transcript
        //
        foreach (var line in vttLines.Skip(1))
        {
                if (line.Contains("-->"))
                {
                    if (sb.Length > 0)
                    {
                        captionScreentextResult.Captions.Add(sb.ToString());
                        sb.Clear();
                    }
                    if (captionScreentextResult.Captions.Count > 0)
                    {
                        csrList.Add(captionScreentextResult);
                        captionScreentextResult = new CaptionScreentextResult() { Captions = new List<string>() };
                    }
                    string[] times = line.Split(new string[] { "-->" }, StringSplitOptions.RemoveEmptyEntries);
                    string startTimeString = times[0].Trim();
                    string endTimeString = times[1].Trim();
                    int startTime = (int)TimeSpan.ParseExact(startTimeString, @"hh\:mm\:ss\.fff", CultureInfo.InvariantCulture).TotalMilliseconds;
                    int endTime = (int)TimeSpan.ParseExact(endTimeString, @"hh\:mm\:ss\.fff", CultureInfo.InvariantCulture).TotalMilliseconds;
                    captionScreentextResult.StartTime = startTime;
                    captionScreentextResult.EndTime = endTime;
                }
                else
                {
                    sb.Append(line);
                }
                if (sb.Length + line.Length > 1024)
                {
                    captionScreentextResult.Captions.Add(sb.ToString());
                    sb.Clear();
                }
            }
            if (sb.Length > 0)
            {
                captionScreentextResult.Captions.Add(sb.ToString());
            }
            if (captionScreentextResult.Captions.Count > 0)
            {
                csrList.Add(captionScreentextResult);
            }

            // Code from the following section in the quickstart

### <a name="moderate-captions-with-the-text-moderation-service"></a>使用文本审查服务审查字幕

接下来，我们使用内容审查器的文本 API 扫描已分析的文本字幕。

> [!NOTE]
> 内容审查器服务密钥有一个每秒请求数 (RPS) 速率限制。 如果超过该限制，SDK 会引发错误代码为 429 的异常。 
>
> 免费层密钥有一个 RPS 速率限制。

    //
    // Moderate the captions or cues
    //
    int waitTime = 1000;
    foreach (var csr in csrList)
    {
                bool captionAdultTextTag = false;
                bool captionRacyTextTag = false;
                bool captionOffensiveTextTag = false;
                bool retry = true;

                foreach (var caption in csr.Captions)
                {
                    while (retry)
                    {
                        try
                        {
                            System.Threading.Thread.Sleep(waitTime);
                            var lang = await CMClient.TextModeration.DetectLanguageAsync("text/plain", caption);
                            var oRes = await CMClient.TextModeration.ScreenTextWithHttpMessagesAsync(lang.DetectedLanguageProperty, "text/plain", caption, null, null, null, true);
                            response = oRes.Response;
                            responseContent = await response.Content.ReadAsStringAsync();
                            retry = false;
                        }
                        catch (Exception e)
                        {
                            if (e.Message.Contains("429"))
                            {
                                Console.WriteLine($"Moderation API call failed. Message: {e.Message}");
                                waitTime = (int)(waitTime * 1.5);
                                Console.WriteLine($"wait time: {waitTime}");
                            }
                            else
                            {
                                retry = false;
                                Console.WriteLine($"Moderation API call failed. Message: {e.Message}");
                            }
                        }
                    }
                    var jsonTextScreen = JsonConvert.DeserializeObject<TextScreen>(responseContent);
                    if (jsonTextScreen != null)
                    {
                        TranscriptProfanity transcriptProfanity = new TranscriptProfanity();
                        transcriptProfanity.TimeStamp = "";
                        List<Terms> transcriptTerm = new List<Terms>();
                        if (jsonTextScreen.Terms != null)
                        {
                            foreach (var term in jsonTextScreen.Terms)
                            {
                                var profanityobject = new Terms
                                {
                                    Term = term.Term,
                                    Index = term.Index
                                };
                                transcriptTerm.Add(profanityobject);
                            }
                            transcriptProfanity.Terms = transcriptTerm;
                            profanityList.Add(transcriptProfanity);
                        }
                        if (jsonTextScreen.Classification.Category1.Score > _amsConfig.Category1TextThreshold) captionAdultTextTag = true;
                        if (jsonTextScreen.Classification.Category2.Score > _amsConfig.Category2TextThreshold) captionRacyTextTag = true;
                        if (jsonTextScreen.Classification.Category3.Score > _amsConfig.Category3TextThreshold) captionOffensiveTextTag = true;
                        if (jsonTextScreen.Classification.Category1.Score > _amsConfig.Category1TextThreshold) category1Tag = true;
                        if (jsonTextScreen.Classification.Category2.Score > _amsConfig.Category2TextThreshold) category2Tag = true;
                        if (jsonTextScreen.Classification.Category3.Score > _amsConfig.Category3TextThreshold) category3Tag = true;
                        category1Score = jsonTextScreen.Classification.Category1.Score > category1Score ? jsonTextScreen.Classification.Category1.Score : category1Score;
                        category2Score = jsonTextScreen.Classification.Category2.Score > category2Score ? jsonTextScreen.Classification.Category2.Score : category2Score;
                        category3Score = jsonTextScreen.Classification.Category3.Score > category3Score ? jsonTextScreen.Classification.Category3.Score : category3Score;
                    }
                    foreach (var frame in frameEntityList.Where(x => x.TimeStamp >= csr.StartTime && x.TimeStamp <= csr.EndTime))
                    {
                        frame.IsAdultTextContent = captionAdultTextTag;
                        frame.IsRacyTextContent = captionRacyTextTag;
                        frame.IsOffensiveTextContent = captionOffensiveTextTag;
                    }
                }
            }
            TranscriptScreenTextResult screenTextResult = new TranscriptScreenTextResult()
            {
                TranscriptProfanity = profanityList,
                Category1Tag = category1Tag,
                Category2Tag = category2Tag,
                Category3Tag = category3Tag,
                Category1Score = category1Score,
                Category2Score = category2Score,
                Category3Score = category3Score
            };
            return screenTextResult;
    }

### <a name="breaking-down-the-text-moderation-step"></a>分解文本审查步骤

`TextScreen()` 是一种很复杂的方法，因此，让我们逐一分解该方法。

1. 首先，该方法逐行读取脚本文件。 它忽略空白行以及包含具有置信度分数的 `NOTE` 的行。 它从文件的*提示*中提取时间戳和文本项。 提示表示音频轨道中的文本，包括开始时间和结束时间。 提示以带有字符串 `-->` 的时间戳行开头。 后面跟有一行或多行文本。

1. `CaptionScreentextResult`（在 `TranscriptProfanity.cs` 中定义）的实例用于保存从每个提示分析得到的信息。  当检测到新的时间戳行，或者达到 1024 个字符的最大文本长度时，会向 `csrList` 添加新的 `CaptionScreentextResult`。 

1. 接下来，该方法将各提示提交给文本审查 API。 它调用 `Microsoft.Azure.CognitiveServices.ContentModerator` 程序集中定义的 `ContentModeratorClient.TextModeration.DetectLanguageAsync()` 和 `ContentModeratorClient.TextModeration.ScreenTextWithHttpMessagesAsync()`。 为了避免速率限制，该方法在提交每个提示之前暂停一秒钟。

1. 在从文本审查服务收到结果之后，该方法对结果进行分析，看它们是否满足置信度阈值。 这些值作为 `OffensiveTextThreshold`、`RacyTextThreshold` 和 `AdultTextThreshold` 存储在 `App.config` 中。 最后，还会存储令人反感的词语本身。 提示的时间范围内的所有帧均标记为包含冒犯性、不雅和/或成人文本。

1. `TextScreen()` 返回一个 `TranscriptScreenTextResult` 实例，其中包含整个视频的文本审查结果。 该对象包含各种令人反感的内容的标志和分数，以及所有令人反感的词语的列表。 调用方 `CreateVideoReviewInContentModerator()` 通过调用 `UploadScreenTextResult()` 将此信息附加到评论中，以供评论人员使用。
 
## <a name="video-review-transcript-view"></a>视频评论脚本视图

以下屏幕展示了脚本文生成和审查步骤的结果。

![视频审查脚本视图](images/video-tutorial-transcript-view.PNG)

## <a name="program-output"></a>程序输出

该程序的以下命令行输出展示了已完成的各种任务。 此外，审查结果（采用 JSON 格式）和语音脚本位于原始视频文件所在的目录中。

    Microsoft.ContentModerator.AMSComponentClient
    Enter the fully qualified local path for Uploading the video :
    "Your File Name.MP4"
    Generate Video Transcript? [y/n] : y
    
    Video compression process started...
    Video compression process completed...
    
    Video moderation process started...
    Video moderation process completed...
    
    Video review process started...
    Video Frames Creation inprogress...
    Frames(83) created successfully.
    Review Created Successfully and the review Id 201801va8ec2108d6e043229ba7a9e6373edec5
    Video review successfully completed...
    
    Total Elapsed Time: 00:05:56.8420355


## <a name="next-steps"></a>后续步骤

[下载本教程的 Visual Studio 解决方案](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp)、示例文件和所需的库，开始进行集成。
