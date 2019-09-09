---
title: 什么是 Azure 自定义视觉？
titleSuffix: Azure Cognitive Services
description: 了解如何通过自定义视觉服务在 Azure 云中生成自定义图像分类器。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: 033b0317b1738e24e4ac9c9ae2150b015cc5a8e5
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560967"
---
# <a name="what-is-azure-custom-vision"></a>什么是 Azure 自定义视觉？

Azure 自定义视觉是一种认知服务，用于生成、部署和改进自己的图像分类器。 图像分类器是一种 AI 服务，可以根据视觉特征为图像应用标签（代表类  ）。 与[计算机视觉](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)服务不同，自定义视觉允许你确定要应用的标签。

## <a name="what-it-does"></a>作用

自定义视觉服务使用机器学习算法为图像应用标签。 你作为开发人员必须提供多组图像，其中包含或缺少相关的特征。 请在提交时自行标记图像。 然后，此算法会针对该数据进行训练并计算其自己的准确度，方法是针对这些相同的图像自行进行测试。 训练算法以后，即可对其进行测试、重新训练，并最终使用它根据应用的需求对新图像分类。 也可导出模型本身，方便脱机使用。

### <a name="classification-and-object-detection"></a>分类和对象检测

可以将自定义视觉功能分为两种功能。 **图像分类**可将一个或多个标签应用到图像。 **对象检测**与之类似，但还在图像中返回坐标，坐标中可以找到应用的标签。

### <a name="optimization"></a>优化

自定义视觉服务经过优化，可以快速识别图像之间的主要差异，因此你可以使用少量数据开始原型制作。 开始时，每个标签通常可以包含 50 个图像。 此服务不适用于检测图像中的细微差异（例如，在质量保证方案中检测细微裂纹或凹陷）。

另外，可以从多种自定义视觉算法中进行选择，这些算法已针对包含某些主题内容（例如特征点或零售商品）的图像进行了优化。 有关详细信息，请参阅[生成分类器](getting-started-build-a-classifier.md)指南。

## <a name="what-it-includes"></a>组成部分

自定义视觉服务以一组本机 SDK 的形式提供，另外也可通过[自定义视觉主页](https://customvision.ai/)上的基于 Web 的界面来获取它 可以通过一种或两种界面来创建、测试和训练模型。

![Chrome 浏览器窗口中的自定义视觉主页](media/browser-home.png)

## <a name="data-privacy-and-security"></a>数据隐私和安全性

与所有认知服务一样，使用自定义视觉服务的开发人员应该了解 Microsoft 针对客户数据的政策。 请参阅 Microsoft 信任中心上的[“认知服务”页面](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)来了解详细信息。

## <a name="next-steps"></a>后续步骤

按照[生成分类器](getting-started-build-a-classifier.md)指南操作，开始在 Web 上使用自定义视觉，或者完成[图像分类教程](csharp-tutorial.md)，以便实现代码中的基本方案。
