---
title: 发行说明 - 人脸 API 服务
titleSuffix: Azure Cognitive Services
description: 人脸 API 服务的发行说明包括各种版本的发布更改历史记录。
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: yluiu
ms.openlocfilehash: a7667f94d3f4dea2901c4b4b0e2b2c893b9f535e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67074098"
---
# <a name="face-api-release-notes"></a>人脸 API 发行说明

本文适用于人脸 API 服务 1.0 版。

### <a name="release-changes-in-june-2019"></a>2019 年 6 月版本更改

* 添加新的人脸检测模型，小、 端视图、 封闭的像素和模糊的人脸上的改进准确度。 使用它通过[人脸-检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)， [FaceList-添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)，[大型人脸列表-添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)， [person Group 人员-添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)和[大型人物组人员-添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)通过指定新的人脸检测模型名称`detection_02`中`detectionModel`参数。 中的更多详细信息[如何指定检测模型](Face-API-How-to-Topics/specify-detection-model.md)。

### <a name="release-changes-in-april-2019"></a>2019 年 4 月版本更改

* 提高了 `age` 和 `headPose` 属性的总体准确性。 此外还更新了 `headPose` 属性，`pitch` 值现已启用。 使用这些属性的方法是在[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` 参数的 `returnFaceAttributes` 参数中指定它们。 

* 提高了[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)、[FaceList - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)、[LargeFaceList - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)、[PersonGroup 人员 - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)和 [LargePersonGroup 人员 - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)的速度。

### <a name="release-changes-in-march-2019"></a>2019 年 3 月版本更改

* 添加了新的人脸识别模型，提高了准确度。 通过[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)、[FaceList - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b)、[LargeFaceList - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)、[PersonGroup - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)和 [LargePersonGroup - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)来使用它，只需在 `recognitionModel` 参数中指定新的人脸识别模型名称 `recognition_02` 即可。 [如何指定识别模型](Face-API-How-to-Topics/specify-recognition-model.md)中提供了更多详细信息。

### <a name="release-changes-in-january-2019"></a>2019 年 1 月的发布更改

* 添加了快照功能以支持跨订阅的数据迁移：[快照](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/snapshot-get)。 如需更多详细信息，请参阅[如何将人脸数据迁移到其他人脸订阅](Face-API-How-to-Topics/how-to-migrate-face-data.md)。

### <a name="release-changes-in-october-2018"></a>2018 年 10 月的发布更改

* 在 [PersonGroup - 获取训练状态](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247)、[LargePersonGroup - 获取训练状态](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae32c6ac60f11b48b5aa5)和 [LargeFaceList - 获取训练状态](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a1582f8d2de3616c086f2cf)中完善了对 `status`、`createdDateTime`、`lastActionDateTime` 和 `lastSuccessfulTrainingDateTime` 的说明。

### <a name="release-changes-in-may-2018"></a>2018 年 5 月的发布更改

* 显着改进了 `gender` 属性，还改进了 `age`、`glasses`、`facialHair`、`hair`、`makeup` 属性。 通过[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` 参数使用这些属性。 

* 在以下部分中将输入图像文件大小限制从 4 MB 增加到了 6 MB：[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)、[FaceList - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)、[LargeFaceList - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)、[PersonGroup 人员 - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)和 [LargePersonGroup人员 - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)。

### <a name="release-changes-in-march-2018"></a>2018 年 3 月的发布更改

* 增加了百万规模的容器：[LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) 和 [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)。 有关更多详细信息，请参阅[如何使用大规模功能](Face-API-How-to-Topics/how-to-use-large-scale.md)。

* 将[人脸 - 识别](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) `maxNumOfCandidatesReturned` 参数从 [1, 5] 增加到了 [1, 100]，默认为 10。

### <a name="release-changes-in-may-2017"></a>2017 年 5 月的发布更改

* 在[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` 参数中添加了 `hair`、`makeup`、`accessory`、`occlusion`、`blur`、`exposure` 和 `noise` 属性。

* 在 PersonGroup 和[人脸 - 识别](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)中支持 10K 人员。

* 在带可选参数 `start` 和 `top` 的 [PersonGroup 人员 - 列表](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241)中支持分页。

* 支持在针对 PersonGroup 中的不同 FaceLists 和不同人员添加/删除人脸时的并发性。

### <a name="release-changes-in-march-2017"></a>2017 年 3 月的发布更改
* 在[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` 参数中添加了 `emotion` 属性。

* 修复了无法使用从[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)返回的矩形重新将人脸检测为 [FaceList - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)和 [PersonGroup 人员 - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)中的 `targetFace`。

* 修复了可检测的人脸大小，以确保它严格在 36x36 到 4096x4096 像素之间。

### <a name="release-changes-in-november-2016"></a>2016 年 11 月的发布更改
* 添加了人脸存储标准订阅以在使用 [PersonGroup 人员 - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)或 [FaceList - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)进行标识或相似性匹配时存储额外的持久性人脸。 存储的图像按每 1000 张人脸 0.5 美元收费，每天以此费率按比例计费。 免费层订阅的总人数仍限制为 1,000 人。

### <a name="release-changes-in-october-2016"></a>2016 年 10 月的发布更改
* 在 [FaceList - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)和 [PersonGroup 人员 - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)中将 targetFace 中多个人脸的错误消息从“There are more than one face in the image”更改为“There is more than one face in the image”。

### <a name="release-changes-in-july-2016"></a>2016 年 7 月的发布更改
* 支持在[人脸 - 验证](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)中进行人脸到人员对象身份验证。

* 添加了可选的 `mode` 参数，可以选择两种工作模式：[人脸 - 查找相似](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)中的 `matchPerson` 和 `matchFace`，默认值为 `matchPerson`。

* 为用户添加了可选的 `confidenceThreshold` 参数，以设置一个人脸是否属于[人脸 - 识别](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)中的 Person 对象的阈值。

* 在 [PersonGroup - 列表](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395248)中添加了可选的 `start` 和 `top` 参数，使用户能够指定要列出的起点和总 PersonGroup 数。

### <a name="v10-changes-from-v0"></a>从 V0 进行的 V1.0 更改
* 将服务根终结点从 ```https://westus.api.cognitive.microsoft.com/face/v0/``` 更改为 ```https://westus.api.cognitive.microsoft.com/face/v1.0/```。 更改已应用于：[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)、[人脸 - 识别](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)、[人脸 - 查找相似](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)和[人脸 - 组](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238)。

* 将最小可检测人脸大小更新为 36x36 像素。 将不会检测到小于 36x36 像素的人脸。

* 已弃用人脸 V0 中的 PersonGroup 和 Person 数据。 使用人脸 V1.0 服务无法访问这些数据。

* 于 2016 年 6 月 30 日弃用了人脸 API 的 V0 终结点。
