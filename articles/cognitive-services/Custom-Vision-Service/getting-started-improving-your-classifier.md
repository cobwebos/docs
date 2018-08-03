---
title: 使用自定义影像服务改进分类器 - Azure 认知服务 | Microsoft Docs
description: 了解如何提高自定义影像服务分类器的质量。
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 07/05/2018
ms.author: nolachar
ms.openlocfilehash: 7c6cbd996d0c35b96fde78daf391bebb36feddce
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/06/2018
ms.locfileid: "37888163"
---
# <a name="how-to-improve-your-classifier"></a>如何改进分类器

了解如何提高自定义影像服务分类器的质量。 分类器的质量取决于向其提供的已标记数据的数量、质量和种类，以及数据集的平衡程度。 一个好的分类器通常具有平衡的定型数据集，该数据集是将提交到分类器的代表数据集。 此类分类器的构建过程通常迭代进行。 常常需要进行几轮定型才能达到预期效果。

以下是改进分类器所采用的常用步骤。 这些步骤并不是硬性规则，而是有助于构建更好的分类器的启发式方法。

1. 第一轮定型
1. 添加更多的图像和平衡数据
1. 重新训练
1. 添加具有不同背景、光照、对象大小、照相机角度和样式的图像
1. 重新训练并输入图像，进行预测
1. 检查预测结果
1. 修改现有定型数据

## <a name="data-quantity-and-data-balance"></a>数据量和数据平衡

最重要的一点是上传足够多的图像进行分类器训练。 建议以定型集每​​个标签至少 50 张图像作为起点。 图像越少，过度拟合风险越高。 虽然性能数据可能表明质量很好，但可能会与真实数据发生冲突。 使用更多图像定型分类器通常可以提高预测结果的准确性。

另一个考虑因素是，应确保数据平衡。 例如，一个标签有 500 张图像，另一个标签有 50 张图像，这将产生不平衡的定型数据集，从而使得模型在预测前一个标签时比后一个标签更准确。 如果在图像最少的标签与图像最多的标签之间至少保持 1:2 的比例，则可能会看到更好的结果。 例如，如果图像数最大的标签具有 500 张图像，则图像最少的标签需要至少具有 250 张图像用于定型。

## <a name="train-more-diverse-images"></a>训练更多样化的图像

提供将在正常使用期间提交到分类器的代表图像。 例如，定型“苹果”分类器时，如果只针对盘中的苹果照片进行定型，而对树上的苹果照片进行预测，则分类器可能不会那么准确。 涵盖各种图像可确保分类器不偏重某方面，涵盖所有方面。 以下是可使定型集更加多样化的一些方法：

背景：在不同背景前提供对象的图像（即盘子中的水果与食品袋中的水果）。 场景中的照片比中性背景前的照片更好，因为前者为分类器提供了更多信息。

![背景图像示例](./media/getting-started-improving-your-classifier/background.png)

光照：提供不同光照下的图像（即在闪光、高曝光等设置下拍摄），尤其是在用于预测的图像具有不同光照的情况下。 涵盖不同饱和度、色调和亮度的图像也很有帮助。

![光照图像示例](./media/getting-started-improving-your-classifier/lighting.png)

对象大小：提供的图像中，对象具有不同大小，拍摄对象的不同部分。 例如，几串香蕉的照片和一根香蕉的特写照片。 不同的大小有助于分类器涵盖所有方面。

![大小图像示例](./media/getting-started-improving-your-classifier/size.png)

照相机角度：提供以不同照相机角度拍摄的图像。 如果所有的照片都是使用一组固定相机（例如监控摄像头）拍摄的，请确保为每个照相机分配不同的标签，即使它们拍摄同一对象也是如此，从而避免过度拟合 - 将不相关的对象（如灯柱）作为关键特点建模。

![角度图像示例](./media/getting-started-improving-your-classifier/angle.png)

样式：提供同一类对象不同样式的图像（即不同种类的柑橘）。 但是，如果是完全不同样式的对象（即米老鼠与实际老鼠）的图像，建议将它们标记为单独的类，这样才能更好地表示其不同的特征。

![样式图像示例](./media/getting-started-improving-your-classifier/style.png)

## <a name="use-images-submitted-for-prediction"></a>使用提交用于预测的图像

自定义影像服务会存储提交到预测终结点的图像。 若要使用这些图像来改进分类器，请使用以下步骤：

1. 若要查看提交到分类器的图像，请打开[自定义视觉网页](https://customvision.ai)，转到相应项目，然后选择“预测”选项卡。默认视图显示当前迭代的图像。 可使用“迭代”下拉字段查看先前迭代期间提交的图像。

    ![“预测”选项卡的图像](./media/getting-started-improving-your-classifier/predictions.png)

2. 将鼠标悬停在图像上可查看分类器预测的标记。 图像存在先后顺序，对分类器最有用的图像位于最上方。 若要选择不同排序依据，请使用“排序”部分。 若要将图像添加到现有定型数据，请依次选择该图像、正确的标记，然后单击“保存并关闭”。 图像将从“预测”中删除并添加到定型图像中。 可选择“定型图像”选项卡查看该图像。

    ![“标记”页面的图像](./media/getting-started-improving-your-classifier/tag.png)

3. 使用“定型”按钮重新定型分类器。

## <a name="visually-inspect-predictions"></a>直观检查预测

若要检查图像预测，请选择“定型图像”选项卡，然后选择“迭代历史记录”。 未正确预测用红框勾勒的图像。

![迭代历史记录的图像](./media/getting-started-improving-your-classifier/iteration.png)

有时，直观检查可以识别模式，随后可通过添加其他定型数据或修改现有定型数据来纠正这些模式。 例如，苹果与酸橙的分类器可能会错误地将所有青苹果标记为酸橙。 可以通过添加并提供包含青苹果的已标记图像的定型数据来解决此问题。

## <a name="unexpected-classification"></a>意外分类

有时，分类器会错误地获知图像通用的特征。 例如，创建苹果与柑橘分类器时，如果提供了手中的苹果和白盘中的柑橘的图像，则分类器可能针对手与白盘进行定型，而不是针对苹果与柑橘。

![意外分类的图像](./media/getting-started-improving-your-classifier/unexpected.png)

要解决此问题，请使用通过更多不同图像定型的以上指南：提供不同角度、背景、对象大小、组和其他变量的图像。

## <a name="negative-image-handling"></a>负片图像处理

自定义影像服务支持某些自动负片图像处理。 如果要生成葡萄与香蕉的分类器并提交一张鞋子图像用于预测，则分类器对于该图像的葡萄与香蕉评分均应接近 0%。

另一方面，如果负片图像仅仅是定型中使用的图像的变量，由于相似性很大，模型很可能将负片图像分类为标记类。 例如，如果具有一个橙子与葡萄柚分类器，并且输入克莱门氏小柑橘的图像，则它可能将克莱门氏小柑橘分类为橙子。 有可能发生这种情况，因为克莱门氏小柑橘的许多特征（颜色、形状、纹理、自然生境等）类似于橙子的特征。  如果负片图像具有这种性质，建议在定型期间创建一个或多个单独的标签（“其他”）并使用此标签标记负片图像，使模型更好地区分这些类。

## <a name="next-steps"></a>后续步骤

了解如何通过将图像提交到预测 API 来以编程方式测试这些图像。

> [!div class="nextstepaction"]
[使用预测 API](use-prediction-api.md)
