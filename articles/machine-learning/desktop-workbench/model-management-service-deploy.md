---
title: Azure 机器学习模型管理 Web 服务部署 | Microsoft Docs
description: 本文档介绍了使用 Azure 机器学习模型管理部署机器学习模型时涉及的步骤。
services: machine-learning
author: aashishb
ms.author: aashishb
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.topic: article
ms.date: 01/03/2018
ms.openlocfilehash: 5360c9371b0e1d3191624cd1a65e505e7b9968de
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2018
ms.locfileid: "34832037"
---
# <a name="deploying-a-machine-learning-model-as-a-web-service"></a>将机器学习模型部署为 Web 服务

Azure 机器学习模型管理提供了相应的接口来将模型部署为容器化的基于 Docker 的 Web 服务。 可以部署使用 Spark、Microsoft Cognitive Toolkit (CNTK)、Keras、Tensorflow 和 Python 之类的框架创建的模型。 

本文档介绍了使用 Azure 机器学习模型管理命令行接口 (CLI) 将模型部署为 Web 服务时所需执行的步骤。

## <a name="what-you-need-to-get-started"></a>入门所需操作

为了充分利用本指南，对可将模型部署到的 Azure 订阅或资源组应该拥有参与者访问权限。
Azure Machine Learning Workbench 和 [Azure DSVM](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-virtual-machine-overview) 上预安装了 CLI。  它也可以作为独立包安装。

另外，须已设置模型管理帐户和部署环境。  有关为本地和群集部署设置模型管理帐户和环境的详细信息，请参阅[模型管理配置](deployment-setup-configuration.md)。

## <a name="deploying-web-services"></a>部署 Web 服务
使用 CLI，可以将 Web 服务部署为在本地计算机或在群集上运行。

建议从本地部署开始。 首先验证模型和代码是否正常工作，然后将 Web 服务部署到群集以供大规模生产使用。

下面是部署步骤：
1. 使用已保存的已训练机器学习模型
2. 为 Web 服务的输入和输出数据创建一个架构
3. 创建一个基于 Docker 的容器映像
4. 创建并部署 Web 服务

### <a name="1-save-your-model"></a>1.保存模型
使用已保存的已训练模型文件（例如 mymodel.pkl）开始操作。 文件扩展名因你用来训练和保存模型的平台而异。 

例如，可以使用 Python 的 Pickle 库将已训练的模型保存为文件。 下面是使用 Iris 数据集的一个[示例](http://scikit-learn.org/stable/modules/model_persistence.html)：

```python
import pickle
from sklearn import datasets
iris = datasets.load_iris()
X, y = iris.data, iris.target
clf = linear_model.LogisticRegression()
clf.fit(X, y)  
saved_model = pickle.dumps(clf)
```

### <a name="2-create-a-schemajson-file"></a>2.创建一个 schema.json 文件

虽然架构生成是可选的，但强烈建议定义请求和输入变量格式以获得更好的处理效果。

创建一个架构来自动验证 Web 服务的输入和输出。 CLI 还使用该架构来为 Web 服务生成 Swagger 文档。

若要创建架构，请导入以下库：

```python
from azureml.api.schema.dataTypes import DataTypes
from azureml.api.schema.sampleDefinition import SampleDefinition
from azureml.api.realtime.services import generate_schema
```
接下来，定义输入变量，例如 Spark 数据帧、Pandas 数据帧或 NumPy 数组。 以下示例使用了一个 Numpy 数组：

```python
inputs = {"input_array": SampleDefinition(DataTypes.NUMPY, yourinputarray)}
generate_schema(run_func=run, inputs=inputs, filepath='./outputs/service_schema.json')
```
以下示例使用了一个 Spark 数据帧：

```python
inputs = {"input_df": SampleDefinition(DataTypes.SPARK, yourinputdataframe)}
generate_schema(run_func=run, inputs=inputs, filepath='./outputs/service_schema.json')
```

以下示例使用了一个 PANDAS 数据帧：

```python
inputs = {"input_df": SampleDefinition(DataTypes.PANDAS, yourinputdataframe)}
generate_schema(run_func=run, inputs=inputs, filepath='./outputs/service_schema.json')
```

以下示例使用通用 JSON 格式：

```python
inputs = {"input_json": SampleDefinition(DataTypes.STANDARD, yourinputjson)}
generate_schema(run_func=run, inputs=inputs, filepath='./outputs/service_schema.json')
```

### <a name="3-create-a-scorepy-file"></a>3.创建 score.py 文件
你将提供一个 score.py 文件，该文件加载一个模型并使用该模型返回预测结果。

该文件必须包括两个函数：init 和 run。

在 score.py 文件的开头添加以下代码以启用数据收集功能，用以帮助收集模型输入和预测数据

```python
from azureml.datacollector import ModelDataCollector
```

有关如何使用此功能的更多详细信息，请检查[模型数据收集](how-to-use-model-data-collection.md)部分。

#### <a name="init-function"></a>Init 函数
使用 init 函数来加载已保存的模型。

用于加载模型的简单 init 函数的示例：

```python
def init():  
    from sklearn.externals import joblib
    global model, inputs_dc, prediction_dc
    model = joblib.load('model.pkl')

    inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
    prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")
```

#### <a name="run-function"></a>Run 函数
run 函数使用模型和输入数据来返回预测。

用于处理输入并返回预测结果的简单 run 函数的示例：

```python
def run(input_df):
    global clf2, inputs_dc, prediction_dc
    try:
        prediction = model.predict(input_df)
        inputs_dc.collect(input_df)
        prediction_dc.collect(prediction)
        return prediction
    except Exception as e:
        return (str(e))
```

### <a name="4-register-a-model"></a>4.注册模型
下面的命令用来注册在上面的步骤 1 中创建的模型。

```
az ml model register --model [path to model file] --name [model name]
```

### <a name="5-create-manifest"></a>5.创建清单
下面的命令用于为模型创建清单。

```
az ml manifest create --manifest-name [your new manifest name] -f [path to score file] -r [runtime for the image, e.g. spark-py]
```
可以通过在如上所示的命令中使用参数 `--model-id` 或 `-i` 将以前注册的模型添加到清单。 可以使用附加的 -i 参数指定多个模型。

### <a name="6-create-an-image"></a>6.创建映像 
可以选择先创建映像的清单，然后再创建映像。 

```
az ml image create -n [image name] --manifest-id [the manifest ID]
```

>[!NOTE] 
>也可使用单个命令来执行模型注册、清单和模型创建。 若需更多详细信息，请将 -h 与 service create 命令一起使用。

作为替代方案，可使用单个命令通过一个步骤来注册模型、创建清单和创建映像（但不能创建和部署 Web 服务），如下所示。

```
az ml image create -n [image name] --model-file [model file or folder path] -f [code file, e.g. the score.py file] -r [the runtime e.g. spark-py which is the Docker container image base]
```

>[!NOTE]
>若需命令参数的更多详细信息，请在命令的末尾键入 -h，例如 az ml image create -h。


### <a name="7-create-and-deploy-the-web-service"></a>7.创建并部署 Web 服务
使用以下命令部署服务：

```
az ml service create realtime --image-id <image id> -n <service name>
```

>[!NOTE] 
>还可以使用单个命令执行上述 4 个步骤。 若需更多详细信息，请将 -h 与 service create 命令一起使用。

作为替代方案，单个命令可作为一个步骤来注册模型、创建清单、创建映像并部署 Web 服务，如下所示。

```azurecli
az ml service create realtime --model-file [model file/folder path] -f [scoring file e.g. score.py] -n [your service name] -s [schema file e.g. service_schema.json] -r [runtime for the Docker container e.g. spark-py or python] -c [conda dependencies file for additional python packages]
```


### <a name="8-test-the-service"></a>8.测试服务
使用以下命令获取有关如何调用服务的信息：

```
az ml service usage realtime -i <service id>
```

从命令提示符使用 run 函数调用服务：

```
az ml service run realtime -i <service id> -d "{\"input_df\": [INPUT DATA]}"
```

以下示例调用了一个示例 Iris Web 服务：

```
az ml service run realtime -i <service id> -d "{\"input_df\": [{\"sepal length\": 3.0, \"sepal width\": 3.6, \"petal width\": 1.3, \"petal length\":0.25}]}"
```

## <a name="next-steps"></a>后续步骤
现在，你已测试了要在本地运行的 Web 服务，可以将其部署到群集以供大规模使用了。 有关为 Web 服务部署设置群集的详细信息，请参阅[模型管理配置](deployment-setup-configuration.md)。 
