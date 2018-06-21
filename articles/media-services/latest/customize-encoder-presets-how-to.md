---
title: 使用 Azure 媒体服务 v3 对自定义转换进行编码 | Microsoft Docs
description: 本主题介绍如何使用 Azure 媒体服务 v3 对自定义转换进行编码。
services: media-services
documentationcenter: ''
author: Juliako
manager: cflower
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 05/17/2018
ms.author: juliako
ms.openlocfilehash: d298070877a366d04b2df1ef8ac63b08f8771de0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34659137"
---
# <a name="how-to-encode-with-a-custom-transform"></a>如何对自定义转换进行编码

使用 Azure 媒体服务进行编码时，可以使用[流式传输文件](stream-files-tutorial-with-api.md)教程中演示的行业最佳做法推荐的内置预设之一快速入门，也可以选择针对特定方案或设备要求生成自定义预设。 

> [!Note]
> 在 Azure 媒体服务 v3 中，所有编码比特率均以每秒比特数为单位。 这与 REST v2 Media Encoder Standard 预设不同。 例如，v2 中的比特率指定为 128，但在 v3 中为 128000。

## <a name="download-the-sample"></a>下载示例

使用以下命令将包含完整 .NET Core 示例的 GitHub 存储库克隆到计算机：  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```
 
自定义预设示例位于 [EncodeCustomTransform](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/EncodeCustomTransform/) 文件夹中。

## <a name="create-a-transform-with-a-custom-preset"></a>使用自定义预设创建转换 

创建新的[转换](https://docs.microsoft.com/rest/api/media/transforms)时，需要指定希望生成的输出内容。 所需参数是 TransformOutput 对象，如以下代码所示。 每个 TransformOutput 包含一个预设。 预设介绍了视频和/或音频处理操作的分步说明，这些操作将用于生成所需的 TransformOutput。 以下 **TransformOutput** 将创建自定义编解码器和层输出设置。

在创建时[转换](https://docs.microsoft.com/rest/api/media/transforms)，首先应检查是否其中一个已存在使用**获取**方法，如下面的代码中所示。  在媒体服务 v3 中，如果实体不存在（不区分大小写的名称检查），实体上的 **Get** 方法会返回 **null**。

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/EncodeCustomTransform/MediaV3ConsoleApp/Program.cs#EnsureTransformExists)]

## <a name="next-steps"></a>后续步骤

[流式传输文件](stream-files-tutorial-with-api.md) 
