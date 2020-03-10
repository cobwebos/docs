---
title: 在 Azure 媒体服务中定义筛选器
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
ms.date: 05/23/2019
ms.author: juliako
ms.openlocfilehash: fdf29924da31db0347938df89e698cb258c2336b
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78388108"
---
# <a name="filters"></a>筛选器

将内容传送给客户时（实时流式处理事件或按需视频），客户端可能需要比默认资产的清单文件中所述的更多的灵活性。 Azure 媒体服务基于预定义的筛选器提供[动态清单](filters-dynamic-manifest-overview.md)。 

筛选器是服务器端的规则，可让客户执行以下操作： 

- 仅播放视频的某个部分（而不是整个视频）。 例如：
  - 缩小清单以显示直播活动的子剪辑（“子剪辑筛选”），或
  - 修剪视频开头（“修剪视频”）。
- 只传送内容播放设备所支持的指定再现内容和/或指定的语言轨道（“再现内容筛选”）。 
- 调整演播窗口，以便在播放器中提供长度有限的 DVR 窗口（“调整演播窗口”）。

媒体服务可让你为内容创建**帐户筛选器**和**资产筛选器**。 此外，你可以将预先创建的筛选器与**流式处理定位符**相关联。

## <a name="defining-filters"></a>定义筛选器

有两种类型的筛选器： 

* [帐户筛选器](https://docs.microsoft.com/rest/api/media/accountfilters)（全局）- 可应用到 Azure 媒体服务帐户中所有的资产，生存期与帐户相同。
* [资产筛选器](https://docs.microsoft.com/rest/api/media/assetfilters)（本地）- 在创建后只能应用到与筛选器关联的资产，生存期与资产相同。 

**帐户筛选**器和**资产筛选器**类型具有用于定义/描述筛选器的相同属性。 需要指定要与筛选器关联的资产名称，但创建**资产筛选器**时除外。

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
|**endTimestamp**|适用于点播视频 (VoD)。<br/>对于实时流式处理演示文稿，该演示文稿结束后会被忽略，并在流变为 VoD 时应用。<br/>这是一个长整型值，它表示演示的绝对结束点，舍入到最接近的下一个 GOP 开始。 单位为时间刻度，因此 1800000000 endTimestamp 为3分钟。<br/>使用 startTimestamp 和 endTimestamp 来剪裁将播放列表（清单）中的片段。<br/>例如，startTimestamp = 40000000 和 endTimestamp = 100000000 使用默认时间刻度将生成一个播放列表，其中包含介于 如果某个段跨越边界，则整个段将包含在清单中。|
|**forceEndTimestamp**|仅适用于实时流式处理。<br/>指示 endTimestamp 属性是否必须存在。 如果为 true，则必须指定 endTimestamp 或返回错误的请求代码。<br/>允许的值：false、true。|
|**liveBackoffDuration**|仅适用于实时流式处理。<br/> 此值定义客户端可以搜索的最新实时位置。<br/>使用此属性，可以延迟实时播放位置，并为播放机创建服务器端缓冲区。<br/>此属性的单位为时间刻度（见下文）。<br/>最大活动返回关闭持续时间为300秒（3000000000）。<br/>例如，如果值为2000000000，则表示最新的可用内容为从真实实时边缘延迟的20秒。|
|**presentationWindowDuration**|仅适用于实时流式处理。<br/>使用 presentationWindowDuration 应用片段的滑动窗口，使其包含在播放列表中。<br/>此属性的单位为时间刻度（见下文）。<br/>例如，设置 presentationWindowDuration=1200000000 会应用 2 分钟的滑动窗口。 直播边缘 2 分钟内的媒体将包含在播放列表中。 如果某个段跨越边界，则整个段将包含在播放列表中。 最小呈现窗口持续时间为 60 秒。|
|**startTimestamp**|适用于视频点播（VoD）或实时流式处理。<br/>这是一个长整型值，表示流的绝对起点。 该值将舍入为最接近的下一个 GOP 起点。 单位为时间刻度，因此150000000的 startTimestamp 将是15秒。<br/>使用 startTimestamp 和 endTimestampp 来剪裁将播放列表（清单）中的片段。<br/>例如，startTimestamp = 40000000 和 endTimestamp = 100000000 使用默认时间刻度将生成一个播放列表，其中包含介于 如果某个段跨越边界，则整个段将包含在清单中。|
|**timescale**|适用于显示时间范围内的所有时间戳和持续时间，指定为一秒的增量数。<br/>默认值为 10000000-10000000 增量，其中每个增量为100毫微秒。<br/>例如，如果要将 startTimestamp 设置为30秒，则使用默认时间刻度时，将使用值300000000。|

### <a name="tracks"></a>轨迹

指定筛选器跟踪属性条件（FilterTrackPropertyConditions）的列表，该列表基于流的音轨（实时流式处理或视频点播）应包含在动态创建的清单中。 使用逻辑 **AND** 和 **OR** 运算来组合筛选器。

筛选器轨迹属性条件描述轨迹类型、值（如下表所述）和运算（Equal、NotEqual）。 

|名称|说明|
|---|---|
|**Bitrate**|使用轨迹的比特率进行筛选。<br/><br/>建议的值为一系列比特率，以比特/秒为单位。 例如“0-2427000”。<br/><br/>注意：尽管可以使用特定的比特率值（例如 250000 比特/秒），但不建议使用此方法，因为确切的比特率可能根据资产的不同而波动。|
|**FourCC**|使用轨迹的 FourCC 值进行筛选。<br/><br/>该值是 [RFC 6381](https://tools.ietf.org/html/rfc6381) 中指定的编解码器格式的第一个元素。 目前支持以下编解码器： <br/>视频：“avc1”、“hev1”、“hvc1”<br/>音频：“mp4a”、“ec-3”<br/><br/>若要确定资产中曲目的 FourCC 值，请获取并检查清单文件。|
|**语言**|使用轨迹的语言进行筛选。<br/><br/>该值是 RFC 5646 中指定的、要包含的语言的标记。 例如，“en”。|
|**名称**|使用轨迹的名称进行筛选。|
|类型|使用轨迹的类型进行筛选。<br/><br/>允许以下值：“video”、“audio”或“text”。|

### <a name="example"></a>示例

下面的示例定义了实时流式处理筛选器： 

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

## <a name="associating-filters-with-streaming-locator"></a>将筛选器与流式处理定位符相关联

可以在[流式处理定位符](https://docs.microsoft.com/rest/api/media/streaminglocators/create#request-body)上指定[资产或帐户筛选器](filters-concept.md)的列表。 [动态包装](dynamic-packaging-overview.md)器会将此筛选器列表与客户端在 URL 中指定的筛选器结合在一起。 此组合生成[动态清单](filters-dynamic-manifest-overview.md)，该清单基于在流式处理定位符上指定的 URL + 筛选器中的筛选器。 

请看以下示例：

* [将筛选器与流式处理定位符关联-.NET](filters-dynamic-manifest-dotnet-howto.md#associate-filters-with-streaming-locator)
* [将筛选器与流式处理定位符关联-CLI](filters-dynamic-manifest-cli-howto.md#associate-filters-with-streaming-locator)

## <a name="updating-filters"></a>更新筛选器
 
当可以更新筛选器时，**流式处理**定位符是不可更新的。 

不建议更新与主动发布的**流式处理定位符**相关联的筛选器的定义，尤其是在启用 CDN 时。 流式处理服务器和 Cdn 可能有可能会导致返回过时缓存数据的内部缓存。 

如果需要更改筛选器定义，请考虑创建一个新的筛选器，并将其添加到**流式处理定位符**URL，或发布直接引用筛选器的新**流式处理定位符**。

## <a name="next-steps"></a>后续步骤

以下文章介绍了如何以编程方式创建筛选器。  

- [使用 REST API 创建筛选器](filters-dynamic-manifest-rest-howto.md)
- [使用 .NET 创建筛选器](filters-dynamic-manifest-dotnet-howto.md)
- [使用 CLI 创建筛选器](filters-dynamic-manifest-cli-howto.md)

