---
title: 使用并部署现有模型
titleSuffix: Azure Machine Learning
description: 了解如何通过在服务外部训练过的模型来使用 Azure 机器学习。 可以注册 Azure 机器学习之外创建的模型，然后将其部署为 Web 服务或 Azure IoT Edge 模块。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 03/17/2020
ms.topic: conceptual
ms.custom: how-to, tracking-python
ms.openlocfilehash: 7dc58540cf78356021f1fa2d33dd498381f1da7c
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87325825"
---
# <a name="use-an-existing-model-with-azure-machine-learning"></a>通过 Azure 机器学习使用现有模型
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

了解如何配合使用现有机器学习模型和 Azure 机器学习。

如果你的机器学习模型是在 Azure 机器学习之外训练的，仍可以使用该服务将模型部署为 Web 服务或 IoT Edge 设备。 

> [!TIP]
> 本文介绍有关注册和部署现有模型的基本信息。 部署后，Azure 机器学习即可用于监视模型。 还可用于存储发送到部署的输入数据，可使用该数据分析数据偏移或训练新版模型。
>
> 有关此处使用的概念和术语的详细信息，请参阅[管理、部署和监视机器学习模型](concept-model-management-and-deployment.md)。
>
> 有关部署流程的一般信息，请参阅[使用 Azure 机器学习部署模型](how-to-deploy-and-where.md)。

## <a name="prerequisites"></a>先决条件

* Azure 机器学习工作区。 有关详细信息，请参阅[创建工作区](how-to-manage-workspace.md)。

    > [!TIP]
    > 本文中的 Python 示例假定将 `ws` 变量设置为 Azure 机器学习工作区。
    >
    > CLI 示例使用 `myworkspace` 和 `myresourcegroup` 的占位符。 将这些占位符替换为你的工作区名称以及包含这些名称的资源组。

* [Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。  

* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 和[机器学习 CLI 扩展](reference-azure-machine-learning-cli.md)。

* 定型的模型。 必须将模型保留到开发环境中的一个或多个文件中。

    > [!NOTE]
    > 为了演示如何注册在 Azure 机器学习之外训练的模型，本文中的示例代码片段使用由 Paolo Ripamonti 的 Twitter 情绪分析项目创建的模型：[https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis](https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis)。

## <a name="register-the-models"></a>注册模型

通过注册模型可以让你在工作区中存储、版本和跟踪有关模型的元数据。 在以下 Python 和 CLI 示例中，`models` 目录包含 `model.h5`、`model.w2v`、`encoder.pkl` 和 `tokenizer.pkl` 文件。 此示例将 `models` 目录中包含的文件上传为名为 `sentiment` 的新模型注册：

```python
from azureml.core.model import Model
# Tip: When model_path is set to a directory, you can use the child_paths parameter to include
#      only some of the files from the directory
model = Model.register(model_path = "./models",
                       model_name = "sentiment",
                       description = "Sentiment analysis model trained outside Azure Machine Learning",
                       workspace = ws)
```

有关详细信息，请参阅 [Model.register()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model(class)?view=azure-ml-py#register-workspace--model-path--model-name--tags-none--properties-none--description-none--datasets-none--model-framework-none--model-framework-version-none--child-paths-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none-) 参考。

```azurecli
az ml model register -p ./models -n sentiment -w myworkspace -g myresourcegroup
```

> [!TIP]
> 还可以设置为向已注册的模型添加 `tags` 和 `properties` 字典对象。 以后可以使用这些值来帮助识别特定的模型。 例如使用的框架、训练参数等。

有关详细信息，请参阅 [az ml model register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register) 参考。


有关一般情况下模型注册的详细信息，请参阅[管理、部署和监视机器学习模型](concept-model-management-and-deployment.md)。

## <a name="define-inference-configuration"></a>定义推理配置

推理配置定义用于运行已部署模型的环境。 推理配置引用以下实体，这些实体用于在部署模型时运行模型：

* 一个入口脚本。 此文件（名为 `score.py`）在启动已部署的服务时加载模型。 它还负责接收数据，将数据传递到模型，然后返回响应。
* Azure 机器学习[环境](how-to-use-environments.md)。 环境定义运行模型和入口脚本所需的软件依赖项。

以下示例演示如何使用 SDK 创建环境，然后将其用于推理配置：

```python
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

# Create the environment
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Define the packages needed by the model and scripts
conda_dep.add_conda_package("tensorflow")
conda_dep.add_conda_package("numpy")
conda_dep.add_conda_package("scikit-learn")
# You must list azureml-defaults as a pip dependency
conda_dep.add_pip_package("azureml-defaults")
conda_dep.add_pip_package("keras")
conda_dep.add_pip_package("gensim")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep

inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

有关详细信息，请参阅以下文章：

+ [如何使用环境](how-to-use-environments.md)。
+ [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) 参考。


CLI 从 YAML 文件加载推理配置：

```yaml
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "myenv.yml"
}
```

使用 CLI，在推理配置所引用的 `myenv.yml` 文件中定义 conda 环境。 以下 YAML 是此文件的内容：

```yaml
name: inference_environment
dependencies:
- python=3.6.2
- tensorflow
- numpy
- scikit-learn
- pip:
    - azureml-defaults
    - keras
    - gensim
```

有关推理配置的详细信息，请参阅[使用 Azure 机器学习部署模型](how-to-deploy-and-where.md)。

### <a name="entry-script"></a>入口脚本

入口脚本仅包含两个必需函数（`init()` 和 `run(data)`）。 这两个函数用于在启动时初始化服务，并使用客户端传入的请求数据运行模型。 脚本的其余部分用于加载和运行模型。

> [!IMPORTANT]
> 没有适用于所有模型的通用入口脚本。 入口脚本总是特定于所使用的模型。 请务必了解如何加载模型、模型所需的数据格式，以及如何使用模型对数据进行评分。

以下 Python 代码是一个入口脚本示例 (`score.py`)：

```python
import os
import pickle
import json
import time
from keras.models import load_model
from keras.preprocessing.sequence import pad_sequences
from gensim.models.word2vec import Word2Vec

# SENTIMENT
POSITIVE = "POSITIVE"
NEGATIVE = "NEGATIVE"
NEUTRAL = "NEUTRAL"
SENTIMENT_THRESHOLDS = (0.4, 0.7)
SEQUENCE_LENGTH = 300

# Called when the deployed service starts
def init():
    global model
    global tokenizer
    global encoder
    global w2v_model

    # Get the path where the deployed model can be found.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), './models')
    # load models
    model = load_model(model_path + '/model.h5')
    w2v_model = Word2Vec.load(model_path + '/model.w2v')

    with open(model_path + '/tokenizer.pkl','rb') as handle:
        tokenizer = pickle.load(handle)

    with open(model_path + '/encoder.pkl','rb') as handle:
        encoder = pickle.load(handle)

# Handle requests to the service
def run(data):
    try:
        # Pick out the text property of the JSON request.
        # This expects a request in the form of {"text": "some text to score for sentiment"}
        data = json.loads(data)
        prediction = predict(data['text'])
        #Return prediction
        return prediction
    except Exception as e:
        error = str(e)
        return error

# Determine sentiment from score
def decode_sentiment(score, include_neutral=True):
    if include_neutral:
        label = NEUTRAL
        if score <= SENTIMENT_THRESHOLDS[0]:
            label = NEGATIVE
        elif score >= SENTIMENT_THRESHOLDS[1]:
            label = POSITIVE
        return label
    else:
        return NEGATIVE if score < 0.5 else POSITIVE

# Predict sentiment using the model
def predict(text, include_neutral=True):
    start_at = time.time()
    # Tokenize text
    x_test = pad_sequences(tokenizer.texts_to_sequences([text]), maxlen=SEQUENCE_LENGTH)
    # Predict
    score = model.predict([x_test])[0]
    # Decode sentiment
    label = decode_sentiment(score, include_neutral=include_neutral)

    return {"label": label, "score": float(score),
       "elapsed_time": time.time()-start_at}  
```

有关入口脚本的详细信息，请参阅[使用 Azure 机器学习部署模型](how-to-deploy-and-where.md)。

## <a name="define-deployment"></a>定义部署

[Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice?view=azure-ml-py) 包中包含部署所用的类。 所用的类确定模型的部署位置。 例如，要在 Azure Kubernetes Service 上部署为 Web 服务，可使用 [AksWebService.deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-) 来创建部署配置。

以下 Python 代码用于定义本地部署的部署配置。 此配置将模型作为 Web 服务部署到你的本地计算机中。

> [!IMPORTANT]
> 本地部署要求在本地计算机上安装有效的 [Docker](https://www.docker.com/)：

```python
from azureml.core.webservice import LocalWebservice

deployment_config = LocalWebservice.deploy_configuration()
```

有关详细信息，请参阅 [LocalWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.localwebservice?view=azure-ml-py#deploy-configuration-port-none-) 参考。

CLI 从 YAML 文件加载部署配置：

```YAML
{
    "computeType": "LOCAL"
}
```

部署到不同的计算目标（如 Azure 云中的 Azure Kubernetes 服务）与更改部署配置一样简单。 有关详细信息，请参阅[部署模型的方式和位置](how-to-deploy-and-where.md)。

## <a name="deploy-the-model"></a>部署模型

以下示例加载名为 `sentiment` 的注册模型的信息，然后将其部署为名为 `sentiment` 的服务。 在部署期间，使用推理配置和部署配置来创建和配置服务环境：

```python
from azureml.core.model import Model

model = Model(ws, name='sentiment')
service = Model.deploy(ws, 'myservice', [model], inference_config, deployment_config)

service.wait_for_deployment(True)
print(service.state)
print("scoring URI: " + service.scoring_uri)
```

有关详细信息，请参阅 [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) 参考。

若要从 CLI 部署模型，请使用以下命令。 此命令使用存储在 `inferenceConfig.json` 和 `deploymentConfig.json` 文件中的推理配置和部署配置来部署已注册模型 (`sentiment:1`) 的版本 1：

```azurecli
az ml model deploy -n myservice -m sentiment:1 --ic inferenceConfig.json --dc deploymentConfig.json
```

有关详细信息，请参阅 [az ml model deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) 参考文档。

有关部署的详细信息，请参阅[部署模型的方式和位置](how-to-deploy-and-where.md)。

## <a name="request-response-consumption"></a>请求-响应的使用

部署完成后将显示评分 URI。 客户端可使用此 URI 将请求提交到服务。 以下示例是一个基本 Python 客户端，该客户端将数据提交到服务并显示响应：

```python
import requests
import json

scoring_uri = 'scoring uri for your service'
headers = {'Content-Type':'application/json'}

test_data = json.dumps({'text': 'Today is a great day!'})

response = requests.post(scoring_uri, data=test_data, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

有关如何使用已部署服务的详细信息，请参阅[创建客户端](how-to-consume-web-service.md)。

## <a name="next-steps"></a>后续步骤

* [使用 Application Insights 监视 Azure 机器学习模型](how-to-enable-app-insights.md)
* [为生产环境中的模型收集数据](how-to-enable-data-collection.md)
* [部署模型的方式和位置](how-to-deploy-and-where.md)
* [如何为已部署的模型创建客户端](how-to-consume-web-service.md)
