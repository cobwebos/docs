---
title: 什么是工作区
titleSuffix: Azure Machine Learning service
description: 了解哪些工作区是，为什么需要一个用于 Azure 机器学习服务。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/21/2019
ms.openlocfilehash: 5785bf8f6538f1d91e7a23178e29487ebee14f29
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2019
ms.locfileid: "65989828"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>什么是 Azure 机器学习工作区？

工作区是 Azure 机器学习服务的顶级资源。 它提供了一个集中的位置来处理使用 Azure 机器学习服务时创建的所有项目。

在工作区保留历史记录的训练运行，包括日志、 指标、 输出和您的脚本的快照。 使用此信息可以确定哪个训练运行产生最佳模型。  

您喜欢的模型后，您注册它的工作区。 使用已注册的模型和评分脚本将部署到 Azure 容器实例时，Azure Kubernetes 服务，或作为基于 REST 的 HTTP 终结点现场可编程门列阵 (FPGA)。 作为一个模块，还可以将模型部署到 Azure IoT Edge 设备。

## <a name="taxonomy"></a>分类 

下图演示了工作区的分类：

[![工作区分类](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png)](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png#lightbox)

关系图显示了一个工作区的以下组件：

+ 工作区可以包含[Notebook Vm](quickstart-run-cloud-notebook.md)，云资源配置了运行 Azure 机器学习所需的 Python 环境。
+ [用户角色](how-to-assign-roles.md)使您能够与其他用户、 团队或项目共享你的工作区。
+ [计算目标](concept-azure-machine-learning-architecture.md#compute-target)用于运行试验。
+ 当创建工作区中，[关联的资源](#resources)还为你创建。
+ [试验](concept-azure-machine-learning-architecture.md#experiment)是用于构建模型的训练运行。  您可以创建和运行使用试验
    + [Azure 机器学习的 Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)。
    + [自动化的机器学习试验 （预览版）](how-to-create-portal-experiments.md)在 Azure 门户中的部分。
    + [（预览版） 的可视界面](ui-concept-visual-interface.md)。
+ [管道](concept-azure-machine-learning-architecture.md#pipeline)是为定型集和重新训练模型的可重用工作流。
+ [数据集](concept-azure-machine-learning-architecture.md#dataset)用于模型训练和管道创建的数据管理提供帮助。
+ 你想要部署的模型后，您创建[已注册的模型](concept-azure-machine-learning-architecture.md#model-registry)。
+ 使用已注册的模型和评分脚本来创建[部署](concept-azure-machine-learning-architecture.md#image-registry)。

## <a name="tools-for-workspace-interaction"></a>工作区交互的工具

按以下方式，可以与你的工作区进行交互：

+ Web:
    + [Azure 门户](https://azure.portal.com)
    + [可视界面 （预览版）](ui-concept-visual-interface.md)
+ 在 Python 中使用 Azure 机器学习[SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
+ 使用 Azure 机器学习命令行上[CLI 扩展](https://docs.microsoft.com/azure/machine-learning/service/reference-azure-machine-learning-cli)

## <a name="machine-learning-with-a-workspace"></a>机器学习工作区

机器学习任务读取和/或将项目写入到你的工作区。 

+ 运行试验来训练模型-写入试验工作区的运行的结果。
+ 使用自动化机器学习训练模型的训练结果写入工作区。
+ 在工作区中注册模型。
+ 部署模型-使用已注册的模型来创建部署。
+ 创建并运行可重用工作流。
+ 查看机器学习试验、 管道、 模型、 部署等项目。
+ 跟踪和监视模型。

## <a name="workspace-management"></a>工作区管理

此外可以执行以下工作区管理任务：

| 工作区管理任务   | 门户              | SDK        | CLI        |
|---------------------------|------------------|------------|------------|
| 创建工作区        | **&check;**     | **&check;** | **&check;** |
| 创建和管理计算资源    | **&check;**   | **&check;** |  **&check;**   |
| 管理工作区的访问权限    | **&check;**   | |  **&check;**    |
| 创建笔记本 VM | **&check;**   | |     |

通过服务入门[创建工作区](setup-create-workspace.md)。

## <a name="resources"></a> 关联的资源

创建新工作区时，它会自动创建工作区使用的几个 Azure 资源：

+ [Azure 容器注册表](https://azure.microsoft.com/services/container-registry/)：注册在训练期间和部署模型时使用的 Docker 容器。 若要最小化成本，ACR 是**延迟加载**创建部署映像之前。
+ [Azure 存储帐户](https://azure.microsoft.com/services/storage/)，用作工作区的默认数据存储。
+ [Azure Application Insights](https://azure.microsoft.com/services/application-insights/)：存储有关模型的监视信息。
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)：存储计算目标使用的机密和工作区所需的其他敏感信息。

> [!NOTE]
> 除创建新版本以外，还可以使用现有的 Azure 服务。

## <a name="next-steps"></a>后续步骤

若要开始使用 Azure 机器学习服务，请参阅：

+ [Azure 机器学习服务概述](overview-what-is-azure-ml.md)
+ [创建工作区](setup-create-workspace.md)
+ [管理工作区](how-to-manage-workspace.md)
+ [教程：训练模型](tutorial-train-models-with-aml.md)
