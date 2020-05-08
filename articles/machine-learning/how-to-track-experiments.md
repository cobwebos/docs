---
title: 记录 ML 试验和指标
titleSuffix: Azure Machine Learning
description: 监视 Azure ML 试验和运行指标，以便改进模型创建过程。 将日志记录添加到训练脚本并查看运行的记录结果。  使用 run.log、Run.start_logging 或 ScriptRunConfig。
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/12/2020
ms.custom: seodec18
ms.openlocfilehash: dcd5668fa2c6e1840eed13a9ee0cbd30d8d8a25a
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82983238"
---
# <a name="monitor-azure-ml-experiment-runs-and-metrics"></a>监视 Azure ML 试验运行和指标
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

通过跟踪试验并监视运行指标来改进模型创建过程。 本文介绍如何将日志记录代码添加到训练脚本、提交试验运行、监视运行以及在 Azure 机器学习中检查结果。

> [!NOTE]
> Azure 机器学习还可以在训练期间记录其他来源的信息，例如自动化机器学习运行或运行训练作业的 Docker 容器。 本文不介绍此类日志。 如果遇到问题且联系了 Microsoft 支持部门，他们可以在排除故障时使用这些日志。

> [!TIP]
> 本文档中的信息主要是为希望监视模型训练过程的数据科学家和开发人员提供的。 如果您是一名管理员，希望监视 Azure 机器学习的资源使用情况和事件，例如配额、已完成的训练运行或已完成的模型部署，请参阅[监视 Azure 机器学习](monitor-azure-machine-learning.md)。

## <a name="available-metrics-to-track"></a>可跟踪的指标

训练实验时可将以下指标添加到运行中。 若要查看可在运行中跟踪的内容的更详细列表，请参阅 [Run 类参考文档](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py)。

|类型| Python 函数 | 说明|
|----|:----|:----|
|标量值 |函数：<br>`run.log(name, value, description='')`<br><br>示例：<br>run.log("accuracy", 0.95) |使用给定名称将数值或字符串值记录到运行中。 在运行中记录某个指标会导致在试验中的运行记录中存储该指标。  可在一次运行中多次记录同一指标，其结果被视为该指标的一个矢量。|
|列表|函数：<br>`run.log_list(name, value, description='')`<br><br>示例：<br>run.log_list("accuracies", [0.6, 0.7, 0.87]) | 使用给定名称将值列表记录到运行中。|
|行|函数：<br>`run.log_row(name, description=None, **kwargs)`<br>示例：<br>run.log_row("Y over X", x=1, y=0.4) | 使用 log_row  创建包含多个列的指标，如 kwargs 中所述。 每个命名的参数会生成一个具有指定值的列。  可调用 log_row  一次，记录一个任意元组，或在一个循环中调用多次，生成一个完整表格。|
|表|函数：<br>`run.log_table(name, value, description='')`<br><br>示例：<br>run.log_table("Y over X", {"x":[1, 2, 3], "y":[0.6, 0.7, 0.89]}) | 使用给定名称将字典对象记录到运行中。 |
|映像|函数：<br>`run.log_image(name, path=None, plot=None)`<br><br>示例：<br>`run.log_image("ROC", plot=plt)` | 将图像记录到运行记录中。 使用 log_image 来记录。PNG 图像文件或 matplotlib 到运行的绘图。  运行记录中可显示和比较这些图像。|
|标记一个运行|函数：<br>`run.tag(key, value=None)`<br><br>示例：<br>run.tag("selected", "yes") | 使用一个字符串键和可选字符串值标记运行。|
|上传文件或目录|函数：<br>`run.upload_file(name, path_or_stream)`<br> <br> 示例：<br>run.upload_file("best_model.pkl", "./model.pkl") | 将文件上传到运行记录。 在指定输出目录中自动运行捕获文件，对于大多数运行类型，该目录默认为 "./outputs"。  仅当需要上传其他文件或未指定输出目录时使用 upload_file。 建议在名称中添加 `outputs` 以便将其上传到输出目录。 可通过调用 `run.get_file_names()` 列出与此运行记录关联的所有文件|

> [!NOTE]
> 标量、列表、行和表的指标的类型可以为：float、integer 或 string。

## <a name="choose-a-logging-option"></a>选择日志记录选项

如果要跟踪或监视试验，须添加代码，用于在提交运行时启动日志记录。 以下是触发运行提交的方法：
* __Run.start_logging__ - 将日志记录功能添加到训练脚本，并在指定试验中启动交互式日志记录会话。 **start_logging** 可创建笔记本等方案中使用的交互式运行。 试验中会话期间记录的任何指标都会添加到运行记录中。
* __ScriptRunConfig__ - 将日志记录功能添加到训练脚本并在运行时加载整个脚本文件夹。  **ScriptRunConfig** 是用于设置脚本运行配置的一个类。 使用此选项，可添加监视代码，在运行完成时发出通知，或让视觉小组件执行监视操作。

## <a name="set-up-the-workspace"></a>设置工作区
添加日志记录和提交试验之前，必须设置工作区。

1. 加载工作区。 若要详细了解如何设置工作区配置，请参阅[工作区配置文件](how-to-configure-environment.md#workspace)。

[！笔记本-python [] （~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb？ name = load_ws）]


## <a name="option-1-use-start_logging"></a>选项 1：使用 start_logging

**start_logging** 可创建笔记本等方案中使用的交互式运行。 试验中会话期间记录的任何指标都会添加到运行记录中。

下面的示例在本地 Jupyter 笔记本中本地训练简单的 sklearn 岭模型。 若要详细了解如何将试验提交到不同的环境，请参阅[使用 Azure 机器学习为模型定型设置计算目标](https://docs.microsoft.com/azure/machine-learning/how-to-set-up-training-targets)。

### <a name="load-the-data"></a>加载数据

此示例使用 scikit-learn 提供的糖尿病数据集，这是一个众所周知的小型数据集。 此单元会加载数据集，并将其拆分为随机定型集和测试集。

[！笔记本-python [] （~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb？ name = load_data）]

### <a name="add-tracking"></a>添加跟踪
使用 Azure 机器学习 SDK 添加试验跟踪并将持久化模型上传到试验运行记录。 以下代码添加标记、日志，并将模型文件上传到试验运行。

[！笔记本-python [] （~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb？ name = create_experiment）]

脚本以 ```run.complete()``` 结束，将运行标记为已完成。  此函数通常用于交互式 Notebook 方案。

## <a name="option-2-use-scriptrunconfig"></a>选项 2：使用 ScriptRunConfig

[**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py)是用于设置脚本运行配置的类。 使用此选项，可添加监视代码，在运行完成时发出通知，或让视觉小组件执行监视操作。

此示例在上面的基本 sklearn 岭模型的基础上进行扩展。 它会对模型的 alpha 值执行简单的参数扫描以捕获指标，并通过在实验中运行来训练模型。 该示例在一个用户管理的环境中执行本地运行。 

1. 创建定型脚本 `train.py`。

   [！代码-python [] （~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py）]

2. `train.py` 脚本引用 `mylib.py`，通过后者，可获取要在岭模型中使用的 alpha 值的列表。

   [！代码-python [] （~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/mylib.py）] 

3. 配置用户管理的本地环境。

   [！笔记本-python [] （~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb？ name = user_managed_env）]


4. 提交要在用户管理的环境中运行的 ```train.py``` 脚本。 整个脚本文件夹都要提交以进行训练，包括 ```mylib.py``` 文件。

   [！笔记本-python [] （~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb？ name = src）][！笔记本-python [] （~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb？ name = run）]




## <a name="manage-a-run"></a>管理运行

[启动、监视和取消训练运行](how-to-manage-runs.md)一文重点介绍了关于管理试验的特定 Azure 机器学习工作流。

## <a name="view-run-details"></a>查看运行详细信息

### <a name="view-activequeued-runs-from-the-browser"></a>在浏览器中查看活动/已排队运行

用于训练模型的计算目标是共享资源。 所以在某些时间点，它们可能会有多个排队或活动的运行。 若要在浏览器中查看特定计算目标的运行，请执行以下步骤：

1. 在 [Azure 机器学习工作室](https://ml.azure.com/)中选择自己的工作区，然后在页面左侧选择“计算”____。

1. 选择“正在训练群集”，显示用于训练的计算目标列表____。 然后选择群集。

    ![选择训练群集](./media/how-to-track-experiments/select-training-compute.png)

1. 选择“运行”____。 此时显示使用此群集的运行列表。 若要查看某个特定运行的详细信息，请点击“运行”列中的链接____。 若要查看试验的详细信息，请点击“试验”列中的链接____。

    ![选择训练群集的运行](./media/how-to-track-experiments/show-runs-for-compute.png)
    
    > [!TIP]
    > 一个运行可以包含多个子级运行，所以一个训练作业可能会产生多个条目。

完成的运行将不再显示在此页上。 若要查看已完成运行的信息，请访问工作室的“试验”部分，然后选择试验和运行____。 有关详细信息，请参阅[查询运行指标](#queryrunmetrics)部分。

### <a name="monitor-run-with-jupyter-notebook-widget"></a>使用 Jupyter 笔记本小组件监视运行
使用 ScriptRunConfig**** 方法提交运行时，可使用 [Jupyter 小组件](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py)监视运行的进度。 和运行提交一样，该小组件采用异步方式，并每隔 10-15 秒提供实时更新，直到作业完成。

1. 在等待运行完成的期间查看 Jupyter 小组件。

   ```python
   from azureml.widgets import RunDetails
   RunDetails(run).show()
   ```

   ![Jupyter 笔记本小组件的屏幕截图](./media/how-to-track-experiments/run-details-widget.png)

   也可以在工作区中找到指向此画面的链接。

   ```python
   print(run.get_portal_url())
   ```

2. **[适用于自动化机器学习运行]** 从以前的运行访问图表。 将 `<<experiment_name>>` 替换为相应的试验名称：

   ``` 
   from azureml.widgets import RunDetails
   from azureml.core.run import Run

   experiment = Experiment (workspace, <<experiment_name>>)
   run_id = 'autoML_my_runID' #replace with run_ID
   run = Run(experiment, run_id)
   RunDetails(run).show()
   ```

   ![自动化机器学习的 Jupyter Notebook 小组件](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)


若要查看某个管道的其他详细信息，请在表中单击要探索的管道，随后，图表将在 Azure 机器学习工作室的弹出窗口中呈现。

### <a name="get-log-results-upon-completion"></a>完成时获取日志结果

模型定型和监视在后台进行，以便在等待时可运行其他任务。 也可以先耐心等待模型完成定型，然后再运行其它代码。 使用 ScriptRunConfig**** 时，可以使用 ```run.wait_for_completion(show_output = True)``` 在模型定型完成时进行显示。 使用 ```show_output``` 标志可查看详细输出。 

<a id="queryrunmetrics"></a>

### <a name="query-run-metrics"></a>查询运行指标

可以使用 ```run.get_metrics()``` 查看训练的模型的指标。 现在可以获取上面示例中记录的所有指标以确定最佳模型。

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-the-experiment-in-your-workspace-in-azure-machine-learning-studio"></a>在 [Azure 机器学习工作室](https://ml.azure.com)中查看工作区中的试验

当实验完成运行时，可浏览到试验运行记录。 可在 [Azure 机器学习工作室](https://ml.azure.com)中访问历史记录。

导航到“试验”选项卡并选择自己的试验。 此时会转到试验运行仪表板，可在其中查看为每个运行记录的跟踪指标和图表。 在本例中，记录了 MSE 和 alpha 值。

  ![Azure 机器学习工作室中的运行详细信息](./media/how-to-track-experiments/experiment-dashboard.png)

还可以向下钻取至特定运行以查看其输出或日志，或下载提交的试验的快照，以便与其他人共享试验文件夹。

### <a name="viewing-charts-in-run-details"></a>在运行详细信息中查看图表

可通过多种方式使用日志记录 API 在运行期间记录不同类型的指标，然后在 Azure 机器学习工作室中以图表形式查看这些指标。

|记录的值|示例代码| 在门户中查看|
|----|----|----|
|记录一组数值| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|单变量折线图|
|使用重复使用的相同指标名称记录单个数值（例如在 for 循环中）| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| 单变量折线图|
|重复记录包含 2 个数字列的行|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|双变量折线图|
|记录包含 2 个数字列的表|`run.log_table(name='Sine Wave', value=sines)`|双变量折线图|


## <a name="example-notebooks"></a>示例笔记本
下面的笔记本展示了本文中的概念：
* [how-to-use-azureml/training/train-within-notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook)
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [如何使用-azureml/跟踪和监视-试验/记录-api](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>后续步骤

尝试执行以下后续步骤，了解如何将 Azure 机器学习 SDK 用于 Python：

* 查看教程[使用 Azure 机器学习训练图像分类模型](tutorial-train-models-with-aml.md)中的示例，了解如何注册和部署最佳模型。

* 了解如何[使用 Azure 机器学习训练 PyTorch 模型](how-to-train-pytorch.md)。
