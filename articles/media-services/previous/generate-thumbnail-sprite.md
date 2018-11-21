---
title: 使用 Azure 媒体服务生成缩略图子画面 | Microsoft Docs
description: 本主题演示了如何使用 Azure 媒体服务生成缩略图子画面。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/24/2018
ms.author: juliako
ms.openlocfilehash: f9ad7fcf414e90acc40ee5cd42e322a3b9e47f17
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2018
ms.locfileid: "49380551"
---
# <a name="generate-a-thumbnail-sprite"></a>生成缩略图子画面 

可以使用 Media Encoder Standard 生成缩略图子画面，该子画面是一个 JPEG 文件，其中包含可拼结为单个大图像的多个小分辨率缩略图，以及一个 VTT 文件。 此 VTT 文件指定每个缩略图在输入视频中代表的时间范围，以及该缩略图在 JPEG 大文件中的大小和坐标。 视频播放器使用 VTT 文件和子画面图像来显示一个“视觉对象”拖动条，提供一个查看器，用于在沿视频时间线前后推移时提供可视反馈。

为了使用 Media Encoder Standard 生成缩略图子画面，预设需要满足以下条件：

1. 必须使用 JPG 缩略图图像格式
2. 必须将 Start/Step/Range 值指定为时间戳或 % 值（而不是帧数） 
    
    1. 可以混合使用时间戳和 % 值

3. 必须具有 SpriteColumn 值，并且必须为大于或等于 1 的非负数

    1. 若将 SpriteColumn 设置为 M > = 1，输出图像则是带有 M 列的矩形。 若通过 #2 生成的缩略图数量不是 M 的精确倍数，则最后一行将不完整且留有黑色像素。  

下面是一个示例：

```json
{
    "Version": 1.0,
    "Codecs": [
    {
      "Start": "00:00:01",
      "Type": "JpgImage",
      "Step": "5%",
      "Range": "100%",
      "JpgLayers": [
        {
          "Type": "JpgLayer",
          "Width": "10%",
          "Height": "10%",
          "Quality": 90
        }
      ],
      "SpriteColumn": 10
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

## <a name="known-issues"></a>已知问题

1.  无法生成包含单行图像的子画面图像（SpriteColumn = 1 时生成的图像包含一列）。
2.  尚不支持将子画面图像分块为中等大小的 JPEG 图像。 因此，必须审慎限制缩略图的数量和大小，使拼结成的缩略图子画面像素约为 8M。
3.  Azure Media Player 在 Microsoft Edge、Chrome 和 Firefox 浏览器上支持子画面。 IE11 不支持 VTT 分析服务。

## <a name="next-steps"></a>后续步骤

[对内容进行编码](media-services-encode-asset.md)