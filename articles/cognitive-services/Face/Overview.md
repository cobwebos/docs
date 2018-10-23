---
title: 什么是人脸 API 服务？
titleSuffix: Azure Cognitive Services
description: 本主题介绍人脸 API 服务和相关的术语。
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: overview
ms.date: 10/11/2018
ms.author: sbowles
ms.openlocfilehash: 6c1e0d0a51bc01c581c05e7ce3215f5501b4be76
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2018
ms.locfileid: "49310347"
---
# <a name="what-is-the-face-api-service"></a>什么是人脸 API 服务？

人脸 API 服务是基于云的服务，其提供的算法可用于分析图像和视频中的人脸。 人脸 API 有两个主要功能：带属性的人脸检测和人脸识别。

## <a name="face-detection"></a>人脸检测

人脸 API 可以在一张图像中检测到至多 64 张人脸，并进行高精度人脸定位。 图像可以通过文件（字节流）或有效的 URL 来指定。

![概述 - 人脸检测](./Images/Face.detection.jpg)

指示人脸在图像中位置的人脸矩形（左边、顶边、宽度和高度）将随检测到的人脸一起返回。 人脸检测还可提取一系列人脸相关属性，例如姿势、性别、年龄、头部姿势、面部毛发和眼镜。 有关详细信息，请参阅[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)。

## <a name="face-recognition"></a>人脸识别

标识人脸的功能在很多领域很重要，其中包括安全、自然用户界面、图像内容分析和管理、移动应用和机器人。 人脸 API 服务提供四项人脸识别功能：人脸验证、查找相似人脸、人脸分组和人员标识。

### <a name="face-verification"></a>人脸验证

人脸验证针对检测到的两个人脸执行身份验证，或由一个检测到的人脸对一个人员对象执行身份验证。 如需更多详细信息，请参阅[人脸 - 验证](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)。

### <a name="finding-similar-faces"></a>查找相似人脸

提供检测到的目标人脸和一组要搜索的候选人脸，该服务即可查找与目标人脸最相似的一小组人脸。 支持两种工作模式，即 **matchFace** 和 **matchPerson**。 应用由[人脸 - 验证](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)所得的相同人员阈值后，**matchPerson** 模式会返回相似人脸。 **matchFace** 模式会忽略相同人员阈值，并返回最相似的候选人脸。 在以下示例中，列出了候选人脸。
![概述 - 人脸查找相似](./Images/FaceFindSimilar.Candidates.jpg) 查询人脸为此人脸。
![概述 - 人脸查找相似](./Images/FaceFindSimilar.QueryFace.jpg)

为了查找四张相似的人脸，**matchPerson** 模式会返回 (a) 和 (b)，它们与查询人脸描述同一个人。 **matchFace** 模式返回 (a)、(b)、(c)、(d)，恰好返回四个候选项（虽然某些选项的相似度低）。 有关详细信息，请参阅[人脸 - 查找相似](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)。

### <a name="face-grouping"></a>人脸分组

给定一组未知人脸，人脸分组 API 会基于相似性自动将其分为几组。 每个组是原始位置人脸集合的互不相交真子集，包含相似的人脸。 可以认为同一组中的所有人脸属于同一人。 有关详细信息，请参阅[人脸 - 分组](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238)。

### <a name="person-identification"></a>人员识别

人脸 API 可用于根据检测到的人脸和人员数据库标识人员。 请提前创建此数据库，可以随着时间推移对其进行编辑。

下图是名为“myfriends”的数据库的示例。 每个组可以包含至多 1,000,000/10,000 个不同的人员对象。 每个人员对象可以注册最多 248 张人脸。

![概述 - LargePersonGroup/PersonGroup](./Images/person.group.clare.jpg)

创建和训练数据库后，可以对组使用新检测到的人脸进行标识。 如果人脸被标识为组中的某一人员，则返回该人员对象。

有关人员标识的详细信息，请参阅以下 API 指南：

[人脸 - 标识](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)  
[PersonGroup - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)  
[PersonGroup 人员 - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)  
[PersonGroup - 训练](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249)  
[LargePersonGroup - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)  
[LargePersonGroup 人员 - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40)  
[LargePersonGroup - 训练](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4)  

#### <a name="face-storage-and-pricing"></a>人脸存储和定价

对人脸 API 使用 LargePersonGroup/PersonGroup 人员对象（[PersonGroup 人员 - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)/[LargePersonGroup 人员 - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)）或 LargeFaceLists/FaceLists（[FaceList - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)/[LargeFaceList - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)）来进行标识或相似度匹配时，人脸存储允许标准订阅存储额外的持久化人脸。 存储的图像按每 1000 张人脸 0.50 美元收费，每天以此费率按比例计费。 免费层订阅总人数限制为 1,000 人。

例如，如果你的帐户在上半月每天使用了 10,000 张持久性人脸，下半月未使用人脸，则仅对 10,000 张人脸存储的天数收取费用。 另外，如果某个月每天仅保留 1,000 张人脸且仅保留几个小时，每晚予以删除，那么每天仍将对保留的这 1,000 张人脸进行收费。

## <a name="sample-apps"></a>示例应用

看看这些使用人脸 API 的示例应用程序。

- [Microsoft 人脸 API：Windows 客户端库和示例](https://github.com/Microsoft/Cognitive-Face-Windows)
  - WPF 示例应用，演示人脸检测、分析和识别的几种方案。
- [FamilyNotes UWP 应用](https://github.com/Microsoft/Windows-appsample-familynotes)
  - 通用 Windows 平台 (UWP) 示例应用，通过家庭便笺共享方案演示语音、Cortana、墨迹和相机。
- [视频帧分析示例](https://github.com/microsoft/cognitive-samples-videoframeanalysis)
  - Win32 示例应用，演示使用人脸、计算机视觉和情感 API 准实时分析实时视频流。

## <a name="tutorials"></a>教程
以下教程演示了人脸 API 的基本功能和订阅流程：
- [面向 CSharp 的人脸 API 入门教程](Tutorials/FaceAPIinCSharpTutorial.md)
- [面向 Java for Android 的人脸 API 入门教程](Tutorials/FaceAPIinJavaForAndroidTutorial.md)
- [面向 Python 的人脸 API 入门教程](Tutorials/FaceAPIinPythonTutorial.md)

## <a name="next-steps"></a>后续步骤

尝试一个可实现简单人脸 API 方案的快速入门。
- [快速入门：使用 C# 检测图像中的人脸](quickstarts/csharp.md)（提供其他语言）
