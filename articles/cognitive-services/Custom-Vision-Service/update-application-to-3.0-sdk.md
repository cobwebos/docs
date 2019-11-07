---
title: 如何将项目更新到 3.0 API
titleSuffix: Azure Cognitive Services
description: 了解如何将以前版本的 API 中的自定义视觉项目更新到 3.0 API。
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: areddish
ms.openlocfilehash: c134f30b124113a23df0e73cd1bbc8209e335183
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73647502"
---
# <a name="update-to-the-30-api"></a>更新到 3.0 API

自定义视觉现已达到一般可用性，已经完成了 API 更新。
此更新包括一些新功能，重要的是一些重大更改：

* 预测 API 现在根据项目类型拆分为两个。
* 视觉 AI 开发人员工具包（VAIDK）导出选项需要以特定方式创建项目。
* 为了支持发布/取消发布命名迭代，已删除默认迭代。

本指南将演示如何更新项目以使用新的 API 版本。 有关更改的完整列表，请参阅[发行说明](release-notes.md)。

## <a name="use-the-updated-prediction-api"></a>使用更新的预测 API

2\.x Api 对图像分类器和对象检测器项目使用相同的预测调用。 对于**PredictImage**和**PredictImageUrl**调用，这两个项目类型都是可接受的。 从3.0 开始，我们拆分了此 API，因此需要将项目类型与调用进行匹配：

* 使用 **[ClassifyImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)** 和 **[ClassifyImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c14)** 获取图像分类项目的预测。
* 使用 **[DetectImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c19)** 和 **[DetectImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c18)** 获取对象检测项目的预测。

## <a name="use-the-new-iteration-publishing-workflow"></a>使用新的迭代发布工作流

2\.x Api 使用默认迭代或指定的迭代 ID 来选择要用于预测的迭代。 从3.0 开始，我们采用了一种发布流程，让你先从培训 API 中的指定名称发布迭代。 然后，将该名称传递给预测方法，以指定要使用的迭代。

> [!IMPORTANT]
> 3\.0 Api 不使用默认的迭代功能。 在我们弃用旧版 Api 之前，你可以继续使用 2.x Api 将迭代作为默认值进行切换。 这些 Api 将在一段时间内维护，你可以调用 **[UpdateIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b818)** 方法将迭代标记为默认值。

### <a name="publish-an-iteration"></a>发布迭代

迭代定型后，可以使用 **[PublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c82db28bf6a2b11a8247bbc)** 方法使其可用于预测。 若要发布迭代，需要提供预测资源 ID，该 ID 在 CustomVision 网站的 "设置" 页上提供。

![包含预测资源 ID 的 "自定义视觉网站设置" 页。](./media/update-application-to-3.0-sdk/prediction-id.png)

> [!TIP]
> 你还可以从[Azure 门户](https://portal.azure.com)获取此信息，方法是转到自定义视觉预测资源，并选择 "**属性**"。

发布迭代后，应用可通过在其预测 API 调用中指定名称来将其用于预测。 若要使迭代不适用于预测调用，请使用 **[UnpublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b81a)** API。

## <a name="next-steps"></a>后续步骤

* [训练 API 参考文档（REST）](https://go.microsoft.com/fwlink/?linkid=865446)
* [预测 API 参考文档（REST）](https://go.microsoft.com/fwlink/?linkid=865445)