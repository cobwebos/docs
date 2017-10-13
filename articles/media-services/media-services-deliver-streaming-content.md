---
title: "使用 .NET 发布 Azure 媒体服务内容 | Microsoft Docs"
description: "了解如何创建用于生成流 URL 的定位符。 代码示例用 C# 编写且使用适用于 .NET 的媒体服务 SDK。"
author: juliako
manager: cfowler
editor: 
services: media-services
documentationcenter: 
ms.assetid: c53b1f83-4cb1-4b09-840f-9c145b7d6f8d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako
ms.openlocfilehash: 2bcb012eef84faa7c1e13ed22e88e45e4300ed54
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="publish-azure-media-services-content-using-net"></a>使用 .NET 发布 Azure 媒体服务内容
> [!div class="op_single_selector"]
> * [REST](media-services-rest-deliver-streaming-content.md)
> * [.NET](media-services-deliver-streaming-content.md)
> * [门户](media-services-portal-publish.md)
> 
> 

## <a name="overview"></a>概述
可以通过创建 OnDemand 流式处理定位符并生成流 URL 来流式传输自适应比特率 MP4 集。 [对资产进行编码](media-services-encode-asset.md)主题说明了如何编码成自适应比特率 MP4 集。 

> [!NOTE]
> 如果内容已加密，则在创建定位符之前配置资产传送策略（如[本](media-services-dotnet-configure-asset-delivery-policy.md)主题中所述）。 
> 
> 

也可以使用 OnDemand 流式处理定位符生成指向可渐进式下载的 MP4 文件的 URL。  

本主题说明如何创建按需流式处理定位符，以发布资产及生成平滑流、MPEG DASH 和 HLS 流式处理 URL。 此外，还将示范如何生成渐进式下载 URL。 

## <a name="create-an-ondemand-streaming-locator"></a>创建 OnDemand 流式处理定位符
若要创建按需流式处理定位符并获取 URL，需要执行以下操作：

1. 如果内容已加密，则定义访问策略。
2. 创建 OnDemand 流式处理定位符。
3. 如果想要流式处理，请获取资产中的流式处理清单文件 (.ism)。 
   
   如果想要渐进式下载，请获取资产中的 MP4 文件名。  
4. 生成清单文件或 MP4 文件的 URL。 


>[!NOTE]
>不同 AMS 策略的策略限制为 1,000,000 个（例如，对于定位器策略或 ContentKeyAuthorizationPolicy）。 如果始终使用相同的天数/访问权限，则使用相同的策略 ID。 例如，适用于需要长期保留使用的定位符的策略（非上传策略）。 有关详细信息，请参阅[此](media-services-dotnet-manage-entities.md#limit-access-policies)主题。

### <a name="use-media-services-net-sdk"></a>使用媒体服务 .NET SDK
生成流 URL 

    private static void BuildStreamingURLs(IAsset asset)
    {

        // Create a 30-day readonly access policy. 
          // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.
        IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

        // Create a locator to the streaming content on an origin. 
        ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

        // Display some useful values based on the locator.
        Console.WriteLine("Streaming asset base path on origin: ");
        Console.WriteLine(originLocator.Path);
        Console.WriteLine();

        // Get a reference to the streaming manifest file from the  
        // collection of files in the asset. 
        var manifestFile = asset.AssetFiles.Where(f => f.Name.ToLower().
                                    EndsWith(".ism")).
                                    FirstOrDefault();

        // Create a full URL to the manifest file. Use this for playback
        // in streaming media clients. 
        string urlForClientStreaming = originLocator.Path + manifestFile.Name + "/manifest";
        Console.WriteLine("URL to manifest for client streaming using Smooth Streaming protocol: ");
        Console.WriteLine(urlForClientStreaming);
        Console.WriteLine("URL to manifest for client streaming using HLS protocol: ");
        Console.WriteLine(urlForClientStreaming + "(format=m3u8-aapl)");
        Console.WriteLine("URL to manifest for client streaming using MPEG DASH protocol: ");
        Console.WriteLine(urlForClientStreaming + "(format=mpd-time-csf)"); 
        Console.WriteLine();
    }

输出：

    URL to manifest for client streaming using Smooth Streaming protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest
    URL to manifest for client streaming using HLS protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)
    URL to manifest for client streaming using MPEG DASH protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)


> [!NOTE]
> 也可以通过 SSL 连接流式传输内容。 若要执行此方法，请确保流 URL 以 HTTPS 开头。 目前，AMS 对自定义域不支持 SSL。
> 
> 

生成渐进式下载 URL 

    private static void BuildProgressiveDownloadURLs(IAsset asset)
    {
        // Create a 30-day readonly access policy. 
        IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

        // Create an OnDemandOrigin locator to the asset. 
        ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

        // Display some useful values based on the locator.
        Console.WriteLine("Streaming asset base path on origin: ");
        Console.WriteLine(originLocator.Path);
        Console.WriteLine();

        // Get MP4 files.
        IEnumerable<IAssetFile> mp4AssetFiles = asset
            .AssetFiles
            .ToList()
            .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Create a full URL to the MP4 files. Use this to progressively download files.
        foreach (var pd in mp4AssetFiles)
            Console.WriteLine(originLocator.Path + pd.Name);
    }

输出：

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4

    . . . 

### <a name="use-media-services-net-sdk-extensions"></a>使用 Azure 媒体服务 .NET SDK 扩展
以下代码将调用 .NET SDK 扩展方法，以创建定位符，并为自适应流式处理生成平滑流式处理、HLS 和 MPEG-DASH URL。

    // Create a loctor.
    _context.Locators.Create(
        LocatorType.OnDemandOrigin,
        inputAsset,
        AccessPermissions.Read,
        TimeSpan.FromDays(30));

    // Get the streaming URLs.
    Uri smoothStreamingUri = inputAsset.GetSmoothStreamingUri();
    Uri hlsUri = inputAsset.GetHlsUri();
    Uri mpegDashUri = inputAsset.GetMpegDashUri();

    Console.WriteLine(smoothStreamingUri);
    Console.WriteLine(hlsUri);
    Console.WriteLine(mpegDashUri);


## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>后续步骤
* [下载资产](media-services-deliver-asset-download.md)
* [配置资产传送策略](media-services-dotnet-configure-asset-delivery-policy.md)

