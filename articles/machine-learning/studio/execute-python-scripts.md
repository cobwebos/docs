---
title: 执行 Python 机器学习脚本
titleSuffix: Azure Machine Learning Studio
description: 了解如何使用 "执行 Python 脚本" 模块在机器学习 Studio （经典）试验和 web 服务中使用 Python 代码。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/12/2019
ms.openlocfilehash: bfc2efca0786838d528b3019a3aff405f46ef645
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053788"
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio"></a>在 Azure 机器学习工作室中执行 Python 机器学习脚本

Python 是披肩多个数据科学家的工具中的一个重要工具。 它在典型机器学习工作流的每个阶段中使用，包括数据浏览、功能提取、模型定型和验证和部署。

本文介绍如何使用 "执行 Python 脚本" 模块在 Azure 机器学习 Studio 试验和 web 服务中使用 Python 代码。

## <a name="using-the-execute-python-script-module"></a>使用执行 Python 脚本模块

Studio 中的 Python 的主要界面是通过[执行 Python 脚本][execute-python-script]模块。 它最多可接受三个输入，生成最多两个输出，类似于 "[执行 R 脚本][execute-r-script]" 模块。 通过名为 `azureml_main`的特殊命名入口点函数将 Python 代码输入到参数框。

![执行 Python 脚本模块](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![模块参数框中的示例 python 代码](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

### <a name="input-parameters"></a>输入参数

Python 模块的输入公开为 Pandas DataFrames。 `azureml_main` 函数最多接受两个可选的 Pandas DataFrames 作为参数。

可以定位输入端口和函数参数之间的映射：

- 第一个连接的输入端口映射到函数的第一个参数。
- 第二个输入（如果已连接）映射到函数的第二个参数。
- 第三个输入用于[导入其他 Python 模块](#import-modules)。

下面显示了如何将输入端口映射到 `azureml_main` 函数的参数的更详细语义。

![输入端口配置表和生成的 Python 签名](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

### <a name="output-return-values"></a>输出返回值

`azureml_main` 函数必须返回在 Python[序列](https://docs.python.org/2/c-api/sequence.html)（如元组、列表或 NumPy 数组）中打包的单个 Pandas 数据帧。 此序列的第一个元素返回到模块的第一个输出端口。 模块的第二个输出端口用于[可视化](#visualizations)，无需返回值。 此方案如下所示。

![将输入端口映射到参数，并将值返回到输出端口](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

## <a name="translation-of-input-and-output-data-types"></a>输入和输出数据类型的转换

Studio 数据集不同于 Panda DataFrames。 因此，Studio 中的输入数据集会转换为 Pandas 数据帧，并且输出 DataFrames 将转换回 Studio 数据集。 在此转换过程中，还会执行以下转换：

 **Python 数据类型** | **Studio 翻译过程** |
| --- | --- |
| 字符串和数值| 转换为 |
| Pandas "NA" | 转换为 "缺失值" |
| 索引向量 | 不支持 |
| 非字符串列名称 | 对列名调用 `str` |
| 列名称重复 | 添加数字后缀：（1）、（2）、（3）等。

**Python 函数中的所有输入数据帧始终具有64位从0到行数减1的数字索引*

## <a id="import-modules"></a>导入现有的 Python 脚本模块

用于执行 Python 的后端基于[Anaconda](https://www.anaconda.com/distribution/)，这是一种广泛使用的科学 Python 分布。 它200包含在以数据为中心的工作负荷中最常见的 Python 包。 Studio 目前不支持使用 package 管理系统（如 Pip 或 Conda）来安装和管理外部库。  如果你发现需要合并其他库，请使用以下方案作为指导。

常见的用例是将现有 Python 脚本合并到工作室试验中。 [执行 Python 脚本][execute-python-script]模块接受包含第三个输入端口的 Python 模块的 zip 文件。 在运行时执行框架会解压缩该文件，内容将添加到 Python 解释器的库路径。 然后，`azureml_main` 入口点函数可直接导入这些模块。 

例如，请考虑包含简单“Hello, World”函数的 Hello.py 文件。

![Hello.py 文件中的用户定义函数](./media/execute-python-scripts/figure4.png)

接下来，我们创建包含 Hello.py 的 Hello.zip 文件：

![Zip 文件包含用户定义的 Python 代码](./media/execute-python-scripts/figure5.png)

将 zip 文件作为数据集上传到 Studio 中。 然后，通过将 Python 代码附加到**执行 Python 脚本**模块的第三个输入端口（如下图所示），创建并运行一个试验。

![使用 Hello 作为执行 Python 脚本模块输入的示例试验](./media/execute-python-scripts/figure6a.png)

![用户定义的 Python 代码上传为 zip 文件](./media/execute-python-scripts/figure6b.png)

模块输出显示，zip 文件未打包，并且函数 `print_hello` 已运行。

![显示用户定义函数的模块输出](./media/execute-python-scripts/figure7.png)

## <a name="accessing-azure-storage-blobs"></a>访问 Azure 存储 Blob

可以使用以下步骤访问存储在 Azure Blob 存储帐户中的数据：

1. 在本地下载[适用于 Python 的 Azure Blob 存储包](https://azuremlpackagesupport.blob.core.windows.net/python/azure.zip)。
1. 将 zip 文件作为数据集上传到工作室工作区。
1. 通过 `protocol='http'` 创建 BlobService 对象

```
from azure.storage.blob import BlockBlobService

# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='account_name', account_key='account_key', protocol='http')
```

1. 禁用存储**配置**设置选项卡中**所需的安全传输**

![禁用 Azure 门户中所需的安全传输](./media/execute-python-scripts/disable-secure-transfer-required.png)

## <a name="operationalizing-python-scripts"></a>实现 Python 脚本

发布为 web 服务时，将调用在评分实验中使用的任何[执行 Python 脚本][execute-python-script]模块。 例如，下图显示了一个计分试验，其中包含用于评估单个 Python 表达式的代码。

![用于 web 服务的 Studio 工作区](./media/execute-python-scripts/figure3a.png)

![Python Pandas 表达式](./media/execute-python-scripts/python-script-with-python-pandas.png)

从此试验创建的 web 服务将执行以下操作：

1. 采用 Python 表达式作为输入（作为字符串）
1. 将 Python 表达式发送到 Python 解释器
1. 返回一个表，其中包含表达式和计算所得的结果。

## <a id="visualizations"></a>使用可视化效果

使用 MatplotLib 创建的图形可由[执行 Python 脚本][execute-python-script]返回。 但是，图形不会像使用 R 时那样自动重定向为图像。因此，用户必须将任何绘图显式保存到 PNG 文件中。

若要从 MatplotLib 生成图像，必须执行以下步骤：

1. 从默认的基于 Qt 的呈现器将后端切换到 "聚合"。
1. 创建新的图形对象。
1. 获取轴，并将所有图形生成到其中。
1. 将图形保存到 PNG 文件。

使用 Pandas 中的 scatter_matrix 函数创建散点图矩阵的下图演示了此过程。

![用于将 MatplotLib 图形保存到图像的代码](./media/execute-python-scripts/figure-v1-8.png)

![单击 "执行 Python 脚本" 模块上的 "可视化" 以查看图形](./media/execute-python-scripts/figure-v2-9a.png)

![使用 Python 代码为示例试验可视化绘图](./media/execute-python-scripts/figure-v2-9b.png)

可以通过将多个图形保存到不同图像来返回这些图形。 Studio 运行时选取所有图像，并将其连接起来以进行可视化。

## <a name="advanced-examples"></a>高级示例

Studio 中安装的 Anaconda 环境包含常用包，例如 NumPy、SciPy 和 Scikits-learn。 这些包可在机器学习管道中有效地用于处理数据。

例如，以下试验和脚本说明了如何在 Scikits-learn 中使用系综学习器-了解如何计算数据集的功能重要性分数。 得分可用于在送入其他模型之前执行受监督的功能选择。

下面演示如何使用 Python 函数计算重要性评分，并根据评分为特征排序：

![用于按分数对功能进行排序的函数](./media/execute-python-scripts/figure8.png)

以下实验则在 Azure 机器学习工作室的“Pima Indian Diabetes”数据集中计算并返回功能的重要性得分：

![使用 Python 对 Pima indian diabetes 印度糖尿病数据集中的功能进行试验](./media/execute-python-scripts/figure9a.png)

![执行 Python 脚本模块的输出的可视化效果](./media/execute-python-scripts/figure9b.png)

## <a name="limitations"></a>限制

[执行 Python 脚本][execute-python-script]模块当前具有以下限制：

### <a name="sandboxed-execution"></a>沙盒执行

Python 运行时当前正在沙盒，不允许以持久方式访问网络或本地文件系统。 所有本地保存的文件都将隔离，并在模块完成后删除。 Python 代码无法访问运行它的计算机上的大部分目录，当前目录及其子目录除外。

### <a name="lack-of-sophisticated-development-and-debugging-support"></a>缺少复杂的开发和调试支持

Python 模块当前不支持诸如 intellisense 和调试等 IDE 功能。 此外，如果模块在运行时失败，则整个 Python 堆栈跟踪可用。 但必须在模块的输出日志中查看跟踪。 我们目前建议在诸如 IPython 的环境中开发和调试 Python 脚本，然后将代码导入模块。

### <a name="single-data-frame-output"></a>单个数据帧输出

Python 入口点仅允许将单个数据帧返回为输出。 目前不能将任意 Python 对象（如训练的模型直接返回到 Studio 运行时）。 与具有相同限制的[执行 R 脚本][execute-r-script]一样，在许多情况下，可以将对象 pickle 到字节数组中，然后将其返回到数据帧中。

### <a name="inability-to-customize-python-installation"></a>无法自定义 Python 安装

当前添加自定义 Python 模块的唯一方法是如前所述的使用 zip 文件机制。 虽然这对于小模块是可行的，但对于大型模块（特别是具有本机 Dll 的模块）或大量模块，这一点很繁琐。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅 [Python 开发人员中心](https://azure.microsoft.com/develop/python/)。

<!-- Module References -->
[execute-python-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-python-script
[execute-r-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-r-script
