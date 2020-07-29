---
title: 在设计器中执行 Python 脚本（预览）
titleSuffix: Azure Machine Learning
description: 了解如何在 Azure 机器学习设计器（预览版）中使用 Python 来转换数据。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: peterclu
ms.author: peterlu
ms.date: 02/28/2020
ms.topic: conceptual
ms.custom: how-to, designer, tracking-python
ms.openlocfilehash: c798fa3bdb6754b2d8b955efe49e208ea954f80a
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87326964"
---
# <a name="run-python-code-in-azure-machine-learning-designer"></a>在 Azure 机器学习设计器中运行 Python 代码

本文介绍了如何使用[“执行 Python 脚本”](algorithm-module-reference/execute-python-script.md)模块将自定义逻辑添加到 Azure 机器学习设计器中。 在下面的操作指南中，你将使用 Pandas 库来执行简单的特征工程。

可以使用内置的代码编辑器来快速添加简单的 Python 逻辑。 若要添加更复杂的代码或上传更多的 Python 库，应使用 zip 文件方法。

默认执行环境使用 Anacondas 发行版 Python。 有关预安装包的完整列表，请参阅[“执行 Python 脚本”模块参考](algorithm-module-reference/execute-python-script.md)页。

![执行 Python 输入映射](media/how-to-designer-python/execute-python-map.png)

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="execute-python-written-in-the-designer"></a>执行在设计器中编写的 Python

### <a name="add-the-execute-python-script-module"></a>添加“执行 Python 脚本”模块

1. 在设计器面板中，查找“执行 Python 脚本”模块。 可以在“Python 语言”部分中找到它。

1. 将此模块拖放到管道画布上。

### <a name="connect-input-datasets"></a>连接输入数据集

本文使用示例数据集“汽车价格数据(原始)”。 

1. 将数据集拖放到管道画布上。

1. 将数据集的输出端口连接到“执行 Python 脚本”模块的左上角输入端口。 设计器将输入作为参数公开给入口点脚本。
    
    右侧的输入端口是为压缩的 python 库预留的。

    ![连接数据集](media/how-to-designer-python/connect-dataset.png)
        

1. 留意使用的是哪个输入端口。 设计器将左侧的输入端口分配给变量 `dataset1`，并将中间输入端口分配给 `dataset2`。 

输入模块是可选的，因为可以直接在“执行 Python 脚本”模块中生成或导入数据。

### <a name="write-your-python-code"></a>编写 Python 代码

设计器提供了一个初始入口点脚本，供你编辑和输入自己的 Python 代码。 

在此示例中，你使用 Pandas 来合并汽车数据集中的两列（“价格”和“马力”），以新建“美元/马力”列。 此列表示每马力的价格，这可能是决定汽车是否物有所值的实用特征。 

1. 选择“执行 Python 脚本”模块。

1. 在随即显示的窗格中，选择画布右侧的“Python 脚本”文本框。

1. 将以下代码复制并粘贴到文本框中。

    ```python
    import pandas as pd
    
    def azureml_main(dataframe1 = None, dataframe2 = None):
        dataframe1['Dollar/HP'] = dataframe1.price / dataframe1.horsepower
        return dataframe1
    ```
    管道应如下图所示：
    
    ![执行 Python 管道](media/how-to-designer-python/execute-python-pipeline.png)

    入口点脚本必须包含函数 `azureml_main`。 有两个函数参数映射到“执行 Python 脚本”模块的两个输入端口。

    返回值必须是 Pandas 数据帧。 最多可以返回两个数据帧作为模块输出。
    
1. 提交管道。

现在，你有了一个具有新特征“美元/马力”的数据集，此特征在训练汽车推荐器方面非常有用。 这是一个特征提取和降维的示例。 

## <a name="next-steps"></a>后续步骤

了解如何在 Azure 机器学习设计器中[导入你自己的数据](how-to-designer-import-data.md)。
