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
ms.openlocfilehash: e1b92563acd6983b1680cacc06a8f2d0789dddf1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91302496"
---
# <a name="what-are-azure-machine-learning-environments"></a>什么是 Azure 机器学习环境？


Azure 机器学习环境封装了一个供你在其中进行机器学习训练的环境。 此类学习环境指定了与训练和评分脚本有关的 Python 包、环境变量和软件设置。 它们还指定运行时（Python、Spark 或 Docker）。 环境是机器学习工作区中受管理且版本受控的实体，可用于创建跨各种计算目标的可再现、可审核且可移植的机器学习工作流。

可以使用本地计算机上的 `Environment` 目标执行以下操作：
* 开发训练脚本。
* 在 Azure 机器学习计算中重用相同的环境进行大规模的模型训练。
* 使用该相同环境部署你的模型。
* 重新访问在其中训练了现有模型的环境。

下图说明了如何 `Environment` 在运行配置 (中使用单个对象进行培训) 以及) 的 web 服务部署的推理和部署配置 (。

![图示：机器学习工作流中的环境](./media/concept-environments/ml-environment.png)

环境、计算目标和训练脚本共同构成了运行配置：完整的训练运行规范。

## <a name="types-of-environments"></a>环境类型

环境可以总体分为三类：特选类、用户管理类以及系统管理类  。

特选环境由 Azure 机器学习提供，且默认可用于你的工作区。 应该按原样使用它们，其中包含的 Python 包和设置的集合可帮助你开始使用各种机器学习框架。 这些预先创建的环境还可以加快部署速度。 如需完整列表，请参阅[特选环境](resource-curated-environments.md)一文。

在用户管理的环境中，你需要负责设置环境，并在计算目标上安装训练脚本所需的每个包。 Conda 不检查你的环境，也不会为你安装任何组件。 如果你定义自己的环境，则必须将版本为 `>= 1.0.45` 的 `azureml-defaults` 列出为 pip 依赖项。 此包包含将模型作为 Web 服务托管时所需的功能。

如果想让 [Conda](https://conda.io/docs/) 为你管理 Python 环境和脚本依赖项，请使用系统管理的环境。 新的 conda 环境基于 conda 依赖项对象构建。 默认情况下，Azure 机器学习服务会采用这种类型的环境，因为它适用于不可手动配置的远程计算目标。

## <a name="create-and-manage-environments"></a>创建和管理环境

可以通过以下方式创建环境：

* 定义新的 `Environment` 对象（通过使用特选环境或通过定义自己的依赖项）。
* 使用工作区中的现有 `Environment` 对象。 此方法可以确保你的依赖项的一致性和可再现性。
* 从现有 Anaconda 环境定义中进行导入。
* 使用 Azure 机器学习 CLI
* [使用 VS Code 扩展](how-to-manage-resources-vscode.md#create-environment)

如需具体的代码示例，请参阅[如何使用环境](how-to-use-environments.md#create-an-environment)中的“创建环境”部分。 还可以通过工作区轻松管理环境。 它们包括以下功能：

* 提交试验时，环境会自动注册到你的工作区。 还可以手动注册它们。
* 可以从你的工作区中提取环境来用于训练或部署，或对环境定义进行编辑。
* 利用版本控制，你可以查看你的环境随时间的变化，从而确保可再现性。
* 你可以基于你的环境自动生成 Docker 映像。

如需代码示例，请参阅[如何使用环境](how-to-use-environments.md#manage-environments)中的“管理环境”部分。

## <a name="environment-building-caching-and-reuse"></a>生成、缓存和重复使用环境

Azure 机器学习服务在 Docker 映像和 conda 环境中生成环境定义。 它还会缓存环境，使其可在后续的训练运行和服务终结点部署中重复使用。 远程运行训练脚本需要创建一个 Docker 映像，而本地运行可以直接使用 Conda 环境。 

### <a name="submitting-a-run-using-an-environment"></a>使用某个环境提交运行

当你首次使用某个环境提交远程运行时，Azure 机器学习服务会在与工作区关联的 Azure 容器注册表 (ACR) 上调用 [ACR 生成任务](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview)。 然后，生成的 Docker 映像将在工作区 ACR 中缓存。 特选环境由全局 ACR 中缓存的 Docker 映像提供支持。 开始执行运行时，计算目标会从相关 ACR 中检索该映像。

对于本地运行，将基于环境定义创建 Docker 或 Conda 环境。 然后，将在目标计算（本地运行时环境或本地 Docker 引擎）上执行脚本。

### <a name="building-environments-as-docker-images"></a>以 Docker 映像的形式生成环境

如果工作区 ACR 中不存在环境定义，则会生成一个新映像。 映像生成包括两个步骤：

 1. 下载基础映像，并执行任何 Docker 步骤
 2. 根据环境定义中指定的 conda 依赖项生成 conda 环境。

如果指定[用户管理的依赖项](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py&preserve-view=true)，则会省略第二个步骤。 在这种情况下，你需要负责安装任何 Python 包，方法是在基础映像中包含这些包，或者在第一个步骤中指定自定义 Docker 步骤。 你还要负责为 Python 可执行文件指定正确的位置。 还可以使用[自定义 Docker 基础映像](how-to-deploy-custom-docker-image.md)。

### <a name="image-caching-and-reuse"></a>缓存和重复使用映像

如果你对另一个运行使用相同的环境定义，Azure 机器学习服务将重复使用工作区 ACR 中缓存的映像。 

若要查看缓存的映像的详细信息，请使用 [Environment.get_image_details](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true#&preserve-view=trueget-image-details-workspace-) 方法。

为了确定是要重复使用缓存的映像还是生成新映像，服务将从环境定义计算一个[哈希值](https://en.wikipedia.org/wiki/Hash_table)，并将其与现有环境的哈希进行比较。 计算的哈希基于：
 
 * 基础映像属性值
 * 自定义 Docker 步骤属性值
 * Conda 定义中的 Python 包列表
 * Spark 定义中的包列表 

此哈希不依赖于环境名称或版本 - 如果只是重命名了环境，或者使用与现有环境完全相同的属性和包创建了新环境，则哈希值将保持不变。 但是，更改环境定义（例如添加或删除 Python 包，或更改包版本）会导致哈希值更改。 更改环境中的依赖项或通道的顺序将产生新的环境，因此需要新的映像版本。 需要注意的是，对特选环境所做的任何更改都会导致哈希失效，并产生新的“非特选”环境。

计算所得的哈希值将与工作区中和全局 ACR（或本地运行的计算目标）中的哈希值进行比较。 如果存在匹配项，则会拉取缓存的映像，否则会触发映像生成。 拉取缓存的映像的持续时间包括下载时间，而拉取新生成的映像的持续时间包括生成时间和下载时间。 

下图显示了三个环境定义。 其中两个定义包含不同的名称和版本，但其中的基础映像和 Python 包完全相同。 但它们具有相同的哈希，因此对应于同一个缓存的映像。 第三个环境包含不同的 Python 包和版本，因此对应于不同的缓存映像。

![作为 Docker 映像缓存的环境示意图](./media/concept-environments/environment-caching.png)

>[!IMPORTANT]
> 如果创建了一个包含未固定包依赖项（例如 ```numpy```）的环境，该环境将继续使用创建环境时安装的包版本。 此外，将来包含匹配定义的任何环境将继续使用旧版本。 

若要更新包，请指定版本号以强制重新生成映像，例如 ```numpy==1.18.1```。 将会安装新的依赖项（包括嵌套的依赖项），这可能会破坏以前正常工作的方案。 

> [!WARNING]
>  [Environment.build](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true#&preserve-view=truebuild-workspace--image-build-compute-none-) 方法将重新生成缓存的映像，这可能会造成更新取消固定包的负面影响，并破坏对应于该缓存映像的所有环境定义的可再现性。

## <a name="next-steps"></a>后续步骤

* 了解如何在 Azure 机器学习中[创建和使用环境](how-to-use-environments.md)。
* 查看 Python SDK 参考文档来了解[环境类](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py&preserve-view=true)。
* 查看 R SDK 参考文档来了解[环境](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments)。
