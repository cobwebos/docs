---
title: 空间分析操作
titleSuffix: Azure Cognitive Services
description: 空间分析操作。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: aahi
ms.openlocfilehash: 80f0d29de6b3013ad02ed1a5d34bebdf81a8766b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91628207"
---
# <a name="spatial-analysis-operations"></a>空间分析操作

空间分析支持分析来自照相机设备的实时流式处理视频。 对于配置的每个照相机设备，空间分析的操作会生成发送到 Azure IoT 中心实例的 JSON 消息输出流。 

空间分析容器实现以下操作：

| 操作标识符| 说明|
|---------|---------|
| cognitiveservices account. spatialanalysis-personcount | 计算照相机的 "视图" 字段中指定区域的人员数。 <br> 发出一个初始 _personCountEvent_ 事件，然后在计数发生更改时 _personCountEvent_ 事件。  |
| cognitiveservices account. spatialanalysis-personcrossingline | 跟踪用户在相机的视图字段中跨越指定线条的时间。 <br>当人员跨越行并提供方向信息时发出 _personLineEvent_ 事件。 
| cognitiveservices account. spatialanalysis-personcrossingpolygon | 跟踪用户在相机的视图字段中跨越指定线条的时间。 <br> 当人员跨越区域并提供方向信息时发出 _personLineEvent_ 事件。 |
| cognitiveservices account. spatialanalysis-persondistance | 跟踪用户违反距离规则的时间。 <br> 定期发出 _personDistanceEvent_ ，其中包含每个距离冲突的位置。 |

版本中还提供了所有这些操作，可以在 `.debug` 处理视频帧时将其可视化。 需要 `xhost +` 在主机计算机上运行才能启用视频帧和事件的可视化。

| 操作标识符| 说明|
|---------|---------|
| cognitiveservices account. spatialanalysis-personcount | 计算照相机的 "视图" 字段中指定区域的人员数。 <br> 发出一个初始 _personCountEvent_ 事件，然后在计数发生更改时 _personCountEvent_ 事件。  |
| cognitiveservices account. spatialanalysis-personcrossingline | 跟踪用户在相机的视图字段中跨越指定线条的时间。 <br>当人员跨越行并提供方向信息时发出 _personLineEvent_ 事件。 
| cognitiveservices account. spatialanalysis-personcrossingpolygon | 跟踪用户在相机的视图字段中跨越指定线条的时间。 <br> 当人员跨越区域并提供方向信息时发出 _personLineEvent_ 事件。 |
| cognitiveservices account. spatialanalysis-persondistance | 跟踪用户违反距离规则的时间。 <br> 定期发出 _personDistanceEvent_ ，其中包含每个距离冲突的位置。 |

还可以将 [实时视频分析](https://aka.ms/lva-spatial-analysis) 作为视频 AI 模块运行空间分析。 

<!--more details on the setup can be found in the [LVA Setup page](LVA-Setup.md). Below is the list of the operations supported with Live Video Analytics. -->

| 操作标识符| 说明|
|---------|---------|
| cognitiveservices account. spatialanalysis-personcount. livevideoanalytics | 计算照相机的 "视图" 字段中指定区域的人员数。 <br> 发出一个初始 _personCountEvent_ 事件，然后在计数发生更改时 _personCountEvent_ 事件。  |
| cognitiveservices account. spatialanalysis-personcrossingline. livevideoanalytics | 跟踪用户在相机的视图字段中跨越指定线条的时间。 <br>当人员跨越行并提供方向信息时发出 _personLineEvent_ 事件。 
| cognitiveservices account. spatialanalysis-personcrossingpolygon. livevideoanalytics | 跟踪用户在相机的视图字段中跨越指定线条的时间。 <br> 当人员跨越区域并提供方向信息时发出 _personLineEvent_ 事件。 |
| cognitiveservices account. spatialanalysis-persondistance. livevideoanalytics | 跟踪用户违反距离规则的时间。 <br> 定期发出 _personDistanceEvent_ ，其中包含每个距离冲突的位置。 |

实时视频分析操作还可用于 `.debug` 版本 (例如 cognitiveservices account. spatialanalysis-personcount) ，它可以将视频帧可视化为正在处理的帧。 你需要 `xhost +` 在主机计算机上运行，以便能够可视化视频帧和事件

> [!IMPORTANT]
> 计算机视觉 AI 模型通过围绕人体间的边界框检测并定位视频素材和输出中的人机状态。 AI 模型不会尝试检测人脸或发现个人的身份或人口统计信息。

这些是每个空间分析操作所需的参数。

| 操作参数| 说明|
|---------|---------|
| Operation ID | 上表中的操作标识符。|
| enabled | 布尔值： true 或 false|
| VIDEO_URL| 照相机设备 (的 RTSP url 示例： `rtsp://username:password@url`) 。 空间分析通过 RTSP、http 或硬件支持-264 编码流 |
| VIDEO_SOURCE_ID | 照相机设备或视频流的友好名称。 这将随事件 JSON 输出一起返回。|
| VIDEO_IS_LIVE| 对于照相机设备为 True;对于录制的视频为 false。|
| VIDEO_DECODE_GPU_INDEX| 用于解码视频帧的 GPU。 默认情况下，它是0。 应与 `gpu_index` 其他节点配置（如）中的相同 `VICA_NODE_CONFIG` `DETECTOR_NODE_CONFIG` 。|
| DETECTOR_NODE_CONFIG | JSON，指示要在其上运行检测节点的 GPU。 应采用以下格式： `"{ \"gpu_index\": 0 }",`|
| SPACEANALYTICS_CONFIG | 区域和行的 JSON 配置，如下所述。|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcount"></a>Cognitiveservices account. spatialanalysis 的区域配置-personcount

 这是用于配置区域的 SPACEANALYTICS_CONFIG 参数的 JSON 输入示例。 你可以为此操作配置多个区域。

```json
{
"zones":[{
    "name": "lobbycamera"
    "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
    "threshold": 50.00,
    "events":[{
        "type": "count",
        "config":{
            "trigger": "event",
            "output_frequency": 1
      }
    }]
}
```

| 名称 | 类型| 说明|
|---------|---------|---------|
| `zones` | list| 区域列表。 |
| `name` | 字符串| 此区域的友好名称。|
| `polygon` | list| 每个值对表示多边形顶点的 x、y。 多边形表示跟踪或计数人员的区域，并且多边形点基于标准化坐标 (0-1) ，其中左上角 (0.0，0.0) ，右下角 (1.0，1.0) 。   
| `threshold` | FLOAT| 当 AI 模型的置信度大于或等于此值时，将出口事件。 |
| `type` | 字符串| 对于 **cognitiveservices account，spatialanalysis-personcount** `count` 。|
| `trigger` | 字符串| 用于发送事件的触发器的类型。 支持的值 `event` 用于在计数发生更改或 `interval` 定期发送事件时发送事件，而不考虑计数是否已更改。
| `interval` | 字符串| 在激发事件之前聚合人员计数的时间（以秒为单位）。 操作将继续以恒定速率分析场景，并返回超过该间隔的最常见计数。 聚合间隔适用于 `event` 和 `interval` 。|

### <a name="line-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingline"></a>Cognitiveservices account 的线条配置。 spatialanalysis-personcrossingline

这是配置行的 SPACEANALYTICS_CONFIG 参数的 JSON 输入示例。 您可以为此操作配置多个交叉行。

```json
{
"lines":[{
    "name": "doorcamera" 
    "line": {
        "start": {"x": 0, "y": 0.5},
        "end": {"x": 1, "y": 0.5}
            },
    "threshold": 50.00,
    "events":[{
        "type": "linecrossing",
        "config":{
            "trigger": "event"
            }
        }]
    }]
}
```

| 名称 | 类型| 说明|
|---------|---------|---------|
| `lines` | list| 行的列表。|
| `name` | 字符串| 此行的友好名称。|
| `line` | list| 线条的定义。 这是一个方向线，可让你了解 "entry" 与 "exit"。|
| `start` | 值对| x，y 坐标用于线条的起点。 Float 值表示顶点相对于顶部、左侧的位置。 若要计算绝对 x，y 值，请将这些值与帧大小相乘。 |
| `end` | 值对| x，y 坐标用于线条的终点。 Float 值表示顶点相对于顶部、左侧的位置。 若要计算绝对 x，y 值，请将这些值与帧大小相乘。 |
| `threshold` | FLOAT| 当 AI 模型的置信度大于或等于此值时，将出口事件。 |
| `type` | 字符串| 对于 **cognitiveservices account，spatialanalysis-personcrossingline** `linecrossing` 。|
|`trigger`|字符串|用于发送事件的触发器的类型。<br>支持的值： "事件"：当有人跨越行时激发。|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon"></a>Cognitiveservices account. spatialanalysis 的区域配置-personcrossingpolygon

这是用于配置区域的 SPACEANALYTICS_CONFIG 参数的 JSON 输入示例。 你可以为此操作配置多个区域。

 ```json
{
"zones":[{
    "name": "queuecamera"
    "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
    "threshold": 50.00,
    "events":[{
        "type": "zone_crossing",
        "config":{
            "trigger": "event"
            }
        }]
    }]
}
```

| 名称 | 类型| 说明|
|---------|---------|---------|
| `zones` | list| 区域列表。 |
| `name` | 字符串| 此区域的友好名称。|
| `polygon` | list| 每个值对表示多边形顶点的 x、y。 多边形表示跟踪或计数人员的区域。 Float 值表示顶点相对于顶部、左侧的位置。 若要计算绝对 x，y 值，请将这些值与帧大小相乘。 
| `threshold` | FLOAT| 当 AI 模型的置信度大于或等于此值时，将出口事件。 |
| `type` | 字符串| 对于 **cognitiveservices account，spatialanalysis-personcrossingpolygon 此项** 应为 `enter` 或 `exit` 。|
| `trigger`|字符串|用于发送事件的触发器的类型<br>支持的值： "事件"：某人进入或退出区域时触发。|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-persondistance"></a>Cognitiveservices account. spatialanalysis 的区域配置-persondistance

这是用于配置 cognitiveservices account 的区域的 SPACEANALYTICS_CONFIG 参数的 JSON 输入示例。 **spatialanalysis-persondistance**。 你可以为此操作配置多个区域。

```json
{
"zones":[{
    "name": "lobbycamera",
    "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
    "threshold": 35.00,
    "events":[{
        "type": "persondistance",
        "config":{
            "trigger": "event",
            "output_frequency":1,
            "minimum_distance_threshold":6.0,
            "maximum_distance_threshold":35.0
            }
        }]
    }]
}
```

| 名称 | 类型| 说明|
|---------|---------|---------|
| `zones` | list| 区域列表。 |
| `name` | 字符串| 此区域的友好名称。|
| `polygon` | list| 每个值对表示多边形顶点的 x、y。 多边形表示统计人员的区域，以及用户之间的距离。 Float 值表示顶点相对于顶部、左侧的位置。 若要计算绝对 x，y 值，请将这些值与帧大小相乘。 
| `threshold` | FLOAT| 当 AI 模型的置信度大于或等于此值时，将出口事件。 |
| `type` | 字符串| 对于 **cognitiveservices account，spatialanalysis-persondistance** `people_distance` 。|
| `trigger` | 字符串| 用于发送事件的触发器的类型。 支持的值 `event` 用于在计数发生更改或 `interval` 定期发送事件时发送事件，而不考虑计数是否已更改。
| `interval` | 字符串 | 触发事件之前聚合冲突的时间（以秒为单位）。 聚合间隔适用于 `event` 和 `interval` 。|
| `output_frequency` | int | 出口事件的速率。 When `output_frequency` = X 时，每个 X 事件都是出口，例如 `output_frequency` = 2 表示输出每个其他事件。 Output_frequency 适用于 `event` 和 `interval` 。|
| `minimum_distance_threshold` | FLOAT| 当用户小于该距离时，将触发 "TooClose" 事件的距离（以英尺为间隔）。|
| `maximum_distance_threshold` | FLOAT| 当人们大于该距离时，将触发 "TooFar" 事件的距离（以英尺为间隔）。|

这是配置 **cognitiveservices account-persondistance** 区域的 DETECTOR_NODE_CONFIG 参数的 JSON 输入示例。

```json
{ 
"gpu_index": 0, 
"do_calibration": true
}
```

| 名称 | 类型| 说明|
|---------|---------|---------|
| `gpu_index` | 字符串| 此操作将在其上运行的 GPU 索引。|
| `do_calibration` | 字符串 | 指示已启用校准。 `do_calibration` 若要使 **cognitiveservices account** 正常工作，必须为 true。|

请参阅 [相机位置](spatial-analysis-camera-placement.md)  准则，了解区域和线路配置。

## <a name="spatial-analysis-operation-output"></a>空间分析操作输出

每个操作的事件都出口为 JSON 格式的 Azure IoT 中心。

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcount-ai-insights"></a>Cognitiveservices account 的 JSON 格式。 spatialanalysis-personcount AI Insights

此操作的事件输出的示例 JSON。

```json
{
    "events": [
        {
            "id": "b013c2059577418caa826844223bb50b",
            "type": "personCountEvent",
            "detectionIds": [
                "bc796b0fc2534bc59f13138af3dd7027",
                "60add228e5274158897c135905b5a019"
            ],
            "properties": {
                "personCount": 2
            },
            "zone": "lobbycamera",
            "trigger": "event"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:06:57.224Z",
        "width": 608,
        "height": 342,
        "frameId": "1400",
        "cameraCalibrationInfo": {
            "status": "Complete",
            "cameraHeight": 10.306597709655762,
            "focalLength": 385.3199462890625,
            "tiltupAngle": 1.0969393253326416
        },
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "bc796b0fc2534bc59f13138af3dd7027",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.612683747944079,
                        "y": 0.25340268765276636
                    },
                    {
                        "x": 0.7185954043739721,
                        "y": 0.6425260577285499
                    }
                ]
            },
            "confidence": 0.9559211134910583,
            "centerGroundPoint": {
                "x": 0.0,
                "y": 0.0
            },
            "metadataType": ""
        },
        {
            "type": "person",
            "id": "60add228e5274158897c135905b5a019",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.22326200886776573,
                        "y": 0.17830915618361087
                    },
                    {
                        "x": 0.34922296122500773,
                        "y": 0.6297955429344847
                    }
                ]
            },
            "confidence": 0.9389744400978088,
            "centerGroundPoint": {
                "x": 0.0,
                "y": 0.0
            },
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| 事件字段名称 | 类型| 说明|
|---------|---------|---------|
| `id` | 字符串| 事件 ID|
| `type` | 字符串| 事件类型|
| `detectionsId` | array| 触发此事件的人员检测的唯一标识符的大小为1的数组|
| `properties` | collection| 值的集合|
| `trackinId` | 字符串| 检测到的人员的唯一标识符|
| `status` | 字符串| "Enter" 或 "Exit"|
| `side` | int| 人员所交叉的多边形的边号|
| `zone` | 字符串 | 表示已越过区域的多边形的 "名称" 字段|
| `trigger` | 字符串| 触发器类型为 "事件" 或 "间隔"，具体取决于中的值 `trigger` SPACEANALYTICS_CONFIG|

| 检测字段名称 | 类型| 说明|
|---------|---------|---------|
| `id` | 字符串| 检测 ID|
| `type` | 字符串| 检测类型|
| `region` | collection| 值的集合|
| `type` | 字符串| 区域类型|
| `points` | collection| 当区域类型为矩形时，左上方和右下点 |
| `confidence` | FLOAT| 算法置信度|

| SourceInfo 字段名称 | 类型| 说明|
|---------|---------|---------|
| `id` | 字符串| 相机 ID|
| `timestamp` | date| 发出 JSON 有效负载时的 UTC 日期|
| `width` | int | 视频帧宽度|
| `height` | int | 视频帧高度|
| `frameId` | int | 帧标识符|
| `cameraCallibrationInfo` | collection | 值的集合|
| `status` | 字符串 | 指示相机校准到地面的情况是否为 "完成"|
| `cameraHeight` | FLOAT | 相机上地面的高度。 这是从自动校准推断出来的。 |
| `focalLength` | FLOAT | 以像素为单位的摄像机焦点长度。 这是从自动校准推断出来的。 |
| `tiltUpAngle` | FLOAT | 相机倾斜角度与垂直。 这是从自动校准推断出来的。|

| SourceInfo 字段名称 | 类型| 说明|
|---------|---------|---------|
| `id` | 字符串| 相机 ID|
| `timestamp` | date| 发出 JSON 有效负载时的 UTC 日期|
| `width` | int | 视频帧宽度|
| `height` | int | 视频帧高度|
| `frameId` | int | 帧标识符|


### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingline-ai-insights"></a>Cognitiveservices account 的 JSON 格式。 spatialanalysis-personcrossingline AI Insights

此操作输出的检测的示例 JSON。

```json
{
    "events": [
        {
            "id": "3733eb36935e4d73800a9cf36185d5a2",
            "type": "personLineEvent",
            "detectionIds": [
                "90d55bfc64c54bfd98226697ad8445ca"
            ],
            "properties": {
                "trackingId": "90d55bfc64c54bfd98226697ad8445ca",
                "status": "CrossLeft"
            },
            "zone": "doorcamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:06:53.261Z",
        "width": 608,
        "height": 342,
        "frameId": "1340",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "90d55bfc64c54bfd98226697ad8445ca",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.491627341822574,
                        "y": 0.2385801348769874
                    },
                    {
                        "x": 0.588894994635331,
                        "y": 0.6395559924387793
                    }
                ]
            },
            "confidence": 0.9005028605461121,
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```
| 事件字段名称 | 类型| 说明|
|---------|---------|---------|
| `id` | 字符串| 事件 ID|
| `type` | 字符串| 事件类型|
| `detectionsId` | array| 触发此事件的人员检测的唯一标识符的大小为1的数组|
| `properties` | collection| 值的集合|
| `trackinId` | 字符串| 检测到的人员的唯一标识符|
| `status` | 字符串| 交叉行方向，"CrossLeft" 或 "CrossRight"|
| `zone` | 字符串 | 所交叉行的 "名称" 字段|

| 检测字段名称 | 类型| 说明|
|---------|---------|---------|
| `id` | 字符串| 检测 ID|
| `type` | 字符串| 检测类型|
| `region` | collection| 值的集合|
| `type` | 字符串| 区域类型|
| `points` | collection| 当区域类型为矩形时，左上方和右下点 |
| `confidence` | FLOAT| 算法置信度|

| SourceInfo 字段名称 | 类型| 说明|
|---------|---------|---------|
| `id` | 字符串| 相机 ID|
| `timestamp` | date| 发出 JSON 有效负载时的 UTC 日期|
| `width` | int | 视频帧宽度|
| `height` | int | 视频帧高度|
| `frameId` | int | 帧标识符|


> [!IMPORTANT]
> AI 模型可检测人员是否与相机的正面或远离相机。 AI 模型不会运行人脸检测或识别，也不会发出任何生物识别信息。 

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon-ai-insights"></a>Cognitiveservices account 的 JSON 格式。 spatialanalysis-personcrossingpolygon AI Insights

此操作输出的检测的示例 JSON。

```json
{
    "events": [
        {
            "id": "f095d6fe8cfb4ffaa8c934882fb257a5",
            "type": "personZoneEvent",
            "detectionIds": [
                "afcc2e2a32a6480288e24381f9c5d00e"
            ],
            "properties": {
                "trackingId": "afcc2e2a32a6480288e24381f9c5d00e",
                "status": "Enter",
                "side": ""
            },
            "zone": "queuecamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:15:09.680Z",
        "width": 608,
        "height": 342,
        "frameId": "428",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "afcc2e2a32a6480288e24381f9c5d00e",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.8135572734631991,
                        "y": 0.6653949670624315
                    },
                    {
                        "x": 0.9937645761590255,
                        "y": 0.9925406829655519
                    }
                ]
            },
            "confidence": 0.6267998814582825,
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| 事件字段名称 | 类型| 说明|
|---------|---------|---------|
| `id` | 字符串| 事件 ID|
| `type` | 字符串| 事件类型|
| `detectionsId` | array| 触发此事件的人员检测的唯一标识符的大小为1的数组|
| `properties` | collection| 值的集合|
| `trackinId` | 字符串| 检测到的人员的唯一标识符|
| `status` | 字符串| 多边形交叉的方向，"Enter" 或 "Exit"|
| `zone` | 字符串 | 表示已越过区域的多边形的 "名称" 字段|

| 检测字段名称 | 类型| 说明|
|---------|---------|---------|
| `id` | 字符串| 检测 ID|
| `type` | 字符串| 检测类型|
| `region` | collection| 值的集合|
| `type` | 字符串| 区域类型|
| `points` | collection| 当区域类型为矩形时，左上方和右下点 |
| `confidence` | FLOAT| 算法置信度|

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-persondistance-ai-insights"></a>Cognitiveservices account 的 JSON 格式。 spatialanalysis-persondistance AI Insights

此操作输出的检测的示例 JSON。

```json
{
    "events": [
        {
            "id": "9c15619926ef417aa93c1faf00717d36",
            "type": "personDistanceEvent",
            "detectionIds": [
                "9037c65fa3b74070869ee5110fcd23ca",
                "7ad7f43fd1a64971ae1a30dbeeffc38a"
            ],
            "properties": {
                "personCount": 5,
                "averageDistance": 20.807043981552123,
                "minimumDistanceThreshold": 6.0,
                "maximumDistanceThreshold": "Infinity",
                "eventName": "TooClose",
                "distanceViolationPersonCount": 2
            },
            "zone": "lobbycamera",
            "trigger": "event"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:17:25.309Z",
        "width": 608,
        "height": 342,
        "frameId": "1199",
        "cameraCalibrationInfo": {
            "status": "Complete",
            "cameraHeight": 12.9940824508667,
            "focalLength": 401.2800598144531,
            "tiltupAngle": 1.057669997215271
        },
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "9037c65fa3b74070869ee5110fcd23ca",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.39988183975219727,
                        "y": 0.2719132942065858
                    },
                    {
                        "x": 0.5051516984638414,
                        "y": 0.6488402517218339
                    }
                ]
            },
            "confidence": 0.948630690574646,
            "centerGroundPoint": {
                "x": -1.4638760089874268,
                "y": 18.29732322692871
            },
            "metadataType": ""
        },
        {
            "type": "person",
            "id": "7ad7f43fd1a64971ae1a30dbeeffc38a",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.5200299714740954,
                        "y": 0.2875368218672903
                    },
                    {
                        "x": 0.6457497446160567,
                        "y": 0.6183311060855263
                    }
                ]
            },
            "confidence": 0.8235412240028381,
            "centerGroundPoint": {
                "x": 2.6310102939605713,
                "y": 18.635927200317383
            },
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| 事件字段名称 | 类型| 说明|
|---------|---------|---------|
| `id` | 字符串| 事件 ID|
| `type` | 字符串| 事件类型|
| `detectionsId` | array| 触发此事件的人员检测的唯一标识符的大小为1的数组|
| `properties` | collection| 值的集合|
| `personCount` | int| 发出事件时检测到的人员数|
| `averageDistance` | FLOAT| 所有检测到的人在英尺内的平均距离|
| `minimumDistanceThreshold` | FLOAT| 当用户小于该距离时，将触发 "TooClose" 事件的距离（以英尺为间隔）。|
| `maximumDistanceThreshold` | FLOAT| 当人们大于距离相隔时，将触发 "TooFar" 事件的距离（以英尺为间隔）。|
| `eventName` | 字符串| 事件名称为 `TooClose` `minimumDistanceThreshold` ，违反，违反 `TooFar` `maximumDistanceThreshold` ，或 `unknown` 在未完成自动校准时|
| `distanceViolationPersonCount` | int| 在违反或的情况中检测到的用户数 `minimumDistanceThreshold``maximumDistanceThreshold`|
| `zone` | 字符串 | 多边形的 "名称" 字段，表示为用户之间的 distancing 监视的区域|
| `trigger` | 字符串| 触发器类型为 "事件" 或 "间隔"，具体取决于中的值 `trigger` SPACEANALYTICS_CONFIG|

| 检测字段名称 | 类型| 说明|
|---------|---------|---------|
| `id` | 字符串| 检测 ID|
| `type` | 字符串| 检测类型|
| `region` | collection| 值的集合|
| `type` | 字符串| 区域类型|
| `points` | collection| 当区域类型为矩形时，左上方和右下点 |
| `confidence` | FLOAT| 算法置信度|
| `centerGroundPoint` | 2浮点值| `x`， `y` 其中的值具有人员在地面上的推断位置的坐标。 `x` 与摄像机的距离是否垂直于在英尺内投影到的相机图像平面。 `y` 与相机的距离是否平行于在英尺内投影到地面上的图像平面。|

| SourceInfo 字段名称 | 类型| 说明|
|---------|---------|---------|
| `id` | 字符串| 相机 ID|
| `timestamp` | date| 发出 JSON 有效负载时的 UTC 日期|
| `width` | int | 视频帧宽度|
| `height` | int | 视频帧高度|
| `frameId` | int | 帧标识符|
| `cameraCallibrationInfo` | collection | 值的集合|
| `status` | 字符串 | 指示相机校准到地面的情况是否为 "完成"|
| `cameraHeight` | FLOAT | 相机上地面的高度。 这是从自动校准推断出来的。 |
| `focalLength` | FLOAT | 以像素为单位的摄像机焦点长度。 这是从自动校准推断出来的。 |
| `tiltUpAngle` | FLOAT | 相机倾斜角度与垂直。 这是从自动校准推断出来的。|


## <a name="use-the-output-generated-by-the-container"></a>使用容器生成的输出

你可能想要将空间分析检测或事件集成到你的应用程序中。 下面是几种需要考虑的方法： 

* 使用所选编程语言的 Azure 事件中心 SDK 连接到 Azure IoT 中心终结点，并接收事件。 有关详细信息，请参阅 [从内置终结点读取设备到云的消息](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-read-builtin) 。 
* 在 Azure IoT 中心设置 **消息路由** ，将事件发送到其他终结点，或者将事件保存到数据存储中。 有关详细信息，请参阅 [IoT 中心消息路由](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c) 。 
* 设置 Azure 流分析作业，以实时处理事件，并创建可视化效果。 

## <a name="deploying-spatial-analysis-operations-at-scale-multiple-cameras"></a>大规模部署空间分析操作 (多个相机) 

为了获得 Gpu 的最佳性能和利用率，你可以使用图形实例在多个相机上部署任何空间分析操作。 下面是 `cognitiveservices.vision.spatialanalysis-personcount` 在五个照相机上运行操作的示例。

```json
 "properties.desired": {
      "globalSettings": {
          "PlatformTelemetryEnabled": false,
          "CustomerTelemetryEnabled": true
      },
      "graphs": {
          "personcount": {
              "operationId": "cognitiveservices.vision.spatialanalysis-personcount",
              "version": 1,
              "enabled": true,
              "sharedNodes": {
                  "shared_detector1": {
                      "node": "PersonCountGraph.detector",
                      "parameters": {
                          "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0, \"batch_size\": 5}",
                      }
                  }
              },
              "parameters": {
                  "VIDEO_DECODE_GPU_INDEX": 0,
                  "VIDEO_IS_LIVE": true
              },
              "instances": {
                  "1": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 1>",
                          "VIDEO_SOURCE_ID": "camera 1",
                          "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"zone5\",\"polygon\":[[0,0],[1,0],[0,1],[1,1],[0,0]],\"threshold\":50.0, \"events\":[{\"type\":\"count\", \"output_frequency\": 1}]}]}"
                      }
                  },
                  "2": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 2>",
                          "VIDEO_SOURCE_ID": "camera 2",
                          "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                      }
                  },
                  "3": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 3>",
                          "VIDEO_SOURCE_ID": "camera 3",
                          "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                      }
                  },
                  "4": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 4>",
                          "VIDEO_SOURCE_ID": "camera 4",
                          "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                      }
                  },
                  "5": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 5>",
                          "VIDEO_SOURCE_ID": "camera 5",
                          "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                      }
                  }
              }
          }
      }
  }
  ```
| 名称 | 类型| 说明|
|---------|---------|---------|
| `batch_size` | int | 指示要在操作中使用的照相机的数目。 |

## <a name="next-steps"></a>后续步骤

* [部署计算 web 应用程序的人员](spatial-analysis-web-app.md)
* [日志记录和故障排除](spatial-analysis-logging.md)
* [照相机位置指南](spatial-analysis-camera-placement.md)
* [区域和线条位置指南](spatial-analysis-zone-line-placement.md)
