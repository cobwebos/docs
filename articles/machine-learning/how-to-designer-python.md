---
title: Python
titleSuffix: Azure Machine Learning
description: 了解如何在 Azure 机器学习设计器中使用 Python 来转换数据。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: peterclu
ms.author: peterlu
ms.date: 02/28/2020
ms.openlocfilehash: b0b0bb5eefde9e744b1f30109d60ded91d3b44e8
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2020
ms.locfileid: "78228679"
---
# <a name="execute-python-code-in-azure-machine-learning-designer"></a>在 Azure 机器学习设计器中执行 Python 代码

本文介绍如何使用 "[执行 Python 脚本](algorithm-module-reference/execute-python-script.md)" 模块将自定义逻辑添加到 Azure 机器学习设计器中。 在以下操作方法中，你将使用 Pandas 库来执行简单的功能工程。

您可以使用内置代码编辑器快速添加简单的 Python 逻辑。 如果要添加更复杂的代码或上传其他 Python 库，应使用 zip 文件方法。

默认执行环境使用 Python 的 Anacondas 分布。 有关预安装包的完整列表，请参阅[执行 Python 脚本模块参考](algorithm-module-reference/execute-python-script.md)页。

![执行 Python 输入映射](media/how-to-designer-python/execute-python-map.png)

## <a name="execute-python-written-in-the-designer"></a>执行在设计器中编写的 Python

### <a name="add-the-execute-python-script-module"></a>添加执行 Python 脚本模块

1. 在设计器调色板中查找 "**执行 Python 脚本**" 模块。 可在**Python 语言**部分找到。

1. 将模块拖放到管道画布上。

### <a name="connect-input-datasets"></a>连接输入数据集

本文使用示例数据集、**汽车价格数据（原始）** 。 

1. 将数据集拖放到管道画布上。

1. 将数据集的输出端口连接到 "**执行 Python 脚本**" 模块的左上角输入端口。 设计器将输入作为参数公开给入口点脚本。
    
    向右输入端口保留用于压缩的 python 库。

    ![连接数据集](media/how-to-designer-python/connect-dataset.png)
        

1. 请注意使用的输入端口。 设计器将左输入端口分配给变量 `dataset1`，并将中间输入端口分配给 `dataset2`。 

输入模块是可选的，因为你可以直接在**执行 Python 脚本**模块中生成或导入数据。

### <a name="write-your-python-code"></a>编写 Python 代码

设计器提供了一个用于编辑和输入自己的 Python 代码的初始入口点脚本。 

在此示例中，将使用 Pandas 合并汽车数据集中的两个列（**价格**和**动力**），以创建一个新列，**每个动力美元美元**。 此列表示你为每个动力支付了多少费用，这可能是一项有用的功能，可用于确定汽车是否为金钱。 

1. 选择 "**执行 Python 脚本**" 模块。

1. 在画布右侧显示的窗格中，选择 " **Python 脚本**" 文本框。

1. 将以下代码复制并粘贴到文本框中。

    ```python
    import pandas as pd
    
    def azureml_main(dataframe1 = None, dataframe2 = None):
        dataframe1['Dollar/HP'] = dataframe1.price / dataframe1.horsepower
        return dataframe1
    ```
    管道应如下图所示：
    
    ![执行 Python 管道](media/how-to-designer-python/execute-python-pipeline.png)

    入口点脚本必须包含函数 `azureml_main`。 有两个函数参数映射到 "**执行 Python 脚本**" 模块的两个输入端口。

    返回值必须是 Pandas 数据帧。 最多可以返回两个 dataframes 作为模块输出。
    
1. 运行管道。

现在，你有一个数据集，其中包含新的 "**美元/HP**" 功能，这对于培训汽车推荐器非常有用。 这是功能提取和维数缩减的示例。 

## <a name="next-steps"></a>后续步骤

了解如何在 Azure 机器学习设计器中[导入自己的数据](how-to-designer-import-data.md)。