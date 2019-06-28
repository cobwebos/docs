---
title: 如何检测 AKS 部署数据偏差 （预览版）
titleSuffix: Azure Machine Learning service
description: 了解如何检测数据偏差 Azure Kubernetes 服务上的部署 Azure 机器学习服务中的模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: copeters
author: cody-dkdc
ms.date: 06/20/2019
ms.openlocfilehash: e4deeab28fb643ff32624ba9dd16574e621f508c
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332811"
---
# <a name="how-to-detect-data-drift-preview-on-models-deployed-to-azure-kubernetes-service"></a>如何检测数据偏差 （预览版） 部署到 Azure Kubernetes 服务的模型
在本文中，您将学习如何监视[数据偏差](concept-data-drift.md)之间已部署的模型的定型数据集和推断数据。 

数据偏差是主要理由之一，随着时间的推移会降低模型准确性。 它发生在提供给在生产环境中的模型的数据不同于用来训练该模型的数据。 Azure 机器学习服务可以监视数据偏差使用数据偏差检测程序。 如果检测到偏差时，该服务可以向你发送警报。  

> [!Note]
> 此服务为 （预览版） 和配置选项的限制。 请参阅我们[API 文档](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/?view=azure-ml-py)并[发行说明](azure-machine-learning-release-notes.md)有关详细信息和更新。 

使用 Azure 机器学习服务时，可以监视在 AKS 上部署的模型的输入和此数据与模型的定型数据集进行比较。 推断数据是按固定间隔[快照和分析](how-to-explore-prepare-data.md)，然后对要生成的数据偏移分析的基准数据集计算的： 

+ 测量数据偏差，称为决定系数偏移量。
+ 度量值数据的功能，告知哪些功能导致数据偏差偏差的发布内容。
+ 度量值距离度量值。 当前 Wasserstein 和能源距离进行计算。
+ 度量值的功能的分发。 当前内核密度估计和直方图。
+ 发送数据警报通过电子邮件偏差。

有关如何计算这些指标的详细信息，请参阅[数据偏差概念](concept-data-drift.md)一文。

## <a name="prerequisites"></a>必备组件

- 如果还没有 Azure 订阅，请在开始前创建免费帐户。 立即试用 [Azure 机器学习服务免费版或付费版](https://aka.ms/AMLFree)。

- 已安装 Azure 机器学习服务工作区，以及适用于 Python 的 Azure 机器学习 SDK。 了解如何通过[如何配置开发环境](how-to-configure-environment.md)一文满足这些先决条件。

- [设置环境](how-to-configure-environment.md)，然后安装数据偏差 SDK 使用以下命令：

    ```
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

- 设置[模型数据收集器](how-to-enable-data-collection.md)从 AKS 部署模型中收集数据并确认数据要收集在`modeldata`blob 容器。

## <a name="import-dependencies"></a>导入依赖项 
本指南中使用的导入依赖项：

```python
# Azure ML service packages 
from azureml.core import Experiment, Run, RunDetails
from azureml.contrib.datadrift import DataDriftDetector, AlertConfiguration
``` 

## <a name="configure-data-drift"></a>配置数据偏差 

下面的 Python 示例演示如何配置`DataDriftDetector`对象：

```python
# if email address is specified, setup AlertConfiguration
alert_config = AlertConfiguration('your_email@contoso.com')

# create a new DatadriftDetector object
datadrift = DataDriftDetector.create(ws, model.name, model.version, services, frequency="Day", alert_config=alert_config)
    
print('Details of Datadrift Object:\n{}'.format(datadrift))
```

有关详细信息，请参阅[DataDrift](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/?view=azure-ml-py)引用。

## <a name="submit-a-datadriftdetector-run"></a>提交 DataDriftDetector 运行

使用配置 DataDriftDetector，可以将提交[运行的数据偏移](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift.datadriftdetector%28class%29?view=azure-ml-py#run-target-date--services--compute-target-name-none--create-compute-target-false--feature-list-none--drift-threshold-none-)在给定日期的模型。 

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

## <a name="get-data-drift-analysis-results"></a>获取分析结果的数据偏移

下面的 Python 示例演示了如何绘制相关数据偏差度量值。 返回度量值可用于生成自定义可视化效果：

```python
# start and end are datetime objects 
drift_metrics = datadrift.get_output(start_time=start, end_time=end)

# Show all data drift result figures, one per serivice.
# If setting with_details is False (by default), only the data drift magnitude will be shown; if it's True, all details will be shown.
drift_figures = datadrift.show(with_details=True)
```

![数据偏差 Show](media/how-to-monitor-data-drift/drift_show.png)

计算的指标的详细信息，请参阅[数据偏差概念](concept-data-drift.md)一文。

## <a name="schedule-data-drift-detection"></a>计划数据偏差检测 

启用数据偏差计划执行指定的频率运行 DataDriftDetector。 如果偏差系数大于给定的阈值，将发送一封电子邮件。 

```python
datadrift.enable_schedule()
datadrift.disable_schedule()
```

在 Azure 门户中的模型的详细信息页上，可以查看数据偏差检测程序的配置。

![Azure 门户数据偏差配置](media/how-to-monitor-data-drift/drift_config.png)

## <a name="view-results-in-azure-ml-workspace-ui"></a>在 Azure 机器学习工作区 UI 中查看结果

若要在 Azure 机器学习工作区用户界面中查看结果，请导航到模型页。 在该模型的详细信息选项卡上将显示数据偏差配置。 数据偏差 （预览版） 选项卡上现已推出数据偏差指标进行了可视化。 

![Azure 门户数据偏差](media/how-to-monitor-data-drift/drift_ui.png)

## <a name="setting-up-alerts"></a>设置警报 

设置警报阈值和电子邮件地址，为提供的偏差系数[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview)如果偏差系数高于阈值，则发送电子邮件警报。 所有数据偏差指标都存储在 app insights 资源与 Azure 机器学习服务工作区，以便设置针对自定义警报或操作相关联。 你可以遵循 app insights 查询中的电子邮件警报的链接。

![数据偏差电子邮件警报](media/how-to-monitor-data-drift/drift_email.png)

## <a name="next-steps"></a>后续步骤

* 使用数据偏差的完整示例，请参阅[Azure 机器学习数据偏差 notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/data-drift/azure-ml-datadrift.ipynb)。 此 Jupyter Notebook 演示如何使用[Azure 打开数据集](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets)来训练模型来预测天气，将其部署到 AKS，并监视数据偏差。 

* 因为数据偏差会向移动常规可用性，我们极大地不胜感激问题、 意见或建议。 使用下面的产品反馈按钮 ！ 
