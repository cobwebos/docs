---
title: 使用用于计算机视觉的 Azure 机器学习包和 Team Data Science Process (TDSP) 进行图像分类 | Microsoft Docs
description: 介绍如何使用 Team Data Science Process (TDSP) 和用于计算机视觉的 Azure 机器学习包进行图像分类。
services: machine-learning, team-data-science-process
documentationcenter: ''
author: xibingao
manager: deguhath
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: xibingao
ms.openlocfilehash: f9e88cfb7185845e96f287b39bebaaa24320f537
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2018
ms.locfileid: "36300783"
---
# <a name="skin-cancer-image-classification-with-the-azure-machine-learning-package-for-computer-vision-and-team-data-science-process"></a>使用用于计算机视觉的 Azure 机器学习包和 Team Data Science Process 进行皮肤癌图像分类

本文介绍如何使用[用于计算机视觉的 Azure 机器学习包](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest)来训练、测试和部署图像分类模型。 本示例在 [Azure Machine Learning Workbench](https://docs.microsoft.com/en-us/azure/machine-learning/service/quickstart-installation) 中使用 Team Data Science Process (TDSP) 结构和模板。 本演练提供完整的示例。 它使用 [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) 作为深度学习框架，而训练则在[数据科学虚拟机](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview) GPU 计算机上进行。 部署使用 Azure 机器学习操作化 CLI。

计算机视觉领域中的许多应用程序都可归为解决图像分类问题。 这包括建立可回答“图像中是否存在对象？”等简单问题的模型， 这里的对象可能是指狗、车或船。 此外还包括更复杂问题（例如“此患者的视网膜扫描显示眼疾严重程度属于哪一级别？”）的答案。 用于计算机视觉的 Azure 机器学习包简化了图像分类数据处理和建模管道。 

## <a name="link-to-the-github-repository"></a>GitHub 存储库的链接
本文是关于此示例的摘要文档。 [GitHub 站点](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification)上提供了内容更广泛的文档。

## <a name="team-data-science-process-walkthrough"></a>Team Data Science Process 演练

此演练使用 [Team Data Science Process](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/overview) 生命周期。 本演练涵盖以下生命周期步骤。

### <a name="1-data-acquisitionhttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode01dataacquisitionandunderstanding"></a>[1.数据采集](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/01_data_acquisition_and_understanding)
国际皮肤成像协作 (ISIC) 数据集用于图像分类任务。 ISIC 是学术界和业界间展开的合作，旨在促进数字皮肤成像的应用，通过研究来确保减少黑色素瘤死亡率。 [ISIC 存档](https://isic-archive.com/#images)中包含 13,000 多张标记为良性或恶性的皮肤病变图像。 请从 ISIC 存档中下载一张样本图像。

### <a name="2-modelinghttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode02modeling"></a>[2.建模](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/02_modeling)
在建模步骤，执行以下子步骤。

#### <a name="dataset-creation"></a>数据集创建

提供本地磁盘上图像的根目录，在用于计算机视觉的 Azure 机器学习包中生成数据集对象。 

#### <a name="image-visualization-and-annotation"></a>图像可视化和注释

可视化数据集对象中的图像，并根据需要更正标签。

#### <a name="image-augmentation"></a>图像增强

使用 [imgaug](https://github.com/aleju/imgaug) 库中所述的转换来增强数据集对象。

#### <a name="dnn-model-definition"></a>DNN 模型定义

定义训练步骤中使用的模型体系结构。 用于计算机视觉的 Azure 机器学习包支持六种预训练的深度神经网络模型：AlexNet、Resnet-18、Resnet-34、Resnet-50、Resnet-101 和 Resnet-152。

#### <a name="classifier-training"></a>分类器训练

使用默认或自定义参数训练神经网络。

#### <a name="evaluation-and-visualization"></a>评估和可视化

提供的功能可以在独立测试数据集中评估已训练模型的性能。 评估指标包括准确度、精确度、召回率，以及 ROC 曲线。

相应的 Jupyter Notebook 中详细介绍了这些子步骤。 该 Notebook 还提供了参数（如学习速率、小批量大小和中途退出率）优化指南，以进一步提高模型性能。

### <a name="3-deploymenthttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode03deployment"></a>[3.部署](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/03_deployment)

此步骤用于使建模步骤生成的模型可操作。 它介绍了先决条件和所需设置。 此外还解释了 Web 服务的使用。 本教程介绍的是如何使用用于计算机视觉的 Azure 机器学习包构建深度学习模型，并在 Azure 中使该模型可操作。

## <a name="next-steps"></a>后续步骤
- 阅读有关[用于计算机视觉的 Azure 机器学习包](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest)的其他文档。
- 阅读 [Team Data Science Process](https://aka.ms/tdsp) 文档，了解如何入门。


## <a name="references"></a>参考

* [Azure Machine Learning package for computer vision](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest)（用于计算机视觉的 Azure 机器学习包）
* [Azure Machine Learning Workbench](https://docs.microsoft.com/en-us/azure/machine-learning/service/quickstart-installation)
* [数据科学虚拟机](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview)

