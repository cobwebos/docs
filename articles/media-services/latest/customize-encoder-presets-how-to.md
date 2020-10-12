---
title: 使用媒体服务 v3 .NET 对自定义转换进行编码 - Azure | Microsoft Docs
description: 本主题介绍如何使用 .NET 通过 Azure 媒体服务 v3 对自定义转换进行编码。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: b5bb505df3cad7856e0b08f04949c2e56ccec1ca
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89295608"
---
# <a name="how-to-encode-with-a-custom-transform---net"></a>如何对自定义转换进行编码 - .NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

使用 Azure 媒体服务进行编码时，可以根据[流式传输文件](stream-files-tutorial-with-api.md)教程中演示的行业最佳做法，使用推荐的内置预设之一快速入门。 也可以构建自定义预设以针对特定方案或设备要求。

## <a name="considerations"></a>注意事项

创建自定义预设时，请注意以下事项：

* AVC 内容上的所有高度和宽度值必须是 4 的倍数。
* 在 Azure 媒体服务 v3 中，所有编码比特率均以每秒比特数为单位。 这与我们的 v2 API 的预设不同，后者使用 千比特/秒作为单位。 例如，如果 v2 中的比特率指定为 128（千比特/秒），则在 v3 中它将设置为 128000（比特/秒）。

## <a name="prerequisites"></a>先决条件 

[创建媒体服务帐户](./create-account-howto.md)

## <a name="download-the-sample"></a>下载示例

使用以下命令将包含完整 .NET Core 示例的 GitHub 存储库克隆到计算机：  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```
 
自定义预设示例位于 [EncodeCustomTransform](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/EncodeCustomTransform/) 文件夹中。

## <a name="create-a-transform-with-a-custom-preset"></a>使用自定义预设创建转换 

创建新的[转换](/rest/api/media/transforms)时，需要指定希望生成的输出内容。 所需参数是 [TransformOutput](/rest/api/media/transforms/createorupdate#transformoutput) 对象，如以下代码所示。 每个 TransformOutput 包含一个预设   。 **预设**介绍了视频和/或音频处理操作的分步说明，这些操作将用于生成所需的 **TransformOutput**。 以下 **TransformOutput** 创建自定义编解码器和层输出设置。

在创建时[转换](/rest/api/media/transforms)，首先应检查是否其中一个已存在使用**获取**方法，如下面的代码中所示。 在媒体服务 v3 中，如果实体不存在（对名称进行不区分大小写检查），实体上的 **Get** 方法将返回 **null**。

### <a name="example"></a>示例

下面的示例定义了一组我们希望在使用此转换时生成的输出。 我们首先为音频编码添加一个 AacAudio 层，为视频编码添加两个 H264Video 层。 在视频层中，我们分配标签，以便可以在输出文件名中使用它们。 接下来，我们希望输出还包括缩略图。 在以下示例中，我们指定 PNG 格式的图像，这些图像以输入视频分辨率的 50% 生成，并以输入视频长度的 {25%, 50%, 75%} 三个时间戳生成。 最后，我们指定输出文件的格式 - 一个用于视频 + 音频，另一个用于缩略图。 由于我们有多个 H264 层，因此我们必须使用宏来为每个层生成唯一的名称。 可以使用 `{Label}` 或 `{Bitrate}` 宏，此示例显示了前者。

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/EncodeCustomTransform/MediaV3ConsoleApp/Program.cs#EnsureTransformExists)]

## <a name="next-steps"></a>后续步骤

[流式传输文件](stream-files-tutorial-with-api.md) 
