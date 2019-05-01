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
ms.openlocfilehash: 3293067190386738efbfeb433bd38453c9e5699f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/26/2019
ms.locfileid: "64572554"
---
# <a name="face-detection-and-attributes"></a>人脸检测和属性

本文介绍的人脸检测和人脸属性数据的概念。 人脸检测是图像中查找人脸并有选择地返回各种人脸相关数据的操作。

您使用[人脸-检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)操作以在图像中检测人脸。 至少，每个检测到人脸将对应于**faceRectangle**字段在响应中。 这是一组的像素坐标 （左侧、 顶部、 宽度、 高度） 将标记所在人脸。 使用这些坐标，可以获取的人脸，以及其大小的位置。 在 API 响应中，人脸按从大到最小的大小顺序列出。

## <a name="face-id"></a>人脸 ID

人脸 ID 是只需为每个检测到的人脸在图像中的唯一标识符字符串。 你可以请求中的人脸 ID 你[人脸-检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)API 调用。

## <a name="face-landmarks"></a>人脸特征点

人脸特征点是人脸上的一组易于查找的点，例如瞳孔或鼻尖。 默认有 27 个预定义的特征点。 下图显示了所有 27 个点：

![包含所有 27 个特征点标记的人脸关系图](../Images/landmarks.1.jpg)

以像素为单位返回的点的坐标。

## <a name="attributes"></a>属性

属性是一组 （可选） 通过检测到的其他人脸特征[人脸-检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)API。 以下是可以检测到的属性：

* **年龄**在年中，特定人脸估计的年龄。
* **模糊**图像中人脸的模糊。 这将返回一个值零和一，以及非正式的分级 ("低"、"medium"、"高") 之间。
* **暴露**暴露在图像中人脸。 这将返回一个值零和一，以及非正式分级 （"曝光"、"goodExposure"、"过度"） 之间。
* **情感**情感与给定的人脸其检测置信度的列表。 置信度分数会进行标准化：所有情感的分数加起来后会得到一个总的分数。 返回的情感包括快乐、悲伤、中性、愤怒、蔑视、厌恶、惊讶、恐惧。
* **面部毛发**估计面部毛发状态显示和给定的人脸的长度。
* **性别**估计给定的人脸的性别。 可能的值为"male"、"female"和"genderless"。
* **眼镜**给定的人脸是否具有眼镜。 可能的值为"NoGlasses"、"ReadingGlasses"、"太阳镜"和"游泳 Goggles"。
* **十字线**人脸的十字线样式。 这将显示十字线是否可见、 是否检测到 baldness，和检测到哪些十字线颜色。
* **头部姿势**在 3D 空间中的人脸的方向。 此描述的间距、 滚动，和偏转以度为单位的角度。 值范围为 [-90，90] [-180，180] 和 [-90，90] 度分别。 请参阅下面的关系图的角度映射：

    ![使用音调、 head 滚动，和偏转轴标记为](../Images/headpose.1.jpg)
* **构成**人脸是否有构成。 这将返回一个布尔值，"eyeMakeup"和"lipMakeup"。
* **干扰**视觉干扰在人脸图像中检测到。 这将返回一个值零和一，以及非正式的分级 ("低"、"medium"、"高") 之间。
* **封闭**是否存在阻止所面临的部件的对象。 这将返回一个布尔值，"eyeOccluded"、"foreheadOccluded"和"mouthOccluded"。
* **微笑**给定的人脸笑意。 这是零 （没有笑脸） 和一个 （清除笑脸） 之间的值。

> [!IMPORTANT]
> 人脸属性通过使用统计算法预测，并且始终可能不准确。 根据特性数据做出决策时请小心。

## <a name="input-data"></a>输入数据

使用以下提示来确保你输入的映像提供最准确的检测结果：

* 支持的输入的图像格式为 JPEG、 PNG、 GIF （第一个帧）、 BMP。
* 图像文件的大小应为不大于 4 MB。
* 可检测的人脸大小介于 36x36 像素和 4096x4096 像素范围之间。 不会检测到在此范围外的人脸。
* 某些面部可能因技术难题而无法检测。 极端的面部角度 （头部姿势） 或人脸的阻挡物 （太阳镜或阻止的人脸一部分的手之类的对象） 可能会影响检测。 正面和接近正面的面部可提供最佳的结果。

## <a name="next-steps"></a>后续步骤

现在，您已熟悉人脸检测的概念，了解如何编写一个简单的脚本，用于在给定图像中检测人脸。

* [如何检测图像中人脸](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)