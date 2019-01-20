---
title: 什么是计算机视觉 API？ - 计算机视觉
titlesuffix: Azure Cognitive Services
description: 使用计算机视觉服务，开发人员可以访问用于处理图像并返回信息的高级算法。
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: overview
ms.date: 08/22/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: cd4eca669936a206241069f1f8a8d9440f45be33
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2019
ms.locfileid: "54229772"
---
# <a name="what-is-computer-vision"></a>什么是计算机视觉？

使用基于云的计算机视觉服务，开发人员可以访问用于处理图像并返回信息的高级算法。 计算机视觉适用于常用图像格式，例如 JPEG 和 PNG。 若要分析图像，可以上传图像，也可以指定图像 URL。 计算机视觉算法可以通过不同方式分析图像的内容，具体取决于你感兴趣的视觉功能。 例如，计算机视觉可以确定图像是否包含成人内容或不雅内容，或者查找图像中的所有人脸。

可以在应用程序中使用计算机视觉，方法是：通过[客户端库](quickstarts-sdk/csharp-analyze-sdk.md)来调用服务，或者直接调用 [REST API](vision-api-how-to-topics/howtocallvisionapi.md)，以便：

- [通过分析图像来获取见解](#analyzing-images-for-insight)
- [从图像中提取文本](#extracting-text-from-images)
- [管理图像中的内容](#moderating-content-in-images)

## <a name="analyzing-images-for-insight"></a>通过分析图像来获取见解

可以使用计算机视觉来分析图像，以便检测并提供有关图像视觉特性和特征的见解。 可以通过上传图像内容来分析本地图像，也可以通过指定图像 URL 来分析远程图像。

计算机视觉在分析图像时，可执行以下操作：

| 操作 | 说明 |
| ------ | ----------- |
|**[标记视觉特性](concept-tagging-images.md)**|从数千个可识别对象、生物、风景和操作识别并标记图像中的视觉对象特性。 如果标记含混不清或者不常见，API 响应会提供“提示”，明确已知设置上下文中的标记的含义。 标记并不局限于主体（如前景中的人员），还包括设置（室内或室外）、家具、工具、工厂、动物、附件、小工具等。|
|**[检测对象](concept-object-detection.md)**| 对象检测类似于添加标记，但 API 返回应用于每个标记的边框坐标。 例如，如果图像包含狗、猫和人，检测操作将列出这些对象及其在图像中的坐标。 可以使用此功能进一步处理图像中各对象之间的关系。 图像中有多个相同标记的实例时，还会通知于你。|
|**[对图像分类](concept-categorizing-images.md)**|使用具有父/子遗传层次结构的[类别分类](Category-Taxonomy.md)对整个图像进行标识和分类。 类别可单独使用或与我们的新标记模型结合使用。<br/>目前，英语是唯一可以对图像进行标记和分类的语言。|
|**[描述图像](concept-describing-images.md)**|使用完整的句子，以人类可读语言生成整个图像的说明。 计算机视觉算法可根据图像中标识的对象生成各种说明。 分别对这些说明进行评估并生成置信度分数。 然后将返回置信度分数从高到低的列表。|
|**[检测人脸](concept-detecting-faces.md)** |检测图像中的人脸，提供每个检测到的人脸的相关信息。 计算机视觉返回每个检测到的人脸的坐标、矩形、性别和年龄。<br/>计算机视觉提供可以在[人脸](/azure/cognitive-services/face/)中发现的部分功能。可以使用人脸服务进行更详细的分析，例如人脸识别和姿势检测。|
|**[检测图像类型](concept-detecting-image-types.md)**|检测图像特征，例如图像是否为素描，或者图像是剪贴画的可能性。|
|**[检测特定领域的内容](concept-detecting-domain-content.md)**|使用域模型来检测和标识图像中特定领域的内容，例如名人和地标。 例如，如果图像中包含人物，则计算机视觉可以使用服务随附的针对名人的域模型来确定图像中检测到的人物是否与已知名人匹配。|
|**[检测颜色方案](concept-detecting-color-schemes.md)**|分析图像中的颜色使用情况。 计算机视觉可以确定图像是黑白的还是彩色的，而对于彩色图像，又可以确定主色和主题色。|
|**[生成缩略图](concept-generating-thumbnails.md)**|分析图像的内容，生成该图像的相应缩略图。 计算机视觉首先生成高质量缩略图，然后通过分析图像中的对象来确定“感兴趣区域”。 然后，计算机视觉会裁剪图像以满足感兴趣区域的要求。 可以用户需求使用与原始图像的纵横比不同的纵横比显示生成的缩略图。|
|**[获取感兴趣区域](concept-generating-thumbnails.md#area-of-interest)**|分析图像内容，以返回“感兴趣区域”的坐标。 这是用于生成缩略图的相同函数，但是计算机视觉并没有裁剪图像，而是返回该区域的边框坐标，因此调用的应用程序可以根据需要修改原始图像。|

## <a name="extracting-text-from-images"></a>从图像中提取文本

可以使用计算机视觉[通过 OCR 来提取文本](concept-extracting-text-ocr.md)，将文本从图像提取到计算机可读的字符流中。 如果需要，OCR 会围绕图像水平轴更正所识别文本的方向（以度为单位），并提供每个词的帧坐标。 OCR 支持 25 种语言，并会自动检测已提取文本的语言。

也可在图像中[识别印刷文本和手写文本](concept-recognizing-text.md)。 计算机视觉可以在图像中检测并提取印刷文本和手写文本，这些图像包含各种具有不同表面和背景的对象（例如收据、海报、名片、信函、白板）。 目前，识别印刷文本和手写文本功能为预览版，并且仅支持英语。  

## <a name="moderating-content-in-images"></a>管理图像中的内容

可以使用计算机视觉在图像中[检测成人内容和不雅内容](concept-detecting-adult-content.md)，对图像包含成人内容或不雅内容的可能性进行评分，并为二者生成一个置信度分数。 可以根据自己的偏好在滑尺上设置成人和不雅内容检测的筛选器。

## <a name="using-containers"></a>使用容器

将标准化的 Docker 容器安装到靠近数据的位置以后，即可在本地[使用计算机视觉容器](computer-vision-how-to-install-containers.md)识别打印的和手写的文本。

## <a name="image-requirements"></a>映像要求

计算机视觉可以分析符合以下要求的图像：

- 图像必须以 JPEG、PNG、GIF 或 BMP 格式显示
- 图像的文件大小必须不到 4 兆字节 (MB)
- 图像的尺寸必须大于 50 x 50 像素  
  对于 OCR，图像的尺寸必须介于 50 x 50 和 4200 x 4200 像素之间

## <a name="data-privacy-and-security"></a>数据隐私和安全性

与所有认知服务一样，使用计算机视觉服务的开发人员应该了解 Microsoft 针对客户数据的政策。 请参阅 Microsoft 信任中心上的[“认知服务”页面](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)来了解详细信息。

## <a name="next-steps"></a>后续步骤

阅读以下快速入门文章之一，完成计算机视觉入门：

- [分析图像](quickstarts-sdk/csharp-analyze-sdk.md)
- [提取手写文本](quickstarts-sdk/csharp-hand-text-sdk.md)
- [生成缩略图](quickstarts-sdk/csharp-thumb-sdk.md)
