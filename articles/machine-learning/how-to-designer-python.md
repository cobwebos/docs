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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455785"
---
# <a name="execute-python-code-in-azure-machine-learning-designer"></a>在 Azure 机器学习设计器中执行 Python 代码

在本文中，您将了解如何使用[执行 Python 脚本](algorithm-module-reference/execute-python-script.md)模块向 Azure 机器学习设计器添加自定义逻辑。 在下面的操作操作中，您可以使用 Pandas 库执行简单的功能工程。

您可以使用内置代码编辑器快速添加简单的 Python 逻辑。 如果要添加更复杂的代码或上载其他 Python 库，则应使用 zip 文件方法。

默认执行环境使用 Python 的 Anacondas 分布。 有关预安装包的完整列表，请参阅执行 Python[脚本模块参考](algorithm-module-reference/execute-python-script.md)页。

![执行 Python 输入映射](media/how-to-designer-python/execute-python-map.png)

## <a name="execute-python-written-in-the-designer"></a>执行在设计器中编写的 Python

### <a name="add-the-execute-python-script-module"></a>添加执行 Python 脚本模块

1. 在设计器调色板中查找**执行 Python 脚本**模块。 可以在**Python 语言**部分找到它。

1. 将模块拖放到管道画布上。

### <a name="connect-input-datasets"></a>连接输入数据集

本文使用示例数据集，**汽车价格数据（原始）。** 

1. 将数据集拖放到管道画布上。

1. 将数据集的输出端口连接到**执行 Python 脚本**模块的左上角输入端口。 设计器将输入作为参数公开到入口点脚本。
    
    右输入端口保留用于压缩的 python 库。

    ![连接数据集](media/how-to-designer-python/connect-dataset.png)
        

1. 记下您使用的输入端口。 设计器将左侧输入端口分配给变量`dataset1`，将中间输入端口分配给 。 `dataset2` 

输入模块是可选的，因为可以直接在**执行 Python 脚本**模块中生成或导入数据。

### <a name="write-your-python-code"></a>编写 Python 代码

设计器提供初始入口点脚本，供您编辑和输入自己的 Python 代码。 

在此示例中，您可以使用 Pandas 组合汽车数据集"**价格**和**马力**"中的两列，以创建一个新列，**即每马力的美元**。 本专栏表示您为每马力支付多少费用，这可能是一个有用的功能，可以决定一辆车是否物有所值。 

1. 选择**执行 Python 脚本**模块。

1. 在画布右侧显示的窗格中，选择**Python 脚本**文本框。

1. 复制以下代码并将其粘贴到文本框中。

    ```python
    import pandas as pd
    
    def azureml_main(dataframe1 = None, dataframe2 = None):
        dataframe1['Dollar/HP'] = dataframe1.price / dataframe1.horsepower
        return dataframe1
    ```
    管道应显示以下图像：
    
    ![执行 Python 管道](media/how-to-designer-python/execute-python-pipeline.png)

    入口点脚本必须包含函数`azureml_main`。 有两个函数参数映射到**执行 Python 脚本**模块的两个输入端口。

    返回值必须为熊猫数据帧。 您最多可以返回两个数据帧作为模块输出。
    
1. 提交管道。

现在，您有一个数据集，具有新功能 **"美元/HP"，** 这对于培训汽车推荐者非常有用。 这是特征提取和尺寸减小的示例。 

## <a name="next-steps"></a>后续步骤

了解如何在 Azure 机器学习设计器中[导入自己的数据](how-to-designer-import-data.md)。