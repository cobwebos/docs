---
title: 部署用于认知搜索的模型
titleSuffix: Azure Machine Learning
description: 本文介绍如何使用 Azure 机器学习部署用于 Azure 认知搜索的模型。 认知搜索可以使用 Azure 机器学习部署的模型作为自定义技能来丰富搜索体验。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: larryfr
ms.reviewer: larryfr
ms.date: 06/11/2020
ms.openlocfilehash: 9f905e23097f244c4c7f05cd7317b759fb2d761e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91250889"
---
# <a name="deploy-a-model-for-use-with-cognitive-search"></a>部署用于认知搜索的模型


本文介绍如何使用 Azure 机器学习部署用于 [Azure 认知搜索](../search/search-what-is-azure-search.md)的模型。

认知搜索对异类内容执行内容处理，以使其可以由人或应用程序查询。 使用通过 Azure 机器学习部署的模型可以增强此过程。

Azure 机器学习可以将经过训练的模型部署为 Web 服务。 然后，将 Web 服务嵌入到认知搜索技能（它会成为处理管道的一部分）__。

> [!IMPORTANT]
> 本文中的信息特定于模型的部署。 本文提供有关受支持的部署配置的信息，这些配置允许认知搜索使用模型。
>
> 有关如何配置认知搜索以使用部署的模型的信息，请参阅[使用 Azure 机器学习构建和部署自定义技能](../search/cognitive-search-tutorial-aml-custom-skill.md)教程。
>
> 有关本教程基于的示例，请参阅 [https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill)。

部署用于 Azure 认知搜索的模型时，部署必须满足以下要求：

* 使用 Azure Kubernetes 服务托管用于推理的模型。
* 为 Azure Kubernetes 服务启用传输层安全性 (TLS)。 TLS 用于保护认知搜索和已部署模型之间的 HTTPS 通信。
* 入口脚本必须使用 `inference_schema` 包来生成服务的 OpenAPI (Swagger) 架构。
* 入口脚本还必须接受 JSON 数据作为输入，并生成 JSON 作为输出。


## <a name="prerequisites"></a>先决条件

* Azure 机器学习工作区。 有关详细信息，请参阅[创建 Azure 机器学习工作区](how-to-manage-workspace.md)。

* 安装了 Azure 机器学习 SDK 的 Python 开发环境。 有关详细信息，请参阅 [Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)。  

* 已注册的模型。 如果没有模型，请使用 [https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill) 上的示例笔记本。

* 大致了解[如何以及在何处部署模型](how-to-deploy-and-where.md)。

## <a name="connect-to-your-workspace"></a>连接到工作区

Azure 机器学习工作区提供了一个集中的位置来处理使用 Azure 机器学习时创建的所有项目。 工作区保留所有训练运行的历史记录，包括日志、指标、输出和脚本快照。

若要连接到现有工作区，请使用以下代码：

> [!IMPORTANT]
> 此代码片段预期工作区配置被保存到当前目录或其父项。 有关详细信息，请参阅[创建和管理 Azure 机器学习工作区](how-to-manage-workspace.md)。 有关将配置保存到文件的详细信息，请参阅[创建工作区配置文件](how-to-configure-environment.md#workspace)。

```python
from azureml.core import Workspace

try:
    # Load the workspace configuration from local cached inffo
    ws = Workspace.from_config()
    print(ws.name, ws.location, ws.resource_group, ws.location, sep='\t')
    print('Library configuration succeeded')
except:
    print('Workspace not found')
```

## <a name="create-a-kubernetes-cluster"></a>创建 Kubernetes 群集

**时间估计**：大约 20 分钟。

Kubernetes 群集是用于运行容器化应用程序的一组虚拟机实例（称为节点）。

将模型从 Azure 机器学习部署到 Azure Kubernetes 服务时，该模型以及将其作为 Web 服务托管所需的所有资产均打包到 Docker 容器中。 然后将此容器部署到群集。

以下代码演示如何为工作区创建新的 Azure Kubernetes 服务 (AKS) 群集：

> [!TIP]
> 还可以将现有的 Azure Kubernetes 服务附加到 Azure 机器学习工作区。 有关详细信息，请参阅[如何将模型部署到 Azure Kubernetes 服务](how-to-deploy-azure-kubernetes-service.md)。

> [!IMPORTANT]
> 请注意，代码使用 `enable_ssl()` 方法为群集启用传输层安全性 (TLS)。 计划从认知服务使用已部署的模型时，必须这样做。

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Create or attach to an AKS inferencing cluster

# Create the provisioning configuration with defaults
prov_config = AksCompute.provisioning_configuration()

# Enable TLS (sometimes called SSL) communications
# Leaf domain label generates a name using the formula
#  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
#  where "######" is a random series of characters
prov_config.enable_ssl(leaf_domain_label = "contoso")

cluster_name = 'amlskills'
# Try to use an existing compute target by that name.
# If one doesn't exist, create one.
try:
    
    aks_target = ComputeTarget(ws, cluster_name)
    print("Attaching to existing cluster")
except Exception as e:
    print("Creating new cluster")
    aks_target = ComputeTarget.create(workspace = ws, 
                                  name = cluster_name, 
                                  provisioning_configuration = prov_config)
    # Wait for the create process to complete
    aks_target.wait_for_completion(show_output = True)
```

> [!IMPORTANT]
> 只要存在 AKS 群集，Azure 就会向你收费。 请务必在使用完 AKS 群集后将其删除。

有关将 AKS 与 Azure 机器学习配合使用的详细信息，请参阅[如何部署到 Azure Kubernetes 服务](how-to-deploy-azure-kubernetes-service.md)。

## <a name="write-the-entry-script"></a>编写入口脚本

入口脚本接收提交给 Web 服务的数据，将其传递给模型，并返回计分结果。 以下脚本在启动时加载模型，然后使用该模型对数据进行评分。 此文件有时称为 `score.py`。

> [!TIP]
> 入口脚本特定于你的模型。 例如，脚本必须知道要与模型、数据格式等一起使用的框架。

> [!IMPORTANT]
> 计划从 Azure 认知服务使用部署的模型时，必须使用 `inference_schema` 包为部署启用架构生成。 此包提供修饰器，可用这些修饰器为使用模型执行推理的 Web 服务定义输入和输出数据的格式。

```python
from azureml.core.model import Model
from nlp_architect.models.absa.inference.inference import SentimentInference
from spacy.cli.download import download as spacy_download
import traceback
import json
# Inference schema for schema discovery
from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.standard_py_parameter_type import StandardPythonParameterType

def init():
    """
    Set up the ABSA model for Inference  
    """
    global SentInference
    spacy_download('en')
    aspect_lex = Model.get_model_path('hotel_aspect_lex')
    opinion_lex = Model.get_model_path('hotel_opinion_lex') 
    SentInference = SentimentInference(aspect_lex, opinion_lex)

# Use inference schema decorators and sample input/output to
# build the OpenAPI (Swagger) schema for the deployment
standard_sample_input = {'text': 'a sample input record containing some text' }
standard_sample_output = {"sentiment": {"sentence": "This place makes false booking prices, when you get there, they say they do not have the reservation for that day.", 
                                        "terms": [{"text": "hotels", "type": "AS", "polarity": "POS", "score": 1.0, "start": 300, "len": 6}, 
                                                  {"text": "nice", "type": "OP", "polarity": "POS", "score": 1.0, "start": 295, "len": 4}]}}
@input_schema('raw_data', StandardPythonParameterType(standard_sample_input))
@output_schema(StandardPythonParameterType(standard_sample_output))    
def run(raw_data):
    try:
        # Get the value of the 'text' field from the JSON input and perform inference
        input_txt = raw_data["text"]
        doc = SentInference.run(doc=input_txt)
        if doc is None:
            return None
        sentences = doc._sentences
        result = {"sentence": doc._doc_text}
        terms = []
        for sentence in sentences:
            for event in sentence._events:
                for x in event:
                    term = {"text": x._text, "type":x._type.value, "polarity": x._polarity.value, "score": x._score,"start": x._start,"len": x._len }
                    terms.append(term)
        result["terms"] = terms
        print("Success!")
        # Return the results to the client as a JSON document
        return {"sentiment": result}
    except Exception as e:
        result = str(e)
        # return error message back to the client
        print("Failure!")
        print(traceback.format_exc())
        return json.dumps({"error": result, "tb": traceback.format_exc()})
```

有关入口脚本的详细信息，请参阅[如何以及在何处部署](how-to-deploy-and-where.md)。

## <a name="define-the-software-environment"></a>定义软件环境

环境类用于定义服务的 Python 依赖项。 它包括模型和入口脚本都需要的依赖项。 在此示例中，它安装来自常规 pypi 索引以及 GitHub 存储库的包。 

```python
from azureml.core.conda_dependencies import CondaDependencies 
from azureml.core import Environment

conda = None
pip = ["azureml-defaults", "azureml-monitoring", 
       "git+https://github.com/NervanaSystems/nlp-architect.git@absa", 'nlp-architect', 'inference-schema',
       "spacy==2.0.18"]

conda_deps = CondaDependencies.create(conda_packages=None, pip_packages=pip)

myenv = Environment(name='myenv')
myenv.python.conda_dependencies = conda_deps
```

有关环境的详细信息，请参阅[创建和管理用于训练和部署的环境](how-to-use-environments.md)。

## <a name="define-the-deployment-configuration"></a>定义部署配置

部署配置定义用于运行 Web 服务的 Azure Kubernetes 服务托管环境。

> [!TIP]
> 如果不确定部署的内存、CPU 或 GPU 需求，可以使用分析来了解这些信息。 有关详细信息，请参阅[部署模型的方式和位置](how-to-deploy-and-where.md)。

```python
from azureml.core.model import Model
from azureml.core.webservice import Webservice
from azureml.core.image import ContainerImage
from azureml.core.webservice import AksWebservice, Webservice

# If deploying to a cluster configured for dev/test, ensure that it was created with enough
# cores and memory to handle this deployment configuration. Note that memory is also used by
# things such as dependencies and AML components.

aks_config = AksWebservice.deploy_configuration(autoscale_enabled=True, 
                                                       autoscale_min_replicas=1, 
                                                       autoscale_max_replicas=3, 
                                                       autoscale_refresh_seconds=10, 
                                                       autoscale_target_utilization=70,
                                                       auth_enabled=True, 
                                                       cpu_cores=1, memory_gb=2, 
                                                       scoring_timeout_ms=5000, 
                                                       replica_max_concurrent_requests=2, 
                                                       max_request_wait_time=5000)
```

有关详细信息，请参阅 [AksService.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-) 的参考文档。

## <a name="define-the-inference-configuration"></a>定义推理配置

推理配置指向入口脚本和环境对象：

```python
from azureml.core.model import InferenceConfig
inf_config = InferenceConfig(entry_script='score.py', environment=myenv)
```

有关详细信息，请参阅 [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py&preserve-view=true) 的参考文档。

## <a name="deploy-the-model"></a>部署模型

将模型部署到 AKS 群集，并等待其创建服务。 在此示例中，从注册表中加载两个注册的模型并将其部署到 AKS。 部署后，部署中的 `score.py` 文件将加载这些模型并使用它们执行推理。

```python
from azureml.core.webservice import AksWebservice, Webservice

c_aspect_lex = Model(ws, 'hotel_aspect_lex')
c_opinion_lex = Model(ws, 'hotel_opinion_lex') 
service_name = "hotel-absa-v2"

aks_service = Model.deploy(workspace=ws,
                           name=service_name,
                           models=[c_aspect_lex, c_opinion_lex],
                           inference_config=inf_config,
                           deployment_config=aks_config,
                           deployment_target=aks_target,
                           overwrite=True)

aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
```

有关详细信息，请参阅[模型](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py&preserve-view=true)的参考文档。

## <a name="issue-a-sample-query-to-your-service"></a>向服务发出示例查询

以下示例使用前面的代码部分中存储在 `aks_service` 变量中的部署信息。 它使用此变量来检索与服务进行通信所需的评分 URL 和身份验证令牌：

```python
import requests
import json

primary, secondary = aks_service.get_keys()

# Test data
input_data = '{"raw_data": {"text": "This is a nice place for a relaxing evening out with friends. The owners seem pretty nice, too. I have been there a few times including last night. Recommend."}}'

# Since authentication was enabled for the deployment, set the authorization header.
headers = {'Content-Type':'application/json',  'Authorization':('Bearer '+ primary)} 

# Send the request and display the results
resp = requests.post(aks_service.scoring_uri, input_data, headers=headers)
print(resp.text)
```

从服务返回的结果类似于下面的 JSON：

```json
{"sentiment": {"sentence": "This is a nice place for a relaxing evening out with friends. The owners seem pretty nice, too. I have been there a few times including last night. Recommend.", "terms": [{"text": "place", "type": "AS", "polarity": "POS", "score": 1.0, "start": 15, "len": 5}, {"text": "nice", "type": "OP", "polarity": "POS", "score": 1.0, "start": 10, "len": 4}]}}
```

## <a name="connect-to-cognitive-search"></a>连接到认知搜索

有关在认知搜索中使用此模型的信息，请参阅[使用 Azure 机器学习构建和部署自定义技能](../search/cognitive-search-tutorial-aml-custom-skill.md)教程。

## <a name="clean-up-the-resources"></a>清理资源

如果专门为此示例创建了 AKS 群集，请在使用认知搜索对其进行测试之后删除资源。

> [!IMPORTANT]
> Azure 会根据 AKS 群集的部署时长来收费。 使用完后，请务必将其清除。

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>后续步骤

* [使用 Azure 机器学习构建和部署自定义技能](../search/cognitive-search-tutorial-aml-custom-skill.md)
