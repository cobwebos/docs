---
title: 如何通过 .NET 使用 Azure Media Services Encoder Standard 来生成缩略图
description: 本文介绍如何通过 .NET 使用 Media Encoder Standard 同时对资产进行编码和生成缩略图。
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
ms.custom: devx-track-csharp
ms.openlocfilehash: 5e4ad7ba75edd1899cbe2d7cd7d3b1b6c124ce35
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89267643"
---
# <a name="how-to-generate-thumbnails-using-encoder-standard-with-net"></a>如何使用编码器标准版通过 .NET 来生成缩略图

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

可以使用 Media Encoder Standard 以 [JPEG](https://en.wikipedia.org/wiki/JPEG)、[PNG](https://en.wikipedia.org/wiki/Portable_Network_Graphics) 或 [BMP](https://en.wikipedia.org/wiki/BMP_file_format) 图像文件格式从输入视频生成一个或多个缩略图。

## <a name="recommended-reading-and-practice"></a>推荐阅读内容及演练

建议阅读[如何通过自定义转换进行编码 - .NET](customize-encoder-presets-how-to.md)，以熟悉自定义转换。

## <a name="transform-code-example"></a>转换代码示例

下面的代码示例仅创建一个缩略图。  你应设置以下参数：

- **开始** - 输入视频中开始生成缩略图的位置。 该值可以是 ISO 8601 格式（例如，PT05S 表示在 5 秒处开始），可以是帧计数（例如，10 表示在第 10 帧处开始），也可以是流持续时间的相对值（例如，10% 表示在流持续时间的 10% 处开始）。 还支持宏 {Best}，它会指示编码器从视频的前几秒中选择最佳的缩略图，并且无论“步距”和“范围”的其他设置如何，都只会生成一个缩略图。 默认值为宏 {Best}。
- **步距** - 生成缩略图的间隔。 该值可以是 ISO 8601 格式（例如，PT05S 表示每 5 秒一张图片），可以是帧计数（例如，30 表示每 30 帧一张图片），也可以是流持续时间的相对值（例如，10% 表示流持续时间的每 10% 一张图片）。 步距值将影响第一个生成的缩略图，该缩略图可能与在转换预设开始时间指定的缩略图不完全相同。 导致这一情况的原因在于编码器，编码器尝试在开始时间和开始时间的步距位置之间选择最佳缩略图作为第一输出。 由于默认值为 10%，因此意味着如果流的持续时间较长，第一个生成的缩略图可能会与开始时指定的缩略图相距甚远。 如果希望第一个缩略图接近开始时间，则尝试为步距选择一个合理的值；如果在开始时间处只需要一个缩略图，则将范围值设置为 1。
- **范围** - 与输入视频中转换预设开始时间相关的位置，在该位置停止生成缩略图。 该值可以是 ISO 8601 格式（例如，PT5M30S 表示从开始时间起 5 分 30 秒停止），也可以是帧计数（例如，300 表示在开始时间帧的第 300 帧停止）。 如果该值为 1，则表示在开始时间处仅生成一个缩略图），或者是相对于流持续时间的相对值（例如，50% 表示从开始时间起的流持续时间的一半处停止）。 默认值是 100%，这表示在流的末尾处停止。
- **层** - 编码器要生成的输出图像层的集合。

```csharp

private static Transform EnsureTransformExists(IAzureMediaServicesClient client, string resourceGroupName, string accountName, string transformName)
{
    // Does a Transform already exist with the desired name? Assume that an existing Transform with the desired name
    // also uses the same recipe or Preset for processing content.
    Transform transform = client.Transforms.Get(resourceGroupName, accountName, transformName);

    if (transform == null)
    {
        // Create a new Transform Outputs array - this defines the set of outputs for the Transform
        TransformOutput[] outputs = new TransformOutput[]
        {
            // Create a new TransformOutput with a custom Standard Encoder Preset
            // This demonstrates how to create custom codec and layer output settings

          new TransformOutput(
                new StandardEncoderPreset(
                    codecs: new Codec[]
                    {
                        // Generate a set of PNG thumbnails
                        new PngImage(
                            start: "25%",
                            step: "25%",
                            range: "80%",
                            layers: new PngLayer[]{
                                new PngLayer(
                                    width: "50%",
                                    height: "50%"
                                )
                            }
                        )
                    },
                    // Specify the format for the output files for the thumbnails
                    formats: new Format[]
                    {
                        new PngFormat(
                            filenamePattern:"Thumbnail-{Basename}-{Index}{Extension}"
                        )
                    }
                ),
                onError: OnErrorType.StopProcessingJob,
                relativePriority: Priority.Normal
            )
        };

        string description = "A transform that includes thumbnails.";
        // Create the custom Transform with the outputs defined above
        transform = client.Transforms.CreateOrUpdate(resourceGroupName, accountName, transformName, outputs, description);
    }

    return transform;
}
```

## <a name="next-steps"></a>后续步骤
[使用 REST 生成缩略图](media-services-generate-thumbnails-rest.md)
