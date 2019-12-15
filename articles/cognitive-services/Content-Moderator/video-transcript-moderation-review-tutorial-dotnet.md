---
title: 教程：在 .NET 中审查视频和脚本 - 内容审查器
titleSuffix: Azure Cognitive Services
description: 本教程可帮助你了解如何使用机器辅助审查和人工介入评论创建，来生成完整的视频和脚本审查解决方案。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 2fbd4270221cb23a4f99a0f8155bb1de76472f31
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976972"
---
# <a name="tutorial-video-and-transcript-moderation"></a>教程：视频和脚本审查

本教程介绍如何使用机器辅助审查和人工评审集成生成完整的视频和脚本审查解决方案。

本教程演示如何：

> [!div class="checklist"]
> - 压缩输入视频以加快处理速度
> - 审查视频以获得有洞察力的镜头和帧
> - 使用帧时间戳创建缩略图（图像）
> - 提交时间戳和缩略图以创建视频评论
> - 使用 Media Indexer API 将视频语音转换为文本（脚本）
> - 使用文本审查服务审查脚本
> - 将已审查的脚本添加到视频评论中

## <a name="prerequisites"></a>先决条件

- 在[内容审查器评审工具](https://contentmoderator.cognitive.microsoft.com/)网站上注册并创建自定义标记。 执行此步骤时如需帮助，请参阅[使用标记](Review-Tool-User-Guide/tags.md)。

    ![屏幕截图：视频审查自定义标记](images/video-tutorial-custom-tags.png)
- 若要运行示例应用程序，需要有 Azure 帐户、Azure 媒体服务帐户、Azure 内容审查器资源，以及 Azure Active Directory 凭据。 有关如何获取这些资源的说明，请查看[视频审查 API](video-moderation-api.md) 指南。
- 从 GitHub 下载[视频审查控制台应用程序](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp)。

## <a name="enter-credentials"></a>输入凭据

编辑文件 `App.config` 并添加 Active Directory 租户名称、服务终结点和用 `#####` 表示的订阅密钥。 你需要以下信息：

|密钥|说明|
|-|-|
|`AzureMediaServiceRestApiEndpoint`|Azure 媒体服务 (AMS) API 的终结点|
|`ClientSecret`|Azure 媒体服务的订阅密钥|
|`ClientId`|Azure 媒体服务的客户端 ID|
|`AzureAdTenantName`|表示组织的 Active Directory 租户名称|
|`ContentModeratorReviewApiSubscriptionKey`|内容审查器评论 API 的订阅密钥|
|`ContentModeratorApiEndpoint`|内容审查器 API 的终结点|
|`ContentModeratorTeamId`|内容审查器团队 ID|

## <a name="examine-the-main-code"></a>检查主代码

`Program.cs` 中的 `Program` 类是视频审查应用程序的主要入口点。

### <a name="methods-of-program-class"></a>Program 类的方法

|方法|说明|
|-|-|
|`Main`|分析命令行、收集用户输入并开始进行处理。|
|`ProcessVideo`|压缩、上传、审查和创建视频评论。|
|`CreateVideoStreamingRequest`|创建用于上传视频的流|
|`GetUserInputs`|收集用户输入；在没有命令行选项时使用|
|`Initialize`|初始化审查过程所需的对象|

### <a name="the-main-method"></a>Main 方法

`Main()` 是执行起始位置，因此，将从这里开始了解视频审查过程。

[!code-csharp[Main](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=20-24,42-52,54-74)]

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

[!code-csharp[ProcessVideo](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=76-118)]

`ProcessVideo()` 方法非常简单。 它按顺序执行以下操作：

- 压缩视频
- 将视频上传到 Azure 媒体服务资产
- 创建 AMS 作业以审查视频
- 在内容审查器中创建视频评论

以下部分更详细地考虑了 `ProcessVideo()` 调用的某些单独进程。 

## <a name="compress-the-video"></a>压缩视频

为了最大限度地减少网络流量，应用程序将视频文件转换为 H.264 (MPEG-4 AVC) 格式，并将其缩放到 640 像素的最大宽度。 推荐使用 H.264 编解码器，因为其效率（压缩率）较高。 压缩操作通过免费的 `ffmpeg` 命令行工具完成，该工具包含在 Visual Studio 解决方案的 `Lib` 文件夹中。 输入文件可以是 `ffmpeg` 支持的任何格式，包括最常用的视频文件格式和编解码器。

> [!NOTE]
> 使用命令行选项启动程序时，指定一个目录，其中包含要提交进行审查的视频文件。 系统会处理此目录中具有 `.mp4` 文件扩展名的所有文件。 若要处理其他文件扩展名，请更新 `Program.cs` 中的 `Main()` 方法，以包含所需的扩展名。

用于压缩单个视频文件的代码是 `AMSComponent.cs` 中的 `AmsComponent` 类。 负责此功能的方法是 `CompressVideo()`，如下所示。

[!code-csharp[CompressVideo](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/AMSComponent.cs?range=31-59)]

该代码执行以下步骤：

- 检查以确保 `App.config` 中的配置包含所有必要数据
- 检查以确保存在 `ffmpeg` 二进制文件
- 通过将 `_c.mp4` 附加到文件的基本名称（例如 `Example.mp4` -> `Example_c.mp4`）来生成输出文件名
- 生成命令行字符串以执行转换
- 使用命令行启动 `ffmpeg` 进程
- 等待处理视频

> [!NOTE]
> 如果知道视频已使用 H.264 进行压缩且尺寸合适，则可以重写 `CompressVideo()` 以跳过压缩。

该方法返回已压缩输出文件的文件名。

## <a name="upload-and-moderate-the-video"></a>上传和审查视频

视频必须先存储在 Azure 媒体服务中，然后才能由内容审查服务进行处理。 `Program.cs` 中的 `Program` 类有一个简短的方法 `CreateVideoStreamingRequest()`，该方法返回一个对象，表示用于上传视频的流式处理请求。

[!code-csharp[CreateVideoStreamingRequest](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=120-133)]

生成的 `UploadVideoStreamRequest` 对象在 `UploadVideoStreamRequest.cs` 中定义（其父级 `UploadVideoRequest` 在 `UploadVideoRequest.cs` 中定义）。 此处未显示这些类；它们很短，仅用于保存压缩的视频数据及其相关信息。 另一个仅包含数据的类 `UploadAssetResult` (`UploadAssetResult.cs`) 用于保存上传过程的结果。 现在可以理解 `ProcessVideo()` 中的这些行了：

[!code-csharp[ProcessVideoSnippet](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=91-104)]

这些行执行以下任务：

- 创建 `UploadVideoStreamRequest` 以上传压缩的视频
- 如果用户请求了文本脚本，则设置请求的 `GenerateVTT` 标志
- 调用 `CreateAzureMediaServicesJobToModerateVideo()` 以执行上传并接收结果

## <a name="examine-video-moderation-code"></a>检查视频审查代码

`CreateAzureMediaServicesJobToModerateVideo()` 方法位于 `VideoModerator.cs` 中，它包含与 Azure 媒体服务交互的大量代码。 该方法的源代码显示在以下摘录中。

[!code-csharp[CreateAzureMediaServicesJobToModerateVideo](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoModerator.cs?range=230-283)]

此代码执行以下任务：

- 创建 AMS 作业以完成处理
- 添加用于对视频文件进行编码、审查以及生成文本脚本的任务
- 提交作业、上传文件并开始进行处理
- 检索审查结果、文本脚本（如果需要）和其他信息

## <a name="sample-video-moderation-output"></a>视频审查输出示例

视频审查作业的结果（请参阅[视频审查快速入门](video-moderation-api.md)）是一种包含审查结果的 JSON 数据结构。 这些结果包括细分的视频片段（镜头），每个片段均包含一些事件（剪辑），这些事件具有标记为待评论的关键帧。 系统根据每个关键帧包含成人内容或不雅内容的可能性对其进行评分。 以下示例展示了 JSON 响应：

```json
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
```

当设置 `GenerateVTT` 标志时，还会生成来自视频的音频脚本。

> [!NOTE]
> 控制台应用程序使用 [Azure Media Indexer API](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2) 根据上传视频的音频轨道生成脚本。结果以 WebVTT 格式提供。 有关此格式的详细信息，请参阅 [Web Video Text Tracks Format](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API)（Web 视频文本轨道格式）。

## <a name="create-a-human-review"></a>创建人工评审

审查过程会从视频返回一个关键帧列表，以及其音频轨道的脚本。 下一步是在内容审查器评论工具中为人工审查器创建评论。 回到 `Program.cs` 中的 `ProcessVideo()` 方法，你会看到对 `CreateVideoReviewInContentModerator()` 方法的调用。 此方法位于 `videoReviewApi` 类中，该类位于 `VideoReviewAPI.cs` 中，如下所示。

[!code-csharp[CreateVideoReviewInContentModerator](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=42-69)]

`CreateVideoReviewInContentModerator()` 调用其他数种方法来执行以下任务：

> [!NOTE]
> 控制台应用程序使用 [FFmpeg](https://ffmpeg.org/) 库生成缩略图。 这些缩略图（图像）对应于视频审查输出中的帧时间戳。

|任务|方法|文件|
|-|-|-|
|从视频中提取关键帧并创建它们的缩略图|`CreateVideoFrames()`<br>`GenerateFrameImages()`|`FrameGeneratorServices.cs`|
|扫描文本脚本（如果可行）以查找成人音频或不雅音频|`GenerateTextScreenProfanity()`| `VideoReviewAPI.cs`|
|准备并提交视频评论请求以供人工检查|`CreateReviewRequestObject()`<br> `ExecuteCreateReviewApi()`<br>`CreateAndPublishReviewInContentModerator()`|`VideoReviewAPI.cs`|

以下屏幕展示了前述步骤的结果。

![视频评论默认视图](images/video-tutorial-default-view.PNG)

## <a name="process-the-transcript"></a>处理脚本

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

[!code-csharp[ConfigureTranscriptTask](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoModerator.cs?range=295-304)]

从解决方案的 `Lib` 文件夹中的文件 `MediaIndexerConfig.json` 读取脚本任务的配置。 为配置文件和脚本过程的输出创建 AMS 资产。 当 AMS 作业运行时，此任务会根据视频文件的音频轨道创建文本脚本。

> [!NOTE]
> 示例应用程序仅识别美国英语语音。

### <a name="transcript-generation"></a>脚本生成

脚本作为 AMS 资产发布。 为了扫描脚本以筛选令人反感的内容，应用程序将从 Azure 媒体服务下载资产。 `CreateAzureMediaServicesJobToModerateVideo()` 调用此处所示的 `GenerateTranscript()` 来检索文件。

[!code-csharp[GenerateTranscript](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoModerator.cs?range=351-370)]

在进行一些必要的 AMS 设置之后，通过调用 `DownloadAssetToLocal()`（一种将 AMS 资产复制到本地文件的泛型函数）来执行实际下载。

## <a name="moderate-the-transcript"></a>审查脚本

获得脚本后，对其进行扫描并用于评论中。 创建评论属于 `CreateVideoReviewInContentModerator()` 的范畴，它调用 `GenerateTextScreenProfanity()` 来执行该工作。 此方法转而调用包含大部分功能的 `TextScreen()`。

`TextScreen()` 执行以下任务：

- 分析脚本的时间戳和字幕
- 提交每个字幕以进行文本审查
- 标记任何可能包含令人反感的语音内容的帧

让我们更详细地研究一下这些任务：

### <a name="initialize-the-code"></a>初始化代码

首先，初始化所有变量和集合。

[!code-csharp[TextScreen](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=515-527)]

### <a name="parse-the-transcript-for-captions"></a>分析脚本字幕

接下来，分析 VTT 格式的脚本的字幕和时间戳。 评论工具会在视频评论屏幕的“脚本”选项卡中显示这些字幕。 时间戳用于将字幕与相应的视频帧同步。

[!code-csharp[TextScreen2](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=528-567)]

### <a name="moderate-captions-with-the-text-moderation-service"></a>使用文本审查服务审查字幕

接下来，我们使用内容审查器的文本 API 扫描已分析的文本字幕。

> [!NOTE]
> 内容审查器服务密钥有一个每秒请求数 (RPS) 速率限制。 如果超过该限制，SDK 会引发错误代码为 429 的异常。
>
> 免费层密钥有一个 RPS 速率限制。

[!code-csharp[TextScreen3](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=568-653)]

### <a name="text-moderation-breakdown"></a>文本审查细分

`TextScreen()` 是一种很复杂的方法，因此，让我们逐一分解该方法。

1. 首先，该方法逐行读取脚本文件。 它忽略空白行以及包含具有置信度分数的 `NOTE` 的行。 它从文件的*提示*中提取时间戳和文本项。 提示表示音频轨道中的文本，包括开始时间和结束时间。 提示以带有字符串 `-->` 的时间戳行开头。 后面跟有一行或多行文本。

1. `CaptionScreentextResult`（在 `TranscriptProfanity.cs` 中定义）的实例用于保存从每个提示分析得到的信息。  当检测到新的时间戳行，或者达到 1024 个字符的最大文本长度时，会向 `csrList` 添加新的 `CaptionScreentextResult`。 

1. 接下来，该方法将各提示提交给文本审查 API。 它调用 `Microsoft.Azure.CognitiveServices.ContentModerator` 程序集中定义的 `ContentModeratorClient.TextModeration.DetectLanguageAsync()` 和 `ContentModeratorClient.TextModeration.ScreenTextWithHttpMessagesAsync()`。 为了避免速率限制，该方法在提交每个提示之前暂停一秒钟。

1. 在从文本审查服务收到结果之后，该方法对结果进行分析，看它们是否满足置信度阈值。 这些值作为 `OffensiveTextThreshold`、`RacyTextThreshold` 和 `AdultTextThreshold` 存储在 `App.config` 中。 最后，还会存储令人反感的词语本身。 提示的时间范围内的所有帧均标记为包含冒犯性、不雅和/或成人文本。

1. `TextScreen()` 返回一个 `TranscriptScreenTextResult` 实例，其中包含整个视频的文本审查结果。 该对象包含各种令人反感的内容的标志和分数，以及所有令人反感的词语的列表。 调用方 `CreateVideoReviewInContentModerator()` 通过调用 `UploadScreenTextResult()` 将此信息附加到评论中，以供评论人员使用。

以下屏幕展示了脚本文生成和审查步骤的结果。

![视频审查脚本视图](images/video-tutorial-transcript-view.PNG)

## <a name="program-output"></a>程序输出

该程序的以下命令行输出展示了已完成的各种任务。 此外，审查结果（采用 JSON 格式）和语音脚本位于原始视频文件所在的目录中。

```console
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
```

## <a name="next-steps"></a>后续步骤

在本教程中，你设置了一个应用程序来审查视频内容（包括脚本内容）并在评审工具中创建评审。 接下来，请了解有关视频审查的详细信息。

> [!div class="nextstepaction"]
> [视频审查](./video-moderation-human-review.md)
