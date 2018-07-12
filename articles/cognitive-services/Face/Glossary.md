---
title: 人脸 API 服务术语表 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 术语表解释了使用人脸 API 时可能遇到的术语。
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: ec3068c013be9f2fa4ff34b1c24596e46e7c5d05
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365576"
---
# <a name="glossary"></a>术语表

## <a name="a"></a>A

#### <a name="Attributes"></a>属性

[检测](#Detection-Face-Detection)结果中的属性是可选的，例如[年龄](#Age-Attribute)、[性别](#Gender-Attribute)[头部姿势](#Head-Pose-Attribute)[面部毛发](#Facial-Hair-Attribute)、[微笑](#Smile-Attribute)。
可指定查询参数 returnFaceAttributes，从[检测](#Detection-Face-Detection) API 获取这些属性。 属性可提供除[人脸 ID](#Face-ID) 和[矩形](#Face-Rectangle)外的关于所选[人脸](#Face)的其他信息。

如需更多详细信息，请参阅[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)指南。

#### <a name="Age-Attribute"></a>年龄（属性）

年龄是一个描述特定人脸年龄的[属性](#Attributes)。 [检测](#Detection-Face-Detection)结果中的年龄属性为可选，且可指定 returnFaceAttributes 参数通过[检测](#Detection-Face-Detection)请求来控制该属性。

如需更多详细信息，请参阅[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)指南。

## <a name="b"></a>B

## <a name="c"></a>C

#### <a name="Candidate"></a>候选对象

候选对象基本就是[识别](#Identification)结果（例如，识别出的人员和检测置信度）。 候选对象由[PersonID](#Person-ID) 和[置信度](#Confidence)表示，表明识别出的人员置信度较高。

如需更多详细信息，请参阅[人脸 - 识别](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)指南。

#### <a name="Confidence"></a>置信度

置信度是一种度量值，以数值表明[人脸](#Face)或[人员](#Person)的相似度，用于[识别](#Identification)和[验证](#Verification)过程，指明搜索、识别和验证结果的相似度。

如需更多详细信息，请参阅以下指南：[人脸 - 查找相似](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)、[人脸 - 识别](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)、[人脸 - 验证](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)。

## <a name="d"></a>D

#### <a name="Detection-Face-Detection"> </a>检测/人脸检测

人脸检测是一种在图像中查找人脸的操作。 用户可在请求中上传图像或指定图像 URL。 操作会返回检测到的人脸，其中包含[人脸 ID](#Face-ID)，表示人脸 API 中的唯一标识。 以像素为单位的矩形表示图像中人脸的位置，以及各个人脸的可选[属性](#Attributes)，例如[年龄](#Age-Attribute)[性别](#Gender-Attribute)[头部姿势](#Head-Pose-Attribute)、[面部毛发](#Facial-Hair-Attribute)和[笑容](#Smile-Attribute)。

如需更多详细信息，请参阅[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)指南。

## <a name="e"></a>E

## <a name="f"></a>F

#### <a name="Face"></a>人脸

人脸是用于人脸 API 派生的结果的统一术语，该 API 与检测到的人脸相关。 总的来说，人脸由统一标识（[人脸 ID](#Face-ID)）、图像中的指定区域（[人脸矩形](#Face-Rectangle)）和与人脸相关的其他[属性](#Face-Attributes-Facial-Attributes)表示，例如[年龄](#Age-Attribute)[性别](#Gender-Attribute)、[特征点](#Face-Landmarks-Facial-Landmarks)和[头部姿势](#Head-Pose-Attribute)。 此外，[检测](#Detection-Face-Detection)操作也可返回人脸。

如需更多详细信息，请参阅[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)指南。

#### <a name="Face-API"></a>人脸 API

人脸 API 是基于云的 API，可提供用于人脸检测和识别的最先进的算法。 人脸 API 的主要功能可分为两类：通过[属性](#Face-Attributes-Facial-Attributes)实现的人脸[检测](#Detection-Face-Detection)和人脸[识别](#Recognition)。

如需更多详细信息，请参阅以下指南：[人脸 API 概述](./Overview.md)、[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)、[人脸 - 查找相似](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)、[人脸 - 分组](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238)、[人脸 - 识别](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)、[人脸 - 验证](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)。

#### <a name="Face-Attributes-Facial-Attributes"></a>人脸属性/面部属性

请参阅[属性](#Attributes)。

#### <a name="Face-ID"></a>人脸 ID

人脸 ID 派生自[检测](#Detection-Face-Detection)结果，其中的字符串表示[人脸 API](#Face-API) 中的[人脸](#Face)。

如需更多详细信息，请参阅[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)指南。

#### <a name="Face-Landmarks-Facial-Landmarks"></a>人脸特征点/面部特征点

[检测](#Detection-Face-Detection)结果中的特征点为可选；它们是语义化的面部点，例如眼睛、鼻子和嘴巴（如下图所示）。 可使用布尔值 returnFaceLandmarks 通过[检测](#Detection-Face-Detection)请求来控制特征点。 如果 returnFaceLandmarks 设置为 true，则返回的人脸具有特征点属性。

如需更多详细信息，请参阅[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)指南。

![HowToDetectFace](./Images/landmarks.1.jpg)

#### <a name="Face-Rectangle"></a>人脸矩形

人脸矩形派生自[检测](#Detection-Face-Detection)结果，它是图像中以像素为单位的竖向矩形（左侧，顶部，宽度，高度）。 除宽度和高度之外，[人脸](#Face)的左上角（左侧、顶部）分别表示 x 和 y 轴的人脸大小。

如需更多详细信息，请参阅[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)指南。

#### <a name="Facial-Hair-Attribute"></a>面部毛发（属性）

面部毛发是一个用于描述识别到的人脸的面部毛发长度的[属性](#Attributes)。 [检测](#Detection-Face-Detection)结果中的面部毛发属性为可选，且可使用 returnFaceAttributes 通过[检测](#Detection-Face-Detection)请求来控制该属性。 如果 returnFaceAttributes 中包含“facialHair”，则返回的人脸具有面部毛发属性。

如需更多详细信息，请参阅[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)指南。

#### <a name="FaceList"></a>FaceList

FaceList 是 [PersistedFace](#PersistedFace) 的集合，是[查找相似](#Find-Similar)的单位。 FaceList 包含 [FaceList ID](#FaceList-ID) 和其他属性，例如[姓名](#Name)和[用户数据](#UserData-User-Data)。

如需更多详细信息，请参阅以下指南：[ FaceList - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b)[FaceList - 获取](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c)。

#### <a name="FaceList-ID"></a>FaceList ID

FaceList ID 是用户提供的字符串，用作 [FaceList](#FaceList) 的标识符。 FaceList ID 在订阅中必须是唯一的。

如需更多详细信息，请参阅以下指南：[FaceList - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b)、[FaceList - 获取](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c)。

#### <a name="Find-Similar"></a>查找相似

此 API 可根据人脸集合搜索/查询相似人脸。 在请求中查询人脸和人脸集合表示为 [人脸 ID](#Face-ID) 或 [FceList ID](#FaceList-ID)/[LargeFaceList ID](#LargeFaceList-ID)。 返回的结果是搜索到的相似人脸，由[人脸 ID](#Face-ID) 或 [PersistedFace ID](#PersistedFace-ID) 表示。

如需更多详细信息，请参阅以下指南：[人脸 - 查找相似](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)、[LargeFaceList - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)、[FaceList - 获取](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b)。

## <a name="g"></a>G

#### <a name="Gender-Attribute"></a>性别（属性）

性别是一个用于描述识别到的人脸的性别的[属性](#Attributes)。 [检测](#Detection-Face-Detection)结果中的性别属性为可选，且可使用 returnFaceAttributes 通过[检测](#Detection-Face-Detection)请求来控制该属性。 如果 returnFaceAttributes 中包含“gender”，则返回的人脸具有性别属性。

如需更多详细信息，请参阅[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)指南。

#### <a name="Grouping"></a>分组

人脸分组是指根据面部相似性对人脸集合进行分组。 人脸集合由请求中的人脸 ID 集合指示。 分组后，相似的人脸被分为各种[组](#Groups)，而不相似的人脸则被合并为一个混乱组。 分组结果中最多只有一个[混乱组](#Messy-Group)。

如需更多详细信息，请参阅[人脸 - 分组](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238)指南。

#### <a name="Groups"></a>组

组派生自[分组](#Grouping)结果。 每个组都包含一组相似的人脸，其中人脸由[人脸 ID](#Face-ID) 表示。

如需更多详细信息，请参阅[人脸 - 分组](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238)指南。

## <a name="h"></a>H

#### <a name="Head-Pose-Attribute"></a>头部姿势（属性）

头部姿势是一个[属性](#Attributes)，可根据头部的转动、俯仰和偏转角度以三维空间表示人脸方向，如下图所示。 转动和偏转值的范围是 [-180、180] 和 [-90、90] 度。 在当前版本中，检测返回的俯仰值始终为 0。 [检测](#Detection-Face-Detection)结果中的头部姿势属性为可选，且可使用 returnFaceAttributes 参数通过[检测](#Detection-Face-Detection)请求来控制该属性。 如果 returnFaceAttributes 参数包含“headPose”，则返回的人脸具有头部姿势属性。

如需更多详细信息，请参阅[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)指南。

![GlossaryHeadPose](./Images/headpose.1.jpg)

## <a name="i"></a>I

#### <a name="Identification"></a>识别

识别是指从 LargePersonGroup/PersonGroup 中识别一张或多张人脸。
[PersonGroup](#PersonGroup)/[ LargePersonGroup](#LargePersonGroup) 是[人员](#Person)的集合。
人脸和 LargePersonGroup/PersonGroup 分别由请求中的 [人脸 ID](#Face-ID) 和 [LargePersonGroup ID](#LargePersonGroup-ID)/[PersonGroup ID](#PersonGroup-ID) 表示。
识别结果是[候选对象](#Candidate)，由带有置信度的[人员](#Person)表示。
会分别考虑输入中的多张人脸，每张人脸都具有其自己的识别结果。

> [!NOTE]
> 在识别前，应成功训练 LargePersonGroup/PersonGroup。 如果未训练 LargePersonGroup/PersonGroup，或者训练[状态](#Status-Train)未显示为“成功”（即“正在运行”、“失败”或“超时”），则请求响应为 400。
> 

如需更多详细信息，请参阅以下指南：[人脸 - 识别](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)、[LargePersonGroup 人员 - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40)、[LargePersonGroup - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)、[LargePersonGroup - 训练](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4)、[PersonGroup 人员 - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)、[PersonGroup - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)、[PersonGroup - 训练](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249)。

#### <a name="Is-Identical"></a>IsIdentical

IsIdentical 是[验证](#Verification)结果的布尔字段，表示两个人脸是否属于同一人。

如需更多详细信息，请参阅[人脸 - 验证](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)指南。

## <a name="j"></a>J

## <a name="k"></a>K

## <a name="l"></a>L

#### <a name="landmarks"></a>特征点

请参阅[人脸特征点](#Face-Landmarks-Facial-Landmarks)。

#### <a name="LargeFaceList"></a>LargeFaceList

LargeFaceList 是 [PersistedFace](#PersistedFace) 的集合，是[查找相似](#Find-Similar)的单位。 LargeFaceList 包含 [LargeFaceList ID](#LargeFaceList-ID) 和其他属性，例如[姓名](#Name)和[用户数据](#UserData-User-Data)。

如需更多详细信息，请参阅以下指南：[LargeFaceList - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)、[LargeFaceList - 获取](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a15827cd2de3616c086f2ce)[LargeFaceList - 列出人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158db4d2de3616c086f2d6)。

#### <a name="LargeFaceList-ID"></a>LargeFaceList ID

LargeFaceList ID 是用户提供的字符串，用作 [LargeFaceList](#LargeFaceList) 的标识符。 LargeFaceList ID 在订阅中必须是唯一的。

如需更多详细信息，请参阅以下指南：[LargeFaceList - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)、[LargeFaceList - 获取](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a15827cd2de3616c086f2ce)。

#### <a name="LargePersonGroup"></a>LargePersonGroup

LargePersonGroup 是[人员](#Person)的集合，是[识别](#Identification)的单位。 LargePersonGroup 包含 [LargePersonGroup ID](#LargePersonGroup-ID) 和其他属性，例如[姓名](#Name)和[用户数据](#UserData-User-Data)。

如需更多详细信息，请参阅以下指南：[LargePersonGroup - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)、[LargePersonGroup - 获取](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e)、[LargePersonGroup 人员 - 列表](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adda06ac60f11b48b5aa1)。

#### <a name="LargePersonGroup-ID"></a>LargePersonGroup ID

LargePersonGroup ID 是用户提供的字符串，用作 [LargePersonGroup](#LargePersonGroup) 的标识符。 LargePersonGroup ID 在订阅中必须是唯一的。

如需更多详细信息，请参阅以下指南：[LargePersonGroup - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)、[LargePersonGroup - 获取](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e)。

## <a name="m"></a>M

#### <a name="Messy-Group"></a>混乱组

混乱组派生自[分组](#Grouping)结果；其中包含各不相似的人脸。 混乱组中的每张脸都由[人脸 ID](#Face-ID) 表示。

如需更多详细信息，请参阅[人脸 - 分组](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238)指南。

## <a name="n"></a>N

#### <a name="name-person"></a>姓名（人员）

姓名是[人员](#Person)的描述性用户友好字符串。 与[人员 ID](#Person-ID) 不同，组中可存在重复人员姓名。

如需更多详细信息，请参阅以下指南：[LargePersonGroup 人员 - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40)[LargePersonGroup 人员 - 获取](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0)、[PersonGroup 人员 - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)[PersonGroup 人员 - 获取](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f)。

#### <a name="Name"></a>名称 (LargePersonGroup/PersonGroup)

名称也是 [LargePersonGroup](#LargePersonGroup)/[PersonGroup](#PersonGroup)的描述性用户友好字符串。 与 [LargePersonGroup ID](#LargePersonGroup-ID)/[PersonGroup ID](#PersonGroup-ID) 不同，订阅中可存在重复 LargePersonGroups/PersonGroups 名称。

如需更多详细信息，请参阅以下指南：[LargePersonGroup - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)、[LargePersonGroup - 获取](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e)、[PersonGroup - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)、[PersonGroup - 获取](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246)。

## <a name="o"></a>O

## <a name="p"></a>P

#### <a name="PersistedFace"></a>PersistedFace

PersistedFace 是 Face API 中的数据结构。 PersistedFace 包含 [PersistedFace ID](#PersistedFace-ID) 和其他属性，例如[姓名](#Name)和[用户数据](#UserData-User-Data)。

如需更多详细信息，请参阅以下指南：[LargeFaceList - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)、[FaceList - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)、[LargePersonGroup 人员 - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)、[PersonGroup 人人员 - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)。

#### <a name="Person-ID"></a>人员 ID

成功创建 [PersistedFace](#PersistedFace)时会生成人员 ID。 会创建一个字符串来表示 [人脸 API](#Face-API) 中的此人脸。

如需更多详细信息，请参阅以下指南：[LargeFaceList - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)、[FaceList - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)、[LargePersonGroup 人员 - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)、[PersonGroup 人人员 - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)。

#### <a name="Person"></a>人员

人员是人脸 API 中托管的数据结构。 人员包含[人员 ID](#Person-ID) 和其他属性，例如[姓名](#Name)、[PersistedFace](#PersistedFace)集合以及[用户数据](#UserData-User-Data)。

如需更多详细信息，请参阅以下指南：[LargePersonGroup 人员 - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40)[LargePersonGroup 人员 - 获取](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0)、[PersonGroup 人员 - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)[PersonGroup 人员 - 获取](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f)。

#### <a name="Person-ID"></a>人员 ID

成功创建[人员](#Person)时会生成人员 ID。 会创建一个字符串来表示[人员](#Face-API)中的此人脸。

如需更多详细信息，请参阅以下指南：[LargePersonGroup 人员 - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40)[LargePersonGroup 人员 - 获取](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0)、[PersonGroup 人员 - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)[PersonGroup 人员 - 获取](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f)。

#### <a name="PersonGroup"></a>PersonGroup

PersonGroup 是[人员](#Person)的集合，是[识别](#Identification)的单位。 PersonGroup 包含 [PersonGroup ID](#PersonGroup-ID) 和其他属性，例如[姓名](#Name)和[用户数据](#UserData-User-Data)。

如需更多详细信息，请参阅以下指南：[PersonGroup - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)、[PersonGroup - 获取](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246)、[PersonGroup 人员 - 列表](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241)。

#### <a name="PersonGroup-ID"></a>PersonGroup ID

PersonGroup ID 是用户提供的字符串，用作 [PersonGroup](#PersonGroup) 的标识符。 该组 ID 在订阅中必须是唯一的。

如需更多详细信息，请参阅以下指南：[PersonGroup - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)、[PersonGroup - 获取](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246)。

#### <a name="pose-attribute"></a>姿势（属性）

请参阅[头部姿势](#Head-Pose-Attribute)。

## <a name="q"></a>Q

## <a name="r"></a>R

#### <a name="Recognition"></a>识别

识别是人脸技术的一个热门应用领域，例如[查找相似](#Find-Similar)、[分组](#Grouping)、[识别](#Identification)、[验证两个人脸是否相同](#Verification)。

如需更多详细信息，请参阅以下指南：[人脸 - 查找相似](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)、[人脸 - 分组](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238)、[人脸 - 识别](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)、[人脸 - 验证](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)。

#### <a name="rectangle-face"></a>矩形（人脸）

请参阅[人脸特矩形](#Face-Rectangle)。

## <a name="s"></a>S

#### <a name="Smile-Attribute"></a>笑容（属性）

笑容是一个用于描述识别到的人脸的微笑情况的[属性](#Attributes)。 [检测](#Detection-Face-Detection)结果中的笑容属性为可选，且可使用 returnFaceAttributes 通过[检测](#Detection-Face-Detection)请求来控制该属性。 如果 returnFaceAttributes 中包含“smile”，则返回的人脸具有笑容属性。

如需更多详细信息，请参阅[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)指南。

#### <a name="similar-face-searching"></a>相似人脸搜索

请参阅[查找相似](#Find-Similar)。

#### <a name="Status-Train"></a>状态（训练）

状态是一个字符串，用于描述[训练 LargeFaceList/LargePersonGroups/PersonGroups ](#Train) 的过程，包括“未开始”、“正在运行”、“成功”、“失败”。

如需更多详细信息，请参阅以下指南：[LargeFaceList - 训练](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158422d2de3616c086f2d1)、[LargePersonGroup - 训练](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4)、[PersonGroup - 训练](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249)。

#### <a name="subscription-key"></a>订阅密钥

订阅密钥是一个字符串，需要指定为查询字符串参数，方可调用任何人脸 API。 登录 Microsoft 认知服务门户后，可以在“我的订阅”页中找到订阅密钥。 每个订阅都关联有两个密钥：一个主密钥和一个辅助密钥。 二者同样都可用于调用 API。 需确保订阅密钥的安全，且可随时从“我的订阅”页重新生成订阅密钥。

## <a name="t"></a>T

#### <a name="Train"></a>训练 (LargeFaceList/LargePersonGroup/PersonGroup)

此 API 可用于预处理 [LargeFaceList](#LargeFaceList)/[LargePersonGroup](#LargePersonGroup)/[PersonGroup](#PersonGroup)，以确保[查找相似](#Find-Similar)/[识别](#Identification)的性能。 如果未进行训练，或[训练状态](#Status-Train)未显示为成功，则此 PersonGroup 的识别会失败。

如需更多详细信息，请参阅以下指南：[LargeFaceList - 训练](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158422d2de3616c086f2d1)、[LargePersonGroup - 训练](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4)、[PersonGroup - 训练](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249)、[人脸 - 识别](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)。

## <a name="u"></a>U

#### <a name="UserData-User-Data"></a>UserData/用户数据

用户数据是与[人员](#Person)和 [PersonGroup](#PersonGroup)/[LargePersonGroup](#LargePersonGroup)相关联的额外信息。 用户数据由用户设置，可使系统能更轻易地使用、理解和记忆数据。

如需更多详细信息，请参阅以下指南：[LargePersonGroup - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)[LargePersonGroup - 更新](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acfc83a7b9412a4d53f3f)、[LargePersonGroup 人员 - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40)、[LargePersonGroup 人员 - 更新](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ade043a7b9412a4d53f41)、[PersonGroup - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)、[PersonGroup - 更新](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524a)、[PersonGroup 人员 - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)、[PersonGroup 人员 - 更新](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395242)。

## <a name="v"></a>V

#### <a name="Verification"></a>验证

此 API 用于验证两个人脸是否相同。 两个人脸在请求中均由人脸 ID 表示。 验证结果包含一个布尔字段 ([isIdentical](#Is-Identical))（如果为 true，则表示相同相）和一个表示置信度的数值（[置信度](#Confidence)）。

如需更多详细信息，请参阅[人脸 - 验证](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)指南。

## <a name="w"></a>W

## <a name="x"></a>X

## <a name="y"></a>Y

## <a name="z"></a>Z
