---
title: 使用 Azure 媒体服务 REST API 创建筛选器 | Microsoft Docs
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
ms.date: 06/03/2019
ms.author: juliako
ms.openlocfilehash: 01c1711fb70d31fe84c7e20272de0eb7ce82c879
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66494231"
---
# <a name="creating-filters-with-media-services-rest-api"></a>使用媒体服务 REST API 创建筛选器

将内容传送到客户（直播流事件或点播视频）时，客户端所需的灵活性可能比默认资产的清单文件中描述的灵活性更高。 使用 Azure 媒体服务可为内容定义帐户筛选器和资产筛选器。 

有关此功能和使用该方案的详细说明，请参阅[动态清单](filters-dynamic-manifest-overview.md)并[筛选器](filters-concept.md)。

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

选择“正文”  选项卡并粘贴你[之前定义的](#define-a-filter) json 代码。

选择“发送”。  

筛选器已创建。

有关详细信息，请参阅[创建或更新](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate)。 此外，请参阅[筛选器的 JSON 示例](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create_an_account_filter)。

## <a name="create-asset-filters"></a>创建资产筛选器  

在已下载的“媒体服务 v3”Postman 集合中，选择“资产”  ->“创建或更新资产筛选器”  。

**PUT** HTTP 请求方法类似于：

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/assets/{assetName}/assetFilters/{filterName}?api-version=2018-07-01
```

选择“正文”  选项卡并粘贴你[之前定义的](#define-a-filter) json 代码。

选择“发送”。  

资产筛选器已创建。

有关如何创建或更新资产筛选器的详细信息，请参阅[创建或更新](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate)。 此外，请参阅[筛选器的 JSON 示例](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create_an_asset_filter)。 

## <a name="next-steps"></a>后续步骤

[流视频](stream-files-tutorial-with-rest.md) 
