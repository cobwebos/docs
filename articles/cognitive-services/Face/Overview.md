---
title: 什么是 Azure 人脸服务？
titleSuffix: Azure Cognitive Services
description: Azure 人脸服务提供用于检测、识别和分析图像中人脸的 AI 算法。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 9/17/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: 人脸识别, 人脸识别软件, 人脸分析, 人脸匹配, 人脸识别应用, 按图像搜索人脸, 人脸识别搜索
ms.openlocfilehash: 0a7e242add9fdaa9e169a4003e8ad8f39b1fb111
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91262478"
---
# <a name="what-is-the-azure-face-service"></a>什么是 Azure 人脸服务？

> [!WARNING]
> 2020 年 6 月 11 日，Microsoft 宣布在以人权为基础的强有力的法规颁布之前，将不向美国的警察局出售人脸识别技术。 同样地，如果客户是美国警察局、由美国警察局准许使用或针对美国警察局使用 Azure 服务，则这些客户不得使用这类服务中包含的人脸识别特性或功能（例如人脸或视频索引器）。

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure 人脸服务提供用于检测、识别和分析图像中人脸的 AI 算法。 人脸识别软件在许多不同场景中都很重要，其中包括安全、自然用户界面、图像内容分析和管理、移动应用和机器人。

人脸服务提供多种不同的人脸分析功能，以下部分一一概述了这些功能。

## <a name="face-detection"></a>人脸检测

人脸服务可以检测图像中的人脸，并返回其位置的矩形坐标。 人脸检测还可提取一系列人脸相关属性，例如身头部姿势、性别、年龄、情感、面部毛发和眼镜。

> [!NOTE]
> 也可以通过[计算机视觉服务](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)使用人脸检测功能。 但是，如果要对人脸数据执行进一步操作，则应改用此服务。

![一位女士和一位男士的图像，在其面部绘制了矩形并显示了年龄和性别](./Images/Face.detection.jpg)

有关人脸检测的详细信息，请参阅[人脸检测](concepts/face-detection.md)概念文章。 另请参阅[检测 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) 参考文档。

## <a name="face-verification"></a>人脸验证

验证 API 针对检测到的两个人脸执行身份验证，或从一个检测到的人脸对一个人员对象执行身份验证。 实际上，它会评估两张脸是否属于同一个人。 此功能可能适用于安全方案。 有关详细信息，请参阅[人脸识别](concepts/face-recognition.md)概念指南或[验证 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) 参考文档。

## <a name="find-similar-faces"></a>查找相似人脸

查找相似人脸 API 会在目标人脸和一组候选人脸之间进行人脸匹配，找出与目标人脸相似的一小组人脸。 这对于按图像进行人脸搜索很有用。 

该 API 支持两种工作模式：matchPerson 和 matchFace 。 使用[验证 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) 针对同一人进行筛选后，matchPerson 模式会返回相似人脸。 matchFace 模式会忽略同一人筛选器。 它返回相似候选人脸的列表，这些人脸不一定属于同一人。

以下示例显示了目标人脸：

![一位微笑的女士](./Images/FaceFindSimilar.QueryFace.jpg)

这些图像是候选人脸：

![五位微笑的人的图像。 图像 a 和 b 显示同一人。](./Images/FaceFindSimilar.Candidates.jpg)

为了查找四张相似人脸，matchPerson 模式会返回 a 和 b，它们与目标人脸显示的是同一个人。 matchFace 模式返回 a、b、c 和 d&mdash;恰好四个候选项，即使某些选项与目标不是同一人或者相似度低，也是如此。 有关详细信息，请参阅[人脸识别](concepts/face-recognition.md)概念指南或[查找相似人脸 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) 参考文档。

## <a name="face-grouping"></a>人脸分组

组 API 会基于相似性将未知人脸的集合分为几组。 每个组是原始人脸集合的互不相交真子集。 一个组中的所有人脸可能属于同一人。 一个人可能有多个不同的组。 组按其他因素（例如表情）区分。 有关详细信息，请参阅[人脸识别](concepts/face-recognition.md)概念指南或[分组 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) 参考文档。

## <a name="person-identification"></a>人员识别

识别 API 用于根据人员数据库识别检测到的人脸（人脸识别搜索）。 此功能可用于照片管理软件中的自动图像标记。 需提前创建数据库，以后可以不断地对其进行编辑。

下图显示名为 `"myfriends"` 的数据库的示例。 每个组最多可以包含 100 万个不同的 person 对象。 每个人员对象可以注册最多 248 张人脸。

![包含三个列（代表不同的人）的网格，每个列包含三行人脸图像](./Images/person.group.clare.jpg)

创建并训练数据库后，可以对组使用新检测到的人脸执行识别。 如果人脸被标识为组中的某一人员，则返回该人员对象。

有关人员识别的详细信息，请参阅[人脸识别](concepts/face-recognition.md)概念指南或[识别 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) 参考文档。

## <a name="containers"></a>容器

将标准化的 Docker 容器安装到靠近数据的位置后，[使用人脸容器](face-how-to-install-containers.md)来检测和识别人脸。

## <a name="sample-apps"></a>示例应用

以下示例应用程序显示了人脸服务的多种用法：

- [人脸 API：Windows 客户端库和示例](https://github.com/Microsoft/Cognitive-Face-Windows)是一个 WPF 应用，演示人脸检测、分析和识别的多种方案。
- [FamilyNotes UWP 应用](https://github.com/Microsoft/Windows-appsample-familynotes)是一个通用 Windows 平台 (UWP) 应用，可以在家庭便笺共享方案中使用人脸识别、语音、Cortana、墨迹和相机。

## <a name="data-privacy-and-security"></a>数据隐私和安全性

与所有认知服务资源一样，使用人脸服务的开发人员必须了解 Microsoft 针对客户数据的政策。 有关详细信息，请参阅 Microsoft 信任中心内的[“认知服务”页](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)。

## <a name="next-steps"></a>后续步骤

按照快速入门中的说明，使用所选语言对人脸识别应用的基本组件进行编码。

- [客户端库快速入门](quickstarts/client-libraries.md)。
