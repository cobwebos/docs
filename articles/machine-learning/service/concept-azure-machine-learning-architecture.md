---
title: Azure 机器学习服务如何工作？
description: 了解组成 Azure 机器学习服务的体系结构、术语和概念。 此外，还将了解如何使用该服务的常规工作流，以及 Azure 机器学习服务所使用的 Azure 服务。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: haining
author: hning86
ms.reviewer: larryfr
ms.date: 10/24/2018
ms.openlocfilehash: 95f74b23b9d0c89966347f066041b23f64f3b82c
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2018
ms.locfileid: "50210680"
---
# <a name="how-the-azure-machine-learning-service-works-architecture-and-concepts"></a>Azure 机器学习服务的工作方式：体系结构和概念

本文档介绍 Azure 机器学习服务的体系结构和概念。 下图显示该服务的主要组件，并说明使用该服务时的常规工作流： 

[![Azure 机器学习服务体系结构和工作流](./media/concept-azure-machine-learning-architecture/workflow.png)](./media/concept-azure-machine-learning-architecture/workflow.png#lightbox)

工作流通常遵循以下步骤：

1. 在 __Python__ 中开发机器学习培训脚本。
1. 创建和配置__计算目标__。
1. __将脚本提交__到配置的计算目标以在该环境中运行。 在培训期间，计算目标会将运行记录存储到__数据存储__。 记录将保存到__试验__。
1. __查询试验__了解当前和过去的运行中已记录的指标。 如果指标未指示所需结果，请循环回到步骤 1 并循环访问脚本。
1. 在找到令人满意的运行后，即在__模型注册表__中注册持久化模型。
1. 开发评分脚本。
1. __创建映像__并将其注册在__映像注册表__中。 
1. 在 Azure 中__将映像部署__为 __Web 服务__。


[!INCLUDE [aml-preview-note](../../../includes/aml-preview-note.md)]

> [!NOTE]
> 虽然本文档定义了 Azure 机器学习使用的术语和概念，但它未定义 Azure 平台的术语和概念。 有关 Azure 平台术语的详细信息，请参阅 [Microsoft Azure 词汇表](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology)。

## <a name="workspace"></a>工作区

工作区是 Azure 机器学习服务的顶级资源。 它提供了一个集中的位置来处理使用 Azure 机器学习服务时创建的所有项目。

工作区保留可用于定型模型的计算目标的列表。 此外，它还保留培训运行的历史记录，包括日志、指标、输出和脚本快照。 此信息用于确定哪个培训运行产生最佳模型。

模型在工作区中注册。 已注册模型和评分脚本用于创建映像。 然后映像可以部署到 Azure 容器实例、Azure Kubernetes 服务中或部署到现场可编程门阵列 (FPGA) 作为基于 REST 的 HTTP 终结点。 它还可以作为模块部署到 Azure IoT Edge 设备。

你可以创建多个工作区，并且每个工作区可由多个用户共享。 共享工作区时，通过向用户分配以下角色来控制对工作区的访问权限：

* 所有者
* 参与者
* 读取器

创建新工作区时，它会自动创建工作区使用的几个 Azure 资源：

* [Azure 容器注册表](https://azure.microsoft.com/services/container-registry/) - 注册在培训期间和部署模型时使用的 docker 容器。
* [Azure 存储](https://azure.microsoft.com/services/storage/) - 用作工作区的默认数据存储。
* [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) - 存储有关模型的监视信息。
* [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) - 存储计算目标使用的机密和工作区所需的其他敏感信息。

> [!NOTE]
> 而且，还可以使用现有的 Azure 服务，而不是创建新版本。 

下图是工作区的分类：

[![工作区分类](./media/concept-azure-machine-learning-architecture/taxonomy.png)](./media/concept-azure-machine-learning-architecture/taxonomy.png#lightbox)

## <a name="model"></a>模型

简单地说，模型是一段接受输入并生成输出的代码。 创建机器学习模型将涉及选择算法、为其提供数据以及优化超参数。 培训是一个迭代过程，将生成经过培训的模型，它会封装模型在培训过程中学到的内容。

模型通过 Azure 机器学习中的运行生成。 你还可以使用在 Azure 机器学习之外培训的模型。 可以在 Azure 机器学习服务工作区下注册模型。

Azure 机器学习服务与框架无关。 创建模型时，你可以使用任何常用机器学习框架，例如 scikit-learn、xgboost、PyTorch、TensorFlow、Chainer 和 CNTK。

有关培训模型的示例，请参阅[快速入门：创建机器学习服务工作区](quickstart-get-started.md)文档。

### <a name="model-registry"></a>模型注册表

模型注册表会跟踪 Azure 机器学习服务工作区中的所有模型。 

模型按名称和版本标识。 每次使用与现有相同的名称注册模型时，注册表都会假定它是新版本。 该版本将递增并且新模型会以该名称注册。

注册模型时，你可以提供其他元数据标记，然后在搜索模型时使用这些标记。

映像正在使用的模型无法删除。

有关注册模型的示例，请参阅[使用 Azure 机器学习培训映像分类模型](tutorial-train-models-with-aml.md)文档。

## <a name="image"></a>映像

映像提供了一种可靠地部署模型的方法，以及使用该模型所需的所有组件。 映像包含以下项：

* 模型。
* 评分脚本或应用程序。 此脚本用于将输入传递到模型，并返回模型的输出。
* 模型或评分脚本/应用程序所需的依赖项。  例如，你可能包括列出 Python 包依赖项的 Conda 环境文件。

Azure 机器学习可以创建两种类型的映像：

* FPGA 映像：在 Azure 云中部署到现场可编程门阵列时使用。
* Docker 映像：在部署到 FPGA 以外的计算目标时使用。 例如，Azure 容器实例和 Azure Kubernetes 服务。

有关创建映像的示例，请参阅[在 Azure 容器实例中部署映像分类模型](tutorial-deploy-models-with-aml.md)文档。

### <a name="image-registry"></a>映像注册表

映像注册表会跟踪从模型创建的映像。 创建映像时，可以提供其他元数据标记。 元数据标记由映像注册表存储，并且可以进行查询以查找映像。

## <a name="deployment"></a>部署

部署是将映像实例化为可在云中托管的 Web 服务或用于集成设备部署的 IoT 模块。 

### <a name="web-service"></a>Web 服务

已部署的 Web 服务可以使用 Azure 容器实例、Azure Kubernetes 服务或现场可编程门阵列 (FPGA)。
该服务是基于封装模型、脚本和关联文件的映像创建的。 映像具有负载均衡的 HTTP 终结点，可接收发送到 Web 服务的评分请求。

如果已选择启用此功能，Azure 可通过收集 Application Insight 遥测数据和/或模型遥测数据帮助监视 Web 服务部署。 遥测数据仅供你访问，并且存储在 Application Insights 和存储帐户实例中。

如果已启用自动缩放，Azure 将自动缩放部署。

有关将模型部署为 Web 服务的示例，请参阅[在 Azure 容器实例中部署映像分类模型](tutorial-deploy-models-with-aml.md)文档。

### <a name="iot-module"></a>IoT 模块

已部署 IoT 模块是一个 Docker 容器，包括模型和关联脚本或应用程序，以及任何其他依赖项。 在边缘设备上使用 Azure IoT Edge 部署这些模块。 

如果已启用监视，Azure 会从 Azure IoT Edge 模块内的模型中收集遥测数据。 遥测数据仅供你访问，并且存储在存储帐户实例中。

Azure IoT Edge 将确保模块正在运行并且监视托管它的设备。

## <a name="datastore"></a>数据存储

数据存储是通过 Azure 存储帐户进行的存储抽象。 数据存储可以使用 Azure blob 容器或 Azure 文件共享作为后端存储。 每个工作区都有默认数据存储，并且你可以注册其他数据存储。 

使用 Python SDK API 或 Azure 机器学习 CLI 可从数据存储中存储和检索文件。 

## <a name="run"></a>运行

运行是包含以下信息的记录：

* 有关运行的元数据（时间戳、持续时间等）
* 脚本记录的指标
* 试验自动收集的或由你显式上传的输出文件。
* 在运行之前包含脚本的目录的快照

提交脚本以培训模型时，会生成运行。 运行可以有零次或多次子级运行。 因此，顶级运行可以有两次子级运行，其中每个可以有其自己的子级运行。

有关查看培训模型生成的运行的示例，请参阅[快速入门：开始使用 Azure 机器学习服务](quickstart-get-started.md)文档。

## <a name="experiment"></a>试验

试验是给定脚本中多次运行的分组。 它始终属于工作区。 当你提交运行时，需提供试验名称。 运行的信息存储在该试验下。 如果你提交运行，并指定一个不存在的试验名称，系统将自动创建一个使用该名称的新试验。

有关使用试验的示例，请参阅[快速入门：开始使用 Azure 机器学习服务](quickstart-get-started.md)文档。

## <a name="pipelines"></a>管道

管道用来创建和管理将各个机器学习阶段整合到一起的工作流。 例如，管道可以包括数据准备、模型训练、模型部署以及推断阶段。 每个阶段可以包含多个步骤，每个步骤都能够以无人参与方式在各种计算目标中运行。

有关机器学习管道与此服务的详细信息，请参阅[管道和 Azure 机器学习](concept-ml-pipelines.md)一文。

## <a name="compute-target"></a>计算目标

计算目标是用于运行培训脚本或托管服务部署的计算资源。 支持的计算目标为： 

| 计算目标 | 培训 | 部署 |
| ---- |:----:|:----:|
| 本地计算机 | ✓ | &nbsp; |
| Azure 中的 Linux VM</br>（例如 Data Science Virtual Machine） | ✓ | &nbsp; |
| Azure Batch AI 群集 | ✓ | &nbsp; |
| Azure Databricks | ✓ | &nbsp; | &nbsp; |
| Azure Data Lake Analytics | ✓ | &nbsp; |
| Apache Spark for HDInsight | ✓ | &nbsp; |
| Azure 容器实例 | ✓ | ✓ |
| Azure Kubernetes 服务 | &nbsp; | ✓ |
| Azure IoT Edge | &nbsp; | ✓ |
| Project Brainwave</br>（现场可编程门阵列） | &nbsp; | ✓ |

计算目标附加到工作区。 本地计算机以外的计算目标由工作区的用户共享。

大多数计算目标可以使用 Azure 门户、Azure 机器学习 SDK 或 Azure CLI 通过工作区直接创建。 如果有其他过程（例如，Azure 门户或 Azure CLI）创建的计算目标，可以将其添加（附加）到工作区。 一些计算目标必须在工作区外部创建，然后附加。

有关选择培训的计算目标的信息，请参阅[选择并使用计算目标以培训模型](how-to-set-up-training-targets.md)文档。

有关选择部署的计算目标的信息，请参阅[使用 Azure 机器学习服务部署模型](how-to-deploy-and-where.md)文档。

## <a name="run-configuration"></a>运行配置

运行配置是一组指令，用于定义应如何在给定计算目标中运行脚本。 它包括一组广泛的行为定义，例如，是使用现有 Python 环境还是使用根据规范构建的 Conda 环境。

运行配置可以保存到包含培训脚本的目录内的文件中，或构造为内存中对象以及用于提交运行。

有关示例运行配置，请参阅[选择并使用计算目标以培训模型](how-to-set-up-training-targets.md)文档。

## <a name="training-script"></a>培训脚本

若要定型模型，你可以指定包含培训脚本和关联文件的目录。 此外，还可指定一个试验名称，用于存储在培训期间收集的信息。 在培训期间，会将整个目录复制到培训环境（计算目标），并启动运行配置指定的脚本。 目录的快照同样存储在工作区中的试验下。

有关示例，请参阅[使用 Python 创建工作区](quickstart-get-started.md)

## <a name="logging"></a>日志记录

在开发解决方案时，请在 Python 脚本中使用 Azure 机器学习 Python SDK 记录任意指标。 运行后，查询指标以确定运行是否生成了要部署的模型。 

## <a name="snapshot"></a>快照

提交运行时，Azure 机器学习会将包含该脚本的目录压缩为 zip 文件并将其发送到计算目标。 然后展开 zip 文件并运行脚本。 Azure 机器学习还将该 zip 文件存储为快照，作为运行记录的一部分。 有权限访问工作区的任何用户都可以浏览运行记录并下载快照。

## <a name="activity"></a>活动

活动表示长时间运行的操作。 以下操作是活动的示例：

* 创建或删除计算目标
* 在计算目标上运行脚本

活动可通过 SDK 或 Web UI 提供通知，以便能够轻松监视这些操作的进度。

## <a name="next-steps"></a>后续步骤

使用以下链接开始使用 Azure 机器学习：

* [什么是 Azure 机器学习服务](overview-what-is-azure-ml.md)
* [快速入门：使用 Python 创建工作区](quickstart-get-started.md)
* [教程：定型模型](tutorial-train-models-with-aml.md)
