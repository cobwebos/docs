---
title: 在 Python 中启动、监视和取消定型运行
titleSuffix: Azure Machine Learning
description: 了解如何启动、设置的状态、标记和组织您的机器学习试验。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 11/04/2019
ms.openlocfilehash: 32b477a108649abd3faedd70d1a3b54b31089b9a
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73574313"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>在 Python 中启动、监视和取消定型运行
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

用于 Python 和[机器学习 CLI](reference-azure-machine-learning-cli.md)的[Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)提供多种方法来监视、组织和管理运行以进行定型和试验。

本文显示以下任务的示例：

* 监视运行性能。
* 取消或失败的运行。
* 创建子运行。
* 标记和查找运行。

## <a name="prerequisites"></a>先决条件

你将需要以下项：

* Azure 订阅。 如果还没有 Azure 订阅，请在开始前创建免费帐户。 立即试用[Azure 机器学习免费版或付费版](https://aka.ms/AMLFree)。

* [Azure 机器学习工作区](how-to-manage-workspace.md)。

* 用于 Python 的 Azure 机器学习 SDK （版本1.0.21 或更高版本）。 若要安装或更新到最新版本的 SDK，请参阅[安装或更新 sdk](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。

    若要检查 Azure 机器学习 SDK 的版本，请使用以下代码：

    ```python
    print(azureml.core.VERSION)
    ```

* Azure 机器学习的[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)和[CLI 扩展](reference-azure-machine-learning-cli.md)。

## <a name="start-a-run-and-its-logging-process"></a>启动运行及其日志记录过程

### <a name="using-the-sdk"></a>使用 SDK

通过从[azureml](https://docs.microsoft.com/python/api/azureml-core/azureml.core?view=azure-ml-py)包导入[工作区](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py)、[试验](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py)、[运行](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py)和[ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py)类来设置试验。

```python
import azureml.core
from azureml.core import Workspace, Experiment, Run
from azureml.core import ScriptRunConfig

ws = Workspace.from_config()
exp = Experiment(workspace=ws, name="explore-runs")
```

使用[`start_logging()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#start-logging--args----kwargs-)方法启动运行及其日志记录过程。

```python
notebook_run = exp.start_logging()
notebook_run.log(name="message", value="Hello from run!")
```

### <a name="using-the-cli"></a>使用 CLI

若要开始运行实验，请执行以下步骤：

1. 在 shell 或命令提示符下，使用 Azure CLI 向 Azure 订阅进行身份验证：

    ```azurecli-interactive
    az login
    ```

1. 将工作区配置附加到包含定型脚本的文件夹。 将 `myworkspace` 替换为 Azure 机器学习工作区。 将 `myresourcegroup` 替换为包含工作区的 Azure 资源组：

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    此命令创建一个 `.azureml` 子目录，其中包含 .runconfig 和 conda 环境文件示例。 它还包含用于与 Azure 机器学习工作区进行通信的 `config.json` 文件。

    有关详细信息，请参阅[az ml folder attach](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach)。

2. 若要开始运行，请使用以下命令。 使用此命令时，请指定 .runconfig 文件的名称 \*（如果您正在查看文件系统，则为 .runconfig）。

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > `az ml folder attach` 命令创建了一个 `.azureml` 子目录，其中包含两个 .runconfig 文件示例。
    >
    > 如果你的 Python 脚本以编程方式创建运行配置对象，则可以使用[.runconfig （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-)将其另存为 .runconfig 文件。
    >
    > 有关 .runconfig 文件的更多示例，请参阅[https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml](https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml)。

    有关详细信息，请参阅[az ml run 提交脚本](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script)。

## <a name="monitor-the-status-of-a-run"></a>监视运行状态

### <a name="using-the-sdk"></a>使用 SDK

使用[`get_status()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-status--)方法获取运行状态。

```python
print(notebook_run.get_status())
```

若要获取运行 ID、执行时间和有关运行的其他详细信息，请使用[`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#get-details--)方法。

```python
print(notebook_run.get_details())
```

成功完成运行后，使用[`complete()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-)方法将其标记为已完成。

```python
notebook_run.complete()
print(notebook_run.get_status())
```

如果使用 Python 的 `with...as` 设计模式，则在运行超出范围时，运行会自动将其标记为已完成。 不需要手动将运行标记为 "已完成"。

```python
with exp.start_logging() as notebook_run:
    notebook_run.log(name="message", value="Hello from run!")
    print(notebook_run.get_status())

print(notebook_run.get_status())
```

### <a name="using-the-cli"></a>使用 CLI

1. 若要查看试验的运行列表，请使用以下命令。 将 `experiment` 替换为试验的名称：

    ```azurecli-interactive
    az ml run list --experiment-name experiment
    ```

    此命令将返回一个 JSON 文档，其中列出了有关此试验运行的信息。

    有关详细信息，请参阅[az ml 实验列表](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list)。

2. 若要查看特定运行的信息，请使用以下命令。 将 `runid` 替换为运行 ID：

    ```azurecli-interactive
    az ml run show -r runid
    ```

    此命令将返回一个 JSON 文档，其中列出了有关运行的信息。

    有关详细信息，请参阅[az ml run show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-show)。

## <a name="cancel-or-fail-runs"></a>取消或失败运行

如果你注意到错误，或者运行时间太长而无法完成，则可以取消该运行。

### <a name="using-the-sdk"></a>使用 SDK

若要使用 SDK 取消运行，请使用[`cancel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#cancel--)方法：

```python
run_config = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')
local_script_run = exp.submit(run_config)
print(local_script_run.get_status())

local_script_run.cancel()
print(local_script_run.get_status())
```

如果您的运行已完成，但它包含错误（例如，使用的训练脚本不正确），则可以使用[`fail()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)#fail-error-details-none--error-code-none---set-status-true-)方法将其标记为失败。

```python
local_script_run = exp.submit(run_config)
local_script_run.fail()
print(local_script_run.get_status())
```

### <a name="using-the-cli"></a>使用 CLI

若要使用 CLI 取消运行，请使用以下命令。 将 `runid` 替换为运行的 ID

```azurecli-interactive
az ml run cancel -r runid -w workspace_name -e experiment_name
```

有关详细信息，请参阅[az ml 运行 cancel](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-cancel)。

## <a name="create-child-runs"></a>创建子运行

创建子运行以将相关运行组合在一起，如用于不同的超参数优化迭代。

> [!NOTE]
> 仅可使用 SDK 创建子运行。

此代码示例使用[`child_run()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#child-run-name-none--run-id-none--outputs-none-)方法，在提交的运行中使用 `hello_with_children.py` 脚本创建一批五个子运行：

```python
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
> 超出范围时，子运行将自动标记为 "已完成"。

若要有效地创建多个子运行，请使用[`create_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#create-children-count-none--tag-key-none--tag-values-none-)方法。 由于每次创建都会导致网络调用，因此创建一批批处理比逐个创建运行更为有效。

### <a name="submit-child-runs"></a>提交子运行

还可以从父运行提交子运行。 这样，你便可以创建父和子运行的层次结构，每个层次结构都在不同的计算目标上运行，这些层次结构由通用父运行 ID 连接。

使用["submit_child （）"](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#submit-child-config--tags-none----kwargs-)方法可从父运行中提交子运行。 若要在父运行脚本中执行此操作，请获取运行上下文，并使用上下文实例的 ``submit_child`` 方法提交子运行。

```python
## In parent run script
parent_run = Run.get_context()
child_run_config = ScriptRunConfig(source_directory='.', script='child_script.py')
parent_run.submit_child(child_run_config)
```

在子运行中，可以查看父运行 ID：

```python
## In child run script
child_run = Run.get_context()
child_run.parent.id
```

### <a name="query-child-runs"></a>查询子运行

若要查询特定父对象的子运行，请使用[`get_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-)方法。 使用 ``recursive = True`` 参数可以查询子级和孙级的嵌套树。

```python
print(parent_run.get_children())
```

## <a name="tag-and-find-runs"></a>标记和查找运行

在 Azure 机器学习中，可以使用属性和标记来帮助组织和查询运行以了解重要信息。

### <a name="add-properties-and-tags"></a>添加属性和标记

#### <a name="using-the-sdk"></a>使用 SDK

若要将可搜索的元数据添加到运行，请使用[`add_properties()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#add-properties-properties-)方法。 例如，下面的代码将 `"author"` 属性添加到运行中：

```Python
local_script_run.add_properties({"author":"azureml-user"})
print(local_script_run.get_properties())
```

属性是不可变的，因此它们会出于审核目的创建永久记录。 下面的代码示例会导致错误，因为我们已添加 `"azureml-user"` 作为前面代码中 `"author"` 属性值：

```Python
try:
    local_script_run.add_properties({"author":"different-user"})
except Exception as e:
    print(e)
```

与属性不同，标记是可变的。 若要为试验的使用者添加可搜索和有意义的信息，请使用[`tag()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#tag-key--value-none-)方法。

```Python
local_script_run.tag("quality", "great run")
print(local_script_run.get_tags())

local_script_run.tag("quality", "fantastic run")
print(local_script_run.get_tags())
```

你还可以添加简单的字符串标记。 如果这些标记在标记字典中显示为键，则其值为 `None`。

```Python
local_script_run.tag("worth another look")
print(local_script_run.get_tags())
```

#### <a name="using-the-cli"></a>使用 CLI

> [!NOTE]
> 使用 CLI，只能添加或更新标记。

若要添加或更新标记，请使用以下命令：

```azurecli-interactive
az ml run update -r runid --add-tag quality='fantastic run'
```

有关详细信息，请参阅[az ml run update](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-update)。

### <a name="query-properties-and-tags"></a>查询属性和标记

可以查询在实验中运行，以返回与特定属性和标记匹配的运行列表。

#### <a name="using-the-sdk"></a>使用 SDK

```Python
list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
```

#### <a name="using-the-cli"></a>使用 CLI

Azure CLI 支持[JMESPath](http://jmespath.org)查询，该查询可用于基于属性和标记来筛选运行。 若要将 JMESPath 查询与 Azure CLI 一起使用，请使用 `--query` 参数指定它。 下面的示例显示了使用属性和标记的基本查询：

```azurecli-interactive
# list runs where the author property = 'azureml-user'
az ml run list --experiment-name experiment [?properties.author=='azureml-user']
# list runs where the tag contains a key that starts with 'worth another look'
az ml run list --experiment-name experiment [?tags.keys(@)[?starts_with(@, 'worth another look')]]
# list runs where the author property = 'azureml-user' and the 'quality' tag starts with 'fantastic run'
az ml run list --experiment-name experiment [?properties.author=='azureml-user' && tags.quality=='fantastic run']
```

有关查询 Azure CLI 结果的详细信息，请参阅[Query Azure CLI 命令 output](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest)。

## <a name="example-notebooks"></a>示例笔记本

以下笔记本演示了本文中的概念：

* 若要了解有关日志记录 Api 的详细信息，请参阅[日志记录 api 笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api/logging-api.ipynb)。

* 有关管理 Azure 机器学习 SDK 运行的详细信息，请参阅[管理运行笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/manage-runs/manage-runs.ipynb)。

## <a name="next-steps"></a>后续步骤

* 若要了解如何记录试验的指标，请参阅[训练运行期间的日志指标](how-to-track-experiments.md)。
* 若要了解如何监视 Azure 机器学习中的资源和日志，请参阅[监视 Azure 机器学习](monitor-azure-machine-learning.md)。