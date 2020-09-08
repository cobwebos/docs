---
title: 什么是 Azure 机器学习工作室？
description: Azure 机器学习工作室是 Azure 机器学习工作区的 Web 门户。 工作室结合了无代码和代码优先体验，以创建包容的数据科学平台。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: peterclu
ms.author: peterlu
ms.date: 08/24/2020
ms.openlocfilehash: 4f6252ffbb880e347a89594cb267fb99d576dc7a
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2020
ms.locfileid: "89039274"
---
# <a name="what-is-azure-machine-learning-studio"></a>什么是 Azure 机器学习工作室？

本文将介绍 Azure 机器学习工作室，这是 [Azure 机器学习](overview-what-is-azure-ml.md)中面向数据科学家开发人员的 Web 门户。 此工作室将无代码和代码优先体验结合起来，打造包容的数据科学平台。

本文内容：
>[!div class="checklist"]
> - 如何在工作室中[创作机器学习项目](#author-machine-learning-projects)。
> - 如何在工作室中[管理资产和资源](#manage-assets-and-resources)。
> - [Azure 机器学习工作室和机器学习工作室（经典）](#ml-studio-classic-vs-azure-machine-learning-studio)之间的差异。


## <a name="author-machine-learning-projects"></a>创作机器学习项目

该工作室提供多种创作体验，具体取决于类型项目和用户体验级别。

+ **Notebook**

  在直接集成到工作室中的托管 [Jupyter Notebook 服务器](how-to-run-jupyter-notebooks.md)中编写和运行自己的代码。 

+ **Azure 机器学习设计器（预览版）**

  使用设计器可在不编写任何代码的情况下训练和部署机器学习模型。 拖放数据集和模块以创建 ML 管道。 尝试[设计器教程](tutorial-designer-automobile-price-train-score.md)。

    ![Azure 机器学习设计器示例](media/concept-designer/designer-drag-and-drop.gif)

+ **自动化机器学习 UI（预览版）**

  了解如何通过易于使用的界面创建[自动化 ML 试验](tutorial-first-experiment-automated-ml.md)。 

  [![Azure 机器学习工作室导航窗格](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)

+ **数据标记**

    使用 [Azure 机器学习数据标签](how-to-create-labeling-projects.md)来高效地协调数据标签项目。

## <a name="manage-assets-and-resources"></a>管理资产和资源

直接在浏览器中管理机器学习资产。 在 SDK 和工作室之间的同一工作区中共享资产，以实现无缝体验。 使用工作室管理：

- 模型
- 数据集
- 数据存储
- 计算资源
- 笔记本
- 试验
- 运行日志
- 管道 
- 管道终结点

即使你是经验丰富的开发人员，工作室也可以简化你管理工作区资源的方式。

## <a name="ml-studio-classic-vs-azure-machine-learning-studio"></a>机器学习工作室（经典）与 Azure 机器学习工作室

机器学习工作室（经典）于 2015 年发布，是我们的第一个拖放式机器学习生成器。 它是一个只提供视觉体验的独立服务。 工作室（经典）不与 Azure 机器学习进行互操作。

Azure 机器学习是一种独立的新式服务，可提供完整的数据科学平台。 它同时支持代码优先和低代码体验。

Azure 机器学习工作室是 Azure 机器学习中的 Web 门户，其中包含用于项目创作和资产管理的低代码和无代码选项。 

我们建议新用户选择 Azure 机器学习而不是机器学习工作室（经典），以使用最新的数据科学工具。

### <a name="feature-comparison"></a>功能比较

下表总结了机器学习工作室（经典）和 Azure 机器学习之间的一些重要差异。

| 功能 | 机器学习工作室（经典版） | Azure 机器学习 |
|---| --- | --- |
| 拖放界面 | 经典体验 | 更新的体验 - [Azure 机器学习设计器（预览版）](concept-designer.md) <br/>（需要企业工作区） | 
| 代码 SDK | 不支持 | 与 [Azure 机器学习 Python](https://docs.microsoft.com/python/api/overview/azure/ml/) 和 [R](tutorial-1st-r-experiment.md) SDK 完全集成 |
| 试验 | 可缩放（10 GB 训练数据限制） | 使用计算目标进行缩放 |
| 训练计算目标 | 专用计算目标，仅限 CPU 支持 | 各种可自定义的[训练计算目标](concept-compute-target.md#train)。 包括 GPU 和 CPU 支持 | 
| 部署计算目标 | 专用 Web 服务格式，不可自定义 | 各种可自定义的[部署计算目标](concept-compute-target.md#deploy)。 包括 GPU 和 CPU 支持 |
| ML 管道 | 不支持 | 生成灵活的模块化[管道](concept-ml-pipelines.md)，用于自动完成工作流 |
| MLOps | 基本模型管理和部署 | 实体版本控制（模型、数据、工作流）、工作流自动化、与 CICD 工具集成，[等等](concept-model-management-and-deployment.md) |
| 模型格式 | 专用格式，仅限工作室（经典） | 多个受支持的格式，具体取决于训练作业类型 |
| 自动化模型训练和超参数优化 |  不支持 | [受支持](concept-automated-ml.md)。 代码优先和无代码选项。 | 
| 数据偏移检测 | 不支持 | [支持](how-to-monitor-datasets.md) |
| 数据标签项目 | 不支持 | [支持](how-to-create-labeling-projects.md) |


## <a name="next-steps"></a>后续步骤

请访问[工作室](https://ml.azure.com)，或浏览以下教程中的不同创作选项：  
  + [使用 Python 笔记本训练和部署模型](tutorial-1st-experiment-sdk-setup.md)
  + [使用自动化机器学习训练和部署模型](tutorial-first-experiment-automated-ml.md)  
  + [使用设计器训练和部署模型](tutorial-designer-automobile-price-train-score.md)

