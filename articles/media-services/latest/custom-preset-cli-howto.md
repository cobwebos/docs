---
title: 编码自定义转换使用媒体服务 v3 CLI-Azure |Microsoft Docs
description: 本主题演示如何使用 Azure 媒体服务 v3 进行编码使用 CLI 进行自定义转换。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 05/14/2019
ms.author: juliako
ms.openlocfilehash: 42b7c2d86525c428253137b424fe58bb61edba70
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65762025"
---
# <a name="how-to-encode-with-a-custom-transform---cli"></a>如何使用自定义转换-CLI 进行编码

使用 Azure 媒体服务编码时，你可以快速入门中所示基于行业最佳实践，建议使用内置预设之一[文件进行流式处理](stream-files-cli-quickstart.md#create-a-transform-for-adaptive-bitrate-encoding)快速入门。 此外可以构建自定义预设，以满足特定的方案或设备要求。

## <a name="considerations"></a>注意事项

时创建自定义预设，应该注意以下事项：

* 高度和宽度 AVC 内容上的所有值必须都是 4 的倍数。
* 在 Azure 媒体服务 v3 所有编码比特率是比特 / 秒。 这是不同于与我们使用千比特/秒为单位的 v2 Api 的预设。 例如，如果在 v2 中的比特率 （千比特/秒） 已指定为 128，v3 中它将设置为 128000 （比特/秒）。

## <a name="prerequisites"></a>必备组件 

[创建媒体服务帐户](create-account-cli-how-to.md)。 <br/>请务必记住资源组名称和媒体服务帐户名称。 

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-custom-preset"></a>定义自定义预设

下面的示例定义新的转换的请求正文。 我们定义一组我们想要使用此转换时生成的输出。 

在此示例中，我们首先添加一个 AacAudio 层的音频编码和视频编码的两个 H264Video 层。 在视频的层，我们将分配标签，以便可以在输出文件名称中使用它们。 接下来，我们想要输出还包括缩略图。 在下面的示例中，我们将指定 PNG 格式，生成在 50%的输入视频的分辨率和三个时间戳的 {25%、 50%、 75} 的输入视频的长度的图像。 最后，我们指定的输出文件-一个用于视频 + 音频格式，另一个用于缩略图。 由于我们有多个 H264Layers，我们必须使用生成每个层的唯一名称的宏。 我们可以使用`{Label}`或`{Bitrate}`宏，该示例显示了前者。

我们将在文件中保存此转换。 在此示例中，我们将文件命名`customPreset.json`。 

```json
{
    "@odata.type": "#Microsoft.Media.StandardEncoderPreset",
    "codecs": [
        {
            "@odata.type": "#Microsoft.Media.AacAudio",
            "channels": 2,
            "samplingRate": 48000,
            "bitrate": 128000,
            "profile": "AacLc"
        },
        {
            "@odata.type": "#Microsoft.Media.H264Video",
            "keyFrameInterval": "PT2S",
            "stretchMode": "AutoSize",
            "sceneChangeDetection": false,
            "complexity": "Balanced",
            "layers": [
                {
                    "width": "1280",
                    "height": "720",
                    "label": "HD",
                    "bitrate": 3400000,
                    "maxBitrate": 3400000,
                    "bFrames": 3,
                    "slices": 0,
                    "adaptiveBFrame": true,
                    "profile": "Auto",
                    "level": "auto",
                    "bufferWindow": "PT5S",
                    "referenceFrames": 3,
                    "entropyMode": "Cabac"
                },
                {
                    "width": "640",
                    "height": "360",
                    "label": "SD",
                    "bitrate": 1000000,
                    "maxBitrate": 1000000,
                    "bFrames": 3,
                    "slices": 0,
                    "adaptiveBFrame": true,
                    "profile": "Auto",
                    "level": "auto",
                    "bufferWindow": "PT5S",
                    "referenceFrames": 3,
                    "entropyMode": "Cabac"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Media.PngImage",
            "stretchMode": "AutoSize",
            "start": "25%",
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

```

## <a name="create-a-new-transform"></a>创建新的转换  

在此示例中，我们将创建**转换**基于我们之前定义的自定义预设。 在创建转换时，应首先检查是否其中一个已存在。 如果转换存在，则重复使用它。 以下`show`命令返回`customTransformName`转换如果它存在，则：

```cli
az ams transform show -a amsaccount -g amsResourceGroup -n customTransformName
```

以下 CLI 命令创建基于自定义预设 （前面定义） 的转换。 

```cli
az ams transform create -a amsaccount -g amsResourceGroup -n customTransformName --description "Basic Transform using a custom encoding preset" --preset customPreset.json
```

对于将转换应用到指定的视频或音频的媒体服务，需要提交作业下该转换。 有关演示如何提交下一个转换，转换作业的完整示例，请参阅[快速入门：Stream 的视频文件的 CLI](stream-files-cli-quickstart.md)。

## <a name="see-also"></a>另请参阅

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
