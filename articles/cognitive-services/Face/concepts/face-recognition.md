---
title: 人脸识别的概念
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
ms.openlocfilehash: fa38c492530cb8938e49bc15e13fdd39ed5b6f1c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65890878"
---
# <a name="face-recognition-concepts"></a>人脸识别的概念

本文介绍的概念验证、 查找相似、 组和标识人脸识别操作和基础数据结构。 广泛地说，识别描述了比较两个不同的人脸，以确定它们是类似是否属于同一人的工作。

## <a name="recognition-related-data-structures"></a>与识别相关的数据结构

识别操作主要使用以下数据结构。 这些对象存储在云中，可按其 ID 字符串引用。 ID 字符串始终是订阅中唯一的。 可以重复名称字段。

|Name|描述|
|:--|:--|
|DetectedFace| 检索此单个面部表示形式[人脸检测](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)操作。 其 ID 过期后创建的 24 小时。|
|PersistedFace| DetectedFace 对象添加到组，如 FaceList 或人员，他们会变为 PersistedFace 对象。 它们可以是[检索](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c)在任何时间，并不过期。|
|[FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b)或[大型人脸列表](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)| 此数据结构是 PersistedFace 对象的各种类型的列表。 FaceList 具有唯一 ID、 名称字符串，将根据需要将用户的数据字符串。|
|[Person](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)| 此数据结构是属于同一人的 PersistedFace 对象的列表。 它具有唯一的 ID、名称字符串，以及（可选的）用户数据字符串。|
|[Person Group](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)或[大型人物组](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)| 此数据结构是 Person 对象的各种类型的列表。 它具有唯一的 ID、名称字符串，以及（可选的）用户数据字符串。 必须为 person Group[训练](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249)才能识别操作中使用。|

## <a name="recognition-operations"></a>识别操作

本部分详细介绍了四个识别操作如何使用前面所述的数据结构。 广泛的每个识别操作说明，请参阅[概述](../Overview.md)。

### <a name="verify"></a>验证

[验证](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)操作将人脸 ID DetectedFace 或 PersistedFace 和另一个人脸 ID 或 Person 对象，并确定它们是否属于同一个人。 如果通过在 Person 对象中，您可以选择传入在 person Group，该用户所属以提高性能。

### <a name="find-similar"></a>查找相似

[查找相似](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)操作将人脸 ID 从 DetectedFace 或 PersistedFace 和 FaceList 或其他人脸 Id 的数组。 FaceList，它将返回类似于给定的人脸的人脸的较小 FaceList。 使用人脸 ID 的数组时，它同样会返回较小的数组。

### <a name="group"></a>组

[组](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238)操作从 DetectedFace 或 PersistedFace 采用多种人脸 Id 的数组，并返回相同的 Id 分组为多个较小的数组。 每个"组"数组包含人脸出现类似的 Id。 单个"messyGroup"数组包含人脸 Id 为没有相似之处未找到。

### <a name="identify"></a>标识

[标识](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)操作从 DetectedFace 或 PersistedFace 和 person Group 所需的一个或多个面部 Id 并返回每张人脸可能属于的 Person 对象的列表。 返回的 Person 对象包装成候选对象，其具有预测置信度值。

## <a name="input-data"></a>输入数据

使用以下提示来确保输入的图像提供最准确的识别结果：

* 支持的输入图像格式为 JPEG、PNG、GIF（第一帧）和 BMP。
* 图像文件不得大于 4 MB。
* 创建 Person 对象时，使用功能不同的角度和照明的照片。
* 某些面部可能无法识别由于技术难题，例如：
  * 具有极端照明，例如，严重的图像背景光。
  * 阻止一个或两个眼睛的障碍物。
  * 十字线类型或面部毛发之间的差异。
  * 由于年龄的面部外观中的更改。
  * 极端的面部表情。

## <a name="next-steps"></a>后续步骤

现在，您熟悉使用人脸识别概念，了解如何编写一个脚本来标识对训练 person Group 的人脸。

* [识别图像中的人脸](../Face-API-How-to-Topics/HowtoIdentifyFacesinImage.md)