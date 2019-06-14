---
title: 如何将你的项目迁移到 3.0 API
titlesuffix: Azure Cognitive Services
description: 了解如何将自定义视觉项目从 API 的早期版本迁移到 3.0 API。
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: areddish
ms.openlocfilehash: 9dd473aadd7123cafc27209f5c34322fdbcffb71
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60816450"
---
# <a name="migrate-to-the-30-api"></a>迁移到 3.0 API

自定义影像现在已达到正式发布，且已经发生了 API 更新。
此更新包括了一些新功能和重要的是，一些重大更改：

* 预测 API 现在拆分为两个基于项目类型。
* 视觉 AI 开发人员工具包 (VAIDK) 导出选项需要以特定方式创建项目。
* 默认迭代已删除为支持发布/取消发布命名的迭代。

本指南将演示如何更新项目以使用新的 API 版本。 请参阅[发行说明](release-notes.md)有关所做的更改的完整列表。

## <a name="use-the-updated-prediction-api"></a>使用更新的预测 API

2\.x Api 使用相同的预测调用图像分类器和对象检测程序项目。 这两种项目类型是可以接受**PredictImage**并**PredictImageUrl**调用。 从 3.0 开始，我们已拆分，以便你需要匹配项目类型设置为在调用此 API:

* 使用 **[ClassifyImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)** 并 **[ClassifyImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c14)** 若要获取的图像分类项目的预测。
* 使用 **[DetectImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c19)** 并 **[DetectImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c18)** 若要获取的对象检测项目的预测。

## <a name="use-the-new-iteration-publishing-workflow"></a>使用新的迭代发布工作流

2\.x Api 使用的默认迭代或指定的迭代 ID 以选择要用于预测的迭代。 从 3.0 开始，我们已采用首次发布培训 API 从使用指定名称的迭代，由此发布流。 然后将名称传递给预测方法，以指定要使用的迭代。

> [!IMPORTANT]
> 3\.0 Api 不使用默认迭代功能。 我们不推荐使用较旧的 Api，直到你可以继续使用 2.x Api 来切换为默认迭代。 这些 Api 将保留一段时间，并且可以调用 **[UpdateIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b818)** 方法将标记为默认值一次迭代。

### <a name="publish-an-iteration"></a>发布一次迭代

迭代训练后，您可以使其可供使用预测 **[PublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c82db28bf6a2b11a8247bbc)** 方法。 若要发布一次迭代，将需要预测资源 ID，可在 CustomVision 网站的设置页。

![带有所述的预测资源 ID 的自定义视觉网站设置页。](./media/update-application-to-3.0-sdk/prediction-id.png)

> [!TIP]
> 此外可以获取此信息从[Azure 门户](https://portal.azure.com)转到自定义视觉预测资源并选择**属性**。

迭代发布后，应用可以使用它进行预测通过其预测 API 调用中指定的名称。 若要使迭代的预测调用不可用，请使用 **[UnpublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b81a)** API。

## <a name="additional-export-options"></a>其他导出选项

使用 3.0 我们将公开两个额外的 Api 导出目标：ARM 体系结构和构想 AI 开发人员工具包。

* 若要使用 ARM，只需选择 Compact 域，然后选择 DockerFile，然后 ARM 作为导出选项。
* 对于视觉 AI 开发人员工具包，该项目必须创建与__常规 (Compact)__ 域以及在目标中指定 VAIDK 导出平台参数。

## <a name="next-steps"></a>后续步骤

* [训练 API 参考文档 (REST)](https://go.microsoft.com/fwlink/?linkid=865446)
* [预测 API 参考文档 (REST)](https://go.microsoft.com/fwlink/?linkid=865445)