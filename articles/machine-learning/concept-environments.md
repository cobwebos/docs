---
title: 什么是 ML 环境
titleSuffix: Azure Machine Learning
description: 在本文中，了解机器学习环境的优点，该环境允许跨不同计算目标的可重复、可审核和可移植机器学习依赖项定义。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: trbye
author: trevorbye
ms.date: 01/06/2020
ms.openlocfilehash: 3216248943ccc0dba788816cdba38732f9e43e14
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2020
ms.locfileid: "75930762"
---
# <a name="what-are-azure-machine-learning-environments"></a>什么是 Azure 机器学习的环境？
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

环境指定了 Python 包、环境变量和软件设置围绕定型和评分脚本以及运行时间（Python、Spark 或 Docker）。 它们是在你的 Azure 机器学习工作区中管理和版本化的实体，可跨不同的计算目标启用可重复的、可审核的和便携式机器学习工作流。

你可以在本地计算上使用环境对象来开发训练脚本、在 Azure 机器学习计算上重复使用同一环境进行大规模模型训练，甚至使用相同的环境部署模型。

下面说明了在用于定型的运行配置中以及针对 web 服务部署的推理和部署配置中，可以使用相同的环境对象。

![机器学习工作流中的环境图示](./media/concept-environments/ml-environment.png)

## <a name="types-of-environments"></a>环境类型

环境可以分为三个类别：**特选**、**用户管理**和**系统管理**。

特选环境由 Azure 机器学习提供，并在默认情况下在工作区中可用。 它们包含 Python 包的集合和设置，以帮助你开始不同的机器学习框架。 

对于用户管理的环境，你需要负责设置环境，并在计算目标上安装训练脚本所需的每个包。 Conda 将不检查你的环境，也不会安装任何组件。 请注意，如果要定义自己的环境，则必须列出版本 `>= 1.0.45` 为 pip 依赖项的 `azureml-defaults`。 此包包含将模型托管为 web 服务所需的功能。

当你希望[Conda](https://conda.io/docs/)管理 Python 环境和脚本依赖项时，将使用系统管理的环境。 默认情况下，该服务将采用这种类型的环境，因为它对不是手动配置的远程计算目标有用。

## <a name="creating-and-managing-environments"></a>创建和管理环境

可以通过以下方式创建环境：

* 使用特选环境或通过定义自己的依赖项定义新 `Environment` 对象
* 使用工作区中现有的 `Environment` 对象。 这允许与依赖项进行一致性和可再现性
* 从现有的 Anaconda 环境定义导入。
* 使用 Azure 机器学习 CLI

有关特定代码示例，请参阅操作[方法](how-to-use-environments.md#create-an-environment)。 还可以通过工作区轻松管理环境，并提供以下功能：

* 提交试验时，环境会自动注册到你的工作区。 还可以手动注册它们
* 从工作区中提取环境，并将其用于定型、部署或编辑环境定义
* 版本控制允许你查看一段时间内对环境所做的更改，并确保可再现性
* 从环境自动生成 Docker 映像

请参阅操作方法示例的[管理环境](how-to-use-environments.md#manage-environments)部分。

## <a name="next-steps"></a>后续步骤

* 了解如何在 Azure 机器学习中[创建和使用环境](how-to-use-environments.md)
* 请参阅适用于[环境类](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py)的 Python SDK 参考文档。
* 请参阅 R SDK 参考文档了解[环境](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments)。