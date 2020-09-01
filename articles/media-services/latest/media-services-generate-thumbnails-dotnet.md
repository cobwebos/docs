---
title: 如何在 .NET 中使用 Azure 媒体服务编码器标准生成缩略图
description: 本文介绍如何使用 .NET 通过 Media Encoder Standard 来对资产进行编码和生成缩略图。
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
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267643"
---
# <a name="how-to-generate-thumbnails-using-encoder-standard-with-net"></a>如何使用编码器 Standard with .NET 生成缩略图

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

可以使用 Media Encoder Standard 以 [JPEG](https://en.wikipedia.org/wiki/JPEG)、[PNG](https://en.wikipedia.org/wiki/Portable_Network_Graphics) 或 [BMP](https://en.wikipedia.org/wiki/BMP_file_format) 图像文件格式从输入视频生成一个或多个缩略图。

## <a name="recommended-reading-and-practice"></a>建议读物和实践

建议你通过阅读 [如何使用自定义转换-.net 编码来](customize-encoder-presets-how-to.md)熟悉自定义转换。

## <a name="transform-code-example"></a>转换代码示例

下面的代码示例仅创建一个缩略图。  应设置以下参数：

- **开始** -输入视频中开始生成缩略图的位置。 此值可以是 ISO 8601 格式 (例如，PT05S 从5秒开始) ，或帧计数 (例如，在第10帧) 为10，从第10帧开始; 对于流持续时间，则为相对值 ()  还支持宏 {最佳}，它会告知编码器从视频的前几秒选择最佳缩略图，只会生成一个缩略图，而不管步骤和范围的其他设置如何。 默认值为 "宏 {最佳}"。
- **步骤** -生成缩略图的间隔。 此值可以是 ISO 8601 格式 (例如，每隔5秒) 一幅图像的 PT05S，或帧计数 (例如，每隔30帧) 一个图像为30个，或是流式处理持续时间的相对值 (例如，10% 表示一个图像每10% 的流持续时间) 。 步骤值将影响第一个生成的缩略图，该缩略图可能不是转换预设开始时间指定的缩略图。 这是由编码器导致的，它尝试选择开始时间和开始时间与开始时间之间的最佳缩略图作为第一个输出。 因为默认值为10%，这意味着，如果流的持续时间较长，则第一个生成的缩略图可能会远离开始时间指定的缩略图。 如果第一个缩略图应接近开始时间，请尝试为步骤选择合理的值，或者如果在开始时间只需要一个缩略图，则将范围值设置为1。
- **范围** -要停止生成缩略图的输入视频中 "转换预设开始时间" 相对于的位置。 该值可以是 ISO 8601 格式 (例如，从开始时间) ，PT5M30S 从开始时间停止到5分钟30秒，或帧计数 (例如，300在1/300 帧开始时停止。 如果此值为1，则表示只在开始时间) 生成一个缩略图，或在流持续时间 (例如，50% 从开始时间开始，从开始时间) 停止。 默认值为100%，表示在流的末尾停止。
- **层** -编码器生成的输出图像层的集合。

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
