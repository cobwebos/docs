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
ms.openlocfilehash: 7fdf9c52df502bc94519ab6c65be2e9fb546ce48
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699143"
---
# <a name="explore-azure-machine-learning-service-with-jupyter-notebooks"></a>使用 Jupyter Notebook 探索 Azure 机器学习服务

[示例 Azure 机器学习笔记本存储库](https://github.com/azure/machinelearningnotebooks)包含最新的 Azure 机器学习 Python SDK 示例。 这些 Juypter 笔记本旨在帮助你浏览 SDK，并充当你自己的机器学习项目的模型。

本文介绍如何从以下环境访问该存储库：

- [Azure 机器学习笔记本 VM](#notebookvm)
- [自带笔记本服务器](#byo)
- [数据科学虚拟机](#dsvm)
- [Azure Notebook](#aznb)

> [!NOTE]
> 克隆此存储库以后，即可在 **tutorials** 文件夹中找到教程笔记本，在 **how-to-use-azureml** 文件夹中找到特定于功能的笔记本。

<a name="notebookvm"></a>
## <a name="get-samples-on-azure-machine-learning-notebook-vm"></a>获取 Azure 机器学习笔记本 VM 上的示例

若要开始使用示例，最简单的方法是完成[基于云的笔记本快速入门](quickstart-run-cloud-notebook.md)。 完成后，你就有了一个预先装载了 SDK 和示例存储库的专用笔记本服务器。 不需下载，也不需安装。

<a name="byo"></a>

## <a name="get-samples-on-your-notebook-server"></a>获取笔记本服务器上的示例

若要自带笔记本服务器进行本地开发，请按照以下步骤操作：

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

这些说明会安装快速入门和教程笔记本所需的基础 SDK 包。 其他示例笔记本可能需要安装额外组件。 有关详细信息，请参阅[安装适用于 Python 的 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install)。

<a name="dsvm"></a>
## <a name="get-samples-on-dsvm"></a>获取 DSVM 上的示例

Data Science Virtual Machine (DSVM) 是专为研究数据科学而构建的自定义 VM 映像。 如果[创建 DSVM](how-to-configure-environment.md#dsvm)，则会为你安装和配置 SDK 和笔记本服务器。 但是，你仍然需要创建工作区并克隆示例存储库。

[!INCLUDE [aml-dsvm-server](../../../includes/aml-dsvm-server.md)]

<a name="aznb"></a>
## <a name="get-samples-on-azure-notebooks"></a>获取 Azure Notebooks 上的示例

在 [Azure Notebooks](https://notebooks.azure.com/) 上，会为你安装和配置 SDK 和笔记本服务器。 Azure Notebooks 提供了一个可供浏览的完全托管式轻量级笔记本环境。

若要访问 Azure Notebooks 上的示例存储库，请通过 [Azure 门户](https://portal.azure.com)导航到 Azure 机器学习工作区。 在“概览”部分选择“Azure Notebooks 入门”。  

## <a name="next-steps"></a>后续步骤

浏览[示例笔记本](https://aka.ms/aml-notebooks)，了解 Azure 机器学习服务的功能，或者尝试以下教程：

- [通过 MNIST 训练并部署图像分类模型](tutorial-train-models-with-aml.md)

- [准备数据并使用纽约市出租车数据集通过自动化机器学习来训练回归模型](tutorial-data-prep.md)
