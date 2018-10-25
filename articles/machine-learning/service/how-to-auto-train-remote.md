---
title: 为远程计算目标设置自动化机器学习 - Azure 机器学习服务
description: 本文介绍了如何使用 Azure 机器学习服务在 Data Science Virtual machine (DSVM) 远程目标上使用自动机器学习构建模型
services: machine-learning
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 1297881373943143578695490531d147d7f5523d
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2018
ms.locfileid: "49115316"
---
# <a name="train-models-with-automated-machine-learning-in-the-cloud"></a>在云中使用自动化机器学习对模型进行训练

在 Azure 机器学习中，你可以在所管理的不同类型的计算资源上定型模型。 计算目标可以是本地计算机，也可以是云中的计算机。

可以通过添加基于 Ubuntu 的 Data Science Virtual Machine (DSVM) 或 Azure Batch AI 等附加计算目标，轻松地纵向扩展或横向扩展机器学习实验。 DSVM 是在 Microsoft Azure 云上专为研究数据科学而生成的自定义 VM 映像。 它预先安装和预配置了许多热门的数据科学和其他工具。  

在本文中，你将了解如何在 DSVM 上使用自动化机器学习来构建模型。 可以在 [GitHub 中的这些示例 notebook](https://aka.ms/aml-notebooks) 中找到使用 Azure Batch AI 的示例。  

## <a name="how-does-remote-differ-from-local"></a>远程与本地有何区别？

教程“[使用自动化机器学习训练分类模型](tutorial-auto-train-models.md)”讲授了如何使用本地计算机通过自动化机器学习来训练模型。  本地培训的工作流同样适用于远程目标。 但是，使用远程计算，能够以异步方式执行自动化机器学习试验迭代。 这允许你取消特定迭代，观察执行状态，或继续在 Jupyter Notebook 的其他单元格上处理。 若要进行远程训练，首先要创建一个远程计算目标，例如 Azure DSVM。  然后，配置远程资源，并在那里提交代码。

本文展示了在远程 DSVM 上运行自动化机器学习试验所需的额外步骤。  本教程中的工作区对象 `ws` 将会在此处的整个代码中使用。

```python
ws = Workspace.from_config()
```

## <a name="create-resource"></a>创建资源

如果尚且没有 DSVM，请在工作区 (`ws`) 中创建 DSVM。 如果先前已创建 DSVM，则此代码将跳过创建过程，并将现有资源详情加载到 `dsvm_compute` 对象。  

时间估计：创建 VM 需要大约 5 分钟。

```python
from azureml.core.compute import DsvmCompute

dsvm_name = 'mydsvm' #Name your DSVM
try:
    dsvm_compute = DsvmCompute(ws, dsvm_name)
    print('found existing dsvm.')
except:
    print('creating new dsvm.')
    # Below is using a VM of SKU Standard_D2_v2 which is 2 core machine. You can check Azure virtual machines documentation for additional SKUs of VMs.
    dsvm_config = DsvmCompute.provisioning_configuration(vm_size = "Standard_D2_v2")
    dsvm_compute = DsvmCompute.create(ws, name = dsvm_name, provisioning_configuration = dsvm_config)
    dsvm_compute.wait_for_completion(show_output = True)
```

现在，可以使用 `dsvm_compute` 对象作为远程计算目标。

DSVM 名称限制包括：
+ 必须小于 64 个字符。  
+ 不得包含以下任何字符：`\` ~ ! @ # $ % ^ & * ( ) = + _ [ ] { } \\\\ | ; : \' \\" , < > / ?.`

>[!Warning]
>如果创建失败，并收到有关商城购买资格的消息：
>    1. 转到 [Azure 门户](https://portal.azure.com)
>    1. 开始创建 DSVM 
>    1. 选择“希望以编程方式创建”来启用以编程方式创建
>    1. 在没有实际创建 VM 的情况下退出
>    1. 重新运行创建代码

此代码没有为预配的 DSVM 创建用户名或密码。 如果希望直接连接到 VM，请转到 [Azure 门户](https://portal.azure.com)来预配凭据。  


## <a name="access-data-using-getdata-file"></a>使用 get_data 文件访问数据

提供对定型数据的远程资源访问权限。 对于在远程计算上运行的自动化机器学习实验，需要使用 `get_data()` 函数来提取数据。  

若要提供访问权限，必须：
+ 创建一个包含 `get_data()` 函数的 get_data.py 文件 
* 将该文件放在包含脚本的文件夹根目录中 

你可以封装代码，以从 blob 存储或 get_data.py 文件中的本地磁盘读取数据。 在下面的代码示例中，数据来自 sklearn 包。

>[!Warning]
>如果使用的是远程计算，则必须使用 `get_data()`，其中将执行数据转换。 如果需要为 get_data() 中的数据转换安装额外的库，则应当接着执行额外的步骤。 有关详细信息，请参阅 [auto-ml-dataprep sample notebook](https://aka.ms/aml-auto-ml-data-prep )。


```python
# Create a project_folder if it doesn't exist
if not os.path.exists(project_folder):
    os.makedirs(project_folder)

#Write the get_data file.
%%writefile $project_folder/get_data.py

from sklearn import datasets
from scipy import sparse
import numpy as np

def get_data():
    
    digits = datasets.load_digits()
    X_digits = digits.data[10:,:]
    y_digits = digits.target[10:]

    return { "X" : X_digits, "y" : y_digits }
```

## <a name="configure-experiment"></a>配置试验

为 `AutoMLConfig` 指定设置。  （请参阅[完整参数列表]()及其可能值。）

在设置中，`run_configuration` 设置为 `run_config` 对象，其中包含 DSVM 的设置和配置。  

```python
from azureml.train.automl import AutoMLConfig
import time
import logging

automl_settings = {
    "name": "AutoML_Demo_Experiment_{0}".format(time.time()),
    "max_time_sec": 600,
    "iterations": 20,
    "n_cross_validations": 5,
    "primary_metric": 'AUC_weighted',
    "preprocess": False,
    "concurrent_iterations": 10,
    "verbosity": logging.INFO
}

automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target = dsvm_compute,
                             data_script=project_folder + "/get_data.py",
                             **automl_settings
                            )
```

## <a name="submit-training-experiment"></a>提交训练试验

现在，请提交配置，以自动选择算法、超参数并定型模型。 （了解有关 `submit` 方法[设置的详细信息]()。）

```python
from azureml.core.experiment import Experiment
experiment=Experiment(ws, 'automl_remote')
remote_run = experiment.submit(automl_config, show_output=True)
```
会看到类似于下面的输出：

    Running on remote compute: mydsvmParent Run ID: AutoML_015ffe76-c331-406d-9bfd-0fd42d8ab7f6
    ***********************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE:  A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    ***********************************************************************************************
    
     ITERATION     PIPELINE                               DURATION                METRIC      BEST
             2      Standardize SGD classifier            0.0                      0.954     0.954
             7      Normalizer DT                         0.0                      0.161     0.954
             0      Scale MaxAbs 1 extra trees            0.0                      0.936     0.954
             4      Robust Scaler SGD classifier          0.0                      0.867     0.954
             1      Normalizer kNN                        0.0                      0.984     0.984
             9      Normalizer extra trees                0.0                      0.834     0.984
             5      Robust Scaler DT                      0.0                      0.736     0.984
             8      Standardize kNN                       0.0                      0.981     0.984
             6      Standardize SVM                       2.2                      0.984     0.984
            10      Scale MaxAbs 1 DT                     0.0                      0.077     0.984
            11      Standardize SGD classifier            0.0                      0.863     0.984
             3      Standardize gradient boosting         5.4                      0.971     0.984
            12      Robust Scaler logistic regression     2.0                      0.955     0.984
            14      Scale MaxAbs 1 SVM                    0.0                      0.989     0.989
            13      Scale MaxAbs 1 gradient boosting      3.4                      0.971     0.989
            15      Robust Scaler kNN                     0.0                      0.904     0.989
            17      Standardize kNN                       0.0                      0.974     0.989
            16      Scale 0/1 gradient boosting           2.8                      0.968     0.989
            18      Scale 0/1 extra trees                 0.0                      0.828     0.989
            19      Robust Scaler kNN                     0.0                      0.983     0.989


## <a name="explore-results"></a>浏览结果

可以使用与[培训教程](tutorial-auto-train-models.md#explore-the-results)中相同的 Jupyter 小组件来查看图表和结果表格。

```python
from azureml.train.widgets import RunDetails
RunDetails(remote_run).show()
```
下面是小组件的静态图像。  在笔记本中，可以单击表格中的任意一行，查看运行属性和该运行的输出日志。   此外，还可以使用图表上方的下拉列表来查看每个迭代的每个可用指标的图表。

![小组件表](./media/how-to-auto-train-remote/table.png)
![小组件绘图](./media/how-to-auto-train-remote/plot.png)

小组件将显示可用于查看和浏览单个运行详细信息的 URL。
 
### <a name="view-logs"></a>查看日志

在 DSVM 的 /tmp/azureml_run/{iterationid}/azureml-logs 下查找日志。

## <a name="example"></a>示例

[Automl/03.auto-ml-remote-execution.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/automl/03.auto-ml-remote-execution.ipynb) 笔记本演示了这篇文章中的概念。  获取此笔记本：

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>后续步骤

了解[如何为自动培训配置设置](how-to-configure-auto-train.md)。