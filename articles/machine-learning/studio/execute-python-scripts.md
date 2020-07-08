---
title: 执行 Python 脚本
titleSuffix: ML Studio (classic) - Azure
description: 了解如何通过“执行 Python 脚本”模块在机器学习工作室（经典版）试验和 Web 服务中使用 Python 代码。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: tracking-python, previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/12/2019
ms.openlocfilehash: 4afb6dca94642ab9b908a4f07ff5de56677626f2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84696297"
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio-classic"></a>在 Azure 机器学习工作室（经典版）中执行 Python 机器学习脚本

Python 是许多数据科学家珍藏的一个有用工具。 它可以在典型机器学习工作流的每个阶段（包括数据探索、特征提取、模型训练和验证，以及部署）中使用。

本文介绍如何通过“执行 Python 脚本”模块在 Azure 机器学习工作室（经典版）试验和 Web 服务中使用 Python 代码。

## <a name="using-the-execute-python-script-module"></a>使用“执行 Python 脚本”模块

与工作室（经典版）中的 Python 对接的主要方式是通过[执行 Python 脚本][execute-python-script]模块。 此模块最多接受三个输入，最多生成两个输出，这类似于[执行 R 脚本][execute-r-script]模块。 通过专门命名为 `azureml_main` 的入口点函数将 Python 代码输入参数框中。

![“执行 Python 脚本”模块](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![模块参数框中的示例 Python 代码](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

### <a name="input-parameters"></a>输入参数

Python 模块的输入公开为 Pandas 数据帧。 `azureml_main` 函数最多接受两个可选的 Pandas 数据帧作为参数。

可以定位输入端口和函数参数之间的映射：

- 第一个连接的输入端口映射到函数的第一个参数。
- 第二个输入（如果已连接）映射到函数的第二个参数。
- 第三个输入用于[导入其他 Python 模块](#import-modules)。

下面显示了有关输入端口如何映射到 `azureml_main` 函数参数的更详细语义。

![输入端口配置和生成的 Python 签名的表格](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

### <a name="output-return-values"></a>输出返回值

`azureml_main` 函数必须返回打包在 Python [序列](https://docs.python.org/2/c-api/sequence.html)内部的单个 Pandas 数据帧，例如元组、列表或 NumPy 数组。 然后，此序列的第一个元素返回到模块的第一个输出端口。 模块的第二个输出端口用于[可视化](#visualizations)，不需要返回值。 下面显示了此方案。

![将输入端口映射到参数，并向输出端口返回值。](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

## <a name="translation-of-input-and-output-data-types"></a>输入和输出数据类型的转换

工作室数据库与 Panda 数据帧不同。 因此，工作室（经典版）中的输入数据集将转换为 Pandas 数据帧，输出数据帧将转换回工作室（经典版）数据集。 在此转换过程中还会执行以下转换：

 **Python 数据类型** | **工作室转换过程** |
| --- | --- |
| 字符串和数字| 转换为 |
| Pandas "NA" | 转换为“缺失值” |
| 索引向量 | 不支持* |
| 非字符串列名 | 对列名调用 `str` |
| 复制列名 | 添加数字后缀：(1)、(2)、(3) 等。

Python 函数中的所有输入数据帧始终具有 64 位的数字索引，范围从 0 到行数减去 1 的值* 

## <a name="importing-existing-python-script-modules"></a><a id="import-modules"></a>导入现有的 Python 脚本模块

用于执行 Python 的后端基于 [Anaconda](https://www.anaconda.com/distribution/)（广泛使用的 Python 科研分发版）。 它随附了以数据为中心的工作负荷中最常用的将近 200 个 Python 包。 工作室（经典版）目前不支持使用 Pip 或 Conda 等包管理系统来安装和管理外部库。  如果需要整合其他库，请参考以下方案。

常见的用例是将现有 Python 脚本整合到工作室（经典版）试验中。 [执行 Python 脚本][execute-python-script]模块接受在第三个输入端口上使用包含 Python 模块的 zip 文件。 在运行时执行框架会解压缩该文件，内容将添加到 Python 解释器的库路径。 然后，`azureml_main` 入口点函数可直接导入这些模块。 

例如，请考虑包含简单“Hello, World”函数的 Hello.py 文件。

![Hello.py 文件中用户定义的函数](./media/execute-python-scripts/figure4.png)

接下来，我们创建包含 Hello.py 的 Hello.zip 文件：

![包含用户定义的 Python 代码的 zip 文件](./media/execute-python-scripts/figure5.png)

将 zip 文件作为数据集上传到工作室（经典版）中。 然后，创建使用 Hello.zip 文件中的 Python 代码的试验，并通过将此试验附加到“执行 Python 脚本”模块的第三个输入端口来运行此试验，如下图所示。 

![使用 Hello.zip 的示例试验，用作“执行 Python 脚本”模块的输入](./media/execute-python-scripts/figure6a.png)

![作为 zip 文件上传的用户定义的 Python 代码](./media/execute-python-scripts/figure6b.png)

模块输出显示，zip 文件未打包，并且函数 `print_hello` 已运行。

![显示用户定义的函数的模块输出](./media/execute-python-scripts/figure7.png)

## <a name="accessing-azure-storage-blobs"></a>访问 Azure 存储 Blob

可以使用以下步骤访问存储在 Azure Blob 存储帐户中的数据：

1. 在本地下载[适用于 Python 的 Azure Blob 存储包](https://azuremlpackagesupport.blob.core.windows.net/python/azure.zip)。
1. 将 zip 文件作为数据集上传到工作室（经典版）工作区。
1. 使用 `protocol='http'` 创建 BlobService 对象

```
from azure.storage.blob import BlockBlobService

# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='account_name', account_key='account_key', protocol='http')
```

1. 在存储的“配置”设置选项卡中禁用“需要安全传输”  

![在 Azure 门户中禁用“需要安全传输”](./media/execute-python-scripts/disable-secure-transfer-required.png)

## <a name="operationalizing-python-scripts"></a>实现 Python 脚本

发布为 Web 服务时，将调用在评分试验中使用的任何[执行 Python 脚本][execute-python-script]模块。 例如，下图显示了包含用于评估单个 Python 表达式的代码的评分试验。

![Web 服务的工作室工作区](./media/execute-python-scripts/figure3a.png)

![Python Pandas 表达式](./media/execute-python-scripts/python-script-with-python-pandas.png)

从此试验创建的 Web 服务将执行以下操作：

1. 将 Python 表达式用作输入（字符串）
1. 将 Python 表达式发送到 Python 解释器
1. 返回包含表达式和评估结果的表格。

## <a name="working-with-visualizations"></a><a id="visualizations"></a>使用可视化效果

使用 MatplotLib 创建的绘图可由[执行 Python 脚本][execute-python-script]返回。 但是，在使用 R 时，绘图不会按原样自动重定向到图像。因此，用户必须显式将所有绘图保存到 PNG 文件。

若要从 MatplotLib 生成图像，必须执行以下步骤：

1. 将后端由默认的基于 Qt 的呈现器切换为“AGG”。
1. 创建新图形对象。
1. 获取轴，并将所有绘图生成到其中。
1. 将图形保存到 PNG 文件。

此过程在 Pandas 中使用 scatter_matrix 函数创建散点图矩阵，下图对此做了演示。

![将 MatplotLib 图形保存为图像的代码](./media/execute-python-scripts/figure-v1-8.png)

![单击“执行 Python 脚本”模块中的“可视化”以查看图形](./media/execute-python-scripts/figure-v2-9a.png)

![使用 Python 代码可视化示例试验的绘图](./media/execute-python-scripts/figure-v2-9b.png)

可以通过将多个图形保存到不同的图像来返回这些图形。 工作室（经典版）运行时将选择所有图像，并将其串联以进行可视化。

## <a name="advanced-examples"></a>高级示例

安装在工作室（经典版）中的 Anaconda 环境包含常用包，例如 NumPy、SciPy 和 Scikits-Learn。 可以针对机器学习管道中的数据处理有效使用这些包。

例如，以下试验和脚本演示如何在 Scikits-Learn 中使用系综学习器计算数据集的特征重要性评分。 在馈送到另一模型之前，评分可用于执行监督式特征选择。

下面演示如何使用 Python 函数计算重要性评分，并根据评分为特征排序：

![按评分将特征排名的函数](./media/execute-python-scripts/figure8.png)

以下试验在 Azure 机器学习工作室（经典版）的“Pima Indian Diabetes”数据集中计算并返回特征的重要性评分：

![使用 Python 为 Pima Indian Diabetes 数据集中的特征排名的试验](./media/execute-python-scripts/figure9a.png)

![“执行 Python 脚本”模块的输出可视化效果](./media/execute-python-scripts/figure9b.png)

## <a name="limitations"></a>限制

[执行 Python 脚本][execute-python-script]模块目前存在以下限制：

### <a name="sandboxed-execution"></a>沙盒执行

Python 运行时目前在沙盒中执行，不允许持续访问网络或本地文件系统。 所有本地保存的文件都将隔离，并在模块完成后删除。 Python 代码无法访问运行它的计算机上的大部分目录，当前目录及其子目录除外。

### <a name="lack-of-sophisticated-development-and-debugging-support"></a>缺少先进的开发和调试支持

Python 模块当前不支持诸如 intellisense 和调试等 IDE 功能。 此外，如果模块在运行时失败，则整个 Python 堆栈跟踪可用。 但必须在模块的输出日志中查看跟踪。 我们目前建议在诸如 IPython 的环境中开发和调试 Python 脚本，然后将代码导入模块。

### <a name="single-data-frame-output"></a>单数据帧输出

Python 入口点仅允许将单个数据帧返回为输出。 目前无法将诸如训练的模型等任意 Python 对象直接返回到工作室（经典版）运行时。 就像存在相同限制的[执行 R 脚本][execute-r-script]一样，在许多情况下可将对象转换为字节数组，并在数据帧中返回该字节数组。

### <a name="inability-to-customize-python-installation"></a>无法自定义 Python 安装

当前添加自定义 Python 模块的唯一方法是如前所述的使用 zip 文件机制。 虽然这对小模块可行，但对大模块（尤其是具有本机 DLL 的模块）或大量模块而言却很麻烦。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅 [Python 开发人员中心](https://azure.microsoft.com/develop/python/)。

<!-- Module References -->
[execute-python-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-python-script
[execute-r-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-r-script
