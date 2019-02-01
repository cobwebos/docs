---
title: 示例 Jupyter Notebook
titleSuffix: Azure Machine Learning service
description: 找到并使用示例 Jupyter 笔记本，以便在 Python 中探索 Azure 机器学习服务。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 5ba555ad31545e1ae1aa822ec58b0bd22ef486ac
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2019
ms.locfileid: "55295145"
---
# <a name="use-jupyter-notebooks-to-explore-azure-machine-learning-service"></a>使用 Jupyter 笔记本探索 Azure 机器学习服务

为了方便用户，我们开发了一系列 Jupyter Python 笔记本，用于探索 Azure 机器学习服务。 

了解如何将服务与有关本站点的文档配合使用，并使用这些笔记本，根据情况来自定义它们。 

使用下面的路径之一，以便运行笔记本服务器和这些示例笔记本。  待服务器运行以后，在 **tutorials** 文件夹中找到教程笔记本，或者在 **how-to-use-azureml** 文件夹中浏览不同的功能。


## <a name="try-azure-notebooks-free-jupyter-notebooks-in-the-cloud"></a>试用 Azure Notebook：云中免费的 Jupyter Notebook

Azure Notebooks 的入门很容易！ 已在 [Azure Notebooks](https://notebooks.azure.com/) 上安装和配置[用于 Python 的 Azure 机器学习 SDK](https://aka.ms/aml-sdk)。 安装和未来的更新通过 Azure 服务自动管理。
  
[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]


## <a name="use-a-data-science-virtual-machine-dsvm"></a>使用 Data Science Virtual Machine (DSVM)

已在 DSVM 上安装和配置[用于 Python 的 Azure 机器学习 SDK](https://aka.ms/aml-sdk) 和笔记本服务器。 

[创建 DSVM](how-to-configure-environment.md#dsvm) 以后，在 DSVM 上根据这些步骤运行笔记本。

[!INCLUDE [aml-dsvm-server](../../../includes/aml-dsvm-server.md)]


## <a name="use-your-own-jupyter-notebook-server"></a>使用自己的 Jupyter Notebook 服务器

执行这些步骤，在计算机上创建本地 Jupyter Notebook 服务器。

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

<a name="automated-ml-setup"></a>

## <a name="automated-machine-learning-setup"></a>自动化机器学习设置 

这些步骤仅适用于 **how-to-use-azureml/automated-machine-learning** 文件夹中的笔记本。

虽然可以使用上述任意选项，但同时也可按照以下说明来安装环境并创建工作区。 

1. 安装 [Mini-conda](https://conda.io/miniconda.html)。 选择 3.7 或更高版本。 按照提示进行安装。 
   >[!NOTE]
   >可以使用现有的 conda，只要它是 4.4.10 或更高版本即可。 使用 `conda -V` 来显示版本。 可以使用 `conda update conda` 命令来更新 conda 版本。 无需专门安装 mini-conda。

1. 从 [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning
) 下载 zip 格式的示例笔记本，将内容解压缩到本地目录。 自动化机器学习笔记本在 `how-to-use-azureml/automated-machine-learning` 文件夹中。

1. 设置新的 Conda 环境。 
   1. 在本地计算机上打开 Conda 提示符。
   
   1. 导航到已解压缩到本地计算机上的文件。
   
   1. 打开 **automated-machine-learning** 文件夹。
   
   1. 在 Windows 版 conda 提示符中执行 `automl_setup.cmd`，或者执行适用于操作系统的 `.sh` 文件。 执行起来可能需要大约 10 分钟。

      设置脚本：
      + 创建新的 conda 环境
      + 安装所需的包
      + 配置小组件
      + 启动 Jupyter 笔记本
      
   >[!NOTE]
   > 脚本采用 conda 环境名称作为可选参数。 默认的 conda 环境名称为 `azure_automl`。 具体的命令取决于操作系统。 如果要创建新环境或升级到新版本，这将非常有用。 例如，可以使用“automl_setup.cmd azure_automl_sandbox”来创建环境名称 azure_automl_sandbox。 
      
1. 脚本完成后，会在浏览器中看到一个 Jupyter 笔记本主页。

1. 导航到保存笔记本的路径。 

1. 打开 automated-machine-learning 文件夹，然后打开 **configuration.ipynb** 笔记本。 

1. 在笔记本中执行相关单元，以便注册机器学习服务资源提供程序并创建一个工作区。

现在可以打开并运行保存在本地计算机上的笔记本了。


## <a name="next-steps"></a>后续步骤

浏览[适用于 Azure 机器学习服务的 GitHub 笔记本存储库](https://aka.ms/aml-notebooks)

试用以下教程：
+ [通过 MNIST 训练并部署图像分类模型](tutorial-train-models-with-aml.md)

+ [准备数据并使用纽约市出租车数据集通过自动化机器学习来训练回归模型](tutorial-data-prep.md)
