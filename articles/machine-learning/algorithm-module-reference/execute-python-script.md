---
title: 执行 Python 脚本：模块引用
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习中的 "执行 Python 脚本" 模块来运行 Python 代码。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 1d82261d4b5c1a66498c33610670d7a38acbb197
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152323"
---
# <a name="execute-python-script-module"></a>执行 Python 脚本模块

本文介绍 Azure 机器学习设计器（预览版）中的模块。

使用此模块可以运行 Python 代码。 有关 Python 的体系结构和设计原则的详细信息，请参阅[以下文章](https://docs.microsoft.com/azure/machine-learning/machine-learning-execute-python-scripts)。

使用 Python，你可以执行现有模块当前不支持的任务，例如：

+ 使用 `matplotlib` 可视化数据
+ 使用 Python 库枚举工作区中的数据集和模型
+ 从 "[导入数据](./import-data.md)" 模块不支持的源读取、加载和操作数据
+ 运行你自己的深度学习代码 


Azure 机器学习使用 Python 的 Anaconda 分发版，其中包括用于数据处理的许多常用实用程序。 我们将自动更新 Anaconda 版本。 当前版本为：
 -  Python 3.6 的 Anaconda 4.5 + 分发 

预安装的包如下：
-  asn1crypto==0.24.0
- attrs==19.1.0
- azure-common==1.1.18
- azure-storage-blob==1.5.0
- azure-storage-common==1.4.0
- certifi==2019.3.9
- cffi==1.12.2
- chardet==3.0.4
- cryptography==2.6.1
- distro==1.4.0
- idna==2.8
- jsonschema==3.0.1
- lightgbm = = 2.2。3
- more-itertools==6.0.0
- numpy==1.16.2
- pandas = = 0.24。2
- Pillow==6.0.0
- pip==19.0.3
- pyarrow==0.12.1
- pycparser==2.19
- pycryptodomex==3.7.3
- pyrsistent = = 0.14.11
- python-dateutil==2.8.0
- pytz==2018.9
- requests==2.21.0
- scikit-learn = = 0.20。3
- scipy==1.2.1
- setuptools = = 40.8。0
- six==1.12.0
- torch==1.0.1.post2
- torchvision==0.2.2.post3
- urllib3==1.24.1
- wheel==0.33.1 

 若要安装不在预安装列表中的其他程序包（例如*scikit-learn*），请将以下代码添加到脚本： 

 ```python
import os
os.system(f"pip install scikit-misc")
```

## <a name="how-to-use"></a>如何使用

**执行 Python 脚本**模块包含可用作起点的示例 python 代码。 若要配置 "**执行 Python 脚本**" 模块，请在 " **Python 脚本**" 文本框中提供要执行的一组输入和 Python 代码。

1. 将**执行 Python 脚本**模块添加到管道。

2. 在**Dataset1**上添加并连接到要用于输入的设计器中的任何数据集。 在 Python 脚本中将此数据集引用为**DataFrame1**。

    如果要使用 Python 生成数据，或使用 Python 代码直接将数据导入到模块中，则可以使用数据集。

    此模块支持在**Dataset2**上添加另一个数据集。 将 Python 脚本中的第二个数据集作为 DataFrame2 引用。

    在 Azure 机器学习中存储的数据集将自动转换为**pandas** 。

    ![执行 Python 输入映射](media/module/python-module.png)

4. 若要包括新的 Python 包或代码，请在**脚本捆绑包**上添加包含这些自定义资源的压缩文件。 **脚本捆绑包**的输入必须是已上载到工作区的压缩文件。 

    在管道执行过程中，可以使用上载的压缩存档中包含的任何文件。 如果存档中包含目录结构，则会保留结构，但必须在路径前面添加一个名为**src**的目录。

5. 在 " **Python 脚本**" 文本框中，键入或粘贴有效的 Python 脚本。

    " **Python 脚本**" 文本框预填充了注释中的某些说明，并预先填充了用于数据访问和输出的示例代码。 必须编辑或替换此代码。 请务必遵循有关缩进和大小写的 Python 约定。

    + 脚本必须包含一个名为 `azureml_main` 的函数作为此模块的入口点。
    + 入口点函数最多可以包含两个输入参数： `Param<dataframe1>` 和 `Param<dataframe2>`
    + 连接到第三个输入端口的压缩文件将被解压缩并存储在目录 `.\Script Bundle`中，该目录还会添加到 Python `sys.path`。 

    因此，如果 zip 文件包含 `mymodule.py`，请使用 `import mymodule`导入它。

    + 可以将两个数据集返回到设计器，该设计器必须是 `pandas.DataFrame`类型的序列。 可以在 Python 代码中创建其他输出，并将其直接写入 Azure 存储。

6. 运行管道，或选择模块，然后单击 "**运行所选项**" 以仅运行 Python 脚本。

    所有数据和代码都将加载到虚拟机中，并使用指定的 Python 环境运行。

## <a name="results"></a>结果

嵌入的 Python 代码执行的任何计算的结果必须以 pandas 的形式提供。数据帧，它将自动转换为 Azure 机器学习的数据集格式，以便可以将结果用于管道中的其他模块。

该模块将返回两个数据集：  
  
+ **结果数据集 1**，由 Python 脚本中第一个返回的 pandas 数据帧定义

+ **结果数据集 2**，由第二个在 Python 脚本中返回的 pandas 数据帧定义


## <a name="next-steps"></a>后续步骤

查看可用于 Azure 机器学习[的模块集](module-reference.md)。 