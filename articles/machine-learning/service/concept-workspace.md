---
title: 什么是工作区
titleSuffix: Azure Machine Learning service
description: 工作区是 Azure 机器学习服务的顶级资源。 它保留所有定型运行的历史记录, 包括日志、指标、输出和脚本的快照。 您可以使用此信息来确定哪个训练运行生成最佳模型
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/21/2019
ms.openlocfilehash: fc3f88e627e0ce19195f6df947d4f11f5f8a73ae
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2019
ms.locfileid: "68772751"
---
# <a name="what-is-an-azure-machine-learning-service-workspace"></a>什么是 Azure 机器学习服务工作区？

工作区是 Azure 机器学习服务的顶级资源, 提供了一个集中的位置来处理使用 Azure 机器学习服务时创建的所有项目。  工作区保存所有定型运行的历史记录, 包括日志、指标、输出和脚本的快照。 使用此信息可以确定哪个训练运行产生最佳模型。  

使用所需的模型后, 可将其注册到工作区。 然后, 使用已注册的模型和评分脚本, 将其部署到 Azure 容器实例、Azure Kubernetes 服务或基于 REST 的 HTTP 终结点作为现场可编程的入口数组 (FPGA)。 你还可以将模型部署到 Azure IoT Edge 设备作为模块。

## <a name="taxonomy"></a>分类 

下图演示了工作区的分类：

[![工作区分类](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png)](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png#lightbox)

此图显示了工作区的以下组件:

+ 工作区可以包含[笔记本 vm](tutorial-1st-experiment-sdk-setup.md)、使用运行 Azure 机器学习所需的 Python 环境配置的云资源。
+ [用户角色](how-to-assign-roles.md)使你可以与其他用户、团队或项目共享你的工作区。
+ [计算目标](concept-azure-machine-learning-architecture.md#compute-targets)用于运行试验。
+ 创建工作区时, 还会为您创建[关联的资源](#resources)。
+ [试验](concept-azure-machine-learning-architecture.md#experiments)是用于构建模型的训练运行。  可以通过创建和运行试验
    + [用于 Python 的 AZURE 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)。
    + Azure 门户中的 "[自动机器学习试验 (预览版)](how-to-create-portal-experiments.md) " 一节。
    + [可视界面 (预览)](ui-concept-visual-interface.md)。
+ [管道](concept-azure-machine-learning-architecture.md#ml-pipelines)是可重复使用的工作流, 用于定型和重新训练模型。
+ [数据集](concept-azure-machine-learning-architecture.md#datasets-and-datastores)有助于管理用于模型定型和管道创建的数据。
+ 如果有要部署的模型, 则需创建一个已注册的模型。
+ 使用已注册的模型和评分脚本来创建[部署](concept-azure-machine-learning-architecture.md#deployment)。

## <a name="tools-for-workspace-interaction"></a>用于工作区交互的工具

可以通过以下方式与工作区交互:

+ 在 web 上:
    + [Azure 门户](https://portal.azure.com)
    + [可视界面 (预览)](ui-concept-visual-interface.md)
+ 在 Python 中使用 Azure 机器学习[SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
+ 在命令行上使用 Azure 机器学习[CLI 扩展](https://docs.microsoft.com/azure/machine-learning/service/reference-azure-machine-learning-cli)

## <a name="machine-learning-with-a-workspace"></a>使用工作区进行机器学习

机器学习任务读取和/或写入您的工作区中的项目。 

+ 运行试验, 将模型写入试验运行结果定型到工作区。
+ 使用自动 ML 来训练模型-将定型结果写入工作区。
+ 在工作区中注册模型。
+ 部署模型-使用注册的模型来创建部署。
+ 创建并运行可重用工作流。
+ 查看机器学习项目, 如试验、管道、模型和部署。
+ 跟踪和监视模型。

## <a name="workspace-management"></a>工作区管理

你还可以执行以下工作区管理任务:

| 工作区管理任务   | 门户              | SDK        | CLI        |
|---------------------------|------------------|------------|------------|
| 创建工作区        | **&check;**     | **&check;** | **&check;** |
| 创建和管理计算资源    | **&check;**   | **&check;** |  **&check;**   |
| 管理工作区访问    | **&check;**   | |  **&check;**    |
| 创建笔记本 VM | **&check;**   | |     |

通过[创建工作区](setup-create-workspace.md)开始使用服务。

## <a name="resources"></a>关联的资源

创建新工作区时，它会自动创建工作区使用的几个 Azure 资源：

+ [Azure 容器注册表](https://azure.microsoft.com/services/container-registry/)：注册在训练期间和部署模型时使用的 Docker 容器。 若要最大程度地降低成本, 请在创建部署映像之前**延迟加载**ACR。
+ [Azure 存储帐户](https://azure.microsoft.com/services/storage/)，用作工作区的默认数据存储。  与笔记本 Vm 一起使用的 Jupyter 笔记本也存储在此处。
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
