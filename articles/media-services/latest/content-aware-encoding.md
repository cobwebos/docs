---
title: 内容感知编码的预设 - Azure 媒体服务
description: 本文介绍 Microsoft Azure 媒体服务 v3 中的内容感知编码。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/24/2020
ms.author: juliako
ms.custom: ''
ms.openlocfilehash: 3ea6c4226a59ba020a477cc5811033ff3dc3c2e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76772078"
---
# <a name="use-the-content-aware-encoding-preset-to-find-the-optimal-bitrate-value-for-a-given-resolution"></a>使用内容感知编码预设查找给定分辨率的最佳比特率值

若要准备通过[自适应比特率流式处理](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)传送的内容，需以多个比特率（从高到低）编码视频。 这可确保质量正常下降，因为比特率降低，视频的分辨率也降低了。 这种多比特率编码使用所谓的编码阶梯 -分辨率和比特率表，请参阅媒体服务[内置编码预设](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset)。

您应该了解正在处理的内容，并自定义/调整编码梯以适应各个视频的复杂性。 在每种分辨率下有一个比特率，超过该比特率会感知不到任何质量提升 – 编码器将以此最佳比特率运行。 下一级优化是基于内容选择分辨率 – 例如，降到 720p 以下不会使 PowerPoint 演示文稿的视频受益。 接下来，可以进一步在编码器中优化视频中每个拍摄画面的设置。 

Microsoft 的[自适应流式处理](autogen-bitrate-ladder.md)预设部分解决了源视频的质量和分辨率的可变性问题。 我们的客户有不同的内容组合，有些在1080p，其他在720p，一些在SD和低分辨率。 此外，并非所有源内容是来自电影或电视制片厂的高质量夹层。 自适应流式处理预设解决了这些问题，它可以确保比特率梯度永不超过输入夹层文件的分辨率或平均比特率。 但是，此预设不会检查分辨率和比特率以外的源属性。

## <a name="the-content-aware-encoding"></a>内容感知编码 

内容感知编码预设扩展了"自适应比特率流"机制，通过合并自定义逻辑，使编码器能够为给定分辨率寻求最佳比特率值，但无需进行广泛的计算分析。 此预设生成一组与 GOP 对齐的 MP4。 给定任何输入内容，该服务对输入内容执行初始轻量级分析，并使用结果确定最佳层数、适当的比特率和分辨率设置，以便通过自适应流式处理进行传递。 此预设对于中低复杂视频特别有效，其中输出文件的比特率将低于自适应流式处理预设，但质量仍可为观看者提供良好的体验。 输出将包含带有视频和音频交错的 MP4 文件

以下示例图显示了使用诸如[PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio)和[VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion)等质量指标的比较。 源的创建方式是将电影和电视节目中的高复杂性拍摄画面的简短剪辑相连接，旨在给编码器提供压力。 按照定义，此预设产生的结果因内容而异 – 这也意味着，对于某些内容，比特率可能不会显著降低，或者质量不会显著提高。

![使用 PSNR 的速率失真 (RD) 曲线](media/content-aware-encoding/msrv1.png)

**图 1：使用 PSNR 指标实现高复杂性源的速率失真 （RD） 曲线**

![使用 VMAF 的速率失真 (RD) 曲线](media/content-aware-encoding/msrv2.png)

**图 2：使用 VMAF 指标实现高复杂性源的速率失真 （RD） 曲线**

下面是另一类源内容的结果，其中编码器能够确定输入质量较差（许多压缩伪影由于比特率低）。 请注意，使用内容感知预设时，编码器决定只生成一个输出层 - 比特率足够低，以便大多数客户端能够在不停滞的情况下播放流。

![使用 PSNR 的 RD 曲线](media/content-aware-encoding/msrv3.png)

**图 3：使用 PSNR 进行低质量输入的 RD 曲线（在 1080p 时）**

![使用 VMAF 的 RD 曲线](media/content-aware-encoding/msrv4.png)

**图 4：使用 VMAF 进行低质量输入的 RD 曲线（在 1080p 时）**

## <a name="how-to-use-the-content-aware-encoding-preset"></a>如何使用内容感知编码预设 

可按如下所示创建使用此预设的转换。 

> [!TIP]
> 有关使用表单输出的教程，请参阅[后续步骤](#next-steps)部分。 输出资产可以从媒体服务流端点（如 MPEG-DASH 和 HLS）（如教程中所示）提供。


```csharp
TransformOutput[] output = new TransformOutput[]
{
   new TransformOutput
   {
      // The preset for the Transform is set to one of Media Services built-in sample presets.
      // You can customize the encoding settings by changing this to use "StandardEncoderPreset" class.
      Preset = new BuiltInStandardEncoderPreset()
      {
         // This sample uses the new preset for content-aware encoding
         PresetName = EncoderNamedPreset.ContentAwareEncoding
      }
   }
};
```

> [!NOTE]
> 使用预设对`ContentAwareEncoding`作业进行编码，并根据输出分钟数计费。 

## <a name="next-steps"></a>后续步骤

* [教程：使用媒体服务 v3 上传、编码和流式传输视频](stream-files-tutorial-with-api.md)
* [教程：基于 URL 对远程文件进行编码并流式传输视频 - REST](stream-files-tutorial-with-rest.md)
* [教程：基于 URL 对远程文件进行编码并流式传输视频 - CLI](stream-files-cli-quickstart.md)
* [教程：基于 URL 对远程文件进行编码并流式传输视频 - .NET](stream-files-dotnet-quickstart.md)
* [教程：基于 URL 对远程文件进行编码并流式传输视频 - Node.js](stream-files-nodejs-quickstart.md)
