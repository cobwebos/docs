---
title: "开发使用媒体服务的 Azure Functions"
description: "本主题说明如何在 Azure 门户中开始开发使用媒体服务的 Azure Functions。"
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: 51bdcb01-1846-4e1f-bd90-70020ab471b0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/13/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: b077504e7a289fd4976aa22bab0ad5784ffb491b
ms.lasthandoff: 03/15/2017


---
#<a name="develop-azure-functions-with-media-services"></a>开发使用媒体服务的 Azure Functions
[!INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

本主题介绍如何在 Azure 门户中开始开发使用媒体服务的 Azure Functions。 

也可以在[此处](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)按下“部署到 Azure”按钮，来部署现有的媒体服务 Azure Functions。 此存储库包含 Azure Functions 示例。这些示例使用 Azure 媒体服务来演示有关直接从 Blob 存储引入内容、编码以及将内容写回 Blob 存储的工作流。 此存储库还包含演示如何通过 WebHook 和 Azure 队列监视作业通知的示例。

可以基于[此](https://github.com/Azure-Samples/media-services-dotnet-Functions-integration)存储库中的示例开发 Functions。 本主题说明如何开始创建使用媒体服务的 Azure Functions。 

## <a name="prerequisites"></a>先决条件

必须先具有有效的 Azure 帐户，然后才能创建你的第一个函数。 如果还没有 Azure 帐户， [可以使用免费帐户](https://azure.microsoft.com/free/)。

若要创建针对 Azure 媒体服务 (AMS) 帐户执行操作或者侦听媒体服务发送的事件的 Azure Functions，应该根据[此文](media-services-portal-create-account.md)中所述创建一个 AMS 帐户。

## <a name="create-a-function-app"></a>创建 Function App

根据[此文](../azure-functions/functions-create-first-azure-function-azure-portal.md#create-a-function-app)中所述创建 Function App。

## <a name="create-a-function"></a>创建函数

部署 Function App 后，可在**应用服务** Azure Functions 中找到它。 

1. 选择你的 Function App，然后单击“新建函数”。
3. 选择“C#”语言和“Webhook + API”方案。
3. 选择“GenericWebHook-CSharp”（每当收到 Webhook 请求时就会运行）或“HttpTrigger-CSharp”（每当收到 HTTP 请求时就会运行），并为函数命名。
4. 单击“创建” 。 

## <a name="get-function-url"></a>获取函数 URL

若要通过 HTTP 测试工具或其他浏览器窗口触发函数的执行，需要提供“函数 URL”值。 

![设置](./media/media-services-azure-functions/media-services-azure-functions002.png)

## <a name="files"></a>文件

Azure 函数与代码文件以及本部分所述的其他文件相关联。 默认情况下，函数与 **function.json** 和 **run.csx** 文件相关联。 需要添加 **project.json** 文件。 本部分的余下内容介绍这些文件的定义。

![文件](./media/media-services-azure-functions/media-services-azure-functions003.png)

### <a name="functionjson"></a>function.json

Function.json 文件定义函数绑定和其他配置设置。 运行时使用此文件确定要监视的事件，以及如何将数据传入函数执行和从函数执行返回数据。 

下面是 **function.json** 文件的示例。

    {
      "bindings": [
        {
          "type": "httpTrigger",
          "name": "req",
          "direction": "in",
          "methods": [
        "post",
        "get",
        "put",
        "update",
        "patch"
          ]
        },
        {
          "type": "http",
          "name": "res",
          "direction": "out"
        }
      ]
    }
    
### <a name="projectjson"></a>project.json

project.json 文件包含依赖项。 下面是包含 AMS 库的 **function.json** 文件的示例。

    {
      "frameworks": {
        "net46":{
          "dependencies": {
        "windowsazure.mediaservices": "3.8.0.5",
        "windowsazure.mediaservices.extensions": "3.8.0.3"
          }
        }
       }
    }
    
### <a name="runcsx"></a>run.csx

这是函数的 C# 代码。 有关 Webhook 函数的示例，请参阅[此](media-services-dotnet-check-job-progress-with-webhooks.md)主题。 

定义完函数后，请单击“运行”。
    
    ///////////////////////////////////////////////////
    #r "Newtonsoft.Json"
    
    using System;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.IO;
    using System.Globalization;
    using Newtonsoft.Json;
    using Microsoft.Azure;
    using System.Net;
    using System.Security.Cryptography;
    

    static string _mediaServicesAccountName = Environment.GetEnvironmentVariable("AMSAccount");
    static string _mediaServicesAccountKey = Environment.GetEnvironmentVariable("AMSKey");
    
    static CloudMediaContext _context = null;
    
    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");
    
        Task<byte[]> taskForRequestBody = req.Content.ReadAsByteArrayAsync();
        byte[] requestBody = await taskForRequestBody;
    
        string jsonContent = await req.Content.ReadAsStringAsync();
        log.Info($"Request Body = {jsonContent}");
    
        // some valication code ...

        _context = new CloudMediaContext(new MediaServicesCredentials(
        _mediaServicesAccountName,
        _mediaServicesAccountKey));

        // some AMS operations ...
      
        return req.CreateResponse(HttpStatusCode.BadRequest, "Generic Error.");
    }



## <a name="configure-function-app-settings"></a>配置 Function App 设置

开发媒体服务函数时，随时可在“应用设置”部分中添加要在整个函数中使用的参数。 

例如：

![设置](./media/media-services-azure-functions/media-services-azure-functions001.png)


## <a name="next-step"></a>后续步骤

现在，你可以开始开发媒体服务应用程序了。 有关详细信息，请参阅[使用 Azure WebHook 通过 .NET 监视媒体服务作业通知](media-services-dotnet-check-job-progress-with-webhooks.md)。   

## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


