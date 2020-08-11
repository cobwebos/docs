---
title: 如何使用 Azure 媒体服务编码器标准版和 REST 生成缩略图
description: 本文介绍如何使用 REST 通过 Media Encoder Standard 来对资产进行编码和生成缩略图。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/10/2020
ms.author: inhenkel
ms.openlocfilehash: 635c1bb500f563da3c0eef8698cad8ab9fa5c810
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2020
ms.locfileid: "88068099"
---
# <a name="how-to-generate-thumbnails-using-encoder-standard-with-rest"></a>如何使用编码器标准版和 REST 生成缩略图

可以使用 Media Encoder Standard 以 [JPEG](https://en.wikipedia.org/wiki/JPEG)、[PNG](https://en.wikipedia.org/wiki/Portable_Network_Graphics) 或 [BMP](https://en.wikipedia.org/wiki/BMP_file_format) 图像文件格式从输入视频生成一个或多个缩略图。

## <a name="recommended-reading-and-practice"></a>建议读物和实践

建议你通过阅读[如何使用自定义转换-REST 进行编码来](custom-preset-rest-howto.md)熟悉自定义转换。

## <a name="thumbnail-parameters"></a>缩略图参数

应设置以下参数：

- **开始**-输入视频中开始生成缩略图的位置。 此值可以是 ISO 8601 格式 (例如，PT05S 从5秒开始) ，或帧计数 (例如，在第10帧) 为10，从第10帧开始; 对于流持续时间，则为相对值 ()  还支持宏 {最佳}，它会告知编码器从视频的前几秒选择最佳缩略图，只会生成一个缩略图，而不管步骤和范围的其他设置如何。 默认值为 "宏 {最佳}"。
- **步骤**-生成缩略图的间隔。 此值可以是 ISO 8601 格式 (例如，每隔5秒) 一幅图像的 PT05S，或帧计数 (例如，每隔30帧) 一个图像为30个，或是流式处理持续时间的相对值 (例如，10% 表示一个图像每10% 的流持续时间) 。 步骤值将影响第一个生成的缩略图，该缩略图可能不是转换预设开始时间指定的缩略图。 这是由编码器导致的，它尝试选择开始时间和开始时间与开始时间之间的最佳缩略图作为第一个输出。 因为默认值为10%，这意味着，如果流的持续时间较长，则第一个生成的缩略图可能会远离开始时间指定的缩略图。 如果第一个缩略图应接近开始时间，请尝试为步骤选择合理的值，或者如果在开始时间只需要一个缩略图，则将范围值设置为1。
- **范围**-要停止生成缩略图的输入视频中 "转换预设开始时间" 相对于的位置。 该值可以是 ISO 8601 格式 (例如，从开始时间) ，PT5M30S 从开始时间停止到5分钟30秒，或帧计数 (例如，300在1/300 帧开始时停止。 如果此值为1，则表示只在开始时间) 生成一个缩略图，或在流持续时间 (例如，50% 从开始时间开始，从开始时间) 停止。 默认值为100%，表示在流的末尾停止。
- **层**-编码器生成的输出图像层的集合。

## <a name="example-of-a-single-png-file-preset"></a>“单个 PNG 文件”预设示例

以下 JSON 预设可用于从输入视频的前几秒生成单个输出 PNG 文件，其中的编码器会尽力尝试查找 "有趣的" 帧。 请注意，输出图像尺寸已设置为 100%，这意味着，这些尺寸与输入视频的尺寸匹配。 另请注意需要如何指定“Outputs”中“Format”设置，以匹配“Codecs”节中“PngLayers”的用法。  

```json
{
    "properties": {
        "description": "Basic Transform using a custom encoding preset for thumbnails",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
                    "@odata.type": "#Microsoft.Media.StandardEncoderPreset",
                    "codecs": [
                        {
                            "@odata.type": "#Microsoft.Media.PngImage",
                            "stretchMode": "AutoSize",
                            "start": "{Best}",
                            "step": "25%",
                            "range": "80%",
                            "layers": [
                                {
                                    "width": "50%",
                                    "height": "50%"
                                }
                            ]
                        }
                    ],
                    "formats": [
                        {
                            "@odata.type": "#Microsoft.Media.Mp4Format",
                            "filenamePattern": "Video-{Basename}-{Label}-{Bitrate}{Extension}",
                            "outputFiles": []
                        },
                        {
                            "@odata.type": "#Microsoft.Media.PngFormat",
                            "filenamePattern": "Thumbnail-{Basename}-{Index}{Extension}"
                        }
                    ]
                }
            }
        ]
    }
}

```

## <a name="example-of-a-series-of-jpeg-images-preset"></a>“一系列 JPEG 图像”预设的示例

以下 JSON 预设可用于生成一组10个图像，其时间戳为5%，15%，...，95% 的输入时间线，其中图像大小指定为输入视频的四分之一。

### <a name="json-preset"></a>JSON 预设

```json
{
  "Version": 1.0,
  "Codecs": [
    {
      "JpgLayers": [
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "25%",
          "Height": "25%"
        }
      ],
      "Start": "5%",
      "Step": "10%",
      "Range": "96%",
      "Type": "JpgImage"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_{Index}{Extension}",
      "Format": {
        "Type": "JpgFormat"
      }
    }
  ]
}
```

## <a name="example-of-a-one-image-at-a-specific-timestamp-preset"></a>“在特定时间戳处生成一个图像”预设示例

以下 JSON 预设可用于在输入视频的30秒标记处生成单个 JPEG 图像。 此预设预期输入视频的持续时间超过 30 秒（否则作业失败）。

### <a name="json-preset"></a>JSON 预设

```json
{
  "Version": 1.0,
  "Codecs": [
    {
      "JpgLayers": [
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "25%",
          "Height": "25%"
        }
      ],
      "Start": "00:00:30",
      "Step": "1",
      "Range": "1",
      "Type": "JpgImage"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_{Index}{Extension}",
      "Format": {
        "Type": "JpgFormat"
      }
    }
  ]
}
```

## <a name="example-of-a-thumbnails-at-different-resolutions-preset"></a>“不同分辨率的缩略图”预设示例

可使用以下预设在同一个任务中的不同解决方案下生成缩略图。 在该示例中，当输入时间线位于 5%、15%、…、95% 时，编码器会在输入视频分辨率的 100% 处和 50% 处各生成一个图像。

请注意 FileName 中 {Resolution} 宏的使用；它指示在生成输出图像的文件名时，编码器要使用在预设的“编码”部分中所指定的宽度和高度。 这也有助于您轻松区分不同的图像。

### <a name="json-preset"></a>JSON 预设

```json
{
  "Version": 1.0,
  "Codecs": [
    {
      "JpgLayers": [
{
  "Quality": 90,
  "Type": "JpgLayer",
  "Width": "100%",
  "Height": "100%"
},
{
  "Quality": 90,
  "Type": "JpgLayer",
  "Width": "50%",
  "Height": "50%"
}

      ],
      "Start": "5%",
      "Step": "10%",
      "Range": "96%",
      "Type": "JpgImage"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_{Resolution}_{Index}{Extension}",
      "Format": {
"Type": "JpgFormat"
      }
    }
  ]
}
```

## <a name="example-of-generating-a-thumbnail-while-encoding"></a>在编码时生成缩略图的示例

上述所有示例都在讨论如何提交仅生成图像的编码任务，但还可以将视频/音频编码与缩略图生成结合起来。 以下 JSON 预设通知编码器标准在编码过程中生成缩略图。

### <a name="json-preset"></a>JSON 预设

有关架构的信息，请参阅[此](/azure/media-services/previous/media-services-mes-schema)文。

```json
{
  "Version": 1.0,
  "Codecs": [
    {
      "KeyFrameInterval": "00:00:02",
      "SceneChangeDetection": "true",
      "H264Layers": [
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 4500,
          "MaxBitrate": 4500,
          "BufferWindow": "00:00:05",
          "Width": 1280,
          "Height": 720,
          "ReferenceFrames": 3,
          "EntropyMode": "Cabac",
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"

        }
      ],
      "Type": "H264Video"
    },
    {
      "JpgLayers": [
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "100%",
          "Height": "100%"
        }
      ],
      "Start": "{Best}",
      "Type": "JpgImage"
    },
    {
      "Channels": 2,
      "SamplingRate": 48000,
      "Bitrate": 128,
      "Type": "AACAudio"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_{Index}{Extension}",
      "Format": {
        "Type": "JpgFormat"
      }
    },
    {
      "FileName": "{Basename}_{Resolution}_{VideoBitrate}.mp4",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

## <a name="next-steps"></a>后续步骤
[使用 .NET 生成缩略图](media-services-generate-thumbnails-dotnet.md)
