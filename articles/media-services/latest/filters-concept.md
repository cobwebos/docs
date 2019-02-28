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
ms.date: 02/12/2019
ms.author: juliako
ms.openlocfilehash: 09de372ffdb48c00fde9a43c07f8f8b574462d1f
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2019
ms.locfileid: "56372921"
---
# <a name="define-account-filters-and-asset-filters"></a>定义帐户筛选器和资产筛选器  

将内容传送到客户（直播流事件或点播视频）时，客户端所需的灵活性可能比默认资产的清单文件中描述的灵活性更高。 使用 Azure 媒体服务可为内容定义帐户筛选器和资产筛选器。 

筛选器是服务器端的规则，可让客户执行以下操作： 

- 仅播放视频的某个部分（而不是整个视频）。 例如：
  - 缩小清单以显示实时事件的子剪辑（“子剪辑筛选”），或
  - 修剪视频开头（“修剪视频”）。
- 只传送内容播放设备所支持的指定再现内容和/或指定的语言轨道（“再现内容筛选”）。 
- 调整演播窗口，以便在播放器中提供长度有限的 DVR 窗口（“调整演播窗口”）。

媒体服务根据预定义的筛选器提供[动态清单](filters-dynamic-manifest-overview.md)。 定义筛选器后，客户端可以在流式 URL 中使用它们。 筛选器可以应用于自适应比特率流式处理协议：Apple HTTP Live Streaming (HLS)、MPEG-DASH 和平滑流式处理。

下表显示了一些包含筛选器的 URL 示例：

|协议|示例|
|---|---|
|HLS V4|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|HLS V3|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3,filter=myAccountFilter)`|
|MPEG DASH|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|平滑流|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=myAssetFilter)`|

## <a name="define-filters"></a>定义筛选器

有两种类型的资产筛选器： 

* [帐户筛选器](https://docs.microsoft.com/rest/api/media/accountfilters)（全局）- 可应用到 Azure 媒体服务帐户中所有的资产，生存期与帐户相同。
* [资产筛选器](https://docs.microsoft.com/rest/api/media/assetfilters)（本地）- 在创建后只能应用到与筛选器关联的资产，生存期与资产相同。 

[帐户筛选器](https://docs.microsoft.com/rest/api/media/accountfilters)和[资产筛选器](https://docs.microsoft.com/rest/api/media/assetfilters)类型的用于定义/描述筛选器的属性完全相同。 需要指定要与筛选器关联的资产名称，但创建**资产筛选器**时除外。

根据具体的方案确定哪种类型的筛选器更合适（资产筛选器或帐户筛选器）。 帐户筛选器适用于设备配置文件（再现内容筛选），而资产筛选器可用于修剪特定的资产。

使用以下属性来描述筛选器。 

|Name|说明|
|---|---|
|firstQuality|筛选器的第一个质量比特率。|
|presentationTimeRange|呈现时间范围。 此属性用于筛选清单起点/终点、呈现窗口长度和直播起始位置。 <br/>有关详细信息，请参阅 [PresentationTimeRange](#PresentationTimeRange)。|
|tracks|轨迹选择条件。 有关详细信息，请参阅[轨迹](#tracks)|

### <a name="presentationtimerange"></a>PresentationTimeRange

请将此属性用于**资产筛选器**。 不建议对**帐户筛选器**设置该属性。

|Name|说明|
|---|---|
|**endTimestamp**|绝对结束时间边界。 适用于点播视频 (VoD)。 对于直播，将以静默方式忽略该属性；当呈现内容结束并且流变为 VoD 时，将应用该属性。<br/><br/>该值表示流的绝对终点。 它将舍入为最接近的下一个 GOP 起点。<br/><br/>使用 StartTimestamp 和 EndTimestamp 修剪播放列表（清单）。 例如，如果 StartTimestamp=40000000 并且 EndTimestamp=100000000，则会生成包含 StartTimestamp 与 EndTimestamp 之间的媒体的播放列表。 如果某个段跨越边界，则整个段将包含在清单中。<br/><br/>另请参阅下面的 **forceEndTimestamp** 定义。|
|**forceEndTimestamp**|适用于直播筛选器。<br/><br/>**forceEndTimestamp** 是一个布尔值，指示 **endTimestamp** 是否设置为有效值。 <br/><br/>如果值为 **true**，则应指定 **endTimestamp** 值。 如果未指定，则返回错误的请求。<br/><br/>例如，若要定义一个在输入视频中 5 分钟处开始并持续到流末尾的筛选器，应将 **forceEndTimestamp** 设置为 false，并省略 **endTimestamp** 设置。|
|**liveBackoffDuration**|仅适用于直播。 该属性用于定义直播播放位置。 使用此规则可以延迟直播播放位置，并为播放器创建服务器端缓冲区。 LiveBackoffDuration 相对于直播位置。 最大直播回退持续时间为 300 秒。|
|**presentationWindowDuration**|适用于直播。 使用 **presentationWindowDuration** 可对播放列表应用滑动窗口。 例如，设置 presentationWindowDuration=1200000000 会应用 2 分钟的滑动窗口。 直播边缘 2 分钟内的媒体将包含在播放列表中。 如果某个段跨越边界，则整个段将包含在播放列表中。 最小呈现窗口持续时间为 60 秒。|
|**startTimestamp**|适用于 VoD 或直播流。 该值表示流的绝对起点。 该值将舍入为最接近的下一个 GOP 起点。<br/><br/>使用 **startTimestamp** 和 **endTimestamp** 修剪播放列表（清单）。 例如，如果 startTimestamp=40000000 并且 endTimestamp=100000000，则会生成包含 StartTimestamp 与 EndTimestamp 之间的媒体的播放列表。 如果某个段跨越边界，则整个段将包含在清单中。|
|**timescale**|适用于 VoD 或直播流。 前面指定的时间戳和持续时间使用的时间刻度。 默认时间刻度为 10000000。 可以使用备用时间刻度。 默认值为 10000000 HNS（100 纳秒）。|

### <a name="tracks"></a>轨迹

指定筛选器轨迹属性条件 (FilterTrackPropertyConditions) 的列表，应该根据该列表将流（直播或点播视频）的轨迹包含到动态创建的清单中。 使用逻辑 **AND** 和 **OR** 运算来组合筛选器。

筛选器轨迹属性条件描述轨迹类型、值（如下表所述）和运算（Equal、NotEqual）。 

|Name|说明|
|---|---|
|**Bitrate**|使用轨迹的比特率进行筛选。<br/><br/>建议的值为一系列比特率，以比特/秒为单位。 例如“0-2427000”。<br/><br/>注意：尽管可以使用特定的比特率值（例如 250000 比特/秒），但不建议使用此方法，因为确切的比特率可能根据资产的不同而波动。|
|**FourCC**|使用轨迹的 FourCC 值进行筛选。<br/><br/>该值是 [RFC 6381](https://tools.ietf.org/html/rfc6381) 中指定的编解码器格式的第一个元素。 目前支持以下编解码器： <br/>视频：“avc1”、“hev1”、“hvc1”<br/>音频：“mp4a”、“ec-3”<br/><br/>若要确定资产中轨迹的 FourCC 值，请[获取并检查清单文件](#get-and-examine-manifest-files)。|
|**语言**|使用轨迹的语言进行筛选。<br/><br/>该值是 RFC 5646 中指定的、要包含的语言的标记。 例如，“en”。|
|**名称**|使用轨迹的名称进行筛选。|
|类型|使用轨迹的类型进行筛选。<br/><br/>允许以下值：“video”、“audio”或“text”。|

## <a name="example"></a>示例

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

## <a name="next-steps"></a>后续步骤

以下文章介绍了如何以编程方式创建筛选器。  

- [使用 REST API 创建筛选器](filters-dynamic-manifest-rest-howto.md)
- [使用 .NET 创建筛选器](filters-dynamic-manifest-dotnet-howto.md)
- [使用 CLI 创建筛选器](filters-dynamic-manifest-cli-howto.md)

