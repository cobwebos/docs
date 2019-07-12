---
title: 检测到 AKS 部署数据偏差 （预览版）
titleSuffix: Azure Machine Learning service
description: 检测数据偏差 Azure Kubernetes 服务上的部署 Azure 机器学习服务中的模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: copeters
author: cody-dkdc
ms.date: 07/08/2019
ms.openlocfilehash: 3b8152bde8b7e44dde1b0b9c82216333778f83da
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67806021"
---
# <a name="detect-data-drift-preview-on-models-deployed-to-azure-kubernetes-service-aks"></a>检测数据偏差 （预览版） 部署到 Azure Kubernetes 服务 (AKS) 的模型

在本文中，您将学习如何监视的数据训练数据集和推理之间的偏差的已部署的模型的数据。 在机器学习的上下文中，定型的机器学习模型可能会遇到由于偏差的降级的预测性能。 使用 Azure 机器学习服务时，你可以监视数据偏差和检测到偏差时该服务可以将电子邮件警报发送到您。

## <a name="what-is-data-drift"></a>数据偏差是什么？

不同于用来训练该模型的数据提供给在生产环境中的模型的数据时，会发生数据偏差。 它是主要理由之一，模型准确性会随着时间推移，降低偏差因此监视数据有助于检测模型的性能问题。 

## <a name="what-can-i-monitor"></a>我可以监视什么？

使用 Azure 机器学习服务时，可以监视在 AKS 上部署的模型的输入和此数据与模型的定型数据集进行比较。 推断数据是按固定间隔[快照和分析](how-to-explore-prepare-data.md)，然后对要生成的数据偏移分析的基准数据集计算的： 

+ 测量数据偏差，称为决定系数偏移量。
+ 度量值数据的功能，告知哪些功能导致数据偏差偏差的发布内容。
+ 度量值距离度量值。 当前 Wasserstein 和能源距离进行计算。
+ 度量值的功能的分发。 当前内核密度估计和直方图。
+ 发送数据警报通过电子邮件偏差。

> [!Note]
> 此服务为 （预览版） 和配置选项的限制。 请参阅我们[API 文档](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/?view=azure-ml-py)并[发行说明](azure-machine-learning-release-notes.md)有关详细信息和更新。 

### <a name="how-data-drift-is-monitored-in-azure-machine-learning-service"></a>如何在 Azure 机器学习服务中监视数据偏差

使用 Azure 机器学习服务，将通过数据集或部署监视数据偏差。 若要监视数据偏差，请指定基准数据集-通常为训练数据集的模型的。 针对基准数据集，第二个数据集-部署-从收集模型输入的数据通常是进行测试。 这两个数据集[分析](how-to-explore-prepare-data.md#explore-with-summary-statistics)和输入数据偏差监视服务。 机器学习模型进行训练，以检测两个数据集之间的差异。 模型的性能将转换为偏移系数，测量两个数据集之间的偏移量。 使用[模型 interpretability](machine-learning-interpretability-explainability.md)，计算偏移系数参与的功能。 从数据集配置文件，跟踪每个功能有关的统计信息。 

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 如果你没有帐户，请在开始之前创建一个免费帐户。 立即试用 [Azure 机器学习服务免费版或付费版](https://aka.ms/AMLFree)。

- 已安装 Azure 机器学习服务工作区，以及适用于 Python 的 Azure 机器学习 SDK。 按照[创建 Azure 机器学习服务工作区](setup-create-workspace.md#sdk)中的说明执行以下操作：

    - 创建 Miniconda 环境
    - 安装用于 Python 的 Azure 机器学习 SDK
    - 创建工作区
    - 编写工作区配置文件 (aml_config/config.json)。

- 安装数据偏差 SDK 使用以下命令：

    ```shell
    pip install azureml-contrib-datadrift
    ```

- 创建[数据集](how-to-create-register-datasets.md)从您的模型的定型数据。

- 指定定型数据集时[注册](concept-model-management-and-deployment.md)模型。 下面的示例演示了如何使用`datasets`参数来指定定型数据集：

    ```python
    model = Model.register(model_path=model_file,
                        model_name=model_name,
                        workspace=ws,
                        datasets=datasets)

    print(model_name, image_name, service_name, model)
    ```

- [启用模型数据收集](how-to-enable-data-collection.md)从 AKS 部署模型中收集数据并确认数据要收集在`modeldata`blob 容器。

## <a name="configure-data-drift"></a>配置数据偏差
若要配置的实验数据偏差，导入依赖项，如下面的 Python 示例中所示。 

此示例演示如何配置[ `DataDriftDetector` ](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift.datadriftdetector.datadriftdetector?view=azure-ml-py)对象：

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

与`DataDriftDetector`配置的对象，您可以提交[运行的数据偏移](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift.datadriftdetector%28class%29?view=azure-ml-py#run-target-date--services--compute-target-name-none--create-compute-target-false--feature-list-none--drift-threshold-none-)在给定日期的模型。 在运行，通过设置启用 DataDriftDetector 警报`drift_threshold`参数。 如果[datadrift_coefficient](#metrics)高于给定`drift_threshold`，发送一封电子邮件。

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

## <a name="visualize-drift-metrics"></a>可视化偏差指标

<a name="metrics"></a>

提交你 DataDriftDetector 运行后，你就能够看到数据偏差任务每个运行迭代中保存的偏差指标：


|指标|描述|
--|--|
wasserstein_distance|定义为一维数值的分布统计的距离。|
energy_distance|定义为一维数值的分布统计的距离。|
datadrift_coefficient|计算方式相似 Matthew 的相关系数，但此输出是一个实数，范围从 0 到 1。 在上下文中的偏差，0 表示无偏差，而 1 表示最大偏差。|
datadrift_contribution|功能的功能用于偏差的重要性。|

有多种方法来查看偏移的度量值：

* 使用 Jupyter 小组件。
* 使用[ `get_metrics()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#get-metrics-name-none--recursive-false--run-type-none--populate-false-)任何函数`datadrift`运行对象。
* 在您的模型在 Azure 门户中查看的度量值

下面的 Python 示例演示了如何绘制相关数据偏差度量值。 返回度量值可用于生成自定义可视化效果：

```python
# start and end are datetime objects 
drift_metrics = datadrift.get_output(start_time=start, end_time=end)

# Show all data drift result figures, one per serivice.
# If setting with_details is False (by default), only the data drift magnitude will be shown; if it's True, all details will be shown.
drift_figures = datadrift.show(with_details=True)
```

![请参阅 Azure 机器学习检测到的数据偏差](media/how-to-monitor-data-drift/drift_show.png)


## <a name="schedule-data-drift-scans"></a>计划数据偏差扫描 

启用数据偏差检测，DataDriftDetector 运行指定的计划的频率。 如果 datadrift_coefficient 达到给定`drift_threshold`，每次计划运行发送一封电子邮件。 

```python
datadrift.enable_schedule()
datadrift.disable_schedule()
```

在 Azure 门户中的模型的详细信息页上，可以查看数据偏差检测程序的配置。

![Azure 门户数据偏差配置](media/how-to-monitor-data-drift/drift_config.png)

## <a name="view-results-in-azure-ml-workspace-ui"></a>在 Azure 机器学习工作区 UI 中查看结果

若要在 Azure 机器学习工作区用户界面中查看结果，请导航到模型页。 在该模型的详细信息选项卡上将显示数据偏差配置。 数据偏差 （预览版） 选项卡上现已推出数据偏差指标进行了可视化。 

![Azure 门户数据偏差](media/how-to-monitor-data-drift/drift_ui.png)

## <a name="receiving-drift-alerts"></a>接收偏移警报

设置警报阈值和提供电子邮件地址的偏移系数[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview)偏差系数值高于阈值时，自动发送电子邮件警报。 

为了使你能够设置自定义警报和操作，所有数据偏差指标都存储在[Application Insights](how-to-enable-app-insights.md)以及 Azure 机器学习服务工作区已创建的资源。 你可以遵循 Application Insights 查询中的电子邮件警报的链接。

![数据偏差电子邮件警报](media/how-to-monitor-data-drift/drift_email.png)

## <a name="retrain-your-model-after-drift"></a>偏移后重新训练模型

当数据偏差对已部署的模型的性能造成负面影响时，就可以重新训练模型。 以下[ `diff()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#diff-rhs-dataset--compute-target-none--columns-none-
)方法，初始了解在旧的和新的训练数据集之间更改的内容。 

```python
from azureml.core import Dataset

old_training_dataset.diff(new_training_dataset)
```

根据前面的代码的输出，您可能想要重新训练模型。 为此，请继续执行以下步骤。

* 调查所收集的数据并准备要为新模型定型的数据。
* 将其拆分为训练/测试数据。
* 训练模型时再次使用新数据。
* 评估新生成的模型的性能。
* 如果性能是更好的生产模型比，部署的新模型。

## <a name="next-steps"></a>后续步骤

* 使用数据偏差的完整示例，请参阅[Azure 机器学习数据偏差 notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/data-drift/azure-ml-datadrift.ipynb)。 此 Jupyter Notebook 演示如何使用[Azure 打开数据集](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets)来训练模型来预测天气，将其部署到 AKS，并监视数据偏差。 

* 因为数据偏差会向移动常规可用性，我们极大地不胜感激问题、 意见或建议。 使用下面的产品反馈按钮 ！ 
