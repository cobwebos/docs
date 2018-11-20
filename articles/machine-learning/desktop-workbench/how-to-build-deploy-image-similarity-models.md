---
title: 使用 Azure 机器学习计算机视觉包生成和部署图像相似度模型。
description: 了解如何使用 Azure 机器学习计算机视觉包，以生成、定型、测试和部署计算机视觉图像相似度模型。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 05/20/2018
ROBOTS: NOINDEX
ms.openlocfilehash: b1b4e3e19adb38a69e16aaa98300972df3bb71a8
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2018
ms.locfileid: "50093643"
---
# <a name="build-and-deploy-image-similarity-models-with-azure-machine-learning"></a>使用 Azure 机器学习生成和部署图像相似度模型

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]


本文介绍了如何使用 Azure 机器学习计算机视觉包 (AMLPCV)，以定型、测试和部署图像相似度模型。 若要概览此包及其详细参考文档，请访问 https://aka.ms/aml-packages/vision。

使用图像相似度，可以解决计算机视觉领域中的大量问题。 这些问题包括生成可解答以下问题的模型：
+ 某个对象（例如，“狗”、“猫”、“船”等）是否在图像中存在？
+ 此患者的视网膜扫描显示眼疾严重程度属于哪一级别？

使用 AMLPCV 生成和部署此模型时，需要经历以下步骤：
1. 数据集创建
2. 图像可视化和注释
3. 图像增强
4. 深度神经网络 (DNN) 模型定义
5. 分类器训练
6. 评估和可视化
7. Web 服务部署
8. Web 服务负载测试

[CNTK](https://www.microsoft.com/en-us/cognitive-toolkit/) 用作深度学习框架，训练在配备 GPU 的计算机（例如 [深度学习数据科学 VM](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview)）本地执行，部署使用 Azure 机器学习操作化 CLI。

## <a name="prerequisites"></a>先决条件

1. 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

1. 必须设置并安装以下帐户和应用程序：
   - 一个 Azure 机器学习试验帐户 
   - Azure 机器学习模型管理帐户
   - 已安装 Azure Machine Learning Workbench

   如果尚未创建或安装上述三项，请遵循 [Azure 机器学习快速入门和 Workbench 安装](../desktop-workbench/quickstart-installation.md)一文操作。 

1. 必须安装用于计算机视觉的 Azure 机器学习包。 了解如何安装此包（如 https://aka.ms/aml-packages/vision 所述）。

## <a name="sample-data-and-notebook"></a>示例数据和 Notebook

### <a name="get-the-jupyter-notebook"></a>获取 Jupyter Notebook

自行下载 Notebook，以运行本文所述的示例。

> [!div class="nextstepaction"]
> [获取 Jupyter Notebook](https://aka.ms/aml-packages/vision/notebooks/object_detection)

### <a name="load-the-sample-data"></a>加载示例数据

以下示例使用包含 63 张餐具图像的数据集。 每张图像标记为属于四种不同的类（碗、杯、刀叉、盘）中的一种。 此示例中的图像数量较少，以加速此示例的执行速度。 在实践中，应该为每个类至少提供 100 张图像。 所有图像位于 *"../sample_data/imgs_recycling/"* 中名为“bowl”、“cup”、“cutlery”和“plate”的子目录内。

![Azure 机器学习数据集](media/how-to-build-deploy-image-classification-models/recycling_examples.jpg)

## <a name="next-steps"></a>后续步骤

请通过以下文章详细了解用于计算机视觉的 Azure 机器学习包：

+ 了解如何[提高此模型的准确性](how-to-improve-accuracy-for-computer-vision-models.md)。

+ 阅读[包概述](https://aka.ms/aml-packages/vision)。

+ 浏览此包的[参考文档](https://docs.microsoft.com/python/api/overview/azure-machine-learning/computer-vision)。

+ 了解 [Azure 机器学习的其他 Python 包](reference-python-package-overview.md)。
