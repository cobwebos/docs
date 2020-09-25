---
title: Azure Databricks ML 试验的 MLflow 跟踪
titleSuffix: Azure Machine Learning
description: 设置 MLflow，通过 Azure 机器学习来记录 Azure Databricks Ml 试验的指标和项目。
services: machine-learning
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 09/22/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: c6bcf6f228049c4f5c4d1cd0d22cb69fb9677c1a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91341660"
---
# <a name="track-azure-databricks-ml-experiments-with-mlflow-and-azure-machine-learning-preview"></a>通过 MLflow 和 Azure 机器学习 (预览版跟踪 Azure Databricks ML 试验) 

本文介绍如何启用 MLflow 的跟踪 URI 和日志记录 API （统称为 [MLflow 跟踪](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api)），以将 AZURE DATABRICKS (ADB) 试验、MLflow 和 Azure 机器学习。

[MLflow](https://www.mlflow.org) 是一个开放源代码库，用于管理机器学习试验的生命周期。 MLFlow 跟踪是 MLflow 的一个组件，用于记录和跟踪定型运行指标和模型项目。 了解 [Azure Databricks 和 MLflow](https://docs.microsoft.com/azure/databricks/applications/mlflow/)的详细信息。 

有关其他 MLflow 和 Azure 机器学习功能集成，请参阅 [跟踪试验运行和创建终结点，并提供 MLflow 和 Azure 机器学习](how-to-use-mlflow.md) 。

>[!NOTE]
> 作为开放源代码库，MLflow 会经常更改。 因此，通过 Azure 机器学习和 MLflow 集成提供的功能应视为预览版，Microsoft 并不完全支持它。

> [!TIP]
> 本文档中的信息主要面向需要监视模型训练过程的数据科学家与开发人员。 如果你是一名管理员并想要了解如何监视 Azure 机器学习的资源使用情况和事件（例如配额、已完成的训练运行或已完成的模型部署），请参阅[监视 Azure 机器学习](monitor-azure-machine-learning.md)。

## <a name="prerequisites"></a>先决条件

* 安装 `azureml-mlflow` 包。 
    * 此包会自动引入 `azureml-core` [Azure 机器学习 Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)，该 SDK 提供 MLflow 的连接以访问工作区。
* [Azure Databricks 工作区和群集](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)。
* [创建 Azure 机器学习工作区](how-to-manage-workspace.md)。

## <a name="track-azure-databricks-runs"></a>跟踪 Azure Databricks 运行

通过 Azure 机器学习跟踪，你可以将 Azure Databricks 中记录的指标和项目存储在你的中： 

* Azure Databricks 工作区。
* Azure 机器学习工作区

创建 Azure Databricks 工作区和群集后， 

1. 从 PyPi 安装 *mlflow* 库，以确保群集有权访问所需的函数和类。

1. 设置实验笔记本。

1. 连接 Azure Databricks 工作区和 Azure 机器学习工作区。

以下部分介绍了这些步骤的其他详细信息，以便你可以通过 Azure Databricks 成功运行 MLflow 试验。 

## <a name="install-libraries"></a>安装库

若要在群集上安装库，请导航到 "**库**" 选项卡并选择 "**安装新**的"

 ![azure databricks 的 mlflow](./media/how-to-use-mlflow-azure-databricks/azure-databricks-cluster-libraries.png)

在 " **包** " 字段中，键入 mlflow，然后选择 "安装"。 根据需要重复此步骤，以将其他包安装到用于试验的群集中。

 ![Azure DB 安装 mlflow 库](./media/how-to-use-mlflow-azure-databricks/install-libraries.png)

## <a name="set-up-your-notebook"></a>设置笔记本 

设置 ADB 群集后， 
1. 选择左侧导航窗格中的 " **工作区** "。 
1. 展开 "工作区" 下拉菜单，然后选择 "**导入**"
1. 拖放或浏览查找要导入 ADB 工作区的实验笔记本。
1. 选择“导入”。 实验笔记本会自动打开。
1. 在左上角的 "笔记本标题" 下，选择要附加到实验笔记本的群集。 

## <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>连接 Azure Databricks 和 Azure 机器学习工作区

通过将 ADB 工作区链接到 Azure 机器学习工作区，你可以在 Azure 机器学习工作区中跟踪实验数据。

若要将 ADB 工作区链接到新的或现有的 Azure 机器学习工作区， 
1. 登录到 [Azure 门户](https://ms.portal.azure.com)。
1. 导航到 ADB 工作区的 " **概述** " 页。
1. 选择右下方 **Azure 机器学习 "工作区** " 按钮的链接。 

 ![链接 Azure DB 和 Azure 机器学习工作区](./media/how-to-use-mlflow-azure-databricks/link-workspaces.png)

## <a name="mlflow-tracking-in-your-workspaces"></a>工作区中的 MLflow 跟踪

实例化工作区后，MLflow 跟踪会自动设置为在所有以下位置进行跟踪：

* 链接 Azure 机器学习工作区。
* 原始 ADB 工作区。 

托管 Azure 机器学习跟踪服务中的所有试验领域。

下面的代码应在实验笔记本中，以获取链接 Azure 机器学习工作区。 

此代码， 

*  获取用于实例化 Azure 机器学习工作区的 Azure 订阅的详细信息。 

* 假设有一个现有的资源组和 Azure 机器学习工作区，则可以 [创建它们](how-to-manage-workspace.md)。 

* 设置试验名称。 `user_name`此处与 `user_name` Azure Databricks 工作区关联。

```python
import mlflow
import mlflow.azureml
import azureml.mlflow
import azureml.core

from azureml.core import Workspace

subscription_id = 'subscription_id'

# Azure Machine Learning resource group NOT the managed resource group
resource_group = 'resource_group_name' 

#Azure Machine Learning workspace name, NOT Azure Databricks workspace
workspace_name = 'workspace_name'  

# Instantiate Azure Machine Learning workspace
ws = Workspace.get(name=workspace_name,
                   subscription_id=subscription_id,
                   resource_group=resource_group)

#Set MLflow experiment. 
experimentName = "/Users/{user_name}/{experiment_folder}/{experiment_name}" 
mlflow.set_experiment(experimentName) 

```

### <a name="set-mlflow-tracking-to-only-track-in-your-azure-machine-learning-workspace"></a>将 MLflow 跟踪设置为仅在 Azure 机器学习工作区中进行跟踪

如果希望在集中位置管理跟踪的试验，可以将 MLflow 跟踪设置为 **仅** 在 Azure 机器学习工作区中进行跟踪。 

在脚本中包括以下代码：

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

在训练脚本中，导入 `mlflow` 以使用 MLflow 记录 API，并开始记录运行指标。 以下示例记录时期损失指标。 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

## <a name="register-models-with-mlflow"></a>用 MLflow 注册模型

在对模型进行定型后，可以通过方法将模型记录并注册到后端跟踪服务器 `mlflow.<model_flavor>.log_model()` 。 `<model_flavor>`引用与模型关联的框架。 [了解支持哪些模型风格](https://mlflow.org/docs/latest/models.html#model-api)。 

默认情况下，后端跟踪服务器为 Azure Databricks 工作区;除非你选择 [将 MLflow 跟踪设置为仅在 Azure 机器学习工作区中跟踪](#set-mlflow-tracking-to-only-track-in-your-azure-machine-learning-workspace)，否则后端跟踪服务器将是 Azure 机器学习工作区。   

* **如果名为的已注册模型不存在**，则该方法将注册一个新模型，并创建版本1，并返回一个 ModelVersion MLflow 对象。 

* **如果已存在具有该名称的已注册模型**，则该方法会创建新的模型版本并返回 version 对象。 

```python
mlflow.spark.log_model(model, artifact_path = "model", 
                       registered_model_name = 'model_name')  

mlflow.sklearn.log_model(model, artifact_path = "model", 
                         registered_model_name = 'model_name') 
```

## <a name="create-endpoints-for-mlflow-models"></a>创建 MLflow 模型的终结点

准备好为 ML 模型创建终结点时。 可以将部署为， 

* 一个 Azure 机器学习的请求-响应 web 服务，用于交互式计分。 此部署使你能够利用 Azure 机器学习模型管理和数据偏差检测功能，并将其用于生产模型。 

* MLFlow 模型对象，可在流管道或批处理管道中用作 Python 函数或 Pandas Udf Azure Databricks 工作区。

### <a name="deploy-models-to-azure-machine-learning-endpoints"></a>将模型部署到 Azure 机器学习终结点 
可以利用 [mlflow](https://www.mlflow.org/docs/latest/python_api/mlflow.azureml.html#mlflow.azureml.deploy) API 将模型部署到 Azure 机器学习工作区。 如果只是将模型注册到 Azure Databricks 工作区（如 [用 MLflow 注册](#register-models-with-mlflow) 模型部分所述），请指定 `model_name` 参数，将模型注册到 Azure 机器学习工作区中。 

Azure Databricks 运行可以部署到以下终结点， 
* [Azure 容器实例](how-to-use-mlflow.md#deploy-to-aci)
* [Azure Kubernetes 服务](how-to-use-mlflow.md#deploy-to-aks)

### <a name="deploy-models-to-adb-endpoints-for-batch-scoring"></a>将模型部署到 ADB 终结点以实现批处理评分 

可以为批处理评分选择 Azure Databricks 分类。 MLFlow 模型将加载并用作 Spark Pandas UDF，以对新数据进行评分。 

```python
from pyspark.sql.types import ArrayType, FloatType 

model_uri = "runs:/"+last_run_id+ {model_path} 

#Create a Spark UDF for the MLFlow model 

pyfunc_udf = mlflow.pyfunc.spark_udf(spark, model_uri) 

#Load Scoring Data into Spark Dataframe 

scoreDf = spark.table({table_name}).where({required_conditions}) 


#Make Prediction 

preds = (scoreDf 

           .withColumn('target_column_name', pyfunc_udf('Input_column1', 'Input_column2', ' Input_column3', …)) 

        ) 

display(preds) 
```

## <a name="clean-up-resources"></a>清理资源

如果不打算使用工作区中记录的指标和项目，目前尚未提供单独删除它们的功能。 可以改为删除包含存储帐户和工作区的资源组，这样就不会产生任何费用：

1. 在 Azure 门户中，选择最左侧的“资源组”。

   ![在 Azure 门户中删除](./media/how-to-use-mlflow-azure-databricks/delete-resources.png)

1. 从列表中选择已创建的资源组。

1. 选择“删除资源组”****。

1. 输入资源组名称。 然后选择“删除”。

## <a name="example-notebooks"></a>示例笔记本

[Azure 机器学习笔记本的 MLflow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow)演示并扩展了本文中介绍的概念。

## <a name="next-steps"></a>后续步骤

* [管理模型](concept-model-management-and-deployment.md)。
* [跟踪试验运行并通过 MLflow 和 Azure 机器学习创建终结点](how-to-use-mlflow.md)。 
* 了解 [Azure Databricks 和 MLflow](https://docs.microsoft.com/azure/databricks/applications/mlflow/)的详细信息。
