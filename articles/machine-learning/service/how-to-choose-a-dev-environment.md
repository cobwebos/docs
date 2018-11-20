---
title: Azure 机器学习的开发环境 | Microsoft Docs
description: 可以与 Azure 机器学习服务一起使用的开发环境概述。 Python 3 是开发环境的唯一要求，但我们建议也使用 Conda 环境。 对于开发工具，我们建议使用 Jupyter Notebook，Azure Notebooks 和 IDE/ 代码编辑器。
services: machine-learning
author: rastala
ms.author: roastala
manager: cgronlun
ms.service: machine-learning
ms.component: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 9/24/2018
ms.openlocfilehash: 4d25e147044053aa76afe2da482b71c24efc2325
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2018
ms.locfileid: "51242868"
---
# <a name="development-environment-for-azure-machine-learning"></a>Azure 机器学习的开发环境 

了解可以与 Azure 机器学习服务一起使用的开发环境。 

Azure 机器学习服务与平台无关，不需要特定的开发环境。 它需要 Python 3，我们建议使用 Conda 创建独立的环境。 如果开发环境已满足这些要求，则可以跳过本文档并转到[配置开发环境](how-to-configure-environment.md)文档。

本文档的其余部分将讨论我们建议使用的开发环境：

* __Jupyter 笔记本__
* __Azure Notebook__
* __集成开发环境 (IDE) 和代码编辑器__
* __数据科学虚拟机__

比较这些环境很困难，因为笔记本电脑和 IDE 都可以扩展。 例如，某些 IDE 可以用作 Jupyter Notebook 的客户端。 通常情况下，笔记本专为交互式实验和可视化效果而设计。 IDE 和代码编辑器提供了提高代码质量和与外部系统集成的工具，例如版本控制。

> [!TIP]
> 使用一个环境不会阻止使用另一个环境。 笔记本和 IDE 只是工具，可以根据需要进行混合。 例如，可以开始在笔记本中进行实验，然后导出 python 脚本以在 IDE 中进行编辑和调试。
>
> 这就是 Data Science Virtual Machine 同时提供 Jupyter Notebook 和几种流行的 Python IDE 的原因。

## <a name="jupyter-notebooks"></a>Jupyter 笔记本

Jupyter Notebook 是 [Jupyter 项目](https://jupyter.org/)的一部分。 他们专注于提供交互式编码体验，可以在其中创建将实时代码与叙述性文本和图形混合在一起的文档。 可以在各种平台上安装 Jupyter Notebook。

Jupyter Notebook 安装完成后，可以根据需要安装和配置环境。 但是，要负责维护系统。

## <a name="azure-notebooks"></a>Azure Notebook

[Azure Notebooks](https://notebooks.azure.com)（预览版）是 Azure 云中的笔记本环境。 它基于 Jupyter，并提供预先加载流行库的环境。 Azure 机器学习 SDK 已经安装在 Azure Notebooks 中，因此可以几乎不进行任何设置就开始试验。

Azure Notebooks 还简化了共享笔记本的过程。 可以共享笔记本的 URL、公开库或者从 Azure Notebooks 界面共享社交媒体上的 URL。

Azure Notebooks 的缺点是无法完全控制环境，并且可能无法安装所需的自定义软件。 它也是一个共享环境，因此笔记本可能比专用的 Jupyter Notebook 安装速度慢。

## <a name="ides-and-code-editors"></a>IDE 和代码编辑器

有许多 IDE 和代码编辑器可以与 Azure 机器学习一起使用。 唯一的软件要求是 Azure 机器学习 SDK，可以使用 `pip` 实用程序进行安装。

我们建议使用 [Visual Studio Code](https://code.visualstudio.com/)，因为它提供了使用 Python 语言和 Azure 机器学习的工具。 它适用于 Linux、macOS 和 Windows 平台。

## <a name="data-science-virtual-machine"></a>数据科学虚拟机

数据科学虚拟机 (DSVM) 是上述环境的组合。 它是 Azure 平台上的一个 VM，预装了 Jupyter Notebook，Visual Studio Code 和 Azure 机器学习 SDK。 由于所需的软件已预先安装在 VM 映像中，因此可以在创建 VM 后快速开始尝试 Azure 机器学习。

DSVM 允许选择所需的计算资源，例如 CPU、GPU 和内存。 它还预装了其他编辑器，如 PyCharm，以及流行的机器学习软件，如 TensorFlow、Keras 和 PyTorch。 如果未安装所需的软件，则可以自行安装。

有关预安装内容的更多信息，请参阅[什么是 Data Science Virtual Machine](../data-science-virtual-machine/overview.md) 文档。

## <a name="next-steps"></a>后续步骤

现在，你已了解有关开发环境，了解[如何配置开发环境](how-to-configure-environment.md)。

