---
title: 使用智能贴标机更快地标注图像
titleSuffix: Azure Cognitive Services
description: 在本指南中，您将学习如何使用智能标注器为图像生成建议的标签。 这样，在训练自定义视觉模型时，您可以更快地标记大量图像。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: pafarley
ms.openlocfilehash: 94ca47e6114e4f8c3485f6072facd07c25e4b96a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647751"
---
# <a name="label-images-faster-with-smart-labeler"></a>使用智能贴标机更快地标注图像

在本指南中，您将学习如何使用智能标注器为图像生成建议的标签。 这样，在训练自定义视觉模型时，您可以更快地标记大量图像。

为自定义视觉模型标记图像时，服务使用模型的最新训练迭代来预测未标记图像的标签。 然后，根据所选的置信度阈值和预测不确定性将这些预测作为建议标记显示。 然后，您可以确认或更改建议，从而加快手动标记图像以进行培训的过程。

## <a name="when-to-use-smart-labeler"></a>何时使用智能贴标机

请记住以下限制：

* 对于内容已训练过一次的图像，应仅请求建议的标记。 不要收到您刚刚开始训练的新标签的建议。

> [!IMPORTANT]
> 智能贴标器功能使用与常规预测相同的[定价模型](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)。 首次为一组图像触发建议标记时，将向您收取与预测调用相同的费用。 之后，该服务将所选图像的结果存储在数据库中 30 天，您可以在该时间段内随时免费访问它们。 30 天后，如果您再次请求他们建议的标签，将向您收费。

## <a name="smart-labeler-workflow"></a>智能贴标器工作流程

以下步骤演示如何使用智能贴标机：

1. 将所有培训图像上传到自定义视觉项目。
1. 标记数据集的一部分，为每个标记选择相同数量的图像。
    > [!TIP]
    > 请确保使用以后需要建议的所有标记。
1. 开始培训过程。
1. 训练完成后，导航到 **"未标记**"视图，然后选择左侧窗格上的"**获取建议标记**"按钮。
    > [!div class="mx-imgBorder"]
    > ![建议的标记按钮显示在未标记的图像选项卡下。](./media/suggested-tags/suggested-tags-button.png)
1. 在显示的弹出窗口中，设置需要建议的图像数。 您应该只获取未标记图像的一部分的初始标记建议。 在迭代此过程时，您将获得更好的标记建议。
1. 确认建议的标记，修复任何不正确标记。
    > [!TIP]
    > 具有建议标记的图像按其预测不确定性进行排序（值越低表示置信度越高）。 您可以使用"**按不确定性排序"** 选项更改排序顺序。 如果将顺序设置为**高到低**，则可以首先更正高不确定性预测，然后快速确认低不确定性预测。
    * 在图像分类项目中，可以选择和确认标记。 按给定建议标记筛选视图，取消选择标记错误的图像，然后确认批处理中的其余图像。
        > [!div class="mx-imgBorder"]
        > ![建议的标记以批处理模式显示，用于带有筛选器的 IC。](./media/suggested-tags/ic-batch-mode.png)

        还可以通过从库中选择图像，在单个图像模式下使用建议标记。

        ![建议的标记以 IC 的单个图像模式显示。](./media/suggested-tags/ic-individual-image-mode.png)
    * 在对象检测项目中，不支持批处理确认，但您仍可以通过建议的标记筛选和排序，以便获得更有条理的标签体验。 未标记图像的缩略图将显示一个边界框的叠加，指示建议标记的位置。 如果未选择建议的标记筛选器，则所有未标记的图像都将显示，而不会叠加边界框。
        > [!div class="mx-imgBorder"]
        > ![建议的标记以批处理模式显示，用于带筛选器的 OD。](./media/suggested-tags/od-batch-mode.png)

        要确认对象检测标记，您需要将它们应用于库中的每个单个图像。

        ![建议的标记以 OD 的单个图像模式显示。](./media/suggested-tags/od-individual-image-mode.png)
1. 重新开始培训过程。
1. 重复上述步骤，直到您对建议质量感到满意。

## <a name="next-steps"></a>后续步骤

遵循快速入门开始创建和培训自定义愿景项目。

* [生成分类器](getting-started-build-a-classifier.md)
* [生成对象检测器](get-started-build-detector.md)