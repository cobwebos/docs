---
title: 人脸识别概念
titleSuffix: Azure Cognitive Services
description: 了解有关人脸识别的概念。
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pafarley
ms.openlocfilehash: 8c0bf001c2bdbedaa041dbd10b5c7edb08eec4c6
ms.sourcegitcommit: 524625dd12e0537173616a991802075e2dc9da12
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2019
ms.locfileid: "64415995"
---
# <a name="face-recognition-concepts"></a>人脸识别概念

此文章介绍了不同的人脸识别操作 （验证、 查找相似，分组，标识） 和基础数据结构的概念。 广泛地说，识别描述了比较两个不同的人脸，以确定它们是否类似的工作或属于同一个人。

## <a name="recognition-related-data-structures"></a>识别相关的数据结构

识别操作使用主要的以下数据结构。 这些对象存储在云中，并可由其 ID 字符串引用。 ID 字符串因此始终是唯一在订阅中，而可以重复名称字段。

|名称|描述|
|:--|:--|
|**DetectedFace**| 这是通过检索到的单个面部表示形式[人脸检测](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)操作。 其 ID 过期后创建的 24 小时。|
|**PersistedFace**| 当**DetectedFace**对象添加到组 (如**FaceList**或**人员**)，它们即成为**PersistedFace**对象，可以要[检索](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c)在任何时间，并不会过期。|
|**[FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b)**/**[LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)**| 这是一个已分类的列表**PersistedFace**对象。 一个**FaceList**具有唯一 ID、 名称字符串，将根据需要将用户的数据字符串。|
|**[人员](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)**| 这是一系列**PersistedFace**属于同一人的对象。 它具有唯一 ID、 名称字符串，将根据需要将用户的数据字符串。|
|**[PersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)**/**[LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)**| 这是一个已分类的列表**人员**对象。 它具有唯一 ID、 名称字符串，将根据需要将用户的数据字符串。 一个**person Group**必须是[训练](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249)才能识别操作中使用。|

## <a name="recognition-operations"></a>识别操作

本部分详细介绍了四个识别操作如何使用上述数据结构。 请参阅[概述](../Overview.md)广泛的每个识别操作说明。

### <a name="verification"></a>验证

[验证](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)操作将人脸 ID (**DetectedFace**或**PersistedFace**) 和任一另一个人脸 ID 或**人员**对象和确定是否属于同一个人。 如果您传入**Person**，可以选择传入**person Group**该**人员**所属以提高性能。

### <a name="find-similar"></a>查找类似

[查找相似](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)操作将人脸 ID (**DetectedFace**或**PersistedFace**) 并将**FaceList**或其他人脸的数组Id。 与**FaceList**，它将返回较小**FaceList**类似于给定的人脸的人脸。 使用人脸 Id 的数组，它同样会返回一个较小的数组。

### <a name="grouping"></a>分组

[组](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238)操作采用多种人脸 Id 的数组 (**DetectedFace**或**PersistedFace**)，并返回相同的 Id 分组为多个较小的数组。 每个"组"数组包含人脸 Id 会显示类似，和单个"messyGroup"数组包含人脸 Id 为没有相似之处未找到。

### <a name="identification"></a>识别

[标识](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)操作采用一个或多个面部 Id (**DetectedFace**或**PersistedFace**) 和一个**person Group** ，并返回列表**人员**每张人脸可能属于的对象。 返回**Person**对象包装成**候选**具有预测置信度值的对象。

## <a name="input-data"></a>输入数据

使用以下提示来确保你输入的映像提供最准确的识别结果：

* 支持的输入的图像格式为 JPEG、 PNG、 GIF （第一个帧）、 BMP。
* 图像文件的大小应为不大于 4 MB。
* 创建时**人员**对象，应使用功能的各种角度和照明的照片。
* 某些面部可能无法识别由于技术挑战：
  * 极端照明 （背景光等严重） 的映像。
  * 阻止一个或两个眼睛的障碍物。
  * 十字线样式或面部毛发之间的差异。
  * 由于年龄不同而人脸外观中的更改。
  * 极端的面部表情。

## <a name="next-steps"></a>后续步骤

现在，您已熟悉人脸识别概念，了解如何编写一个简单的脚本，用于标识针对一个训练的人脸**person Group**。

* [如何识别图像中的人脸](../Face-API-How-to-Topics/HowtoIdentifyFacesinImage.md)