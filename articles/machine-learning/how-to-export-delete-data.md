---
title: 导出或删除工作区数据
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习工作室、CLI、SDK 和经过身份验证的 REST API 导出或删除工作区。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
author: lobrien
ms.author: laobri
ms.date: 04/24/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 4aa0cecb380f75080abe471a4c414029436c57d6
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2020
ms.locfileid: "89645944"
---
# <a name="export-or-delete-your-machine-learning-service-workspace-data"></a>导出或删除机器学习服务工作区数据

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在 Azure 机器学习中，可以使用门户的图形界面或 Python SDK 来导出或删除工作区数据。 本文介绍这两种选项。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>控制工作区数据

Azure 机器学习存储的产品内数据可用于导出和删除。 可以使用 Azure 机器学习工作室、CLI 和 SDK 进行导出和删除。 可通过 Azure 隐私门户访问遥测数据。 

在 Azure 机器学习中，个人数据包括运行历史记录文档中的用户信息。 

## <a name="delete-high-level-resources-using-the-portal"></a>使用门户删除高级资源

创建工作区时，Azure 在资源组中创建大量资源：

- 工作区本身
- 一个存储帐户
- 容器注册表
- Application Insights 实例
- 密钥保管库

可以从列表选择这些资源，然后选择“删除”将它们删除 

:::image type="content" source="media/how-to-export-delete-data/delete-resource-group-resources.png" alt-text="突出显示了“删除”图标的门户的屏幕截图":::

运行历史记录文档（其中可能包含个人用户信息）存储在 Blob 存储的存储帐户的 `/azureml` 的子文件夹。 可以从门户下载并删除数据。

:::image type="content" source="media/how-to-export-delete-data/storage-account-folders.png" alt-text="门户中存储帐户内 azureml 目录的屏幕截图":::

## <a name="export-and-delete-machine-learning-resources-using-azure-machine-learning-studio"></a>使用 Azure 机器学习工作室导出和删除机器学习资源

Azure 机器学习工作室提供机器学习资源（如笔记本、数据集、模型和试验）的统一视图。 Azure 机器学习工作室强调保存数据和试验的记录。 可以使用浏览器删除计算性资源（如管道和计算资源）。 对于这些资源，导航到相关资源并选择“删除”。 

可以取消注册数据集，并且可以存档试验，但这些操作不删除数据。 若要完全删除数据，必须在存储级别删除数据集和运行数据。 如前文所述，使用门户在存储级别完成删除。

可以使用工作室从实验性运行下载训练项目。  选择“试验”，然后选择感兴趣的“运行”。 选择“输出 + 日志”并导航到要下载的特定项目。  选择“...”和“下载”。

 可以通过导航到所需“模型”并选择“下载”来下载已注册的模型。 

:::image type="contents" source="media/how-to-export-delete-data/model-download.png" alt-text="突出显示了“下载”选项的工作室模型页的屏幕截图":::

## <a name="export-and-delete-resources-using-the-python-sdk"></a>使用 Python SDK 导出和删除资源

可以使用以下内容来下载特定运行的输出： 

```python
# Retrieved from Azure Machine Learning web UI
run_id = 'aaaaaaaa-bbbb-cccc-dddd-0123456789AB'
experiment = ws.experiments['my-experiment']
run = next(run for run in ex.get_runs() if run.id == run_id)
metrics_output_port = run.get_pipeline_output('metrics_output')
model_output_port = run.get_pipeline_output('model_output')

metrics_output_port.download('.', show_progress=True)
model_output_port.download('.', show_progress=True)
```

可以使用 Python SDK 删除以下机器学习资源： 

| 类型 | 函数调用 | 说明 | 
| --- | --- | --- |
| `Workspace` | [`delete`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#&preserve-view=truedelete-delete-dependent-resources-false--no-wait-false-) | 使用 `delete-dependent-resources` 来级联删除 |
| `Model` | [`delete`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model(class)?view=azure-ml-py#&preserve-view=truedelete--) | | 
| `ComputeTarget` | [`delete`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py#&preserve-view=truedelete--) | |
| `WebService` | [`delete`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py&preserve-view=true) | | 

