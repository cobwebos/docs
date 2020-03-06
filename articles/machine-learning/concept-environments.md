---
title: 关于 Azure 机器学习环境
titleSuffix: Azure Machine Learning
description: 在本文中，将了解机器学习环境的优点，该环境允许跨各种计算目标的可重复、可审核和可移植机器学习依赖项定义。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: trbye
author: trevorbye
ms.date: 01/06/2020
ms.openlocfilehash: 036efa27fb8d22c32f2f6bce1efe9dea300a3972
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302758"
---
# <a name="what-are-azure-machine-learning-environments"></a>什么是 Azure 机器学习的环境？
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure 机器学习环境指定有关训练和评分脚本的 Python 包、环境变量和软件设置。 它们还指定运行时间（Python、Spark 或 Docker）。 环境是在你的机器学习工作区中管理和版本化的实体，可跨各种计算目标启用可重复、可审核和便携式机器学习工作流。

你可以在本地计算上使用 `Environment` 对象来执行以下操作：
* 开发您的培训脚本。
* 按比例在 Azure 机器学习计算模型训练时，重复使用相同的环境。
* 在同一环境中部署模型。

下图说明了如何在运行配置中使用单个 `Environment` 对象进行定型，并使用你的推理和部署配置来实现 web 服务部署。

![机器学习工作流中的环境图示](./media/concept-environments/ml-environment.png)

## <a name="types-of-environments"></a>环境类型

环境可以分为三个类别：*特选*、*用户管理*和*系统管理*。

特选环境由 Azure 机器学习提供，并在默认情况下在工作区中可用。 它们包含 Python 包的集合和设置，以帮助你开始各种机器学习框架。 

在用户托管环境中，你负责设置你的环境，并在计算目标上安装定型脚本所需的每个包。 Conda 不会检查你的环境或安装任何内容。 如果要定义自己的环境，则必须列出版本 `>= 1.0.45` 为 pip 依赖项 `azureml-defaults`。 此包包含将模型托管为 web 服务所需的功能。

如果希望[Conda](https://conda.io/docs/)管理 Python 环境和脚本依赖项，请使用系统管理的环境。 默认情况下，该服务将采用这种类型的环境，因为它对不能手动配置的远程计算目标有用。

## <a name="create-and-manage-environments"></a>创建和管理环境

可以通过以下方式创建环境：

* 使用特选环境或通过定义自己的依赖项，定义新的 `Environment` 对象。
* 使用工作区中现有的 `Environment` 对象。 此方法允许一致性和可再现性依赖项。
* 从现有的 Anaconda 环境定义导入。
* 使用 Azure 机器学习 CLI

有关特定的代码示例，请参阅用于[定型和部署的重复使用环境](how-to-use-environments.md#create-an-environment)中的 "创建环境" 一节。 还可以通过工作区轻松管理环境。 它们包括以下功能：

* 提交试验时，环境会自动注册到你的工作区。 还可以手动注册它们。
* 您可以从您的工作区中提取要用于定型或部署的环境，或对环境定义进行编辑。
* 利用版本控制，您可以查看随时间变化的环境，从而确保可再现性。
* 你可以从你的环境中自动生成 Docker 映像。

有关代码示例，请参阅[用于定型和部署的重复使用环境](how-to-use-environments.md#manage-environments)中的 "管理环境" 一节。

## <a name="environment-building-caching-and-reuse"></a>环境构建、缓存和重复使用

Azure 机器学习服务将环境定义构建到 Docker 映像和 conda 环境中。 它还会缓存环境，使其可在后续的培训运行和服务终结点部署中重复使用。

### <a name="building-environments-as-docker-images"></a>将环境构建为 Docker 映像

通常，当你首次提交使用环境的运行时，Azure 机器学习服务会在与工作区关联的 Azure 容器注册表（ACR）上调用[ACR 生成任务](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview)。 然后，在工作区 ACR 上缓存构建的 Docker 映像。 在运行执行开始时，计算目标会检索该图像。

映像生成包括两个步骤：

 1. 下载基本映像并执行任何 Docker 步骤
 2. 根据环境定义中指定的 conda 依赖项构建 conda 环境。

如果指定[用户管理的依赖项](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py)，则省略第二步。 在这种情况下，你需要负责安装任何 Python 包，方法是在基础映像中包含它们，或者在第一步中指定自定义 Docker 步骤。 你还负责为 Python 可执行文件指定正确的位置。

### <a name="image-caching-and-reuse"></a>映像缓存和重复使用

如果对另一运行使用相同的环境定义，则 Azure 机器学习服务将重用工作区 ACR 中的缓存映像。 

若要查看缓存的映像的详细信息，请使用[get_image_details](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#get-image-details-workspace-)方法。

若要确定是重复使用缓存的映像还是生成新映像，服务将从环境定义计算[哈希值](https://en.wikipedia.org/wiki/Hash_table)，并将其与现有环境的哈希进行比较。 哈希基于：
 
 * 基本映像属性值
 * 自定义 docker 步骤属性值
 * Conda 定义中的 Python 包列表
 * Spark 定义中的包列表 

哈希不依赖于环境名称或版本。 环境定义更改（如添加或删除 Python 包或更改包版本）会导致哈希值更改并触发映像重新生成。 但是，如果你只是重命名环境或使用现有的属性和包来创建新的环境，则哈希值将保持不变，并使用缓存的图像。

请参阅下图，其中显示了三个环境定义。 其中两个名称和版本不同，但基本映像和 Python 包完全相同。 它们具有相同的哈希，因此对应于同一个缓存映像。 第三个环境具有不同的 Python 包和版本，因此对应于不同的缓存映像。

![作为 Docker 映像的环境缓存示意图](./media/concept-environments/environment-caching.png)

如果创建了一个具有取消固定包依赖关系的环境（例如 ```numpy```），该环境将继续使用在创建环境时安装的包版本。 此外，任何使用匹配定义的未来环境都将继续使用旧版本。 若要更新包，请指定版本号以强制重新生成映像，例如 ```numpy==1.18.1```。 请注意，将安装新的依赖项（包括嵌套的依赖项），这可能会破坏先前工作的方案

> [!WARNING]
>  在[环境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#build-workspace-)中，build 方法将重新生成缓存的映像，并将更新已取消固定的包的副作用，并打破与该缓存映像相对应的所有环境定义的可再现性。

## <a name="next-steps"></a>后续步骤

* 了解如何在 Azure 机器学习中[创建并使用环境](how-to-use-environments.md)。
* 请参阅[环境类](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py)的 Python SDK 参考文档。
* 请参阅适用于[环境](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments)的 R SDK 参考文档。
