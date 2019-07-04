---
title: 如何使用现有模型
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure 机器学习服务的外部服务已训练的模型。 可以注册 Azure 机器学习服务外部创建的模型，然后将它们部署为 web 服务或 Azure IoT Edge 模块。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/19/2019
ms.openlocfilehash: 332129c9847c317369d5631c3af584da9430e9dd
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67453679"
---
# <a name="how-to-use-an-existing-model-with-azure-machine-learning-service"></a>如何使用 Azure 机器学习服务的现有模型

了解如何使用现有机器学习模型与 Azure 机器学习服务。

如果您有一种机器学习模型进行训练 Azure 机器学习服务外，您仍可以使用服务将模型部署为 web 服务或 IoT Edge 设备。 

> [!TIP]
> 本文提供有关注册和部署的现有模型的基本信息。 部署后，Azure 机器学习服务提供监视您的模型。 它还允许您存储输入的数据发送到部署，可用于数据偏差分析或定型新模型的版本。
>
> 有关的概念和术语此处使用的详细信息，请参阅[管理、 部署和监视机器学习模型](concept-model-management-and-deployment.md)。
>
> 有关部署过程的常规信息，请参阅[部署模型与 Azure 机器学习服务](how-to-deploy-and-where.md)。

## <a name="prerequisites"></a>必备组件

* Azure 机器学习服务工作区。 有关详细信息，请参阅[创建工作区](setup-create-workspace.md)。

    > [!TIP]
    > 在这篇文章的 Python 示例假定`ws`变量设置为你的 Azure 机器学习服务工作区。
    >
    > CLI 示例使用的占位符`myworkspace`和`myresourcegroup`。 将其与你的工作区和包含它的资源组的名称为。

* Azure 机器学习 SDK。 有关详细信息，请参阅 Python SDK 一部分[创建工作区](setup-create-workspace.md#sdk)。

* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)并[机器学习 CLI 扩展](reference-azure-machine-learning-cli.md)。

* 定型的模型。 必须将模型保存到您的开发环境上的一个或多个文件。

    > [!NOTE]
    > 若要演示注册模型训练 Azure 机器学习服务外，这篇文章中的示例代码片段使用 Paolo Ripamonti Twitter 情绪分析项目所创建的模型： [ https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis ](https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis)。

## <a name="register-the-models"></a>注册模型

注册模型，可存储版本，并跟踪有关您的工作区中的模型的元数据。 在以下 Python 和 CLI 示例`models`目录包含`model.h5`， `model.w2v`， `encoder.pkl`，和`tokenizer.pkl`文件。 此示例将中包含的文件上传`models`目录作为名为新的模型注册`sentiment`:

```python
from azureml.core.model import Model
# Tip: When model_path is set to a directory, you can use the child_paths parameter to include
#      only some of the files from the directory
model = Model.register(model_path = "./models",
                       model_name = "sentiment",
                       description = "Sentiment analysis model trained outside Azure Machine Learning service",
                       workspace = ws)
```

有关详细信息，请参阅[Model.register()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model(class)?view=azure-ml-py#register-workspace--model-path--model-name--tags-none--properties-none--description-none--datasets-none--model-framework-none--model-framework-version-none--child-paths-none-)引用。

```azurecli
az ml model register -p ./models -n sentiment -w myworkspace -g myresourcegroup
```

有关详细信息，请参阅[az ml 模型注册](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register)引用。


有关详细信息模型注册一般情况下，请参阅[管理、 部署和监视机器学习模型](concept-model-management-and-deployment.md)。


## <a name="define-inference-configuration"></a>定义推理配置

推理配置用于定义用来运行已部署的模型的环境。 推理配置引用用于部署时运行该模型的以下文件：

* 运行时。 运行时的唯一有效的值目前是 Python。
* 入口脚本。 此文件 (名为`score.py`) 已部署的服务启动时加载模型。 它是还负责接收数据，并向其传递到模型中，并返回响应。
* Conda 环境文件。 此文件定义了运行模型和入口脚本所需的 Python 包。 

下面的示例演示使用 Python SDK 的基本推理配置：

```python
from azureml.core.model import InferenceConfig

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score.py",
                                   conda_file="myenv.yml")
```

有关详细信息，请参阅[InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)引用。

CLI 从 YAML 文件加载推断配置：

```yaml
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "myenv.yml"
}
```

推理配置的详细信息，请参阅[部署模型与 Azure 机器学习服务](how-to-deploy-and-where.md)。

### <a name="entry-script"></a>入口脚本

入口脚本有只有两个所需的函数，`init()`和`run(data)`。 这些函数用于初始化服务在启动时和运行在模型中使用由客户端传入的请求数据。 其余的脚本处理加载和运行模型。

> [!IMPORTANT]
> 没有适用于所有模型的一般项脚本。 它始终是特定于所使用的模型。 它必须了解如何将模型加载模型需要的数据格式以及如何使用模型数据评分。

以下 Python 代码是一个示例条目脚本 (`score.py`):

```python
import pickle
import json
import time
from keras.models import load_model
from keras.preprocessing.sequence import pad_sequences
from gensim.models.word2vec import Word2Vec
from azureml.core.model import Model

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

    # Get the path where the model(s) registered as the name 'sentiment' can be found.
    model_path = Model.get_model_path('sentiment')
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

输入脚本的详细信息，请参阅[部署模型与 Azure 机器学习服务](how-to-deploy-and-where.md)。

### <a name="conda-environment"></a>Conda 环境

以下 YAML 描述了运行模型和入口脚本所需的 conda 环境：

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
```

有关详细信息，请参阅[部署模型与 Azure 机器学习服务](how-to-deploy-and-where.md)。

## <a name="define-deployment"></a>定义部署

[Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice?view=azure-ml-py)包包含用于部署的类。 您使用的类确定部署模型。 例如，若要将部署为 Azure Kubernetes 服务上的 web 服务，使用[AksWebService.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none-)以创建部署配置。

以下 Python 代码定义的本地部署的部署配置。 此配置会将模型部署为 web 服务到本地计算机。

> [!IMPORTANT]
> 本地部署要求正确安装[Docker](https://www.docker.com/)在本地计算机上：

```python
from azureml.core.webservice import LocalWebservice

deployment_config = LocalWebservice.deploy_configuration()
```

有关详细信息，请参阅[LocalWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.localwebservice?view=azure-ml-py#deploy-configuration-port-none-)引用。

CLI 从 YAML 文件加载部署配置：

```YAML
{
    "computeType": "LOCAL"
}
```

部署到不同的计算目标，例如在 Azure 云中的 Azure Kubernetes 服务非常简单，只需为正在更改部署配置。 有关详细信息，请参阅[如何以及在何处部署模型](how-to-deploy-and-where.md)。

## <a name="deploy-the-model"></a>部署模型

下面的示例加载名为已注册的模型的信息`sentiment`，然后将其部署为一个名为服务`sentiment`。 在部署期间，推理配置和部署配置用于创建和配置服务环境：

```python
from azureml.core.model import Model

model = Model(ws, name='sentiment')
service = Model.deploy(ws, 'myservice', [model], inference_config, deployment_config)

service.wait_for_deployment(True)
print(service.state)
print("scoring URI: " + service.scoring_uri)
```

有关详细信息，请参阅[Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-)引用。

若要部署的模型从 CLI，使用以下命令。 此命令将部署的已注册的模型的版本 1 (`sentiment:1`) 使用存储中的推断和部署配置`inferenceConfig.json`和`deploymentConfig.json`文件：

```azurecli
az ml model deploy -n myservice -m sentiment:1 --ic inferenceConfig.json --dc deploymentConfig.json
```

有关详细信息，请参阅[az ml 模型部署](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy)引用。

有关部署的详细信息，请参阅[如何以及在何处部署模型](how-to-deploy-and-where.md)。

## <a name="request-response-consumption"></a>请求-响应消耗

在部署后，会显示评分的 URI。 客户端可以使用此 URI 以提交对服务的请求。 下面的示例是一个基本的 Python 客户端向服务提交数据并显示响应：

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

有关如何使用已部署的服务的详细信息，请参阅[创建一个客户端](how-to-consume-web-service.md)。

## <a name="next-steps"></a>后续步骤

* [使用 Application Insights 监视 Azure 机器学习模型](how-to-enable-app-insights.md)
* [为生产环境中的模型收集数据](how-to-enable-data-collection.md)
* [如何以及在何处部署模型](how-to-deploy-and-where.md)
* [如何创建已部署的模型的客户端](how-to-consume-web-service.md)