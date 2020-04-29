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
ms.openlocfilehash: a2bd9845cd29c7d139e2042f39b4697847639207
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "79455785"
---
# <a name="execute-python-code-in-azure-machine-learning-designer"></a>在 Azure 机器学习设计器中执行 Python 代码

本文介绍如何使用[执行 Python 脚本](algorithm-module-reference/execute-python-script.md)模块将自定义逻辑添加到 Azure 机器学习设计器。 在以下操作方法中，我们使用 Pandas 库执行简单的特征工程。

可以使用内置代码编辑器快速添加简单的 Python 逻辑。 如果要添加更复杂的代码或上传其他 Python 库，应使用 zip 文件方法。

默认执行环境使用 Python 的 Anacondas 发行版。 如需预安装包的完整列表，请参阅[“执行 Python 脚本”模块引用](algorithm-module-reference/execute-python-script.md)页。

![执行 Python 输入映射](media/how-to-designer-python/execute-python-map.png)

## <a name="execute-python-written-in-the-designer"></a>执行在设计器中编写的 Python

### <a name="add-the-execute-python-script-module"></a>添加“执行 Python 脚本”模块

1. 在设计器面板中找到“执行 Python 脚本”  模块。 可以在“Python 语言”  部分找到它。

1. 将模块拖放到管道画布上。

### <a name="connect-input-datasets"></a>连接输入数据集

本文使用示例数据集“汽车价格数据(原始)”  。 

1. 将数据集拖放到管道画布。

1. 将数据集的输出端口连接到“执行 Python 脚本”  模块的左上角输入端口。 设计器将输入作为参数公开给入口点脚本。
    
    右侧输入端口保留用于压缩的 Python 库。

    ![连接数据集](media/how-to-designer-python/connect-dataset.png)
        

1. 请记下使用的输入端口。 设计器将左侧输入端口分配给变量 `dataset1`，将中间输入端口分配给 `dataset2`。 

输入模块为可选模块，因为你可以直接在“执行 Python 脚本”  模块中生成或导入数据。

### <a name="write-your-python-code"></a>编写 Python 代码

设计器提供了一个用于编辑和输入你自己的 Python 代码的初始入口点脚本。 

在此示例中，我们使用 Pandas 来合并汽车数据集中的两个列（“价格”  和“马力”  ），以创建新列“美元/马力”  。 此列表示为每个马力支付的费用，此特征可以用来确定汽车是否合算。 

1. 选择“执行 Python 脚本”模块。 

1. 在显示在画布右侧的窗格中，选择“Python 脚本”文本框。 

1. 将以下代码复制并粘贴到文本框中。

    ```python
    import pandas as pd
    
    def azureml_main(dataframe1 = None, dataframe2 = None):
        dataframe1['Dollar/HP'] = dataframe1.price / dataframe1.horsepower
        return dataframe1
    ```
    管道应如下图所示：
    
    ![执行 Python 管道](media/how-to-designer-python/execute-python-pipeline.png)

    入口点脚本必须包含函数 `azureml_main`。 有两个函数参数映射到“执行 Python 脚本”  模块的两个输入端口。

    返回值必须是 Pandas 数据帧。 最多可以返回两个数据帧作为模块输出。
    
1. 提交管道。

现在你有了一个数据集，其中包含新特征“美元/马力”  ，它对于训练汽车推荐程序非常有用。 这是特征提取和维数缩减的示例。 

## <a name="next-steps"></a>后续步骤

了解如何在 Azure 机器学习设计器中[导入自己的数据](how-to-designer-import-data.md)。