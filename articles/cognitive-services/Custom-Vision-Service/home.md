---
title: 什么是 Azure 自定义视觉？
titlesuffix: Azure Cognitive Services
description: 了解如何通过自定义视觉服务在 Azure 云中生成自定义图像分类器。
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: overview
ms.date: 10/26/2018
ms.author: anroth
ms.openlocfilehash: 5e8e675b32bfd4c741b82b1ab341a80adbb0529d
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741634"
---
# <a name="what-is-azure-custom-vision"></a>什么是 Azure 自定义视觉？

Azure 自定义视觉 API 是一种认知服务，用于生成、部署和改进自定义图像分类器。 图像分类器是一种 AI 服务，可以根据某些特征将图像分成各种类（标记）。 与[计算机视觉](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)服务不同，自定义视觉允许你创建自己的分类。

## <a name="what-it-does"></a>作用

自定义视觉服务使用机器学习算法对图像分类。 你作为开发人员必须提供多组图像，其中包含或缺少相关的分类。 请在提交时指定正确的图像标记。 然后，此算法会针对该数据进行训练并计算其自己的准确度，方法是针对这些相同的数据自行进行测试。 训练模型以后，即可对其进行测试、重新训练，并最终使用它根据应用的需求对新图像分类。 也可导出模型本身，方便脱机使用。

### <a name="classification-and-object-detection"></a>分类和对象检测

可以将自定义视觉功能分为两种功能。 **图像分类**可以为每个图像分配类别。 **对象检测**与之类似，但还在图像中返回坐标，坐标中可以找到应用的标记。

### <a name="optimization"></a>优化

通常情况下，自定义视觉服务使用的方法能够可靠地处理差异，因此，使用少量的数据就能开始制作原型。 开始时，每个标记通常可以包含 50 个图像。 但是，这意味着此服务不适用于检测图像中的细微差异（例如，在质量保证方案中检测细微裂纹或凹陷）。

另外，可以从多种自定义视觉算法中进行选择，这些算法已针对某些主题内容（例如特征点或零售商品）进行了优化。 此方面的详细信息，请参阅[生成分类器](getting-started-build-a-classifier.md)指南。

## <a name="what-it-includes"></a>组成部分
自定义视觉服务以一组本机 SDK 的形式提供，另外也可通过[自定义视觉主页](https://customvision.ai/)上的基于 Web 的界面来获取它。 可以通过一种或两种界面来创建、测试和训练模型。

![Chrome 浏览器窗口中的自定义视觉主页](media/browser-home.png)

## <a name="next-steps"></a>后续步骤

按照[生成分类器](getting-started-build-a-classifier.md)指南操作，开始在 Web 上使用自定义视觉，或者完成[图像分类教程](csharp-tutorial.md)，以便实现代码中的方案。
