---
title: 体系结构 & 重要概念
titleSuffix: Azure Machine Learning
description: 了解构成 Azure 机器学习的体系结构、术语、概念和工作流。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 07/12/2019
ms.custom: seodec18
ms.openlocfilehash: c886289f098eb41f4b215b4abc2e206db93a27f9
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2019
ms.locfileid: "71710138"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>Azure 机器学习的工作方式：体系结构和概念

了解 Azure 机器学习的体系结构、概念和工作流。 下图显示该服务的主要组件，以及使用该服务时的常规工作流：

![Azure 机器学习体系结构和工作流](./media/concept-azure-machine-learning-architecture/workflow.png)

## <a name="workflow"></a>工作流

机器学习模型工作流通常遵循此顺序:

1. **辆**
    + 在**Python**中或通过视觉对象界面开发机器学习培训脚本。
    + 创建和配置**计算目标**。
    + **将脚本提交**到配置的计算目标以在该环境中运行。 在训练期间，脚本可以读取或写入**数据存储**。 并且执行记录在**工作区**中保存为**运行**，并在**试验**下分组。

1. **包**-发现满意的运行后, 在**模型注册表**中注册持久化模型。

1. **验证查询从**当前和过去的运行中记录的度量值的**实验。**  -  如果指标未指示所需结果，请循环回到步骤 1 并循环访问脚本。

1. **部署**-开发一个计分脚本, 该脚本使用模型并将**模型部署**为 Azure 中的**web 服务**, 或部署到**IoT Edge 设备**。

1. **监视**-监视已部署模型的定型数据集和推理数据之间的**数据偏移**。 必要时, 请循环返回到步骤 1, 以通过新的定型数据重新训练模型。

## <a name="tools-for-azure-machine-learning"></a>用于 Azure 机器学习的工具

使用这些工具进行 Azure 机器学习:

+  与[适用于 python 的 AZURE 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)的任何 Python 环境中的服务进行交互。
+ 通过[AZURE 机器学习 CLI](https://docs.microsoft.com/azure/machine-learning/service/reference-azure-machine-learning-cli)自动执行机器学习活动。
+ 用[Azure 机器学习 VS Code 扩展](how-to-vscode-tools.md)在 Visual Studio Code 中编写代码
+ 使用[visual interface （预览版） for Azure 机器学习](ui-concept-visual-interface.md)在不编写代码的情况下执行工作流步骤。

> [!NOTE]
> 尽管本文定义 Azure 机器学习所使用的术语和概念，但它并不定义 Azure 平台的术语和概念。 有关 Azure 平台术语的详细信息，请参阅 [Microsoft Azure 词汇表](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology)。

## <a name="glossary"></a>术语表
+ <a href="#activities">活动</a>
+ <a href="#compute-targets">计算目标</a>
+ <a href="#datasets-and-datastores">数据集 & 数据存储</a>
+ <a href="#deployment">部署</a>
+ <a href="#environments">情形</a>
+ [估算](#estimators)
+ <a href="#experiments">试验</a>
+ <a href="#github-tracking-and-integration">Git 跟踪</a>
+ <a href="#iot-module-deployments">IoT 模块</a>
+ <a href="#logging">日志记录</a>
+ <a href="#ml-pipelines">ML 管道</a>
+ <a href="#models">机型</a>
+ <a href="#runs">Run</a>
+ <a href="#run-configurations">运行配置</a>
+ <a href="#snapshots">快照</a>
+ <a href="#training-scripts">训练脚本</a>
+ <a href="#web-service-deployments">Web 服务</a>
+ <a href="#workspaces">空间</a>

### <a name="activities"></a>activities

活动表示长时间运行的操作。 以下操作是活动的示例：

* 创建或删除计算目标
* 在计算目标上运行脚本

活动可通过 SDK 或 Web UI 提供通知，使你能够轻松监视这些操作的进度。

### <a name="compute-targets"></a>计算目标

[计算目标](concept-compute-target.md)使你可以指定运行训练脚本或托管服务部署的计算资源。 此位置可以是本地计算机, 也可以是基于云的计算资源。 计算目标可以在不更改代码的情况下轻松更改你的计算环境。

详细了解[培训和部署可用的计算目标](concept-compute-target.md)。

### <a name="datasets-and-datastores"></a>数据集和数据存储

**Azure 机器学习数据集**(预览版) 可让你更轻松地访问和处理数据。 数据集管理各种方案 (例如模型定型和管道创建) 中的数据。 使用 Azure 机器学习 SDK，你可以访问基础存储、浏览数据以及管理不同数据集定义的生命周期。

数据集提供了一些方法，用于处理常用格式的数据， `from_delimited_files()`例如`to_pandas_dataframe()`使用或。

有关详细信息, 请参阅[创建和注册 Azure 机器学习数据集](how-to-create-register-datasets.md)。  有关使用数据集的更多示例, 请参阅[示例笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets)。

**数据**存储是通过 Azure 存储帐户进行的存储提取。 数据存储可以使用 Azure blob 容器或 Azure 文件共享作为后端存储。 每个工作区都有默认数据存储，并且你可以注册其他数据存储。 使用 Python SDK API 或 Azure 机器学习 CLI 可从数据存储中存储和检索文件。

### <a name="deployment"></a>部署

部署是将模型实例化到可在云中托管的 web 服务, 也可以是集成设备部署的 IoT 模块。

#### <a name="web-service-deployments"></a>Web 服务部署

已部署的 Web 服务可以使用 Azure 容器实例、Azure Kubernetes 服务或 FPGA。 您可以从模型、脚本和关联的文件创建该服务。 这些封装在映像中, 后者提供 web 服务的运行时环境。 映像具有负载均衡的 HTTP 终结点，可接收发送到 Web 服务的评分请求。

如果已选择启用此功能，Azure 可通过收集 Application Insight 遥测数据或模型遥测数据帮助监视 Web 服务部署。 遥测数据仅供你访问，并且存储在 Application Insights 和存储帐户实例中。

如果已启用自动缩放，Azure 将自动缩放部署。

有关将模型部署为 Web 服务的示例，请参阅[在 Azure 容器实例中部署映像分类模型](tutorial-deploy-models-with-aml.md)。

#### <a name="iot-module-deployments"></a>IoT 模块部署

已部署 IoT 模块是一个 Docker 容器，包括模型和关联脚本或应用程序，以及任何其他依赖项。 使用边缘设备上的 Azure IoT Edge 部署这些模块。

如果已启用监视，Azure 会从 Azure IoT Edge 模块内的模型中收集遥测数据。 遥测数据仅供你访问，并且存储在存储帐户实例中。

Azure IoT Edge 将确保模块正在运行并且监视托管它的设备。

### <a name="environments"></a>环境

Azure ML 环境用于指定用于为数据准备、模型定型和模型服务创建可重复环境的配置（Docker/Python/Spark/等等）。 它们是在你的 Azure 机器学习工作区中管理和版本化的实体，可跨不同的计算目标启用可重复的、可审核的和便携式机器学习工作流。

你可以在本地计算上使用环境对象来开发训练脚本、在 Azure 机器学习计算上重复使用同一环境进行大规模模型训练，甚至使用相同的环境部署模型。 

了解[如何创建和管理可重用的 ML 环境](how-to-use-environments.md)以进行定型和推理。

### <a name="estimators"></a>估算

为了便于通过常用框架进行模型训练，估计器类使你能够轻松构造运行配置。 您可以创建和使用一般[估计器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py)来提交使用您选择的任何学习框架的培训脚本（如 scikit-learn）。

对于 PyTorch、TensorFlow 和 Chainer 任务，Azure 机器学习还提供相应的[PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py)、 [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)和[Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py)估算以简化使用这些框架的操作。

有关详细信息，请参阅以下文章：

* [用估算训练 ML 模型](how-to-train-ml-models.md)。
* [Azure 机器学习，按比例为 Pytorch 深度学习模型定型](how-to-train-pytorch.md)。
* [Azure 机器学习，大规模定型和注册 TensorFlow 模型](how-to-train-tensorflow.md)。
* [Azure 机器学习，大规模定型和注册 Chainer 模型](how-to-train-chainer.md)。

### <a name="experiments"></a>试验

试验是指定的脚本中多个运行的分组。 它始终属于工作区。 当你提交运行时，需提供试验名称。 运行的信息存储在该试验下。 如果提交运行，并指定一个不存在的试验名称，则系统将使用新指定的名称自动创建一个新试验。

有关使用试验的示例，请参阅[教程：训练您的第](tutorial-1st-experiment-sdk-train.md)一个模型。


### <a name="github-tracking-and-integration"></a>GitHub 跟踪和集成

当你开始在源目录为本地 Git 存储库的训练运行时, 有关存储库的信息存储在运行历史记录中。 例如, 将在历史记录中记录存储库的当前提交 ID。 这适用于使用估计器、ML 管道或脚本运行提交的运行。 它还适用于从 SDK 或机器学习 CLI 提交的运行。


### <a name="logging"></a>日志记录

开发解决方案时，请在 Python 脚本中使用 Azure 机器学习 Python SDK 记录任意指标。 运行后，查询指标以确定运行是否生成了要部署的模型。

### <a name="ml-pipelines"></a>ML 管道

使用机器学习管道可以创建和管理将各个机器学习阶段整合到一起的工作流。 例如, 管道可能包含数据准备、模型定型、模型部署和推理/评分阶段。 每个阶段可以包含多个步骤，每个步骤都能够以无人参与方式在各种计算目标中运行。 

管道步骤可重复使用，如果该步骤的输出未发生更改，则可以运行，而无需重新运行后续步骤。 例如，如果数据未更改，则可以重新训练模型，而无需重新运行成本高昂的数据准备步骤。 管道还允许数据科学家协作，同时处理机器学习工作流的不同区域。

有关机器学习管道与此服务的详细信息，请参阅[管道和 Azure 机器学习](concept-ml-pipelines.md)。

### <a name="models"></a>Models

简单地说，模型是一段接受输入并生成输出的代码。 创建机器学习模型将涉及选择算法、为其提供数据以及优化超参数。 培训是一个迭代过程，将生成经过培训的模型，它会封装模型在培训过程中学到的内容。

模型通过 Azure 机器学习中的运行生成。 还可以使用在 Azure 机器学习外部训练的模型。 可以在 Azure 机器学习工作区中注册模型。

Azure 机器学习与框架无关。 创建模型时, 可以使用任何主流机器学习框架, 如 Scikit-learn、XGBoost、PyTorch、TensorFlow 和 Chainer。

有关使用 scikit-learn 和估计器为模型定型的示例，请参阅[教程：使用 Azure 机器学习](tutorial-train-models-with-aml.md)训练图像分类模型。

**模型注册表**将跟踪 Azure 机器学习工作区中的所有模型。

模型按名称和版本标识。 每次使用与现有相同的名称注册模型时，注册表都会假定它是新版本。 该版本将递增并且新模型会以同一名称注册。

注册模型时，可以提供其他元数据标记，然后在搜索模型时使用这些标记。

> [!TIP]
> 已注册的模型是组成模型的一个或多个文件的逻辑容器。 例如, 如果您有一个存储在多个文件中的模型, 则可以将它们作为一个模型注册到 Azure 机器学习工作区中。 注册后, 可以下载或部署已注册的模型, 并接收已注册的所有文件。

不能删除活动部署正在使用的已注册模型。

有关注册模型的示例，请参阅[使用 Azure 机器学习训练映像分类模型](tutorial-train-models-with-aml.md)。


### <a name="runs"></a>运行

"运行" 是训练脚本的单次执行。 Azure 机器学习记录所有运行并存储以下信息：

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
> 若要防止在快照中包含不必要的文件, 请使用 ignore file (. .gitignore 或 amlignore)。 将此文件放在快照目录中, 并在其中添加要忽略的文件名。 Amlignore 文件[与 .gitignore 文件使用相同的语法和模式](https://git-scm.com/docs/gitignore)。 如果这两个文件都存在, 则将优先使用 amlignore 文件。

### <a name="training-scripts"></a>定型脚本

若要定型模型，你可以指定包含培训脚本和关联文件的目录。 此外，还可指定一个试验名称，用于存储在训练期间收集的信息。 在训练期间，会将整个目录复制到训练环境（计算目标），并启动运行配置指定的脚本。 目录的快照同样存储在工作区中的试验下。

有关示例，请参阅[教程：使用 Azure 机器学习](tutorial-train-models-with-aml.md)训练图像分类模型。

### <a name="workspaces"></a>工作区

[工作区](concept-workspace.md)是 Azure 机器学习的顶级资源。 使用 Azure 机器学习时，它提供了一个集中的位置来处理所创建的所有项目。 可以与他人共享工作区。 有关工作区的详细说明，请参阅[什么是 Azure 机器学习工作区？](concept-workspace.md)。


### <a name="next-steps"></a>后续步骤

若要开始 Azure 机器学习，请参阅：

* [什么是 Azure 机器学习？](overview-what-is-azure-ml.md)
* [创建 Azure 机器学习工作区](how-to-manage-workspace.md)
* [教程（第 1 部分）：训练模型](tutorial-train-models-with-aml.md)
