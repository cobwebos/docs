---
title: 播放录制-Azure
description: 你可以使用 IoT Edge 上的实时视频分析来录制连续视频，从而将视频录制到云中几周或几个月。 还可以通过基于事件的录制，将记录限制为感兴趣的剪辑。 本文讨论如何播放录制。
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: 6222d2c05b2fe05945d4bcbef6dbb0d64bd4726a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84260380"
---
# <a name="playback-of-recordings"></a>播放录制 

## <a name="pre-read"></a>预读  

* [视频播放](video-playback-concept.md)
* [连续视频录制](continuous-video-recording-concept.md)
* [基于事件的视频录制](event-based-video-recording-concept.md)

## <a name="background"></a>背景  

你可以在 IoT Edge 上使用实时视频分析来进行[连续视频录制](continuous-video-recording-concept.md)（CVR），以便你可以将视频录制到云中几周或几个月。 还可以通过[基于事件的视频录制](event-based-video-recording-concept.md)（EVR）将录制限制为感兴趣的剪辑。 

你的媒体服务帐户已链接到 Azure 存储帐户，当你将视频录制到云时，内容将写入到[媒体服务资产](terminology.md#asset)中。 媒体服务允许你在录制完成后或记录正在进行时[流式传输该内容](terminology.md#streaming)。 媒体服务提供了通过 HLS 或 MPEG-短划线协议将流交付到播放设备（客户端）所需的功能。 有关更多详细信息，请参阅[视频播放](video-playback-concept.md)文章。 使用媒体服务 Api 来生成必要的流式处理 Url，客户端使用这些 Url 播放视频 & 音频。 若要构造资产的流 URL，请参阅[创建流式处理定位符和生成 url](../latest/create-streaming-locator-build-url.md)。 为资产创建了流式处理 URL 后，就可以和应将 URL 与内容管理系统中的视频源（即相机）关联。

例如，当通过 HLS 进行流式处理时，流 URL 将如下所示 `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl).m3u8` 。 对于 MPEG-短线，其外观类似于 `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=mpd-time-cmaf).mpd` 。

这会返回一个清单文件，其中的所有内容都描述正在传递的流的总持续时间，以及它是表示预先录制的内容还是正在进行的 "实时" 源。

### <a name="live-vs-vod"></a>Live 与 VoD  

流式处理协议（如 HLS 和 MPEG 破折号）是为处理实时视频流式处理等方案而编写的，并可按需或预先录制的内容（如电视节目和电影）进行流式处理。 对于 live 视频，HLS 和 MPEG 虚线客户端旨在开始从 "最近的时间" 开始播放。 但对于电影，观看者希望能够从一开始就开始，并在选择时跳过。 HLS 和 MPEG 虚线清单具有标志，这些标志向客户端指示视频是表示实时流还是预先录制的内容。
此概念还适用于包含 IoT Edge 上使用实时视频分析录制的视频的资产的 HLS 和 MPEG-短线流。

## <a name="browsing-and-selective-playback-of-recordings"></a>浏览和选择性地播放录制  

请考虑在 IoT Edge 上使用实时视频分析的情况，以便在一年中的一天内（一年中的一年）将视频录制到10AM。 也许您只能从7AM-7PM 到7AM-7PM 的国家/地区假期记录视频。 在这两种情况下，如果尝试浏览并观看视频录制，则需要：

* 确定记录中视频的日期/小时的一种方法。
* 一种方法，用于选择要播放的记录的一部分（例如，上午9点到11AM）。

媒体服务提供了一个查询 API （availableMedia），用于处理第一个问题，并提供了时间范围筛选器（startTime，endTime）来解决第二个问题。

## <a name="query-api"></a>查询 API 

当使用 CVR 时，播放设备（客户端）无法请求包含整个记录播放功能的清单。  多年记录将生成一个清单文件，该文件对于播放而言太大，因而无法在客户端上分析为可用部分。  客户端需要知道哪些日期时间范围具有记录中的数据，以便无需猜测工作就能进行有效的请求。 这就是新查询 API 的作用，客户端现在可以使用此服务器端 API 来发现内容。

其中，精度值可以是以下值之一： year、month、day 或 full （如下所示）。 

|精度|year|月份|day|完全|
|---|---|---|---|---|
|查询|`/availableMedia?precision=year&startTime=2018&endTime=2019`|`/availableMedia?precision=month& startTime=2018-01& endTime=2019-02`|`/availableMedia?precision=day& startTime=2018-01-15& endTime=2019-02-02`|`/availableMedia?precision=full& startTime=2018-01-15T10:08:11.123& endTime=2019-01-015T12:00:01.123`|
|响应|`{  "timeRanges":[{ "start":"2018", "end":"2019" }]}`|`{  "timeRanges":[{ "start":"2018-03", "end":"2019-01" }]}`|`{  "timeRanges":[    { "start":"2018-03-01", "end":"2018-03-07" },    { "start":"2018-03-09", "end":"2018-03-31" }  ]}`|完全保真响应。 如果根本没有空白，则开始时间为 startTime，end 为 endTime。|
|约束|&#x2022;startTime <= endTime<br/>&#x2022;二者都应为 YYYY 格式，否则返回错误。<br/>&#x2022;值可以是任意数量的年份。<br/>&#x2022;值为 "包含"。|&#x2022;startTime <= endTime<br/>&#x2022;都应采用 YYYY-MM-DD 格式，否则返回错误。<br/>&#x2022;值最多可以是12个月。<br/>&#x2022;值为 "包含"。|&#x2022;startTime <= endTime<br/>&#x2022;都应采用 YYYY-MM-DD 格式，否则返回错误。<br/>&#x2022;值最多可以是31天。<br/>值是包含值。|&#x2022;startTime < endTime<br/>&#x2022;值最多可以是25小时。<br/>&#x2022;值为 "包含"。|

#### <a name="additional-request-format-considerations"></a>其他请求格式注意事项

* 所有时间都采用 UTC 格式
* 需要精度查询字符串参数。  
* StartTime 和 endTime 查询字符串参数对于月、日和完全精度值是必需的。  
* StartTime 和 endTime 查询字符串参数对于年份精度值是可选的（"无"、"或" 都支持）。  

    * 如果省略 startTime，则假定它是记录中的第一年。
    * 如果省略 endTime，则假定它是记录中的最后一年。
    * 例如，如果您的记录从2011开始，并一直持续到2020，则：

        * /availableMedia？ precision = year 等于/availableMedia？精度 = year&startTime = 2011&endTime = 2020
        * /availableMedia？ precision = year&startTime = 2015 与/availableMedia？精度 = year&startTime = 2015&endTime = 2020
        * /availableMedia？ precision = year&endTime = 2018 与/availableMedia？ precision = year&startTime = 2011&endTime = 2018

如果时间范围中没有可用的媒体数据，则返回一个空列表。

如果资产不包含媒体图中的记录，则返回 HTTP 400 响应，并显示一条错误消息，说明此功能仅适用于通过 media graph 记录的内容。

如果参数指定的时间长度大于给定查询类型的最大时间范围允许的时间，则将返回 HTTP 400，并返回一条错误消息，说明请求的查询类型允许的最大时间范围。

假设时间范围对于给定的参数是有效的，则不会为记录中数据的时间范围之外的查询返回错误。  意思是，如果录制在7小时前启动，而客户要求提供从 {UtcNow –24小时} 到 UtcNow 的可用媒体，那么我们只会返回 {UtcNow – 7} 小时的数据。

### <a name="response-format"></a>响应格式  

AvailableMedia 调用返回一组时间值。

响应将为 JSON 正文，其中 timeRanges 的值是一系列 ISO 8601 UTC 日期（以年为格式）、月份（YYYY-MM-DD 格式）或日（YYYY-MM-DD），具体取决于请求的精度。  完整的 timeRanges 将包含格式为 ISO 8601 UTC 日期时间的开始值和结束值（以 YYYY-MM-DD-Yyyy-mm-ddthh： MM： ss. sssZ 格式）。

对于 availableMedia 查询，API 将关闭视频时间线。 时间线中的任何中断将显示为响应中的间隔。

#### <a name="response-example-for-availablemedia"></a>AvailableMedia 的响应示例

##### <a name="example-1-live-recording-with-no-gaps"></a>示例1：实时录制，无间隔

下面是显示2019年12月21日所有可用媒体的响应，其中，记录已完成100%。 响应仅有一个开始/结束对。

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

##### <a name="example-2-live-recording-with-a-2-second-gap"></a>示例2：具有2秒间隙的实时录制

出于某种原因，照相机无法在一天的2秒时间间隔内发送有效视频。 下面是显示2019年12月21日所有可用媒体的响应：

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

##### <a name="example-3-live-recording-with-an-8-hour-gap"></a>示例3：具有8小时间隙的实时录制

假设照相机和/或本地设施在一天的8小时内断电，从上午4点 UTC 到中午 UTC，并且没有备份电源。 下面显示了一天中所有可用媒体的响应

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

#### <a name="example-4-live-recording-where-no-video-is-recorded-over-summer-holidays"></a>示例4：在夏季假期内未记录视频的实时录制

假设您仅在学校处于会话中时才录制视频，而录制已于6月17日至9月6日停止。 可用月份的查询如下所示：

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

如果随后要求你提供6月的可用天数，你会看到：

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

##### <a name="example-5-live-recording-where-no-video-is-recorded-over-weekends-or-holidays"></a>示例5：在周末或假日不记录视频的实时录制

假设您仅在工作时间内录制了视频。 可用天数的查询如下所示

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

如上所述，这些筛选器可帮助你选择录制的某些部分（例如，在新的几天上午9点到11AM）进行播放。 通过 HLS 进行流式处理时，流式处理 URL 如下所示 `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl).m3u8` 。 为了选择部分记录，需要添加 startTime 和 endTime 参数，如： `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T08:00:00Z,endTime=2019-12-21T10:00:00Z).m3u8` 。 因此，时间范围筛选器是 URL 修饰符，用于描述包含在流式处理清单中的记录时间线部分：

* `starttime`是 ISO 8601 日期时间戳，用于描述返回的清单中视频时间线的所需开始时间。
* `endtime`是 ISO 8601 日期时间戳，用于描述清单中返回的视频时间线的所需结束时间。

此类清单的最大长度（以时间为单位）不能超过24小时。

下面是对筛选器的约束。

|startTime| endTime |结果|
|---|---|---|
|不存在 |不存在 |返回资产中最新的视频部分，最大长度为4小时。<br/><br/>如果该资产尚未写入（最近未传入任何新视频数据），则返回点播（VoD）清单。 否则，将返回动态清单。|
|现值|不存在|    如果此类清单将短于24小时，则返回一个清单，其中包含的任何可用视频均比 startTime 新的视频。<br/>如果清单的长度超过24小时，则返回错误。<br/>如果该资产尚未写入（最近未传入任何新视频数据），则返回点播（VoD）清单。 否则，将返回动态清单。
|不存在|现值 |Error –如果存在 startTime，则 endTime 是必需的。|
|现值|现值|返回具有 startTime 和 endTime 之间可用的任何视频的 VoD 清单。<br/>跨度（startTime，endTime）不能超过24小时。|

### <a name="response-examples"></a>响应示例  

#### <a name="example-1-live-recording-with-no-gaps"></a>示例1：实时录制，无间隔

下面是显示2019年12月21日所有可用媒体的响应，其中，记录已完成100%。 响应仅有一个开始/结束对。

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

如果记录是连续的，则可以在该开始/结束对中的任何时间段内请求 HLS 或短划线清单–只要范围为24小时或更短时间。 以上是4小时 HLS 清单请求的示例：

`GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T14:00:00.000Z,endTime=2019-12-21T18:00:00.000Z).m3u8`

#### <a name="example-2-live-recording-with-a-2-second-gap"></a>示例2：具有2秒间隙的实时录制

出于某种原因，照相机无法在一天的2秒时间间隔内发送有效视频。 下面是显示2019年12月21日可用媒体的响应：

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

使用如上所示的记录，只要范围低于24小时，你就可以使用任何 startTime/endTime 对请求 HLS 和短线清单。 如果这些值 straddled 04：01： 08AM UTC 处的间隔，则返回的清单会根据这些协议的相关规格，通知媒体时间线中的中断。

#### <a name="example-3-live-recording-with-an-8-hour-gap"></a>示例3：具有8小时间隙的实时录制

假设照相机和/或本地设施在一天的8小时内断电，从上午4点 UTC 到中午 UTC，并且没有备份电源。 下面显示了一天中所有可用媒体的响应。

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

使用此类记录：

* 如果你请求一个清单，其中两个 startTime/endTime 范围筛选器都在时间线的 "可用" 部分（即从午夜到4AM，或从中午到午夜）内，则该服务将返回一个清单，其中包含从 startTime 到 endTime 的时间，例如：

    `GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T14:01:00.000Z,endTime=2019-12-21T03:00:00.000Z).m3u8`
* 如果你请求一个清单，其中 startTime 和 endTime 位于中间的 "洞" 内（例如从早晨8点到 10AM UTC），则该服务的行为方式与资产筛选器导致空结果的行为相同。

    [这是一个获取空响应的请求]`GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T08:00:00.000Z,endTime=2019-12-21T10:00:00.000Z).m3u8`
* 如果你请求一个清单，其中只有一个 startTime 或 endTime 位于 "洞" 内，则返回的清单将只包含该 timespan 的一部分。 它会将 startTime 或 endTime 值与最接近的有效边界对齐。 例如，如果要求从10AM 到下午3小时的流，则响应将包含12个月12点到下午1点的媒体

    `GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T10:00:00.000Z,endTime=2019-12-21T13:00:00.000Z).m3u8`
    
    返回的清单将从 2019-12-21T12：00： 00.000 Z 开始，即使请求要求开始10AM。 使用播放机插件构建视频管理系统时，应小心向查看器发出通知。 不了解的查看器将与播放时间线从中午开始，而不是按要求10AM

## <a name="recording-and-playback-latencies"></a>录制和播放延迟

使用 IoT Edge 上的实时视频分析来记录到资产时，将指定 segmentLength 属性，该属性指示模块在将视频记录到云之前聚合最小持续时间（以秒为单位）。 例如，如果将 segmentLength 设置为300，则在上传5分钟 "组块" 之前，该模块将累积5分钟的视频，然后在下5分钟进入累积模式，然后重新上传。 提高 segmentLength 具有降低 Azure 存储事务成本的好处，因为读取次数和写入次数将不再超过每隔 segmentLength 秒一次。

因此，媒体服务中的视频流式处理将至少延迟这么长时间。 

确定播放延迟的另一个因素（在摄像机前面播放事件的时间与可以在播放设备上观看的时间之间的延迟）是一组图片为 " [GOP](https://en.wikipedia.org/wiki/Group_of_pictures)持续时间"。 由于[使用3个简单的技术解释了实时流的延迟](https://medium.com/vrt-digital-studio/reducing-the-delay-of-live-streams-by-using-3-simple-techniques-e8e028b0a641)，因此在 GOP 持续时间内越长，延迟时间就越长。 通常会在监视和安全方案中使用 IP 照相机，将 Gop 配置为使用超过30秒的时间。 这会对总体延迟产生很大的影响。

## <a name="next-steps"></a>后续步骤

[连续视频录制教程](continuous-video-recording-tutorial.md)
