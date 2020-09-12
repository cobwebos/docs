---
title: 监视和查看 ML 运行日志与指标
titleSuffix: Azure Machine Learning
description: 监视 Azure ML 试验并查看运行指标，以便改进模型创建过程。 使用小组件和工作室门户来浏览运行状态并查看运行记录。
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 642186ef4af43127ba0d6eb6950f09e069eb79ca
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2020
ms.locfileid: "89649281"
---
# <a name="monitor-and-view-ml-run-logs-and-metrics"></a>监视和查看 ML 运行日志与指标

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文介绍如何监视 Azure 机器学习运行并查看其日志。 在查看日志之前，必须先启用它们。 有关详细信息，请参阅[在 Azure 机器学习训练运行中启用日志记录](how-to-track-experiments.md)。

日志可帮助你诊断错误和警告，或跟踪参数和模型准确性等性能指标。 本文介绍如何使用以下方法查看日志：

> [!div class="checklist"]
> * 在工作室中监视运行
> * 使用 Jupyter Notebook 小组件监视运行
> * 监视自动化机器学习运行
> * 完成时查看输出日志
> * 在工作室中查看输出日志

有关如何管理试验的常规信息，请参阅[启动、监视和取消训练运行](how-to-manage-runs.md)。

## <a name="monitor-runs-in-the-studio"></a>在工作室中监视运行

若要在浏览器中监视特定计算目标的运行，请执行以下步骤：

1. 在 [Azure 机器学习工作室](https://ml.azure.com/)中选择自己的工作区，然后在页面左侧选择“计算”。

1. 选择“正在训练群集”，显示用于训练的计算目标列表。 然后选择群集。

    ![选择训练群集](./media/how-to-track-experiments/select-training-compute.png)

1. 选择“运行”。 此时显示使用此群集的运行列表。 若要查看某个特定运行的详细信息，请点击“运行”列中的链接。 若要查看试验的详细信息，请点击“试验”列中的链接。

    ![选择训练群集的运行](./media/how-to-track-experiments/show-runs-for-compute.png)
    
    > [!TIP]
    > 由于训练计算目标是共享资源，因此它们可以让多个运行排队或在给定时间处于活动状态。
    > 
    > 一个运行可以包含多个子级运行，所以一个训练作业可能会产生多个条目。

完成的运行将不再显示在此页上。 若要查看已完成运行的信息，请访问工作室的“试验”部分，然后选择试验和运行。 有关详细信息，请参阅[查看已完成运行的指标](#view-the-experiment-in-the-web-portal)部分。

## <a name="monitor-runs-using-the-jupyter-notebook-widget"></a>使用 Jupyter Notebook 小组件监视运行

使用 ScriptRunConfig 方法提交运行时，可使用 [Jupyter 小组件](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py&preserve-view=true)监视运行的进度。 和运行提交一样，该小组件采用异步方式，并每隔 10-15 秒提供实时更新，直到作业完成。

在等待运行完成的期间查看 Jupyter 小组件。
    
```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

![Jupyter 笔记本小组件的屏幕截图](./media/how-to-track-experiments/run-details-widget.png)

也可以在工作区中找到指向此画面的链接。

```python
print(run.get_portal_url())
```

## <a name="monitor-automated-machine-learning-runs"></a>监视自动化机器学习运行

对于自动化机器学习运行，若要访问根据以前的运行生成的图表，请将 `<<experiment_name>>` 替换为相应的试验名称：

```python
from azureml.widgets import RunDetails
from azureml.core.run import Run

experiment = Experiment (workspace, <<experiment_name>>)
run_id = 'autoML_my_runID' #replace with run_ID
run = Run(experiment, run_id)
RunDetails(run).show()
```

![自动化机器学习的 Jupyter Notebook 小组件](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)

## <a name="show-output-upon-completion"></a>完成时显示输出

使用 ScriptRunConfig 时，可以使用 ```run.wait_for_completion(show_output = True)``` 在模型定型完成时进行显示。 使用 ```show_output``` 标志可查看详细输出。 有关详细信息，请参阅[如何启用日志记录](how-to-track-experiments.md#scriptrunconfig-logs)中的 ScriptRunConfig 部分。

<a id="queryrunmetrics"></a>
## <a name="query-run-metrics"></a>查询运行指标

可以使用 ```run.get_metrics()``` 查看训练的模型的指标。 例如，可以将此方法示例与上面的示例配合使用，通过查找具有最低均方误差 (mse) 值的模型来确定最佳模型。

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-run-records-in-the-studio"></a>在工作室中查看运行记录

可以在 [Azure 机器学习工作室](https://ml.azure.com)中浏览已完成的运行记录，包括记录的指标。

导航到“试验”选项卡并选择自己的试验。 在“试验运行”仪表板中，可以看到为每次运行跟踪的指标和日志。 

向下钻取至特定运行以查看其输出或日志，或下载试验的快照，以便与其他人共享试验文件夹。

还可以编辑“运行列表”表，以选择多个运行并显示运行的最新记录值、最小记录值或最大记录值。 自定义自己的图表，以比较多个运行上的已记录指标值和聚合。

![Azure 机器学习工作室中的运行详细信息](media/how-to-track-experiments/experimentation-tab.gif)

### <a name="format-charts-in-the-studio"></a>设置工作室中图表的格式

使用日志记录 API 中的以下方法可影响将指标可视化的工作室。

|记录的值|示例代码| 门户中的格式|
|----|----|----|
|记录一组数值| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|单变量折线图|
|使用重复使用的相同指标名称记录单个数值（例如在 for 循环中）| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| 单变量折线图|
|重复记录包含 2 个数字列的行|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|双变量折线图|
|记录包含 2 个数字列的表|`run.log_table(name='Sine Wave', value=sines)`|双变量折线图|


## <a name="next-steps"></a>后续步骤

尝试执行以下后续步骤，了解如何使用 Azure 机器学习：

* 了解如何[在 Azure 机器学习设计器（预览版）中跟踪试验并启用日志](how-to-track-designer-experiments.md)。

* 查看教程[使用 Azure 机器学习训练图像分类模型](tutorial-train-models-with-aml.md)中的示例，了解如何注册和部署最佳模型。

