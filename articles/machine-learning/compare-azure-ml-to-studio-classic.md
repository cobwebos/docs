---
title: Azure 机器学习与机器学习工作室（经典版）
description: Azure 机器学习和机器学习工作室（经典版）有何区别？
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 03/25/2020
ms.openlocfilehash: 5577a9847ff405397c553028a6dfdf2df80d03fd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "80371845"
---
# <a name="azure-machine-learning-vs-machine-learning-studio-classic"></a>Azure 机器学习与机器学习工作室（经典版）

本文介绍 Azure 机器学习和机器学习工作室（经典版）的区别。 

Azure 机器学习提供 Python 和 R SDK 以及  “拖放”设计器，用于构建和部署机器学习模型。 工作室（经典版）仅提供独立的拖放体验。

建议新用户选择 Azure 机器学习，以便获取最广泛的先进机器学习工具。

## <a name="quick-comparison"></a>快速比较

下表总结了 Azure 机器学习和工作室（经典版）之间的一些重要差异：

| | 机器学习工作室（经典版） | Azure 机器学习 |
|---| --- | --- |
| 拖放界面 | 支持 | 支持 - [Azure 机器学习设计器（预览版）](concept-designer.md) | 
| 试验 | 可缩放（10 GB 训练数据限制） | 使用计算目标进行缩放 |
| 训练计算目标 | 专用计算目标，仅限 CPU 支持 | 各种可自定义的[训练计算目标](concept-compute-target.md#train)。 包括 GPU 和 CPU 支持 | 
| 部署计算目标 | 专用 Web 服务格式，不可自定义 | 各种可自定义的[部署计算目标](concept-compute-target.md#deploy)。 包括 GPU 和 CPU 支持 |
| ML 管道 | 不支持 | 生成灵活的模块化[管道](concept-ml-pipelines.md)，用于自动完成工作流 |
| MLOps | 基本模型管理和部署 | 实体版本控制（模型、数据、工作流）、工作流自动化、与 CICD 工具集成，[等等](concept-model-management-and-deployment.md) |
| 模型格式 | 专用格式，仅限工作室（经典版） | 多个受支持的格式，具体取决于训练作业类型 |
| 自动化模型训练和超参数优化 |  不支持 | [在 SDK 和可视化工作区中受支持](concept-automated-ml.md) | 
| 数据偏移检测 | 不支持 | [在 SDK 和可视化工作区中受支持](how-to-monitor-datasets.md) |


## <a name="migrate-from-machine-learning-studio-classic"></a>从机器学习工作室（经典版）迁移

目前没有办法将工作室（经典版）资产迁移到 Azure 机器学习设计器（预览版）。 当前的工作室（经典版）用户可以继续使用其机器学习资产。 但是，我们建议所有用户考虑使用此设计器，它提供用户熟悉的拖放式体验和改进的工作流以及可伸缩性、版本控制和企业安全性。 

## <a name="get-started-with-azure-machine-learning"></a>开始使用 Azure 机器学习

以下资源可帮助你开始 Azure 机器学习。 

- 请阅读 [Azure 机器学习概述](overview-what-is-azure-ml.md)。

- 创建[第一个通过 Python SDK 进行的试验](tutorial-1st-experiment-sdk-setup.md)。

- [创建第一个设计器管道](tutorial-designer-automobile-price-train-score.md)以预测汽车价格。

![Azure 机器学习设计器示例](media/concept-designer/designer-drag-and-drop.gif)

## <a name="next-steps"></a>后续步骤

除了设计器中的拖放式功能外，Azure 机器学习还提供了其他工具：  
  + [使用 Python 笔记本训练和部署 ML 模型](tutorial-1st-experiment-sdk-setup.md)
  + [使用 R Markdown 训练和部署 ML 模型](tutorial-1st-r-experiment.md) 
  + [使用自动化机器学习训练和部署 ML 模型](tutorial-first-experiment-automated-ml.md)  
  + [使用机器学习 CLI 训练和部署模型](tutorial-train-deploy-model-cli.md)

