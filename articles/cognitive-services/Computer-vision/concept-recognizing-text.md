---
title: 识别印刷文本、手写文本 - 计算机视觉
titleSuffix: Azure Cognitive Services
description: 与使用计算机视觉 API 识别图像中的印刷文本和手写文本相关的概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 48ce15a11c3e3282535420f3e1bb1915276d70f5
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2019
ms.locfileid: "56313171"
---
# <a name="recognizing-printed-and-handwritten-text"></a>识别打印文本和手写文本

计算机视觉可以在图像中检测并提取印刷文本或手写文本，这些图像包含各种具有不同表面和背景的对象（例如收据、海报、名片、信函、白板）。

文本识别功能与[光学字符识别 (OCR)](concept-extracting-text-ocr.md) 非常相似，但与 OCR 不同，它以异步方式执行并使用更新的识别模型。

> [!NOTE]
> 此技术当前处于预览状态，且仅适用于英语文本。

## <a name="text-recognition-requirements"></a>文本识别要求

计算机视觉可以识别符合以下要求的图像中的印刷和手写文本：

- 图像必须以 JPEG、PNG 或 BMP 格式显示
- 图像的文件大小必须不到 4 兆字节 (MB)
- 图像的尺寸必须介于 50 x 50 和 4200 x 4200 像素之间

## <a name="next-steps"></a>后续步骤

请参阅[识别文本参考文档](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200)了解详细信息。