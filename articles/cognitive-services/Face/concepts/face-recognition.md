---
title: 人脸识别的概念
titleSuffix: Azure Cognitive Services
description: 本文解释“验证”、“查找相似人脸”、“分组”和“识别”人脸识别操作和底层数据结构的概念。
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pafarley
ms.openlocfilehash: 164e5a8c107f445b376d26f9be7db92a7983b0d3
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73743075"
---
# <a name="face-recognition-concepts"></a>人脸识别的概念

本文解释“验证”、“查找相似人脸”、“分组”和“识别”人脸识别操作和底层数据结构的概念。 从广义上讲，识别描述的是比较两个不同的人脸，以确定它们是否相似或属于同一个人的工作流程。

## <a name="recognition-related-data-structures"></a>与识别相关的数据结构

识别操作主要使用以下数据结构。 这些对象存储在云中，可按其 ID 字符串引用。 ID 字符串在订阅中始终唯一。 名称字段可以重复。

|名称|说明|
|:--|:--|
|DetectedFace| 此单个人脸表示形式通过[人脸检测](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)操作检索。 其 ID 在创建 24 小时后过期。|
|PersistedFace| 将 DetectedFace 对象添加到某个组（例如 FaceList 或 Person）后，它们将变成 PersistedFace 对象。 它们可以随时[检索](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c)，且不会过期。|
|[FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b) 或 [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)| 此数据结构是 PersistedFace 对象的分类列表。 FaceList 具有唯一的 ID、名称字符串，以及（可选的）用户数据字符串。|
|[Person](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)| 此数据结构是属于同一个人的 PersistedFace 对象列表。 它具有唯一的 ID、名称字符串，以及（可选的）用户数据字符串。|
|[PersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) 或 [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)| 此数据结构是 Person 对象的分类列表。 它具有唯一的 ID、名称字符串，以及（可选的）用户数据字符串。 PersonGroup 必须事先经过[训练](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249)才能在识别操作中使用。|

## <a name="recognition-operations"></a>识别操作

本部分详细说明四项识别操作如何使用上述数据结构。 [概述](../Overview.md)中详细介绍了每项识别操作。

### <a name="verify"></a>验证

[验证](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)操作采用人脸 ID（来自 DetectedFace 或 PersistedFace）以及另一个人脸 ID 或 Person 对象，并确定它们是否属于同一个人。 如果传入了 Person 对象，可以选择性地传入该 Person 所属的 PersonGroup 以提高性能。

### <a name="find-similar"></a>查找相似

[查找相似人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)操作采用人脸 ID（来自 DetectedFace 或 PersistedFace）以及 FaceList 或其他人脸 ID 的数组。 使用 FaceList 时，它会返回与给定人脸相似的人脸的较小 FaceList。 使用人脸 ID 的数组时，它同样会返回较小的数组。

### <a name="group"></a>组

[分组](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238)操作采用分类人脸 ID（来自 DetectedFace 或 PersistedFace）的数组，并返回分组为多个较小数组的相同 ID。 每个“groups”数组包含看上去相似的人脸 ID。 单个“messyGroup”数组包含未找到相似性的人脸 ID。

### <a name="identify"></a>识别

[识别](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)操作采用一个或多个人脸 ID（来自 DetectedFace 或 PersistedFace）和一个 PersonGroup，并返回每张人脸可能属于的 Person 对象列表。 返回的 Person 对象包装为具有预测置信度值的 Candidate 对象。

## <a name="input-data"></a>输入数据

使用以下提示来确保输入的图像提供最准确的识别结果：

* 支持的输入图像格式为 JPEG、PNG、GIF（第一帧）和 BMP。
* 图像文件不得大于 4 MB。
* 创建 Person 对象时，请使用具有不同类型的角度和照明效果的照片。
* 某些人脸会因技术难题而识别不到，例如：
  * 具有极端照明（例如严重的背光）的图像。
  * 有障碍物挡住了一只或两只眼睛。
  * 发型或胡须的差异。
  * 年龄使面貌发生变化。
  * 极端的面部表情。

## <a name="next-steps"></a>后续步骤

熟悉人脸识别的概念后，接下来请了解如何编写一个可以针对训练的 PersonGroup 识别人脸的脚本。

* [识别图像中的人脸](../Face-API-How-to-Topics/HowtoIdentifyFacesinImage.md)