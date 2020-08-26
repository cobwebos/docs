---
title: 关于 Azure 机器学习环境
titleSuffix: Azure Machine Learning
description: 在本文中，你将了解机器学习环境的优点，在该环境中可以创建跨各种计算目标的可重复、可审核且可移植的机器学习依赖项定义。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: BlackMist
ms.date: 07/08/2020
ms.openlocfilehash: cc4c39cf26f3ab8d1037222f967789bfbeca05ba
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2020
ms.locfileid: "88166750"
---
# <a name="what-are-azure-machine-learning-environments"></a>什么是 Azure 机器学习环境？
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure 机器学习环境是计算机学习定型发生环境的封装。 它们指定了有关训练和评分脚本的 Python 包、环境变量和软件设置。 它们还指定运行时（Python、Spark 或 Docker）。 环境是机器学习工作区中受管理且版本受控的实体，可用于创建跨各种计算目标的可再现、可审核且可移植的机器学习工作流。

可以使用本地计算机上的 `Environment` 目标执行以下操作：
* 开发训练脚本。
* 在 Azure 机器学习计算中重用相同的环境进行大规模的模型训练。
* 使用该相同环境部署你的模型。
* 重新访问已训练现有模型的环境。

下图说明了如何 `Environment` 在运行配置 (中使用单个对象进行培训) 以及) 的 web 服务部署的推理和部署配置 (。

![图示：机器学习工作流中的环境](./media/concept-environments/ml-environment.png)

环境、计算目标和训练脚本共同构成了运行配置：定型运行的完整规范。

## <a name="types-of-environments"></a>环境类型

环境可以总体分为三类：特选类、用户管理类以及系统管理类  。

特选环境由 Azure 机器学习提供，且默认可用于你的工作区。 旨在按原样使用，其中包含 Python 包的集合和设置，以帮助你开始使用各种机器学习框架。 这些预先创建的环境还允许更快的部署时间。 有关完整列表，请参阅[特选环境一文](resource-curated-environments.md)。

在用户管理的环境中，你需要负责设置环境，并在计算目标上安装训练脚本所需的每个包。 Conda 不检查你的环境，也不会为你安装任何组件。 如果你定义自己的环境，则必须将版本为 `>= 1.0.45` 的 `azureml-defaults` 列出为 pip 依赖项。 此包包含将模型作为 Web 服务托管时所需的功能。

如果想让 [Conda](https://conda.io/docs/) 为你管理 Python 环境和脚本依赖项，请使用系统管理的环境。 新的 conda 环境是基于 conda 依赖项对象生成的。 默认情况下，Azure 机器学习服务假定这种类型的环境，因为它对不是手动配置的远程计算目标有用。

## <a name="create-and-manage-environments"></a>创建和管理环境

可以通过以下方式创建环境：

* 定义新的 `Environment` 对象（通过使用特选环境或通过定义自己的依赖项）。
* 使用工作区中的现有 `Environment` 对象。 此方法可以确保你的依赖项的一致性和可再现性。
* 从现有 Anaconda 环境定义中进行导入。
* 使用 Azure 机器学习 CLI
* [使用 VS Code 扩展](how-to-manage-resources-vscode.md#create-environment)

有关特定的代码示例，请参阅[如何使用环境](how-to-use-environments.md#create-an-environment)中的 "创建环境" 一节。 还可以通过工作区轻松管理环境。 它们包括以下功能：

* 提交试验时，环境会自动注册到你的工作区。 还可以手动注册它们。
* 可以从你的工作区中提取环境来用于训练或部署，或对环境定义进行编辑。
* 利用版本控制，你可以查看你的环境随时间的变化，从而确保可再现性。
* 你可以基于你的环境自动生成 Docker 映像。

有关代码示例，请参阅[如何使用环境](how-to-use-environments.md#manage-environments)中的 "管理环境" 一节。

## <a name="environment-building-caching-and-reuse"></a>生成、缓存和重复使用环境

Azure 机器学习服务在 Docker 映像和 conda 环境中生成环境定义。 它还会缓存环境，使其可在后续的训练运行和服务终结点部署中重复使用。 远程运行训练脚本需要创建 Docker 映像，而本地运行可以直接使用 Conda 环境。 

### <a name="submitting-a-run-using-an-environment"></a>使用环境提交运行

当你首次使用环境提交远程运行时，Azure 机器学习服务将在与工作区关联的 Azure 容器注册表 (ACR) 上调用[Acr 生成任务](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview)。 然后，生成的 Docker 映像将在工作区 ACR 中缓存。 特选环境受缓存在全局 ACR 中的 Docker 映像的支持。 在运行执行开始时，计算目标会从相关的 ACR 检索映像。

对于本地运行，将基于环境定义创建 Docker 或 Conda 环境。 然后，在目标计算-本地运行时环境或本地 Docker 引擎上执行脚本。

### <a name="building-environments-as-docker-images"></a>以 Docker 映像的形式生成环境

如果工作区 ACR 中不存在环境定义，则会生成一个新图像。 映像生成包括两个步骤：

 1. 下载基础映像，并执行任何 Docker 步骤
 2. 根据环境定义中指定的 conda 依赖项生成 conda 环境。

如果指定[用户管理的依赖项](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py)，则会省略第二个步骤。 在这种情况下，你需要负责安装任何 Python 包，方法是在基础映像中包含这些包，或者在第一个步骤中指定自定义 Docker 步骤。 你还要负责为 Python 可执行文件指定正确的位置。 还可以使用[自定义 Docker 基本映像](how-to-deploy-custom-docker-image.md)。

### <a name="image-caching-and-reuse"></a>缓存和重复使用映像

如果你对另一个运行使用相同的环境定义，Azure 机器学习服务将重复使用工作区 ACR 中缓存的映像。 

若要查看缓存的映像的详细信息，请使用 [Environment.get_image_details](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#get-image-details-workspace-) 方法。

为了确定是要重复使用缓存的映像还是生成新映像，服务将从环境定义计算一个[哈希值](https://en.wikipedia.org/wiki/Hash_table)，并将其与现有环境的哈希进行比较。 计算的哈希基于：
 
 * 基础映像属性值
 * 自定义 Docker 步骤属性值
 * Conda 定义中的 Python 包列表
 * Spark 定义中的包列表 

哈希不依赖于环境名称或版本-如果你重命名环境或使用现有的属性和包来创建新的环境，则哈希值将保持不变。 但是，环境定义更改（如添加或删除 Python 包或更改包版本）会导致更改哈希值。 更改环境中的依赖项或通道的顺序将产生新的环境，因此需要新的映像生成。 需要注意的是，对特选环境所做的任何更改都将导致哈希失效，并产生新的 "非特选" 环境。

计算所得的哈希值将与工作区中的哈希值和全局 ACR (或本地运行) 的计算目标进行比较。 如果存在匹配项，则会拉取缓存的映像，否则会触发映像生成。 请求缓存图像的持续时间包括下载时间，而用于拉取新生成的映像的持续时间包括生成时间和下载时间。 

下图显示了三个环境定义。 其中两个名称和版本不同，但基本映像和 Python 包完全相同。 但它们具有相同的哈希，因此对应于同一个缓存映像。 第三个环境包含不同的 Python 包和版本，因此对应于不同的缓存映像。

![作为 Docker 映像缓存的环境示意图](./media/concept-environments/environment-caching.png)

>[!IMPORTANT]
> 如果创建了一个包含未固定包依赖项（例如 ```numpy```）的环境，该环境将继续使用创建环境时安装的包版本。 此外，将来包含匹配定义的任何环境将继续使用旧版本。 

若要更新包，请指定版本号以强制重新生成映像，例如 ```numpy==1.18.1```。 将安装新的依赖项（包括嵌套的依赖项），这可能会破坏先前工作的方案。 

> [!WARNING]
>  [Environment.build](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#build-workspace--image-build-compute-none-) 方法将重新生成缓存的映像，这可能会造成更新取消固定包的负面影响，并破坏对应于该缓存映像的所有环境定义的可再现性。

## <a name="next-steps"></a>后续步骤

* 了解如何在 Azure 机器学习中[创建和使用环境](how-to-use-environments.md)。
* 查看 Python SDK 参考文档来了解[环境类](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py)。
* 查看 R SDK 参考文档来了解[环境](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments)。
