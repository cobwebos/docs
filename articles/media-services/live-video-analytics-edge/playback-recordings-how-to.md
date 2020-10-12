---
title: 播放录制内容 - Azure
description: 可以使用 IoT Edge 上的实时视频分析进行连续视频录制，持续数周或数月将视频录制到云中。 还可以通过基于事件的录制将录制限制为你感兴趣的剪辑。 本文讨论如何播放录制内容。
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: 6222d2c05b2fe05945d4bcbef6dbb0d64bd4726a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84260380"
---
# <a name="playback-of-recordings"></a>播放录制内容 

## <a name="pre-read"></a>预读  

* [视频播放](video-playback-concept.md)
* [连续视频录制](continuous-video-recording-concept.md)
* [基于事件的视频录制](event-based-video-recording-concept.md)

## <a name="background"></a>背景  

可以使用 IoT Edge 上的实时视频分析进行[连续视频录制](continuous-video-recording-concept.md) (CVR)，持续数周或数月将视频录制到云中。 还可以通过[基于事件的视频录制](event-based-video-recording-concept.md) (EVR) 将录制限制为你感兴趣的剪辑。 

媒体服务帐户已链接到 Azure 存储帐户，将视频录制到云中时，内容会写入[媒体服务资产](terminology.md#asset)。 通过媒体服务，你可以在录制完成后或在录制进行时[流式传输此内容](terminology.md#streaming)。 媒体服务提供将流通过 HLS 或 MPEG-DASH 协议交付到播放设备（客户端）所需的功能。 有关详细信息，请参阅[视频播放](video-playback-concept.md)一文。 使用媒体服务 API 来生成必备的流式处理 URL – 由客户端用来播放视频和音频。 若要为资产构造流式处理 URL，请参阅[创建流式处理定位符并生成 URL](../latest/create-streaming-locator-build-url.md)。 在为资产创建了流式处理 URL 后，你可以并且应该将此 URL 与视频源（即相机）的关联存储在内容管理系统中。

例如，当通过 HLS 进行流式处理时，流式处理 URL 类似于 `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl).m3u8`。 对于 MPEG-DASH，它则类似于 `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=mpd-time-cmaf).mpd`。

这会返回一个清单文件，其中的所有内容都描述正在传递的流的总持续时间，以及它是表示预先录制的内容还是正在进行的 "实时" 源。

### <a name="live-vs-vod"></a>实时与VoD  

流协议（例如 HLS 和 MPEG-DASH）是为处理实时视频的流式处理等方案以及为流式处理点播/预先录制的内容（例如电视节目和电影）而创建的。 对于 live 视频，HLS 和 MPEG 虚线客户端旨在开始从 "最近的时间" 开始播放。 但是对于电影而言，观看者希望能够从开头看起，并且可以选择跳过某些内容。 HLS 和 MPEG-DASH 清单拥有标记，这些标记会向客户端指示视频表示的是实时流还是预先录制的内容。
此概念也适用于来自资产的 HLS 和 MPEG-DASH 流，这些资产包含使用 IoT Edge 上的实时视频分析录制的视频。

## <a name="browsing-and-selective-playback-of-recordings"></a>浏览和选择性地播放录制内容  

请考虑下面的情况：在整个学年期间，你使用了 IoT Edge 上的实时视频分析来仅在学校开放日上午 8 点到 10 点的时间段录制视频。 或者你只在全国法定假日上午 7 点到晚上 7 点的时间段录制视频。 在这两种情况下，如果要尝试浏览并观看你的视频录制，你需要：

* 一种能确定录制视频的日期/时间的方法。
* 一种能选择要播放的录制部分（例如新年当天上午 9 点到 11 点）的方法。

媒体服务提供了查询 API (availableMedia)，用于解决第一个问题，并提供了时间范围筛选器（startTime，endTime），用于解决第二个问题。

## <a name="query-api"></a>查询 API 

使用 CVR 时，播放设备（客户端）无法请求包含播放整个录制内容的清单。  多年的录制会生成一个清单文件，此文件会因太大而无法播放，并且难以在客户端上被分析为可用的部分。  客户端需要知道哪些日期/时间范围有录制的数据，这样一来，无需太多猜测工作即可进行有效的请求。 因此，我们引入了新的查询 API – 客户端现在可以使用此服务器端 API 来发现内容。

其中，精度值可以是以下值之一：year、month、day 或 full（如下所示）。 

|Precision|year|月份|day|full|
|---|---|---|---|---|
|查询|`/availableMedia?precision=year&startTime=2018&endTime=2019`|`/availableMedia?precision=month& startTime=2018-01& endTime=2019-02`|`/availableMedia?precision=day& startTime=2018-01-15& endTime=2019-02-02`|`/availableMedia?precision=full& startTime=2018-01-15T10:08:11.123& endTime=2019-01-015T12:00:01.123`|
|响应|`{  "timeRanges":[{ "start":"2018", "end":"2019" }]}`|`{  "timeRanges":[{ "start":"2018-03", "end":"2019-01" }]}`|`{  "timeRanges":[    { "start":"2018-03-01", "end":"2018-03-07" },    { "start":"2018-03-09", "end":"2018-03-31" }  ]}`|全保真响应。 如果根本没有间隔，那么 start 为 startTime，end 为 endTime。|
|约束|&#x2022; startTime <= endTime<br/>&#x2022; 两者都应采用 YYYY 格式，否则会返回错误。<br/>&#x2022; 值可以间隔任意年数。<br/>&#x2022; 值为包含式。|&#x2022; startTime <= endTime<br/>&#x2022; 两者都应采用 YYYY-MM 格式，否则会返回错误。<br/>&#x2022; 值最多可间隔 12 个月。<br/>&#x2022; 值为包含式。|&#x2022; startTime <= endTime<br/>&#x2022; 两者都应采用 YYYY-MM-DD 格式，否则会返回错误。<br/>&#x2022; 值最多可间隔 31 天。<br/>值为包含式。|&#x2022;startTime < endTime<br/>&#x2022; 值最多可间隔 25 小时。<br/>&#x2022; 值为包含式。|

#### <a name="additional-request-format-considerations"></a>其他请求格式注意事项

* 所有时间均是 UTC 时间
* 需要精度查询字符串参数。  
* 对于 month、day 和 full 精度值，startTime 和 endTime 查询字符串参数是必需的。  
* startTime 和 endTime 查询字符串参数对于 year 精度值是可选的（不支持、支持其中一个、或者两者都支持）。  

    * 如果省略 startTime，则假定它为录制的第一个年份。
    * 如果省略 endTime，则假定它为录制的最后一个年份。
    * 例如，如果录制从 2011 年开始并一直持续到 2020 年，则：

        * /availableMedia?precision=year is the same as /availableMedia?precision=year&startTime=2011&endTime=2020
        * /availableMedia?precision=year&startTime=2015 is the same as /availableMedia?precision=year&startTime=2015&endTime=2020
        * /availableMedia?precision=year&endTime=2018 is the same as /availableMedia?precision=year&startTime=2011&endTime=2018

如果针对时间范围没有可用的媒体数据，则返回一个空列表。

如果资产不包含来自媒体图的录制，则将返回 HTTP 400 响应，并附带一条错误消息，说明此功能仅适用于通过媒体图录制的内容。

在给定某一查询类型的情况下，如果参数指定的持续时间大于最大时间范围所允许的持续时间，则将返回 HTTP 400，并附带一条错误消息，说明请求的查询类型所允许的最大时间范围。

假设时间范围对于给定的参数是有效的，那么对于数据录制时段之外的查询，不会返回错误。  这意味着，如果录制是在 7 小时前启动的，但客户要求提供从 {UtcNow – 24 小时} 到 UtcNow 的可用媒体，那么我们只会返回 {UtcNow – 7} 小时的数据。

### <a name="response-format"></a>响应格式  

availableMedia 调用返回一组时间值。

响应将为 JSON 正文，其中，对于 year（格式为 YYYY）、month（格式为 YYYY-MM）或 day（格式为 YYYY-MM-DD），timeRanges 值是由 ISO 8601 UTC 日期组成的数组，具体取决于请求的精度。  完整的 timeRanges 将包含格式为 ISO 8601 UTC 日期时间的开始值和结束值（格式为 YYYY-MM-DDThh:mm:ss.sssZ）。

对于 availableMedia 查询，API 将切断视频时间线。 时间线中的任何中断将显示为响应中的间隔。

#### <a name="response-example-for-availablemedia"></a>availableMedia 的响应示例

##### <a name="example-1-live-recording-with-no-gaps"></a>示例 1：无间隔的实时录制

下面是显示 2019 年 12 月 21 日所有可用媒体的响应，其中，录制已 100% 完成。 响应仅有一个开始/结束对。

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
   {
         "start":"2019-12-21T00:00:00.000Z", 
         "end":"2019-12-22T00:00:00.000Z"
    }
   ]
}
```

##### <a name="example-2-live-recording-with-a-2-second-gap"></a>示例 2：间隔为 2 秒的实时录制

假设出于某种原因，相机无法在一天内以 2 秒的间隔发送有效视频。 下面是显示 2019 年 12 月 21 日所有可用媒体的响应：

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:01:08Z"
    },
    {
         "start":"2019-12-21T04:01:10Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

##### <a name="example-3-live-recording-with-an-8-hour-gap"></a>示例 3：间隔为 8 小时的实时录制

假设相机和/或本地设施在一天中从 UTC 凌晨 4 点到 UTC 中午一直处于断电状态，持续了 8 小时，并且没有备份电源。 下面是显示这一天内所有可用媒体的响应

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:00:00Z"
    },
    {
         "start":"2019-12-21T12:00:00Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

#### <a name="example-4-live-recording-where-no-video-is-recorded-over-summer-holidays"></a>示例 4：在暑假期间不录制视频的实时录制

假设你仅在学校上课时才录制视频，并且在 6 月 17 日至 9 月 6 日之间停止了录制。 针对可用月份的一个查询如下所示：

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=month&startTime=2019-01&endTime=2019-12
{
   "timeRanges":[
    {
         "start":"2019-01", 
         "end":"2019-06"
    },
    {
         "start":"2019-09", 
         "end":"2019-12"
    }
   ]
}
```

如果随后要求你提供 6 月可用天数的录制内容，你会看到：

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=day&startTime=2019-06-01&endTime=2019-06-30
{
   "timeRanges":[
    {
         "start":"2019-06-01", 
         "end":"2019-06-17"
    }
   ]
}
```

##### <a name="example-5-live-recording-where-no-video-is-recorded-over-weekends-or-holidays"></a>示例 5：在周末或节假日不录制视频的实时录制

假设你仅在工作时间内录制了视频。 针对可用天数的一个查询如下所示

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=day&startTime=2020-02-01&endTime=2020-02-29
{
   "timeRanges":[
    {
         "start":"2020-02-03", 
         "end":"2020-02-07"
    },
    {
         "start":"2020-02-10", 
         "end":"2020-02-14"
    },
    {
         "start":"2020-02-18", // Monday Feb 17th was a holiday
         "end":"2020-02-21"
    },
    {
         "start":"2020-02-24", 
         "end":"2020-02-28"
    }
   ]
}
```

## <a name="time-range-filters"></a>时间范围筛选器

如上所述，这些筛选器可帮助你选择录制的某些部分（例如新年当天上午 9 点到 11 点）进行播放。 当通过 HLS 进行流式处理时，流式处理 URL 类似于 `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl).m3u8`。 若要选择录制的部分，需要添加 startTime 和 endTime 参数，例如：`https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T08:00:00Z,endTime=2019-12-21T10:00:00Z).m3u8`。 因此，时间范围筛选器是 URL 修饰符，用于描述包含在流式处理清单中的录制时间线的部分：

* `starttime` 是 ISO 8601 日期/时间戳，描述返回的清单中视频时间线的预期开始时间。
* `endtime` 是 ISO 8601 日期/时间戳，描述清单中返回的视频时间线的预期结束时间。

此类清单的最大长度（以时间为单位）不能超过 24 小时。

下面是对筛选器的约束。

|startTime| endTime |结果|
|---|---|---|
|不存在 |不存在 |返回资产中视频的最近部分，最大长度为 4 小时。<br/><br/>如果资产最近尚未被写入（没有任何新的视频数据传入），则返回点播 (VoD) 清单。 否则，将返回实时清单。|
|现值|不存在|    如果此类清单小于 24 小时，则返回一个清单，此清单包含任何比 startTime 更新的可用视频。<br/>如果清单的长度超过 24 小时，则返回错误。<br/>如果资产最近尚未被写入（没有任何新的视频数据传入），则返回点播 (VoD) 清单。 否则，将返回实时清单。
|不存在|现值 |错误 – 如果 startTime 存在，endTime 则是必需的。|
|现值|现值|返回一个 VoD 清单，此清单包含 startTime 和 endTime 之间可用的任何视频。<br/>跨度（startTime，endTime）不能超过 24 小时。|

### <a name="response-examples"></a>响应示例  

#### <a name="example-1-live-recording-with-no-gaps"></a>示例 1：无间隔的实时录制

下面是显示 2019 年 12 月 21 日所有可用媒体的响应，其中，录制已 100% 完成。 响应仅有一个开始/结束对。

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
   {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

在连续录制的情况下，你可以在该 Start/End 对中的任何时段内请求 HLS 或 DASH 清单 – 只要跨度为 24 小时或更小即可。 下面是上述 4 小时的 HLS 清单请求的示例：

`GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T14:00:00.000Z,endTime=2019-12-21T18:00:00.000Z).m3u8`

#### <a name="example-2-live-recording-with-a-2-second-gap"></a>示例 2：间隔为 2 秒的实时录制

假设出于某种原因，相机无法在一天内以 2 秒的间隔发送有效视频。 下面是显示 2019 年 12 月 21 日可用媒体的响应：

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:01:08Z"
    },
    {
         "start":"2019-12-21T04:01:10Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

对于如上所示的录制，再次强调，只要跨度小于 24 小时，你就可以使用任何 startTime/endTime 对来请求 HLS 和 DASH 清单。 如果这些值在 04:01:08AM UTC 时跨越了间隔，那么根据这些协议的相关规范，返回的清单将指示出媒体时间线中的中断。

#### <a name="example-3-live-recording-with-an-8-hour-gap"></a>示例 3：间隔为 8 小时的实时录制

假设相机和/或本地设施在一天中从 UTC 凌晨 4 点到 UTC 中午一直处于断电状态，持续了 8 小时，并且没有备份电源。 下面是显示这一天内所有可用媒体的响应。

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:00:00Z"
    },
    {
         "start":"2019-12-21T12:00:00Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

对于此类录制：

* 如果你请求一个清单，其中 startTime/endTime 范围筛选器都在时间线的“可用”部分内，即从午夜到凌晨 4 点或从中午到午夜，此服务则将返回一个清单，其中包含从 startTime 到 endTime 的时间，例如：

    `GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T14:01:00.000Z,endTime=2019-12-21T03:00:00.000Z).m3u8`
* 如果你请求一个清单，其中 startTime 和 endTime 都位于中间的“缺口”内（例如从 UTC 上午 8 点到 10 点），此服务的行为则与资产筛选器会导致空结果时的行为相同。

    [这是一个获取空响应的请求] `GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T08:00:00.000Z,endTime=2019-12-21T10:00:00.000Z).m3u8`
* 如果你请求一个清单，其中只有一个 startTime 或 endTime 位于“缺口”内，则返回的清单将仅包含该时间跨度的一部分。 它会将 startTime 或 endTime 值与最接近的有效边界对齐。 例如，如果要求上午 10 点到下午 1 点这 3 小时的流，响应则将包含中午 12 点到下午 1 点这 1 小时的媒体

    `GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T10:00:00.000Z,endTime=2019-12-21T13:00:00.000Z).m3u8`
    
    返回的清单将从 2019-12-21T12:00:00.000Z 开始，即使此请求要求从上午 10 点开始。 使用播放器插件构建视频管理系统时，要注意向观看者发出此信号。 不知道的观看者会感到困惑，为什么播放时间线从中午开始，而不是按请求从上午 10 点开始

## <a name="recording-and-playback-latencies"></a>录制和播放延迟

使用 IoT Edge 上的实时视频分析来记录到资产时，你将指定 segmentLength 属性，该属性指示模块在将视频录制到云之前聚合视频的最小持续时间（以秒为单位）。 例如，如果将 segmentLength 设置为 300，则在上传一段 5 分钟的“区块”之前，该模块将首先累积 5 分钟的视频，然后在下一个 5 分钟进入累积模式，然后再次上传。 增加 segmentLength 有助于降低 Azure 存储事务成本，因为读取次数和写入次数将不会超过每 segmentLength 秒一次。

因此，从媒体服务流式处理视频将至少延迟这么长时间。 

确定播放延迟（延迟处于相机前事件发生的时间与可以在播放设备上观看的时间之间）的另一个因素是画面组 [GOP](https://en.wikipedia.org/wiki/Group_of_pictures) 持续时间。 正如[通过使用 3 个简单的技术来降低实时流的延迟](https://medium.com/vrt-digital-studio/reducing-the-delay-of-live-streams-by-using-3-simple-techniques-e8e028b0a641)所解释的一样，GOP 持续时间越长，延迟越长。 通常会在监视和安全性方案中使用 IP 相机，将使用 GOP 的时间配置为超过 30 秒。 这会对总体延迟会产生很大的影响。

## <a name="next-steps"></a>后续步骤

[连续视频录制教程](continuous-video-recording-tutorial.md)
