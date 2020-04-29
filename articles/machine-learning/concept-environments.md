---
title: 关于 Azure 机器学习环境
titleSuffix: Azure Machine Learning
description: 在本文中，你将了解机器学习环境的优点，在该环境中可以创建跨各种计算目标的可重复、可审核且可移植的机器学习依赖项定义。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: trbye
author: trevorbye
ms.date: 03/18/2020
ms.openlocfilehash: 50ddbffd00e0cbbd0641089613aaa40d03658c9e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80064192"
---
# <a name="what-are-azure-machine-learning-environments"></a>什么是 Azure 机器学习环境？
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure 机器学习环境指定与训练和评分脚本有关的 Python 包、环境变量和软件设置。 它们还指定运行时（Python、Spark 或 Docker）。 环境是机器学习工作区中受管理且版本受控的实体，可用于创建跨各种计算目标的可再现、可审核且可移植的机器学习工作流。

可以使用本地计算机上的 `Environment` 目标执行以下操作：
* 开发训练脚本。
* 在 Azure 机器学习计算中重用相同的环境进行大规模的模型训练。
* 使用该相同环境部署你的模型。

下图说明了如何将单个 `Environment` 对象同时用于你的运行配置（用于训练）与你的推理和部署配置（用于 Web 服务部署）。

![图示：机器学习工作流中的环境](./media/concept-environments/ml-environment.png)

## <a name="types-of-environments"></a>环境类型

环境可以总体分为三类：特选类、用户管理类以及系统管理类    。

特选环境由 Azure 机器学习提供，且默认可用于你的工作区。 它们包含 Python 包和设置的集合，有助于你开始使用各种机器学习框架。 

在用户管理的环境中，你需要负责设置环境，并在计算目标上安装训练脚本所需的每个包。 Conda 不检查你的环境，也不会为你安装任何组件。 如果你定义自己的环境，则必须将版本为 `>= 1.0.45` 的 `azureml-defaults` 列出为 pip 依赖项。 此包包含将模型作为 Web 服务托管时所需的功能。

如果想让 [Conda](https://conda.io/docs/) 为你管理 Python 环境和脚本依赖项，请使用系统管理的环境。 默认情况下，该服务将采用这种类型的环境，因为它适用于非手动配置的远程计算目标。

## <a name="create-and-manage-environments"></a>创建和管理环境

可以通过以下方式创建环境：

* 定义新的 `Environment` 对象（通过使用特选环境或通过定义自己的依赖项）。
* 使用工作区中的现有 `Environment` 对象。 此方法可以确保你的依赖项的一致性和可再现性。
* 从现有 Anaconda 环境定义中进行导入。
* 使用 Azure 机器学习 CLI

有关特定的代码示例，请参阅[重用环境进行训练和部署](how-to-use-environments.md#create-an-environment)中的“创建环境”部分。 还可以通过工作区轻松管理环境。 它们包括以下功能：

* 提交试验时，环境会自动注册到你的工作区。 还可以手动注册它们。
* 可以从你的工作区中提取环境来用于训练或部署，或对环境定义进行编辑。
* 利用版本控制，你可以查看你的环境随时间的变化，从而确保可再现性。
* 你可以基于你的环境自动生成 Docker 映像。

有关代码示例，请参阅[重用环境进行训练和部署](how-to-use-environments.md#manage-environments)中的“管理环境”部分。

## <a name="environment-building-caching-and-reuse"></a>生成、缓存和重复使用环境

Azure 机器学习服务在 Docker 映像和 conda 环境中生成环境定义。 它还会缓存环境，使其可在后续的训练运行和服务终结点部署中重复使用。

### <a name="building-environments-as-docker-images"></a>以 Docker 映像的形式生成环境

通常，当你首次使用环境提交运行时，Azure 机器学习服务会在与工作区关联的 Azure 容器注册表 (ACR) 中调用一个 [ACR 生成任务](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview)。 然后，生成的 Docker 映像将在工作区 ACR 中缓存。 开始执行运行时，计算目标会检索该映像。

映像生成包括两个步骤：

 1. 下载基础映像，并执行任何 Docker 步骤
 2. 根据环境定义中指定的 conda 依赖项生成 conda 环境。

如果指定[用户管理的依赖项](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py)，则会省略第二个步骤。 在这种情况下，你需要负责安装任何 Python 包，方法是在基础映像中包含这些包，或者在第一个步骤中指定自定义 Docker 步骤。 你还要负责为 Python 可执行文件指定正确的位置。

### <a name="image-caching-and-reuse"></a>缓存和重复使用映像

如果你对另一个运行使用相同的环境定义，Azure 机器学习服务将重复使用工作区 ACR 中缓存的映像。 

若要查看缓存的映像的详细信息，请使用 [Environment.get_image_details](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#get-image-details-workspace-) 方法。

为了确定是要重复使用缓存的映像还是生成新映像，服务将从环境定义计算一个[哈希值](https://en.wikipedia.org/wiki/Hash_table)，并将其与现有环境的哈希进行比较。 计算的哈希基于：
 
 * 基础映像属性值
 * 自定义 Docker 步骤属性值
 * Conda 定义中的 Python 包列表
 * Spark 定义中的包列表 

该哈希不依赖于环境名称或版本。 更改环境定义（例如添加或删除 Python 包，或更改包版本）会导致哈希值更改并触发映像重新生成。 但是，如果只是重命名了环境，或者使用与现有环境完全相同的属性和包创建了新环境，则哈希值将保持不变，并会使用缓存的映像。

请参阅下图，其中显示了三个环境定义。 其中两个定义包含不同的名称和版本，但其中的基础映像和 Python 包完全相同。 它们具有相同的哈希，因此对应于同一个缓存映像。 第三个环境包含不同的 Python 包和版本，因此对应于不同的缓存映像。

![作为 Docker 映像缓存的环境示意图](./media/concept-environments/environment-caching.png)

>[!IMPORTANT]
> 例如```numpy```，如果创建的环境具有已取消固定的包依赖项，则该环境将继续使用_在创建环境时_安装的包版本。 此外，将来包含匹配定义的任何环境将继续使用旧版本。 

若要更新包，请指定版本号以强制重新生成映像，例如 ```numpy==1.18.1```。 请注意，将安装新的依赖项（包括嵌套的依赖项），这可能会破坏先前工作的方案。

> [!WARNING]
>  [Environment.build](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#build-workspace--image-build-compute-none-) 方法将重新生成缓存的映像，这可能会造成更新取消固定包的负面影响，并破坏对应于该缓存映像的所有环境定义的可再现性。

## <a name="next-steps"></a>后续步骤

* 了解如何在 Azure 机器学习中[创建和使用环境](how-to-use-environments.md)。
* 查看 Python SDK 参考文档来了解[环境类](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py)。
* 查看 R SDK 参考文档来了解[环境](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments)。
