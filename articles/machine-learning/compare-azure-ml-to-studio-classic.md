---
title: Azure 机器学习与机器学习工作室（经典版）
description: Azure 机器学习与机器学习工作室（经典版）的不同之处
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 10/29/2019
ms.openlocfilehash: 44fe7d4e51e50cdb3d1f1ed598f760113fb623bd
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490158"
---
# <a name="how-azure-machine-learning-differs-from-ml-studio-classic"></a>Azure 机器学习与机器学习工作室（经典版）的不同之处

本文将 Azure 机器学习与机器学习工作室（经典版）的功能和接口进行了比较。 

## <a name="about-ml-studio-classic"></a>关于机器学习工作室（经典版）
[机器学习工作室（经典版）](/studio/what-is-ml-studio.md)是一个协作型拖放式可视工作区，无需编写代码即可在其中生成、测试和部署机器学习解决方案。 它使用预先生成和预先配置的机器学习算法、数据处理模块和专用计算平台。

## <a name="about-azure-machine-learning"></a>关于 Azure 机器学习

同时，[Azure 机器学习](/service/overview-what-is-azure-ml.md)提供称为设计器（预览版）的 Web 界面以及一些 SDK 和 CLI，可以快速准备数据，以及对机器学习模型进行训练和部署  。 通过 Azure 机器学习可以获得缩放性、多个框架支持、高级 ML 功能，如自动机器学习和管道支持。

Azure 机器学习设计器提供与工作室（经典版）类似的拖放体验。 但是，不像工作室（经典版）的专用计算平台，此设计器使用你自己的计算资源，可缩放，并且已完全集成到 Azure 机器学习中。  

> [!TIP]
> 鼓励当前正在使用或评估机器学习工作室（经典版）的客户尝试 [Azure 机器学习设计器](https://docs.microsoft.com/azure/machine-learning/service/ui-concept-visual-interface)（预览版），它提供拖放 ML 模块以及  可伸缩性、版本控制和企业安全性。

## <a name="comparison-azure-machine-learning-vs-ml-studio-classic"></a>比较：Azure 机器学习与机器学习工作室（经典版）

这是一个快速比较。

||  Azure 机器学习设计器|工作室（经典版） |
|---| --- | --- |
||设计器处于预览版阶段，Azure 机器学习已正式发布|正式发布 (GA) | 
|拖放界面| 是 | 是|
|试验| 使用计算目标进行缩放|规模（10GB 训练数据限制） | 
|界面的模块|  多个常用模块|很多 |
|训练计算目标| AML 计算 (GPU/CPU)|专用计算目标，仅 CPU|
|推断计算目标| 用于实时推理的 Azure Kubernetes 服务 <br/>用于批处理推理的 AML 计算|专用 Web 服务格式，不可自定义 | 
|ML 管道| 管道创作 <br/> 已发布管道 <br/> 管道终结点 <br/> [详细了解 ML 管道](service/concept-ml-pipelines.md)|不支持 | 
|机器学习操作| 可配置的部署、模型和管道版本控制|基本模型管理和部署 | 
|模型| 标准格式，因训练作业而异|专有的非可移植格式。| 
|自动化模型训练|尚不在设计器中，但可以通过接口和 Sdk 使用。| 否 | 

## <a name="get-started-with-azure-machine-learning-designer"></a>Azure 机器学习设计器入门

以下资源可帮助你开始 Azure 机器学习

- 请阅读 [Azure 机器学习概述](service/tutorial-first-experiment-automated-ml.md) 

- [创建第一个设计器管道](service/tutorial-designer-automobile-price-train-score.md)以预测汽车价格。


![Azure 机器学习设计器示例](service/media/concept-ml-pipelines/visual-design-surface.gif)

## <a name="next-steps"></a>后续步骤

除了设计器中的拖放式功能外，Azure 机器学习还提供了其他工具：  
  + [使用 Python 笔记本训练和部署 ML 模型](./service/tutorial-1st-experiment-sdk-setup.md)
  + [使用 R Markdown 训练和部署 ML 模型](./service/tutorial-1st-r-experiment.md) 
  + [使用自动化机器学习训练和部署 ML 模型](./service/tutorial-designer-automobile-price-train-score.md) 
  + [使用机器学习 CLI 训练和部署模型](./service/tutorial-train-deploy-model-cli.md)

