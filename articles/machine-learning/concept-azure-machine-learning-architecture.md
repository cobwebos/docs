---
title: 体系结构和重要概念
titleSuffix: Azure Machine Learning
description: 了解构成 Azure 机器学习的体系结构、术语和概念。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 08/20/2020
ms.custom: seoapril2019, seodec18
ms.openlocfilehash: c3abd6a57eac851a5440ecdef6185cb310305434
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2020
ms.locfileid: "89146770"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>Azure 机器学习的工作原理：体系结构和概念

了解 [Azure 机器学习](overview-what-is-azure-ml.md)的体系结构和概念。  本文提供了有关组件以及它们如何协同工作以帮助构建、部署和维护机器学习模型的过程的高级理解。

## <a name="workspace"></a><a name="workspace"></a> 空间

[机器学习工作区](concept-workspace.md)是 Azure 机器学习的顶级资源。

:::image type="content" source="media/concept-azure-machine-learning-architecture/architecture.svg" alt-text="关系图：工作区及其组件的 Azure 机器学习体系结构":::

工作区是的集中位置：

* 管理用于定型和部署模型的资源，例如 [计算](#compute-instance)
* 使用 Azure 机器学习时，存储所创建的资产，包括：
  * [环境](#environments)
  * [试验](#experiments)
  * [管道](#ml-pipelines)
  * [数据集](#datasets-and-datastores)
  * [模型](#models)
  * [Endpoints](#endpoints)

工作区包含工作区使用的其他 Azure 资源：

+ [Azure 容器注册表 (ACR) ](https://azure.microsoft.com/services/container-registry/)：注册在训练过程中和部署模型时使用的 docker 容器。 为了最大限度地降低成本，只能在创建部署映像时创建 ACR。
+ [Azure 存储帐户](https://azure.microsoft.com/services/storage/)，用作工作区的默认数据存储。  与 Azure 机器学习计算实例一起使用的 Jupyter 笔记本也存储在此处。
+ [Azure Application Insights](https://azure.microsoft.com/services/application-insights/)：存储有关模型的监视信息。
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)：存储计算目标使用的机密和工作区所需的其他敏感信息。

可与其他人共享工作区。

## <a name="computes"></a>契

<a name="compute-targets"></a>[计算目标](concept-compute-target.md)是用于运行训练脚本或托管服务部署的任何计算机或计算机集。 你可以使用本地计算机或远程计算资源作为计算目标。  通过计算目标，你可以在本地计算机上开始培训，然后在不更改定型脚本的情况下向外扩展到云。

Azure 机器学习引入了两个完全托管的基于云的虚拟机 (VM) ，这些虚拟机配置了机器学习任务：

* <a name="compute-instance"></a>**计算实例**：计算实例是一个 VM，其中包含为机器学习安装的多个工具和环境。 计算实例的主要用途是用于开发工作站。  你可以开始运行示例笔记本，无需安装。 计算实例还可用作定型和推断作业的计算目标。

* **计算群集**：计算群集是包含多节点缩放功能的 vm 群集。 计算群集更适合用于大型作业和生产的计算目标。  提交作业时，群集会自动扩展。  使用作为训练计算目标，或用于开发/测试部署。

有关定型计算目标的详细信息，请参阅 [训练计算目标](concept-compute-target.md#train)。  有关部署计算目标的详细信息，请参阅 [部署目标](concept-compute-target.md#deploy)。

## <a name="datasets-and-datastores"></a>数据集和数据存储

[**Azure 机器学习数据集**](concept-data.md#datasets)  ，可以更方便地访问和处理数据。 通过创建数据集，可以创建对数据源位置的引用以及其元数据的副本。 由于数据保留在其现有位置中，因此不会产生额外的存储成本，也不会损害数据源的完整性。

有关详细信息，请参阅[创建和注册 Azure 机器学习数据集](how-to-create-register-datasets.md)。  有关使用数据集的更多示例，请参阅[示例笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets-tutorial)。

数据集使用 [数据存储](concept-data.md#datastores) 安全地连接到 Azure 存储服务。 数据存储存储连接信息，而无需对你的身份验证凭据和原始数据源的完整性造成风险。 它们在与工作区关联的 Key Vault 中存储连接信息，如订阅 ID 和令牌授权，因此，你可以安全地访问存储，而无需在脚本中对其进行硬编码。

## <a name="environments"></a>环境

[工作区](#workspace)  > **环境**

[环境](concept-environments.md)是环境的封装，其中发生了机器学习模型的定型或评分。 环境指定围绕定型和评分脚本的 Python 包、环境变量和软件设置。  

如需代码示例，请参阅[如何使用环境](how-to-use-environments.md#manage-environments)中的“管理环境”部分。

## <a name="experiments"></a>试验

[工作区](#workspace)  > **试验**

试验是指定的脚本中多个运行的分组。 它始终属于工作区。 当你提交运行时，需提供试验名称。 运行的信息存储在该试验下。 如果提交实验时该名称不存在，则会自动创建新的实验。
  
有关使用试验的示例，请参阅[教程：训练第一个模型](tutorial-1st-experiment-sdk-train.md)。

### <a name="runs"></a>运行次数

[工作区](#workspace)  > [试验](#experiments)  > **运行**

一次运行就是执行一次训练脚本。 试验通常包含多个运行。

Azure 机器学习在试验中记录所有运行并存储以下信息：

* 有关运行的元数据（时间戳、持续时间等）
* 脚本记录的指标
* 试验自动收集的或由你显式上传的输出文件
* 在运行之前包含脚本的目录的快照

提交脚本以训练模型时，会生成运行。 运行可以有零次或多次子级运行。 例如，顶级运行可以有两次子级运行，其中每个可以有其自己的子级运行。

### <a name="run-configurations"></a>运行配置

[工作区](#workspace)  > [试验](#experiments)  > [运行](#runs)  > **运行配置**

运行配置是一组指令，用于定义如何在指定的计算目标中运行脚本。 该配置包括一组广泛的行为定义，例如，是使用现有 Python 环境还是使用根据规范构建的 Conda 环境。

可以将运行配置保存到包含定型脚本的目录中的文件中。   也可以将它构造为内存中对象，并用于提交运行。

有关运行配置的示例，请参阅 [使用计算目标来训练模型](how-to-set-up-training-targets.md)。

### <a name="estimators"></a>估算器

为了便于使用流行框架进行模型训练，可以通过估算器类轻松构造运行配置。 可以创建并使用泛型[估算器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py)来提交使用所选任何学习框架（例如 scikit-learn）的训练脚本。

有关估算的详细信息，请参阅 [通过估算训练 ML 模型](how-to-train-ml-models.md)。

### <a name="snapshots"></a>快照

[工作区](#workspace)  > [试验](#experiments)  > [运行](#runs)  > **快照**

提交运行时，Azure 机器学习会将包含该脚本的目录压缩为 zip 文件并将其发送到计算目标。 然后解压缩 zip 文件并运行脚本。 Azure 机器学习还将该 zip 文件存储为快照，作为运行记录的一部分。 有权限访问工作区的任何用户都可以浏览运行记录并下载快照。


### <a name="logging"></a>日志记录

开发解决方案时，请在 Python 脚本中使用 Azure 机器学习 Python SDK 记录任意指标。 运行后，查询指标以确定运行是否生成了要部署的模型。


> [!NOTE]
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]

### <a name="git-tracking-and-integration"></a>Git 跟踪和集成

如果以本地 Git 存储库作为源目录开始训练运行，有关存储库的信息将存储在运行历史记录中。 这适用于使用估算器、ML 管道或脚本运行提交的运行。 此外，还适用于从 SDK 或机器学习 CLI 提交的运行。

有关详细信息，请参阅 [Azure 机器学习的 Git 集成](concept-train-model-git-integration.md)。

## <a name="models"></a>模型

简单地说，模型是一段接受输入并生成输出的代码。 创建机器学习模型涉及到选择算法、向其提供数据以及 [优化超参数](how-to-tune-hyperparameters.md)。 培训是一个迭代过程，将生成经过培训的模型，它会封装模型在培训过程中学到的内容。

你可以将已训练的模型置于 Azure 机器学习之外。 或者，您可以通过在 Azure 机器学习中将[实验](#experiments)的[运行](#runs)提交给[计算目标](#compute-targets)来定型模型。 有了模型后，就可以在工作区中 [注册该模型](#register-model) 了。

Azure 机器学习与框架无关。 创建模型时，可以使用任何流行的机器学习框架，例如 Scikit-learn、XGBoost、PyTorch、TensorFlow 和 Chainer。

有关使用 Scikit-learn 为模型定型的示例，请参阅 [教程：使用 Azure 机器学习训练图像分类模型](tutorial-train-models-with-aml.md)。


### <a name="model-registry"></a><a name="register-model"></a> 模型注册表

[工作区](#workspace)  > **模型**

利用 **模型注册表** ，可以跟踪 Azure 机器学习工作区中的所有模型。

模型按名称和版本标识。 每次使用与现有相同的名称注册模型时，注册表都会假定它是新版本。 该版本将递增并且新模型会以同一名称注册。

注册模型时，可以提供其他元数据标记，然后在搜索模型时使用这些标记。

> [!TIP]
> 已注册的模型是构成模型的一个或多个文件的逻辑容器。 例如，如果你有一个存储在多个文件中的模型，则可以在 Azure 机器学习工作区中将这些文件注册为单个模型。 注册后，可以下载或部署已注册的模型，并接收注册的所有文件。

无法删除在活动部署正在使用的已注册模型。

有关注册模型的示例，请参阅[使用 Azure 机器学习训练映像分类模型](tutorial-train-models-with-aml.md)。

## <a name="deployment"></a>部署

将 [已注册的模型](#register-model) 部署为服务终结点。 需要以下组件：

* **环境**。 此环境封装运行模型进行推理所需的依赖项。
* **评分代码**。 此脚本接受请求、使用模型为请求评分并返回结果。
* **推理配置**。 推理配置指定以服务形式运行模型所需的环境、入口脚本和其他组件。

有关这些组件的详细信息，请参阅 [部署模型与 Azure 机器学习](how-to-deploy-and-where.md)。

### <a name="endpoints"></a>终结点

[工作区](#workspace)  > **终结点**

终结点是模型在 Web 服务（可托管于云中）中的实例化，或用于集成设备部署的 IoT 模块。

#### <a name="web-service-endpoint"></a>Web 服务终结点

将模型部署为 web 服务时，可以将终结点部署在 Azure 容器实例、Azure Kubernetes 服务或 Fpga 上。 可以从模型、脚本和关联的文件创建服务。 这些将放置到基容器映像中，其中包含模型的执行环境。 映像具有负载均衡的 HTTP 终结点，可接收发送到 Web 服务的评分请求。

可以启用 Application Insights 遥测或模型遥测来监视 web 服务。 只能由你访问遥测数据。  它存储在 Application Insights 和存储帐户实例中。

如果已启用自动缩放，Azure 将自动缩放部署。

有关将模型部署为 Web 服务的示例，请参阅[在 Azure 容器实例中部署映像分类模型](tutorial-deploy-models-with-aml.md)。

#### <a name="iot-module-endpoints"></a>IoT 模块终结点

已部署 IoT 模块终结点是一个 Docker 容器，其中包含模型和关联脚本或应用程序，以及任何其他依赖项。 在边缘设备上使用 Azure IoT Edge 部署这些模块。

如果已启用监视，Azure 会从 Azure IoT Edge 模块内的模型中收集遥测数据。 遥测数据仅供你访问，并且存储在存储帐户实例中。

Azure IoT Edge 将确保模块正在运行并且监视托管它的设备。 
## <a name="automation"></a>自动化

### <a name="azure-machine-learning-cli"></a>Azure 机器学习 CLI 

[AZURE 机器学习 CLI](reference-azure-machine-learning-cli.md)是对 Azure CLI （Azure 平台的跨平台命令行接口）的扩展。 此扩展提供用于自动执行机器学习活动的命令。

### <a name="ml-pipelines"></a>ML 管道

您可以使用 [机器学习管道](concept-ml-pipelines.md) 来创建和管理将机器学习阶段结合在一起的工作流。 例如，管道可以包括数据准备、模型训练、模型部署以及推理/评分阶段。 每个阶段可以包含多个步骤，每个步骤都能够以无人参与方式在各种计算目标中运行。 

管道步骤可重用，如果这些步骤的输出没有更改，则无需重新运行前面的步骤即可运行。 例如，如果数据未更改，你可以重新训练模型，不需要重新运行成本高昂的数据准备步骤。 管道还使数据科学家能够展开协作，同时可以处理机器学习工作流的不同环节。

## <a name="interacting-with-your-workspace"></a>与工作区交互

### <a name="studio"></a>工作室

[Azure 机器学习 studio](https://ml.azure.com) 提供了工作区中所有项目的 web 视图。  您可以查看数据集、试验、管道、模型和终结点的结果和详细信息。  你还可以在工作室中管理计算资源和数据存储。

通过工作室，你还可以访问 Azure 机器学习中包含的交互工具：

+ [Azure 机器学习设计器 (预览) ](concept-designer.md) 在不编写代码的情况下执行工作流步骤
+ [自动机器学习](concept-automated-ml.md)的 Web 体验
+ [数据标记项目](how-to-create-labeling-projects.md) ，用于创建、管理和监视项目以标记数据

### <a name="programming-tools"></a>编程工具

> [!IMPORTANT]
> 下面标记了“（预览版）”的工具目前为公共预览版。
> 该预览版在提供时没有附带服务级别协议，建议不要将其用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

+  使用[适用于 Python 的 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 来与任何 Python 环境中的服务交互。
+ 在具有适用于 R (预览版) 的 [AZURE 机器学习 SDK](https://azure.github.io/azureml-sdk-for-r/reference/index.html) 的任何 r 环境中与服务进行交互。
+ 使用 [AZURE 机器学习 CLI](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli) 实现自动化。
+ [多模型解决方案加速器](https://aka.ms/many-models)（预览）是在 Azure 机器学习的基础之上构建而成，可便于你训练、操作和管理成百上千的机器学习模型。

## <a name="next-steps"></a>后续步骤

若要开始使用 Azure 机器学习，请参阅：

* [什么是 Azure 机器学习？](overview-what-is-azure-ml.md)
* [创建 Azure 机器学习工作区](how-to-manage-workspace.md)
* [教程（第 1 部分）：训练模型](tutorial-train-models-with-aml.md)
