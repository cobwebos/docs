---
title: 用建议的标记加快图像标签
titleSuffix: Azure Cognitive Services
description: 在本指南中，你将学习如何使用建议的标记，以便在训练自定义视觉模型时更快速地标记大量图像。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: pafarley
ms.openlocfilehash: 31b8dfc234ac99d6f04061d6596e3dc8113e8d0f
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213836"
---
# <a name="label-images-faster-with-suggested-tags"></a>用建议的标记加快图像标签

在本指南中，您将学习如何使用建议的标记功能在训练自定义视觉模型时更快速地标记大量图像。 

为自定义视觉模型标记图像时，服务将使用模型的最新定型迭代来预测未标记图像的标签。 然后，它会根据所选的置信度阈值和预测不确定性，将这些预测作为建议标记显示。 然后，可以确认或更改建议，加速手动标记图像以进行训练的过程。

## <a name="when-to-use-suggested-tags"></a>何时使用建议的标记

请记住以下限制：

* 只为其内容已经过一次训练的图像请求建议标记。 不要为刚开始训练的新标记获取建议。
* 只能在未标记的图像上使用建议的标记;不能为已标记的图像上的其他标记获取建议。

> [!IMPORTANT]
> 建议的标记功能使用与常规预测相同的[定价模型](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)。 第一次触发一组图像的建议标记时，将按与预测调用相同的方式进行收费。 之后，该服务将在数据库中将所选图像的结果存储30天，并且可以在该时间段内随时访问这些结果。 30天后，如果再次请求其建议的标记，则会向你收费。

## <a name="suggested-tags-workflow"></a>建议的标记工作流

以下步骤演示如何使用建议的标记功能：

1. 将所有定型图像上传到自定义视觉项目。
1. 为每个标记选择相同数量的图像，为数据集添加标签。
    > [!TIP]
    > 请确保在以后使用所需建议的所有标记。
1. 开始训练过程。
1. 训练完成后，导航到未标记**的视图**，并在左窗格中选择 "**获取建议的标记**" 按钮。
    > [!div class="mx-imgBorder"]
    > !["未标记的图像" 选项卡下显示了 "建议的标记" 按钮。](./media/suggested-tags/suggested-tags-button.png)
1. 选择要为其建议的图像集。 只应获取部分未标记图像的初始标记建议。 循环访问此过程时，将获得更好的标记建议。
1. 确认建议的标记，修复不正确的标记。
    > [!TIP]
    > 带有建议标记的图像按其预测不确定性排序（较小的值表明置信度较低）。 您可以使用 "**按不确定性排序**" 选项更改排序顺序。 如果将顺序设置为 "**高**" 并将其设置为 "低"，则可以先更正高确定性预测，然后迅速确认不确定性的预测。
    * 在 "图像分类项目" 中，可以选择和确认批次中的标记。 按给定的建议标记筛选视图，取消选择标记为错误的图像，然后确认批处理中的其余部分。
        > [!div class="mx-imgBorder"]
        > ![对于带有筛选器的 IC，建议标记以批处理模式显示。](./media/suggested-tags/ic-batch-mode.png)

        还可以通过从库中选择一个图像，在单独的图像模式下使用建议的标记。

        ![对于 IC，建议标记显示在单独的图像模式中。](./media/suggested-tags/ic-individual-image-mode.png)
    * 在对象检测项目中，不支持批处理确认，但仍可以按建议的标记进行筛选和排序，以获得更有条理的标记体验。 未标记图像的缩略图将显示一叠加边界框，指示建议标记的位置。 如果未选择建议的标记筛选器，则所有未标记的图像将显示而不覆盖边界框。
        > [!div class="mx-imgBorder"]
        > ![对于带有筛选器的 OD，建议标记以批处理模式显示。](./media/suggested-tags/od-batch-mode.png)

        若要确认对象检测标记，需要将它们应用到库中的每个单独的映像。

        ![建议的标记以 OD 的单独图像模式显示。](./media/suggested-tags/od-individual-image-mode.png)
1. 再次开始训练过程。
1. 重复上述步骤，直到对建议质量感到满意。

## <a name="next-steps"></a>后续步骤

按照快速入门，开始创建和训练自定义视觉项目。

* [生成分类器](getting-started-build-a-classifier.md)
* [生成对象探测器](get-started-build-detector.md)