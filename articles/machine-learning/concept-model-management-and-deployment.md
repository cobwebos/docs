---
title: MLOps： ML 模型管理
titleSuffix: Azure Machine Learning
description: '了解 Azure 机器学习（MLOps）的模型管理。 部署、管理和监视模型，以便不断改善它们。 '
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: jpe316
ms.author: jordane
ms.date: 11/22/2019
ms.custom: seodec18
ms.openlocfilehash: e7c1f3384b88de7b58457251cf72b2815267cb29
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/28/2019
ms.locfileid: "75541694"
---
# <a name="mlops-model-management-deployment-and-monitoring-with-azure-machine-learning"></a>MLOps：模型管理、部署和监视 Azure 机器学习

本文介绍如何使用 Azure 机器学习来管理模型的生命周期。 Azure 机器学习使用机器学习操作（MLOps）方法。 MLOps 提高了机器学习解决方案的质量和一致性。 

Azure 机器学习提供以下 MLOps 功能：

- **创建可重复的 ML 管道**。 管道使你可以为数据准备、定型和评分过程定义可重复和可重复使用的步骤。
- **从任何位置注册、打包和部署模型**，并跟踪使用该模型所需的相关元数据。
- **捕获捕获端到端 ML 生命周期所需的管理数据**，包括发布模型的人员、进行更改的原因以及在生产环境中部署或使用模型的时间。
- **通知和警报 ML 生命周期中的事件**，例如试验完成、模型注册、模型部署和数据偏差检测。
- **监视 ml 应用程序的操作和 ml 相关的问题**。 比较定型与推理之间的模型输入，探索特定于模型的指标，并提供有关 ML 基础结构的监视和警报。
- **通过 Azure 机器学习和 Azure DevOps 自动执行端到端 ML 生命周期**，以频繁更新模型、测试新模型，并随其他应用程序和服务一起不断推出新的 ML 模型。

## <a name="create-reproducible-ml-pipelines"></a>创建可重复的 ML 管道

使用 Azure 机器学习的 ML 管道将模型定型过程中涉及的所有步骤汇聚在一起。

ML 管道可包含从数据准备到功能提取到超参数优化到模型评估的步骤。 有关详细信息，请参阅[ML 管道](service/concept-ml-pipelines.md)。

如果使用[设计器](service/concept-designer.md)创建 ML 管道，则可以随时单击设计器页右上角的 **"..."** ，然后选择 "**克隆**"。 通过克隆管道，可以在不丢失旧版本的情况下循环访问管道设计。  

## <a name="register-package-and-deploy-models-from-anywhere"></a>从任何位置注册、打包和部署模型

### <a name="register-and-track-ml-models"></a>注册和跟踪 ML 模型

通过模型注册，可在 Azure 云的工作区中存储模型并确定模型版本。 使用模型注册表，可轻松组织和跟踪定型的模型。

> [!TIP]
> 已注册的模型是组成模型的一个或多个文件的逻辑容器。 例如，如果您有一个存储在多个文件中的模型，则可以将它们作为一个模型注册到 Azure 机器学习工作区中。 注册后，可以下载或部署已注册的模型，并接收已注册的所有文件。

按名称和版本标识已注册的模型。 每次使用与现有名称相同的名称来注册模型时，注册表都会将版本递增。 注册期间可以提供其他元数据标记。 然后在搜索模型时使用这些标记。 Azure 机器学习支持可使用 Python 3.5.2 或更高版本加载的任何模型。

> [!TIP]
> 你还可以注册在 Azure 机器学习之外训练的模型。

不能删除在活动部署中使用的已注册模型。
有关详细信息，请参阅[部署模型](how-to-deploy-and-where.md#registermodel)的注册模型部分。

### <a name="package-and-debug-models"></a>包和调试模型

在将模型部署到生产环境之前，会将其打包到 Docker 映像中。 在大多数情况下，映像创建会在部署期间自动执行。 可以手动指定映像。

如果遇到部署问题，可以在本地开发环境中进行部署，以便进行故障排除和调试。

有关详细信息，请参阅[部署模型](how-to-deploy-and-where.md#registermodel)和[排查部署问题](how-to-troubleshoot-deployment.md)。

### <a name="validate-and-profile-models"></a>验证和分析模型

Azure 机器学习可以使用分析来确定部署模型时要使用的理想 CPU 和内存设置。 在此过程中，将使用为分析过程提供的数据来进行模型验证。

### <a name="convert-and-optimize-models"></a>转换和优化模型

将模型转换为[开放式神经网络交换](https://onnx.ai)（ONNX）可以提高性能。 平均情况下，转换为 ONNX 可能会提高性能的2倍。

有关 ONNX 与 Azure 机器学习的详细信息，请参阅[创建和加速 ML 模型](concept-onnx.md)一文。

### <a name="use-models"></a>使用模型

训练的机器学习模型作为 web 服务部署到云中或本地。 你还可以将模型部署到 Azure IoT Edge 设备。 部署将 CPU、GPU 或现场可编程入口阵列（FPGA）用于推断。 你还可以使用 Power BI 中的模型。

使用模型作为 web 服务或 IoT Edge 设备时，需要提供以下项：

* 用于对提交到服务/设备的数据进行评分的模型。
* 一个项脚本。 此脚本接受请求，使用模型对数据进行评分并返回响应。
* Conda 环境文件，用于描述模型和条目脚本所需的依赖项。
* 模型和输入脚本所需的任何其他资产，如文本、数据等。

还提供目标部署平台的配置。 例如，在部署到 Azure Kubernetes 服务时，VM 家族类型、可用内存和核心数。

创建映像时，还会添加 Azure 机器学习所需的组件。 例如，运行 web 服务并与 IoT Edge 进行交互所需的资产。

#### <a name="batch-scoring"></a>批处理评分
通过 ML 管道支持批处理评分。 有关详细信息，请参阅[大数据上的批处理预测](how-to-run-batch-predictions.md)。

#### <a name="real-time-web-services"></a>实时 web 服务

您可以在**web 服务**中使用您的模型以及以下计算目标：

* Azure 容器实例
* Azure Kubernetes 服务
* 本地开发环境

若要将模型部署为 web 服务，必须提供以下项：

* 模型的模型或系综。
* 使用模型所需的依赖项。 例如，接受请求并调用模型的脚本、conda 依赖项等。
* 描述如何以及在何处部署模型的部署配置。

有关详细信息，请参阅[部署模型](how-to-deploy-and-where.md)。

#### <a name="iot-edge-devices"></a>IoT Edge 设备

可以通过**Azure IoT Edge 模块**将模型与 IoT 设备一起使用。 IoT Edge 模块部署到硬件设备上，该设备在设备上启用推理或模型评分。

有关详细信息，请参阅[部署模型](how-to-deploy-and-where.md)。

### <a name="analytics"></a>分析

Microsoft Power BI 支持使用机器学习模型进行数据分析。 有关详细信息，请参阅[Power BI （预览版）中的 Azure 机器学习集成](https://docs.microsoft.com/power-bi/service-machine-learning-integration)。

## <a name="capture-the-governance-data-required-for-capturing-the-end-to-end-ml-lifecycle"></a>捕获捕获端到端 ML 生命周期所需的管理数据

Azure ML 使你能够跟踪所有 ML 资产的端到端审核记录。 具体如下：

- Azure ML[与 Git 集成](how-to-set-up-training-targets.md#gitintegration)，以跟踪有关代码来源的存储库/分支/提交的信息。
- [AZURE ML 数据集](how-to-create-register-datasets.md)可帮助你跟踪、分析和版本数据。 
- Azure ML 运行历史记录存储用于定型模型的代码、数据和计算的快照。
- Azure ML 模型注册表捕获与模型关联的所有元数据（这会对它进行定型，以便在部署时进行定型）。

## <a name="notify-automate-and-alert-on-events-in-the-ml-lifecycle"></a>针对 ML 生命周期中的事件发出通知、自动执行和警报
Azure ML 将关键事件发布到 Azure EventGrid，可用于在 ML 生命周期中通知和自动执行事件。 有关详细信息，请参阅[此文档](how-to-use-event-grid.md)。


## <a name="monitor-for-operational--ml-issues"></a>监视操作 & ML 问题

利用监视功能，您可以了解要发送到您的模型的数据以及返回的预测。

此信息可帮助你了解模型的使用方式。 收集的输入数据在训练模型的未来版本时也可能会很有用。

有关详细信息，请参阅[如何启用模型数据收集](how-to-enable-data-collection.md)。

## <a name="automate-the-ml-lifecycle"></a>自动执行 ML 生命周期 

您可以使用 GitHub 和 Azure Pipelines 来创建用于训练模型的持续集成过程。 在典型方案中，当数据科研人员将更改签入项目的 Git 存储库时，Azure 管道将开始训练运行。 然后，可以检查运行的结果，以查看定型模型的性能特征。 还可以创建一个管道，用于将模型部署为 web 服务。

使用[Azure 机器学习扩展](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml)可以更轻松地使用 Azure Pipelines。 它为 Azure Pipelines 提供了以下增强功能：

* 定义服务连接时启用工作区选择。
* 允许在定型管道中创建的定型模型触发发布管道。

有关将 Azure Pipelines 与 Azure 机器学习配合使用的详细信息，请参阅使用 Azure Pipelines 文章和[Azure 机器学习 MLOps](https://aka.ms/mlops)存储库实现[ML 模型的持续集成和部署](/azure/devops/pipelines/targets/azure-machine-learning)。

## <a name="next-steps"></a>后续步骤

阅读并浏览以下资源了解详细信息：

+ [& 如何 Azure 机器学习部署模型](how-to-deploy-and-where.md)

+ [教程：在 ACI 中部署图像分类模型](tutorial-deploy-models-with-aml.md)。

+ [端到端 MLOps 示例存储库](https://github.com/microsoft/MLOps)

+ [具有 Azure Pipelines 的 ML 型号的 CI/CD](/azure/devops/pipelines/targets/azure-machine-learning)

+ 创建使用已[部署模型](how-to-consume-web-service.md)的客户端

+ [规模化机器学习](/azure/architecture/data-guide/big-data/machine-learning-at-scale)

+ [Azure AI 参考体系结构 & 最佳实践代表](https://github.com/microsoft/AI)
