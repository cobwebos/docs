---
title: Azure 媒体服务 v3 输入元数据架构
description: 本文概述了 Azure 媒体服务 v3 输入元数据架构。
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/14/2020
ms.author: juliako
ms.openlocfilehash: 40e61061878c8aec6bad353bfd0c5f2f4178ce14
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85095548"
---
# <a name="input-metadata"></a>输入元数据 

编码作业与要在其上执行某些编码任务的输入资产（或资产）相关联。  完成任务后，会生成一个输出资产。 输出资产包含视频、音频、缩略图、清单和其他文件。 

输出资产还包含提供输入资产相关元数据的文件。 元数据 JSON 文件的名称具有一个随机 ID，不能使用它来标识输出资产所属的输入资产。 若要确定其所属的输入资产，请使用 `Uri` 字段（有关详细信息，请参阅[其他子元素](#other-child-elements)）。  

媒体服务不会提前扫描输入资产以生成元数据。 输入元数据仅在工作中处理输入资产时作为项目生成。 因此，会将此项目写入输出资产。 不同的工具用于为输入资产和输出资产生成元数据。 因此，输入元数据的模式与输出元数据略有不同。

本文介绍了输入元数据（ &lt; asset_id_metadata.js）所基于的 JSON 架构的元素和类型 &gt; 。 有关包含有关输出资产的元数据的文件的信息，请参阅[输出元数据](output-metadata-schema.md)。  

可以在本文末尾找到 JSON 架构示例。  

## <a name="assetfile"></a>AssetFile  

包含用于编码作业的 AssetFile 元素集合。  

> [!NOTE]
> 以下四个子元素必须出现在一个序列中。  
> 
> 

| “属性”  | 描述 |
| --- | --- | 
| **VideoTracks**|每个物理资产文件可包含交错成适当容器格式的零个或多个视频轨道。 有关详细信息，请参阅[VideoTracks](#videotracks)。 |
| **AudioTracks**|每个物理资产文件可包含交错成适当容器格式的零个或多个音频轨。 有关详细信息，请参阅[AudioTracks](#audiotracks) |
| **元数据**  |资产文件的元数据表示为键\值字符串。 <br />例如： `<Metadata key="language" value="eng" />` |

### <a name="other-child-elements"></a>其他子元素

| “属性” | 描述 |
| --- | --- |
| **名称**<br />必需 |资产文件名。 <br /><br />示例：`"Name": "Ignite-short.mp4"` |
| **Uri**<br />必需 |输入资产所在的 URL。 若要确定输出资产所属的输入资产，请使用 `Uri` 字段而不是 ID。|
| **大小**<br />必需 |资产文件的大小（以字节为单位）。  <br /><br />示例：`"Size": 75739259`|
| **Duration**<br />必需 |内容播放持续时间。 <br /><br />示例：`"Duration": "PT1M10.304S"`。 |
| **NumberOfStreams**<br />必需 |资产文件中的流数。  <br /><br />示例：`"NumberOfStreams": 2`|
| **FormatNames**<br />必需 |格式名。  <br /><br />示例：`"FormatNames": "mov,mp4,m4a,3gp,3g2,mj2"`|
| **FormatVerboseName**<br /> 必需 |格式详细名称。 <br /><br />示例：`"FormatVerboseName": "QuickTime / MOV"` |
| **StartTime** |内容开始时间。  <br /><br />示例：`"StartTime": "PT0S"` |
| **OverallBitRate** |资产文件的平均比特率（以每秒位数为单位）。  <br /><br />示例：`"OverallBitRate": 8618539`|

## <a name="videotracks"></a>VideoTracks

| “属性” |  | 描述 |
| --- | --- |
| **FourCC**<br />必需 |Ffmpeg 报告的视频编解码器 FourCC 代码。<br /><br />示例：`"FourCC": "avc1"` |
| **配置文件** |视频轨的配置文件。 <br /><br />示例：`"Profile": "Main"`|
| **级别** |视频轨的级别。 <br /><br />示例：`"Level": "3.2"`|
| **PixelFormat** |视频轨的像素格式。 <br /><br />示例：`"PixelFormat": "yuv420p"`|
| **Width**<br />必需 |编码视频宽度（以像素为单位）。 <br /><br />示例：`"Width": "1280"`|
| **Height**<br />必需 |编码视频高度（以像素为单位）。<br /><br />示例：`"Height": "720"` |
| **DisplayAspectRatioNumerator**<br />必需 |视频显示纵横比分子。<br /><br />示例：`"DisplayAspectRatioNumerator": 16.0` |
| **DisplayAspectRatioDenominator**<br />必需 |视频显示纵横比分母。 <br /><br />示例：`"DisplayAspectRatioDenominator": 9.0`|
| **SampleAspectRatioNumerator** |视频采样纵横比分子。 <br /><br />示例：`"SampleAspectRatioNumerator": 1.0`|
| **SampleAspectRatioDenominator**|示例：`"SampleAspectRatioDenominator": 1.0`|
| **速率**<br />必需 |采用 .3f 格式测量的视频帧速率。 <br /><br />示例：`"FrameRate": 29.970`|
| **率** |从资产文件计算的平均视频比特率（以每秒比特数为单位）。 只对基本流有效负载进行计算，且不包括打包开销。 <br /><br />示例：`"Bitrate": 8421583`|
| **HasBFrames** |B 帧的视频轨数量。 <br /><br />示例：`"HasBFrames": 2`|
| **元数据** |可用于保存各种信息的通用键/值字符串。 <br />请参阅文章末尾的完整示例。 |
| Id<br />必需 |此音频轨或视频轨从零开始的索引。<br /><br /> 此“Id”不一定是 MP4 文件中使用的 TrackID****。 <br /><br />示例：`"Id": 2`|
| **编解码器** |视频轨编解码器字符串。 <br /><br />示例：`"Codec": "h264"`|
| **CodecLongName** |音频或视频轨编解码器长名称。 <br /><br />示例：`"CodecLongName": "H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10"`|
| **编解码器** |视频轨编解码器字符串。 <br /><br />示例：`"Codec": "h264"`|
| **TimeBase**<br />必需 |时间基数。<br /><br />示例：`"TimeBase": "1/30000"`|
| **NumberOfFrames** |帧数（针对视频轨）。 <br /><br />示例：`"NumberOfFrames": 2107`|
| **StartTime** |轨道开始时间。<br /><br />示例：`"StartTime": "PT0.033S"` |
| **Duration** |轨道持续时间。 <br /><br />示例：`"Duration": "PT1M10.304S"`|

## <a name="audiotracks"></a>AudioTracks

| “属性”  | 描述 |
| --- | --- | 
| **SampleFormat** |示例格式。 <br /><br />示例：`"SampleFormat": "fltp"`|
| **ChannelLayout** |通道布局。 <br /><br />示例：`"ChannelLayout": "stereo"`|
| 通道<br />必需 |音频通道数（0 个或多个）。 <br /><br />示例：`"Channels": 2`|
| **SamplingRate**<br />必需 |音频采样速率（以采样数/秒或 Hz 为单位）。 <br /><br />示例：`"SamplingRate": 48000`|
| **率** |由资产文件计算的平均音频比特率（以 bps 为单位）。 只对基本流有效负载进行计算，且不包括打包开销。 <br /><br />示例：`"Bitrate": 192080`|
| **元数据** |可用于保存各种信息的通用键/值字符串。  <br />请参阅文章末尾的完整示例。 |
| Id<br />必需 |此音频轨或视频轨从零开始的索引。<br /><br /> 这不一定是 MP4 文件中使用的 TrackID。 <br /><br />示例：`"Id": 1`|
| **编解码器** |视频轨编解码器字符串。 <br /><br />示例：`"Codec": "aac"`|
| **CodecLongName** |音频或视频轨编解码器长名称。 <br /><br />示例：`"CodecLongName": "AAC (Advanced Audio Coding)"`|
| **TimeBase**<br />必需 |时间基数。<br /><br />示例：`"TimeBase": "1/48000"` |
| **NumberOfFrames** |帧数（针对视频轨）。 <br /><br />示例：`"NumberOfFrames": 3294`|
| **StartTime** |轨道开始时间。 有关详细信息，请参阅[ISO8601](https://www.iso.org/iso-8601-date-and-time-format.html)。 <br /><br />示例：`"StartTime": "PT0S"` |
| **Duration** |轨道持续时间。 <br /><br />示例：`"Duration": "PT1M10.272S"` |

## <a name="metadata"></a>Metadata

| 名称 | 说明 |
| --- | --- |
| **键**<br />必需 |键/值对中的键。 |
| **value**<br /> 必需 |键/值对中的值。 |

## <a name="schema-example"></a>架构示例

```json
{
  "AssetFile": [
    {
      "VideoTracks": [
        {
          "FourCC": "avc1",
          "Profile": "Main",
          "Level": "3.2",
          "PixelFormat": "yuv420p",
          "Width": "1280",
          "Height": "720",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "SampleAspectRatioNumerator": 1.0,
          "SampleAspectRatioNumeratorSpecified": true,
          "SampleAspectRatioDenominator": 1.0,
          "SampleAspectRatioDenominatorSpecified": true,
          "FrameRate": 29.970,
          "Bitrate": 8421583,
          "BitrateSpecified": true,
          "HasBFrames": 2,
          "HasBFramesSpecified": true,
          "Disposition": {
            "Default": 1
          },
          "Metadata": [
            {
              "key": "creation_time",
              "value": "2018-02-21T21:42:08.000000Z"
            },
            {
              "key": "language",
              "value": "eng"
            },
            {
              "key": "handler_name",
              "value": "Video Media Handler"
            },
            {
              "key": "encoder",
              "value": "AVC Coding"
            }
          ],
          "Id": 2,
          "Codec": "h264",
          "CodecLongName": "H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10",
          "TimeBase": "1/30000",
          "NumberOfFrames": 2107,
          "NumberOfFramesSpecified": true,
          "StartTime": "PT0.033S",
          "Duration": "PT1M10.304S"
        }
      ],
      "AudioTracks": [
        {
          "SampleFormat": "fltp",
          "ChannelLayout": "stereo",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 192080,
          "BitrateSpecified": true,
          "BitsPerSampleSpecified": true,
          "Disposition": {
            "Default": 1
          },
          "Metadata": [
            {
              "key": "creation_time",
              "value": "2018-02-21T21:42:08.000000Z"
            },
            {
              "key": "language",
              "value": "eng"
            },
            {
              "key": "handler_name",
              "value": "Sound Media Handler"
            }
          ],
          "Id": 1,
          "Codec": "aac",
          "CodecLongName": "AAC (Advanced Audio Coding)",
          "TimeBase": "1/48000",
          "NumberOfFrames": 3294,
          "NumberOfFramesSpecified": true,
          "StartTime": "PT0S",
          "Duration": "PT1M10.272S"
        }
      ],
      "Metadata": [
        {
          "key": "major_brand",
          "value": "mp42"
        },
        {
          "key": "minor_version",
          "value": "19529854"
        },
        {
          "key": "compatible_brands",
          "value": "mp42isom"
        },
        {
          "key": "creation_time",
          "value": "2018-02-21T21:42:08.000000Z"
        }
      ],
      "Name": "Ignite-short.mp4",
      "Uri": "https://amsstorageacct.blob.core.windows.net/asset-00000000-0000-0000-000000000000/ignite.mp4",
      "Size": 75739259,
      "Duration": "PT1M10.304S",
      "NumberOfStreams": 2,
      "FormatNames": "mov,mp4,m4a,3gp,3g2,mj2",
      "FormatVerboseName": "QuickTime / MOV",
      "StartTime": "PT0S",
      "OverallBitRate": 8618539,
      "OverallBitRateSpecified": true
    }
  ]
}
```

## <a name="next-steps"></a>后续步骤

[输出元数据](output-metadata-schema.md)
