---
title: 示例 Jupyter Notebook
titleSuffix: Azure Machine Learning service
description: 找到并使用示例 Jupyter 笔记本，以便探索 Azure 机器学习服务 Python SDK。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 07/31/2019
ms.custom: seodec18
ms.openlocfilehash: 14962b936d1c09a6c50daa7bec460ce11dbefe5d
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860393"
---
# <a name="explore-azure-machine-learning-service-with-jupyter-notebooks"></a>使用 Jupyter Notebook 探索 Azure 机器学习服务

[示例 Azure 机器学习笔记本存储库](https://github.com/azure/machinelearningnotebooks)包含最新的 Azure 机器学习 Python SDK 示例。 这些 Juypter 笔记本旨在帮助你浏览 SDK，并充当你自己的机器学习项目的模型。

本文介绍如何从以下环境访问该存储库：

- [Azure 机器学习笔记本 VM](#notebookvm)
- [自带笔记本服务器](#byo)
- [数据科学虚拟机](#dsvm)

> [!NOTE]
> 克隆此存储库以后，即可在 **tutorials** 文件夹中找到教程笔记本，在 **how-to-use-azureml** 文件夹中找到特定于功能的笔记本。

<a name="notebookvm"></a>
## <a name="get-samples-on-azure-machine-learning-notebook-vm"></a>获取 Azure 机器学习笔记本 VM 上的示例

若要开始使用示例，最简单的方法是完成[教程：设置环境和工作区](tutorial-1st-experiment-sdk-setup.md)。 完成后，你就有了一个预先装载了 SDK 和示例存储库的专用笔记本服务器。 不需下载，也不需安装。

<a name="byo"></a>

## <a name="get-samples-on-your-notebook-server"></a>获取笔记本服务器上的示例

若要自带笔记本服务器进行本地开发，请按照以下步骤操作：

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

这些说明会安装快速入门和教程笔记本所需的基础 SDK 包。 其他示例笔记本可能需要安装额外组件。 有关详细信息，请参阅[安装适用于 Python 的 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install)。

<a name="dsvm"></a>
## <a name="get-samples-on-dsvm"></a>获取 DSVM 上的示例

Data Science Virtual Machine (DSVM) 是专为研究数据科学而构建的自定义 VM 映像。 如果[创建 DSVM](how-to-configure-environment.md#dsvm)，则会为你安装和配置 SDK 和笔记本服务器。 但是，你仍然需要创建工作区并克隆示例存储库。

[!INCLUDE [aml-dsvm-server](../../../includes/aml-dsvm-server.md)]

## <a name="next-steps"></a>后续步骤

浏览[示例笔记本](https://aka.ms/aml-notebooks)，了解 Azure 机器学习服务的功能，或者尝试以下教程：

- [通过 MNIST 训练并部署图像分类模型](tutorial-train-models-with-aml.md)

- [准备数据并使用纽约市出租车数据集通过自动化机器学习来训练回归模型](tutorial-auto-train-models.md)
