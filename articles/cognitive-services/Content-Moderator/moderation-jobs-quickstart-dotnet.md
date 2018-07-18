---
title: Azure 内容审查器 - 使用 .NET 启动审核工作 | Microsoft Docs
description: 如何使用适用于 .NET 的 Azure 内容审查器 SDK 启动审核工作
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/06/2018
ms.author: sajagtap
ms.openlocfilehash: a103875607355993e216ce1ddea02009fc8fa1c4
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365498"
---
# <a name="start-moderation-jobs-using-net"></a>使用 .NET 启动审核工作

本文提供了信息和代码示例，帮助你开始使用适用于 .NET 的内容审查器 SDK 来执行以下操作：
 
- 启动审核工作以扫描和创建人工审查器评审
- 获取待处理的评审状态
- 跟踪和获取评审的最终状态
- 将结果提交至回调 Url

本文假定你已熟悉 Visual Studio 和 C#。

## <a name="sign-up-for-content-moderator-services"></a>注册内容审查器服务

在可以通过 REST API 或 SDK 使用内容审查器服务之前，需要一个订阅密钥。
请参考[快速入门](quick-start.md)了解如何获取密钥。

## <a name="define-a-custom-moderation-workflow"></a>定义自定义审核工作流

审核工作使用 API 扫描内容，并使用工作流确定是否创建评审。
虽然审查工具包含默认工作流，但还是为此快速入门[定义一个自定义工作流](Review-Tool-User-Guide/Workflows.md)。

可以在代码中使用工作流的名称，从而启动审核工作。

## <a name="create-your-visual-studio-project"></a>创建 Visual Studio 项目

1. 向解决方案添加新的控制台应用 (.NET Framework) 项目。

   在示例代码中，将项目命名为“CreateReviews”。

1. 选择此项目作为解决方案的单个启动项目。

1. 向在[内容审查器客户端帮助程序快速入门](content-moderator-helper-quickstart-dotnet.md)中创建的“ModeratorHelper”项目程序集添加一个引用。

### <a name="install-required-packages"></a>安装所需程序包

安装以下 NuGet 包：

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>更新程序的 using 语句

修改程序的 using 语句。

    using Microsoft.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator.Models;
    using ModeratorHelper;
    using Newtonsoft.Json;
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Threading;

### <a name="initialize-application-specific-settings"></a>初始化特定于应用程序的设置

向 Program.cs 中的 Program 类添加以下常数和静态字段。

> [!NOTE]
> 将 TeamName 常数设置为创建内容审查器订阅时使用的名称。 从[内容审查器网站](https://westus.contentmoderator.cognitive.microsoft.com/)检索 TeamName。
> 登录后，从“设置”（齿轮）菜单中选择“凭据”。
>
> 团队名称是“API”部分中“ID”字段的值。


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
    /// the Conent Moderator web site. Your team name is the Id associated 
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
    /// <remarks>Relative paths are ralative the execution directory.</remarks>
    private const string OutputFile = "OutputLog.txt";

    /// <summary>
    /// The number of seconds to delay after a review has finished before
    /// getting the review results from the server.
    /// </summary>
    private const int latencyDelay = 45;

    /// <summary>
    /// The callback endpoint for completed reviews.
    /// </summary>
    /// <remarks>Revies show up for reviewers on your team. 
    /// As reviewers complete reviews, results are sent to the
    /// callback endpoint using an HTTP POST request.</remarks>
    private const string CallbackEndpoint = "";

## <a name="add-code-to-auto-moderate-create-a-review-and-get-the-job-details"></a>将代码添加到自动-审核、创建评审，并获取作业详细信息

> [!Note]
> 在实践中，将回调 URL CallbackEndpoint 设置为接收人工评审结果的 URL（通过 HTTP POST 请求）。

首先，将以下代码添加到 Main 方法。

    using (TextWriter writer = new StreamWriter(OutputFile, false))
    {
        using (var client = Clients.NewClient())
        {
            writer.WriteLine("Create review job for an image.");
            var content = new Content(ImageUrl);
        
            // The WorkflowName contains the nameof the workflow defined in the online review tool.
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

> [!NOTE]
> 内容审查器服务密钥有一个每秒请求数 (RPS) 速率限制。 如果超过了该限制，SDK 会引发随附 429 错误代码的异常。 
>
> 免费层密钥有一个 RPS 速率限制。

## <a name="run-the-program-and-review-the-output"></a>运行程序并检查输出

可以在控制台中看到以下示例输出：

    Perform manual reviews on the Content Moderator site.
    Then, press any key to continue.

登录内容审查器审阅工具，查看待处理的图像审查。

使用“下一步”按钮进行提交。

![人工审查器的图像审查](images/ocr-sample-image.PNG)

## <a name="see-the-sample-output-in-the-log-file"></a>查看日志文件中的示例输出

> [!NOTE]
> 在输出文件中，字符串 Teamname、ContentId、CallBackEndpoint 和 WorkflowId 反映之前使用的值。

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


## <a name="your-callback-url-if-provided-receives-this-response"></a>如果提供了回调 URL，将接收此响应。

可以看到类似于以下示例的响应：

> [!NOTE]
> 在回调响应中，字符串 ContentId 和 WorkflowId 反应之前使用的值。

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


## <a name="next-steps"></a>后续步骤

针对适用于 .NET 的此内容审查器以及其他内容审查器快速入门[下载 Visual Studio 解决方案](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator)，并开始集成。
