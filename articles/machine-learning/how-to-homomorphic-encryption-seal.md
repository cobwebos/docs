---
title: 部署加密推理服务
titleSuffix: Azure Machine Learning
description: 了解如何使用 Microsoft SEAL 对映像分类部署加密预测服务
author: luisquintanilla
ms.author: luquinta
ms.date: 05/18/2020
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.openlocfilehash: b79ab513950f14cda98fc3113bb062b431ebd69e
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83663625"
---
# <a name="how-to-deploy-an-encrypted-inferencing-web-service"></a>如何部署加密推理 Web 服务

了解如何在 [Azure 容器实例](https://docs.microsoft.com/azure/container-instances/) (ACI) 中将映像分类模型部署为加密推理 Web 服务。 Web 服务是包含模型和评分逻辑的 Docker 容器映像。

在本指南中，将使用 Azure 机器学习服务执行以下操作：

> [!div class="checklist"]
> * 配置环境
> * 部署加密推理 Web 服务
> * 准备测试数据
> * 作出加密预测
> * 清理资源

ACI 是用于测试和了解模型部署工作流的绝佳解决方案。 对于可缩放的生产部署，请考虑使用 Azure Kubernetes 服务。 有关详细信息，请参阅[部署方式及位置](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where)。

本示例中使用的加密方法是[同态加密](https://github.com/Microsoft/SEAL#homomorphic-encryption)。 使用同态加密，无需访问机密（解密）密钥即可对加密数据进行计算。 计算的结果已加密，只能由密钥的所有者公开。 

## <a name="prerequisites"></a>先决条件

本指南默认你已在 Azure 机器学习中注册映像分类模型。 如果尚未注册，请使用[预训练模型](https://github.com/Azure/MachineLearningNotebooks/raw/master/tutorials/image-classification-mnist-data/sklearn_mnist_model.pkl)注册该模型或通过完成[通过 Azure 机器学习教程训练映像分类模型](tutorial-train-models-with-aml.md)来创建自己的模型。

## <a name="configure-local-environment"></a>配置本地环境

在 Jupyter 笔记本中

1. 导入此示例所需的 Python 包。

    ```python
    %matplotlib inline
    import numpy as np
    import matplotlib.pyplot as plt

    import azureml.core

    # display the core SDK version number
    print("Azure ML SDK Version: ", azureml.core.VERSION)
    ```

2. 安装同态加密库以进行安全推理。

    > [!NOTE]
    > `encrypted-inference` 包目前处于预览阶段。

    [`encrypted-inference`](https://pypi.org/project/encrypted-inference) 是一个库，其中包含基于 [Microsoft SEAL](https://github.com/Microsoft/SEAL) 的加密推理的绑定。

    ```python
    !pip install encrypted-inference==0.9
    ```

## <a name="configure-the-inferencing-environment"></a>配置推理环境

创建推理环境并将 `encrypted-inference` 包添加为 conda 依赖项。

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

# to install required packages
env = Environment('tutorial-env')
cd = CondaDependencies.create(pip_packages=['azureml-dataprep[pandas,fuse]>=1.1.14', 'azureml-defaults', 'azure-storage-blob', 'encrypted-inference==0.9'], conda_packages = ['scikit-learn==0.22.1'])

env.python.conda_dependencies = cd

# Register environment to re-use later
env.register(workspace = ws)
```

## <a name="deploy-encrypted-inferencing-web-service"></a>部署加密推理 Web 服务

将该模型部署为在 ACI 中托管的 Web 服务。

要为 ACI 构建正确的环境，请提供以下信息：

* 显示如何使用模型的评分脚本
* 要生成 ACI 的配置文件
* 一个已训练模型

### <a name="create-scoring-script"></a>创建评分脚本

创建推理 Web 服务使用的评分脚本 `score.py`。

必须在评分脚本中包含两个必需的函数：

* `init()` 函数，它通常将模型加载到全局对象中。 此函数只能在 Docker 容器启动时运行一次。
* `run(input_data)` 函数，它使用模型来基于输入数据预测值。 运行的输入和输出通常使用 JSON 进行序列化和反序列化，但支持其他格式。 该函数将获取服务调用方上传的基于同态加密的公钥。

```python
%%writefile score.py
import json
import os
import pickle
import joblib
from azure.storage.blob import BlobServiceClient, BlobClient, ContainerClient, PublicAccess
from encrypted.inference.eiserver import EIServer

def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment.
    # It is the path to the model folder (./azureml-models/$MODEL_NAME/$VERSION)
    # For multiple models, it points to the folder containing all deployed models (./azureml-models)
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

    global server
    server = EIServer(model.coef_, model.intercept_, verbose=True)

def run(raw_data):

    json_properties = json.loads(raw_data)

    key_id = json_properties['key_id']
    conn_str = json_properties['conn_str']
    container = json_properties['container']
    data = json_properties['data']

    # download the public keys from blob storage
    blob_service_client = BlobServiceClient.from_connection_string(conn_str=conn_str)
    blob_client = blob_service_client.get_blob_client(container=container, blob=key_id)
    public_keys = blob_client.download_blob().readall()

    result = {}
    # make prediction
    result = server.predict(data, public_keys)

    # you can return any data type as long as it is JSON-serializable
    return result
```

### <a name="create-configuration-file"></a>创建配置文件

创建部署配置文件并指定 ACI 容器所需的 CPU 数量和 RAM 大小（单位为 GB）。 虽然这取决于具体模型，但对于许多模型而言，默认的 1 核和 1 GB 的 RAM 通常已足够。 如果你认为以后需要更多核心或 RAM，请重新创建映像并重新部署服务。

```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "MNIST",  "method" : "sklearn"}, 
                                               description='Encrypted Predict MNIST with sklearn + SEAL')
```

### <a name="deploy-to-azure-container-instances"></a>部署到 Azure 容器实例

估计完成时间：约 2-5 分钟

配置映像和部署。 下面的代码将完成这些步骤：

1. 使用环境文件 (`myenv.yml`) 创建包含模型所需依赖项的环境对象
1. 使用以下项创建将模型部署为 Web 服务所需的推理配置：
   * 评分文件 (`score.py`)
   * 在上一步中创建的环境对象
1. 将模型部署到 ACI 容器。
1. 获取 Web 服务 HTTP 终结点。

```python
%%time
from azureml.core.webservice import Webservice
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core import Workspace
from azureml.core.model import Model

ws = Workspace.from_config()
model = Model(ws, 'sklearn_mnist')

myenv = Environment.get(workspace=ws, name="tutorial-env")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

service = Model.deploy(workspace=ws,
                       name='sklearn-encrypted-mnist-svc',
                       models=[model],
                       inference_config=inference_config,
                       deployment_config=aciconfig)

service.wait_for_deployment(show_output=True)
```

获取评分 Web 服务的 HTTP 终结点，该终结点接受 REST 客户端调用。 可以与想要测试 Web 服务或要将其集成到应用程序中的任何人共享此终结点。

```python
print(service.scoring_uri)
```

## <a name="prepare-test-data"></a>准备测试数据

1. 下载测试数据。 在本例中，它会保存到名为“data”的目录中。

    ```python
    import os
    from azureml.core import Dataset
    from azureml.opendatasets import MNIST
    
    data_folder = os.path.join(os.getcwd(), 'data')
    os.makedirs(data_folder, exist_ok=True)
    
    mnist_file_dataset = MNIST.get_file_dataset()
    mnist_file_dataset.download(data_folder, overwrite=True)
    ```

1. 从“data”目录加载测试数据。

    ```python
    from utils import load_data
    import os
    import glob
    
    data_folder = os.path.join(os.getcwd(), 'data')
    # note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the neural network converge faster
    X_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-images-idx3-ubyte.gz"), recursive=True)[0], False) / 255.0
    y_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-labels-idx1-ubyte.gz"), recursive=True)[0], True).reshape(-1)
    ```

## <a name="make-encrypted-predictions"></a>作出加密预测

将测试数据集提供给模型以获得预测。

作出加密预测：

1. 创建一个新的 `EILinearRegressionClient`（基于同态加密的客户端）和公钥。

    ```python
    from encrypted.inference.eiclient import EILinearRegressionClient

    # Create a new Encrypted inference client and a new secret key.
    edp = EILinearRegressionClient(verbose=True)

    public_keys_blob, public_keys_data = edp.get_public_keys()
    ```

1. 将同态加密生成的公钥上传到工作区的默认 Blob 存储。 这将允许你与推理服务器共享密钥。

    ```python
    import azureml.core
    from azureml.core import Workspace, Datastore
    import os

    ws = Workspace.from_config()

    datastore = ws.get_default_datastore()
    container_name=datastore.container_name

    # Create a local file and write the keys to it
    public_keys = open(public_keys_blob, "wb")
    public_keys.write(public_keys_data)
    public_keys.close()

    # Upload the file to blob store
    datastore.upload_files([public_keys_blob])

    # Delete the local file
    os.remove(public_keys_blob)
    ```

1. 加密测试数据

    ```python
    #choose any one sample from the test data 
    sample_index = 1

    #encrypt the data
    raw_data = edp.encrypt(X_test[sample_index])

    ```

1. 使用 SDK 的 `run` API 调用服务，并向模型提供测试数据集以获取预测。 我们需要将连接字符串发送到已上传公钥的 Blob 存储。

    ```python
    import json
    from azureml.core import Webservice

    service = Webservice(ws, 'sklearn-encrypted-mnist-svc')

    #pass the connection string for blob storage to give the server access to the uploaded public keys 
    conn_str_template = 'DefaultEndpointsProtocol={};AccountName={};AccountKey={};EndpointSuffix=core.windows.net'
    conn_str = conn_str_template.format(datastore.protocol, datastore.account_name, datastore.account_key)

    #build the json 
    data = json.dumps({"data": raw_data, "key_id" : public_keys_blob, "conn_str" : conn_str, "container" : container_name })
    data = bytes(data, encoding='ASCII')

    print ('Making an encrypted inference web service call ')
    eresult = service.run(input_data=data)

    print ('Received encrypted inference results')
    ```

1. 使用客户端解密结果。

    ```python
    import numpy as np

    results = edp.decrypt(eresult)

    print ('Decrypted the results ', results)

    #Apply argmax to identify the prediction result
    prediction = np.argmax(results)

    print ( ' Prediction : ', prediction)
    print ( ' Actual Label : ', y_test[sample_index])
    ```

## <a name="clean-up-resources"></a>清理资源

删除在本示例中创建的 Web 服务：

```python
service.delete()
```

如果不再打算使用已创建的 Azure 资源，请将其删除。 这可避免对仍在运行中但未使用的资源付费。 有关详细信息，请参阅有关如何[清理资源](how-to-manage-workspace.md#clean-up-resources)的指南。