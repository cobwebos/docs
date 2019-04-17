---
title: 在训练运行日志指标
titleSuffix: Azure Machine Learning service
description: 了解如何将日志记录添加到训练脚本、如何提交试验、如何检查正在运行的作业的进度以及如何查看运行结果。 您可以跟踪试验并监视度量值来改进模型创建过程。
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 59a801fd149a0f647298d94522f9eefc1ea6eb9f
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2019
ms.locfileid: "59609461"
---
# <a name="log-metrics-during-training-runs-in-azure-machine-learning"></a>在 Azure 机器学习中运行在定型期间日志指标

在本文中，了解如何将日志记录添加到训练脚本、 提交试验运行、 监视运行和查看 Azure 机器学习服务中运行的结果。 通过跟踪试验并监视指标提高模型创建过程中。 

## <a name="list-of-training-metrics"></a>训练指标列表 

训练实验时可将以下指标添加到运行中。 若要查看可在运行中跟踪的内容的更详细列表，请参阅 [Run 类参考文档](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py)。

|Type| Python 函数 | 说明|
|----|:----|:----|
|标量值 |函数：<br>`run.log(name, value, description='')`<br><br>示例：<br>run.log("accuracy", 0.95) |使用给定名称将数值或字符串值记录到运行中。 在运行中记录某个指标会导致在试验中的运行记录中存储该指标。  可在一次运行中多次记录同一指标，其结果被视为该指标的一个矢量。|
|列表|函数：<br>`run.log_list(name, value, description='')`<br><br>示例：<br>run.log_list("accuracies", [0.6, 0.7, 0.87]) | 使用给定名称将值列表记录到运行中。|
|行|函数：<br>`run.log_row(name, description=None, **kwargs)`<br>示例：<br>run.log_row("Y over X", x=1, y=0.4) | 使用 log_row 创建包含多个列的指标，如 kwargs 中所述。 每个命名的参数会生成一个具有指定值的列。  可调用 log_row 一次，记录一个任意元组，或在一个循环中调用多次，生成一个完整表格。|
|表|函数：<br>`run.log_table(name, value, description='')`<br><br>示例：<br>run.log_table("Y over X", {"x":[1, 2, 3], "y":[0.6, 0.7, 0.89]}) | 使用给定名称将字典对象记录到运行中。 |
|映像|函数：<br>`run.log_image(name, path=None, plot=None)`<br><br>示例：<br>`run.log_image("ROC", plt)` | 将图像记录到运行记录中。 使用 log_image 在运行中记录图像文件或 matplotlib 图。  运行记录中可显示和比较这些图像。|
|标记一个运行|函数：<br>`run.tag(key, value=None)`<br><br>示例：<br>run.tag("selected", "yes") | 使用一个字符串键和可选字符串值标记运行。|
|上传文件或目录|函数：<br>`run.upload_file(name, path_or_stream)`<br> <br> 示例：<br>run.upload_file("best_model.pkl", "./model.pkl") | 将文件上传到运行记录。 在指定输出目录中自动运行捕获文件，对于大多数运行类型，该目录默认为 "./outputs"。  仅当需要上传其他文件或未指定输出目录时使用 upload_file。 建议在名称中添加 `outputs` 以便将其上传到输出目录。 可通过调用 `run.get_file_names()` 列出与此运行记录关联的所有文件|

> [!NOTE]
> 标量、列表、行和表的指标的类型可以为：float、integer 或 string。

## <a name="start-logging-metrics"></a>开始记录指标

如果要跟踪或监视试验，须添加代码，用于在提交运行时启动日志记录。 以下是触发运行提交的方法：
* __Run.start_logging__ - 将日志记录功能添加到训练脚本，并在指定试验中启动交互式日志记录会话。 **start_logging** 可创建笔记本等方案中使用的交互式运行。 试验中会话期间记录的任何指标都会添加到运行记录中。
* __ScriptRunConfig__ - 将日志记录功能添加到训练脚本并在运行时加载整个脚本文件夹。  **ScriptRunConfig** 是用于设置脚本运行配置的一个类。 使用此选项，可添加监视代码，在运行完成时发出通知，或让视觉小组件执行监视操作。

## <a name="set-up-the-workspace"></a>设置工作区
添加日志记录和提交试验之前，必须设置工作区。

1. 加载工作区。 若要了解有关设置工作区配置的详细信息，请按照中的步骤[创建 Azure 机器学习服务工作区](setup-create-workspace.md#sdk)。

   ```python
   from azureml.core import Experiment, Run, Workspace
   import azureml.core
  
   ws = Workspace(workspace_name = <<workspace_name>>,
               subscription_id = <<subscription_id>>,
               resource_group = <<resource_group>>)
   ```
  
## <a name="option-1-use-startlogging"></a>选项 1：使用 start_logging

**start_logging** 可创建笔记本等方案中使用的交互式运行。 试验中会话期间记录的任何指标都会添加到运行记录中。

下面的示例在本地 Jupyter 笔记本中本地训练简单的 sklearn 岭模型。 若要详细了解如何将试验提交到不同的环境，请参阅[使用 Azure 机器学习服务为模型定型设置计算目标](https://docs.microsoft.com/azure/machine-learning/service/how-to-set-up-training-targets)。

1. 在本地 Jupyter 笔记本中创建训练脚本。 

   ``` python
   # load diabetes dataset, a well-known small dataset that comes with scikit-learn
   from sklearn.datasets import load_diabetes
   from sklearn.linear_model import Ridge
   from sklearn.metrics import mean_squared_error
   from sklearn.model_selection import train_test_split
   from sklearn.externals import joblib

   X, y = load_diabetes(return_X_y = True)
   columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
   X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state = 0)
   data = {
      "train":{"X": X_train, "y": y_train},        
      "test":{"X": X_test, "y": y_test}
   }
   reg = Ridge(alpha = 0.03)
   reg.fit(data['train']['X'], data['train']['y'])
   preds = reg.predict(data['test']['X'])
   print('Mean Squared Error is', mean_squared_error(preds, data['test']['y']))
   joblib.dump(value = reg, filename = 'model.pkl');
   ```

2. 使用 Azure 机器学习服务 SDK 添加试验跟踪并将持久化模型上传到试验运行记录。 以下代码添加标记、日志，并将模型文件上传到试验运行。

   ```python
   # Get an experiment object from Azure Machine Learning
   experiment = Experiment(workspace = ws, name = "train-within-notebook")
  
   # Create a run object in the experiment
   run = experiment.start_logging()# Log the algorithm parameter alpha to the run
   run.log('alpha', 0.03)

   # Create, fit, and test the scikit-learn Ridge regression model
   regression_model = Ridge(alpha=0.03)
   regression_model.fit(data['train']['X'], data['train']['y'])
   preds = regression_model.predict(data['test']['X'])

   # Output the Mean Squared Error to the notebook and to the run
   print('Mean Squared Error is', mean_squared_error(data['test']['y'], preds))
   run.log('mse', mean_squared_error(data['test']['y'], preds))

   # Save the model to the outputs directory for capture
   joblib.dump(value=regression_model, filename='outputs/model.pkl')

   # Take a snapshot of the directory containing this notebook
   run.take_snapshot('./')

   # Complete the run
   run.complete()
  
   ```

脚本以 ```run.complete()``` 结束，将运行标记为已完成。  此函数通常用于交互式 Notebook 方案。

## <a name="option-2-use-scriptrunconfig"></a>选项 2：使用 ScriptRunConfig

[**ScriptRunConfig** ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py)设置的脚本配置运行的是一个类。 使用此选项，可添加监视代码，在运行完成时发出通知，或让视觉小组件执行监视操作。

此示例在上面的基本 sklearn 岭模型的基础上进行扩展。 它会对模型的 alpha 值执行简单的参数扫描以捕获指标，并通过在实验中运行来训练模型。 该示例在一个用户管理的环境中执行本地运行。 

1. 创建定型脚本 `train.py`。

   ```python
   # train.py

   import os
   from sklearn.datasets import load_diabetes
   from sklearn.linear_model import Ridge
   from sklearn.metrics import mean_squared_error
   from sklearn.model_selection import train_test_split
   from azureml.core.run import Run
   from sklearn.externals import joblib

   import numpy as np

   #os.makedirs('./outputs', exist_ok = True)

   X, y = load_diabetes(return_X_y = True)

   run = Run.get_context()

   X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state = 0)
   data = {"train": {"X": X_train, "y": y_train},
          "test": {"X": X_test, "y": y_test}}

   # list of numbers from 0.0 to 1.0 with a 0.05 interval
   alphas = mylib.get_alphas()

   for alpha in alphas:
      # Use Ridge algorithm to create a regression model
      reg = Ridge(alpha = alpha)
      reg.fit(data["train"]["X"], data["train"]["y"])

      preds = reg.predict(data["test"]["X"])
      mse = mean_squared_error(preds, data["test"]["y"])
      # log the alpha and mse values
      run.log('alpha', alpha)
      run.log('mse', mse)

      model_file_name = 'ridge_{0:.2f}.pkl'.format(alpha)
      # save model in the outputs folder so it automatically get uploaded
      with open(model_file_name, "wb") as file:
          joblib.dump(value = reg, filename = model_file_name)

      # upload the model file explicitly into artifacts 
      run.upload_file(name = model_file_name, path_or_stream = model_file_name)

      # register the model
      #run.register_model(file_name = model_file_name)

      print('alpha is {0:.2f}, and mse is {1:0.2f}'.format(alpha, mse))
  
   ```

2. `train.py` 脚本引用 `mylib.py`，通过后者，可获取要在岭模型中使用的 alpha 值的列表。

   ```python
   # mylib.py
  
   import numpy as np

   def get_alphas():
      # list of numbers from 0.0 to 1.0 with a 0.05 interval
      return np.arange(0.0, 1.0, 0.05)
   ```

3. 配置用户管理的本地环境。

   ```python
   from azureml.core.runconfig import RunConfiguration

   # Editing a run configuration property on-fly.
   run_config_user_managed = RunConfiguration()

   run_config_user_managed.environment.python.user_managed_dependencies = True

   # You can choose a specific Python environment by pointing to a Python path 
   #run_config.environment.python.interpreter_path = '/home/user/miniconda3/envs/sdk2/bin/python'
   ```

4. 提交要在用户管理的环境中运行的 ```train.py``` 脚本。 整个脚本文件夹都要提交以进行训练，包括 ```mylib.py``` 文件。

   ```python
   from azureml.core import ScriptRunConfig
  
   experiment = Experiment(workspace=ws, name="train-on-local")
   src = ScriptRunConfig(source_directory = './', script = 'train.py', run_config = run_config_user_managed)
   run = experiment.submit(src)
   ```

## <a name="manage-a-run"></a>管理运行

[启动、 监视和取消训练运行](how-to-manage-runs.md)本文重点说明如何管理在实验的特定 Azure 机器学习工作流。

## <a name="view-run-details"></a>查看运行详细信息

### <a name="monitor-run-with-jupyter-notebook-widgets"></a>使用 Jupyter 笔记本小组件监视运行
使用 ScriptRunConfig 方法提交运行时，可使用 Jupyter 笔记本小组件监视运行的进度。 和运行提交一样，该小组件采用异步方式，并每隔 10-15 秒提供实时更新，直到作业完成。

1. 在等待运行完成的期间查看 Jupyter 小组件。

   ```python
   from azureml.widgets import RunDetails
   RunDetails(run).show()
   ```

   ![Jupyter 笔记本小组件的屏幕截图](./media/how-to-track-experiments/widgets.PNG)

2. **[适用于自动化机器学习运行]** 从以前的运行访问图表。 替换为`<<experiment_name>>`具有相应试验名称：

   ``` 
   from azureml.widgets import RunDetails
   from azureml.core.run import Run

   experiment = Experiment (workspace, <<experiment_name>>)
   run_id = 'autoML_my_runID' #replace with run_ID
   run = Run(experiment, run_id)
   RunDetails(run).show()
   ```

   ![自动化机器学习的 Jupyter Notebook 小组件](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)


若要查看某个管道的其他详细信息，请在表中单击要探索的管道，随后，图表将在 Azure 门户上的弹出窗口中呈现。

### <a name="get-log-results-upon-completion"></a>完成时获取日志结果

模型定型和监视在后台进行，以便在等待时可运行其他任务。 也可以先耐心等待模型完成定型，然后再运行其它代码。 使用 ScriptRunConfig 时，可以使用 ```run.wait_for_completion(show_output = True)``` 在模型定型完成时进行显示。 使用 ```show_output``` 标志可查看详细输出。 
  
### <a name="query-run-metrics"></a>查询运行指标

可以使用 ```run.get_metrics()``` 查看训练的模型的指标。 现在可以获取上面示例中记录的所有指标以确定最佳模型。

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-the-experiment-in-the-azure-portal"></a>在 Azure 门户中查看实验

当实验完成运行时，可浏览到试验运行记录。 可通过两种方法访问历史记录：

* 直接获取运行 URL ```print(run.get_portal_url())```
* 通过提交运行名称来查看运行详细信息（在此示例中为 ```run```）。 此方法可以查看试验名称、ID、类型、状态、详细信息页、Azure 门户链接和文档链接。

点击运行链接可直接转到 Azure 门户中的运行详细信息页面。 在这里可查看试验中记录的任何属性、跟踪的指标、图像和图表。 在本例中，记录了 MSE 和 alpha 值。

  ![Azure 门户中的运行详细信息](./media/how-to-track-experiments/run-details-page-web.PNG)

还可查看运行的任何输出或日志，或下载提交的试验的快照，以便与他人共享试验文件夹。

### <a name="viewing-charts-in-run-details"></a>在运行详细信息中查看图表

可通过多种方式使用日志记录 API 在运行期间记录不同类型的指标，然后在 Azure 门户中以图表形式查看这些指标。 

|记录的值|示例代码| 在门户中查看|
|----|----|----|
|记录一组数值| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|单变量折线图|
|使用重复使用的相同指标名称记录单个数值（例如在 for 循环中）| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| 单变量折线图|
|重复记录包含 2 个数字列的行|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|双变量折线图|
|记录包含 2 个数字列的表|`run.log_table(name='Sine Wave', value=sines)`|双变量折线图|

<a name="auto"></a>
## <a name="understanding-automated-ml-charts"></a>了解自动化机器学习图表

在 Notebook 中提交自动化机器学习作业后，可以在机器学习服务工作区中找到这些运行的历史记录。 

了解有关以下方面的详细信息：
+ [分类模型的图表和曲线](#classification)
+ [回归模型的图表和图形](#regression)
+ [模型解释功能](#model-explain-ability-and-feature-importance)


### <a name="view-the-run-charts"></a>查看运行图表

1. 转到你的工作区。 

1. 在工作区的最左侧面板中选择“试验”。

   ![试验菜单的屏幕截图](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment_menu.PNG)

1. 选择所需的试验。

   ![试验列表](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment_list.PNG)

1. 在表中选择“运行编号”。

   ![试验运行](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment_run.PNG)

1. 在表中，选择要进一步探索的模型的“迭代编号”。

   ![试验模型](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment_model.PNG)



### <a name="classification"></a>分类

对于使用 Azure 机器学习的自动化机器学习功能生成的每个分类模型，可以查看以下图表： 
+ [混淆矩阵](#confusion-matrix)
+ [精度-召回率图表](#precision-recall-chart)
+ [接收方操作特征 (ROC)](#roc)
+ [提升曲线](#lift-curve)
+ [增益曲线](#gains-curve)
+ [校准图](#calibration-plot)

#### <a name="confusion-matrix"></a>混淆矩阵

混淆矩阵用于描述分类模型的性能。 每一行显示真实类的实例，每一列表示预测类的实例。 混淆矩阵显示给定模型的正确分类标签和错误分类标签。

对于分类问题，Azure 机器学习会自动为生成的每个模型提供一个混淆矩阵。 对于每个混淆矩阵，自动化机器学习将以绿色显示正确分类的标签，以红色显示错误分类的标签。 圆的大小表示该箱中的样本数。 此外，相邻的条形图中会提供每个预测标签和每个真实标签的频率计数。 

示例 1：准确度不佳的分类模型![准确度不佳的分类模型](./media/how-to-track-experiments/azure-machine-learning-auto-ml-confusion_matrix1.PNG)

示例 2：准确度较高的分类模型（理想状态）![准确度较高的分类模型](./media/how-to-track-experiments/azure-machine-learning-auto-ml-confusion_matrix2.PNG)


#### <a name="precision-recall-chart"></a>精度-召回率图表

使用此图表可以比较每个模型的精度-召回率曲线，以确定哪个模型的精度与召回率关系可接受，可以解决特定的业务问题。 此图表显示宏观平均精度-召回率、微观平均精度-召回率，以及与模型的所有类关联的精度-召回率。

术语“精度”表示分类器正确标记所有实例的能力。 “召回率”表示分类器查找特定标签的所有实例的能力。 精度-召回率曲线显示这两个概念之间的关系。 在理想情况下，模型具有 100% 的精度和 100% 的准确度。

示例 1：精度和召回率较低的分类模型![精度和召回率较低的分类模型](./media/how-to-track-experiments/azure-machine-learning-auto-ml-precision_recall1.PNG)

示例 2：精度和召回率大约为 100% 的分类模型（理想状态）![精度和召回率较高的分类模型](./media/how-to-track-experiments/azure-machine-learning-auto-ml-precision_recall2.PNG)

#### <a name="roc"></a>ROC

接收方操作特征 (ROC) 是特定模型的正确分类标签与错误分类标签的对比图。 以较高的偏差基于数据集训练模型时，ROC 曲线提供的信息可能较少，因为它不会显示误报标签。

示例 1：真实标签较少、虚假标签较多的分类模型![真实标签较少、虚假标签较多的分类模型](./media/how-to-track-experiments/azure-machine-learning-auto-ml-roc1.PNG)

示例 2：真实标签较多、虚假标签较少的分类模型![真实标签较多、虚假标签较少的分类模型](./media/how-to-track-experiments/azure-machine-learning-auto-ml-roc2.PNG)

#### <a name="lift-curve"></a>提升曲线

可以根据基线比较 Azure 机器学习自动生成的模型的性能提升，以查看该特定模型的值增益。

提升图用于评估分类模型的性能。 它显示使用模型比不使用模型预期能够将性能改善多少。 

示例 1：模型的性能比随机选择模型更差![性能比随机选择模型更差的分类模型](./media/how-to-track-experiments/azure-machine-learning-auto-ml-lift_curve1.PNG)

示例 2：模型的性能比随机选择模型更好![性能更好的分类模型](./media/how-to-track-experiments/azure-machine-learning-auto-ml-lift_curve2.PNG)

#### <a name="gains-curve"></a>增益曲线

增益图按每个数据部分评估分类模型的性能。 它针对数据集的每个百分位，显示性能预期要比随机选择模型改善多少。

借助累积增益图，可以使用一个对应于模型所需增益的百分比来选择分类截止值。 此信息提供了查看随附提升图中的结果的另一种方式。

示例 1：增益极低的分类模型![增益极低的分类模型](./media/how-to-track-experiments/azure-machine-learning-auto-ml-gains_curve1.PNG)

示例 2：增益极高的分类模型![增益极高的分类模型](./media/how-to-track-experiments/azure-machine-learning-auto-ml-gains_curve2.PNG)

#### <a name="calibration-plot"></a>校准图

对于所有分类问题，可以查看微观平均、宏观平均以及给定预测模型中每个类的校准行。 

校准图用于显示预测模型的置信度。 为此，它会显示预测概率与实际概率之间的关系。其中，“概率”表示特定实例属于某个标签的可能性。 适当校准的模型与 y=x 行对齐，表示它在预测中具有合理的置信度。 置信度过高的模型与 y=0 行对齐，其中会显示预测概率，但不显示实际概率。

示例 1：适当校准的模型![适当校准的模型](./media/how-to-track-experiments/azure-machine-learning-auto-ml-calib_curve1.PNG)

示例 2：置信度过高的模型![置信度过高的模型](./media/how-to-track-experiments/azure-machine-learning-auto-ml-calib_curve2.PNG)

### <a name="regression"></a>回归
对于使用 Azure 机器学习的自动化机器学习功能生成的每个回归模型，可以查看以下图表： 
+ [预测与真实](#pvt)
+ [残差直方图](#histo)

<a name="pvt"></a>

#### <a name="predicted-vs-true"></a>预测与True

预测与“真实”显示回归问题的预测值与其相关真实值之间的关系。 可以使用此图形来衡量模型的性能，因为预测值与 y=x 行越接近，预测模型的准确度就越高。

每次运行后，可以查看每个回归模型的预测与真实图形。 为了保护数据隐私，值已装箱在一起，每个箱的大小在图表区域的下半部分显示为条形图。 可将预测模型（带有浅色阴影，其中显示了误差边际）与模型的理想值进行比较。

示例 1：预测准确度较低的回归模型![预测准确度较低的回归模型](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression1.PNG)

示例 2：预测准确度较高的回归模型![预测准确度较高的回归模型](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression2.PNG)

<a name="histo"></a>

#### <a name="histogram-of-residuals"></a>残差直方图

残差表示观测到的 y - 预测的 y。 若要显示偏差较小的误差边际，应该以 0 为中心，将残差直方图绘制成钟形曲线。 

示例 1：误差中带有偏差的回归模型![误差中带有偏差的 SA 回归模型](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression3.PNG)

示例 2：误差更均匀分布的回归模型![误差更均匀分布的回归模型](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression4.PNG)

### <a name="model-explain-ability-and-feature-importance"></a>模型解释功能和特征重要性

特征重要性提供一个评分用于指示模型构造中每个特征的价值大小。 可以查看整个模型的特征重要性评分，也可以查看预测模型中每个类的该评分。 可以查看每个特征的重要性如何对每个类和整个模型进行比较。

![模型解释功能](./media/how-to-track-experiments/azure-machine-learning-auto-ml-feature_explain1.PNG)

## <a name="example-notebooks"></a>示例笔记本
下面的笔记本展示了本文中的概念：
* [how-to-use-azureml/training/train-within-notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook)
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/training/logging-api/logging-api.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>后续步骤

尝试执行以下后续步骤，了解如何将 Azure 机器学习 SDK 用于 Python：

* 查看教程[使用 Azure 机器学习训练图像分类模型](tutorial-train-models-with-aml.md)中的示例，了解如何注册和部署最佳模型。

* 了解如何[使用 Azure 机器学习训练 PyTorch 模型](how-to-train-pytorch.md)。
