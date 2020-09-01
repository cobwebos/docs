---
title: 试验跟踪和部署模型
titleSuffix: Azure Data Science Virtual Machine
description: 了解如何在 Azure 机器学习和/或 MLFlow 的 Data Science Virtual Machine 中跟踪和记录试验。
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: samkemp
ms.author: samkemp
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: 205aed1811c3d9d21a10be7bc4f01c73eb7295b7
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/01/2020
ms.locfileid: "89254740"
---
# <a name="track-experiments-and-deploy-models-in-azure-machine-learning"></a>在 Azure 机器学习中跟踪试验和部署模型

通过跟踪试验并监视运行指标来改进模型创建过程。 本文介绍如何使用 [MLflow](https://mlflow.org/) API 将日志记录代码添加到训练脚本，并 Azure 机器学习中跟踪试验。

下图说明使用 MLflow 跟踪，你可以跟踪试验的运行指标，并将模型项目存储在 Azure 机器学习工作区中。

![跟踪试验](./media/how-to-track-experiments/mlflow-diagram-track.png)

## <a name="prerequisites"></a>必备条件

* 需要 [预配 Azure 机器学习工作区](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace#create-a-workspace)

## <a name="create-a-new-notebook"></a>创建新的 Notebook

Azure 机器学习和 MLFlow SDK 预安装在 Data Science VM 上，并可在**azureml_py36_ \* ** conda 环境中访问。 在 Jupyterlab 中，单击 "启动程序"，并选择以下内核：

![内核选择](./media/how-to-track-experiments/experiment-tracking-1.png)

## <a name="set-up-the-workspace"></a>设置工作区

中转到 [Azure 门户](https://portal.azure.com) ，并选择作为必备组件的一部分预配的工作区。 你将看到 __下载 config.js__ (参阅下文) -下载配置并确保它存储在 DSVM 上的工作目录中。

![获取配置文件](./media/how-to-track-experiments/experiment-tracking-2.png)

Config 包含工作区名称、订阅等信息，这意味着不需要对这些参数进行硬编码。

## <a name="track-dsvm-runs"></a>跟踪 DSVM 运行

将以下代码添加到笔记本 (或脚本) 以设置 AzureML 工作区对象。

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
跟踪 URI 的有效时间不超过一小时。 如果在一段空闲时间后重新启动脚本，请使用 get_mlflow_tracking_uri API 来获取新的 URI。

### <a name="load-the-data"></a>加载数据

本示例使用 scikit-learn 附带的糖尿病数据集（一个众所周知的小型数据集）。 此单元会加载数据集，并将其拆分为随机训练集和测试集。

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
from sklearn.externals import joblib

X, y = load_diabetes(return_X_y = True)
columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=0)
data = {
    "train":{"X": X_train, "y": y_train},
    "test":{"X": X_test, "y": y_test}
}

print ("Data contains", len(data['train']['X']), "training samples and",len(data['test']['X']), "test samples")
```

### <a name="add-tracking"></a>添加跟踪

使用 Azure 机器学习 SDK 添加试验跟踪并将持久化模型上传到试验运行记录。 下面的代码将添加日志，并将模型文件上传到试验运行中。 模型还在 Azure 机器学习模型注册表中注册。

```python
# Get an experiment object from Azure Machine Learning
from azureml.mlflow import register_model
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    # Log the algorithm parameter alpha to the run
    mlflow.log_param('alpha', 0.03)

    # Create, fit, and test the scikit-learn Ridge regression model
    regression_model = Ridge(alpha=0.03)
    regression_model.fit(data['train']['X'], data['train']['y'])
    preds = regression_model.predict(data['test']['X'])

    # Output the Mean Squared Error to the notebook and to the run
    print('Mean Squared Error is', mean_squared_error(data['test']['y'], preds))
    mlflow.log_metric('mse', mean_squared_error(data['test']['y'], preds))

    # Save the model
    model_file_name = 'model.pkl'
    joblib.dump(value = regression_model, filename = model_file_name)

    # upload the model file explicitly into artifacts
    mlflow.log_artifact(model_file_name)
    # register the model
    register_model(mlflow.active_run(), 'diabetes_model', 'model.pkl', model_framework="ScikitLearn")
```

### <a name="view-runs-in-azure-machine-learning"></a>查看运行 Azure 机器学习

可以在 [Azure 机器学习 Studio](https://ml.azure.com)中查看试验运行。 单击左侧菜单中的 " __试验__ "，并选择 "experiment_with_mlflow" (或者，如果你决定以不同的方式命名试验，请单击) 使用的名称：

![选择试验](./media/how-to-track-experiments/mlflow-experiments.png)

应会看到记录的平均平方误差 (MSE) ：

![MSE](./media/how-to-track-experiments/mlflow-experiments-2.png)

如果单击运行，则会在__输出 + 日志__中看到其他详细信息和 pickle 模型。

## <a name="deploy-model-in-azure-machine-learning"></a>在 Azure 机器学习中部署模型

本部分概述了如何将 DSVM 上训练的模型部署到 Azure 机器学习。

### <a name="step-1-create-inference-compute"></a>步骤1：创建推理计算

在 [AzureML Studio](https://ml.azure.com) 的左侧菜单中，单击 " __计算__ "，然后单击 " __推理群集__ " 选项卡。接下来，单击 " __+ 新建__ "，如下所述：

![创建推理计算](./media/how-to-track-experiments/mlflow-experiments-6.png)

在 __新的推理群集__ 窗格中填写以下内容的详细信息：

* 计算名称
* Kubernetes 服务-选择 "新建"
* 选择区域
* 选择 VM 大小 (在本教程中，Standard_D3_v2 的默认值就足够了) 
* 群集目的-选择 __开发-测试__
* 节点数应等于 __1__
* 网络配置-基本

接下来，单击 " __创建__"。

![计算详细信息](./media/how-to-track-experiments/mlflow-experiments-7.png)

### <a name="step-2-deploy-no-code-inference-service"></a>步骤2：部署无代码推理服务

使用在代码中注册模型时 `register_model` ，我们将该框架指定为 spark-sklearn。 Azure 机器学习不支持以下框架的代码部署：

* scikit-learn
* Tensorflow SaveModel 格式
* ONNX 模型格式

无代码部署意味着可以直接从模型项目进行部署，而无需指定任何特定评分脚本。

若要部署糖尿病模型，请前往 [Azure 机器学习 Studio](https://ml.azure.com) 中的左侧菜单，然后选择 " __模型__"。 接下来，单击注册的 diabetes_model：

![选择模型](./media/how-to-track-experiments/mlflow-experiments-3.png)

接下来，单击 "模型详细信息" 窗格中的 " __部署__ " 按钮：

![部署](./media/how-to-track-experiments/mlflow-experiments-4.png)

我们会将模型部署到在步骤1中创建)  (Azure Kubernetes 服务的推理群集。 填写以下详细信息：提供服务的名称，并在步骤 1)  (创建的 AKS 计算群集的名称。 我们还建议你将 __CPU 预留容量__ 增加到 1 (从 0.1) ，将 __内存预留容量__ 增加到 0.5) 中的1个 (-可以通过单击 " __高级__ " 并填入详细信息来提高这一增长。 然后单击 " __部署__"。

![部署详细信息](./media/how-to-track-experiments/mlflow-experiments-5.png)

### <a name="step-3-consume"></a>步骤3：使用

如果模型已成功部署，你应该看到以下 (若要访问此页面，请单击左侧菜单中的 "终结点" > 然后单击已部署的服务) 的名称：

![使用模型](./media/how-to-track-experiments/mlflow-experiments-8.png)

应会看到部署状态从 __转换__ 到 __正常__。 此外，此详细信息部分提供了 REST 终结点和 Swagger Url，应用程序开发人员可以使用它将 ML 模型集成到其应用程序中。

可以使用 [Postman](https://www.postman.com/)测试终结点，也可以使用 AzureML SDK：

```python
from azureml.core import Webservice
import json

# if you called your service differently then change the name below
service = Webservice(ws, name="diabetes-service")

input_payload = json.dumps({
    'data': X_test[0:2].tolist(),
    'method': 'predict'  # If you have a classification model, you can get probabilities by changing this to 'predict_proba'.
})

output = service.run(input_payload)

print(output)
```

### <a name="step-4-clean-up"></a>步骤4：清理

删除在步骤1中创建的推理计算，以便不会产生正在进行的计算费用。 在 Azure 机器学习 Studio 中的左侧菜单中，单击 "计算" > 推理群集 > 选择 "计算" > "删除"。

## <a name="next-steps"></a>后续步骤

* 了解有关[在 AzureML 中部署模型的](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where)详细信息
