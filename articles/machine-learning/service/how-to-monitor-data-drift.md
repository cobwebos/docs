---
title: 在 AKS 部署上检测数据偏移（预览版）
titleSuffix: Azure Machine Learning
description: 在 Azure 机器学习中检测 Azure Kubernetes Service 部署的模型的数据偏差。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: copeters
author: cody-dkdc
ms.date: 09/13/2019
ms.openlocfilehash: 3b3fbce40c93389037435a7cdb1271e773163de3
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2019
ms.locfileid: "71123271"
---
# <a name="detect-data-drift-preview-on-models-deployed-to-azure-kubernetes-service-aks"></a>在部署到 Azure Kubernetes Service （AKS）的模型上检测数据偏差（预览）

本文介绍如何监视已部署模型的定型数据集和推理数据之间的数据偏移。 在机器学习的上下文中，经过训练的机器学习模型可能会因为偏差而遇到下降的预测性能。 使用 Azure 机器学习，可以监视数据偏移，在检测到偏差时，服务可以向你发送电子邮件警报。

## <a name="what-is-data-drift"></a>什么是数据偏移？

在机器学习的上下文中，数据偏移是模型输入数据中的更改，导致模型性能下降。 这是模型准确性在一段时间内的最常见原因之一，因此，监视数据偏移可帮助检测模型性能问题。 

## <a name="what-can-i-monitor"></a>可以监视哪些内容？

使用 Azure 机器学习，可以监视 AKS 上部署的模型的输入，并将此数据与模型的定型数据集进行比较。 固定时间间隔，推理数据是[快照和分析](how-to-explore-prepare-data.md)的，然后针对基线数据集进行计算，以生成数据偏移分析： 

+ 度量数据偏移量（称为偏移系数）。
+ 按功能测量数据偏移量，并通知哪些功能导致了数据偏移。
+ 度量距离指标。 当前已计算 Wasserstein 和能量距离。
+ 度量功能分布。 当前内核密度估算和直方图。
+ 通过电子邮件将警报发送到数据偏移。

> [!Note]
> 此服务处于 "（预览版）" 和 "配置选项限制"。 有关详细信息和更新，请参阅我们的[API 文档](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/?view=azure-ml-py)和[发行说明](azure-machine-learning-release-notes.md)。 

### <a name="how-data-drift-is-monitored-in-azure-machine-learning"></a>如何监视数据偏移 Azure 机器学习

使用 Azure 机器学习，数据偏移通过数据集或部署来监视。 若要监视数据偏移，请指定基线数据集，这通常是模型的定型数据集。 第二个数据集（通常是从部署中收集的模型输入数据）根据基线数据集进行测试。 这两个数据集都被分析并输入到数据偏移监视服务。 训练机器学习模型来检测两个数据集之间的差异。 模型的性能转换为偏移系数，这会测量两个数据集之间的偏差量。 使用[model interpretability](machine-learning-interpretability-explainability.md)，将计算构成偏移系数的特征。 在数据集配置文件中，将跟踪有关每个功能的统计信息。 

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 如果没有，请在开始之前创建一个免费帐户。 立即试用[Azure 机器学习免费版或付费版](https://aka.ms/AMLFree)。

- 安装了适用于 Python 的 Azure 机器学习 SDK。 按照 [Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) 中的说明执行以下操作：

    - 创建 Miniconda 环境
    - 安装用于 Python 的 Azure 机器学习 SDK

- [Azure 机器学习工作区](how-to-manage-workspace.md)。

- 工作区[配置文件](how-to-configure-environment.md#workspace)。

- 使用以下命令安装数据偏移 SDK：

    ```shell
    pip install azureml-contrib-datadrift
    ```

- 从模型的定型数据创建[数据集](how-to-create-register-datasets.md)。

- [注册](concept-model-management-and-deployment.md)模型时指定定型数据集。 下面的示例演示如何使用`datasets`参数指定定型数据集：

    ```python
    model = Model.register(model_path=model_file,
                        model_name=model_name,
                        workspace=ws,
                        datasets=datasets)

    print(model_name, image_name, service_name, model)
    ```

- [启用模型数据收集](how-to-enable-data-collection.md)，从模型的 AKS 部署收集数据，并确认在`modeldata` blob 容器中收集数据。

## <a name="configure-data-drift"></a>配置数据偏移
若要为试验配置数据偏移，请导入依赖项，如以下 Python 示例中所示。 

此示例演示如何配置[`DataDriftDetector`](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift.datadriftdetector.datadriftdetector?view=azure-ml-py)对象：

```python
# Import Azure ML packages
from azureml.core import Experiment, Run, RunDetails
from azureml.contrib.datadrift import DataDriftDetector, AlertConfiguration

# if email address is specified, setup AlertConfiguration
alert_config = AlertConfiguration('your_email@contoso.com')

# create a new DatadriftDetector object
datadrift = DataDriftDetector.create(ws, model.name, model.version, services, frequency="Day", alert_config=alert_config)
    
print('Details of Datadrift Object:\n{}'.format(datadrift))
```

## <a name="submit-a-datadriftdetector-run"></a>提交 DataDriftDetector 运行

在配置`DataDriftDetector`了对象的情况下，你可以在给定日期为模型提交[数据偏移](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift.datadriftdetector%28class%29?view=azure-ml-py#run-target-date--services--compute-target-name-none--create-compute-target-false--feature-list-none--drift-threshold-none-)。 在运行过程中，通过设置`drift_threshold`参数启用 DataDriftDetector 警报。 如果[datadrift_coefficient](#metrics)超出给定`drift_threshold`的，则发送电子邮件。

```python
# adhoc run today
target_date = datetime.today()

# create a new compute - creates datadrift-server
run = datadrift.run(target_date, services, feature_list=feature_list, create_compute_target=True)

# or specify existing compute cluster
run = datadrift.run(target_date, services, feature_list=feature_list, compute_target='cpu-cluster')

# show details of the data drift run
exp = Experiment(ws, datadrift._id)
dd_run = Run(experiment=exp, run_id=run)
RunDetails(dd_run).show()
```

## <a name="visualize-drift-metrics"></a>可视化偏移度量值

<a name="metrics"></a>

提交 DataDriftDetector 运行后，可以在每次运行迭代中看到为数据偏差任务保存的偏差度量值：


|指标|描述|
--|--|
wasserstein_distance|为一维数值分布定义的统计距离。|
energy_distance|为一维数值分布定义的统计距离。|
datadrift_coefficient|计算方式类似于 Matthew 的相关系数，但此输出为介于0到1之间的实数。 在偏差上下文中，0表示无偏差，1表示最大偏移量。|
datadrift_contribution|导致偏移的功能的重要性。|

可以通过多种方式查看偏差指标：

* 使用 Jupyter[小组件。](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) `RunDetails`
* 对任何`datadrift`运行对象使用[函数。`get_metrics()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#get-metrics-name-none--recursive-false--run-type-none--populate-false-)
* 从[工作区登录页](https://ml.azure.com)的 "**模型**" 部分查看指标（预览）。

以下 Python 示例演示如何绘制相关的数据偏差指标。 可以使用返回的度量值生成自定义可视化效果：

```python
# start and end are datetime objects 
drift_metrics = datadrift.get_output(start_time=start, end_time=end)

# Show all data drift result figures, one per serivice.
# If setting with_details is False (by default), only the data drift magnitude will be shown; if it's True, all details will be shown.
drift_figures = datadrift.show(with_details=True)
```

![查看 Azure 机器学习检测到的数据偏移](media/how-to-monitor-data-drift/drift_show.png)


## <a name="schedule-data-drift-scans"></a>计划数据偏移扫描 

启用数据偏移检测时，将按指定的计划频率运行 DataDriftDetector。 如果 datadrift_coefficient 到达给定`drift_threshold`的，则会随每个计划运行发送一封电子邮件。 

```python
datadrift.enable_schedule()
datadrift.disable_schedule()
```

在[工作区登陆页面（预览）](https://ml.azure.com)的 "**详细信息**" 选项卡中的 "**模型**" 下，可以查看数据偏移检测程序的配置。

![Azure 门户数据偏移](media/how-to-monitor-data-drift/drift-config.png)

## <a name="view-results-in-your-workspace-landing-page"></a>在工作区登录页中查看结果

若要在[工作区登录页（预览版）](https://ml.azure.com)中查看工作区中的结果，请导航到 "模型" 页。 在模型的 "详细信息" 选项卡上，将显示数据偏移配置。 现在，**数据偏移**选项卡可用来可视化数据偏差指标。 

[![工作区登录页数据偏移](media/how-to-monitor-data-drift/drift-ui.png)](media/how-to-monitor-data-drift/drift-ui-expanded.png)


## <a name="receiving-drift-alerts"></a>接收偏移警报

通过设置偏移系数警报阈值并提供电子邮件地址，每当偏移系数超过阈值时，就会自动发送[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview)电子邮件警报。 

为了设置自定义警报和操作，所有数据偏差指标都存储在与 Azure 机器学习工作区一起创建的[Application Insights](how-to-enable-app-insights.md)资源中。 可以跟踪电子邮件警报中的链接到 Application Insights 查询。

![数据偏移电子邮件警报](media/how-to-monitor-data-drift/drift_email.png)

## <a name="retrain-your-model-after-drift"></a>偏移后重新训练模型

当数据偏移对已部署的模型的性能产生负面影响时，可以重新训练模型。 为此，请继续执行以下步骤。

* 调查收集的数据并准备数据以对新模型定型。
* 将其拆分为训练/测试数据。
* 使用新数据重新训练模型。
* 评估新生成的模型的性能。
* 如果性能比生产模型好，则部署新的模型。

## <a name="next-steps"></a>后续步骤

* 有关使用数据偏移的完整示例，请参阅[AZURE ML 数据偏移笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/monitor-models/data-drift/azure-ml-datadrift.ipynb)。 本 Jupyter Notebook 演示如何使用[Azure 开放式数据集](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets)来训练模型预测天气、将其部署到 AKS，并监视数据偏移。 

* 随着数据偏移的公开上市，我们将非常感谢你的问题、意见或建议。 使用以下产品反馈按钮！ 
