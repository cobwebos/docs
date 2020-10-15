---
title: 示例 Jupyter Notebook
titleSuffix: Azure Machine Learning
description: 找到并使用示例 Jupyter Notebook，以便探索 Azure 机器学习 Python SDK。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: 10cf06a829b991bfe15d7b24c4fc61d0fe22eaaf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88653709"
---
# <a name="explore-azure-machine-learning-with-jupyter-notebooks"></a>使用 Jupyter Notebook 探索 Azure 机器学习

[示例 Azure 机器学习笔记本存储库](https://github.com/azure/machinelearningnotebooks)包含最新的 Azure 机器学习 Python SDK 示例。 这些 Juypter 笔记本旨在帮助你浏览 SDK，并充当你自己的机器学习项目的模型。

本文介绍如何从以下环境访问该存储库：

- [Azure 机器学习计算实例](#notebookvm)
- [自带笔记本服务器](#byo)
- [数据科学虚拟机](#dsvm)

> [!NOTE]
> 克隆此存储库以后，即可在 **tutorials** 文件夹中找到教程笔记本，在 **how-to-use-azureml** 文件夹中找到特定于功能的笔记本。

<a name="notebookvm"></a>
## <a name="get-samples-on-azure-machine-learning-compute-instance"></a>获取 Azure 机器学习计算实例的示例

若要开始使用示例，最简单的方法是完成[教程：设置环境和工作区](tutorial-1st-experiment-sdk-setup.md)。 完成后，你就有了一个预先装载了 SDK 和示例存储库的专用笔记本服务器。 不需下载，也不需安装。

<a name="byo"></a>

## <a name="get-samples-on-your-notebook-server"></a>获取笔记本服务器上的示例

若要自带笔记本服务器进行本地开发，请按照以下步骤操作：

[!INCLUDE [aml-your-server](../../includes/aml-your-server.md)]

这些说明会安装快速入门和教程笔记本所需的基础 SDK 包。 其他示例笔记本可能需要安装额外组件。 有关详细信息，请参阅[安装适用于 Python 的 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install)。

<a name="dsvm"></a>
## <a name="get-samples-on-dsvm"></a>获取 DSVM 上的示例

Data Science Virtual Machine (DSVM) 是专为研究数据科学而构建的自定义 VM 映像。 如果[创建 DSVM](how-to-configure-environment.md#dsvm)，则会为你安装和配置 SDK 和笔记本服务器。 但是，你仍然需要创建工作区并克隆示例存储库。

[!INCLUDE [aml-dsvm-server](../../includes/aml-dsvm-server.md)]

## <a name="next-steps"></a>后续步骤

浏览[示例笔记本](https://github.com/Azure/MachineLearningNotebooks)，了解 Azure 机器学习的功能。

有关 GitHub 示例项目和示例的详细信息，请参阅以下存储库：
+ [Microsoft/MLOps](https://github.com/Microsoft/MLOps)
+ [Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)

试用以下教程：

- [通过 MNIST 训练并部署图像分类模型](tutorial-train-models-with-aml.md)

- [准备数据并使用纽约市出租车数据集通过自动化机器学习来训练回归模型](tutorial-auto-train-models.md)
