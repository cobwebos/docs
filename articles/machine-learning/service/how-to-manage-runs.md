---
title: 启动、 监视和取消在 Python 中的训练运行
titleSuffix: Azure Machine Learning service
description: 了解如何启动、 设置标记，状态和组织在机器学习实验。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 04/05/2019
ms.openlocfilehash: c0c1c1353b12944fa913dfb0789192917b99f234
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60819207"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>启动、 监视和取消在 Python 中的训练运行

[适用于 Python 的 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)提供各种方法来监视、 组织和管理在运行过程中的用于训练和试验。

本文介绍的以下任务的示例：

* 运行性能监视器。
* 取消或失败运行。
* 创建子运行。
* 标记和查找的运行。

## <a name="prerequisites"></a>必备组件

您将需要以下项：

* Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个免费帐户。 立即试用 [Azure 机器学习服务免费版或付费版](https://aka.ms/AMLFree)。

* [Azure 机器学习服务工作区](setup-create-workspace.md)。

* 用于 Python 的 Azure 机器学习 SDK (版本 1.0.21 或更高版本)。 若要安装或更新到最新版本的 sdk，请参阅[安装或更新 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。

    若要检查你的 Azure 机器学习 SDK 版本，请使用以下代码：

    ```Python
    print(azureml.core.VERSION)
    ```

## <a name="start-a-run-and-its-logging-process"></a>开始运行和其日志记录进程

通过导入设置试验[工作区](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py)，[试验](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py)，[运行](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py)，并[ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py)类从[azureml.core](https://docs.microsoft.com/python/api/azureml-core/azureml.core?view=azure-ml-py)包。

```Python
import azureml.core
from azureml.core import Workspace, Experiment, Run
from azureml.core import ScriptRunConfig

ws = Workspace.from_config()
exp = Experiment(workspace=ws, name="explore-runs")
```

启动运行并且使用其日志记录进程[ `start_logging()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#start-logging--args----kwargs-)方法。

```Python
notebook_run = exp.start_logging()

notebook_run.log(name="message", value="Hello from run!")
```

## <a name="monitor-the-status-of-a-run"></a>监视运行状态

获取与运行的状态[ `get_status()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-status--)方法。

```Python
print(notebook_run.get_status())
```

若要获取有关运行的其他详细信息，请使用[ `get_details()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#get-details--)方法。

```Python
notebook_run.get_details()
```

当你运行已成功完成后时，使用[ `complete()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-)方法以将其标记为已完成。

```Python
notebook_run.complete()
print(notebook_run.get_status())
```

如果使用 Python 的`with...as`模式下，运行会自动将标记本身为已完成运行后超出范围。 您不必手动将运行标记为已完成。

```Python
with exp.start_logging() as notebook_run:
    notebook_run.log(name="message", value="Hello from run!")
    print("Is it still running?",notebook_run.get_status())

print("Has it completed?",notebook_run.get_status())
```

## <a name="cancel-or-fail-runs"></a>取消或失败，运行

 如果您注意到了错误的操作或如果您运行所需时间太长，无法完成，请使用[ `cancel()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#cancel--)方法完成之前停止运行并将其标记为已取消。

```Python
run_config = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')

local_script_run = exp.submit(run_config)
print("Did the run start?",local_script_run.get_status())

local_script_run.cancel()
print("Did the run cancel?",local_script_run.get_status())
```

如果运行完成，但其包含一个错误 （例如，使用不正确的训练脚本），可以使用[ `fail()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#fail-error-details-none---set-status-true-)方法以将其标记为失败。

```Python
local_script_run = exp.submit(run_config)
local_script_run.fail()

print(local_script_run.get_status())
```

## <a name="create-child-runs"></a>创建子运行

创建子要组合在一起相关的运行时，例如为不同的超参数优化迭代的运行。

此代码示例使用`hello_with_children.py`脚本以使用创建从已提交的运行中的五个子运行一批[ `child_run()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#child-run-name-none--run-id-none--outputs-none-)方法：

```Python
!more hello_with_children.py
run_config = ScriptRunConfig(source_directory='.', script='hello_with_children.py')

local_script_run = exp.submit(run_config)
local_script_run.wait_for_completion(show_output=True)
print(local_script_run.get_status())

with exp.start_logging() as parent_run:
    for c,count in enumerate(range(5)):
        with parent_run.child_run() as child:
            child.log(name="Hello from child run", value=c)
```

> [!NOTE]
> 当它们不在范围内移动时，子运行将自动标记为已完成。

你还可以启动子运行，但每个创建会导致网络调用，因为它是比提交批处理的运行效率较低。

若要查询特定父节点的子级运行，请使用[ `get_children()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-)方法。

```Python
list(parent_run.get_children())
```

## <a name="tag-and-find-runs"></a>标记和查找的运行

在 Azure 机器学习服务中，可以使用属性和标记以帮助组织和查询在运行过程中的重要信息。

### <a name="add-properties-and-tags"></a>添加属性和标记

若要将搜索元数据添加到在运行过程中，使用[ `add_properties()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#add-properties-properties-)方法。 例如，下面的代码添加`"author"`属性设置为运行：

```Python
local_script_run.add_properties({"author":"azureml-user"})
print(local_script_run.get_properties())
```

属性是不可变的因此他们创建的永久记录供审核。 下面的代码示例将导致错误，因为我们已添加`"azureml-user"`作为`"author"`在上述代码中的属性值：

```Python
try:
    local_script_run.add_properties({"author":"different-user"})
except Exception as e:
    print(e)
```

与属性不同，标记是可更改。 若要添加使用者在试验的可搜索和有意义信息，请使用[ `tag()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#tag-key--value-none-)方法。

```Python
local_script_run.tag("quality", "great run")
print(local_script_run.get_tags())

local_script_run.tag("quality", "fantastic run")
print(local_script_run.get_tags())
```

此外可以添加简单的字符串标记。 当这些标记出现在标记字典时，它们具有值为`None`。

```Python
local_script_run.tag("worth another look")
print(local_script_run.get_tags())
```

### <a name="query-properties-and-tags"></a>查询属性和标记

您可以查询运行试验以返回运行特定的属性和标记相匹配的列表内。

```Python
list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
```

## <a name="example-notebooks"></a>示例笔记本

以下 notebook 演示这篇文章中的概念：

* 若要了解有关日志记录 Api 的详细信息，请参阅[日志记录 API notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api/logging-api.ipynb)。

* 有关使用 Azure 机器学习 SDK 运行管理详细信息，请参阅[管理运行 notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/manage-runs)。

## <a name="next-steps"></a>后续步骤

* 若要了解如何记录用于实验的指标，请参阅[训练运行期间记录指标](how-to-track-experiments.md)。