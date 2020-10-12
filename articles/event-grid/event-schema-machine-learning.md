---
title: 作为事件网格源的 Azure 机器学习
description: 介绍为 Azure 事件网格中的机器学习工作区事件提供的属性
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: fb8cd76829622962b642580bbda7f2a655604c2f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87458036"
---
# <a name="azure-machine-learning-as-an-event-grid-source"></a>作为事件网格源的 Azure 机器学习

本文提供了机器学习工作区事件的属性和架构。 有关事件架构的简介，请参阅 [Azure 事件网格事件架构](event-schema.md)。

## <a name="event-grid-event-schema"></a>事件网格事件架构

### <a name="available-event-types"></a>可用事件类型

Azure 机器学习发出以下事件类型：

| 事件类型 | 说明 |
| ---------- | ----------- |
| Microsoft.MachineLearningServices.ModelRegistered | 已成功注册新模型或模型版本时引发。 |
| Microsoft.MachineLearningServices.ModelDeployed | 将模型成功部署到终结点时引发。 |
| Microsoft.MachineLearningServices.RunCompleted | 在成功完成运行时引发。 |
| Microsoft.MachineLearningServices.DatasetDriftDetected | 当数据集偏移监视器检测到偏移时引发。 |
| Microsoft.MachineLearningServices.RunStatusChanged | 当运行状态更改时引发。 |

### <a name="the-contents-of-an-event-response"></a>事件响应的内容

触发某个事件后，事件网格服务会将有关该事件的数据发送到订阅终结点。

本部分包含一个示例，介绍每个事件的数据外观。

### <a name="microsoftmachinelearningservicesmodelregistered-event"></a>Microsoft.MachineLearningServices.ModelRegistered event

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "models/sklearn_regression_model:20",
  "eventType": "Microsoft.MachineLearningServices.ModelRegistered",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ModelName": "sklearn_regression_model",
    "ModelVersion": 20,
    "ModelTags": {
        "area": "diabetes",
        "type": "regression"
    },
    "ModelProperties": {
        "type": "test"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesmodeldeployed-event"></a>Microsoft.MachineLearningServices.ModelDeployed event

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "endpoints/my-sklearn-service",
  "eventType": "Microsoft.MachineLearningServices.ModelDeployed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ServiceName": "my-sklearn-service",
    "ServiceComputeType": "ACI",
    "ModelIds": "sklearn_regression_model:1,sklearn_regression_model:2",
    "ServiceTags": {
        "area": "diabetes",
        "type": "regression"
    },
    "ServiceProperties": {
        "type": "test"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesruncompleted-event"></a>Microsoft.MachineLearningServices.RunCompleted event

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "experiments/0fa9dfaa-cba3-4fa7-b590-23e48548f5c1/runs/AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
  "eventType": "Microsoft.MachineLearningServices.RunCompleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "experimentId": "0fa9dfaa-cba3-4fa7-b590-23e48548f5c1",
    "experimentName": "automl-local-regression",
    "runId": "AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
    "runType": null,
    "runTags": {},
    "runProperties": {
        "runTemplate": "automl_child",
        "pipeline_id": "5adc0a4fe02504a586f09a4fcbb241f9a4012062",
        "pipeline_spec": "{\"objects\": [{\"class_name\": \"StandardScaler\", \"module\": \"sklearn.preprocessing\", \"param_args\": [], \"param_kwargs\": {\"with_mean\": true, \"with_std\": false}, \"prepared_kwargs\": {}, \"spec_class\": \"preproc\"}, {\"class_name\": \"LassoLars\", \"module\": \"sklearn.linear_model\", \"param_args\": [], \"param_kwargs\": {\"alpha\": 0.001, \"normalize\": true}, \"prepared_kwargs\": {}, \"spec_class\": \"sklearn\"}], \"pipeline_id\": \"5adc0a4fe02504a586f09a4fcbb241f9a4012062\"}",
        "training_percent": "100",
        "predicted_cost": "0.062226144097381045",
        "iteration": "5",
        "run_template": "automl_child",
        "run_preprocessor": "StandardScalerWrapper",
        "run_algorithm": "LassoLars",
        "conda_env_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/conda_env_v_1_0_0.yml",
        "model_name": "AutoMLad912b2d65",
        "scoring_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/scoring_file_v_1_0_0.py",
        "model_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/model.pkl"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesdatasetdriftdetected-event"></a>Microsoft.MachineLearningServices.DatasetDriftDetected event

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "datadrifts/{}/runs/{}",
  "eventType": "Microsoft.MachineLearningServices.DatasetDriftDetected",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "DataDriftId": "01d29aa4-e6a4-470a-9ef3-66660d21f8ef",
    "DataDriftName": "myDriftMonitor",
    "RunId": "01d29aa4-e6a4-470a-9ef3-66660d21f8ef_1571590300380",
    "BaseDatasetId": "3c56d136-0f64-4657-a0e8-5162089a88a3",
    "TargetDatasetId": "d7e74d2e-c972-4266-b5fb-6c9c182d2a74",
    "DriftCoefficient": 0.83503490684792081,
    "StartTime": "2019-07-04T00:00:00+00:00",
    "EndTime": "2019-07-05T00:00:00+00:00"
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesrunstatuschanged-event"></a>Microsoft.MachineLearningServices.RunStatusChanged 事件

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "experiments/0fa9dfaa-cba3-4fa7-b590-23e48548f5c1/runs/AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
  "eventType": "Microsoft.MachineLearningServices.RunStatusChanged",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "experimentId": "0fa9dfaa-cba3-4fa7-b590-23e48548f5c1",
    "experimentName": "automl-local-regression",
    "runId": "AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
    "runType": null,
    "runTags": {},
    "runProperties": {
        "runTemplate": "automl_child",
        "pipeline_id": "5adc0a4fe02504a586f09a4fcbb241f9a4012062",
        "pipeline_spec": "{\"objects\": [{\"class_name\": \"StandardScaler\", \"module\": \"sklearn.preprocessing\", \"param_args\": [], \"param_kwargs\": {\"with_mean\": true, \"with_std\": false}, \"prepared_kwargs\": {}, \"spec_class\": \"preproc\"}, {\"class_name\": \"LassoLars\", \"module\": \"sklearn.linear_model\", \"param_args\": [], \"param_kwargs\": {\"alpha\": 0.001, \"normalize\": true}, \"prepared_kwargs\": {}, \"spec_class\": \"sklearn\"}], \"pipeline_id\": \"5adc0a4fe02504a586f09a4fcbb241f9a4012062\"}",
        "training_percent": "100",
        "predicted_cost": "0.062226144097381045",
        "iteration": "5",
        "run_template": "automl_child",
        "run_preprocessor": "StandardScalerWrapper",
        "run_algorithm": "LassoLars",
        "conda_env_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/conda_env_v_1_0_0.yml",
        "model_name": "AutoMLad912b2d65",
        "scoring_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/scoring_file_v_1_0_0.py",
        "model_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/model.pkl"
    },
   "runStatus": "failed"
   },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="event-properties"></a>事件属性

事件具有以下顶级数据：

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| 主题 | string | 事件源的完整资源路径。 此字段不可写入。 事件网格提供此值。 |
| subject | string | 事件主题的发布者定义路径。 |
| eventType | string | 此事件源的一个注册事件类型。 |
| EventTime | string | 基于提供程序 UTC 时间的事件生成时间。 |
| id | string | 事件的唯一标识符。 |
| 数据 | object | Blob 存储事件数据。 |
| dataVersion | string | 数据对象的架构版本。 发布者定义架构版本。 |
| metadataVersion | string | 事件元数据的架构版本。 事件网格定义顶级属性的架构。 事件网格提供此值。 |

对于每个事件类型，数据对象具有以下属性：

### <a name="microsoftmachinelearningservicesmodelregistered"></a>Microsoft.MachineLearningServices.ModelRegistered

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| ModelName | string | 已注册模型的名称。 |
| ModelVersion | string | 已注册模型的版本。 |
| ModelTags | object | 已注册模型的标记。 |
| ModelProperties | object | 已注册模型的属性。 |

### <a name="microsoftmachinelearningservicesmodeldeployed"></a>Microsoft.MachineLearningServices.ModelDeployed

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| ServiceName | string | 已部署服务的名称。 |
| ServiceComputeType | string | 已部署服务的计算类型（例如 ACI、AKS）。 |
  | ModelIds | string | 模型 ID 的逗号分隔列表。 服务中部署的模型的 ID。 |
| ServiceTags | object | 已部署服务的标记。 |
| ServiceProperties | object | 已部署服务的属性。 |

### <a name="microsoftmachinelearningservicesruncompleted"></a>Microsoft.MachineLearningServices.RunCompleted

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| experimentId | string | 此运行所属的试验的 ID。 |
| experimentName | string | 此运行所属的试验的名称。 |
| runId | string | 已完成的运行的 ID。 |
| runType | string | 已完成的运行的运行类型。 |
| runTags | object | 已完成的运行的标记。 |
| runProperties | object | 已完成的运行的属性。 |

### <a name="microsoftmachinelearningservicesdatasetdriftdetected"></a>Microsoft.MachineLearningServices.DatasetDriftDetected

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| DataDriftId | string | 触发了事件的数据偏移监视器的 ID。 |
| DataDriftName | string | 触发了事件的数据偏移监视器的名称。 |
| RunId | string | 检测到数据偏移的运行的 ID。 |
| BaseDatasetId | string | 用于检测偏移的基础数据集的 ID。 |
| TargetDatasetId | string | 用于检测偏移的目标数据集的 ID。 |
| DriftCoefficient | Double | 触发了事件的系数结果。 |
| StartTime | datetime | 导致了偏移检测的目标数据集时序的开始时间。  |
| EndTime | datetime | 导致了偏移检测的目标数据集时序的结束时间。 |

### <a name="microsoftmachinelearningservicesrunstatuschanged"></a>Microsoft.MachineLearningServices.RunStatusChanged

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| experimentId | string | 此运行所属的试验的 ID。 |
| experimentName | string | 此运行所属的试验的名称。 |
| runId | string | 已完成的运行的 ID。 |
| runType | string | 已完成的运行的运行类型。 |
| runTags | object | 已完成的运行的标记。 |
| runProperties | object | 已完成的运行的属性。 |
| runStatus | string | 运行的状态。 |

## <a name="tutorials-and-how-tos"></a>教程和操作指南
| 标题 | 说明 |
| ----- | ----- |
| [使用 Azure 机器学习事件](../machine-learning/how-to-use-event-grid.md) | 概述 Azure 机器学习与事件网格的集成。 |

## <a name="next-steps"></a>后续步骤

* 有关 Azure 事件网格的简介，请参阅[什么是事件网格？](overview.md)
* 有关创建 Azure 事件网格订阅的详细信息，请参阅[事件网格订阅架构](subscription-creation-schema.md)
* 有关如何将 Azure 事件网格与 Azure 机器学习配合使用的简介，请参阅[使用 Azure 机器学习事件](../machine-learning/how-to-use-event-grid.md)
* 若要通过示例来了解如何将 Azure 事件网格与 Azure 机器学习配合使用，请参阅[创建事件驱动的机器学习工作流](../machine-learning/how-to-use-event-grid.md)
