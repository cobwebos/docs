---
title: 将 Web 服务部署到 Azure 容器实例 (ACI) - Azure 机器学习
description: 了解如何通过 Azure 机器学习服务在 Azure 容器实例 (ACI) 上将已训练的模型部署为 Web 服务。 本文说明在 ACI 上部署模型的三种不同方法。 它们的区别在于代码行数以及对命名部署部件的控制程度。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: raymondl
author: raymondlaghaeian
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: b004abb3959bbfe36fc200bf762114f88f3d2ead
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/09/2018
ms.locfileid: "51345029"
---
# <a name="deploy-web-services-to-azure-container-instances"></a>将 Web 服务部署到 Azure 容器实例 

可在 [Azure 容器实例](https://azure.microsoft.com/services/container-instances/) (ACI)、[Azure Kubernetes 服务](https://azure.microsoft.com/services/kubernetes-service/) (AKS)、IoT Edge 设备或[现场可编程门阵列 (FPGA)](concept-accelerate-with-fpgas.md) 上将训练模型部署为 Web 服务 

ACI 通常比 AKS 便宜，并且只需 4-6 行代码即可设置。 ACI 是测试部署的绝佳选择。 之后，在准备将模型和 Web 服务用于大规模生产时，可[将它们部署到 AKS](how-to-deploy-to-aks.md)。

本文说明在 ACI 上部署模型的三种不同方法。 它们的区别在于代码行数以及对命名部署部件的控制程度。 从使用代码量和控制最少的方法到最多的方法，ACI 选项分别如下：

* 使用 `Webservice.deploy()` 从模型文件进行部署 
* 使用 `Webservice.deploy_from_model()` 从已注册的模型进行部署
* 使用 `Webservice.deploy_from_image()` 从映像部署已注册的模型

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。


## <a name="prerequisites"></a>先决条件

- 已安装 Azure 机器学习服务工作区，以及适用于 Python 的 Azure 机器学习 SDK。 使用 [Azure 机器学习快速入门](quickstart-get-started.md)，了解如何获取这些先决条件。

- Azure 机器学习服务工作区对象

    ```python
    from azureml.core import Workspace
    ws = Workspace.from_config()
    ```

- 要部署的模型。 本文中的示例使用按“[训练模型](tutorial-train-models-with-aml.md)”教程创建的模型。 如果不使用此模型，请修改提到模型名称的步骤。  还需要写入自己的评分脚本来运行模型。


## <a name="configure-an-image"></a>配置映像

配置用于存储所有模型文件的 Docker 映像。
1. [使用这些说明](tutorial-deploy-models-with-aml.md#create-scoring-script)创建评分脚本 (score.py)。

    > [!IMPORTANT]
    > 评分脚本接收从客户端提交的数据并将其传递给模型进行评分。 记录脚本和模型所需的数据结构。 使用此文档可以在构建使用 Web 服务的客户端时更轻松。

1. [使用这些说明](tutorial-deploy-models-with-aml.md#create-environment-file)创建环境文件 (myenv.yml)。

1. 使用这两个文件在使用 SDK 的 Python 中配置 Docker 映像，如下所示：

    ```python
    from azureml.core.image import ContainerImage

    image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                      runtime = "python",
                                                      conda_file = "myenv.yml",
                                                      description = "Image with mnist model",
                                                      tags = {"data": "mnist", "type": "classification"}
                                                     )
    ```

## <a name="configure-the-aci-container"></a>配置 ACI 容器

通过指定 ACI 容器所需的 CPU 数量和 RAM 大小（单位为 GB）来配置 ACI 容器。 对于许多模型而言，默认的 1 个核心和 1 GB RAM 就足够了。 如果你认为以后需要更多核心或 RAM，请重新创建映像并重新部署服务。  

```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores = 1, 
                                               memory_gb = 1, 
                                               tags = {"data": "mnist", "type": "classification"},
                                               description = 'Handwriting recognition')
```

## <a name="register-a-model"></a>注册模型

> 如果是[从模型文件部署](#deploy-from-model-file) (`Webservice.deploy()`)，可跳过本条。

注册模型以使用 [Webservice.deploy_from_model](#deploy-from-registered-model) 或 [Webservice.deploy_from_image](#deploy-from-image)。 如果已有注册的模型，请立即检索它。

### <a name="retrieve-a-registered-model"></a>检索已注册的模型
如果使用 Azure 机器学习来训练模型，该模型可能已在工作区中注册。  例如，在[训练模型教程](tutorial-train-models-with-aml.md)的最后一步就注册了模型。  然后，检索要部署的已注册模型。

```python
from azureml.core.model import Model

model_name = "sklearn_mnist"
model=Model(ws, model_name)
```
  
### <a name="register-a-model-file"></a>注册模型文件

如果模型是在其他地方生成的，你仍可将其注册到工作区中。  若要注册模型，模型文件（本示例中为 `sklearn_mnist_model.pkl`）必须位于当前工作目录中。 然后通过 `Model.register()` 将该文件注册为工作区中名为 `sklearn_mnist` 的模型。
    
```python
from azureml.core.model import Model

model_name = "sklearn_mnist"
model = Model.register(model_path = "sklearn_mnist_model.pkl",
                        model_name = model_name,
                        tags = {"data": "mnist", "type": "classification"},
                        description = "Mnist handwriting recognition",
                        workspace = ws)
```

<a name='deploy-from-model-file'/>
## <a name="option-1-deploy-from-model-file"></a>选项 1：从模型文件部署

从模型文件部署的选项需要写入的代码量最少，但也对组件的命名提供的控制也最少。 此选项从模型文件开始，将其注册到工作区中。  但是，你无法对模型命名或为其关联标记或描述。  

此选项使用 SDK 方法：Webservice.deploy()。  

**时间估算**：部署大约需要 6-7 分钟。

1. 请确保模型文件位于本地工作目录中。

1. 打开必备模型文件 score.py，然后将 `init()` 部分更改为：

    ```python
    def init():
        global model
        # retreive the local path to the model using the model name
        model_path = Model.get_model_path('sklearn_mnist_model.pkl')
        model = joblib.load(model_path)
    ```

1. 部署模型文件。

    ```python
    from azureml.core.webservice import Webservice
    
    service_name = 'aci-mnist-1'
    service = Webservice.deploy(deployment_config = aciconfig,
                                    image_config = image_config,
                                    model_paths = ['sklearn_mnist_model.pkl'],
                                    name = service_name,
                                    workspace = ws)
    
    service.wait_for_deployment(show_output = True)
    print(service.state)
    ```

1. 现可[测试 Web 服务](#test-web-service)。

<a name='deploy-from-registered-model'/>
## <a name="option-2-deploy-from-registered-model"></a>选项 2：从已注册的模型部署

部署已注册模型文件的选项需要更多行代码，并且需要对输出的命名进行一些控制。 此选项是部署已有注册模型的便捷方法。  然而，你无法命名 Docker 映像。  

此选项使用 SDK 方法：Webservice.deploy_from_model()。

**时间估算**：通过此选项部署大约需要 8 分钟。

1. 运行代码部署 Docker 容器和 ACI 容器，并指定已注册模型。

    ```python
    from azureml.core.webservice import Webservice

    service_name = 'aci-mnist-2'
    service = Webservice.deploy_from_model(deployment_config = aciconfig,
                                           image_config = image_config,
                                           models = [model], # this is the registered model object
                                           name = service_name,
                                           workspace = ws)
    service.wait_for_deployment(show_output = True)
    print(service.state)
    ```

1. 现可[测试 Web 服务](#test-web-service)。

<a name='deploy-from-image'/>
## <a name="option-3-deploy-from-image"></a>选项 3：从映像部署

使用 `Webservice.deploy_from_image()` 部署已注册的模型 (`model`)。 使用此方法，可单独创建 Docker 映像，然后从该映像进行部署。

1. 在工作区中使用 `ContainerImage.create()` 生成并注册 Docker 映像

    此方法在单独的步骤中创建映像，使你对映像具有更多控制。  此映像包含已注册模型 (`model`)。
    
    ```python
    from azureml.core.image import ContainerImage
    
    image = ContainerImage.create(name = "myimage1",
                                  models = [model], # this is the registered model object
                                  image_config = image_config,
                                  workspace = ws)
    
    image.wait_for_creation(show_output = True)
    ```
**时间估算**：大约需要 3 分钟。

1. 使用 `Webservice.deploy_from_image()` 将 Docker 映像部署为服务

    现在，将映像部署到 ACI。  
    
    ```python
    from azureml.core.webservice import Webservice
    
    service_name = 'aci-mnist-3'
    service = Webservice.deploy_from_image(deployment_config = aciconfig,
                                                image = image,
                                                name = service_name,
                                                workspace = ws)
    service.wait_for_deployment(show_output = True)
    print(service.state)
    ```   
 
**时间估算**：大约需要 3 分钟。

此方法对部署过程中组件的创建和命名的控制最多。

现可测试 Web 服务。

## <a name="a-nametest-web-servicetest-the-web-service"></a><a name='test-web-service'/>测试 Web 服务

无论使用哪一种方法，Web 服务都相同。  若要获得预测，请使用服务的 `run` 方法。  

```python
# Load Data
import os
import urllib

os.makedirs('./data', exist_ok = True)

urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename = './data/test-images.gz')

from utils import load_data
X_test = load_data('./data/test-images.gz', False) / 255.0

from sklearn import datasets
import numpy as np
import json

# find 5 random samples from test set
n = 5
sample_indices = np.random.permutation(X_test.shape[0])[0:n]

test_samples = json.dumps({"data": X_test[sample_indices].tolist()})
test_samples = bytes(test_samples, encoding = 'utf8')

# predict using the deployed model
prediction = service.run(input_data = test_samples)
print(prediction)
```


## <a name="clean-up-resources"></a>清理资源

如果不打算使用此 Web 服务，请将其删除，以免产生任何费用。

```python
service.delete()
```

## <a name="next-steps"></a>后续步骤

* 了解如何[使用部署为 Web 服务的 ML 模型](how-to-consume-web-service.md)。
* 了解如何[部署到 Azure Kubernetes 服务](how-to-deploy-to-aks.md)以进行更大规模的部署。 
