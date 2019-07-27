---
title: 使用 .NET 内容审查器审核作业
titleSuffix: Azure Cognitive Services
description: 使用内容审查器 .NET SDK 来启动 Azure 内容审查器中图像或文本内容的端到端内容审核作业。
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/18/2019
ms.author: sajagtap
ms.openlocfilehash: d98c88a6e1d6dfa23f53c32c85c013b4cf2c468d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565602"
---
# <a name="define-and-use-moderation-jobs-net"></a>定义和使用审核作业 (.NET)

裁决作业作为内容审核、工作流和评论功能的一种包装。 本指南提供了信息和代码示例, 帮助你开始使用[适用于 .net 的内容审查器 SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) :

- 启动审核工作以扫描和创建人工审查器评审
- 获取待处理的评审状态
- 跟踪和获取评审的最终状态
- 将评审结果提交给回调 URL

## <a name="prerequisites"></a>系统必备

- 登录或创建内容审查器[审核工具](https://contentmoderator.cognitive.microsoft.com/)站点上的帐户。

## <a name="ensure-your-api-key-can-call-the-review-api-for-review-creation"></a>确保 API 密钥可以调用评审 API 以创建评审

完成上述步骤后，如果从 Azure 门户着手，最终可能会得到两个内容审查器密钥。

如果计划在 SDK 示例中使用 Azure 提供的 API 密钥，请按照[将 Azure 密钥与评审 API 配合使用](./review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis)部分中提到的步骤操作，以允许应用程序调用评审 API 并创建评审。

如果使用评审工具生成的免费试用密钥，则评审工具帐户已经知道密钥，因此无需其他步骤。

## <a name="define-a-custom-moderation-workflow"></a>定义自定义审核工作流

审核工作使用 API 扫描内容，并使用工作流确定是否创建评审。
虽然审查工具包含默认工作流，但还是为此快速入门[定义一个自定义工作流](Review-Tool-User-Guide/Workflows.md)。

可以在代码中使用工作流的名称，从而启动审核工作。

## <a name="create-your-visual-studio-project"></a>创建 Visual Studio 项目

1. 向解决方案添加新的“控制台应用(.NET Framework)”项目。

   在示例代码中，将此项目命名为“CreateReviews”。

1. 将此项目选为解决方案的单一启动项目。

### <a name="install-required-packages"></a>安装所需程序包

安装以下 NuGet 包：

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>更新程序的 using 语句

修改程序的 using 语句。

```csharp
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.CognitiveServices.ContentModerator;
using Microsoft.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
```

### <a name="create-the-content-moderator-client"></a>Create the Content Moderator client

添加以下代码来为订阅创建内容审查器客户端。

> [!IMPORTANT]
> 使用区域标识符和订阅密钥的值更新 AzureRegion 和 CMSubscriptionKey 字段。

```csharp
/// <summary>
/// Wraps the creation and configuration of a Content Moderator client.
/// </summary>
/// <remarks>This class library contains insecure code. If you adapt this
/// code for use in production, use a secure method of storing and using
/// your Content Moderator subscription key.</remarks>
public static class Clients
{
    /// <summary>
    /// The region/location for your Content Moderator account,
    /// for example, westus.
    /// </summary>
    private static readonly string AzureRegion = "YOUR API REGION";

    /// <summary>
    /// The base URL fragment for Content Moderator calls.
    /// </summary>
    private static readonly string AzureBaseURL =
        $"https://{AzureRegion}.api.cognitive.microsoft.com";

    /// <summary>
    /// Your Content Moderator subscription key.
    /// </summary>
    private static readonly string CMSubscriptionKey = "YOUR API KEY";

    /// <summary>
    /// Returns a new Content Moderator client for your subscription.
    /// </summary>
    /// <returns>The new client.</returns>
    /// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
    /// When you have finished using the client,
    /// you should dispose of it either directly or indirectly. </remarks>
    public static ContentModeratorClient NewClient()
    {
        // Create and initialize an instance of the Content Moderator API wrapper.
        ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey));

        client.Endpoint = AzureBaseURL;
        return client;
    }
}
```

### <a name="initialize-application-specific-settings"></a>初始化应用专用设置

向 Program.cs 中的 Program 类添加以下常数和静态字段。

> [!NOTE]
> 将 TeamName 常数设置为创建内容审查器订阅时使用的名称。 从[内容审查器网站](https://westus.contentmoderator.cognitive.microsoft.com/)检索 TeamName。
> 登录后，从“设置”（齿轮）菜单中选择“凭据”。
>
> 团队名称是“API”部分中的“ID”字段值。

```csharp
/// <summary>
/// The moderation job will use this workflow that you defined earlier.
/// See the quickstart article to learn how to setup custom workflows.
/// </summary>
private const string WorkflowName = "OCR";

/// <summary>
/// The name of the team to assign the job to.
/// </summary>
/// <remarks>This must be the team name you used to create your
/// Content Moderator account. You can retrieve your team name from
/// the Content Moderator web site. Your team name is the Id associated
/// with your subscription.</remarks>
private const string TeamName = "***";

/// <summary>
/// The URL of the image to create a review job for.
/// </summary>
private const string ImageUrl =
    "https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png";

/// <summary>
/// The name of the log file to create.
/// </summary>
/// <remarks>Relative paths are relative to the execution directory.</remarks>
private const string OutputFile = "OutputLog.txt";

/// <summary>
/// The number of seconds to delay after a review has finished before
/// getting the review results from the server.
/// </summary>
private const int latencyDelay = 45;

/// <summary>
/// The callback endpoint for completed reviews.
/// </summary>
/// <remarks>Reviews show up for reviewers on your team.
/// As reviewers complete reviews, results are sent to the
/// callback endpoint using an HTTP POST request.</remarks>
private const string CallbackEndpoint = "";
```

## <a name="add-code-to-auto-moderate-create-a-review-and-get-the-job-details"></a>将代码添加到自动-审核、创建评审，并获取作业详细信息

> [!Note]
> 在实践中，将回调 URL CallbackEndpoint 设置为接收人工评审结果的 URL（通过 HTTP POST 请求）。

首先，将以下代码添加到 Main 方法。

```csharp
using (TextWriter writer = new StreamWriter(OutputFile, false))
{
    using (var client = Clients.NewClient())
    {
        writer.WriteLine("Create review job for an image.");
        var content = new Content(ImageUrl);

        // The WorkflowName contains the name of the workflow defined in the online review tool.
        // See the quickstart article to learn more.
        var jobResult = client.Reviews.CreateJobWithHttpMessagesAsync(
                TeamName, "image", "contentID", WorkflowName, "application/json", content, CallbackEndpoint);

        // Record the job ID.
        var jobId = jobResult.Result.Body.JobIdProperty;

        // Log just the response body from the returned task.
        writer.WriteLine(JsonConvert.SerializeObject(
            jobResult.Result.Body, Formatting.Indented));

        Thread.Sleep(2000);
        writer.WriteLine();

        writer.WriteLine("Get review job status.");
        var jobDetails = client.Reviews.GetJobDetailsWithHttpMessagesAsync(
                TeamName, jobId);

        // Log just the response body from the returned task.
        writer.WriteLine(JsonConvert.SerializeObject(
                jobDetails.Result.Body, Formatting.Indented));

        Console.WriteLine();
        Console.WriteLine("Perform manual reviews on the Content Moderator site.");
        Console.WriteLine("Then, press any key to continue.");
        Console.ReadKey();

        Console.WriteLine();
        Console.WriteLine($"Waiting {latencyDelay} seconds for results to propagate.");
        Thread.Sleep(latencyDelay * 1000);

        writer.WriteLine("Get review details.");
        jobDetails = client.Reviews.GetJobDetailsWithHttpMessagesAsync(
        TeamName, jobId);

        // Log just the response body from the returned task.
        writer.WriteLine(JsonConvert.SerializeObject(
        jobDetails.Result.Body, Formatting.Indented));
    }
    writer.Flush();
    writer.Close();
}
```

> [!NOTE]
> 内容审查器服务密钥有一个每秒请求数 (RPS) 速率限制。 如果超过该限制，SDK 会引发错误代码为 429 的异常。
>
> 免费层密钥有一个 RPS 速率限制。

## <a name="run-the-program-and-review-the-output"></a>运行程序并检查输出

可以在控制台中看到以下示例输出：

```console
Perform manual reviews on the Content Moderator site.
Then, press any key to continue.
```

登录内容审查器审阅工具，查看待处理的图像审查。

使用“下一步”按钮进行提交。

![人工审查器的图像审查](images/ocr-sample-image.PNG)

## <a name="see-the-sample-output-in-the-log-file"></a>查看日志文件中的示例输出

> [!NOTE]
> 在输出文件中，字符串 Teamname、ContentId、CallBackEndpoint 和 WorkflowId 反映之前使用的值。

```json
Create moderation job for an image.
{
    "JobId": "2018014caceddebfe9446fab29056fd8d31ffe"
}

Get review details.
{
    "Id": "2018014caceddebfe9446fab29056fd8d31ffe",
    "TeamName": "some team name",
    "Status": "InProgress",
    "WorkflowId": "OCR",
    "Type": "Image",
    "CallBackEndpoint": "",
    "ReviewId": "",
    "ResultMetaData": [],
    "JobExecutionReport": [
    {
        "Ts": "2018-01-07T00:38:26.7714671",
        "Msg": "Successfully got hasText response from Moderator"
    },
    {
        "Ts": "2018-01-07T00:38:26.4181346",
        "Msg": "Getting hasText from Moderator"
    },
    {
        "Ts": "2018-01-07T00:38:25.5122828",
        "Msg": "Starting Execution - Try 1"
    }
    ]
}
```

## <a name="your-callback-url-if-provided-receives-this-response"></a>回叫 URL（若有）接收此响应

可以看到如下示例响应：

> [!NOTE]
> 在回调响应中，字符串 ContentId 和 WorkflowId 反应之前使用的值。

```json
{
    "JobId": "2018014caceddebfe9446fab29056fd8d31ffe",
    "ReviewId": "201801i28fc0f7cbf424447846e509af853ea54",
    "WorkFlowId": "OCR",
    "Status": "Complete",
    "ContentType": "Image",
    "CallBackType": "Job",
    "ContentId": "contentID",
    "Metadata": {
        "hastext": "True",
        "ocrtext": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n",
        "imagename": "contentID"
    }
}
```

## <a name="next-steps"></a>后续步骤

为适用于 .NET 的此内容审查器快速入门和其他内容审查器快速入门获取[内容审查器 .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) 和 [Visual Studio 解决方案](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator)，并开始集成。
