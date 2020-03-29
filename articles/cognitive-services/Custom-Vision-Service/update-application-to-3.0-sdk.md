---
title: 如何将项目更新为 3.0 API
titleSuffix: Azure Cognitive Services
description: 了解如何将自定义视觉项目从以前的 API 版本更新为 3.0 API。
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: areddish
ms.openlocfilehash: c134f30b124113a23df0e73cd1bbc8209e335183
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647502"
---
# <a name="update-to-the-30-api"></a>更新至 3.0 API

自定义愿景现已达到通用性，并经历了 API 更新。
此更新包括一些新功能，更重要的是，一些重大更改：

* 预测 API 现在根据项目类型分为两个。
* 愿景 AI 开发人员工具包 （VAIDK） 导出选项要求以特定方式创建项目。
* 默认迭代已被删除，有利于发布/取消发布命名迭代。

本指南将介绍如何更新项目以使用新的 API 版本。 有关更改的完整列表，请参阅[发行说明](release-notes.md)。

## <a name="use-the-updated-prediction-api"></a>使用更新的预测 API

2.x API 对图像分类器和对象检测器项目使用相同的预测调用。 预测**图像**和**预测图像Url**调用可以接受这两种项目类型。 从 3.0 开始，我们拆分此 API，以便您需要将项目类型与调用匹配：

* 使用**["分类图像](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)**"和**["分类图像Url"](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c14)** 获取图像分类项目的预测。
* 使用**[检测图像](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c19)** 和**[检测图像Url](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c18)** 获取对象检测项目的预测。

## <a name="use-the-new-iteration-publishing-workflow"></a>使用新的迭代发布工作流

2.x API 使用默认迭代或指定的迭代 ID 选择用于预测的迭代。 从 3.0 开始，我们采用了发布流，通过该流，您首先从培训 API 中以指定名称发布迭代。 然后，将名称传递给预测方法，以指定要使用的迭代。

> [!IMPORTANT]
> 3.0 API 不使用默认迭代功能。 在我们弃用较旧的 API 之前，可以继续使用 2.x API 将迭代作为默认值切换。 这些 API 将维护一段时间，您可以调用**[UpdateTimea](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b818)** 方法将迭代标记为默认值。

### <a name="publish-an-iteration"></a>发布迭代

训练迭代后，可以使用**[Publish 迭代](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c82db28bf6a2b11a8247bbc)** 方法将其可用于预测。 要发布迭代，您需要预测资源 ID，该 ID 可在自定义 Vision 网站的设置页面上提供。

![自定义视觉网站设置页面，概述预测资源 ID。](./media/update-application-to-3.0-sdk/prediction-id.png)

> [!TIP]
> 还可以通过访问自定义视觉预测资源和选择**属性**从[Azure 门户](https://portal.azure.com)获取此信息。

发布迭代后，应用可以通过在其预测 API 调用中指定名称来使用它进行预测。 要使迭代对预测调用不可用，请使用**["未发布迭代](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b81a)**"API。

## <a name="next-steps"></a>后续步骤

* [培训 API 参考文档 （REST）](https://go.microsoft.com/fwlink/?linkid=865446)
* [预测 API 参考文档 （REST）](https://go.microsoft.com/fwlink/?linkid=865445)