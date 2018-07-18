---
title: Azure 内容审查器 - 使用 .NET 创建视频审查 | Microsoft Docs
description: 如何使用适用于 .NET 的 Azure 内容审查器 SDK 创建视频审查
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/18/2018
ms.author: sajagtap
ms.openlocfilehash: cb487314b8695f3676fdb22a9d7e3ec5ca3ed9f2
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365559"
---
# <a name="create-video-reviews-using-net"></a>使用 .NET 创建视频审查

本文提供了信息和代码示例，帮助你快速开始使用带 C# 的内容审查器 SDK 来执行以下操作：

- 为人工审查器创建视频审查
- 将帧添加到审查
- 获取帧以供审查 
- 获取审查状态和详细信息
- 发布审查

## <a name="prerequisites"></a>先决条件

本文假定[已审查视频（请见快速入门）](video-moderation-api.md)并已获取响应数据。 你需要它来为人工审查器创建基于帧的审查。

本文还假定你已熟悉 Visual Studio 和 C#。

### <a name="sign-up-for-content-moderator-services"></a>注册内容审查器服务

在可以通过 REST API 或 SDK 使用内容审查器服务之前，需要一个订阅密钥。

在内容审查器仪表板中，可以在“设置” > “凭据” > “API” > “试用 Ocp-Apim-Subscription-Key”中查找订阅密钥。 有关详细信息，请参阅[概述](overview.md)。

### <a name="prepare-your-video-and-the-video-frames-for-review"></a>准备视频和视频帧以供审查

必须在线发布要审查的视频和示例视频帧，因为你需要它们的 URL。

> [!NOTE]
> 程序使用带随机成人/不雅分数的视频中手动保存的屏幕截图显示审查 API 的使用。 在实际情况下，使用[视频审查输出](video-moderation-api.md#run-the-program-and-review-the-output)创建图像并分配分数。 

对于视频，需要一个流式处理终结点，以便审查工具在播放器视图中播放此视频。

![视频演示缩略图](images/ams-video-demo-view.PNG)

- 在此 [Azure 媒体服务演示](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest)页面上为清单 URL 复制 **URL**。

对于视频帧（图像），使用下列图像：

![视频帧缩略图 1](images/ams-video-frame-thumbnails-1.PNG) | ![视频帧缩略图 2](images/ams-video-frame-thumbnails-2.PNG) | ![视频帧缩略图 3](images/ams-video-frame-thumbnails-3.PNG) |
| :---: | :---: | :---: |
[帧 1](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame1-00-17.PNG) | [帧 2](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-2-01-04.PNG) | [帧 3](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-3-02-24.PNG) |

## <a name="create-your-visual-studio-project"></a>创建 Visual Studio 项目

1. 向解决方案添加新的控制台应用 (.NET Framework) 项目。

1. 为项目“VideoReviews”命名。

1. 选择此项目作为解决方案的单个启动项目。

### <a name="install-required-packages"></a>安装所需程序包

为 TermLists 项目安装下列 NuGet 包。

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Microsoft.Rest.ClientRuntime.Azure
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>更新程序的 using 语句

如下所示修改程序的 using 语句。

    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Threading;
    using Microsoft.Azure.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator.Models;
    using Newtonsoft.Json;


### <a name="add-private-properties"></a>添加私有属性

添加下列私有属性到命名空间 VideoReviews、类程序。

如有指示，替换这些属性的实例值。


    namespace VideoReviews
    {
        class Program
        {
            // NOTE: Replace this example location with the location for your Content Moderator account.
            /// <summary>
            /// The region/location for your Content Moderator account, 
            /// for example, westus.
            /// </summary>
            private static readonly string AzureRegion = "YOUR CONTENT MODERATOR REGION";

            // NOTE: Replace this example key with a valid subscription key.
            /// <summary>
            /// Your Content Moderator subscription key.
            /// </summary>
            private static readonly string CMSubscriptionKey = "YOUR CONTENT MODERATOR KEY";

            // NOTE: Replace this example team name with your Content Moderator team name.
            /// <summary>
            /// The name of the team to assign the job to.
            /// </summary>
            /// <remarks>This must be the team name you used to create your 
            /// Content Moderator account. You can retrieve your team name from
            /// the Conent Moderator web site. Your team name is the Id associated 
            /// with your subscription.</remarks>
            public static readonly string TeamName = "YOUR CONTENT MODERATOR TEAM ID";

            /// <summary>
            /// The base URL fragment for Content Moderator calls.
            /// </summary>
            private static readonly string AzureBaseURL =
                $"{AzureRegion}.api.cognitive.microsoft.com";

            /// <summary>
            /// The minimum amount of time, in milliseconds, to wait between calls
            /// to the Content Moderator APIs.
            /// </summary>
            private const int throttleRate = 2000;


### <a name="create-content-moderator-client-object"></a>创建内容审查器客户端对象

添加下列方法定义到命名空间 VideoReviews、类程序。

    /// <summary>
    /// Returns a new Content Moderator client for your subscription.
    /// </summary>
    /// <returns>The new client.</returns>
    /// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
    /// When you have finished using the client,
    /// you should dispose of it either directly or indirectly. </remarks>
    public static ContentModeratorClient NewClient()
    {
        return new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey))
        {
            BaseUrl = AzureBaseURL
        };
    }

## <a name="create-a-video-review"></a>创建视频审查

使用 ContentModeratorClient.Reviews.CreateVideoReviews 创建视频审查。 有关详细信息，请参阅 [API 参考](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4)。

CreateVideoReviews 具有以下必需参数：
1. 一个包含应为“应用程序/json”的 MIME 类型的字符串。 
1. 你的内容审查器团队名称。
1. 一个 IList<CreateVideoReviewsBodyItem>对象。 每个 CreateVideoReviewsBodyItem  对象表示一次视频审查。 本快速入门教程每次创建一个审查。

“CreateVideoReviewsBodyItem”具有多个属性。 至少设置以下属性：
- Content。 将要审查的视频的 URL。
- ContentId。 要分配给视频审查的 ID。
- Status。 将此值设为“未发布”。 如果未进行设置，则默认为“正在等待审查”，这意味着视频审查已发布且正在等待人工审查。 视频审查发布后，无法再添加视频帧、脚本或其脚本审查结果。

> [!NOTE]
> “CreateVideoReviews”返回 IList<string>。 这些字符串中的每一个都包含视频审查的 ID。 这些 ID 是 GUID，与“ContentId”属性值不同。 

添加下列方法定义到命名空间 VideoReviews、类程序。

    /// <summary>
    /// Create a video review. For more information, see the API reference:
    /// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="id">The ID to assign to the video review.</param>
    /// <param name="content">The URL of the video to review.</param>
    /// <returns>The ID of the video review.</returns>
    private static string CreateReview(ContentModeratorClient client, string id, string content)
    {
        Console.WriteLine("Creating a video review.");

        List<CreateVideoReviewsBodyItem> body = new List<CreateVideoReviewsBodyItem>() {
            new CreateVideoReviewsBodyItem
            {
                Content = content,
                ContentId = id,
                /* Note: to create a published review, set the Status to "Pending".
                However, you cannot add video frames or a transcript to a published review. */
                Status = "Unpublished",
            }
        };

        var result = client.Reviews.CreateVideoReviews("application/json", TeamName, body);

        Thread.Sleep(throttleRate);

        // We created only one review.
        return result[0];
    }

> [!NOTE]
> 内容审查器服务密钥有每秒的请求数 (RPS) 速率限制，如果超出限制，SDK 则会引发 429 错误代码的异常。 
>
> 免费层密钥有一个 RPS 速率限制。

## <a name="add-video-frames-to-the-video-review"></a>将视频帧添加到视频审查

使用“ContentModeratorClient.Reviews.AddVideoFrameUrl”（如果在线托管视频帧）或“ContentModeratorClient.Reviews.AddVideoFrameStream”（如果在本地托管视频帧）将视频帧添加到视频审查。 本快速入门教程假定的是在线托管视频帧，因此使用“AddVideoFrameUrl”。 有关详细信息，请参阅 [API 参考](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b76ae7151f0b10d451fd)。

“AddVideoFrameUrl”具有以下必需参数：
1. 一个包含应为“应用程序/json”的 MIME 类型的字符串。
1. 你的内容审查器团队名称。
1. 由“CreateVideoReviews”返回的视频审查 ID。
1. 一个 IList<VideoFrameBodyItem>对象。 每个“VideoFrameBodyItem”对象表示一个视频帧。

“VideoFrameBodyItem”具有以下属性：
- Timestamp。 一个包含视频帧在视频中所用时间（以秒为单位）的字符串。
- FrameImage。 视频帧的 URL。
- Metadata。 一个 IList<VideoFrameBodyItemMetadataItem>。 “VideoFrameBodyItemMetadataItem”只是一个键/值对。 有效键包括：
- reviewRecommended。 如果推荐视频帧的人工审查，则为 True。
- adultScore。 评估视频帧中成人内容严重性的值，范围从 0 到 1。
- a。 如果视频包含成人内容，则为 True。
- racyScore。 评估视频帧中不雅内容严重性的值，范围从 0 到 1。
- r。 如果视频帧包含不雅内容，则为 True。
- ReviewerResultTags。 一个 IList<VideoFrameBodyItemReviewerResultTagsItem>。 “VideoFrameBodyItemReviewerResultTagsItem”只是一个键/值对。 应用程序可以使用这些标记来组织视频帧。

> [!NOTE]
> 本快速入门教程为“adultScore”和“racyScore”属性生成随机值。 在生产应用程序中，将从[视频审查服务](video-moderation-api.md)中获取这些值，部署为 Azure 媒体服务。

添加下列方法定义到命名空间 VideoReviews、类程序。

    <summary>
    /// Create a video frame to add to a video review after the video review is created.
    /// </summary>
    /// <param name="url">The URL of the video frame image.</param>
    /// <returns>The video frame.</returns>
    private static VideoFrameBodyItem CreateFrameToAddToReview(string url, string timestamp_seconds)
    {
        // We generate random "adult" and "racy" scores for the video frame.
        Random rand = new Random();

        var frame = new VideoFrameBodyItem
        {
            // The timestamp is measured in milliseconds. Convert from seconds.
            Timestamp = (int.Parse(timestamp_seconds) * 1000).ToString(),
            FrameImage = url,

            Metadata = new List<VideoFrameBodyItemMetadataItem>
            {
                new VideoFrameBodyItemMetadataItem("reviewRecommended", "true"),
                new VideoFrameBodyItemMetadataItem("adultScore", rand.NextDouble().ToString()),
                new VideoFrameBodyItemMetadataItem("a", "false"),
                new VideoFrameBodyItemMetadataItem("racyScore", rand.NextDouble().ToString()),
                new VideoFrameBodyItemMetadataItem("r", "false")
            },

            ReviewerResultTags = new List<VideoFrameBodyItemReviewerResultTagsItem>()
            {
                new VideoFrameBodyItemReviewerResultTagsItem("tag1", "value1")
            }
        };

        return frame;
    }

    /// <summary>
    /// Add a video frame to the indicated video review. For more information, see the API reference:
    /// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b76ae7151f0b10d451fd
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="review_id">The video review ID.</param>
    /// <param name="url">The URL of the video frame image.</param>
    static void AddFrame(ContentModeratorClient client, string review_id, string url, string timestamp_seconds)
    {
        Console.WriteLine("Adding a frame to the review with ID {0}.", review_id);

        var frames = new List<VideoFrameBodyItem>()
        {
            CreateFrameToAddToReview(url, timestamp_seconds)
        };
            
        client.Reviews.AddVideoFrameUrl("application/json", TeamName, review_id, frames);

        Thread.Sleep(throttleRate);
    

## <a name="get-video-frames-for-video-review"></a>为视频审查获取视频帧

可使用“ContentModeratorClient.Reviews.GetVideoFrames”为视频审查获取视频帧。 “GetVideoFrames”具有以下必需参数：
1. 你的内容审查器团队名称。
1. 由“CreateVideoReviews”返回的视频审查 ID。
1. 待获取的首个视频帧的从零开始的索引。
1. 待获取的视频帧的数字。

添加下列方法定义到命名空间 VideoReviews、类程序。

    /// <summary>
    /// Get the video frames assigned to the indicated video review.  For more information, see the API reference:
    /// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7ba43e7151f0b10d45200
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="review_id">The video review ID.</param>
    static void GetFrames(ContentModeratorClient client, string review_id)
    {
        Console.WriteLine("Getting frames for the review with ID {0}.", review_id);

        Frames result = client.Reviews.GetVideoFrames(TeamName, review_id, 0, Int32.MaxValue);
        Console.WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));

        Thread.Sleep(throttleRate);
    }

## <a name="get-video-review-information"></a>获取视频审查信息

使用“ContentModeratorClient.Reviews.GetReview”获取视频审查信息。 “GetReview”具有以下必需参数：
1. 你的内容审查器团队名称。
1. 由“CreateVideoReviews”返回的视频审查 ID。

添加下列方法定义到命名空间 VideoReviews、类程序。

    /// <summary>
    /// Get the information for the indicated video review. For more information, see the reference API:
    /// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="review_id">The video review ID.</param>
    private static void GetReview(ContentModeratorClient client, string review_id)
    {
        Console.WriteLine("Getting the status for the review with ID {0}.", review_id);

        var result = client.Reviews.GetReview(ModeratorHelper.Clients.TeamName, review_id);
        Console.WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));

        Thread.Sleep(throttleRate);
    }

## <a name="publish-video-review"></a>发布视频审查

使用“ContentModeratorClient.Reviews.PublishVideoReview”发布视频审查。 “PublishVideoReview”具有以下必需参数：
1. 你的内容审查器团队名称。
1. 由“CreateVideoReviews”返回的视频审查 ID。

添加下列方法定义到命名空间 VideoReviews、类程序。

    /// <summary>
    /// Publish the indicated video review. For more information, see the reference API:
    /// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7bb29e7151f0b10d45201
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="review_id">The video review ID.</param>
    private static void PublishReview(ContentModeratorClient client, string review_id)
    {
        Console.WriteLine("Publishing the review with ID {0}.", review_id);
        client.Reviews.PublishVideoReview(TeamName, review_id);
        Thread.Sleep(throttleRate);
    }

## <a name="putting-it-all-together"></a>汇总

将“Main”方法定义添加到命令空间 VideoReviews、类程序。 最后，关闭程序类和 VideoReviews 命名空间。

    static void Main(string[] args)
    {
        using (ContentModeratorClient client = NewClient())
        {
            // Create a review with the content pointing to a streaming endpoint (manifest)
            var streamingcontent = "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest";
            string review_id = CreateReview(client, "review1", streamingcontent);

            var frame1_url = "https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame1-00-17.PNG";
            var frame2_url = "https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-2-01-04.PNG";
            var frame3_url = "https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-3-02-24.PNG";

            // Add the frames from 17, 64, and 144 seconds.
            AddFrame(client, review_id, frame1_url, "17");
            AddFrame(client, review_id, frame2_url, "64");
            AddFrame(client, review_id, frame3_url, "144");

            // Get frames information and show
            GetFrames(client, review_id);
            GetReview(client, review_id);

            // Publish the review
            PublishReview(client, review_id);

            Console.WriteLine("Open your Content Moderator Dashboard and select Review > Video to see the review.");
            Console.WriteLine("Press any key to close the application.");
            Console.Read();
        }
    }

## <a name="run-the-program-and-review-the-output"></a>运行程序并检查输出
运行应用程序时，你会在以下行中看到输出：

    Creating a video review.
    Adding a frame to the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
    Adding a frame to the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
    Adding a frame to the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
    Getting frames for the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
    {
        "ReviewId": "201801v3212bda70ced4928b2cd7459c290c7dc",
        "VideoFrames": [
        {
            "Timestamp": "17000",
            "FrameImage": "https://reviewcontentprod.blob.core.windows.net/testreview6/FRM_201801v3212bda70ced4928b2cd7459c290c7dc_17000.PNG",
            "Metadata": [
            {
                "Key": "reviewRecommended",
                "Value": "true"
            },
            {
                "Key": "adultScore",
                "Value": "0.808312381528463"
            },
            {
                "Key": "a",
                "Value": "false"
            },
            {
                "Key": "racyScore",
                "Value": "0.846378884206702"
            },
            {
                "Key": "r",
                "Value": "false"
            }
            ],
            "ReviewerResultTags": [
            {
                "Key": "tag1",
                "Value": "value1"
            }
        ]
        },
        {
            "Timestamp": "64000",
            "FrameImage": "https://reviewcontentprod.blob.core.windows.net/testreview6/FRM_201801v3212bda70ced4928b2cd7459c290c7dc_64000.PNG",
            "Metadata": [
            {
                "Key": "reviewRecommended",
                "Value": "true"
            },
            {
                "Key": "adultScore",
                "Value": "0.576078300166912"
            },
            {
                "Key": "a",
                "Value": "false"
            },
            {
                "Key": "racyScore",
                "Value": "0.244768953064815"
            },
            {
                "Key": "r",
                "Value": "false"
            }
            ],
            "ReviewerResultTags": [
            {
                "Key": "tag1",
                "Value": "value1"
            }
        ]
        },
        {
            "Timestamp": "144000",
            "FrameImage": "https://reviewcontentprod.blob.core.windows.net/testreview6/FRM_201801v3212bda70ced4928b2cd7459c290c7dc_144000.PNG",
            "Metadata": [
            {
                "Key": "reviewRecommended",
                "Value": "true"
            },
            {
                "Key": "adultScore",
                "Value": "0.664480847150311"
            },
            {
                "Key": "a",
                "Value": "false"
            },
            {
                "Key": "racyScore",
                "Value": "0.933817870418456"
            },
            {
                "Key": "r",
                "Value": "false"
            }
            ],
            "ReviewerResultTags": [
            {
                "Key": "tag1",
                "Value": "value1"
            }
            ]
        }
        ]
    }
    
    Getting the status for the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
    {
        "ReviewId": "201801v3212bda70ced4928b2cd7459c290c7dc",
        "SubTeam": "public",
        "Status": "UnPublished",
        "ReviewerResultTags": [],
        "CreatedBy": "testreview6",
        "Metadata": [
        {
            "Key": "FrameCount",
            "Value": "3"
        }
        ],
        "Type": "Video",
        "Content": "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
        "ContentId": "review1",
        "CallbackEndpoint": null
    }

    Publishing the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
    Open your Content Moderator Dashboard and select Review > Video to see the review.
    Press any key to close the application.

## <a name="check-out-your-video-review"></a>查看视频审查

最后，你会在“审查”>“视频”屏幕上的内容审查器中看到视频审查。

![用于人工审查器的视频审查](images/ams-video-review.PNG)

## <a name="next-steps"></a>后续步骤

了解如何将[脚本审查](video-transcript-moderation-review-tutorial-dotnet.md)添加到视频审查。 

查看有关如何开发[完整视频审查解决方案](video-transcript-moderation-review-tutorial-dotnet.md)的详细教程。

为适用于 .NET 的此内容审查器快速入门以及其他内容审查器快速入门[下载 Visual Studio 解决方案](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator)。
