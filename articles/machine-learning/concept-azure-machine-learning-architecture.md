---
title: 体系结构和重要概念
titleSuffix: Azure Machine Learning
description: 了解构成 Azure 机器学习的体系结构、术语、概念和工作流。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 03/17/2020
ms.custom: seoapril2019, seodec18
ms.openlocfilehash: e70401bbaa97920163f3c7e76e32b9b9be2f5e72
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871476"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>Azure 机器学习的工作原理：体系结构和概念

了解 Azure 机器学习的体系结构、概念和工作流。 下图显示该服务的主要组件，以及使用该服务时的常规工作流：

![Azure 机器学习体系结构和工作流](./media/concept-azure-machine-learning-architecture/workflow.png)

## <a name="workflow"></a>工作流

机器学习模型工作流通常遵循以下顺序：

1. **训练**
    + 在**Python**、 **R**或可视化设计器中开发机器学习培训脚本。
    + 创建和配置**计算目标**。
    + 将**脚本提交**到配置的计算目标，以便在该环境中运行。 在训练过程中，脚本可以从**数据存储**读取或向其写入。 在训练过程中生成的日志和输出保存为**运行**于**工作区**中并在**试验**下分组。

1. **打包** - 找到满意的运行后，在**模型注册表**中注册持久化模型。

1. **验证** - **查询试验**了解当前和过去的运行中已记录的指标。 如果指标未指示所需结果，请循环回到步骤 1 并循环访问脚本。

1. **部署** - 开发一个使用该模型的评分脚本，并**将该模型部署**为 Azure 中的 **Web 服务**，或部署到 **IoT Edge 设备**。

1. **监视** - 监视已部署模型的训练数据集与推理数据之间的**数据偏移**。 必要时，循环回到步骤 1，以使用新的训练数据重新训练模型。

## <a name="tools-for-azure-machine-learning"></a>适用于 Azure 机器学习的工具

使用以下适用于 Azure 机器学习的工具：

+  使用[适用于 Python 的 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 来与任何 Python 环境中的服务交互。
+ 使用[适用于 R 的 Azure 机器学习 SDK](https://azure.github.io/azureml-sdk-for-r/reference/index.html) 来与任何 R 环境中的服务交互。
+ 使用 [Azure 机器学习 CLI](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli) 自动化机器学习活动。
+ 使用 [Azure 机器学习设计器（预览版）](concept-designer.md)执行工作流步骤且无需编写任何代码。


> [!NOTE]
> 本文定义了 Azure 机器学习使用的术语和概念，但未定义 Azure 平台的术语和概念。 有关 Azure 平台术语的详细信息，请参阅 [Microsoft Azure 词汇表](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology)。

## <a name="glossary"></a>术语表

* [活动](#activities)
* [工作区](#workspaces)
    * [试验](#experiments)
        * [Run](#runs) 
            * [运行配置](#run-configurations)
            * [快照](#snapshots)
            * [Git 跟踪](#github-tracking-and-integration)
            * [日志记录](#logging)
    * [ML 管道](#ml-pipelines)
    * [模型](#models)
        * [环境](#environments)
        * [训练脚本](#training-scripts)
        * [估算器](#estimators)
    * [Endpoints](#endpoints)
        * [Web 服务](#web-service-endpoint)
        * [IoT 模块](#iot-module-endpoints)
    * [数据集和数据存储](#datasets-and-datastores)
    * [计算目标](#compute-targets)

### <a name="activities"></a>活动

活动表示长时间运行的操作。 以下操作是活动的示例：

* 创建或删除计算目标
* 在计算目标上运行脚本

活动可通过 SDK 或 Web UI 提供通知，使你能够轻松监视这些操作的进度。

### <a name="workspaces"></a>工作区

[工作区](concept-workspace.md)是 Azure 机器学习的顶级资源。 它提供一个中心位置来处理使用 Azure 机器学习时创建的所有项目。 可与其他人共享工作区。 有关工作区的详细介绍，请参阅[什么是 Azure 机器学习工作区？](concept-workspace.md)。

### <a name="experiments"></a>试验

试验是指定的脚本中多个运行的分组。 它始终属于工作区。 当你提交运行时，需提供试验名称。 运行的信息存储在该试验下。 如果提交运行，并指定一个不存在的试验名称，则系统将使用新指定的名称自动创建一个新试验。

有关使用试验的示例，请参阅[教程：训练第一个模型](tutorial-1st-experiment-sdk-train.md)。

### <a name="runs"></a>运行次数

运行是训练脚本的单次执行。 试验通常包含多个运行。

Azure 机器学习在试验中记录所有运行并存储以下信息：

* 有关运行的元数据（时间戳、持续时间等）
* 脚本记录的指标
* 试验自动收集的或由你显式上传的输出文件
* 在运行之前包含脚本的目录的快照

提交脚本以训练模型时，会生成运行。 运行可以有零次或多次子级运行。 例如，顶级运行可以有两次子级运行，其中每个可以有其自己的子级运行。

### <a name="run-configurations"></a>运行配置

运行配置是一组指令，用于定义如何在指定的计算目标中运行脚本。 该配置包括一组广泛的行为定义，例如，是使用现有 Python 环境还是使用根据规范构建的 Conda 环境。

运行配置可以保存到包含训练脚本的目录内的文件中，或构造为内存中对象以及用于提交运行。

有关示例运行配置，请参阅[选择并使用计算目标来训练模型](how-to-set-up-training-targets.md)。

### <a name="snapshots"></a>快照

提交运行时，Azure 机器学习会将包含该脚本的目录压缩为 zip 文件并将其发送到计算目标。 然后解压缩 zip 文件并运行脚本。 Azure 机器学习还将该 zip 文件存储为快照，作为运行记录的一部分。 有权限访问工作区的任何用户都可以浏览运行记录并下载快照。

> [!NOTE]
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]

### <a name="github-tracking-and-integration"></a>GitHub 跟踪与集成

如果以本地 Git 存储库作为源目录开始训练运行，有关存储库的信息将存储在运行历史记录中。 这适用于使用估算器、ML 管道或脚本运行提交的运行。 此外，还适用于从 SDK 或机器学习 CLI 提交的运行。

有关详细信息，请参阅 [Azure 机器学习的 Git 集成](concept-train-model-git-integration.md)。

### <a name="logging"></a>Logging

开发解决方案时，请在 Python 脚本中使用 Azure 机器学习 Python SDK 记录任意指标。 运行后，查询指标以确定运行是否生成了要部署的模型。

### <a name="ml-pipelines"></a>ML 管道

使用机器学习管道可以创建和管理将各个机器学习阶段整合到一起的工作流。 例如，管道可以包括数据准备、模型训练、模型部署以及推理/评分阶段。 每个阶段可以包含多个步骤，每个步骤都能够以无人参与方式在各种计算目标中运行。 

管道步骤可重复使用，如果这些步骤的输出未发生更改，则可以运行，而无需重新运行前面的步骤。 例如，如果数据未更改，则无需重新运行高开销的数据准备步骤，即可重新训练模型。 管道还使数据科学家能够展开协作，同时可以处理机器学习工作流的不同环节。

有关机器学习管道与此服务的详细信息，请参阅[管道和 Azure 机器学习](concept-ml-pipelines.md)。

### <a name="models"></a>模型

简单地说，模型是一段接受输入并生成输出的代码。 创建机器学习模型将涉及选择算法、为其提供数据以及优化超参数。 培训是一个迭代过程，将生成经过培训的模型，它会封装模型在培训过程中学到的内容。

模型通过 Azure 机器学习中的运行生成。 还可以使用在 Azure 机器学习外部训练的模型。 可在 Azure 机器学习工作区中注册模型。

Azure 机器学习与框架无关。 创建模型时，可以使用任何流行的机器学习框架，例如 Scikit-learn、XGBoost、PyTorch、TensorFlow 和 Chainer。

有关使用 Scikit-learn 和估计器为模型定型的示例，请参阅[教程：使用 Azure 机器学习训练图像分类模型](tutorial-train-models-with-aml.md)。

**模型注册表**将跟踪 Azure 机器学习工作区中的所有模型。

模型按名称和版本标识。 每次使用与现有相同的名称注册模型时，注册表都会假定它是新版本。 该版本将递增并且新模型会以同一名称注册。

注册模型时，可以提供其他元数据标记，然后在搜索模型时使用这些标记。

> [!TIP]
> 已注册的模型是构成模型的一个或多个文件的逻辑容器。 例如，如果你有一个存储在多个文件中的模型，则可以在 Azure 机器学习工作区中将这些文件注册为单个模型。 注册后，可以下载或部署已注册的模型，并接收注册的所有文件。

无法删除在活动部署正在使用的已注册模型。

有关注册模型的示例，请参阅[使用 Azure 机器学习训练映像分类模型](tutorial-train-models-with-aml.md)。

### <a name="environments"></a>环境

Azure ML 环境用于指定在为数据准备、模型训练和模型服务创建可再现环境时所用的配置（Docker、Python、Spark 等）。 它们是 Azure 机器学习工作区中受到管理和版本控制的实体，可跨不同的计算目标支持可再现、可审核和可移植的机器学习工作流。

可以在本地计算上使用环境对象来开发训练脚本、在 Azure 机器学习计算上重复使用同一环境进行大规模的模型训练，甚至可以使用相同的环境部署模型。 

了解[如何创建和管理用于训练与推理的可重用 ML 环境](how-to-use-environments.md)。

### <a name="training-scripts"></a>定型脚本

若要定型模型，你可以指定包含培训脚本和关联文件的目录。 此外，还可指定一个试验名称，用于存储在训练期间收集的信息。 在训练期间，会将整个目录复制到训练环境（计算目标），并启动运行配置指定的脚本。 目录的快照同样存储在工作区中的试验下。

有关示例，请参阅[教程：使用 Azure 机器学习训练图像分类模型](tutorial-train-models-with-aml.md)。

### <a name="estimators"></a>估算器

为了便于使用流行框架进行模型训练，可以通过估算器类轻松构造运行配置。 可以创建并使用泛型[估算器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py)来提交使用所选任何学习框架（例如 scikit-learn）的训练脚本。

对于 PyTorch、TensorFlow 和 Chainer 任务，Azure 机器学习还提供相应的[PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py)、 [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)和[Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py)估算以简化使用这些框架的操作。

有关详细信息，请参阅以下文章：

* [用估算训练 ML 模型](how-to-train-ml-models.md)。
* [使用 Azure 机器学习大规模训练 Pytorch 深度学习模型](how-to-train-pytorch.md)
* [使用 Azure 机器学习大规模训练并注册 TensorFlow 模型](how-to-train-tensorflow.md)
* [使用 Azure 机器学习大规模训练并注册 Chainer 模型](how-to-train-ml-models.md)

### <a name="endpoints"></a>终结点

终结点是模型在 Web 服务（可托管于云中）中的实例化，或用于集成设备部署的 IoT 模块。

#### <a name="web-service-endpoint"></a>Web 服务终结点

将模型部署为 Web 服务时，可以在 Azure 容器实例、Azure Kubernetes 服务或 FPGA 上部署终结点。 可以从模型、脚本和关联的文件创建服务。 这些对象已放入到包含模型执行环境的基础容器映像中。 映像具有负载均衡的 HTTP 终结点，可接收发送到 Web 服务的评分请求。

如果已选择启用此功能，Azure 可通过收集 Application Insights 遥测数据或模型遥测数据帮助监视 Web 服务。 遥测数据仅供你访问，并且存储在 Application Insights 和存储帐户实例中。

如果已启用自动缩放，Azure 将自动缩放部署。

有关将模型部署为 Web 服务的示例，请参阅[在 Azure 容器实例中部署映像分类模型](tutorial-deploy-models-with-aml.md)。

#### <a name="iot-module-endpoints"></a>IoT 模块终结点

已部署 IoT 模块终结点是一个 Docker 容器，其中包含模型和关联脚本或应用程序，以及任何其他依赖项。 在边缘设备上使用 Azure IoT Edge 部署这些模块。

如果已启用监视，Azure 会从 Azure IoT Edge 模块内的模型中收集遥测数据。 遥测数据仅供你访问，并且存储在存储帐户实例中。

Azure IoT Edge 将确保模块正在运行并且监视托管它的设备。


### <a name="compute-instance-preview"></a><a name="compute-instance"></a>计算实例（预览版）

**Azure 机器学习计算实例**（前称为 Notebook VM）是完全托管式的基于云的工作站，其中包含为机器学习安装的多个工具和环境。 可将计算实例用作训练和推理作业的计算目标。 对于大型任务，具有多节点缩放功能的 [Azure 机器学习计算群集](how-to-set-up-training-targets.md#amlcompute)是更好的计算目标选项。

详细了解[计算实例](concept-compute-instance.md)。

### <a name="datasets-and-datastores"></a>数据集和数据存储

使用 **Azure 机器学习数据集**（预览版）可以更轻松地访问和处理数据。 数据集管理各种方案（例如模型训练和管道创建）中的数据。 使用 Azure 机器学习 SDK 可以访问底层存储、浏览数据，以及管理不同数据集定义的生命周期。

数据集提供多种方法用于处理常用格式的数据，例如使用 `from_delimited_files()` 或 `to_pandas_dataframe()`。

有关详细信息，请参阅[创建和注册 Azure 机器学习数据集](how-to-create-register-datasets.md)。  有关使用数据集的更多示例，请参阅[示例笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets-tutorial)。

**数据**存储是通过 Azure 存储帐户进行的存储提取。 数据存储可以使用 Azure blob 容器或 Azure 文件共享作为后端存储。 每个工作区都有默认数据存储，并且你可以注册其他数据存储。 使用 Python SDK API 或 Azure 机器学习 CLI 可从数据存储中存储和检索文件。

### <a name="compute-targets"></a>计算目标

在[计算目标](concept-compute-target.md)上，可以指定用于运行训练脚本或托管服务部署的计算资源。 此位置可以是你的本地计算机，也可以是基于云的计算资源。

详细了解[可用于训练和部署的计算目标](concept-compute-target.md)。

### <a name="next-steps"></a>后续步骤

若要开始使用 Azure 机器学习，请参阅：

* [什么是 Azure 机器学习？](overview-what-is-azure-ml.md)
* [创建 Azure 机器学习工作区](how-to-manage-workspace.md)
* [教程（第1部分）：定型模型](tutorial-train-models-with-aml.md)
