---
title: 使用自定义影像服务改进分类器 - Azure 认知服务 | Microsoft Docs
description: 了解如何提高自定义影像服务分类器的质量。
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: 65b1424f259066c7d5bd6b2b508d2a4052ff0527
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365891"
---
# <a name="how-to-improve-your-classifier"></a>如何改进分类器

了解如何提高自定义影像服务分类器的质量。 分类器的质量取决于向其提供的已标记数据的质量。 

## <a name="train-more-varied-images"></a>定型更多不同图像

提供不同角度、背景、对象大小、照片组和其他变量的已标记图像可改进分类器。 上下文中的照片优于中性背景前面的照片。 包括代表将在正常使用期间提交到分类器的图像。

有关添加图像的详细信息，请参阅[生成分类器](getting-started-build-a-classifier.md)文档。

> [!IMPORTANT]
> 在添加图像后，请记得定型分类器。

## <a name="use-images-submitted-for-prediction"></a>使用提交用于预测的图像

自定义影像服务会存储提交到预测终结点的图像。 若要使用这些图像来改进分类器，请使用以下步骤：

1. 若要查看提交到分类器的图像，请打开[自定义影像网页](https://customvision.ai)，然后选择“预测”选项卡。

    ![“预测”选项卡的图像](./media/getting-started-improving-your-classifier/predictions-tab.png)

    > [!TIP]
    > 默认视图显示当前迭代的图像。 可使用“迭代”下拉字段查看先前迭代期间提交的图像。

2. 将鼠标悬停在图像上可查看分类器预测的标记。

    > [!TIP]
    > 图像存在先后顺序，对分类器最有用的图像位于最上方。 若要选择不同排序依据，请使用“排序”部分。

    若要将图像添加到定型数据，请依次选择该图像和标记，然后选择“保存并关闭”。 图像将从“预测”中删除并添加到定型图像中。 可选择“定型图像”选项卡查看该图像。

    ![“标记”页面的图像](./media/getting-started-improving-your-classifier/tag-image.png)

3. 使用“定型”按钮重新定型分类器。

## <a name="visually-inspect-predictions"></a>直观检查预测

若要检查图像预测，请选择“定型图像”选项卡，然后选择“迭代历史记录”。 未正确预测用红框勾勒的图像。

![迭代历史记录的图像](./media/getting-started-improving-your-classifier/iteration-history.png)

有时，直观检查可以识别模式，随后可通过添加其他定型数据来纠正这些模式。 例如，玫瑰与雏菊的分类器可能错误地将所有白色玫瑰标记为雏菊。 你可以通过添加并提供包含白色玫瑰的已标记图像的定型数据来解决此问题。

## <a name="unexpected-classification"></a>意外分类

有时，分类器可获知你的图像通用的特征。 例如，你想要为玫瑰与郁金香创建分类器。 提供在旷野中的郁金香的图像，以及在蓝墙前面的红色花瓶中的玫瑰的图像。 鉴于此数据，分类器可能会为旷野与墙 + 花瓶定型，而不是为玫瑰与郁金香定型。

要解决此问题，请使用通过更多不同图像定型的指南：提供不同角度、背景、对象大小、组和其他变量的图像。

## <a name="negative-image-handling"></a>负片图像处理

自定义影像服务支持某些自动负片图像处理。 如果要生成猫与狗的分类器并提交用于预测的鞋图像，则分类器应为猫和狗将该图像评分为接近 0%。 

> [!WARNING]
> 自动方法适用于明确负片图像。 如果负片图像只是定型中使用的图像的变体，则可能无法使用该自动方法。 
>
> 例如，如果你有哈士奇与柯吉犬的分类器，并在博美犬的图像中喂食，则可能会将博美犬视为哈士奇。 如果你的负片图像具有此特性，请创建新标记（如“其他”），并将其应用到负片定型图像。

## <a name="next-steps"></a>后续步骤

[使用预测 API](use-prediction-api.md)