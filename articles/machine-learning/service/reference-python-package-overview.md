---
title: 适用于 Azure 机器学习的 Python 包
description: 了解适用于 Azure 机器学习用户的 Python 包。
ms.service: machine-learning
ms.component: studio
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jmartens
author: j-martens
ms.date: 05/07/2018
ms.openlocfilehash: 7a8f9e9d757aaab04706dd3b7dc2b962b3a04130
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2018
ms.locfileid: "34833257"
---
# <a name="python-packages-for-azure-machine-learning"></a>适用于 Azure 机器学习的 Python 包

了解 Microsoft 提供的专用于 Azure 机器学习的 Python 包。 可以将这些库和函数与其他开源包或第三方包结合使用，但若要使用专有包，必须以可提供解释器的服务或计算机为基础来运行 Python 代码。

Azure 机器学习包是**适用于 Azure 机器学习的 Python pip 可安装扩展**，方便数据科学家和 AI 开发人员针对各种域快速生成和部署高度准确的机器学习和深度学习模型。

<a name="amlpcv"></a>
## <a name="azure-ml-package-for-computer-vision"></a>适用于计算机视觉的 Azure ML 包

有了适用于计算机视觉的 Azure ML 包，就可以生成、优化和部署适用于图像分类、对象检测和图像相似性的深度学习模型。

对于此包，请尝试下述后续步骤：
1. [下载](https://aka.ms/aml-packages/vision/download)包。
1. 阅读[安装文档](https://aka.ms/aml-packages/vision)。
1. 通过 Jupyter Notebook [生成和部署计算机视觉模型](how-to-build-deploy-image-classification-models.md)。
1. 浏览包[参考文档](https://aka.ms/aml-packages/vision)。

<a name="amlpf"></a>
## <a name="azure-ml-package-for-forecasting"></a>适用于预测的 Azure ML 包

有了适用于预测的 Azure ML 包，就可以针对财务和需求预测方案创建和部署时序预测模型。

对于此包，请尝试下述后续步骤：
1. [下载](https://aka.ms/aml-packages/forecasting/download)包。
1. 阅读[安装文档](https://aka.ms/aml-packages/forecasting)。
1. 通过 Jupyter Notebook [生成和部署预测模型](how-to-build-deploy-forecast-models.md)。
1. 浏览包[参考文档](https://aka.ms/aml-packages/forecasting)。

<a name="amlpta"></a>
## <a name="azure-ml-package-for-text-analytics"></a>适用于文本分析的 Azure ML 包

有了适用于文本分析的 Azure ML 包，就可以生成适用于文本分类、自定义实体提取和单词嵌入的文本深度学习模型。

对于此包，请尝试下述后续步骤：
1. [下载](https://aka.ms/aml-packages/text/download)包。
1. 阅读[安装文档](https://aka.ms/aml-packages/text)。
1. 通过 Jupyter Notebook [生成和部署文本分类模型](how-to-build-deploy-text-classification-models.md)。
1. 浏览包[参考文档](https://aka.ms/aml-packages/text)。

<a name="amlrealtimeai"></a>
## <a name="amlrealtimeai-fpga-acceleration"></a>amlrealtimeai（FPGA 加速）

有了 Azure 机器学习加速包，数据科学家和 AI 开发人员就可以使用量化版 ResNet 50 来抽取图像特征，根据这些特征将分类器定型，然后将模型部署到 Azure 的[现场可编程门阵列 (FPGA)](concept-accelerate-with-fpgas.md)，以便进行超低延迟推断。

对于此包，请尝试下述后续步骤：
1. [下载](https://aka.ms/aml-real-time-ai-package)包。
1. 阅读[安装文档](reference-fpga-package-overview.md)。
1. [将模型部署到 FPGA](how-to-deploy-fpga-web-service.md)。

