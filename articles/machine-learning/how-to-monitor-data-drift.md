---
title: 检测 AKS 部署中的数据偏移
titleSuffix: Azure Machine Learning
description: 在 Azure 机器学习中检测 Azure Kubernetes 服务部署的模型上的数据偏移（预览版）。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: copeters
author: cody-dkdc
ms.date: 11/04/2019
ms.openlocfilehash: d1da7309b296b57db0c28d5b52fe91efa86709c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75537001"
---
# <a name="detect-data-drift-preview-on-models-deployed-to-azure-kubernetes-service-aks"></a>在部署到 Azure Kubernetes 服务 (AKS) 的模型中检测数据偏移（预览版）
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

本文介绍如何监视已部署模型的训练数据集与推理数据之间的数据偏移。 在机器学习的上下文中，已训练的机器学习模型可能会由于偏移而遇到预测性能下降的问题。 使用 Azure 机器学习可以监视数据偏移，检测到偏移时，该服务可以发送电子邮件警报。

## <a name="what-is-data-drift"></a>什么是数据偏移？

在机器学习的上下文中，数据偏移是指模型输入数据中导致模型性能下降的变化。 这是模型准确度在一段时间后下降的最主要原因之一，因此，监视数据偏移有助于检测模型性能问题。 

## <a name="what-can-i-monitor"></a>可以监视哪些内容？

使用 Azure 机器学习可以监视 AKS 中部署的模型的输入，并将此数据与模型的训练数据集进行比较。 推理数据将按固定的时间间隔[生成快照和经过分析](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py)，然后根据基线数据集经过计算，以生成数据偏移分析来实现以下目的： 

+ 度量数据偏移幅度（称为偏移系数）。
+ 按特征度量数据偏移贡献，指示数据偏移是由哪些特征导致的。
+ 度量距离指标。 当前已计算 Wasserstein 和能量距离。
+ 度量特征分布。 当前的内核密度评估和直方图。
+ 通过电子邮件发送数据偏移警报。

> [!Note]
> 此服务目前为预览版，在配置选项方面存在限制。 有关详细信息和更新，请参阅我们的 [API 文档](https://docs.microsoft.com/python/api/azureml-datadrift/)和[发行说明](azure-machine-learning-release-notes.md)。 

### <a name="how-data-drift-is-monitored-in-azure-machine-learning"></a>如何在 Azure 机器学习中监视数据偏移

使用 Azure 机器学习通过数据集或部署监视数据偏移。 若要监视数据偏移，需要指定一个基线数据集 - 通常是模型的训练数据集。 另一个数据集 - 通常是从部署收集的模型输入数据 - 根据基线数据集进行测试。 这两个数据集都将经过分析并输入到数据偏移监视服务。 将训练某个机器学习模型来检测两个数据集之间的差异。 模型的性能转换为偏移系数，用于度量两个数据集之间的偏移幅度。 使用[模型可解释性](how-to-machine-learning-interpretability.md)计算构成偏移系数的特征。 通过数据集配置文件跟踪有关每个特征的统计信息。 

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 如果没有订阅，请在开始之前创建一个免费帐户。 立即试用[免费版或付费版 Azure 机器学习](https://aka.ms/AMLFree)。

- 安装了适用于 Python 的 Azure 机器学习 SDK。 按照 [Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) 中的说明执行以下操作：

    - 创建 Miniconda 环境
    - 安装用于 Python 的 Azure 机器学习 SDK

- [Azure 机器学习工作区](how-to-manage-workspace.md)。

- 一个工作区[配置文件](how-to-configure-environment.md#workspace)。

- 使用以下命令安装数据偏移 SDK：

    ```shell
    pip install azureml-datadrift
    ```

- 从模型的训练数据创建一个[数据集](how-to-create-register-datasets.md)。

- 在[注册](concept-model-management-and-deployment.md)模型时指定训练数据集。 以下示例演示如何使用 `datasets` 参数指定训练数据集：

    ```python
    model = Model.register(model_path=model_file,
                        model_name=model_name,
                        workspace=ws,
                        datasets=datasets)

    print(model_name, image_name, service_name, model)
    ```

- [启用模型数据收集](how-to-enable-data-collection.md)从模型的 AKS 部署收集数据，并确认数据收集到 `modeldata` Blob 容器中。

## <a name="configure-data-drift"></a>配置数据偏移
若要配置试验的数据偏移，请导入以下 Python 示例中所示的依赖项。 

此示例演示配置[`DataDriftDetector`](/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector)对象：

```python
# Import Azure ML packages
from azureml.core import Experiment, Run, RunDetails
from azureml.datadrift import DataDriftDetector, AlertConfiguration

# if email address is specified, setup AlertConfiguration
alert_config = AlertConfiguration('your_email@contoso.com')

# create a new DataDriftDetector object
datadrift = DataDriftDetector.create(ws, model.name, model.version, services, frequency="Day", alert_config=alert_config)
    
print('Details of Datadrift Object:\n{}'.format(datadrift))
```

## <a name="submit-a-datadriftdetector-run"></a>提交 DataDriftDetector 运行

配置 `DataDriftDetector` 对象后，可以在模型的给定日期提交[数据偏移运行](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector#run-target-date--services-none--compute-target-none--create-compute-target-false--feature-list-none--drift-threshold-none-)。 在运行过程中，通过设置 `drift_threshold` 参数启用 DataDriftDetector 警报。 如果 [datadrift_coefficient](#visualize-drift-metrics) 超过给定的 `drift_threshold`，将发送电子邮件。

```python
# adhoc run today
target_date = datetime.today()

# create a new compute - creates datadrift-server
run = datadrift.run(target_date, services, feature_list=feature_list, create_compute_target=True)

# or specify existing compute cluster
run = datadrift.run(target_date, services, feature_list=feature_list, compute_target='cpu-cluster')

# show details of the data drift run
exp = Experiment(ws, datadrift._id)
dd_run = Run(experiment=exp, run_id=run.id)
RunDetails(dd_run).show()
```

## <a name="visualize-drift-metrics"></a>可视化偏移指标

<a name="metrics"></a>

提交 DataDriftDetector 运行后，可以看到在数据偏移任务的每次运行迭代中保存的偏移指标：


|指标|描述|
--|--|
wasserstein_distance|为一维数字分布定义的统计距离。|
energy_distance|为一维数字分布定义的统计距离。|
datadrift_coefficient|计算方式类似于 Matthew 相关系数，但此输出是 0 到 1 的实数。 在偏移上下文中，0 表示无偏移，1 表示最大偏移。|
datadrift_contribution|导致偏移的特征的重要性。|

可通过多种方式查看偏移指标：

* 使用 `RunDetails`[Jupyter 小组件](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py)。
* 在任何[`get_metrics()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#get-metrics-name-none--recursive-false--run-type-none--populate-false-)`datadrift`运行对象上使用函数。
* 在 [Azure 机器学习工作室](https://ml.azure.com)中工作区的“模型”部分查看指标。****

以下 Python 示例演示如何绘制相关数据偏移指标的图形。 可以使用返回的指标生成自定义可视化效果：

```python
# start and end are datetime objects 
drift_metrics = datadrift.get_output(start_time=start, end_time=end)

# Show all data drift result figures, one per service.
# If setting with_details is False (by default), only the data drift magnitude will be shown; if it's True, all details will be shown.
drift_figures = datadrift.show(with_details=True)
```

![查看 Azure 机器学习检测到的数据偏移](./media/how-to-monitor-data-drift/drift_show.png)


## <a name="schedule-data-drift-scans"></a>计划数据偏移扫描 

启用数据偏移检测后，将按指定的计划频率运行 DataDriftDetector。 如果 datadrift_coefficient 达到给定的 `drift_threshold`，每个按计划运行时都会发送电子邮件。 

```python
datadrift.enable_schedule()
datadrift.disable_schedule()
```

在 [Azure 机器学习工作室](https://ml.azure.com)中工作区的“详细信息”选项卡上的“模型”下，可以看到数据偏移检测器的配置。********

[![Azure 机器学习工作室数据漂移](./media/how-to-monitor-data-drift/drift-config.png)](media/how-to-monitor-data-drift/drift-config-expanded.png)

## <a name="view-results-in-your-azure-machine-learning-studio"></a>在 Azure 机器学习工作室中查看结果

若要在 [Azure 机器学习工作室](https://ml.azure.com)的工作区中查看结果，请导航到模型页。 在模型的详细信息选项卡上，将显示数据偏移配置。 现在，可以使用“数据偏移”选项卡可视化数据偏移指标。**** 

[![Azure 机器学习工作室数据漂移](./media/how-to-monitor-data-drift/drift-ui.png)](media/how-to-monitor-data-drift/drift-ui-expanded.png)

## <a name="receiving-drift-alerts"></a>接收偏移警报

设置偏移系数警报阈值并提供电子邮件地址后，每当偏移系数超过阈值时，就会自动发送 [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) 电子邮件警报。 

为了让你设置自定义警报和操作，所有数据偏移指标已存储在连同 Azure 机器学习工作区一起创建的 [Application Insights](how-to-enable-app-insights.md) 资源中。 可以按照电子邮件警报中 Application Insights 查询的链接进行操作。

![数据偏移电子邮件警报](./media/how-to-monitor-data-drift/drift_email.png)

## <a name="retrain-your-model-after-drift"></a>偏移后重新训练模型

当数据偏移对部署的模型性能造成负面影响时，可以重新训练模型。 为此，请继续执行以下步骤。

* 调查收集的数据，并准备数据以训练新模型。
* 将数据拆分为训练/测试数据。
* 使用新数据再次训练模型。
* 评估新生成的模型的性能。
* 如果性能优于生产模型，则部署新模型。

## <a name="next-steps"></a>后续步骤

* 有关使用数据偏移的完整示例，请参阅 [Azure ML 数据偏移笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/monitor-models/data-drift/drift-on-aks.ipynb)。 此 Jupyter Notebook 演示如何使用 [Azure 开放数据集](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets)来训练某个模型以预测天气，将模型部署到 AKS，并监视数据偏移。 

* 使用[数据集监视器](how-to-monitor-datasets.md)检测数据偏移。

* 在数据偏移推出正式版的过程中，我们感谢各位提出问题、意见或建议。 请使用下面的产品反馈按钮！ 
