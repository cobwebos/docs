---
title: 使用媒体服务 v3 对自定义转换进行编码 - Azure CLI | Microsoft Docs
description: 本主题介绍如何使用 Azure CLI 通过 Azure 媒体服务 v3 对自定义转换进行编码。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: c61ac8c7cf85b1fae4c9b531f16b951dd2b80876
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89295778"
---
# <a name="how-to-encode-with-a-custom-transform---azure-cli"></a>如何对自定义转换进行编码 - Azure CLI

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

使用 Azure 媒体服务进行编码时，可以根据[流式传输文件](stream-files-cli-quickstart.md#create-a-transform-for-adaptive-bitrate-encoding)快速入门中演示的行业最佳做法，使用推荐的内置预设之一快速入门。 也可以构建自定义预设以针对特定方案或设备要求。

## <a name="considerations"></a>注意事项

创建自定义预设时，请注意以下事项：

* AVC 内容上的所有高度和宽度值必须是 4 的倍数。
* 在 Azure 媒体服务 v3 中，所有编码比特率均以每秒比特数为单位。 这与我们的 v2 API 的预设不同，后者使用 千比特/秒作为单位。 例如，如果 v2 中的比特率指定为 128（千比特/秒），则在 v3 中它将设置为 128000（比特/秒）。

## <a name="prerequisites"></a>先决条件

[创建媒体服务帐户](./create-account-howto.md)。

请务必记住资源组名称和媒体服务帐户名称。

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-custom-preset"></a>定义自定义预设

以下示例定义新转换的请求正文。 我们定义了一组希望在使用此转换时生成的输出。

在此示例中，我们首先为音频编码添加一个 AacAudio 层，为视频编码添加两个 H264Video 层。 在视频层中，我们分配标签，以便可以在输出文件名中使用它们。 接下来，我们希望输出还包括缩略图。 在以下示例中，我们指定 PNG 格式的图像，这些图像以输入视频分辨率的 50% 生成，并以输入视频长度的 {25%, 50%, 75} 三个时间戳生成。 最后，我们指定输出文件的格式 - 一个用于视频 + 音频，另一个用于缩略图。 由于我们有多个 H264 层，因此我们必须使用宏来为每个层生成唯一的名称。 可以使用 `{Label}` 或 `{Bitrate}` 宏，此示例显示了前者。

我们要将此转换保存到某个文件中。 在此示例中，文件命名为 `customPreset.json`。

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

## <a name="create-a-new-transform"></a>创建新转换  

在此示例中，我们基于前面定义的自定义预设创建**转换**。 创建转换时，应首先检查是否已存在转换。 如果存在转换，请重新使用它。 以下 `show` 命令返回 `customTransformName` 转换（如果存在）：

```azurecli-interactive
az ams transform show -a amsaccount -g amsResourceGroup -n customTransformName
```

以下 Azure CLI 命令根据自定义预设（先前已定义）创建转换。

```azurecli-interactive
az ams transform create -a amsaccount -g amsResourceGroup -n customTransformName --description "Basic Transform using a custom encoding preset" --preset customPreset.json
```

要使媒体服务将转换应用于指定的视频或音频，需要在该转换下提交作业。 有关演示如何在转换下提交作业的完整示例，请参阅[快速入门：对视频文件进行流式处理 - Azure CLI](stream-files-cli-quickstart.md)。

## <a name="see-also"></a>另请参阅

[Azure CLI](/cli/azure/ams)
