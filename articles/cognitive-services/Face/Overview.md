---
title: 什么是人脸 API？
titleSuffix: Azure Cognitive Services
description: 了解如何使用人脸服务来检测和分析图像中的人脸。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: 4be33f781dec93fd9fe1b1846322672266cd7350
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606913"
---
# <a name="what-is-the-azure-face-api"></a>什么是 Azure 人脸 API？

Azure 认知服务人脸 API 提供用于检测、识别和分析图像中人脸的算法。 在许多不同的软件方案中，处理人脸信息的功能非常重要。 示例方案包括安全、自然用户界面、图像内容分析和管理、移动应用及机器人。

人脸 API 提供多个不同的函数。 以下各部分将概述每个函数。 请继续阅读以了解详细信息。

## <a name="face-detection"></a>人脸检测

人脸 API 可以检测图像中的人脸，并返回其位置的矩形坐标。 （可选）人脸检测可以提取一系列人脸相关的属性。 示例包括头部姿势、性别、年龄、情感、胡须和眼镜。

> [!NOTE]
> 也可以通过[计算机视觉 API](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) 使用人脸检测功能。 若要对人脸数据执行进一步的操作，请使用本文所述的人脸 API 服务。

![一位女士和一位男士的图像，在其面部绘制了矩形并显示了年龄和性别](./Images/Face.detection.jpg)

有关人脸检测的详细信息，请参阅[人脸检测](concepts/face-detection.md)概念文章。 另请参阅[检测 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) 参考文档。

## <a name="face-verification"></a>人脸验证

验证 API 针对检测到的两个人脸执行身份验证，或由一个检测到的人脸对一个人员对象执行身份验证。 实际上，它会评估两张脸是否属于同一个人。 此功能可能适用于安全方案。 有关详细信息，请参阅[人脸识别](concepts/face-recognition.md)概念指南或[验证 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) 参考文档。

## <a name="find-similar-faces"></a>查找相似人脸

查找相似人脸 API 会将某个目标人脸与一组候选人脸进行比较，以查找与目标人脸相似的一小组人脸。 支持两种工作模式：matchPerson 和 matchFace。 使用[验证 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) 针对同一人进行筛选后，matchPerson 模式会返回相似人脸。 matchFace 模式会忽略相同人员筛选器。 它返回相似候选人脸的列表，这些人脸不一定属于同一人。

以下示例显示了目标人脸：

![一位微笑的女士](./Images/FaceFindSimilar.QueryFace.jpg)

下面是候选人脸：

![五位微笑的人的图像。 图像 a 和 b 显示同一人。](./Images/FaceFindSimilar.Candidates.jpg)

为了查找四张相似人脸，matchPerson 模式会返回 a 和 b，其中显示与目标人脸相同的人。 matchFace 模式返回 a、b、c 和 d，恰好四个候选项，即使某些选项与目标不是同一人或者相似度低，也是如此。 有关详细信息，请参阅[人脸识别](concepts/face-recognition.md)概念指南或[查找相似人脸 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) 参考文档。

## <a name="face-grouping"></a>人脸分组

组 API 会基于相似性将未知人脸的集合分为几组。 每个组是原始人脸集合的互不相交真子集。 一个组中的所有人脸可能属于同一人。 一个人可能有多个不同的组。 组按其他因素（例如表情）区分。 有关详细信息，请参阅[人脸识别](concepts/face-recognition.md)概念指南或[分组 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) 参考文档。

## <a name="person-identification"></a>人员识别

识别 API 用于根据人员数据库识别检测到的人脸。 此功能可用于照片管理软件中的自动图像标记。 需提前创建数据库，以后可以不断地对其进行编辑。

下图显示名为 `"myfriends"` 的数据库的示例。 每个组最多可以包含 100 万个不同的 person 对象。 每个人员对象可以注册最多 248 张人脸。

![包含三个列（代表不同的人）的网格，每个列包含三行人脸图像](./Images/person.group.clare.jpg)

创建并训练数据库后，可以对组使用新检测到的人脸执行识别。 如果人脸被标识为组中的某一人员，则返回该人员对象。

有关人员识别的详细信息，请参阅[人脸识别](concepts/face-recognition.md)概念指南或[识别 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) 参考文档。

## <a name="use-containers"></a>使用容器

将标准化的 Docker 容器安装到靠近数据的位置后，[使用人脸容器](face-how-to-install-containers.md)来检测和识别人脸。

## <a name="sample-apps"></a>示例应用

以下示例应用程序显示了人脸 API 的多种用法：

- [Microsoft 人脸 API：Windows 客户端库和示例](https://github.com/Microsoft/Cognitive-Face-Windows)是一个 WPF 应用，演示人脸检测、分析和识别的多种方案。
- [FamilyNotes UWP 应用](https://github.com/Microsoft/Windows-appsample-familynotes)是一个通用 Windows 平台 (UWP) 应用，可以在家庭便笺共享方案中使用人脸识别、语音、Cortana、墨迹和相机。

## <a name="data-privacy-and-security"></a>数据隐私和安全性

与所有认知服务资源一样，使用人脸服务的开发人员必须了解 Microsoft 针对客户数据的政策。 有关详细信息，请参阅 Microsoft 信任中心内的[“认知服务”页](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)。

## <a name="next-steps"></a>后续步骤

遵循以下快速入门在代码中实现人脸检测方案：

- [快速入门：使用 .NET SDK 和 C# 检测图像中的人脸](quickstarts/csharp.md)。 此外还有其他语言的相关教程。
