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
ms.openlocfilehash: 8906299cc9e2c000dab2ac9d2a345d9aaf238260
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045862"
---
# <a name="what-are-azure-machine-learning-environments"></a>什么是 Azure 机器学习的环境？
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure 机器学习环境指定有关训练和评分脚本的 Python 包、环境变量和软件设置。 它们还指定运行时间（Python、Spark 或 Docker）。 它们是在你的机器学习工作区中管理和版本化的实体，可跨多种计算目标启用可重复的、可审核的和便携式机器学习工作流。

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

## <a name="next-steps"></a>后续步骤

* 了解如何在 Azure 机器学习中[创建并使用环境](how-to-use-environments.md)。
* 请参阅[环境类](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py)的 Python SDK 参考文档。
* 请参阅适用于[环境](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments)的 R SDK 参考文档。
