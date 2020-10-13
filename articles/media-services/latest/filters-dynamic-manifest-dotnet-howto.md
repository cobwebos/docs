---
title: 使用 Azure 媒体服务 v3 .NET SDK 创建筛选器
description: 本主题介绍如何创建筛选器，以便客户端能够使用它们来流式传输流的特定部分。 媒体服务 v3 .NET SDK 创建动态清单以实现此选择性流。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 9f54a505d63660fb7c97b3226df4c232e48125e1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90527354"
---
# <a name="create-filters-with-media-services-net-sdk"></a>使用媒体服务 .NET SDK 创建筛选器

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

将内容传送到客户（直播流活动或点播视频）时，客户端所需的灵活性可能比默认资产的清单文件中描述的灵活性更高。 使用 Azure 媒体服务可为内容定义帐户筛选器和资产筛选器。 

有关此功能及其使用方案的详细说明，请参阅[动态清单](filters-dynamic-manifest-overview.md)和[筛选器](filters-concept.md)。

本主题展示了如何使用媒体服务 .NET SDK 为点播视频资产定义筛选器，以及如何创建[帐户筛选器](/dotnet/api/microsoft.azure.management.media.models.accountfilter?view=azure-dotnet)和[资产筛选器](/dotnet/api/microsoft.azure.management.media.models.assetfilter?view=azure-dotnet)。 

> [!NOTE]
> 请确保查看 [presentationTimeRange](filters-concept.md#presentationtimerange)。

## <a name="prerequisites"></a>必备条件 

- 查看[筛选器和动态清单](filters-dynamic-manifest-overview.md)。
- [创建媒体服务帐户](./create-account-howto.md)。 请务必记住资源组名称和媒体服务帐户名称。 
- 获取[访问 API](./access-api-howto.md) 所需的信息
- 查看[使用 Azure 媒体服务进行上传、编码和流式传输](stream-files-tutorial-with-api.md)来了解如何[开始使用 .NET SDK](stream-files-tutorial-with-api.md#start-using-media-services-apis-with-net-sdk)

## <a name="define-a-filter"></a>定义筛选器  

在 .NET 中，你使用 [FilterTrackSelection](/dotnet/api/microsoft.azure.management.media.models.filtertrackselection?view=azure-dotnet) 和 [FilterTrackPropertyCondition](/dotnet/api/microsoft.azure.management.media.models.filtertrackpropertycondition?view=azure-dotnet) 类配置曲目选择。 

以下代码定义了一个筛选器，它包括属于 EC-3 的任何音频曲目和比特率在 0-1000000 范围内的任何视频曲目。

```csharp
var audioConditions = new List<FilterTrackPropertyCondition>()
{
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Type, "Audio", FilterTrackPropertyCompareOperation.Equal),
    new FilterTrackPropertyCondition(FilterTrackPropertyType.FourCC, "EC-3", FilterTrackPropertyCompareOperation.Equal)
};

var videoConditions = new List<FilterTrackPropertyCondition>()
{
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Type, "Video", FilterTrackPropertyCompareOperation.Equal),
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Bitrate, "0-1000000", FilterTrackPropertyCompareOperation.Equal)
};

List<FilterTrackSelection> includedTracks = new List<FilterTrackSelection>()
{
    new FilterTrackSelection(audioConditions),
    new FilterTrackSelection(videoConditions)
};
```

## <a name="create-account-filters"></a>创建帐户筛选器

以下代码展示了如何使用 .NET 创建一个帐户筛选器，使其包括[上面定义的](#define-a-filter)所有曲目选择。 

```csharp
AccountFilter accountFilterParams = new AccountFilter(tracks: includedTracks);
client.AccountFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, "accountFilterName1", accountFilter);
```

## <a name="create-asset-filters"></a>创建资产筛选器

以下代码展示了如何使用 .NET 创建一个资产筛选器，使其包括[上面定义的](#define-a-filter)所有曲目选择。 

```csharp
AssetFilter assetFilterParams = new AssetFilter(tracks: includedTracks);
client.AssetFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, encodedOutputAsset.Name, "assetFilterName1", assetFilterParams);
```

## <a name="associate-filters-with-streaming-locator"></a>将筛选器与流定位器关联

可以指定资产或帐户筛选器的列表，这些筛选器将应用于流定位器。 [动态打包程序（流式处理终结点）](dynamic-packaging-overview.md)将此筛选器列表与客户端在 URL 中指定的筛选器一起应用。 此组合将生成[动态清单](filters-dynamic-manifest-overview.md)，该清单基于你在流定位器上指定的“URL + 筛选器”中的筛选器。 如果要应用筛选器，但不希望在 URL 中公开筛选器名称，建议使用此功能。

以下 C# 代码显示了如何创建流定位器并指定 `StreamingLocator.Filters`。 这是一个可选属性，它采用筛选器名称的 `IList<string>`。

```csharp
IList<string> filters = new List<string>();
filters.Add("filterName");

StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly,
        Filters = filters
    });
```
      
## <a name="stream-using-filters"></a>使用筛选器的流

定义筛选器后，客户端可以在流式 URL 中使用它们。 筛选器可应用到自适应比特率流式处理协议：Apple HTTP Live Streaming (HLS)、MPEG-DASH 和平滑流式处理。

下表显示了一些包含筛选器的 URL 示例：

|协议|示例|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|平滑流|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>后续步骤

[流视频](stream-files-tutorial-with-api.md) 
