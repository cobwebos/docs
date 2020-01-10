---
title: 通过 Azure 媒体服务 v3 REST API 创建筛选器
description: 本主题介绍如何创建筛选器，以便客户端能够使用它们来流式传输流的特定部分。 媒体服务将创建动态清单来存档此选择性流。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/13/2019
ms.author: juliako
ms.openlocfilehash: f9134dd3bc926e6e2f454e5187e03365e91ed22a
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75780328"
---
# <a name="creating-filters-with-media-services-rest-api"></a>使用媒体服务 REST API 创建筛选器

将内容传送到客户（直播流活动或点播视频）时，客户端所需的灵活性可能比默认资产的清单文件中描述的灵活性更高。 使用 Azure 媒体服务可为内容定义帐户筛选器和资产筛选器。 

有关此功能及其使用方案的详细说明，请参阅[动态清单](filters-dynamic-manifest-overview.md)和[筛选器](filters-concept.md)。

本主题展示了如何为点播视频资产定义筛选器，以及如何使用 REST API 创建[帐户筛选器](https://docs.microsoft.com/rest/api/media/accountfilters)和[资产筛选器](https://docs.microsoft.com/rest/api/media/assetfilters)。 

> [!NOTE]
> 请确保查看[presentationTimeRange](filters-concept.md#presentationtimerange)。

## <a name="prerequisites"></a>必备组件 

若要完成本主题中所述的步骤，必须：

- 查看[筛选器和动态清单](filters-dynamic-manifest-overview.md)。
- [配置 Postman 以便进行 Azure 媒体服务 REST API 调用](media-rest-apis-with-postman.md)。

    确保遵循[获取 Azure AD 令牌](media-rest-apis-with-postman.md#get-azure-ad-token)主题中的最后一步。 

## <a name="define-a-filter"></a>定义筛选器  

下面是**请求正文**示例，其中定义了添加到清单的曲目择条件。 此筛选器包括属于 EC-3 的任何音频曲目和比特率在 0-1000000 范围内的任何视频曲目。

```json
{
    "properties": {
        "tracks": [
          {
                "trackSelections": [
                    {
                        "property": "Type",
                        "value": "Audio",
                        "operation": "Equal"
                    },
                    {
                        "property": "FourCC",
                        "value": "EC-3",
                        "operation": "Equal"
                    }
                ]
            },
            {
                "trackSelections": [
                    {
                        "property": "Type",
                        "value": "Video",
                        "operation": "Equal"
                    },
                    {
                        "property": "Bitrate",
                        "value": "0-1000000",
                        "operation": "Equal"
                    }
                ]
            }
        ]
     }
}
```

## <a name="create-account-filters"></a>创建帐户筛选器

在你下载的 Postman 的集合中，选择“帐户筛选器”->“创建或更新帐户筛选器”。

**PUT** HTTP 请求方法类似于：

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/accountFilters/{filterName}?api-version=2018-07-01
```

选择“正文”选项卡并粘贴你[之前定义的](#define-a-filter) json 代码。

选择“发送”。 

筛选器已创建。

有关详细信息，请参阅[创建或更新](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate)。 此外，请参阅[筛选器的 JSON 示例](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create-an-account-filter)。

## <a name="create-asset-filters"></a>创建资产筛选器  

在已下载的“媒体服务 v3”Postman 集合中，选择“资产”->“创建或更新资产筛选器”。

**PUT** HTTP 请求方法类似于：

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/assets/{assetName}/assetFilters/{filterName}?api-version=2018-07-01
```

选择“正文”选项卡并粘贴你[之前定义的](#define-a-filter) json 代码。

选择“发送”。 

资产筛选器已创建。

有关如何创建或更新资产筛选器的详细信息，请参阅[创建或更新](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate)。 此外，请参阅[筛选器的 JSON 示例](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create-an-asset-filter)。 

## <a name="associate-filters-with-streaming-locator"></a>将筛选器与流式处理定位符关联

可以指定将应用于流式处理定位符的资产或帐户筛选器的列表。 [动态包装器（流式处理终结点）](dynamic-packaging-overview.md)将此筛选器列表与客户端在 URL 中指定的筛选器结合在一起。 这种组合生成[动态清单](filters-dynamic-manifest-overview.md)，它基于在流式处理定位符上指定的 URL 中的筛选器和筛选器。 如果要应用筛选器，但不希望在 URL 中公开筛选器名称，建议使用此功能。

若要使用 REST 创建筛选器并将其与流式处理定位符相关联，请使用[流式处理](https://docs.microsoft.com/rest/api/media/streaminglocators/create)定位符创建 API 并在[请求正文](https://docs.microsoft.com/rest/api/media/streaminglocators/create#request-body)中指定 `properties.filters`。
                                
## <a name="stream-using-filters"></a>使用筛选器的流

定义筛选器后，客户端可以在流式 URL 中使用它们。 筛选器可应用到自适应比特率流式处理协议：Apple HTTP Live Streaming (HLS)、MPEG-DASH 和平滑流式处理。

下表显示了一些包含筛选器的 URL 示例：

|协议|示例|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|平滑流|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>后续步骤

[流视频](stream-files-tutorial-with-rest.md) 
