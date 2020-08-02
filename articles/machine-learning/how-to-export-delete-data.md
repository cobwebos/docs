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
ms.openlocfilehash: fd3abdfd9b0bc0e658caa1cc5ab5c5a7edfda453
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2020
ms.locfileid: "87493989"
---
# <a name="export-or-delete-your-machine-learning-service-workspace-data"></a>导出或删除机器学习服务工作区数据

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在 Azure 机器学习中，可以使用门户的图形界面或 Python SDK 导出或删除工作区数据。 本文介绍这两种方法。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>控制工作区数据

Azure 机器学习存储的产品内数据可用于导出和删除。 可以使用 Azure 机器学习 studio、CLI 和 SDK 进行导出和删除。 可通过 Azure 隐私门户访问遥测数据。 

在 Azure 机器学习中，个人数据包含运行历史记录文档中的用户信息。 

## <a name="delete-high-level-resources-using-the-portal"></a>使用门户删除高级资源

创建工作区时，Azure 会在资源组中创建多个资源：

- 工作区本身
- 一个存储帐户
- 容器注册表
- Application Insights 实例
- 密钥保管库

可以通过从列表中选择这些资源，然后选择 "**删除**" 来删除这些资源 

:::image type="content" source="media/how-to-export-delete-data/delete-resource-group-resources.png" alt-text="门户的屏幕截图，突出显示 "删除" 图标":::

运行历史记录文档（其中可能包含个人用户信息）存储在 blob 存储中的存储帐户中，后者位于的子文件夹中 `/azureml` 。 你可以从门户下载并删除数据。

:::image type="content" source="media/how-to-export-delete-data/storage-account-folders.png" alt-text="门户中存储帐户内 azureml 目录的屏幕截图":::

## <a name="export-and-delete-machine-learning-resources-using-azure-machine-learning-studio"></a>使用 Azure 机器学习 studio 导出和删除机器学习资源

Azure 机器学习 studio 提供了一种统一的机器学习资源视图，如笔记本、数据集、模型和试验。 Azure 机器学习 studio 强调保存数据和试验记录。 可以使用浏览器删除计算资源（如管道和计算资源）。 对于这些资源，请导航到相关资源，然后选择 "**删除**"。 

数据集可以注销，可以存档试验，但这些操作不会删除数据。 若要完全删除数据，必须在存储级别删除数据集和运行数据。 如前文所述，在存储级别删除是使用门户来完成的。

您可以使用 Studio 从实验性运行下载定型项目。 选择你感兴趣的**试验**和**运行**。 选择 "**输出 + 日志**"，并导航到要下载的特定项目。 选择 **...** 和**下载**。

您可以通过导航到所需的**模型**并选择 "**下载**" 来下载已注册的模型。 

:::image type="contents" source="media/how-to-export-delete-data/model-download.png" alt-text="突出显示了下载选项的 studio 模型页的屏幕截图":::

## <a name="export-and-delete-resources-using-the-python-sdk"></a>使用 Python SDK 导出和删除资源

您可以使用下载特定运行的输出： 

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

| 类型 | 函数调用 | 备注 | 
| --- | --- | --- |
| `Workspace` | [`delete`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#delete-delete-dependent-resources-false--no-wait-false-) | 用于 `delete-dependent-resources` 级联删除 |
| `Model` | [`delete`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model(class)?view=azure-ml-py#delete--) | | 
| `ComputeTarget` | [`delete`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py#delete--) | |
| `WebService` | [`delete`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py) | | 

