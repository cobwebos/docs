---
title: 在 Python 中启动、监视和取消训练运行
titleSuffix: Azure Machine Learning
description: 了解如何启动、标记和组织机器学习试验及设置其状态。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 01/09/2020
ms.openlocfilehash: d6dc2eeb572eeed17281677945c93067bbadee94
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82628565"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>在 Python 中启动、监视和取消训练运行
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

用于 Python、[机器学习 CLI](reference-azure-machine-learning-cli.md)和[Azure 机器学习 STUDIO](https://ml.azure.com)的[Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)提供多种方法来监视、组织和管理运行以进行定型和试验。

本文演示以下任务的示例：

* 监视运行性能。
* 取消运行或使其失败。
* 创建子运行。
* 标记和查找运行。

## <a name="prerequisites"></a>必备条件

需要准备好以下各项：

* Azure 订阅。 如果没有 Azure 订阅，请在开始操作前先创建一个免费帐户。 立即试用[免费版或付费版 Azure 机器学习](https://aka.ms/AMLFree)。

* [Azure 机器学习工作区](how-to-manage-workspace.md)。

* 适用于 Python 的 Azure 机器学习 SDK（1.0.21 或更高版本）。 若要安装或更新到最新版本的 SDK，请参阅[安装或更新 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。

    若要检查 Azure 机器学习 SDK 的版本，请使用以下代码：

    ```python
    print(azureml.core.VERSION)
    ```

* [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) 和 [Azure 机器学习的 CLI 扩展](reference-azure-machine-learning-cli.md)。

## <a name="start-a-run-and-its-logging-process"></a>启动运行及其日志记录过程

### <a name="using-the-sdk"></a>使用 SDK

通过从 [azureml.core](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) 包导入 [Workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py)、[Experiment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py)、[Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) 和 [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core?view=azure-ml-py) 类来设置试验。

```python
import azureml.core
from azureml.core import Workspace, Experiment, Run
from azureml.core import ScriptRunConfig

ws = Workspace.from_config()
exp = Experiment(workspace=ws, name="explore-runs")
```

使用 [`start_logging()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#start-logging--args----kwargs-) 方法启动运行及其日志记录过程。

```python
notebook_run = exp.start_logging()
notebook_run.log(name="message", value="Hello from run!")
```

### <a name="using-the-cli"></a>使用 CLI

若要启动试验的运行，请使用以下步骤：

1. 在 shell 中或命令提示符下，使用 Azure CLI 对 Azure 订阅进行身份验证：

    ```azurecli-interactive
    az login
    ```
    
    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

1. 将工作区配置附加到包含训练脚本的文件夹。 请将 `myworkspace` 替换为你的 Azure 机器学习工作区。 将 `myresourcegroup` 替换为包含工作区的 Azure 资源组：

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    此命令创建包含示例 runconfig 和 conda 环境文件的 `.azureml` 子目录。 此子目录还包含用来与 Azure 机器学习工作区通信的 `config.json` 文件。

    有关详细信息，请参阅 [az ml folder attach](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach)。

2. 若要启动运行，请使用以下命令。 使用此命令时，请为 -c 参数指定 runconfig 文件的名称（如果查看的是文件系统，此名称为 \*.runconfig 前面的文本）。

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > `az ml folder attach` 命令创建了一个 `.azureml` 子目录，其中包含两个示例 runconfig 文件。
    >
    > 如果你的某个 Python 脚本以编程方式创建运行配置对象，则你可以使用 [RunConfig.save()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-) 将此对象另存为 runconfig 文件。
    >
    > 有关更多示例 runconfig 文件，请参阅 [https://github.com/MicrosoftDocs/pipelines-azureml/](https://github.com/MicrosoftDocs/pipelines-azureml/)。

    有关详细信息，请参阅 [az ml run submit-script](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script)。

### <a name="using-azure-machine-learning-studio"></a>使用 Azure 机器学习工作室

若要开始在设计器（预览版）中提交管道运行，请执行以下步骤：

1. 为管道设置默认计算目标。

1. 在管道画布顶部选择“运行”  。

1. 选择用于为管道运行分组的试验。

## <a name="monitor-the-status-of-a-run"></a>监视运行的状态

### <a name="using-the-sdk"></a>使用 SDK

使用 [`get_status()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-status--) 方法获取运行的状态。

```python
print(notebook_run.get_status())
```

若要获取运行 ID、执行时间和有关运行的更多详细信息，请使用 [`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#get-details--) 方法。

```python
print(notebook_run.get_details())
```

成功完成运行后，使用 [`complete()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-) 方法将其标记为已完成。

```python
notebook_run.complete()
print(notebook_run.get_status())
```

如果使用 Python 的 `with...as` 设计模式，则当运行超出范围时，该运行会自动将自身标记为已完成。 无需手动将它标记为已完成。

```python
with exp.start_logging() as notebook_run:
    notebook_run.log(name="message", value="Hello from run!")
    print(notebook_run.get_status())

print(notebook_run.get_status())
```

### <a name="using-the-cli"></a>使用 CLI

1. 若要查看试验的运行列表，请使用以下命令。 请将 `experiment` 替换为你的试验名称。

    ```azurecli-interactive
    az ml run list --experiment-name experiment
    ```

    此命令返回一个 JSON 文档，其中列出了有关此试验的运行的信息。

    有关详细信息，请参阅 [az ml experiment list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list)。

2. 若要查看有关特定运行的信息，请使用以下命令。 请将 `runid` 替换为运行的 ID：

    ```azurecli-interactive
    az ml run show -r runid
    ```

    此命令返回一个 JSON 文档，其中列出了有关运行的信息。

    有关详细信息，请参阅 [az ml run show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-show)。


### <a name="using-azure-machine-learning-studio"></a>使用 Azure 机器学习工作室

在工作室中查看试验的活动运行数。

1. 导航到“试验”  部分。 

1. 选择一个试验。

    在试验页中，可以看到活动计算目标数以及每个运行的持续时间。 

1. 选择特定的运行编号。

1. 在“日志”  选项卡中，可以找到管道运行的诊断日志和错误日志。


## <a name="cancel-or-fail-runs"></a>取消运行或使其失败

如果发现错误，或者完成运行花费的时间太长，可以取消该运行。

### <a name="using-the-sdk"></a>使用 SDK

若要使用 SDK 取消运行，请使用 [`cancel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#cancel--) 方法：

```python
run_config = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')
local_script_run = exp.submit(run_config)
print(local_script_run.get_status())

local_script_run.cancel()
print(local_script_run.get_status())
```

如果运行已完成但包含错误（例如，使用了错误的训练脚本），可以使用 [`fail()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)#fail-error-details-none--error-code-none---set-status-true-) 方法将其标记为失败。

```python
local_script_run = exp.submit(run_config)
local_script_run.fail()
print(local_script_run.get_status())
```

### <a name="using-the-cli"></a>使用 CLI

若要使用 CLI 取消运行，请使用以下命令。 请将 `runid` 替换为运行的 ID

```azurecli-interactive
az ml run cancel -r runid -w workspace_name -e experiment_name
```

有关详细信息，请参阅 [az ml run cancel](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-cancel)。

### <a name="using-azure-machine-learning-studio"></a>使用 Azure 机器学习工作室

若要在工作室中取消某个运行，请执行以下步骤：

1. 转到“试验”  或“管道”  部分中正在运行的管道。 

1. 选择要取消的管道运行编号。

1. 在工具栏中，选择“取消” 


## <a name="create-child-runs"></a>创建子运行

创建子运行可将相关的运行组合到一起，例如，以完成不同的超参数优化迭代。

> [!NOTE]
> 只能使用 SDK 创建子运行。

此代码示例使用 `hello_with_children.py` 脚本，通过 [`child_run()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#child-run-name-none--run-id-none--outputs-none-) 方法从已提交的运行内部创建包含五个子运行的批：

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
> 当子运行超出范围时，会自动标记为已完成。

若要高效地创建许多子运行，请使用 [`create_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#create-children-count-none--tag-key-none--tag-values-none-) 方法。 由于每次创建操作都会造成网络调用，因此，创建一批运行比逐个创建更为高效。

### <a name="submit-child-runs"></a>提交子运行

也可以从父运行提交子运行。 这允许您创建父和子运行的层次结构。 

你可能希望你的子运行使用与父运行不同的运行配置。 例如，你可以对父对象使用基于 GPU 的强大配置，同时为你的孩子使用基于 GPU 的配置。 另一种常见的需求是传递每个子不同的参数和数据。 若要自定义子运行，请`RunConfiguration`将对象传递给子`ScriptRunConfig`构造函数。 此代码示例是父`ScriptRunConfig`对象的脚本的一部分：

- 创建检索`RunConfiguration`命名计算资源的`"gpu-compute"`
- 循环访问要传递给子`ScriptRunConfig`对象的不同参数值
- 使用自定义计算资源和参数创建并提交新的子运行
- 在所有子运行完成之前阻止

```python
# parent.py
# This script controls the launching of child scripts
from azureml.core import Run, ScriptRunConfig, RunConfiguration

run_config_for_aml_compute = RunConfiguration()
run_config_for_aml_compute.target = "gpu-compute"
run_config_for_aml_compute.environment.docker.enabled = True 

run = Run.get_context()

child_args = ['Apple', 'Banana', 'Orange']
for arg in child_args: 
    run.log('Status', f'Launching {arg}')
    child_config = ScriptRunConfig(source_directory=".", script='child.py', arguments=['--fruit', arg], run_config = run_config_for_aml_compute)
    # Starts the run asynchronously
    run.submit_child(child_config)

# Experiment will "complete" successfully at this point. 
# Instead of returning immediately, block until child runs complete

for child in run.get_children():
    child.wait_for_completion()
```

若要创建多个具有相同配置、参数和输入的子运行，请[`create_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#create-children-count-none--tag-key-none--tag-values-none-)使用方法。 由于每次创建操作都会造成网络调用，因此，创建一批运行比逐个创建更为高效。

在子运行内部，可以查看父运行 ID：

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

在 Azure 机器学习中，可以使用属性与标记来帮助组织运行，以及查询运行以获取重要信息。

### <a name="add-properties-and-tags"></a>添加属性和标记

#### <a name="using-the-sdk"></a>使用 SDK

若要将可搜索的元数据添加到[`add_properties()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#add-properties-properties-)运行，请使用方法。 例如，以下代码将 `"author"` 属性添加到运行：

```Python
local_script_run.add_properties({"author":"azureml-user"})
print(local_script_run.get_properties())
```

属性是不可变的，因此它们将创建一条永久记录用于审核目的。 以下代码示例会导致出错，因为我们已在前面的代码中添加了 `"azureml-user"` 作为 `"author"` 属性值：

```Python
try:
    local_script_run.add_properties({"author":"different-user"})
except Exception as e:
    print(e)
```

与属性不同，标记是可变的。 若要为试验的使用者添加可搜索和有意义的[`tag()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#tag-key--value-none-)信息，请使用方法。

```Python
local_script_run.tag("quality", "great run")
print(local_script_run.get_tags())

local_script_run.tag("quality", "fantastic run")
print(local_script_run.get_tags())
```

还可以添加简单的字符串标记。 当这些标记作为键出现在标记字典中时，它们的值为 `None`。

```Python
local_script_run.tag("worth another look")
print(local_script_run.get_tags())
```

#### <a name="using-the-cli"></a>使用 CLI

> [!NOTE]
> 使用 CLI 只能添加或更新标记。

若要添加或更新标记，请使用以下命令：

```azurecli-interactive
az ml run update -r runid --add-tag quality='fantastic run'
```

有关详细信息，请参阅 [az ml run update](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-update)。

### <a name="query-properties-and-tags"></a>查询属性和标记

可以查询试验中的运行，以返回与特定属性和标记匹配的运行列表。

#### <a name="using-the-sdk"></a>使用 SDK

```Python
list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
```

#### <a name="using-the-cli"></a>使用 CLI

Azure CLI 支持 [JMESPath](http://jmespath.org) 查询，可以使用这些查询基于属性和标记来筛选运行。 若要在 Azure CLI 中使用 JMESPath 查询，请使用 `--query` 参数指定该查询。 以下示例演示了使用属性和标记的基本查询：

```azurecli-interactive
# list runs where the author property = 'azureml-user'
az ml run list --experiment-name experiment [?properties.author=='azureml-user']
# list runs where the tag contains a key that starts with 'worth another look'
az ml run list --experiment-name experiment [?tags.keys(@)[?starts_with(@, 'worth another look')]]
# list runs where the author property = 'azureml-user' and the 'quality' tag starts with 'fantastic run'
az ml run list --experiment-name experiment [?properties.author=='azureml-user' && tags.quality=='fantastic run']
```

有关查询 Azure CLI 结果的详细信息，请参阅[查询 Azure CLI 命令输出](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest)。

### <a name="using-azure-machine-learning-studio"></a>使用 Azure 机器学习 studio

1. 导航到 "**管道**" 部分。

1. 使用 "搜索" 栏可以使用标记、说明、试验名称和提交者名称来筛选管道。

## <a name="example-notebooks"></a>示例笔记本

以下笔记本演示了本文中的概念：

* 若要详细了解日志记录 API，请参阅[日志记录 API 笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api/logging-api.ipynb)。

* 有关使用 Azure 机器学习 SDK 管理运行的详细信息，请参阅[管理运行笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/manage-runs/manage-runs.ipynb)。

## <a name="next-steps"></a>后续步骤

* 若要了解如何记录试验的指标，请参阅[在训练运行期间记录指标](how-to-track-experiments.md)。
* 若要了解如何监视 Azure 机器学习中的资源和日志，请参阅[监视 Azure 机器学习](monitor-azure-machine-learning.md)。