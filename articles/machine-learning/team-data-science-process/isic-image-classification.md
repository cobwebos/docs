---
title: 使用用于计算机视觉的 Azure 机器学习 (AML) 包 (AMLPCV) 和 Team Data Science Process (TDSP) 进行图像分类 | Microsoft Docs
description: 介绍如何使用 TDSP (Team Data Science Process) 和 AMLPCV 进行图像分类
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
ms.openlocfilehash: a3dcfd8a9292d31c7342b8d50ec58c0da53318d3
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837212"
---
# <a name="skin-cancer-image-classification-with-azure-machine-learning-aml-package-for-computer-vision-amlpcv-and-team-data-science-process-tdsp"></a>使用用于计算机视觉的 Azure 机器学习 (AML) 包 (AMLPCV) 和 Team Data Science Process (TDSP) 对皮肤癌图像进行分类

## <a name="introduction"></a>介绍

本文介绍如何使用[用于计算机视觉的 Azure 机器学习包 (AMLPCV)](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest) 来训练、测试和部署图像分类模型。 本示例将在 [Azure Machine Learning Workbench](https://docs.microsoft.com/en-us/azure/machine-learning/service/quickstart-installation) 中使用 TDSP 结构和模板。 本演练中提供了完整的示例。 它使用 [CNTK](https://www.microsoft.com/en-us/cognitive-toolkit/) 作为深度学习框架，并在 [数据科学 VM](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview) GPU 计算机上进行训练。 部署使用 Azure ML 实施 CLI。

计算机视觉领域中的许多应用程序都可归为解决图像分类问题。 这包括建立可回答“图像中是否存在对象？”等问题的模型 （对象可以是狗、汽车或船）等问题，以及更为复杂的问题，例如“此患者的视网膜扫描显示眼疾严重程度属于哪一级别？” AMLPCV 简化了图像分类数据处理和建模管道。 

## <a name="link-to-github-repository"></a>GitHub 存储库链接
我们在此处提供了有关该示例的摘要文档。 [GitHub 站点](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification)上提供了内容更广泛的文档。

## <a name="team-data-science-process-tdsp-walkthrough-with-amlpcv"></a>使用 AMLPCV 进行的 Team Data Science Process (TDSP) 演练

此演练使用 [Team Data Science Process (TDSP)](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/overview) 生命周期。

本演练涵盖以下生命周期步骤：

### <a name="1-data-acquisionhttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode01dataacquisitionandunderstanding"></a>[1.获取数据](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/01_data_acquisition_and_understanding)
ISIC 数据集可用于图像分类任务。 ISIC（国际皮肤成像协作）是学术界和业界间展开的合作，旨在促进数字皮肤成像的应用，研究和帮助减少黑色素瘤死亡率。 [ISIC 档案](https://isic-archive.com/#images)中包含带有良性或恶性标签的 13,000 多张皮肤病变图像。 我们从 ISIC 档案中下载了一张图像样本。

### <a name="2-modelinghttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode02modeling"></a>[2.建模](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/02_modeling)
在建模步骤，执行以下子步骤。 

<b>2.1 数据集创建</b><br>

为了在 AMLPCV 中生成数据集对象，需提供本地磁盘上的图像根目录。 

<b>2.2 图像可视化和批注</b><br>

可视化数据集对象中的图像，并根据需要更正某些标签。

<b>2.3 图像增强</b><br>

使用 [imgaug](https://github.com/aleju/imgaug) 库中所述的转换来增强数据集对象。

<b>2.4 DNN 模型定义</b><br>

定义训练步骤中使用的模型体系结构。 AMLPCV 支持六种不同的训练得来的深度神经网络模型：AlexNet、Resnet-18、Resnet-34 和 Resnet-50、Resnet-101 以及 Resnet-152。

<b>2.5 分类器训练</b><br>

使用默认或自定义参数训练神经网络。

<b>2.6 评估和可视化</b><br>

通过该子步骤，可在独立测试数据集中评估已训练模型的性能。 评估指标包括准确度、精确度和召回率，以及 ROC 曲线。

相应的 Jupyter Notebook 中详细介绍了这些子步骤。 我们还提供了参数（如学习速率、小批量大小和中途退出率）调整指南，以进一步提高模型性能。

### <a name="3-deploymenthttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode03deployment"></a>[3.部署](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/03_deployment)

此步骤用于实施建模步骤生成的模型。 它介绍了实施先决条件和设置。 最后还解释了 Web 服务的消耗。 通过本教程，可学习如何使用 AMLPCV 构建深度学习模型，并在 Azure 中实施该模型。

## <a name="next-steps"></a>后续步骤
在[用于计算机视觉的 Azure 机器学习包 (AMLPCV)](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest) 和 [Team Data Science Process (TDSP)](https://aka.ms/tdsp) 中进一步了解相关信息以开始使用。


## <a name="references"></a>参考

* [用于计算机视觉的 Azure 机器学习包 (AMLPCV)](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest)
* [Azure Machine Learning Workbench](https://docs.microsoft.com/en-us/azure/machine-learning/service/quickstart-installation)
* [数据科学 VM](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview)

