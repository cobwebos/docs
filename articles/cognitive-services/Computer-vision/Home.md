---
title: 什么是计算机视觉 API？ - 计算机视觉
titlesuffix: Azure Cognitive Services
description: 使用计算机视觉服务，开发人员可以访问用于处理图像并返回信息的高级算法。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 03/04/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 87c5d24f0e9077acf66bacd97097cfc32bd49ead
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2019
ms.locfileid: "57432101"
---
# <a name="what-is-computer-vision"></a>什么是计算机视觉？

使用 Azure 的计算机视觉服务，开发人员可以访问用于处理图像并返回信息的高级算法。 若要分析图像，可以上传图像，也可以指定图像 URL。 图像处理算法可以通过多种不同的方式分析内容，具体取决于你感兴趣的视觉功能。 例如，计算机视觉可以确定图像是否包含成人内容或不雅内容，或者查找图像中的所有人脸。

可以在应用程序中使用计算机视觉，方法是：使用本机 SDK，或者直接调用 REST API。 此页广泛地介绍了计算机视觉的功能。

## <a name="analyze-images-for-insight"></a>通过分析图像来获取见解

可以分析图像，以便检测并提供有关视觉特性和特征的见解。 下表中的所有特性由[分析图像](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API 提供。

| 操作 | 说明 |
| ------ | ----------- |
|**[标记视觉特性](concept-tagging-images.md)**|从数千个可识别对象、生物、风景和操作识别并标记图像中的视觉对象特性。 如果标记含混不清或者不常见，API 响应会提供“提示”，明确已知设置上下文中的标记的含义。 标记并不局限于主体（如前景中的人员），还包括设置（室内或室外）、家具、工具、工厂、动物、附件、小工具等。|
|**[检测对象](concept-object-detection.md)**| 对象检测类似于添加标记，但 API 返回应用于每个标记的边框坐标。 例如，如果图像包含狗、猫和人，检测操作将列出这些对象及其在图像中的坐标。 可以使用此功能进一步处理图像中各对象之间的关系。 图像中有多个相同标记的实例时，还会通知于你。|
|**[检测品牌](concept-brand-detection.md)**|根据一个包含数千全球徽标的数据库，确定图像或视频中的商业品牌。 可以使用此功能来执行特定的操作，例如，发现哪些品牌在社交媒体上最受欢迎，或者哪些品牌在社交产品排名上最靠前。|
|**[对图像分类](concept-categorizing-images.md)**|使用具有父/子遗传层次结构的[类别分类](Category-Taxonomy.md)对整个图像进行标识和分类。 类别可单独使用或与我们的新标记模型结合使用。<br/>目前，英语是唯一可以对图像进行标记和分类的语言。|
|**[描述图像](concept-describing-images.md)**|使用完整的句子，以人类可读语言生成整个图像的说明。 计算机视觉算法可根据图像中标识的对象生成各种说明。 分别对这些说明进行评估并生成置信度分数。 然后将返回置信度分数从高到低的列表。|
|**[检测人脸](concept-detecting-faces.md)** |检测图像中的人脸，提供每个检测到的人脸的相关信息。 计算机视觉返回每个检测到的人脸的坐标、矩形、性别和年龄。<br/>计算机视觉提供可以在[人脸](/azure/cognitive-services/face/)中发现的部分功能。可以使用人脸服务进行更详细的分析，例如人脸识别和姿势检测。|
|**[检测图像类型](concept-detecting-image-types.md)**|检测图像特征，例如图像是否为素描，或者图像是剪贴画的可能性。|
|**[检测特定领域的内容](concept-detecting-domain-content.md)**|使用域模型来检测和标识图像中特定领域的内容，例如名人和地标。 例如，如果图像中包含人物，则计算机视觉可以使用服务随附的针对名人的域模型来确定图像中检测到的人物是否与已知名人匹配。|
|**[检测颜色方案](concept-detecting-color-schemes.md)**|分析图像中的颜色使用情况。 计算机视觉可以确定图像是黑白的还是彩色的，而对于彩色图像，又可以确定主色和主题色。|
|**[生成缩略图](concept-generating-thumbnails.md)**|分析图像的内容，生成该图像的相应缩略图。 计算机视觉首先生成高质量缩略图，然后通过分析图像中的对象来确定“感兴趣区域”。 然后，计算机视觉会裁剪图像以满足感兴趣区域的要求。 可以用户需求使用与原始图像的纵横比不同的纵横比显示生成的缩略图。|
|**[获取感兴趣区域](concept-generating-thumbnails.md#area-of-interest)**|分析图像内容，以返回“感兴趣区域”的坐标。 这是用于生成缩略图的相同函数，但是计算机视觉并没有裁剪图像，而是返回该区域的边框坐标，因此调用的应用程序可以根据需要修改原始图像。|

## <a name="extract-text-from-images"></a>从图像中提取文本

可以使用计算机视觉通过[光学字符识别 (OCR)](concept-recognizing-text.md#ocr-optical-character-recognition-api) 将图像中的文本提取到机器可读的字符流中。 如果需要，OCR 会校正已识别文本的旋转角度并提供每个词的帧坐标。 OCR 支持 25 种语言，并会自动检测已识别文本的语言。

还可以使用 [Read API](concept-recognizing-text.md#read-api) 从图像和以文本为主的文档中提取印刷体文本和手写文本。 Read API 使用更新的模型，适用于具有不同表面和背景的各种对象，比如收据、海报、名片、信件和白板。 目前，Read API 处于预览状态，英语是唯一受支持的语言。

## <a name="moderate-content-in-images"></a>管理图像中的内容

可以使用计算机视觉来[检测图像中的成人和不雅内容](concept-detecting-adult-content.md)，并返回这两者的置信度分数。 可以根据自己的偏好在滑尺上设置成人和不雅内容检测的筛选器。

## <a name="use-containers"></a>使用容器

将标准化的 Docker 容器安装到靠近数据的位置以后，即可在本地[使用计算机视觉容器](computer-vision-how-to-install-containers.md)识别印刷体文本和手写文本。

## <a name="image-requirements"></a>映像要求

计算机视觉可以分析符合以下要求的图像：

- 图像必须以 JPEG、PNG、GIF 或 BMP 格式显示
- 图像的文件大小必须不到 4 兆字节 (MB)
- 图像的尺寸必须大于 50 x 50 像素
  - 对于 OCR，图像的尺寸必须介于 50 x 50 和 4200 x 4200 像素之间

## <a name="data-privacy-and-security"></a>数据隐私和安全性

与所有认知服务一样，使用计算机视觉服务的开发人员应该了解 Microsoft 针对客户数据的政策。 请参阅 Microsoft 信任中心上的[“认知服务”页面](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)来了解详细信息。

## <a name="next-steps"></a>后续步骤

按某个快速入门指南操作，完成计算机视觉入门：

- [快速入门：分析图像](quickstarts-sdk/csharp-analyze-sdk.md)
- [快速入门：提取手写文本](quickstarts-sdk/csharp-hand-text-sdk.md)
- [快速入门：生成缩略图](quickstarts-sdk/csharp-thumb-sdk.md)
