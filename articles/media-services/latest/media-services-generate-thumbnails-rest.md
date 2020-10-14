---
title: 如何使用 Azure Media Services Encoder Standard 通过 REST 来生成缩略图
description: 本文介绍如何使用 REST 通过 Media Encoder Standard 同时对资产进行编码和生成缩略图。
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
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: ec2782297f2659341c9fa7e87ce15d3dbceb022c
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019556"
---
# <a name="how-to-generate-thumbnails-using-encoder-standard-with-rest"></a>如何使用 Encoder Standard 通过 REST 来生成缩略图

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

可以使用 Media Encoder Standard 以 [JPEG](https://en.wikipedia.org/wiki/JPEG)、[PNG](https://en.wikipedia.org/wiki/Portable_Network_Graphics) 或 [BMP](https://en.wikipedia.org/wiki/BMP_file_format) 图像文件格式从输入视频生成一个或多个缩略图。

## <a name="recommended-reading-and-practice"></a>推荐阅读内容及演练

建议阅读[如何通过自定义转换进行编码 - REST](custom-preset-rest-howto.md)，以熟悉自定义转换。

## <a name="thumbnail-parameters"></a>缩略图参数

你应设置以下参数：

- **开始** - 输入视频中开始生成缩略图的位置。 该值可以是 ISO 8601 格式（例如，PT05S 表示在 5 秒处开始），可以是帧计数（例如，10 表示在第 10 帧处开始），也可以是流持续时间的相对值（例如，10% 表示在流持续时间的 10% 处开始）。 还支持宏 {Best}，它会指示编码器从视频的前几秒中选择最佳的缩略图，并且无论“步距”和“范围”的其他设置如何，都只会生成一个缩略图。 默认值为宏 {Best}。
- **步距** - 生成缩略图的间隔。 该值可以是 ISO 8601 格式（例如，PT05S 表示每 5 秒一张图片），可以是帧计数（例如，30 表示每 30 帧一张图片），也可以是流持续时间的相对值（例如，10% 表示流持续时间的每 10% 一张图片）。 步距值将影响第一个生成的缩略图，该缩略图可能与在转换预设开始时间指定的缩略图不完全相同。 导致这一情况的原因在于编码器，编码器尝试在开始时间和开始时间的步距位置之间选择最佳缩略图作为第一输出。 由于默认值为 10%，因此意味着如果流的持续时间较长，第一个生成的缩略图可能会与开始时指定的缩略图相距甚远。 如果希望第一个缩略图接近开始时间，则尝试为步距选择一个合理的值；如果在开始时间处只需要一个缩略图，则将范围值设置为 1。
- **范围** - 与输入视频中转换预设开始时间相关的位置，在该位置停止生成缩略图。 该值可以是 ISO 8601 格式（例如，PT5M30S 表示从开始时间起 5 分 30 秒停止），也可以是帧计数（例如，300 表示在开始时间帧的第 300 帧停止）。 如果该值为 1，则表示在开始时间处仅生成一个缩略图），或者是相对于流持续时间的相对值（例如，50% 表示从开始时间起的流持续时间的一半处停止）。 默认值是 100%，这表示在流的末尾处停止。
- **层** - 编码器要生成的输出图像层的集合。

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

可以使用以下 JSON 预设在输入时间线的 5%、15% ... 95% 时间戳处生成一组 10 幅图像，其中的图像大小指定为输入视频的四分之一。

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

可以使用以下 JSON 预设在输入视频的 30 秒标记处生成单个 JPEG 图像。 此预设预期输入视频的持续时间超过 30 秒（否则作业失败）。

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

请注意 FileName 中 {Resolution} 宏的使用；它指示在生成输出图像的文件名时，编码器要使用在预设的“编码”部分中所指定的宽度和高度。 这还能够帮助轻松区分不同图像。

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

上述所有示例都在讨论如何提交仅生成图像的编码任务，但还可以将视频/音频编码与缩略图生成结合起来。 以下的 JSON 预设指示 Encoder Standard 在编码过程中生成一个缩略图。

### <a name="json-preset"></a>JSON 预设

有关架构的信息，请参阅[此](../previous/media-services-mes-schema.md)文。

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