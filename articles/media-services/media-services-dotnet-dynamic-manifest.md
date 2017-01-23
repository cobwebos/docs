---
title: "使用 Azure 媒体服务 .NET SDK 创建筛选器"
description: "本主题介绍如何创建筛选器，以便客户端能够使用它们来流式传输流的特定部分。 媒体服务将创建动态清单来存档此选择性流。"
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 2f6894ca-fb43-43c0-9151-ddbb2833cafd
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 07/18/2016
ms.author: juliako;cenkdin
translationtype: Human Translation
ms.sourcegitcommit: ff663f40507547ba561053b5c9a7a8ce93fbf213
ms.openlocfilehash: 76ad2de236c4691523db9a897d99312b721b48d2


---
# <a name="creating-filters-with-azure-media-services-net-sdk"></a>使用 Azure 媒体服务 .NET SDK 创建筛选器
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-dynamic-manifest.md)
> * [REST](media-services-rest-dynamic-manifest.md)
> 
> 

从 2.11 版开始，媒体服务可让你为资产定义筛选器。 这些筛选器是服务器端规则，可让你的客户选择运行如下操作：只播放一段视频（而非播放完整视频），或只指定客户设备可以处理的一部分音频和视频再现内容（而非与该资产相关的所有再现内容）。 通过按客户请求创建的**动态清单**可以实现对资产进行这种筛选，并基于指定的筛选器流式传输视频。

有关与筛选器和动态清单相关的更多详细信息，请参阅[动态清单概述](media-services-dynamic-manifest-overview.md)。

本主题介绍如何使用媒体服务 .NET SDK 创建、更新和删除筛选器。 

请注意，如果你更新筛选器，则流式处理终结点需要 2 分钟的时间来刷新规则。 如果内容是通过使用此筛选器提供的（并在代理和 CDN 缓存中缓存），则更新此筛选器会导致播放器失败。 建议在更新筛选器之后清除缓存。 如果此选项不可用，请考虑使用其他筛选器。 

## <a name="types-used-to-create-filters"></a>用于创建筛选器的类型
创建筛选器时将使用以下类型： 

* **IStreamingFilter**。  此类型基于以下 REST API [Filter](http://msdn.microsoft.com/library/azure/mt149056.aspx)
* **IStreamingAssetFilter**。 此类型基于以下 REST API [AssetFilter](http://msdn.microsoft.com/library/azure/mt149053.aspx)
* **PresentationTimeRange**。 此类型基于以下 REST API [PresentationTimeRange](http://msdn.microsoft.com/library/azure/mt149052.aspx)
* **FilterTrackSelectStatement** 和 **IFilterTrackPropertyCondition**。 这些类型基于以下 REST API [FilterTrackSelect 和 FilterTrackPropertyCondition](http://msdn.microsoft.com/library/azure/mt149055.aspx)

## <a name="createupdatereaddelete-global-filters"></a>创建/更新/读取/删除全局筛选器
下面的代码演示如何使用 .NET 创建、更新、读取和删除资产筛选器。

    string filterName = "GlobalFilter_" + Guid.NewGuid().ToString();

    List<FilterTrackSelectStatement> filterTrackSelectStatements = new List<FilterTrackSelectStatement>();

    FilterTrackSelectStatement filterTrackSelectStatement = new FilterTrackSelectStatement();
    filterTrackSelectStatement.PropertyConditions = new List<IFilterTrackPropertyCondition>();
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackNameCondition("Track Name", FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackBitrateRangeCondition(new FilterTrackBitrateRange(0, 1), FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackTypeCondition(FilterTrackType.Audio, FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatements.Add(filterTrackSelectStatement);

    // Create
    IStreamingFilter filter = _context.Filters.Create(filterName, new PresentationTimeRange(), filterTrackSelectStatements);

    // Update
    filter.PresentationTimeRange = new PresentationTimeRange(timescale: 500);
    filter.Update();

    // Read
    var filterUpdated = _context.Filters.FirstOrDefault();
    Console.WriteLine(filterUpdated.Name);

    // Delete
    filter.Delete();


## <a name="createupdatereaddelete-asset-filters"></a>创建/更新/读取/删除资产筛选器
下面的代码演示如何使用 .NET 创建、更新、读取和删除资产筛选器。

    string assetName = "AssetFilter_" + Guid.NewGuid().ToString();
    var asset = _context.Assets.Create(assetName, AssetCreationOptions.None);

    string filterName = "AssetFilter_" + Guid.NewGuid().ToString();


    // Create
    IStreamingAssetFilter filter = asset.AssetFilters.Create(filterName,
                                        new PresentationTimeRange(), 
                                        new List<FilterTrackSelectStatement>());

    // Update
    filter.PresentationTimeRange = 
            new PresentationTimeRange(start: 6000000000, end: 72000000000);

    filter.Update();

    // Read
    asset = _context.Assets.Where(c => c.Id == asset.Id).FirstOrDefault();
    var filterUpdated = asset.AssetFilters.FirstOrDefault();
    Console.WriteLine(filterUpdated.Name);

    // Delete
    filterUpdated.Delete();




## <a name="build-streaming-urls-that-use-filters"></a>生成使用筛选器的流 URL
有关如何发布和传送资产的信息，请参阅[将内容传送到客户概述](media-services-deliver-content-overview.md)。

以下示例演示了如何将筛选器添加到流 URL。

**MPEG DASH** 

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V4**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V3**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)

**平滑流式处理**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)


## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>另请参阅
[动态清单概述](media-services-dynamic-manifest-overview.md)




<!--HONumber=Dec16_HO2-->


