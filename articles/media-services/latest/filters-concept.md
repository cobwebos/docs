---
title: 在 Azure 媒体服务中定义筛选器
description: 本主题介绍如何创建筛选器，以便客户端能够使用它们来流式传输流的特定部分。 媒体服务将创建动态清单来存档此选择性流。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: bb5561ced93c3f5a899c6e48fdab0f14e52914bb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89291540"
---
# <a name="filters"></a>筛选器

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

将内容传送到客户（实时传送视频流事件或点播视频）时，客户端所需的灵活性可能比默认资产的清单文件中描述的灵活性更高。 Azure 媒体服务根据预定义的筛选器提供[动态清单](filters-dynamic-manifest-overview.md)。 

筛选器是服务器端的规则，可让客户执行以下操作： 

- 仅播放视频的某个部分（而不是整个视频）。 例如：
  - 缩小清单以显示直播活动的子剪辑（“子剪辑筛选”），或
  - 修剪视频开头（“修剪视频”）。
- 只传送内容播放设备所支持的指定再现内容和/或指定的语言轨道（“再现内容筛选”）。 
- 调整演播窗口，以便在播放器中提供长度有限的 DVR 窗口（“调整演播窗口”）。

使用媒体服务可为内容创建**帐户筛选器**和**资产筛选器**。 此外，可将预先创建的筛选器关联到**流定位符**。

## <a name="defining-filters"></a>定义筛选器

有两种类型的筛选器： 

* [帐户筛选器](/rest/api/media/accountfilters)（全局）- 可应用到 Azure 媒体服务帐户中所有的资产，生存期与帐户相同。
* [资产筛选器](/rest/api/media/assetfilters)（本地）- 在创建后只能应用到与筛选器关联的资产，生存期与资产相同。 

**帐户筛选器**和**资产筛选器**类型的用于定义/描述筛选器的属性完全相同。 需要指定要与筛选器关联的资产名称，但创建**资产筛选器**时除外。

根据具体的方案确定哪种类型的筛选器更合适（资产筛选器或帐户筛选器）。 帐户筛选器适用于设备配置文件（再现内容筛选），而资产筛选器可用于修剪特定的资产。

使用以下属性来描述筛选器。 

|名称|说明|
|---|---|
|firstQuality|筛选器的第一个质量比特率。|
|presentationTimeRange|呈现时间范围。 此属性用于筛选清单起点/终点、呈现窗口长度和直播起始位置。 <br/>有关详细信息，请参阅 [PresentationTimeRange](#presentationtimerange)。|
|tracks|轨迹选择条件。 有关详细信息，请参阅[轨迹](#tracks)|

### <a name="presentationtimerange"></a>presentationTimeRange

请将此属性用于**资产筛选器**。 不建议对**帐户筛选器**设置该属性。

|名称|说明|
|---|---|
|**endTimestamp**|适用于点播视频 (VoD)。<br/>对于实时传送视频流演播，将以静默方式忽略该属性；当呈现内容结束并且流变为 VoD 时，将应用该属性。<br/>这是一个长值，表示演播的绝对终点，舍入为最接近的下一个 GOP 起点。 单位是时间刻度，endTimestamp 1800000000 表示 3 分钟。<br/>使用 startTimestamp 和 endTimestamp 来修剪将播放列表（清单）中的片段。<br/>例如，在使用默认时间刻度的情况下指定 startTimestamp=40000000 和 endTimestamp=100000000 会生成一个播放列表，其中包含 VoD 演播内容第 4 秒到第 10 秒的片段。 如果某个段跨越边界，则整个段将包含在清单中。|
|**forceEndTimestamp**|仅适用于实时传送视频流。<br/>指示 endTimestamp 属性是否必须存在。 如果为 true，则必须指定 endTimestamp，否则会返回“错误的请求”代码。<br/>允许的值：false、true。|
|**liveBackoffDuration**|仅适用于实时传送视频流。<br/> 此值定义客户端可以搜寻的最新实时位置。<br/>使用此属性可以延迟直播播放位置，并为播放器创建服务器端缓冲区。<br/>此属性的单位为时间刻度（参阅下文）。<br/>最大直播回退持续时间为 300 秒 (3000000000)。<br/>例如，值 2000000000 表示最新可用内容从实际实时边缘延迟 20 秒。|
|**presentationWindowDuration**|仅适用于实时传送视频流。<br/>使用 presentationWindowDuration 对要包含在播放列表中的片段应用滑动窗口。<br/>此属性的单位为时间刻度（参阅下文）。<br/>例如，设置 presentationWindowDuration=1200000000 会应用 2 分钟的滑动窗口。 直播边缘 2 分钟内的媒体将包含在播放列表中。 如果某个段跨越边界，则整个段将包含在播放列表中。 最小呈现窗口持续时间为 60 秒。|
|**startTimestamp**|适用于点播视频 (VoD) 或实时传送视频流。<br/>这是一个长值，表示流的绝对起点。 该值将舍入为最接近的下一个 GOP 起点。 单位是时间刻度，startTimestamp 150000000 表示 15 秒。<br/>使用 startTimestamp 和 endTimestampp 来修剪将播放列表（清单）中的片段。<br/>例如，在使用默认时间刻度的情况下指定 startTimestamp=40000000 和 endTimestamp=100000000 会生成一个播放列表，其中包含 VoD 演播内容第 4 秒到第 10 秒的片段。 如果某个段跨越边界，则整个段将包含在清单中。|
|**timescale**|适用于演播时间范围内的所有时间戳和持续时间，指定为一秒中的增量数。<br/>默认值为 10000000 - 一秒中有 1000 万个增量，每个增量的长度为 100 纳秒。<br/>例如，若要将 startTimestamp 设置为 30 秒，则在使用默认时间刻度时，需使用值 300000000。|

### <a name="tracks"></a>轨迹

指定筛选器轨迹属性条件 (FilterTrackPropertyConditions) 的列表，应该根据该列表将流（实时传送视频流或点播视频）的轨迹包含到动态创建的清单中。 使用逻辑 **AND** 和 **OR** 运算来组合筛选器。

筛选器轨迹属性条件描述轨迹类型、值（如下表所述）和运算（Equal、NotEqual）。 

|名称|说明|
|---|---|
|**Bitrate**|使用轨迹的比特率进行筛选。<br/><br/>建议的值为一系列比特率，以比特/秒为单位。 例如“0-2427000”。<br/><br/>注意：尽管可以使用特定的比特率值（例如 250000 比特/秒），但不建议使用此方法，因为确切的比特率可能根据资产的不同而波动。|
|**FourCC**|使用轨迹的 FourCC 值进行筛选。<br/><br/>该值是 [RFC 6381](https://tools.ietf.org/html/rfc6381) 中指定的编解码器格式的第一个元素。 目前支持以下编解码器： <br/>视频：“avc1”、“hev1”、“hvc1”<br/>音频：“mp4a”、“ec-3”<br/><br/>若要确定资产中轨迹的 FourCC 值，请获取并检查清单文件。|
|**语言**|使用轨迹的语言进行筛选。<br/><br/>该值是 RFC 5646 中指定的、要包含的语言的标记。 例如，“en”。|
|**名称**|使用轨迹的名称进行筛选。|
|类型 |使用轨迹的类型进行筛选。<br/><br/>允许以下值：“video”、“audio”或“text”。|

### <a name="example"></a>示例

以下示例定义实时传送视频流筛选器： 

```json
{
  "properties": {
    "presentationTimeRange": {
      "startTimestamp": 0,
      "endTimestamp": 170000000,
      "presentationWindowDuration": 9223372036854776000,
      "liveBackoffDuration": 0,
      "timescale": 10000000,
      "forceEndTimestamp": false
    },
    "firstQuality": {
      "bitrate": 128000
    },
    "tracks": [
      {
        "trackSelections": [
          {
            "property": "Type",
            "operation": "Equal",
            "value": "Audio"
          },
          {
            "property": "Language",
            "operation": "NotEqual",
            "value": "en"
          },
          {
            "property": "FourCC",
            "operation": "NotEqual",
            "value": "EC-3"
          }
        ]
      },
      {
        "trackSelections": [
          {
            "property": "Type",
            "operation": "Equal",
            "value": "Video"
          },
          {
            "property": "Bitrate",
            "operation": "Equal",
            "value": "3000000-5000000"
          }
        ]
      }
    ]
  }
}
```

## <a name="associating-filters-with-streaming-locator"></a>将筛选器与流定位符相关联

可以在[流定位符](/rest/api/media/streaminglocators/create#request-body)中指定[资产或帐户筛选器](filters-concept.md)列表。 [动态打包器](dynamic-packaging-overview.md)将此筛选器列表与客户端在 URL 中指定的筛选器一起应用。 此组合将生成[动态清单](filters-dynamic-manifest-overview.md)，该清单基于你在流定位符上指定的“URL + 筛选器”中的筛选器。 

请看以下示例：

* [将筛选器与流定位符相关联 - .NET](filters-dynamic-manifest-dotnet-howto.md#associate-filters-with-streaming-locator)
* [将筛选器与流定位符相关联 - CLI](filters-dynamic-manifest-cli-howto.md#associate-filters-with-streaming-locator)

## <a name="updating-filters"></a>更新筛选器
 
尽管筛选器可更新，但**流定位符**不可更新。 

不建议更新与主动发布的 **流式处理定位符**相关联的筛选器的定义，尤其是在启用 CDN 时。 流式处理服务器和 Cdn 可能有可能会导致返回过时缓存数据的内部缓存。 

如果需要更改筛选器定义，请考虑创建一个新的筛选器，并将其添加到**流定位符** URL，或发布直接引用筛选器的新**流定位符**。

## <a name="next-steps"></a>后续步骤

以下文章介绍了如何以编程方式创建筛选器。  

- [使用 REST API 创建筛选器](filters-dynamic-manifest-rest-howto.md)
- [使用 .NET 创建筛选器](filters-dynamic-manifest-dotnet-howto.md)
- [使用 CLI 创建筛选器](filters-dynamic-manifest-cli-howto.md)
