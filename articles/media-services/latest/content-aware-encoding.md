---
title: 适用于内容识别编码的预设-Azure 媒体服务
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
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2020
ms.locfileid: "76772078"
---
# <a name="use-the-content-aware-encoding-preset-to-find-the-optimal-bitrate-value-for-a-given-resolution"></a>使用 "内容感知编码" 预设来查找给定解析的最佳比特率值

若要为[自适应比特率流式处理](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)准备传递内容，需要以多个比特率（从高到低）对视频进行编码。 这可以确保正常质量的质量下降，因为比特率降低了视频的分辨率。 此类多比特率编码使用所谓的编码阶梯（一份解决方案和比特率的表），请参阅媒体服务内置的[编码预设](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset)。

应注意正在处理的内容，并自定义编码阶梯，并将其调整为单独视频的复杂性。 在每个解决方法中，有比特率更高的比特率敏锐–编码器以此最佳比特率进行操作。 下一级别的优化是选择基于内容的解决方案–例如，PowerPoint 演示文稿的视频不会受益于下面的720p。 接下来，可以对编码器进行优化，以优化视频中每个拍摄的设置。 

Microsoft 的[自适应流式处理](autogen-bitrate-ladder.md)预设部分寻址源视频的质量和分辨率的变化问题。 我们的客户有不同的内容组合，某些位置为1080p，另一些则为720p，一些 at SD 和低分辨率。 而且，并非所有源内容都是从电影或电视工作室获得高质量的 mezzanines。 自适应流式处理预设通过确保比特率阶梯从不超过输入夹层的分辨率或平均比特率来解决这些问题。 但是，此预设不检查分辨率和比特率之外的源属性。

## <a name="the-content-aware-encoding"></a>内容感知编码 

内容识别编码预设通过合并自定义逻辑来扩展 "自适应比特率流式处理" 机制，该机制可让编码器查找给定解析的最佳比特率值，而无需进行大量的计算分析。 此预设生成一组 GOP 对齐的 Mp4。 根据给定的任何输入内容，该服务会对输入内容执行初始的轻型分析，并使用这些结果来确定最理想的层数，并为自适应流式处理提供适当的比特率和分辨率设置。 此预设对于低和中复杂度视频特别有效，其中输出文件的比特率低于自适应流式处理预设，但仍会为查看者提供良好的体验。 输出将包含带有视频和音频交错的有文件文件

下面的示例关系图显示了使用[PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio)和[VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion)之类的质量指标进行比较。 源是通过连接电影和电视节目中的高复杂性照片的简短剪辑来创建的，目的是为了强调编码器。 按照定义，此预设会生成与内容不同的结果-这也意味着对于某些内容，可能不会显著降低比特率或质量提高。

![使用 PSNR 的速率失真（RD）曲线](media/content-aware-encoding/msrv1.png)

**图1：使用 PSNR 度量值为高复杂性源的速率失真（RD）曲线**

![使用 VMAF 的速率失真（RD）曲线](media/content-aware-encoding/msrv2.png)

**图2：使用 VMAF 度量值为高复杂性源的速率失真（RD）曲线**

下面是另一个类别的源内容的结果，编码器能够确定输入的质量质量较差（由于低比特率，很多压缩项目）。 请注意，使用内容感知预设时，编码器决定仅生成一个输出层–低比特率，以便大多数客户端能够在不停止的情况下播放流。

![使用 PSNR 的 RD 曲线](media/content-aware-encoding/msrv3.png)

**图3：使用 PSNR 的 RD 曲线用于低质量输入（在1080p 上）**

![使用 VMAF 的 RD 曲线](media/content-aware-encoding/msrv4.png)

**图4：使用 VMAF 实现低质量输入的 RD 曲线（以1080p 为限）**

## <a name="how-to-use-the-content-aware-encoding-preset"></a>如何使用内容识别编码预设 

可以创建使用此预设的转换，如下所示。 

> [!TIP]
> 有关使用转换输出的教程，请参阅[后续步骤](#next-steps)部分。 可以从媒体服务流式处理终结点（如 MPEG-2 和 HLS）传递输出资产（如教程中所述）。


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
> 使用 `ContentAwareEncoding` 预设的编码作业按输出分钟数计费。 

## <a name="next-steps"></a>后续步骤

* [教程：通过媒体服务 v3 上传、编码和流式传输视频](stream-files-tutorial-with-api.md)
* [教程：根据 URL 编码远程文件并流式传输视频-REST](stream-files-tutorial-with-rest.md)
* [教程：根据 URL 编码远程文件并流式传输视频-CLI](stream-files-cli-quickstart.md)
* [教程：根据 URL 编码远程文件并流式传输视频-.NET](stream-files-dotnet-quickstart.md)
* [教程：根据 URL 编码远程文件并流式传输视频 node.js](stream-files-nodejs-quickstart.md)
