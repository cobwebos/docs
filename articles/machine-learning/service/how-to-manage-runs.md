---
title: 启动、 监视和取消在 Python 中的训练运行
titleSuffix: Azure Machine Learning service
description: 了解如何启动、 设置标记，状态和组织您的机器学习试验。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 04/05/2019
ms.openlocfilehash: 82df2258116ce55fa440b67ec0a66b106d0d72c7
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/10/2019
ms.locfileid: "59471488"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>启动、 监视和取消在 Python 中的训练运行

[适用于 Python 的 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)提供各种方法来监视、 组织和管理在运行过程中的用于训练和试验。

本操作说明显示了示例的以下任务：

* [运行性能监视器](#monitor)
* [取消或失败，运行](#cancel)
* [创建子运行](#children)
* [标记和查找的运行](#tag)

## <a name="prerequisites"></a>必备组件

你将需要以下各项：

* Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个免费帐户。 立即试用 [Azure 机器学习服务免费版或付费版](https://aka.ms/AMLFree)。

* Azure 机器学习服务工作区。 请参阅[创建 Azure 机器学习服务工作区](setup-create-workspace.md)。

* 用于安装 Python 的 Azure 机器学习 SDK (版本 1.0.21 或更高版本)。 若要安装或更新到最新版本的 sdk，请转到[安装/更新 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)页。

    若要检查你的 Azure 机器学习 SDK 版本，请使用下面的代码。

    ```Python
    print(azureml.core.VERSION)
    ```

<a name="monitor"></a>

## <a name="start-a-run-and-set-its-status"></a>启动运行并将其状态设置

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

获取与运行的状态[ `get_status()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-status--)。

```Python
print(notebook_run.get_status())
```

若要获取有关运行使用其他详细信息[ `get_details()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#get-details--)。

```Python
notebook_run.get_details()
```

当你运行已成功完成后时，使用[ `complete()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-)方法以将其标记为已完成。

```Python
notebook_run.complete()
print(notebook_run.get_status())
```

此外可以使用 Python 的`with...as`模式。 在此上下文中运行会自动将标记本身为已完成运行后超出范围。 这样一来您不需要手动将运行标记为已完成。

```Python
with exp.start_logging() as notebook_run:
    notebook_run.log(name="message", value="Hello from run!")
    print("Is it still running?",notebook_run.get_status())

print("Has it completed?",notebook_run.get_status())
```

<a name="cancel"></a>

## <a name="cancel-or-fail-runs"></a>取消或失败，运行

 如果您注意到了错误的操作或运行似乎要花一段时间才能完成，请使用[ `cancel()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#cancel--)方法完成之前停止运行并将其标记为已取消。

```Python
run_config = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')

local_script_run = exp.submit(run_config)
print("Did the run start?",local_script_run.get_status())

local_script_run.cancel()
print("Did the run cancel?",local_script_run.get_status())
```

如果你运行完成后，但包含类似的错误，使用了不正确的训练脚本，则可以使用[ `fail()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#fail-error-details-none---set-status-true-)方法以将其标记为失败。

```Python
local_script_run = exp.submit(run_config)
local_script_run.fail()

print(local_script_run.get_status())
```

<a name="children"></a>

## <a name="create-child-runs"></a>创建子运行

创建子要组合在一起相关的运行时，例如为不同的超参数优化迭代的运行。

此代码示例使用 hello_with_children.py 脚本来创建一个批处理中提交的运行使用五个子运行来自[ `child_run()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#child-run-name-none--run-id-none--outputs-none-)方法。

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
> 子运行完成时自动随着超出范围。

你还可以启动子运行，但每个创建会导致网络调用，因为它是比提交批处理的运行效率较低。

使用[ `get_children()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-)特定父项的运行方法，以查询子级。

```Python
list(parent_run.get_children())
```

<a name="tag"></a>

## <a name="tag-and-find-runs"></a>标记和查找的运行

在 Azure 机器学习服务中，可以使用属性和标记以帮助组织和查询在运行过程中的重要信息。

### <a name="add-properties-and-tags"></a>添加属性和标记

使用[ `add_properties()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#add-properties-properties-)将可搜索的元数据添加到你的运行。 例如，下面的代码会将"author"属性添加到运行中。

```Python
local_script_run.add_properties({"author":"azureml-user"})
print(local_script_run.get_properties())
```

属性是不可变的这很有用的永久记录供审核。 下面的代码示例将导致错误，因为我们已在前面的代码添加"azureml 用户"作为"author"属性。

```Python
try:
    local_script_run.add_properties({"author":"different-user"})
except Exception as e:
    print(e)
```

标记，但是是可更改。 使用[ `tag()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#tag-key--value-none-)添加使用者在试验的可搜索、 更有意义信息。

```Python
local_script_run.tag("quality", "great run")
print(local_script_run.get_tags())

local_script_run.tag("quality", "fantastic run")
print(local_script_run.get_tags())
```

此外可以添加一个简单的字符串标记。 它会显示在标记字典中的值， `None`。

```Python
local_script_run.tag("worth another look")
print(local_script_run.get_tags())
```

### <a name="query-properties-and-tags"></a>查询属性和标记

您可以查询运行试验，匹配特定的属性和标记内。

```Python
list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
```

## <a name="example-notebooks"></a>示例笔记本

下面的笔记本展示了本文中的概念：

* 若要了解有关日志记录 Api 的详细信息，请参阅[日志记录 API notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api/logging-api.ipynb)。

* 有关使用 Azure 机器学习 SDK 运行管理有关的其他信息，请参阅[管理运行 notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/manage-runs)。

## <a name="next-steps"></a>后续步骤

* 若要了解如何记录用于实验的指标，请参阅[训练运行期间记录指标](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiment)一文。