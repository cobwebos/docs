---
title: 什么是 Azure Data Science Virtual Machine？
titleSuffix: Azure Data Science Virtual Machine
description: Azure Data Science Virtual Machine 概述 - Azure 云平台上易于使用的虚拟机，其中已预安装且已配置了工具和库，可进行数据科学研究。
keywords: 数据科学工具, 数据科学虚拟机, 数据科学工具, Linux 数据科学
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 04/02/2020
ms.openlocfilehash: 2bfcdfcb01e7908c199054e793d82cdfa1b726c7
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "88816331"
---
# <a name="what-is-the-azure-data-science-virtual-machine-for-linux-and-windows"></a>什么是适用于 Linux 和 Windows 的 Azure Data Science Virtual Machine？

Data Science Virtual Machine (DSVM) 是专为开展数据科学构建的 Azure 云平台上的自定义 VM 映像。 它已预安装且已预配了许多热门数据科学工具，可快速构建用于高级分析的智能应用程序。

DSVM 在以下环境中可用：

+ Windows Server 2019
+ Ubuntu 18.04 LTS

## <a name="comparison-with-azure-machine-learning"></a>与 Azure 机器学习进行比较

DSVM 是一种用于数据科学的自定义 VM 映像，而 [Azure 机器学习](https://docs.microsoft.com/azure/machine-learning/overview-what-is-azure-ml) (AzureML) 是一个端到端平台，其中包含：

+ 完全托管计算
  + 计算实例
  + 用于分布式 ML 任务的计算群集
  + 用于实时评分的推理群集
+ 数据存储（例如 Blob、ADLS Gen2、SQL DB）
+ 试验跟踪
+ 模型管理
+ 笔记本
+ 环境（管理 conda 和 R 依赖项）
+ 标记
+ 管道（自动化端到端数据科学工作流）

### <a name="comparison-with-azureml-compute-instances"></a>与 AzureML 计算实例进行比较

[Azure 机器学习计算实例](https://docs.microsoft.com/azure/machine-learning/concept-compute-instance)是完全配置的托管 VM 映像，而 DSVM 是非托管的 VM 。

下面详细介绍了这两种产品之间的主要区别：


|功能 |数据科学<br>VM |AzureML<br>计算实例  | 
|---------|---------|---------|
| 完全托管 | 否        | 是        |
|语言支持     |  Python、R、Julia、SQL、C#、<br> Java、Node.js、F#       | Python 和 R        |
|操作系统     | Ubuntu<br>Windows         |    Ubuntu     |
|已预配置的 GPU 选项     |  是       |    是     |
|纵向扩展选项 | 是 | 是 |
|SSH 访问权限    | 是        |    是     |
|RDP 访问权限    | 是        |     否    |
|内置<br>托管的 Notebooks     |   否<br>（需要其他配置）      |      是   |
|内置 SSO     | 否 <br>（需要其他配置）         |    是     |
|内置协作     | 否         | 是        |
|预安装的工具     |  Jupyter(lab)、RStudio Server、VSCode、<br> Visual Studio、PyCharm、Juno、<br>Power BI Desktop、SSMS、 <br>Microsoft Office 365、Apache Drill       |     Jupyter(lab)<br> RStudio Server   |

## <a name="sample-use-cases"></a>示例用例

下面演示了 DSVM 客户的一些常见用例。

### <a name="short-term-experimentation-and-evaluation"></a>短期实验和评估

可以使用 DSVM，专门参考我们发布的一些[示例和演练](./dsvm-samples-and-walkthroughs.md)来评估或学习新的数据科学[工具](./tools-included.md)。

### <a name="deep-learning-with-gpus"></a>使用 GPU 进行深度学习

在 DSVM 中，训练模型可以使用基于图形处理单元 (GPU) 的硬件上的深度学习算法。 利用 Azure 平台的 VM 缩放功能，DSVM 可帮助根据需要在云中使用基于 GPU 的硬件。 若要训练大型模型或者在保留相同 OS 磁盘的同时进行高速计算，可以切换到基于 GPU 的 VM。 可在 DSVM 中选择启用了 N 系列 GPU 的任意虚拟机 SKU。 请注意，Azure 免费帐户不支持启用了 GPU 的虚拟机 SKU。

Windows 版的 DSVM 预安装了 GPU 驱动程序、框架和 GPU 版本的深度学习框架。 Linux 版的 Ubuntu DSVM 上启用了基于 GPU 的深度学习。 

还可以将 Ubuntu 或 Windows 版本的 DSVM 部署到不基于 GPU 的 Azure 虚拟机。 在这种情况下，所有深度学习框架都将回退到 CPU 模式。

[详细了解可用的深度学习和 AI 框架](dsvm-tools-deep-learning-frameworks.md)。

### <a name="data-science-training-and-education"></a>数据科学训练和培训

教授数据科学课程的企业培训师和教师通常提供虚拟机映像。 该映像确保学员具有一致的设置且示例以可预测方式工作。

DSVM 创建可缓解支持和不兼容性挑战的一致设置的按需环境。 这些环境需要频繁生成，特别是短期培训课程的情况从中获益极大。


## <a name="whats-included-on-the-dsvm"></a>DSVM 中包含哪些组件？

在[此处](tools-included.md)查看 Windows 和 Linux DSVM 上的完整工具列表。

## <a name="next-steps"></a>后续步骤

通过以下文章，了解详细信息：

+ Windows:
  + [设置 Windows DSVM](provision-vm.md)
  + [Windows DSVM 上的数据科学](vm-do-ten-things.md)

+ Linux：
  + [设置 Linux DSVM (Ubuntu)](dsvm-ubuntu-intro.md)
  + [Linux DSVM 上的数据科学](linux-dsvm-walkthrough.md)
