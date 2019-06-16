---
title: 人脸检测和属性的概念
titleSuffix: Azure Cognitive Services
description: 了解有关人脸检测和人脸属性的概念。
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: pafarley
ms.openlocfilehash: e61048eeab9d7061c18f3237db22fc87ca52f526
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65891172"
---
# <a name="face-detection-and-attributes"></a>人脸检测和属性

本文解释人脸检测和人脸属性数据的概念。 人脸检测是图像中查找人脸并有选择地返回不同类型的人脸相关数据的操作。

可以使用[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)操作在图像中检测人脸。 至少，每个检测到的人脸对应于在响应中 faceRectangle 字段。 此组的左侧、 顶部、 宽度和高度的像素坐标将标记所在人脸。 使用这些坐标，可以获取的人脸和其大小的位置。 在 API 响应中，人脸按照从大到小的顺序列出。

## <a name="face-id"></a>人脸 ID

人脸 ID 是每个检测到的人脸在图像中的唯一标识符字符串。 可以在[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API 调用中请求人脸 ID。

## <a name="face-landmarks"></a>人脸特征点

人脸特征点是一组易于查找人脸，如瞳孔、 鼻子的提示上的点。 默认有 27 个预定义的特征点。 下图显示了所有 27 个点：

![标有所有 27 个特征点的人脸插图](../Images/landmarks.1.jpg)

以像素为单位返回的点坐标。

## <a name="attributes"></a>属性

属性是一组 （可选） 通过检测到的功能[人脸-检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)API。 可以检测到以下属性：

* **年龄**。 在年的特定的人脸估计的年龄。
* **模糊**。 在图像中人脸的模糊。 此属性返回的值介于 0 和一个与非正式分级为低，之间中, 或高。
* **情感**。 使用给定的人脸其检测置信度的情感的列表。 规范化后的置信度评分，并跨所有情感评分添加最多一个。 返回的情感包括快乐、悲伤、中性、愤怒、蔑视、厌恶、惊讶、恐惧。
* **暴露**。 在图像中人脸的暴露。 此属性返回的值介于 0 和一个与曝光、 goodExposure 或过度非正式分级之间。
* **面部毛发**。 估计的面部毛发存在以及给定的人脸的长度。
* **性别**。 给定的人脸估计的性别。 可能的值为男性、 女性，和 genderless。
* **眼镜**。 给定的人脸是否眼镜。 可能的值为 NoGlasses、 ReadingGlasses、 场闹剧，和游泳 Goggles。
* **十字线**。 面部毛发类型。 此属性显示十字线是否可见、 是否检测到 baldness，以及检测到哪些十字线颜色。
* **头部姿势**。 在 3D 空间中的人脸的方向。 此属性描述的间距、 滚动，和偏转以度为单位的角度。 值的范围分别为-90 度到 90 度、-180 到 180 度度和-90 度到 90 度。 有关角度映射，请参阅以下示意图：

    ![标有俯仰、翻滚和偏航轴的头部](../Images/headpose.1.jpg)
* **构成**。 人脸是否构成。 此属性返回一个布尔值，eyeMakeup 和 lipMakeup。
* **干扰**。 人脸图像中检测到视觉干扰。 此属性返回的值介于 0 和一个与非正式分级为低，之间中, 或高。
* **封闭**。 是否有对象人脸阻止的部分。 此属性返回一个布尔值，eyeOccluded、 foreheadOccluded 和 mouthOccluded。
* **微笑**。 给定的人脸笑脸表达式。 此值为零表示没有笑脸，另一个用于清除笑脸之间。

> [!IMPORTANT]
> 通过使用统计算法预测人脸属性。 它们可能始终不准确。 在决定使用基于属性的数据时要格外小心。

## <a name="input-data"></a>输入数据

使用以下提示来确保你输入的映像提供最准确的检测结果：

* 所支持的输入的图像格式的第一帧和 BMP 是 JPEG、 PNG、 GIF。
* 图像文件大小应为不大于 4 MB。
* 可检测的人脸大小范围为 36 x 36 到 4096 x 4096 像素。 无法检测超出此范围的人脸。
* 某些面部可能因技术难题而无法检测。 极端的面部角度 （头部姿势） 或人脸的阻挡物 （太阳镜或阻止的人脸一部分的手之类的对象） 可能会影响检测。 正面和接近正面的人脸可提供最佳结果。

如果检测视频源中的人脸时，您可以通过调整视频摄像机上的某些设置来提高性能：

* **平滑**:许多视频照相机应用平滑的效果。 如果可能因为它会创建帧之间的模糊并减少了清楚起见，应将关闭此。
* **快门速度**:更快的快门速度可以降低帧之间移动的量并更清晰的每个帧。 我们建议为 1/60 秒或更快的快门速度。
* **快门角度**:某些摄像机指定快门角度而不是快门速度。 如果可能应使用较低的快门角度。 这将导致更清晰的视频帧。

    >[!NOTE]
    > Camera 替换较低的快门角度将在每个帧中，接受较少光，从而使映像将保持较暗。 你将需要确定要使用的正确级别。

## <a name="next-steps"></a>后续步骤

既然您已经熟悉人脸检测的概念，了解如何编写一个脚本，在给定图像中检测人脸。

* [检测图像中的人脸](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)