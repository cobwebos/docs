---
title: Azure 机器学习模型数据收集 API 参考 | Microsoft Docs
description: Azure 机器学习模型数据收集 API 参考。
services: machine-learning
author: aashishb
ms.author: aashishb
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/12/2017
ms.openlocfilehash: db8718b60cdd934e3301479489f21fe45f5578cd
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2018
---
# <a name="azure-machine-learning-model-data-collection-api-reference"></a>Azure 机器学习模型数据收集 API 参考

使用模型数据收集功能可以将机器学习 Web 服务中的模型输入和预测存档。 请参阅[模型数据收集操作方法指南](how-to-use-model-data-collection.md)，了解如何在 Windows 和 Linux 计算机上安装 `azureml.datacollector`。

在此 API 参考指南中，我们使用分步方法说明如何从机器学习 Web 服务中收集模型输入和预测。

## <a name="enable-model-data-collection-in-azure-ml-workbench-environment"></a>在 Azure ML Workbench 环境中启用模型数据收集

 在项目的 aml_config 文件夹下查找 conda\_dependencies.yml 文件，并使 conda\_dependencies.yml 文件在 pip 节下包含 azureml.datacollector 模块，如下所示。 请注意，这仅是完整 conda\_dependencies.yml 文件的一个子部分：

    dependencies:
      - python=3.5.2
      - pip:
        - azureml.datacollector==0.1.0a13

>[!NOTE] 
>目前，可以通过以 docker 模式运行来使用 Azure ML Workbench 中的数据收集器模块。 本地模式可能并非适用于所有环境。




## <a name="enable-model-data-collection-in-the-scoring-file"></a>在评分文件中启用模型数据收集

在用于操作化的评分文件中，导入数据收集器模块和 ModelDataCollector 类：

    from azureml.datacollector import ModelDataCollector


## <a name="model-data-collector-instantiation"></a>模型数据收集器实例化
实例化 ModelDataCollector 的新实例：

    dc = ModelDataCollector(model_name, identifier='default', feature_names=None, model_management_account_id='unknown', webservice_name='unknown', model_id='unknown', model_version='unknown')

请参阅“类和参数详细信息”：

### <a name="class"></a>类
| 名称 | 说明 |
|--------------------|--------------------|
| ModelDataCollector | azureml.datacollector 命名空间中的类。 此类的实例将用于收集模型数据。 单个评分文件可以包含多个 ModelDataCollectors。 每个实例应该用于收集评分文件中一个离散位置中的数据，以使收集的数据架构保持一致（即，输入和预测）|


### <a name="parameters"></a>parameters

| 名称 | Type | 说明 |
|-------------|------------|-------------------------|
| model_name | 字符串 | 要收集其数据的模型的名称 |
| 标识符 | 字符串 | 即标识此数据的代码中位置'RawInput' 或 'Prediction' |
| feature_names | 字符串列表 | 提供时成为 csv 标头的功能名称列表 |
| model_management_account_id | 字符串 | 存储此模型的模型管理帐户的标识符。 通过 AML 实施模型时自动填充此项 |
| webservice_name | 字符串 | 此模型当前部署到的 Web 服务的名称。 通过 AML 实施模型时自动填充此项 |
| model_id | 字符串 | 此模型在模型管理帐户上下文中的唯一标识符。 通过 AML 实施模型时自动填充此项 |
| model_version | 字符串 | 此模型在模型管理帐户上下文中的版本号。 通过 AML 实施模型时自动填充此项 |



 

## <a name="collecting-the-model-data"></a>收集模型数据

可以使用上面创建的 ModelDataCollector 实例收集模型数据。

    dc.collect(input_data, user_correlation_id="")

请参阅“方法和参数详细信息”：

### <a name="method"></a>方法
| 名称 | 说明 |
|--------------------|--------------------|
| collect | 用于收集模型输入或预测的数据|


### <a name="parameters"></a>parameters

| 名称 | Type | 说明 |
|-------------|------------|-------------------------|
| input_data | 多个类型 | 要收集的数据（目前接受类型列表、numpy.array、pandas.DataFrame、pyspark.sql.DataFrame）。 对于数据帧类型，如果存在包含功能名称的标头，则数据目标中将包含此信息（无需在 ModelDataCollector 构造函数中显式传递功能名称） |
| user_correlation_id | 字符串 | 可选的关联 ID，用户可以提供它来关联此预测 |

