---
title: 使用和部署现有模型
titleSuffix: Azure Machine Learning service
description: 了解如何将 Azure 机器学习服务与在该服务外部训练的模型一起使用。 可以在 Azure 机器学习 service 之外注册创建的模型, 然后将其部署为 web 服务或 Azure IoT Edge 模块。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/19/2019
ms.openlocfilehash: 4534f7e5bba2e34316be3376e2627c15c20ba81f
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2019
ms.locfileid: "68694372"
---
# <a name="use-an-existing-model-with-azure-machine-learning-service"></a>将现有模型用于 Azure 机器学习服务

了解如何将现有机器学习模型与 Azure 机器学习服务结合使用。

如果你有在 Azure 机器学习服务外训练的机器学习模型, 你仍可以使用该服务将该模型部署为 web 服务或 IoT Edge 设备。 

> [!TIP]
> 本文提供了有关注册和部署现有模型的基本信息。 部署后, Azure 机器学习 service 为你的模型提供监视。 它还允许存储发送到部署的输入数据, 该数据可用于数据偏移分析或定型新版本的模型。
>
> 有关此处使用的概念和术语的详细信息, 请参阅[管理、部署和监视机器学习模型](concept-model-management-and-deployment.md)。
>
> 有关部署过程的一般信息, 请参阅[部署具有 Azure 机器学习服务的模型](how-to-deploy-and-where.md)。

## <a name="prerequisites"></a>先决条件

* Azure 机器学习服务工作区。 有关详细信息, 请参阅[创建工作区](setup-create-workspace.md)。

    > [!TIP]
    > 本文中的 Python 示例假设`ws`将变量设置为 Azure 机器学习服务工作区。
    >
    > CLI 示例使用和`myworkspace` `myresourcegroup`的占位符。 将这些名称替换为你的工作区的名称以及包含它的资源组。

* Azure 机器学习 SDK。 有关详细信息, 请参阅[创建工作区](setup-create-workspace.md#sdk)的 Python SDK 部分。

* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)和[机器学习 CLI 扩展](reference-azure-machine-learning-cli.md)。

* 定型的模型。 必须将模型保存到开发环境中的一个或多个文件中。

    > [!NOTE]
    > 为了演示如何注册 Azure 机器学习服务外部训练的模型, 本文中的示例代码片段使用了由 Paolo Ripamonti 的 Twitter 情绪分析项目创建的模型[https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis](https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis):。

## <a name="register-the-models"></a>注册模型

通过注册模型, 可以在工作区中存储、版本和跟踪有关模型的元数据。 在下面的 Python 和 CLI 示例中, `models`目录`model.h5`包含、 `model.w2v`、 `encoder.pkl`和`tokenizer.pkl`文件。 此示例将`models`目录中包含的文件上传为名`sentiment`为的新模型注册:

```python
from azureml.core.model import Model
# Tip: When model_path is set to a directory, you can use the child_paths parameter to include
#      only some of the files from the directory
model = Model.register(model_path = "./models",
                       model_name = "sentiment",
                       description = "Sentiment analysis model trained outside Azure Machine Learning service",
                       workspace = ws)
```

有关详细信息, 请参阅[Model ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model(class)?view=azure-ml-py#register-workspace--model-path--model-name--tags-none--properties-none--description-none--datasets-none--model-framework-none--model-framework-version-none--child-paths-none-)引用。

```azurecli
az ml model register -p ./models -n sentiment -w myworkspace -g myresourcegroup
```

有关详细信息, 请参阅[az ml model register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register) reference。


有关一般情况下的模型注册的详细信息, 请参阅[管理、部署和监视机器学习模型](concept-model-management-and-deployment.md)。


## <a name="define-inference-configuration"></a>定义推理配置

推理配置定义用于运行已部署模型的环境。 推理配置引用以下文件, 这些文件用于在部署模型时运行模型:

* 运行时。 对于运行时, 唯一有效的值当前为 Python。
* 一个项脚本。 此文件 (名`score.py`为) 在部署的服务启动时加载模型。 它还负责接收数据, 将数据传递到模型, 然后返回响应。
* Conda 环境文件。 此文件定义运行模型和条目脚本所需的 Python 包。 

下面的示例演示了使用 Python SDK 的基本推理配置:

```python
from azureml.core.model import InferenceConfig

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score.py",
                                   conda_file="myenv.yml")
```

有关详细信息, 请参阅[InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)参考。

CLI 从 YAML 文件加载推理配置:

```yaml
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "myenv.yml"
}
```

有关推理配置的详细信息, 请参阅[部署具有 Azure 机器学习服务的模型](how-to-deploy-and-where.md)。

### <a name="entry-script"></a>输入脚本

条目脚本仅包含两个必需函数: `init()`和`run(data)`。 这些函数用于在启动时初始化服务, 并使用客户端传入的请求数据运行模型。 脚本的其余部分将处理模型的加载和运行。

> [!IMPORTANT]
> 没有适用于所有模型的通用条目脚本。 它始终特定于所使用的模型。 它必须了解如何加载模型、模型所需的数据格式, 以及如何使用模型对数据进行评分。

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

有关输入脚本的详细信息, 请参阅[部署模型与 Azure 机器学习 service](how-to-deploy-and-where.md)。

### <a name="conda-environment"></a>Conda 环境

下面的 YAML 介绍了运行模型和条目脚本所需的 conda 环境:

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

有关详细信息, 请参阅[部署模型和 Azure 机器学习服务](how-to-deploy-and-where.md)。

## <a name="define-deployment"></a>定义部署

[Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice?view=azure-ml-py)包包含用于部署的类。 使用的类确定模型的部署位置。 例如, 若要在 Azure Kubernetes Service 上部署为 web 服务, 请使用[AksWebService ()](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none-)来创建部署配置。

以下 Python 代码定义了本地部署的部署配置。 此配置将模型作为 web 服务部署到你的本地计算机。

> [!IMPORTANT]
> 本地部署要求在本地计算机上运行[Docker](https://www.docker.com/) :

```python
from azureml.core.webservice import LocalWebservice

deployment_config = LocalWebservice.deploy_configuration()
```

有关详细信息, 请参阅[LocalWebservice. deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.localwebservice?view=azure-ml-py#deploy-configuration-port-none-)引用。

CLI 从 YAML 文件加载部署配置:

```YAML
{
    "computeType": "LOCAL"
}
```

部署到不同的计算目标 (如 Azure 云中的 Azure Kubernetes 服务) 与更改部署配置一样简单。 有关详细信息, 请参阅[部署模型的方式和位置](how-to-deploy-and-where.md)。

## <a name="deploy-the-model"></a>部署模型

下面的示例加载名为`sentiment`的注册模型的信息, 然后将其部署为名为`sentiment`的服务。 在部署期间, 推理配置和部署配置用于创建和配置服务环境:

```python
from azureml.core.model import Model

model = Model(ws, name='sentiment')
service = Model.deploy(ws, 'myservice', [model], inference_config, deployment_config)

service.wait_for_deployment(True)
print(service.state)
print("scoring URI: " + service.scoring_uri)
```

有关详细信息, 请参阅[Model ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-)引用。

若要从 CLI 部署模型, 请使用以下命令。 此命令使用存储`sentiment:1` `inferenceConfig.json`在和`deploymentConfig.json`文件中的推理和部署配置来部署已注册模型的版本 1 ():

```azurecli
az ml model deploy -n myservice -m sentiment:1 --ic inferenceConfig.json --dc deploymentConfig.json
```

有关详细信息, 请参阅[az ml 模型部署](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy)参考。

有关部署的详细信息, 请参阅[部署模型的方式和位置](how-to-deploy-and-where.md)。

## <a name="request-response-consumption"></a>请求-响应消耗

部署后, 将显示计分 URI。 此 URI 可供客户端用来向服务提交请求。 下面的示例是一个基本 Python 客户端, 它将数据提交到服务并显示响应:

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

有关如何使用已部署服务的详细信息, 请参阅[创建客户端](how-to-consume-web-service.md)。

## <a name="next-steps"></a>后续步骤

* [使用 Application Insights 监视 Azure 机器学习模型](how-to-enable-app-insights.md)
* [为生产环境中的模型收集数据](how-to-enable-data-collection.md)
* [部署模型的方式和位置](how-to-deploy-and-where.md)
* [如何为已部署的模型创建客户端](how-to-consume-web-service.md)
