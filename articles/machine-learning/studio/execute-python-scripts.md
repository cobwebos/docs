---
title: "执行 Python 机器学习脚本 | Microsoft Docs"
description: "概述了 Azure 机器学习中支持 Python 脚本下的设计原则，以及基本的使用方案、功能和限制。"
keywords: "python 机器学习, pandas, python pandas, python 脚本, 执行 python 脚本"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: ee9eb764-0d3e-4104-a797-19fc29345d39
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: bradsev;garye
ms.openlocfilehash: c25f31ca72417672298657c4585184ad72db6c99
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2017
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio"></a>在 Azure 机器学习工作室中执行 Python 机器学习脚本

本主题介绍了 Azure 机器学习中当前对 Python 脚本支持下的设计原则。 此外，还概述了主要功能，包括：

- 执行基本使用方案
- 为 Web 服务中的实验评分
- 支持导入现有代码
- 导出可视化效果
- 执行受监管的特征选择
- 了解一些限制

[Python](https://www.python.org/) 是许多数据科学家工具箱中不可缺少的工具。 它具有：

* 简洁明了的语法、 
* 跨平台支持、 
* 强大库的大型集合，以及 
* 成熟的开发工具。 

Python 广泛应用于通常在机器学习建模中使用的工作流的所有阶段：

- 数据引入和处理 
- 特征构造
- 模型训练 
- 模型验证
- 模型部署

Azure 机器学习工作室支持在 Microsoft Azure 上将 Python 脚本嵌入机器学习实验的各个部分，也支持将它们无缝发布为 Web 服务。

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


## <a name="design-principles-of-python-scripts-in-machine-learning"></a>机器学习中 Python 脚本的设计原则

Azure 机器学习工作室中 Python 主接口通过图 1 中所示的[执行 Python 脚本][execute-python-script]模块连接。

![image1](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![image2](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

图 1. **执行 Python 脚本**模块。

Azure 机器学习工作室中的[执行 Python 脚本][execute-python-script]模块接受最多三个输入，生成最多两个输出（如下一部分所述），就像 R 模拟一样，即[执行 R 脚本][execute-r-script]模块。 要执行的 Python 代码输入参数框，作为名为 `azureml_main` 的特殊命名的入口点函数。 以下是用于实现此模块的关键设计原则：

1. *必须是 Python 用户常用。* 大多数 Python 用户将他们的代码视为模块中的函数。 因此将大量可执行语句放入顶层模块的情况相对较少。 因此，脚本框对一系列语句采用特殊命名的 Python 函数。 在函数中显示的对象都是标准的 Python 库类型，例如 [Pandas](http://pandas.pydata.org/) 数据帧和 [NumPy](http://www.numpy.org/) 数组。
2. *在本地和云执行之间必须具有高保真度。* 用于执行 Python 代码的后端基于 [Anaconda](https://store.continuum.io/cshop/anaconda/)，这是跨平台科学分配 Python，已在广泛使用。 它附带接近 200 个最常用的 Python 程序包。 因此，数据科学家可在本地 Azure 机器学习兼容的 Anaconda 环境中调试和限制代码。 然后使用 [IPython](http://ipython.org/) Notebook 或[用于 Visual Studio 的 Python 工具](http://aka.ms/ptvs)等现有开发环境将其作为 Azure 机器学习实验的一部分运行。 `azureml_main` 入口点是普通 Python 函数，因此****可在没有 Azure 机器学习特定代码或不安装 SDK 的情况下编写。
3. *必须与其他 Azure 机器学习模块无缝组合。* 作为输入和输出，[执行 Python 脚本][execute-python-script]模块接受标准的 Azure 机器学习数据集。 基础框架透明且有效地连接 Azure 机器学习和 Python 运行时。 因此，Python 可与现有的 Azure 机器学习工作流一起使用，包括调入 R 和 SQLite 的工作流。 因此，数据科学家可以这样设想工作流：
   * 将 Python 和 Pandas 用于数据预处理和清理
   * 将数据传送到 SQL 转换，集合多个数据集形成特征
   * 使用 Azure 机器学习中的算法训练模型 
   * 使用 R 评估并后期处理结果。


## <a name="basic-usage-scenarios-in-ml-for-python-scripts"></a>Python 脚本在机器学习中的基本使用方案

在本部分中，我们调查[执行 Python 脚本][execute-python-script]模块的一些基本用例。 Python 模块的输入公开为 Pandas 数据帧。 该函数必须返回打包在 Python [序列](https://docs.python.org/2/c-api/sequence.html)内部的单个 Pandas 数据帧，例如元组、列表或 NumPy 数组。 然后，此序列的第一个元素返回在模块的第一个输出端口。 此方案如图 2 所示。

![image3](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

图 2。 将输入端口映射到参数，并向输出端口返回值。

输入端口如何映射到 `azureml_main` 函数参数的详细语义显示在表 1 中：

![image1T](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

表 1. 将输入端口映射到函数参数。

可以定位输入端口和函数参数之间的映射：

- 第一个连接的输入端口映射到函数的第一个参数。 
- 第二个输入（如果已连接）映射到函数的第二个参数。

有关 Python Pandas 的详细信息以及如何使用它来有效处理数据，请参阅 W. McKinney 撰写的 *Python 数据分析* (O'Reilly, 2012)。 


## <a name="translation-of-input-and-output-types"></a>转换输入和输出类型 
Azure 机器学习中的输入数据集转换为 Pandas 中的数据帧。 输出数据帧转换回 Azure 机器学习数据集。 执行以下转换：

1. 字符串和数字列按原样转换，数据集中的缺失值转换为 Pandas 中的“NA”值。 反过来也会发生相同的转换（Pandas 中的 NA 值转换为 Azure 机器学习中的缺失值）。
2. Pandas 中的索引矢量在 Azure 机器学习中不受支持。 Python 函数中的所有输入数据帧始终具有 64 位的数字索引，范围从 0 到行数减去 1 的值。 
3. Azure 机器学习数据集不得拥有重复列名称和不是字符串的列名称。 如果输出数据帧包含非数字列，则框架调用列名称的 `str`。 同样地，任何列名称都会自动改变，确保名称的唯一性。 后缀 (2) 将添加到第一个重复名，(3) 添加到第二个重复名，依此类推。


## <a name="operationalizing-python-scripts"></a>实现 Python 脚本

任何在评分实验中使用的[执行 Python 脚本][execute-python-script]模块在发布为 Web 服务时都将受到调用。 例如，图 3 显示包含代码的评分实验可评估单个 Python 表达式。 

![Image4](./media/execute-python-scripts/figure3a.png)

![image5](./media/execute-python-scripts/python-script-with-python-pandas.png)

图 3。 评估 Python 表达式的 Web 服务。

基于此实验创建的 Web 服务：

- 将 Python 表达式（字符串）用作输入
- 将此字符串发送到 Python 解释器 
- 返回包含表达式和评估结果的表格。


## <a name="importing-existing-python-script-modules"></a>导入现有的 Python 脚本模块

许多数据科学家常用的用例是将现有 Python 脚本包含到 Azure 机器学习实验。 [执行 Python 脚本][execute-python-script]模块不需要连接所有代码并将它们粘贴到单个脚本框中，而可接受一个 zip 文件，其中包含第三个输入端口处的 Python 模块。 在运行时执行框架会解压缩该文件，内容将添加到 Python 解释器的库路径。 然后，`azureml_main` 入口点函数可直接导入这些模块。

例如，请考虑包含简单“Hello, World”函数的 Hello.py 文件。

![image6](./media/execute-python-scripts/figure4.png)

图 4。 Hello.py 文件中用户定义的函数。

接下来，我们创建包含 Hello.py 的 Hello.zip 文件：

![image7](./media/execute-python-scripts/figure5.png)

图 5。 包含用户定义的 Python 代码的 zip 文件。

将此 zip 文件作为数据集上传到 Azure 机器学习工作室。 然后，在 Hello.zip 文件中使用 Python 代码的实验附加到“执行 Python 脚本”模块的第三个输入端口，创建并运行该实验，如下图所示。

![image8](./media/execute-python-scripts/figure6a.png)

![image9](./media/execute-python-scripts/figure6b.png)

图 6。 用户定义的 Python 代码上传为 zip 文件的实验示例。

模块输出显示，zip 文件未打包，并且函数 `print_hello` 已运行。
 
![image10](./media/execute-python-scripts/figure7.png)

图 7。 在[执行 Python 脚本][execute-python-script]模块中使用的用户定义的函数。


## <a name="working-with-visualizations"></a>使用可视化效果

使用可在浏览器上可视化的 MatplotLib 创建的图形可由[执行 Python 脚本][execute-python-script]返回。 但图形不会像使用 R 时那样自动重定向为图像。因此，如果任何图形要返回到 Azure 机器学习，用户必须显式将这些图形保存为 PNG 文件。 

若要使用 MatplotLib 生成图像，必须完成以下步骤：

* 将后端由默认的基于 Qt 的呈现器切换为“AGG” 
* 创建新图形对象 
* 获取轴，并将所有图形生成到其中 
* 将图形保存为 PNG 文件 

此过程在 Pandas 中使用 scatter_matrix 函数创建散点图矩阵，在下图 8 中作了介绍。

![image1v](./media/execute-python-scripts/figure-v1-8.png)

图 8。 将 MatplotLib 图形保存为图像的代码。

图 9 显示通过第二个输出端口使用之前显示的脚本返回图形的实验。

![image2v](./media/execute-python-scripts/figure-v2-9a.png) 

![image2v](./media/execute-python-scripts/figure-v2-9b.png) 

图 9. 可视化使用 Python 代码生成的图形。

将多个图形保存为不同图像可返回它们，Azure 机器学习运行时会选择所有图像，并将它们连接起来产生可视化效果。


## <a name="advanced-examples"></a>高级示例

安装在 Azure 机器学习中的 Anaconda 环境包含常用包，例如 NumPy、SciPy 和 Scikits-Learn。 可以针对机器学习管道中的各种数据处理任务有效使用这些包。 例如，以下示例和脚本介绍如何在 Scikits-Learn 中使用系综学习器计算数据集的特征重要性评分。 在馈送到其他机器学习模型前，评分可用于执行受监控特征选择。

下面演示如何使用 Python 函数计算重要性评分，并根据评分为特征排序：

![image11](./media/execute-python-scripts/figure8.png)

图 10. 根据得分对功能排名的函数。
  以下实验在 Azure 机器学习的“Pima Indian Diabetes”数据集中计算并返回功能的重要性得分：

![image12](./media/execute-python-scripts/figure9a.png)
![image13](./media/execute-python-scripts/figure9b.png)    

图 11. 对 Pima Indian Diabetes 数据集中的功能排名的实验。

## <a name="limitations"></a>限制
[执行 Python 脚本][execute-python-script]当前具有以下限制：

1. *沙盒执行。* Python 运行时当前在沙盒中执行，因此不能持续访问网络或本地文件系统。 所有本地保存的文件都将隔离，并在模块完成后删除。 Python 代码无法访问运行它的计算机上的大部分目录，当前目录及其子目录除外。
2. *缺少复杂的开发和调试支持。* Python 模块当前不支持诸如 intellisense 和调试等 IDE 功能。 此外，如果模块在运行时失败，则整个 Python 堆栈跟踪可用。 但必须在模块的输出日志中查看跟踪。 我们目前建议在诸如 IPython 的环境中开发和调试 Python 脚本，然后将代码导入模块。
3. *单个数据帧输出。* Python 入口点仅允许将单个数据帧返回为输出。 当前无法将诸如训练的模型等任意 Python 对象直接返回到 Azure 机器学习运行时。 就像具有相同限制的[执行 R 脚本][execute-r-script]一样，在许多情况下可将对象转换为字节数组，并在数据帧中返回字节数组。
4. *无法自定义 Python 安装*。 当前添加自定义 Python 模块的唯一方法是如前所述的使用 zip 文件机制。 虽然这对小模块可行，但对大模块（尤其是具有本地 DLL 的模块）或许多模块而言却很麻烦。 

## <a name="conclusions"></a>结论
[执行 Python 脚本][execute-python-script]模块支持数据科学家在 Azure 机器学习中将现有 Python 代码合并到云托管的机器学习工作流，并将它们无缝实现为 Web 服务的一部分。 Python 脚本模块在 Azure 机器学习中可与其他模块无障碍互操作。 该模块可用于一系列任务，范围包括从数据浏览到预处理，再到特征提取，并可评估和后期处理结果。 用于执行的后端运行时基于经过全面测试并广泛使用的 Python 分配 Anaconda。 此后端简化了将现有代码资产载入到云的过程。

我们希望向[执行 Python 脚本][execute-python-script]模块提供其他功能，例如在 Python 中训练和操作模型、在 Azure 机器学习工作室中更好地支持开发和调试代码。

## <a name="next-steps"></a>后续步骤
有关详细信息，请参阅 [Python 开发人员中心](https://azure.microsoft.com/develop/python/)。

<!-- Module References -->
[execute-python-script]: https://msdn.microsoft.com/library/azure/cdb56f95-7f4c-404d-bde7-5bb972e6f232/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
