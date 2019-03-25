---
title: 什么是人脸 API？
titleSuffix: Azure Cognitive Services
description: 了解如何使用人脸服务来检测和分析图像中的人脸。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 02/20/2019
ms.author: pafarley
ms.openlocfilehash: dcfb50c58f1205a5ab31c3fc6b9b22fdb503e4ec
ms.sourcegitcommit: 89b5e63945d0c325c1bf9e70ba3d9be6888da681
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57588952"
---
# <a name="what-is-the-azure-face-api"></a>什么是 Azure 人脸 API？

Azure 人脸 API 服务是一项认知服务，其提供的算法可用于检测、识别和分析图像中的人脸。 处理人脸信息的功能在许多不同的软件方案中很重要，这些方案包括安全性、自然用户界面、图像内容分析和管理、移动应用和机器人。

人脸 API 提供多项不同的功能，每一项在以下部分都进行了概述。 请继续阅读了解每一项功能的更多信息。

## <a name="face-detection"></a>人脸检测

人脸 API 可以检测图像中的人脸，并返回其位置的矩形坐标。 人脸检测还可提取一系列人脸相关属性，例如身体姿势、头部姿势、性别、年龄、情感、面部毛发和眼镜。

> [!NOTE] 
> 人脸检测功能也可通过[计算机视觉 API](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) 获取，但若要对人脸数据执行进一步的操作，则应使用人脸 API（本服务）。

![一位女士和一位男士的图像，在其面部绘制了矩形并显示了年龄和性别](./Images/Face.detection.jpg)

有关人脸检测的详细信息，请参阅[人脸检测操作指南](face-api-how-to-topics/howtodetectfacesinimage.md)，或浏览[检测 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) 参考文档。

## <a name="face-verification"></a>人脸验证

验证 API 针对检测到的两个人脸执行身份验证，或由一个检测到的人脸对一个人员对象执行身份验证。 实际上，它会评估两张脸是否属于同一个人。 这可能适用于安全方案。 有关详细信息，请参阅[验证 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)。

## <a name="find-similar-faces"></a>查找相似人脸

查找相似人脸 API 会根据一个目标人脸在一组候选人脸中搜索，找出与目标人脸最相似的一小组人脸。 支持两种工作模式，即 **matchPerson** 和 **matchFace**。 在针对同一人进行筛选（使用[验证 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)）后，**matchPerson** 模式会返回相似人脸。 **matchFace** 模式会忽略相同人员筛选器，返回一系列相似的候选人脸，这些人脸可能属于或不属于同一人。

在下面的示例中，这是目标人脸：

![一位微笑的女士](./Images/FaceFindSimilar.QueryFace.jpg)

下面是候选人脸：

![五位微笑的人的图像。 图像 a) 和 b) 为同一人](./Images/FaceFindSimilar.Candidates.jpg)

为了查找四张相似的人脸，**matchPerson** 模式会返回 (a) 和 (b)，它们与目标人脸描述同一个人。 **matchFace** 模式返回 (a)、(b)、(c)、(d)，恰好四个候选项，即使某些选项与目标不是同一人或者相似度低，也是如此。 有关详细信息，请参阅[查找相似人脸 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)。

## <a name="face-grouping"></a>人脸分组

组 API 会基于相似性将未知人脸的集合分为几组。 每个组是原始人脸集合的互不相交真子集。 一个组中的所有人脸可能属于同一人，但一个人可能有多个不同的组（按其他特征区分，例如按表情区分）。 有关详细信息，请参阅[组 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238)。

## <a name="person-identification"></a>人员识别

标识 API 可用于根据人员数据库标识检测的人脸。 这可以用于照片管理软件的自动图像标记。 请提前创建此数据库，可以随着时间推移对其进行编辑。

下图描述名为“myfriends”的数据库的示例。 每个组可以包含至多 1,000,000 个不同的人员对象，每个人员对象最多可以注册 248 张人脸。

![一个包含 3 个列（代表不同的人）的网格，每个列包含 3 行人脸图像](./Images/person.group.clare.jpg)

创建和训练数据库后，可以对组使用新检测到的人脸进行标识。 如果人脸被标识为组中的某一人员，则返回该人员对象。

有关人员标识的详细信息，请参阅[标识 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)。

## <a name="use-containers"></a>使用容器

将标准化的 Docker 容器安装到靠近数据的位置以后，即可[使用人脸容器](face-how-to-install-containers.md)检测、识别和标识人脸。

## <a name="sample-apps"></a>示例应用

以下示例应用程序展示了如何通过多种方式来使用人脸 API。

- [Microsoft 人脸 API：Windows 客户端库和示例](https://github.com/Microsoft/Cognitive-Face-Windows) - 一种 WPF 应用，演示人脸检测、分析和识别的几种方案。
- [FamilyNotes UWP 应用](https://github.com/Microsoft/Windows-appsample-familynotes) - 一种通用 Windows 平台 (UWP) 应用，可以在家庭便笺共享方案中使用人脸识别、语音、Cortana、墨迹和相机。

## <a name="data-privacy-and-security"></a>数据隐私和安全性

与所有认知服务一样，使用人脸服务的开发人员应该了解 Microsoft 针对客户数据的政策。 请参阅 Microsoft 信任中心上的[“认知服务”页面](https://www.microsoft.com/en-us/trustcenter/cloudservices/cognitiveservices)来了解详细信息。

## <a name="next-steps"></a>后续步骤

按照一个快速入门的说明，通过代码实现简单的人脸检测方案。
- [快速入门：使用 .NET SDK 和 C# 检测图像中的人脸](quickstarts/csharp.md)（有其他语言可用）
