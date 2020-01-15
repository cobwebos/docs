---
title: Azure 机器学习与机器学习工作室（经典版）
description: Azure 机器学习与机器学习工作室（经典版）有何不同
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 10/29/2019
ms.openlocfilehash: a808387c891b4078092c5cac1bf95df8472ff0f6
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/28/2019
ms.locfileid: "75529537"
---
# <a name="how-azure-machine-learning-differs-from-machine-learning-studio-classic"></a>Azure 机器学习与机器学习工作室（经典版）有何不同

本文将 Azure 机器学习与机器学习工作室（经典版）的功能和接口进行了比较。 

## <a name="about-machine-learning-studio-classic"></a>关于机器学习工作室（经典版）
[机器学习工作室（经典版）](studio/what-is-ml-studio.md)是一个协作型拖放式可视工作区，可以在其中生成、测试和部署机器学习解决方案，不需编写代码。 它使用预先生成和预先配置的机器学习算法、数据处理模块和专用计算平台。

## <a name="about-azure-machine-learning"></a>关于 Azure 机器学习

同时，[Azure 机器学习](overview-what-is-azure-ml.md)提供称为设计器（预览版）的 Web 界面以及一些 SDK 和 CLI，可以快速准备数据，以及对机器学习模型进行训练和部署  。 通过 Azure 机器学习可以获得缩放性、多个框架支持、高级 ML 功能，如自动机器学习和管道支持。

Azure 机器学习设计器提供与工作室（经典版）类似的拖放体验。 但是，不像工作室（经典版）的专用计算平台，此设计器使用你自己的计算资源，可缩放，并且已完全集成到 Azure 机器学习中。  

> [!TIP]
> 鼓励当前正在使用或评估机器学习工作室（经典版）的客户尝试 [Azure 机器学习设计器](https://docs.microsoft.com/azure/machine-learning/service/ui-concept-visual-interface)（预览版），它提供拖放 ML 模块以及  可伸缩性、版本控制和企业安全性。

## <a name="comparison-azure-machine-learning-vs-machine-learning-studio-classic"></a>比较：Azure 机器学习与机器学习工作室（经典版）

这是一个快速比较。

||  Azure 机器学习设计器|工作室（经典版） |
|---| --- | --- |
||设计器目前为预览版，Azure 机器学习已推出正式版|正式发布 (GA) | 
|拖放界面| 是 | 是|
|试验| 使用计算目标进行缩放|规模（10GB 训练数据限制） | 
|界面的模块| [许多流行模块](algorithm-module-reference/module-reference.md) | 很多 |
|训练计算目标| AML 计算 (GPU/CPU)|专用计算目标，仅 CPU|
|推断计算目标| 用于实时推理的 Azure Kubernetes 服务 <br/>用于批处理推理的 AML 计算|专用 Web 服务格式，不可自定义 | 
|ML 管道| 管道创作 <br/> 已发布管道 <br/> 管道终结点 <br/> [详细了解 ML 管道](concept-ml-pipelines.md)|不支持 | 
|机器学习操作| 可配置的部署、模型和管道版本控制|基本模型管理和部署 | 
|“模型”| 标准格式，因训练作业而异|专有的非可移植格式。| 
|自动化模型训练|尚不在设计器中，但可以通过接口和 Sdk 使用。| 否 | 

## <a name="get-started-with-azure-machine-learning"></a>开始使用 Azure 机器学习

以下资源可帮助你开始 Azure 机器学习

- 请阅读 [Azure 机器学习概述](tutorial-first-experiment-automated-ml.md) 

- [创建第一个设计器管道](tutorial-designer-automobile-price-train-score.md)以预测汽车价格。

![Azure 机器学习设计器示例](media/concept-designer/designer-drag-and-drop.gif)

## <a name="next-steps"></a>后续步骤

除了设计器中的拖放式功能外，Azure 机器学习还提供了其他工具：  
  + [使用 Python 笔记本训练和部署 ML 模型](tutorial-1st-experiment-sdk-setup.md)
  + [使用 R Markdown 训练和部署 ML 模型](tutorial-1st-r-experiment.md) 
  + [使用自动化机器学习训练和部署 ML 模型](tutorial-designer-automobile-price-train-score.md) 
  + [使用机器学习 CLI 训练和部署模型](tutorial-train-deploy-model-cli.md)

