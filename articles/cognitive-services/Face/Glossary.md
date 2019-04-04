---
title: 术语表 - 人脸 API 服务
titleSuffix: Azure Cognitive Services
description: 术语表解释了使用人脸 API 时可能遇到的术语。
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: bd4285a2d0f882b8c766563f2304031f1f6e2898
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2019
ms.locfileid: "58904791"
---
# <a name="glossary"></a>术语表

## <a name="a"></a>A

#### <a name="attributes"></a>属性

属性是可以检测的可选人脸特征，例如[年龄](#age-attribute)、[性别](#gender-attribute)、[头部姿势](#head-pose-attribute)、[面部毛发](#facial-hair-attribute)、[笑容](#smile-attribute)。 这些属性可以从检测 API 获取，只需指定 _returnFaceAttributes_ 查询参数即可。

如需人脸属性的完整列表，请参阅参考文档：[Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)（人脸 - 检测）。

#### <a name="age-attribute"></a>年龄（属性）

年龄是一个描述特定人脸年龄的[属性](#attributes)。 检测结果中的年龄属性为可选，且可指定 returnFaceAttributes 参数通过检测请求来控制该属性。

有关详细信息，请参阅参考文档：[Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)（人脸 - 检测）。

## <a name="c"></a>C

#### <a name="candidate"></a>候选对象

候选对象基本就是[识别](#identification)结果（例如，识别出的人员和检测置信度）。 候选对象由[PersonID](#person-id) 和[置信度](#confidence)表示，表明识别出的人员置信度较高。

有关详细信息，请参阅参考文档：[Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)（人脸 - 识别）。

#### <a name="confidence"></a>置信度

置信度是一种度量值，以数值表明[人脸](#face)或[人员](#person)的相似度，用于[识别](#identification)和[验证](#verification)过程，指明搜索、识别和验证结果的相似度。

有关详细信息，请参阅参考文档：[人脸 - 查找相似](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)、[人脸 - 识别](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)、[人脸 - 验证](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)。

## <a name="d"></a>D

#### <a name="detectionface-detection"></a>检测/人脸检测

人脸检测是一种在图像中查找人脸的操作。 用户可在请求中上传图像或指定图像 URL。 操作会返回检测到的人脸，其中包含[人脸 ID](#face-id)，表示人脸 API 中的唯一标识。 以像素为单位的矩形表示图像中人脸的位置，以及各个人脸的可选[属性](#attributes)，例如[年龄](#age-attribute)[性别](#gender-attribute)[头部姿势](#head-pose-attribute)、[面部毛发](#facial-hair-attribute)和[笑容](#smile-attribute)。

有关详细信息，请参阅参考文档：[Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)（人脸 - 检测）。

## <a name="e"></a>E

#### <a name="emotion-attribute"></a>情感（属性）

情感是[人脸属性](#attributes)之一。 对它进行查询时，它会返回一个列表，其中包含给定人脸的情感及其检测置信度。 置信度分数会进行标准化：所有情感的分数加起来后会得到一个总的分数。 返回的情感包括快乐、悲伤、中性、愤怒、蔑视、厌恶、惊讶、恐惧。

有关详细信息，请参阅参考文档：[Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)（人脸 - 检测）。

## <a name="f"></a>F

#### <a name="face"></a>人脸

人脸是用于人脸 API 派生的结果的统一术语，该 API 与检测到的人脸相关。 总之，在表示人脸时，可以使用统一的标识（[人脸 ID](#face-id)）、图像中的指定区域（[人脸矩形](#face-rectangle)），以及额外的与人脸相关的属性，例如[年龄](#age-attribute)、[性别](#gender-attribute)、特征点、[头部姿势](#head-pose-attribute)。 此外，检测操作也可返回人脸。

有关详细信息，请参阅参考文档：[Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)（人脸 - 检测）。

#### <a name="face-api"></a>人脸 API

人脸 API 是基于云的 API，可提供用于人脸检测和识别的最先进的算法。 人脸 API 的主要功能可分为两类：通过属性实现的人脸检测和人脸[识别](#recognition)。

有关详细信息，请参阅参考文档：[人脸 API 概述](./Overview.md)、[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)、[人脸 - 查找相似](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)、[人脸 - 分组](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238)、[人脸 - 识别](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)、[人脸 - 验证](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)。

#### <a name="face-attributesfacial-attributes"></a>人脸属性/面部属性

请参阅[属性](#attributes)。

#### <a name="face-id"></a>人脸 ID

人脸 ID 派生自检测结果，其中的字符串表示[人脸 API](#face-api) 中的[人脸](#face)。

有关详细信息，请参阅参考文档：[Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)（人脸 - 检测）。

#### <a name="face-landmarksfacial-landmarks"></a>人脸特征点/面部特征点

检测结果中的特征点为可选；它们是语义化的面部点，例如眼睛、鼻子和嘴巴（如下图所示）。 可使用布尔值 returnFaceLandmarks 通过检测请求来控制特征点。 如果 returnFaceLandmarks 设置为 true，则返回的人脸具有特征点属性。

有关详细信息，请参阅参考文档：[Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)（人脸 - 检测）。

![HowToDetectFace](./Images/landmarks.1.jpg)

#### <a name="face-rectangle"></a>人脸矩形

人脸矩形派生自检测结果，它是图像中以像素为单位的竖向矩形（左侧，顶部，宽度，高度）。 除宽度和高度之外，[人脸](#face)的左上角（左侧、顶部）分别表示 x 和 y 轴的人脸大小。

有关详细信息，请参阅参考文档：[Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)（人脸 - 检测）。

#### <a name="facial-hair-attribute"></a>面部毛发（属性）

面部毛发是一个用于描述识别到的人脸的面部毛发长度的[属性](#attributes)。 检测结果中的面部毛发属性为可选，且可使用 returnFaceAttributes 通过检测请求来控制该属性。 如果 returnFaceAttributes 中包含“facialHair”，则返回的人脸具有面部毛发属性。

有关详细信息，请参阅参考文档：[Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)（人脸 - 检测）。

#### <a name="facelist"></a>FaceList

FaceList 是 [PersistedFace](#persistedface) 的集合，是[查找相似](#find-similar)的单位。 FaceList 包含 [FaceList ID](#facelist-id) 和其他属性（例如，姓名和用户数据）。

有关详细信息，请参阅参考文档：[FaceList - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b)、[FaceList - 获取](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c)。

#### <a name="facelist-id"></a>FaceList ID

FaceList ID 是用户提供的字符串，用作 [FaceList](#facelist) 的标识符。 FaceList ID 在订阅中必须是唯一的。

有关详细信息，请参阅参考文档：[FaceList - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b)、[FaceList - 获取](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c)。

#### <a name="find-similar"></a>查找相似

此 API 可根据人脸集合搜索/查询相似人脸。 在请求中查询人脸和人脸集合表示为 [人脸 ID](#face-id) 或 [FceList ID](#facelist-id)/[LargeFaceList ID](#largefacelist-id)。 返回的结果是搜索到的相似人脸，由[人脸 ID](#face-id) 或 PersistedFace ID 表示。

有关详细信息，请参阅参考文档：[人脸 - 查找相似](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)、[LargeFaceList - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)、[FaceList - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b)。

## <a name="g"></a>G

#### <a name="gender-attribute"></a>性别（属性）

性别是一个用于描述识别到的人脸的性别的[属性](#attributes)。 检测结果中的性别属性为可选，且可使用 returnFaceAttributes 通过检测请求来控制该属性。 如果 returnFaceAttributes 中包含“gender”，则返回的人脸具有性别属性。

有关详细信息，请参阅参考文档：[Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)（人脸 - 检测）。

#### <a name="grouping"></a>分组

人脸分组是指根据面部相似性对人脸集合进行分组。 人脸集合由请求中的人脸 ID 集合指示。 分组后，相似的人脸被分为各种[组](#groups)，而不相似的人脸则被合并为一个混乱组。 分组结果中最多只有一个[混乱组](#messy-group)。

有关详细信息，请参阅参考文档：[Face - Group](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238)（人脸 - 分组）。

#### <a name="groups"></a>组

组派生自[分组](#grouping)结果。 每个组都包含一组相似的人脸，其中人脸由[人脸 ID](#face-id) 表示。

有关详细信息，请参阅参考文档：[Face - Group](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238)（人脸 - 分组）。

## <a name="h"></a>H

#### <a name="head-pose-attribute"></a>头部姿势（属性）

头部姿势是一个[属性](#attributes)，可根据头部的转动、俯仰和偏转角度以三维空间表示人脸方向，如下图所示。 转动和偏转值的范围是 [-180、180] 和 [-90、90] 度。 在当前版本中，检测返回的俯仰值始终为 0。 检测结果中的头部姿势属性为可选，且可使用 returnFaceAttributes 参数通过检测请求来控制该属性。 如果 returnFaceAttributes 参数包含“headPose”，则返回的人脸具有头部姿势属性。

有关详细信息，请参阅参考文档：[Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)（人脸 - 检测）。

![GlossaryHeadPose](./Images/headpose.1.jpg)

## <a name="i"></a>I

#### <a name="identification"></a>识别

识别是指从 LargePersonGroup/PersonGroup 中识别一张或多张人脸。
[PersonGroup](#persongroup)/[ LargePersonGroup](#largepersongroup) 是[人员](#person)的集合。
人脸和 LargePersonGroup/PersonGroup 分别由请求中的 [人脸 ID](#face-id) 和 [LargePersonGroup ID](#largepersongroup-id)/[PersonGroup ID](#persongroup-id) 表示。
识别结果是[候选对象](#candidate)，由带有置信度的[人员](#person)表示。
会分别考虑输入中的多张人脸，每张人脸都具有其自己的识别结果。

> [!NOTE]
> 在识别前，应成功训练 LargePersonGroup/PersonGroup。 如果未训练 LargePersonGroup/PersonGroup，或者训练[状态](#status-train)未显示为“成功”（即“正在运行”、“失败”或“超时”），则请求响应为 400。
> 

有关详细信息，请参阅参考文档：[人脸 - 识别](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)、[LargePersonGroup 人员 - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40)、[LargePersonGroup - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)、[LargePersonGroup - 训练](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4)、[PersonGroup 人员 - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)、[PersonGroup - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)、[PersonGroup - 训练](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249)。

#### <a name="isidentical"></a>IsIdentical

IsIdentical 是[验证](#verification)结果的布尔字段，表示两个人脸是否属于同一人。

有关详细信息，请参阅参考文档：[Face - Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)（人脸 - 验证）。

## <a name="l"></a>L

#### <a name="landmarks"></a>特征点

请参阅人脸特征点。

#### <a name="largefacelist"></a>LargeFaceList

LargeFaceList 是 [PersistedFace](#persistedface) 的集合，是[查找相似](#find-similar)的单位。 LargeFaceList 包含 [LargeFaceList ID](#largefacelist-id) 和其他属性（例如，姓名和用户数据）。

有关详细信息，请参阅参考文档：[LargeFaceList - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)、[LargeFaceList - 获取](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a15827cd2de3616c086f2ce)、[LargeFaceList - 列出人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158db4d2de3616c086f2d6)。

#### <a name="largefacelist-id"></a>LargeFaceList ID

LargeFaceList ID 是用户提供的字符串，用作 [LargeFaceList](#largefacelist) 的标识符。 LargeFaceList ID 在订阅中必须是唯一的。

有关详细信息，请参阅参考文档：[LargeFaceList - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)、[LargeFaceList - 获取](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a15827cd2de3616c086f2ce)。

#### <a name="largepersongroup"></a>LargePersonGroup

LargePersonGroup 是[人员](#person)的集合，是[识别](#identification)的单位。 LargePersonGroup 包含 [LargePersonGroup ID](#largepersongroup-id) 和其他属性（例如，姓名和用户数据）。

有关详细信息，请参阅参考文档：[LargePersonGroup - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)、[LargePersonGroup - 获取](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e)、[LargePersonGroup 人员 - 列出](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adda06ac60f11b48b5aa1)。

#### <a name="largepersongroup-id"></a>LargePersonGroup ID

LargePersonGroup ID 是用户提供的字符串，用作 [LargePersonGroup](#largepersongroup) 的标识符。 LargePersonGroup ID 在订阅中必须是唯一的。

有关详细信息，请参阅参考文档：[LargePersonGroup - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)、[LargePersonGroup - 获取](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e)。

## <a name="m"></a>M

#### <a name="messy-group"></a>混乱组

混乱组派生自[分组](#grouping)结果；其中包含各不相似的人脸。 混乱组中的每张脸都由[人脸 ID](#face-id) 表示。

有关详细信息，请参阅参考文档：[Face - Group](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238)（人脸 - 分组）。

## <a name="n"></a>N

#### <a name="name-person"></a>姓名（人员）

名称是用于[人员](#person)的描述性字符串，便于用户记忆。 与[人员 ID](#person-id) 不同，组中可存在重复人员姓名。

有关详细信息，请参阅参考文档：[LargePersonGroup Person - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40)、[LargePersonGroup 人员 - 获取](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0)、[PersonGroup 人员 - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)、[PersonGroup 人员 - 获取](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f)。

#### <a name="name-largepersongrouppersongroup"></a>名称 (LargePersonGroup/PersonGroup)

名称也是用于 [LargePersonGroup](#largepersongroup)/[PersonGroup](#persongroup) 的描述性字符串，便于用户记忆。 与 [LargePersonGroup ID](#largepersongroup-id)/[PersonGroup ID](#persongroup-id) 不同，订阅中可存在重复 LargePersonGroups/PersonGroups 名称。

有关详细信息，请参阅参考文档：[LargePersonGroup - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)、[LargePersonGroup - 获取](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e)、[PersonGroup - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)、[PersonGroup - 获取](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246)。

## <a name="p"></a>P

#### <a name="persistedface"></a>PersistedFace

PersistedFace 是 Face API 中的数据结构。 附带了 PersistedFace [PersistedFace ID](#persisted-face-id)和其他属性，如名称和用户数据。

有关详细信息，请参阅参考文档：[LargeFaceList - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)、[FaceList - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)、[LargePersonGroup 人员 - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)、[PersonGroup 人人员 - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)。

#### <a name="persisted-face-id"></a>持久化的人脸 ID

保存人脸 ID 时生成[PersistedFace](#persistedface)已成功创建。 会创建一个字符串来表示 [人脸 API](#face-api) 中的此人脸。

有关详细信息，请参阅参考文档：[LargeFaceList - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)、[FaceList - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)、[LargePersonGroup 人员 - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)、[PersonGroup 人人员 - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)。

#### <a name="person"></a>人员

人员是人脸 API 中托管的数据结构。 人员包含[人员 ID](#person-id) 和其他属性（例如，姓名、[PersistedFace](#persistedface)集合以及用户数据）。

有关详细信息，请参阅参考文档：[LargePersonGroup Person - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40)、[LargePersonGroup 人员 - 获取](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0)、[PersonGroup 人员 - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)、[PersonGroup 人员 - 获取](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f)。

#### <a name="person-id"></a>人员 ID

成功创建[人员](#person)时会生成人员 ID。 会创建一个字符串来表示[人员](#face-api)中的此人脸。

有关详细信息，请参阅参考文档：[LargePersonGroup Person - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40)、[LargePersonGroup 人员 - 获取](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0)、[PersonGroup 人员 - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)、[PersonGroup 人员 - 获取](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f)。

#### <a name="persongroup"></a>PersonGroup

PersonGroup 是[人员](#person)的集合，是[识别](#identification)的单位。 PersonGroup 包含 [PersonGroup ID](#persongroup-id) 和其他属性（例如，姓名和用户数据）。

有关详细信息，请参阅参考文档：[PersonGroup - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)、[PersonGroup - 获取](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246)、[PersonGroup 人员 - 列出](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241)。

#### <a name="persongroup-id"></a>PersonGroup ID

PersonGroup ID 是用户提供的字符串，用作 [PersonGroup](#persongroup) 的标识符。 该组 ID 在订阅中必须是唯一的。

有关详细信息，请参阅参考文档：[PersonGroup - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)、[PersonGroup - 获取](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246)。

#### <a name="pose-attribute"></a>姿势（属性）

请参阅[头部姿势](#head-pose-attribute)。

## <a name="r"></a>R

#### <a name="recognition"></a>识别

识别是人脸技术的一个热门应用领域，例如[查找相似](#find-similar)、[分组](#grouping)、[识别](#identification)、[验证两个人脸是否相同](#verification)。

有关详细信息，请参阅参考文档：[人脸 - 查找相似](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)、[人脸 - 分组](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238)、[人脸 - 识别](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)、[人脸 - 验证](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)。

#### <a name="rectangle-face"></a>矩形（人脸）

请参阅[人脸特矩形](#face-rectangle)。

## <a name="s"></a>S

#### <a name="similar-face-searching"></a>相似人脸搜索

请参阅[查找相似](#find-similar)。

#### <a name="smile-attribute"></a>笑容（属性）

笑容是一个用于描述识别到的人脸的微笑情况的[属性](#attributes)。 检测结果中的笑容属性为可选，且可使用 returnFaceAttributes 通过检测请求来控制该属性。 如果 returnFaceAttributes 中包含“smile”，则返回的人脸具有笑容属性。

有关详细信息，请参阅参考文档：[Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)（人脸 - 检测）。

#### <a name="snapshot"></a>快照

快照是特定人脸数据类型的临时远程存储。 它可用作一种剪贴板，用于将数据从一个订阅复制到另一个订阅。 用户先“拍摄”来源订阅中数据的快照，再将快照“应用”到目标订阅中的新数据对象。 

有关更多详细信息，请参阅[人脸迁移指南](./face-api-how-to-topics/how-to-migrate-face-data.md)和[快照 - 拍摄](/rest/api/cognitiveservices/face/snapshot/take)以及[快照 - 应用](/rest/api/cognitiveservices/face/snapshot/apply)参考文档 (REST)。

#### <a name="status-train"></a>状态（训练）

状态是一个字符串，用于描述训练 LargeFaceList/LargePersonGroups/PersonGroups 的过程，包括“未开始”、“正在运行”、“成功”、“失败”。

有关详细信息，请参阅参考文档：[LargeFaceList - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158422d2de3616c086f2d1)（LargeFaceList - 训练）、[LargePersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4)（LargePersonGroup - 训练）、[PersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249)（PersonGroup - 训练）。

#### <a name="subscription-key"></a>订阅密钥

订阅密钥是一个字符串，需要指定为查询字符串参数，方可调用任何人脸 API。 登录 Microsoft 认知服务门户后，可以在“我的订阅”页中找到订阅密钥。 每个订阅都关联有两个密钥：一个主密钥和一个辅助密钥。 二者同样都可用于调用 API。 需确保订阅密钥的安全，且可随时从“我的订阅”页重新生成订阅密钥。

## <a name="t"></a>T

#### <a name="train-largefacelistlargepersongrouppersongroup"></a>训练 (LargeFaceList/LargePersonGroup/PersonGroup)

此 API 可用于预处理 [LargeFaceList](#largefacelist)/[LargePersonGroup](#largepersongroup)/[PersonGroup](#persongroup)，以确保[查找相似](#find-similar)/[识别](#identification)的性能。 如果未进行训练，或[训练状态](#status-train)未显示为成功，则此 PersonGroup 的识别会失败。

有关详细信息，请参阅参考文档：[LargeFaceList - 训练](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158422d2de3616c086f2d1)、[LargePersonGroup - 训练](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4)、[PersonGroup - 训练](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249)、[人脸 - 识别](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)。

## <a name="u"></a>U

#### <a name="userdatauser-data"></a>UserData/用户数据

用户数据是与[人员](#person)和 [PersonGroup](#persongroup)/[LargePersonGroup](#largepersongroup)相关联的额外信息。 用户数据由用户设置，可使系统能更轻易地使用、理解和记忆数据。

有关详细信息，请参阅参考文档：[LargePersonGroup - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)[LargePersonGroup - 更新](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acfc83a7b9412a4d53f3f)、[LargePersonGroup 人员 - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40)、[LargePersonGroup 人员 - 更新](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ade043a7b9412a4d53f41)、[PersonGroup - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)、[PersonGroup - 更新](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524a)、[PersonGroup 人员 - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)、[PersonGroup 人员 - 更新](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395242)。

## <a name="v"></a>V

#### <a name="verification"></a>验证

此 API 用于验证两个人脸是否相同。 两个人脸在请求中均由人脸 ID 表示。 验证结果包含一个布尔字段 (isIdentical)（如果为 true，则表示相同相同）和一个表示置信度的数值（[置信度](#confidence)）。

有关详细信息，请参阅参考文档：[Face - Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)（人脸 - 验证）。
