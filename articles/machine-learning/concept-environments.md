---
title: 关于 Azure 机器学习环境
titleSuffix: Azure Machine Learning
description: 在本文中，了解机器学习环境的优势，这些环境支持跨各种计算目标的可重现、可审核和可移植的机器学习依赖定义。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: trbye
author: trevorbye
ms.date: 03/18/2020
ms.openlocfilehash: 50ddbffd00e0cbbd0641089613aaa40d03658c9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064192"
---
# <a name="what-are-azure-machine-learning-environments"></a>什么是 Azure 机器学习环境？
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure 机器学习环境指定有关定型脚本和评分脚本的 Python 包、环境变量和软件设置。 它们还指定运行时间（Python、Spark 或 Docker）。 这些环境是机器学习工作区中的管理和版本化实体，可跨各种计算目标实现可重现、可审核和可移植的机器学习工作流。

您可以将本地计算上`Environment`的对象用于：
* 开发您的培训脚本。
* 在 Azure 机器学习计算上重用相同的环境，以便大规模进行模型培训。
* 使用相同的环境部署模型。

下图说明了如何在运行配置、培训以及推理和`Environment`部署配置中使用单个对象进行 Web 服务部署。

![机器学习工作流中环境图](./media/concept-environments/ml-environment.png)

## <a name="types-of-environments"></a>环境类型

环境大致可以分为三类：*策划*、*用户管理和**系统管理*。

特选环境由 Azure 机器学习提供，且默认可用于你的工作区。 它们包含 Python 包和设置的集合，可帮助您开始使用各种机器学习框架。 

在用户管理的环境中，您负责设置环境并安装训练脚本在计算目标上所需的每个包。 康达不会检查您的环境或为您安装任何内容。 如果要定义自己的环境，则必须将版本`azureml-defaults``>= 1.0.45`列为点依赖项。 此包包含将模型托管为 Web 服务所需的功能。

当您希望[Conda](https://conda.io/docs/)为您管理 Python 环境和脚本依赖项时，可以使用系统管理的环境。 默认情况下，该服务假定这种类型的环境，因为它对无法手动配置的远程计算目标很有用。

## <a name="create-and-manage-environments"></a>创建和管理环境

您可以通过：

* 通过使用精心策划的`Environment`环境或定义自己的依赖项来定义新对象。
* 使用工作区`Environment`中的现有对象。 此方法允许与您的依赖项保持一致和重现。
* 从现有的 Anaconda 环境定义导入。
* 使用 Azure 机器学习 CLI

有关特定代码示例，请参阅[用于培训和部署 的"重用环境](how-to-use-environments.md#create-an-environment)"部分。 环境也可通过工作区轻松管理。 它们包括以下功能：

* 提交实验时，环境将自动注册到工作区。 也可以手动注册它们。
* 可以从工作区获取环境以用于训练或部署，或对环境定义进行编辑。
* 通过版本控制，您可以看到环境随时间的变化，从而确保可重现性。
* 可以从环境中自动生成 Docker 映像。

有关代码示例，请参阅[用于培训和部署 的"重用环境](how-to-use-environments.md#manage-environments)"部分。

## <a name="environment-building-caching-and-reuse"></a>环境建设、缓存和重用

Azure 机器学习服务将环境定义构建到 Docker 映像和 conda 环境中。 它还缓存环境，以便在后续训练运行和服务终结点部署中重用它们。

### <a name="building-environments-as-docker-images"></a>将环境构建为 Docker 映像

通常，当您首次使用环境提交运行时，Azure 机器学习服务将调用与工作区关联的 Azure 容器注册表 （ACR） 上的[ACR 生成任务](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview)。 然后，生成的 Docker 映像将缓存在工作区 ACR 上。 在运行执行开始时，计算目标检索图像。

映像生成由两个步骤组成：

 1. 下载基本映像，并执行任何 Docker 步骤
 2. 根据环境定义中指定的 conda 依赖关系构建 conda 环境。

如果指定[用户管理的依赖项，](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py)则省略第二步。 在这种情况下，您负责安装任何 Python 包，通过在基本映像中包括它们，或在第一步中指定自定义 Docker 步骤。 您还负责为 Python 可执行文件指定正确的位置。

### <a name="image-caching-and-reuse"></a>图像缓存和重用

如果对另一次运行使用相同的环境定义，Azure 机器学习服务将重用工作区 ACR 中的缓存映像。 

要查看缓存映像的详细信息，请使用[环境.get_image_details](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#get-image-details-workspace-)方法。

要确定是重用缓存映像还是生成新映像，服务将从环境定义中计算[哈希值](https://en.wikipedia.org/wiki/Hash_table)，并将其与现有环境的哈希值进行比较。 哈希基于：
 
 * 基本图像属性值
 * 自定义 Docker 步骤属性值
 * 康达定义中的 Python 包列表
 * Spark 定义中的包列表 

哈希不依赖于环境名称或版本。 环境定义更改（如添加或删除 Python 包或更改包版本）会导致哈希值更改并触发映像重建。 但是，如果只是重命名环境或创建具有现有环境的确切属性和包的新环境，则哈希值保持不变，并使用缓存的图像。

请参阅下图，该图显示了三个环境定义。 其中两个的名称和版本不同，但基本映像和 Python 包相同。 它们具有相同的哈希，因此对应于相同的缓存图像。 第三个环境具有不同的 Python 包和版本，因此对应于不同的缓存映像。

![环境缓存图作为 Docker 映像](./media/concept-environments/environment-caching.png)

>[!IMPORTANT]
> 例如```numpy```，如果创建具有未固定包依赖项的环境，则该环境将继续使用_创建环境时安装的_包版本。 此外，任何具有匹配定义的将来环境都将继续使用旧版本。 

要更新包，请指定一个版本号以强制映像重建，例如```numpy==1.18.1```。 请注意，将安装新的依赖项（包括嵌套依赖项），这些依赖项可能会破坏以前的工作方案。

> [!WARNING]
>  [Environment.build](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#build-workspace--image-build-compute-none-)方法将重建缓存映像，并可能具有更新未固定包和破坏与该缓存映像对应的所有环境定义的重现性的副作用。

## <a name="next-steps"></a>后续步骤

* 了解如何在 Azure 机器学习中[创建和使用环境](how-to-use-environments.md)。
* 请参阅[环境类](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py)的 Python SDK 参考文档。
* 请参阅[环境](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments)的 R SDK 参考文档。
