---
title: 执行 Python 机器学习脚本
titleSuffix: Azure Machine Learning Studio
description: 了解如何在 Azure 机器学习工作室中使用 Python。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/12/2019
ms.openlocfilehash: 4b4f3877b56752756050de0af226571ac2a93293
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "57888139"
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio"></a>在 Azure 机器学习工作室中执行 Python 机器学习脚本

Python 是许多数据科学家工具衣柜中一个重要的工具。 在典型机器学习工作流包括数据浏览、 提取特征、 模型训练和验证，以及部署每个阶段。

本文介绍如何使用执行 Python 脚本模块，若要使用 Azure 机器学习工作室试验和 web 服务中的 Python 代码。

## <a name="using-the-execute-python-script-module"></a>使用执行 Python 脚本模块

在 Studio 中 Python 主接口是通过[执行 Python 脚本][ execute-python-script]模块。 它接受最多三个输入，生成最多两个输出，类似于[执行 R 脚本][ execute-r-script]模块。 Python 代码输入到通过一个名为的特殊命名的入口点函数的参数框`azureml_main`。

![执行 Python 脚本模块](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![模块参数框中的示例 python 代码](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

### <a name="input-parameters"></a>输入参数

Python 模块的输入公开为 Pandas 数据帧。 `azureml_main`函数接受最多两个可选的 Pandas Dataframe 作为参数。

可以定位输入端口和函数参数之间的映射：

- 第一个连接的输入端口映射到函数的第一个参数。
- 第二个输入（如果已连接）映射到函数的第二个参数。
- 第三个输入是用于[导入其他 Python 模块](#import-modules)。

更多详细的输入的端口如何映射到参数的语义`azureml_main`函数如下所示。

![输入的端口配置和生成的 Python 签名的表](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

### <a name="output-return-values"></a>输出返回值

`azureml_main`函数必须返回打包在 Python 中单个 Pandas 数据帧[序列](https://docs.python.org/2/c-api/sequence.html)元组、 列表或 NumPy 数组等。 此序列的第一个元素返回到该模块的第一个输出端口。 用于模块的第二个输出端口[可视化效果](#visualizations)并且不需要返回值。 此方案如下所示。

![映射输入参数的端口，并向输出端口返回值](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

## <a name="translation-of-input-and-output-data-types"></a>输入和输出数据类型的转换

Studio 数据集不与 Panda 数据帧相同。 因此，在 Studio 中的输入数据集转换为 Pandas 数据帧和输出数据帧转换回 Studio 数据集。 在此转换过程中，还执行以下转换：

 **Python 的数据类型** | **Studio 转换过程** |
| --- | --- |
| 字符串和数字| 按原样转换 |
| Pandas NA | 转换为 Missing 值 |
| 索引矢量在 | 不受支持 * |
| 非字符串列名称 | 调用`str`列名称 |
| 重复的列名称 | 添加数字后缀：(1)，(2)，(3)，依此类推。

**Python 函数中的所有输入的数据帧始终具有 64 位数字索引中从 0 到减 1 的行数*

## <a id="import-modules"></a>导入现有的 Python 脚本模块

用于执行 Python 的后端基于[Anaconda](https://store.continuum.io/cshop/anaconda/)、 广泛使用科学记数法 Python 分发版。 它附带接近 200 个以数据为中心的工作负荷中使用的最常见的 Python 程序包。 Studio 目前不支持使用 Pip 或 Conda 安装和管理外部库等包管理系统。  如果您发现需要合并其他库，请使用以下方案中作为指南。

一个常见用例是将现有 Python 脚本包含到 Studio 实验。 [执行 Python 脚本][ execute-python-script]模块接受包含第三个输入端口处的 Python 模块的 zip 文件。 在运行时执行框架会解压缩该文件，内容将添加到 Python 解释器的库路径。 然后，`azureml_main` 入口点函数可直接导入这些模块。 

例如，请考虑包含简单“Hello, World”函数的 Hello.py 文件。

![Hello.py 文件中的用户定义函数](./media/execute-python-scripts/figure4.png)

接下来，我们创建包含 Hello.py 的 Hello.zip 文件：

![包含用户定义的 Python 代码的 zip 文件](./media/execute-python-scripts/figure5.png)

将 zip 文件作为数据集上载到 Studio。 然后，创建并运行一个试验，将其附加到的第三个输入端口 Hello.zip 文件中使用 Python 代码**执行 Python 脚本**模块，在下图中所示。

![执行 Python 脚本模块的输入为 Hello.zip 示例实验](./media/execute-python-scripts/figure6a.png)

![用户定义的 Python 代码作为 zip 文件上传](./media/execute-python-scripts/figure6b.png)

模块输出显示，zip 文件未打包，并且函数 `print_hello` 已运行。

![模块输出显示了用户定义函数](./media/execute-python-scripts/figure7.png)

## <a name="accessing-azure-storage-blobs"></a>访问 Azure 存储 Blob

您可以访问数据存储在 Azure Blob 存储帐户使用以下步骤：

1. 下载[适用于 Python 的 Azure Blob 存储包](https://azuremlpackagesupport.blob.core.windows.net/python/azure.zip)本地。
1. 将 zip 文件上载到工作室工作区作为数据集。
1. 创建使用 BlobService 对象 `protocol='http'`

```
from azure.storage.blob import BlockBlobService

# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='account_name', account_key='account_key', protocol='http')
```

1. 禁用**需要安全传输**存储中**配置**设置选项卡

![禁用在 Azure 门户中需要安全传输](./media/execute-python-scripts/disable-secure-transfer-required.png)

## <a name="operationalizing-python-scripts"></a>实现 Python 脚本

任何在评分实验中使用的[执行 Python 脚本][execute-python-script]模块在发布为 Web 服务时都将受到调用。 例如下, 图显示了包含用于评估单个 Python 表达式的代码的评分实验。

![Web 服务的的工作室工作区](./media/execute-python-scripts/figure3a.png)

![Python Pandas 表达式](./media/execute-python-scripts/python-script-with-python-pandas.png)

创建基于此实验的 web 服务将执行以下操作：

1. 采用 Python 表达式 （作为字符串） 作为输入
1. 将 Python 表达式发送到 Python 解释器
1. 返回包含表达式和评估的结果的表。

## <a id="visualizations"></a>使用可视化效果

可以通过返回使用 MatplotLib 创建绘图[执行 Python 脚本][execute-python-script]。 但是，绘图不会自动重定向到映像时使用。因此用户必须显式将任何绘图保存为 PNG 文件。

若要从 MatplotLib 生成图像，必须执行以下步骤：

1. 从默认的基于 Qt 的呈现器切换为"AGG"的后端。
1. 创建新的图形对象。
1. 获取轴，并所有图形都生成到它。
1. 将图形保存为 PNG 文件。

此过程演示了创建散点图矩阵在 Pandas 中使用 scatter_matrix 函数的以下映像。

![若要将 MatplotLib 图形保存到图像的代码](./media/execute-python-scripts/figure-v1-8.png)

![若要查看的数字执行 Python 脚本模块上单击可视化](./media/execute-python-scripts/figure-v2-9a.png)

![可视化使用 Python 代码的示例实验的绘图](./media/execute-python-scripts/figure-v2-9b.png)

就可以通过将它们保存到不同的图像返回多个图形。 Studio 运行时选择所有图像，并将它们连接的可视化效果。

## <a name="advanced-examples"></a>高级示例

在 Studio 中安装的 Anaconda 环境包含常用包，例如 NumPy、 SciPy 和 Scikits-learn。 这些包可以有效地用于机器学习管道中的数据处理。

例如，以下示例和脚本说明了使用系综中 Scikits-learn 来计算数据集的特征重要性分数。 分数可用于执行受监管的特征选择，然后在馈送到另一个模型。

下面演示如何使用 Python 函数计算重要性评分，并根据评分为特征排序：

![函数对功能排名的分数](./media/execute-python-scripts/figure8.png)

以下实验则在 Azure 机器学习工作室的“Pima Indian Diabetes”数据集中计算并返回功能的重要性得分：

![试验中使用 Python Pima Indian Diabetes 数据集中的排名功能](./media/execute-python-scripts/figure9a.png)

![执行 Python 脚本模块的输出的可视化效果](./media/execute-python-scripts/figure9b.png)

## <a name="limitations"></a>限制

[执行 Python 脚本][ execute-python-script]模块当前具有以下限制：

### <a name="sandboxed-execution"></a>沙盒执行

Python 运行时是当前在沙盒，不以持久方式允许访问网络或本地文件系统。 所有本地保存的文件都将隔离，并在模块完成后删除。 Python 代码无法访问运行它的计算机上的大部分目录，当前目录及其子目录除外。

### <a name="lack-of-sophisticated-development-and-debugging-support"></a>缺少的复杂的开发和调试支持

Python 模块当前不支持诸如 intellisense 和调试等 IDE 功能。 此外，如果模块在运行时失败，则整个 Python 堆栈跟踪可用。 但必须在模块的输出日志中查看跟踪。 我们目前建议在诸如 IPython 的环境中开发和调试 Python 脚本，然后将代码导入模块。

### <a name="single-data-frame-output"></a>单个数据帧输出

Python 入口点仅允许将单个数据帧返回为输出。 不是目前无法直接的训练模型等任意 Python 对象返回到 Studio 运行时。 就像具有相同限制的[执行 R 脚本][execute-r-script]一样，在许多情况下可将对象转换为字节数组，并在数据帧中返回字节数组。

### <a name="inability-to-customize-python-installation"></a>无法自定义 Python 安装

当前添加自定义 Python 模块的唯一方法是如前所述的使用 zip 文件机制。 虽然这是对小模块可行，非常麻烦的大型模块 （尤其是本机 Dll 的模块） 或大量的模块。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅 [Python 开发人员中心](https://azure.microsoft.com/develop/python/)。

<!-- Module References -->
[execute-python-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-python-script
[execute-r-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-r-script