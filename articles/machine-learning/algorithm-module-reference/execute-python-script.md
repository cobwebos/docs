---
title: 执行 Python 脚本：模块参考
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习中的“执行 Python 脚本”模块来运行 Python 代码。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/10/2020
ms.openlocfilehash: 79dc1b188e91028a98f43dc24972228f2d2101be
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684725"
---
# <a name="execute-python-script-module"></a>“执行 Python 脚本”模块

本文介绍了 Azure 机器学习设计器（预览版）中的一个模块。

使用此模块可以运行 Python 代码。 有关 Python 的体系结构和设计原则的详细信息，请参阅[以下文章](https://docs.microsoft.com/azure/machine-learning/machine-learning-execute-python-scripts)。

使用 Python 可以执行现有模块当前不支持的任务，例如：

+ 使用 `matplotlib` 将数据可视化
+ 使用 Python 库枚举工作区中的数据集和模型
+ [导入数据](./import-data.md)模块不支持的从源中读取、加载和操作数据
+ 运行自己的深度学习代码 


Azure 机器学习使用 Python 的 Anaconda 分发版，其中包括用于数据处理的许多常用实用工具。 我们将自动更新 Anaconda 版本。 当前版本为：
 -  Python 3.6 的 Anaconda 4.5 + 分发版 

预安装的包如下：
-    adal=1.2.2
-    应用程序见解=0.11.9
-    attrs=19.3.0
-    azure 公共 =1.1.25
-    azure 核心 =1.3.0
-    azure-图形杆菌=0.61.1
-    azure 标识=1.3.0
-    azure-mgmt 授权=0.60.0
-    azure-mgmt-容器注册=2.8.0
-    azure-mgmt-键保管=2.2.0
-    azure-mgmt 资源=8.0.1
-    azure-mgmt 存储=8.0.0
-    azure-storage-blob==1.5.0
-    azure 存储 -公共=1.4.2
-    azureml-core=1.1.5.5
-    azureml 数据准备-本机 =14.1.0
-    azureml 数据准备=1.3.5
-    azureml 默认值=1.1.5.1
-    azureml-设计器-经典模块=0.0.118
-    azureml-designer-core=0.0.31
-    azureml-设计器内部=0.0.18
-    azureml 模型管理-sdk=1.0.1b6.post1
-    azureml-管道-核心=1.1.5
-    azureml 遥测=1.1.5.3
-    后端口.tempfile=1.0
-    后端口.weakref=1.0.post1
-    博托3=1.12.29
-    博托科=1.15.29
-    缓存工具=4.0.0
-    证书=2019.11.28
-    cffi=1.12.3
-    chardet==3.0.4
-    单击=7.1.1
-    云片=1.3.0
-    配置器=3.7.4
-    上下文lib2=0.6.0.后1
-    密码学=2.8
-    循环器=0.10.0
-    迪尔=0.3.1.1
-    distro==1.4.0
-    docker=4.2.0
-    多库蒂尔=0.15.2
-    点网芯2=2.1.13
-    烧瓶=1.0.3
-    保险丝=3.0.1
-    gensim=3.8.1
-    谷歌-api-core=1.16.0
-    google-auth=1.12.0
-    谷歌云核心=1.3.0
-    google 云存储 =1.26.0
-    谷歌可恢复媒体=0.5.0
-    googleapis-公共原型=1.51.0
-    古尼科恩=19.9.0
-    伊德纳=2.9
-    不平衡学习=0.4.3
-    等日期=0.6.0
-    其危险=1.1.0
-    吉普尼=0.4.3
-    jinja2=2.11.1
-    jmespath=0.9.5
-    joblib=0.14.0
-    json-日志记录-py=0.2
-    jsonpickle=1.3
-    jsonschema==3.0.1
-    奇异器=1.1.0
-    利亚克-阿尔夫=2.4.0
-    lightgbm==2.2.3
-    标记安全 =1.1.1
-    垫子利布=3.1.3
-    more-itertools==6.0.0
-    msal 扩展=0.1.3
-    msal=1.1.0
-    msrest=0.6.11
-    msrestazure=0.6.3
-    ndg-httpsclient=0.5.1
-    尼姆布姆=1.6.1
-    数字=1.18.2
-    奥思利布=3.1.0
-    熊猫=0.25.3
-    路径规格=0.7.0
-    点 =20.0.2
-    波特洛克=1.6.0
-    普罗托布夫=3.11.3
-    pyarrow=0.16.0
-    pyasn1-模块=0.2.8
-    pyasn1=0.4.8
-    除虫器=2.20
-    pycryptodomex==3.7.3
-    pyjwt=1.7.1
-    pyopenssl=19.1.0
-    除位 =2.4.6
-    热西滕=0.16.0
-    python-dateutil=2.8.1
-    pytz_2019.3
-    请求-奥阿特利布=1.3.0
-    请求=2.23.0
-    rsa=4.0
-    鲁阿梅尔.亚姆尔=0.15.89
-    s3传输=0.3.3
-    学学习=0.22.2
-    奇皮=1.4.1
-    秘密存储=3.1.2
-    设置工具=46.1.1.后20200323
-    六=1.14.0
-    智能打开=1.10.0
-    urllib3=1.25.8
-    websocket 客户端=0.57.0
-    韦克泽古=0.16.1
-    车轮=0.34.2

 若要安装不在预安装列表中的其他包（例如 *scikit-misc*），请将以下代码添加到脚本中： 

 ```python
import os
os.system(f"pip install scikit-misc")
```

## <a name="upload-files"></a>上传文件
**执行 Python 脚本**支持使用 Azure 机器学习 Python [SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#upload-file-name--path-or-stream-)上载文件。

下面的示例演示如何在**执行 Python 脚本**模块中上载图像文件：

```Python

# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# imports up here can be used to
import pandas as pd

# The entry point function must have two input arguments:
#   Param<dataframe1>: a pandas.DataFrame
#   Param<dataframe2>: a pandas.DataFrame
def azureml_main(dataframe1 = None, dataframe2 = None):

    # Execution logic goes here
    print(f'Input pandas.DataFrame #1: {dataframe1}')

    from matplotlib import pyplot as plt
    plt.plot([1, 2, 3, 4])
    plt.ylabel('some numbers')
    img_file = "line.png"
    plt.savefig(img_file)

    from azureml.core import Run
    run = Run.get_context(allow_offline=True)
    run.upload_file(f"graphics/{img_file}", img_file)

    # Return value must be of a sequence of pandas.DataFrame
    # E.g.
    #   -  Single return value: return dataframe1,
    #   -  Two return values: return dataframe1, dataframe2
    return dataframe1,
}
```

管道运行完成后，您可以在模块的右侧面板中预览图像

> [!div class="mx-imgBorder"]
> ![上传图像](media/module/upload-image-in-python-script.png)

## <a name="how-to-configure-execute-python-script"></a>如何配置执行 Python 脚本

**执行 Python 脚本**模块包含可用作起点的示例 Python 代码。 若要配置**执行 Python 脚本**模块，请在“Python 脚本”**** 文本框中提供要执行的一组输入和 Python 代码。

1. 将**执行 Python 脚本**模块添加到管道。

2. 从设计器中，在 **Dataset1** 上添加并连接要用于输入的任何数据集。 在 Python 脚本中将此数据集引用为 **DataFrame1**。

    如果要使用 Python 生成数据，或者使用 Python 代码将数据直接导入到模块中，则可以选择使用数据集。

    此模块支持在 **Dataset2** 上添加另一个数据集。 在 Python 脚本中将第二个数据集引用为 DataFrame2。

    使用此模块加载时，存储在 Azure 机器学习中的数据集将自动转换为 **pandas** data.frames。

    ![执行 Python 输入映射](media/module/python-module.png)

4. 要包括新的 Python 包或代码，请在**脚本捆绑包**上添加包含这些自定义资源的压缩文件。 **脚本捆绑包**的输入必须是作为文件类型数据集上载到工作区的压缩文件。 您可以在 **"数据集"** 资产页中上载数据集，还可以从设计器创作页中左侧模块树中的 **"我的数据集"** 列表中拖放数据集模块。 

    在管道执行期间，可以使用已上传的压缩存档中包含的任何文件。 如果存档中包含目录结构，则会保留该结构，但你必须在路径前面追加一个名为 **src** 的目录。

5. 在“Python 脚本”文本框中，键入或粘贴有效的 Python 脚本****。

    > [!NOTE]
    > 编写脚本时请非常小心，并确保没有语法错误，例如使用未声明的对象或未导入的模块。 还要特别注意预安装的模块列表。 要导入未列出的模块，请在脚本中安装相应的包，例如
    >  ``` Python
    > import os
    > os.system(f"pip install scikit-misc")
    > ```
    
    “Python 脚本”**** 文本框中预先填充了注释中的某些说明，以及用于数据访问和输出的示例代码。 你必须编辑或替换此代码。 请务必遵循有关缩进和大小写的 Python 约定。

    + 脚本必须包含名为 `azureml_main` 的函数作为此模块的入口点。
    + 入口点函数必须具有两个输入参数：`Param<dataframe1>`和`Param<dataframe2>`，即使脚本中不使用这些参数也是如此。
    + 连接到第三个输入端口的压缩文件将被解压缩并存储在目录 `.\Script Bundle` 中，该目录还会添加到 Python `sys.path` 中。 

    因此，如果 zip 文件包含 `mymodule.py`，请使用 `import mymodule` 导入它。

    + 可以向设计器返回两个数据集，数据集必须是 `pandas.DataFrame` 类型的序列。 可以在 Python 代码中创建其他输出，并将其直接写入到 Azure 存储。

6. 提交管道，或选择模块，然后单击 **"运行"** 仅运行 Python 脚本。

    所有数据和代码都将加载到虚拟机中，并使用指定的 Python 环境运行。

## <a name="results"></a>结果

嵌入的 Python 代码执行的任何计算的结果必须以 pandas.DataFrame 形式提供，它将自动转换为 Azure 机器学习数据集格式，以便可以将结果用于管道中的其他模块。

此模块返回两个数据集：  
  
+ **结果数据集 1**，由 Python 脚本中返回的第一个 pandas 数据帧定义

+ **结果数据集 2**，由 Python 脚本中返回的第二个 pandas 数据帧定义


## <a name="next-steps"></a>后续步骤

参阅 Azure 机器学习[可用的模块集](module-reference.md)。 