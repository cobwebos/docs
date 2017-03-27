---

title: "使用 .NET SDK 管理流式处理终结点。 | Microsoft Docs"
description: "本主题说明如何使用 Azure 门户管理流式处理终结点。"
services: media-services
documentationcenter: 
author: Juliako
writer: juliako
manager: erikre
editor: 
ms.assetid: 0da34a97-f36c-48d0-8ea2-ec12584a2215
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 4f61f7c0fd50065d341ae1ce182a033395857579
ms.openlocfilehash: 68011ef634b1f3bdeb7c219a46e1307698a12f7e


---


# <a name="manage-streaming-endpoints-with-net-sdk"></a>使用 .NET SDK 管理流式处理终结点

>[!NOTE]
>请确保查看[概述](media-services-streaming-endpoints-overview.md)主题。 此外，请查看 [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint)。

本主题中的代码演示如何使用 Azure 媒体服务 .NET SDK 执行以下任务：

- 检查默认的流式处理终结点。
- 创建/添加新的流式处理终结点。

    如果想要具有不同的 CDN 或一个 CDN 及直接访问权限，可能需要多个流式处理终结点。

    > [!NOTE]
    > 仅当流式处理终结点处于运行状态时才进行计费。
    
- 更新流式处理终结点。
    
    请确保调用 Update() 函数。

- 删除流式处理终结点。

    >[!NOTE]
    >无法删除默认流式处理终结点。

有关如何缩放流式处理终结点的信息，请参阅[此](media-services-portal-scale-streaming-endpoints.md)主题。


###<a name="set-up-the-visual-studio-project"></a>设置 Visual Studio 项目

1. 在 Visual Studio 2015 中创建新的 C# 控制台应用程序。  
2. 生成解决方案。
3. 使用 **NuGet** 安装[最新的 Azure 媒体服务 .NET SDK 包](https://www.nuget.org/packages/windowsazure.mediaservices/)。   
4. 将 appSettings 部分添加到 .config 文件中，并更新媒体服务名称和密钥值。 
    
        <appSettings>
          <add key="MediaServicesAccountName" value="Media-Services-Account-Name"/>
          <add key="MediaServicesAccountKey" value="Media-Services-Account-Key"/>
        </appSettings>

###<a name="add-code-that-manages-streaming-endpoints"></a>添加管理流式处理终结点的代码
    
将 Program.cs 中的代码替换为以下代码：

    using System;
    using System.Collections.Generic;
    using System.Configuration;
    using System.Linq;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using Microsoft.WindowsAzure.MediaServices.Client.Live;
    
    namespace AMSStreamingEndpoint
    {
        class Program
        {
            // Read values from the App.config file.
            private static readonly string _mediaServicesAccountName =
                ConfigurationManager.AppSettings["MediaServicesAccountName"];
            private static readonly string _mediaServicesAccountKey =
                ConfigurationManager.AppSettings["MediaServicesAccountKey"];
    
            // Field for service context.
            private static CloudMediaContext _context = null;
            private static MediaServicesCredentials _cachedCredentials = null;
    
            static void Main(string[] args)
            {
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
                // Used the cached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);
    
                var defaultStreamingEndpoint = _context.StreamingEndpoints.Where(s=>s.Name.Contains("default")).FirstOrDefault();
                ExamineStreamingEndpoint(defaultStreamingEndpoint);
    
                IStreamingEndpoint newStreamingEndpoint = AddStreamingEndpoint();
                UpdateStreamingEndpoint(newStreamingEndpoint);
                DeleteStreamingEndpoint(newStreamingEndpoint);
            }
    
            static public void ExamineStreamingEndpoint(IStreamingEndpoint streamingEndpoint)
            {
                Console.WriteLine(streamingEndpoint.Name);
                Console.WriteLine(streamingEndpoint.StreamingEndpointVersion);
                Console.WriteLine(streamingEndpoint.FreeTrialEndTime);
                Console.WriteLine(streamingEndpoint.ScaleUnits);
                Console.WriteLine(streamingEndpoint.CdnProvider);
                Console.WriteLine(streamingEndpoint.CdnProfile);
                Console.WriteLine(streamingEndpoint.CdnEnabled);
            }
    
            static public IStreamingEndpoint AddStreamingEndpoint()
            {
                var name = "StreamingEndpoint" + DateTime.UtcNow.ToString("hhmmss");
                var option = new StreamingEndpointCreationOptions(name, 1)
                {
                    StreamingEndpointVersion = new Version("2.0"),
                    CdnEnabled = true,
                    CdnProfile = "CdnProfile",
                    CdnProvider = CdnProviderType.PremiumVerizon
                };
    
                var streamingEndpoint = _context.StreamingEndpoints.Create(option);
    
                return streamingEndpoint;
            }
    
            static public void UpdateStreamingEndpoint(IStreamingEndpoint streamingEndpoint)
            {
                if(streamingEndpoint.StreamingEndpointVersion == "1.0")
                    streamingEndpoint.StreamingEndpointVersion = "2.0";
    
                streamingEndpoint.CdnEnabled = false;
                streamingEndpoint.Update();
            }
    
            static public void DeleteStreamingEndpoint(IStreamingEndpoint streamingEndpoint)
            {
                streamingEndpoint.Delete();
            }
        }
    }
    

## <a name="next-steps"></a>后续步骤
查看媒体服务学习路径。

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Jan17_HO2-->


